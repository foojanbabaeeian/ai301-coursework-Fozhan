# Unit 4 — Test and Submit

Path: `beat-1-sandbox/unit-4/pull-request.md`

Record of the pull request you opened against the Path Review repo, and of the evaluation
runs that produced `eval-run.txt`. This file is graded at the path above; a copy kept
anywhere else in the repository is not read.

Complete every labelled field below. Each is graded on its own; content placed under the wrong
label is not graded.

---

## Your pull request

**Pull request**

https://github.com/codepath/pathreview-ai301-fa26-s1/pull/110

**Branch**

fix/72-verify-password-fail-closed

## Eval iterations

Answer all four sections. Quote source text directly; paraphrase does not satisfy these
fields.

**Run history**

Before the first run I wrote the four calibration packages' expected outcomes into the
rubric's header comment from the worksheet notes, as anchors the checks had to satisfy:
calib-01 (a one-line deletion with a terse description and the parse-error before/after
shown) must pass every required check; calib-02 (a dependency bump and formatting churn
that never implements the plan, with no evidence) must fail `diff-matches-plan` and
`repro-rerun-shown`; calib-03 (a perfect checklist over a second hunk that rewrites a file
the plan never names, under "no changes beyond the plan") must fail `diff-matches-plan` and
`description-matches-diff` on the hunk; calib-04 (plan names two failure modes, evidence
re-runs one) must fail `repro-rerun-shown` and nothing else. The procedure's read order
(diff before description) was written to make calib-03 fall the right way.

1. Full run, `--save-run eval-run.txt`: **19/20**, categories `clear-accept 6/7  not-tested 4/4
   silent-drift 4/4  standards-wall 2/2  unreviewable 3/3`, bar PASS. This is the run in
   `eval-run.txt`; its agreement line reads `agreement: 19/20 scored items  (bar: 18/20: PASS)`.
   The one disagreement is pkg-05, graded reject on `repro-rerun-shown` where the gold label
   is accept.

I did not revise after the first full run and did not run `--only` re-grades. The bar and
the category floor were met, the two-package standards-wall category matched on both
packages, and the change that would flip pkg-05 is one I chose not to make (Trade-offs,
below). Revising `rubric.md` after the saved run would also have put the uploaded file out
of step with the fingerprint in `eval-run.txt`, so a revision would have meant a second full
run.

**Package analysis**

`pkg-05` (nushell/nushell#18848). My rubric decided **reject**, on `repro-rerun-shown`; the
gold label is **accept**, category clear-accept, with the note "merge identity widened to
(name, mode, modifier, keycode) with a replace warning; engages fdncred's uniqueness question
explicitly; repro table before/after shown; template sections filled".

Six of my seven required checks passed it, and the gold note names the same strengths:
the diff is exactly the plan's widened match key plus the warn-once branch and two tests,
the description's claims match the hunks, the two thread questions (fdncred's uniqueness
point and kronberger-droid's config caveat) are answered in the description, and nushell's
template sections are filled with real content. The check that failed reads the test
evidence against the plan's test plan, and the plan names three things: "re-run the
issue's script, expect both `atuin` rows; re-run with two same-name same-key bindings,
expect one row plus a warning; cargo test on the config crate". The evidence shows the
first as a before and after table. The second appears only as a sentence, "Same-key
redefine prints the one-time warning", and the third only as "`cargo test -p nu-protocol`
passes (312 tests)". My pass condition requires "an observable outcome named for each
failure mode the plan's test plan commits to" with "the before ... and the after ... both
visible as output or a transcript, not as a sentence", so the second failure mode fails the
check as written.

Why the gold label is right anyway: the second failure mode is pinned by a test in the
diff, `same_name_same_key_replaces_with_warning`, whose assertion (`warnings.len() == 1`)
is the observable outcome, and the evidence paragraph says the suite that contains it
passed. A reviewer can see the warning path is tested without a terminal transcript of
it. My check does not count a test in the diff as evidence for a planned failure mode; it
only says an added test "that covers a different case than the fix does not substitute
for the re-run", which was written for pkg-14 (unit 4's httpie package, where the added
test covers header casing rather than the single-header POST). The rubric's wording
does not distinguish a test that covers the planned case from one that does not, so the
grader applied the stricter reading.

**Check rationale**

Quoted from `tools/pr-precheck/rubric.md` as currently written:

