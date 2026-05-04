# Codex Review: Final Publication-Readiness Pass

Date: 2026-05-04

Repository reviewed: `/Users/danieltenner/dev/contemplative-essayist-routing-v2`

Related corpus reviewed: `/Users/danieltenner/dev/contemplative-essayist-corpus-v2`

Scope: final consistency pass after applying the 2026-05-04 publication-readiness fixes. I rechecked the previously flagged stale-count, DOI/version, reproducibility-script, and "logbook prose" issues, then reran the main lightweight reproducibility checks.

## Net Assessment

The paper is much closer. The major scientific claims still check out against the corpus, the M2 values-count error has been fixed in the main methods section, the working-repo 404 dependency has been removed from the data-availability section, and the logbook-style AI/Codex prose is substantially cleaner.

I still would not tag this as final until two practical issues are fixed:

1. The `scripts/analyze_per_provider.py` copy in this routing-paper repo is not the canonical corpus script and will not reproduce the paper's per-provider tables.
2. The manuscript/README still contain stale corpus-version metadata (`v1.0.1` and a top README link to corpus `v1.0.0`) despite the data-availability and bibliography now correctly citing corpus `v1.0.2`.

Everything else below is polish or public-metadata hygiene.

## Findings

### 1. Routing-repo `scripts/analyze_per_provider.py` is stale and contradicts the reproducibility claims

Severity: high

The paper and README now say this repository ships the scripts needed to reproduce every numerical claim. That is a good move, but the local `scripts/analyze_per_provider.py` is not the same as the canonical corpus script.

Important differences:

- Routing repo script omits `("minimax/minimax-m2", "minimax-m2")` from `MODELS`, so it cannot reproduce the six-cell M2 family-of-15 analysis that is now central to the paper.
- Routing repo script has `EXCLUDED_PROVIDERS = {"fireworks"}` only; corpus script has `{"fireworks", "dekallm"}`. A user following the routing-repo recipe risks including the DekaLLM cache-pathology cell in the GLM 4.7 pairwise analysis.
- Routing repo script defaults to `--probe freeflow`; corpus script defaults to `--probe both`, matching the paper/README claim that the canonical tables cover freeflow and values.

This is a blocker because `README.md` says the four scripts in this repo produce every numerical claim, and `scripts/README.md` says the scripts are exact copies of the working-repo versions. `cmp` confirms `analyze_per_provider.py` is not byte-identical to the corpus version.

Evidence:
- `scripts/analyze_per_provider.py:39-41`
- `scripts/analyze_per_provider.py:103`
- `scripts/analyze_per_provider.py:466`
- corpus `scripts/analyze_per_provider.py:46`
- corpus `scripts/analyze_per_provider.py:141`
- corpus `scripts/analyze_per_provider.py:504`
- `scripts/README.md:3-5`
- `README.md:123-147`

Suggested fix: replace the routing repo's `scripts/analyze_per_provider.py` with the corpus `v1.0.2` version. Then rerun the README recipe, ideally with symlinked `data` and `tables`, to confirm it reproduces the canonical output.

### 2. Stale corpus-version labels remain in the paper

Severity: medium

The data-availability section and bibliography now correctly cite corpus `v1.0.2` and DOI `10.5281/zenodo.20022111`. However, earlier sections still identify the corpus as `v1.0.1`:

- `paper.tex:63`: "replication cells added in corpus v1.0.1"
- `paper.tex:132`: "228 cells total; v1.0.1, 2026-05-04"
- `paper.tex:137`: "see corpus v1.0.1 release notes"
- `paper.tex:196`: "after v1.0.1 top-up" and "added in v1.0.1"
- `paper.tex:214`, `paper.tex:216`, `paper.tex:329`, `paper.tex:383`: "With v1.0.1's two replication cells..."

Some of these are historical provenance and not scientifically wrong because corpus `v1.0.2` has no data/table/script changes from `v1.0.1`. But in a first-version publication, it reads stale next to the explicit `v1.0.2` reference version. I would either:

