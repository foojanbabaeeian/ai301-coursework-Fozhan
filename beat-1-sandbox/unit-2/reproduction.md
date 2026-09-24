# Unit 2 — Claim and Reproduce

Path: `beat-1-sandbox/unit-2/reproduction.md`

Record of your claim and reproduction on the issue you chose in Unit 1, and of the
evaluation runs that produced `eval-run.txt`. This file is graded at the path above; a copy
kept anywhere else in the repository is not read.

Complete every labelled field below. Each is graded on its own; content placed under the wrong
label is not graded.

---

## Your identity upstream

**GitHub username**

foojanbabaeeian

---

## Posted upstream

**Claim comment**

@@CLAIM_LINK@@

@@CLAIM_TEXT@@

**Reproduction comment**

@@REPRO_LINK@@

@@REPRO_TEXT@@

## Eval iterations

Answer all four sections. Quote source text directly; paraphrase does not satisfy these
fields.

**Run history**

Before spending anything I graded `calib-02` by hand against the finished rubric: reject, on artifact-shows-issue (no artifact, only "Same here!!"), outcome-honest (a cause "obviously" asserted from nothing), and claim-specific (a +1 with no intent). That matched the worksheet label and cost nothing.

My first launch of the harness crashed on Windows before any package was graded: `run_eval.py` pipes each bundle into `claude -p` through a text pipe that Windows Python opens as cp1252, and it died on the 🙏 in pkg-04 (`UnicodeEncodeError: 'charmap' codec can't encode character '\U0001f64f'`). No package returned a verdict, so it produced no score. Setting `PYTHONUTF8=1` for the run fixed it without editing the harness. (The other Windows wrinkle: Python's `subprocess` cannot launch the npm `claude.cmd` shim, so the folder holding the real `claude.exe` had to be on `PATH`.)

1. Full run, `--save-run eval-run.txt`: **20/20**, categories `clear-accept 8/8  disclosure 1/1  no-evidence 4/4  unfollowable-comms 3/3  wrong-target 4/4`, bar PASS. This is the run in `eval-run.txt`; its agreement line reads `agreement: 20/20 scored items  (bar: 18/20: PASS)`.

One full run was a complete answer, so there were no `--only` re-runs and no revisions after the first run.

**Package analysis**

`pkg-16` (pandas-dev/pandas#66656). My rubric decided **reject**; the gold label is **reject**, category wrong-target. What makes it worth writing about is that my rubric rejected it on a different check than the one I first expected to catch it.

The package looks like a clean reproduction: exact two-line script from the issue, a real traceback (`ValueError: Length of new names must be 1, got 3`), expected and actual stated the right way round. My `artifact-shows-issue` check passed it, and I think rightly: the thread's comment from aaron-seq describes the failure as the length check inside `Index.set_names`, and that is exactly the error shown. If the rubric had only a "does the artifact match the issue" check, this package would have been accepted.

What the gold note calls out is the environment: the report tested pandas 1.5.3, while the issue's template checkboxes confirm the bug on the latest release and on `main`, and the thread confirms it on 2.3.3. The report never says it is on an old version and never limits its conclusion to that version. My `target-faithful` check failed it on exactly that evidence: "Tested pandas 1.5.3, older than the issue's latest/main and the thread's 2.3.3, and the report neither names the deviation nor limits its conclusion to 1.5.3." The `template-followed` check (preferred, so it did not move the verdict) also noted that the pandas template asks for confirmation on latest and main plus `pd.show_versions()`, neither of which the report gives.

So the rubric read it the way the gold label did because the wrong-target family is split into two checks: one that reads the artifact against the issue's behavior, and one that reads the tested version and platform against the issue's target. pkg-16 passes the first and fails the second. pkg-02, pkg-08, and pkg-17 are the mirror image: they pass `target-faithful` and fail `artifact-shows-issue`. One combined check would have had to describe both failure shapes in one pass condition, and the first draft of it that I wrote in my head kept letting pkg-16 through because its artifact is plausible.

**Check rationale**

Quoted from `tools/repro-check/rubric.md` as currently written:

> | target-faithful | The environment record's project version, platform, and build, read against what the issue targets: the version the reporter names, any "confirmed on latest / main" statement in the issue or thread, and any platform or build profile the thread says is decisive | Pass when the version tested is the issue's version, a newer release, or the main branch, OR when the tested version, platform, or build differs from the issue's and the report names the difference ("the issue was filed against X; I tested Y", or a cannot-reproduce that lists what differed). Fail when the tested version is older than the version the issue was reported or confirmed on, or the platform or build profile differs in a way the issue or thread says matters, and the report neither names the deviation nor limits its conclusion to what it ran. | required |

Why it reads that way. The wrong-target category in the eval set contains two kinds of unfaithful report: ones whose artifact shows an adjacent behavior (a graceful argument error narrated as a crash in pkg-02, a compile error narrated as the path bug in pkg-08, garbled text with the terminal alive narrated as a crash in pkg-17), and one whose artifact is fine but whose environment is quietly the wrong target (pandas 1.5.3 in pkg-16). I first thought of "the behavior matches the issue" as one check, then realized nothing in that pass condition could see pkg-16, because its traceback is exactly what the thread says the bug produces. So the version question got its own row.

The shape of the pass condition came from the accepts, not the rejects. Three clear-accept packages test a newer version than the issue names (pkg-03 on ripgrep 15.2.0 against a 13.0.0 report, pkg-07 on p5.js 1.11.7, pkg-12 on prettier 3.9.6), and two honest cannot-reproduce packages (pkg-09, pkg-10) run on a different OS and shell from the reporter. All five have to pass. That is why the rule is "same version, newer, or main, OR the difference is named", rather than "same version". The fail branch is deliberately narrow: older than the issue's confirmed version, or a platform or build the thread says matters, AND no acknowledgment. Naming the deviation is enough to pass this check; whether the conclusion then overreaches is `outcome-honest`'s job.

What I rejected: folding version fidelity into `env-recorded`. That check answers "is the environment written down at all" (the question pkg-06 and calib-04 fail), and mixing "missing" with "wrong" would have made its note column useless for finding which of the two went wrong.

**Trade-offs**

Nothing changed after the first full run, so there was no loosening and no canary to re-run; here is what I know the check gives up anyway. `target-faithful` accepts any report that names its deviation, including a report that tests an older version and says so. A report reading "I tested pandas 1.5.3; the issue is confirmed on main" would pass this check even though its evidence is still about 1.5.3. I accept that miss on purpose: the harm in pkg-16 is the silence, not the old version, and once the deviation is on the page a reader can weigh it. The remaining risk, a report that names the old version and then still generalizes ("so the bug is present on main"), is caught by `outcome-honest`'s last fail clause, "a conclusion that generalizes past the run", which is the same clause that failed pkg-17 for declaring the crash present on the Store release the maintainers could not reproduce on. The evidence that nothing else moved: every package in the set that runs on a newer version or a different platform than the reporter (pkg-03, pkg-07, pkg-09, pkg-10, pkg-11, pkg-12, pkg-20) passed `target-faithful` on the single full run, and the only package it failed was pkg-16, the one the gold note says it should fail.

---

Related paths: `eval-run.txt` in this directory; your skill's files in
`tools/repro-check/`.
