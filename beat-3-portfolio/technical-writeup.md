# The check I refused to loosen caught my own bug: shipping a Prometheus exporter to EcoTrace

## Overview

EcoTrace is an open-source Python library that measures the carbon footprint of
your code. Issue [#232](https://github.com/Zwony/ecotrace/issues/232) asked for a
new exporter that pushes those measurements to a Prometheus Pushgateway, the
standard way short-lived jobs hand metrics to Prometheus. I built it in
[PR #250](https://github.com/Zwony/ecotrace/pull/250): a new
`ecotrace/exporters/prometheus.py`, its registration, and 15 tests. My first
version had a data-loss bug that my tests could not see. plan-check, the
plan-review skill I built in unit 3, flagged the claim that hid it; the rubric
check that caught it, `unknowns-honest`, is one I wrote and chose not to loosen
when it cost me an eval point. I confirmed the bug against a real Pushgateway,
redesigned how pushes are grouped, and the maintainer approved and merged the PR
on November 6, 2026.

## What happened

### Picking and reproducing

I found the issue on the course's issue site and graded it with my issue-scout:
accept, "straightforward". The issue named the file to create and the exporter
to copy. Before writing code I reproduced the gap on `main` (`05d9a12`):

```
$ python -c "from ecotrace.exporters import PrometheusExporter"
ImportError: cannot import name 'PrometheusExporter' from 'ecotrace.exporters'
```

### My first version, and why I believed it

I modelled the exporter on the repo's `WebhookExporter` and pushed every
measurement to one URL per job. My first commit (`a335e4c`) did this:

```python
        self.push_url = "%s/metrics/job/%s" % (
            url.rstrip("/"),
            quote(job, safe=""),
        )
```

Its docstring said: "POST adds to the job's group, so samples from different
functions and runs coexist." Eight tests passed, all of them mocking
`requests.post`, so none of them talked to a real Pushgateway.

### The catch

Before opening the PR I ran my plan through plan-check. It rejected the plan on
one check, `unknowns-honest`:

> "'POST adds to the job's group instead of replacing it, so samples from different
> runs and functions coexist' is asserted as settled, never measured (tests mock
> requests.post), and contradicts Pushgateway semantics, where grouping comes from
> the URL path and POST replaces same-named metrics in the group."

That check has history. In unit 3 my plan-check scored 19/20 on the course's
eval set, and the one miss was this check: it rejected an arguable package
(pkg-14) that the answer key accepted. I wrote at the time that I would not
loosen it, because a plan that states something unmeasured as fact is the risk
I most wanted it to catch. Here it caught exactly that, in my own plan.

I didn't take the tool's word for it either. I ran the official
`prom/pushgateway:v1.11.1` image in Docker, pushed `train` and then `evaluate` the
old way, and read the gateway's own `/metrics`:

```
-- old job-only URL, after pushing train then evaluate
   ecotrace_carbon_emitted_gco2{function="evaluate",...,run_id="oldrun"} 0.1
```

`train` was gone. Every push overwrote the previous function's numbers, so a user
tracking several functions would only ever see the last one.

### The change

The Pushgateway groups metrics by the URL path, so I moved `function` and
`run_id` into the path as grouping labels, base64-encoded so that names with
slashes or empty names stay safe (commit `703c4d0`):

```diff
-        self.push_url = "%s/metrics/job/%s" % (
+        self.job_url = "%s/metrics/%s" % (
...
+    def push_url(self, func_name: str, run_id: str) -> str:
+        return "%s/%s/%s" % (
+            self.job_url,
+            _grouping_segment("function", func_name),
+            _grouping_segment("run_id", run_id),
```

The same Docker gateway, after EcoTrace tracked `train()` then `evaluate()`,
now kept both:

```
   ecotrace_carbon_emitted_gco2{function="evaluate",...} 3.329915718668195e-05
   ecotrace_carbon_emitted_gco2{function="train",...} 0.00011581219026017659
```

I added a test that pins the new behaviour, so two functions must push to two
different groups:

```python
    exporter.export(0.1, "a", 1.0, "DE", run_id="r1")
    exporter.export(0.2, "b", 2.0, "DE", run_id="r1")
    urls = [c.args[0] for c in mock_post.call_args_list]
    assert urls == [
        "http://pgw:9091/metrics/job@base64/ZWNvdHJhY2U/function@base64/YQ/run_id@base64/cjE",
        "http://pgw:9091/metrics/job@base64/ZWNvdHJhY2U/function@base64/Yg/run_id@base64/cjE",
    ]
```

Because mocked tests had already fooled me once, I also checked whether my
tests would notice a bug. I planted 12 bugs in the exporter one at a time and
ran the Prometheus tests against each. The first version of my tests missed
four of them, so I added tests until every planted bug failed at least one:

```
baseline (no bug)                  ok      15 passed
job-only URL (the original bug)    ok      10 failed, 5 passed
```

The full suite went from 251 passed on `main` to 266 passed on my branch.

### The result

I opened the PR on November 4 through my ship-gate hook, which reran the repo's
tests and my pre-PR review before letting `gh pr create` through. Two days later
the maintainer approved it:

> "Reviewed the Pushgateway exporter, grouping-key escaping, exposition format,
> and EcoTrace callback integration. Validation: python -m pytest
> tests/test_exporters.py -q — 33 passed."

CI passed on Python 3.8 through 3.14, and the PR was merged as `736dc14` on
November 6, 2026, closing issue #232. `ecotrace/exporters/prometheus.py` is now
part of EcoTrace's `main`.

I used Claude Code throughout: it drafted the code and tests and ran the
commands at my direction, and I reviewed every change and every output. One
honesty fix belongs here too. My PR description first said I had run every
command myself, which wasn't true, and I corrected it on November 5 to say
plainly what the tool ran.

## Limitations and what's next

- **Mocks hid a design flaw.** My first tests passed a design that was wrong
  against the real service, and I only caught it because a review tool flagged
  an unmeasured claim. Next time I'll run plan-check before the first commit
  instead of after it. When a test mocks a service, I'll also run it once
  against the real thing (here, a Docker Pushgateway) before calling the design
  settled.
- **Groups pile up.** Each EcoTrace process gets a new run ID, so every run
  leaves new groups in the Pushgateway, and they stay until someone deletes
  them. I documented how to delete a group but didn't automate it.
- **Only the latest value per function and run survives.** Calling the same
  function twice in one run overwrites the gauge rather than adding to a total.

What I'd build next is an optional cleanup for the second limit: either a
`delete_on_exit` flag that removes the run's groups when EcoTrace shuts down, or
a mode that groups by job and function only for users who want just the latest
run. I'd raise it with the maintainer as a follow-up issue first, since the
right default is their call.