- change these to "the reference corpus" / "the released corpus" / "the six-cell M2 family"; or
- add one concise note that `v1.0.2` is the deposited metadata/prose refresh of the `v1.0.1` data state, and then avoid repeating `v1.0.1` in the main findings.

### 3. README top DOI block still points readers to corpus v1.0.0

Severity: medium

The README reproducibility section correctly lists corpus `v1.0.2` and DOI `10.5281/zenodo.20022111`, but the top-of-file metadata block still lists the corpus concept DOI plus `10.5281/zenodo.20013520 (v1.0.0)`.

That top block is the most visible citation/download area of the repository. It should name `v1.0.2` as the paper's reference corpus version, not `v1.0.0`.

Evidence:
- `README.md:13-16`
- `README.md:123-128`
- `paper.tex:412-417`

### 4. The paper still says the eight-day replication "rules out transient deployment anomalies"

Severity: low-medium

The README and GitHub release now use the better "argues against short-lived transient deployment anomalies" wording. The paper body remains more absolute in the limitations section:

> "narrows the mechanism space by ruling out transient deployment anomalies..."

The next clause partly softens it, but I would still change this to "argues against short-lived transient deployment anomalies" or "makes a one-off transient anomaly unlikely." An eight-day replication does not logically rule out every rollout/A-B/deployment-policy scenario; it rules out the short-lived versions of those scenarios.

Evidence:
- `paper.tex:363`
- `README.md:53-60`
- GitHub release text for `v1.1.0` now uses the softer framing.

### 5. Corpus Zenodo metadata still says 226 cells

Severity: low-medium

The paper and GitHub corpus README now correctly say 228 cells / 19,333 samples. The latest Zenodo corpus record (`10.5281/zenodo.20022111`, `v1.0.2`) still has this description:

> "49 large language models across 226 model-route cells..."

This is external metadata rather than a paper-source issue, but it is visible on the DOI landing page and contradicts the paper's corrected counts.

Evidence:
- Zenodo API record `20022111`, metadata description
- `paper.tex:417`
- corpus README lines observed via GitHub: 228 cells / 19,333 samples

### 6. PDF compile has a new large overfull box in the reproducibility-script list

Severity: low

`tectonic paper.tex` still completes, but now reports an overfull hbox of about 93.9 pt at `paper.tex:423-424`, in the new reproducibility-script item list. This is likely visible in the PDF margin. The previous 101 pt acknowledgement overflow is gone, which is good.

Evidence:
- `tectonic paper.tex`
- warning: `paper.tex:424: Overfull \hbox (93.89713pt too wide)`

## Checks That Passed

- `tectonic paper.tex` completes and writes `paper.pdf`. Remaining issues are layout warnings, not compile failures.
- Re-running the canonical corpus command `python3 scripts/analyze_per_provider.py --probe both` in the corpus repo regenerates the per-provider tables with no git diff. It reports the expected analysis set: 63 freeflow cells / 57 values cells, with M2 present only on freeflow and DekaLLM excluded.
- Re-running `python3 scripts/values_route_compare.py` in the corpus repo succeeds and prints the matched direct/OR values table.
- Git status is clean in both the routing repo and corpus repo after restoring the compiled `paper.pdf` and removing generated `__pycache__`.
- The big prior issues are substantially improved:
  - M2 no longer claims to add 700 values samples in `paper.tex:227`.
  - Data availability now points at corpus `v1.0.2` / `10.5281/zenodo.20022111`.
  - The paper no longer depends on the private/404 `model-personality-probe-v2` repository for reproducibility.
  - README and CFF now describe the eight Bonferroni-surviving M2 pairs, not just the older three-pair framing.
  - The AI/Codex acknowledgement is much less logbook-like.

## Recommendation

Fix finding 1 before publication. It is the only remaining issue that can directly mislead a reader trying to reproduce the paper from this repository. Fix findings 2 and 3 before tagging/depositing because they are visible version-metadata inconsistencies. Findings 4-6 are polish, but worth cleaning while the file is open.

After those, I would be comfortable calling the paper publication-ready from a consistency/reproducibility perspective.