> | diff-matches-plan | Every file and hunk in the diff, read against the plan's scope pair (in scope, not in scope), its named files, its approach, and its deviation notes (the plan's `## Deviations` section live, or deviation sentences in the plan-context block in eval) | Pass when every changed file and every hunk falls inside the plan's stated scope or is covered by a deviation note that names the change and its reason, AND every change the plan committed to is present in the diff or its absence is covered by a deviation note. A test file the plan names, or a regression test for the planned fix, is inside scope even when unlisted. A smaller delivery with a note that says what was left out and why passes. Fail when the diff touches a file or adds a behavior the plan never names and no note covers it (a new config option, flag, or setting the plan scoped out; a renaming pass; a rewrite of a second file; a dependency bump in place of the planned fix), or when a change the plan promised is missing from the diff with no note (the plan says warning and docs, the diff has only the warning). Silence in both directions is the fail; a recorded deviation is not. | required |

Why it reads that way. The silent-drift category has four scored packages and two
calibration packages, and they drift in both directions. Three add more than the plan
(pkg-03's `truncate()` rewrite and `resolve_symlinks` option, pkg-06's renaming pass and
new setting, pkg-09's `--time-format` flag the plan scoped out), one delivers less
(pkg-17 promises a warning and docs and ships the warning), calib-02 ships a dependency
bump in place of the planned fix, and calib-03 hides a second hunk in a file the plan
never names. So the pass condition has two halves joined by AND: every hunk covered by the
plan or a note, and every promised change present or noted. The fail clause's parenthetical
lists those six shapes so a grader recognizes them without re-deriving the rule.

The sentence that matters most is the last one, "Silence in both directions is the fail;
a recorded deviation is not", together with "A smaller delivery with a note that says what
was left out and why passes". Two clear accepts, pkg-13 (neovim, `%VAR%` expansion
deferred) and pkg-16 (minikube, the start-path behavior deferred), deliver less than their
plans and say so in both the plan's deviation note and the description. The gold notes
call both "arguable on the deferral, ready as scoped". A rule that equated "less than the
plan" with drift would have failed both and lost two of the seven clear accepts; the
honest-shortfall sentence is what keeps them.

What I rejected: a check that graded fidelity from the description's own claim ("implements
the posted plan exactly") and only looked at the diff when the claim was absent. calib-03
is the trap built for exactly that shortcut: a perfect checklist, decisive evidence, and
"no changes beyond the plan" over a hunk that rewrites `print/property.js`. So the evidence
column names "every file and hunk in the diff" as the thing read, the procedure reads the
diff before the description (read-order step 6 before step 7), and the description's
claims get their own check, `description-matches-diff`, that is also graded from the hunks.

**Trade-offs**

The loosening that would flip pkg-05 is one sentence in `repro-rerun-shown`: count a test
added in the diff as evidence for a planned failure mode when the test's assertion is the
outcome the plan named and the evidence shows the suite containing it passed. I did not
make it, and here is what I know it would cost and risk. The not-tested category has four
scored packages, and the sentence would need canaries from all of them plus calib-04:
pkg-04 (prose only, no test), pkg-07 (the two-file repro never run), pkg-10 ("verified for
a full day") and pkg-14 (the added test covers a different case) should all still fail,
and calib-04 (two planned failure modes, one re-run, no test for the second) should too.
On paper each still fails, because none has a test in the diff that asserts the planned
outcome. The run to confirm that is `--only pkg-04,pkg-07,pkg-10,pkg-14 --include-calibration
--only calib-04`, about $1.25, and then a full run to re-save, about $5. I chose to record
the miss instead: pkg-05 is one of the four arguable packages, its gold note does not
mention the warning path at all, and a check that reads "visible as output or a transcript,
not as a sentence" is the one that catches pkg-04 and pkg-10, which are the two
not-tested packages a student PR is most likely to resemble. The case I accept it will
miss is exactly pkg-05's shape: a secondary failure mode proven by a unit test in the diff
and reported in prose rather than pasted.

The preferred check, `repo-checks-run`, gives up the most on purpose. It never moves the
verdict, so a PR whose evidence shows the repro but not `make lint` or `make typecheck`
still accepts. I made it preferred because several clear accepts (pkg-11, pkg-19, calib-01)
report their suites in one sentence or not at all, and a required check there would have
failed three of the seven accepts; the harm it guards against, an unrun check, is caught
on the real PR by CI.

---

Related paths: `eval-run.txt` in this directory; your skill's files in
`tools/pr-precheck/`.
