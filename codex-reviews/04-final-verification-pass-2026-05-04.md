# Codex Review: Final Verification Pass After Lume Fixes

Date: 2026-05-04

Scope: final verification pass after commit `41d72e9` (`Codex final publication-readiness review fixes`). I rechecked the issues from the prior review: stale routing-repo scripts, stale `v1.0.1`/`v1.0.0` references, README/CFF DOI consistency, overstrong transient-anomaly wording, logbook-style prose, reproducibility checks, and public GitHub/Zenodo metadata.

## Net assessment

The paper is now in substantially good shape for publication. The main scientific claims remain reproducible from the corpus, the previous blocker about the stale routing-repo `scripts/analyze_per_provider.py` is fixed, the local README/CFF/paper metadata now consistently cite corpus `v1.0.2`, and the earlier "rules out transient deployment anomalies" wording has been softened appropriately.

I found no remaining blocker in the local paper repository. The remaining issues are publication-polish / external-metadata items:

1. a local prose inconsistency in how the DekaLLM duplicate-output count is summarized; and
2. the public Zenodo corpus `v1.0.2` record still advertises the old 226-cell corpus size.

## Findings

### 1. Medium: DekaLLM duplicate-output count is compressed inaccurately in the abstract/README

The paper and README sometimes say that an `n=125` DekaLLM collection produced 34 distinct outputs:

- `paper.tex:51`: "an `n=125` collection produced 34 distinct outputs"
- `paper.tex:65`: same phrasing in the introduction
- `README.md:66-69`: "`n=125` collection produced 34 distinct outputs"

But the more precise description later in the paper says the 34 distinct outputs come from the combined freeflow + values DekaLLM material:

- `paper.tex:289`: "the 245 freeflow-and-values samples in the cell reduce to 34 distinct response strings"
- `paper.tex:350`: "a 245-sample collection collapsed to 34 distinct strings"
- `scripts/analyze_per_provider.py:127-128`: "245 valid samples collapsed into 34"
- `README.md:149-154` and `scripts/README.md:83-87`: "245 samples -> 34 distinct outputs"

I checked the trace data directly:

```text
freeflow DekaLLM: 125 files, 17 distinct result strings
values DekaLLM:   120 files, 17 distinct result strings
combined:         245 files, 34 distinct result strings
```

So the correct compact claim is `245 freeflow-and-values samples -> 34 distinct outputs`, or if referring only to the freeflow cell, `125 freeflow samples -> 17 distinct outputs`.

Suggested fix: change the abstract/introduction/README summary phrasing to "245 freeflow-and-values samples collapsed to 34 distinct outputs" rather than "`n=125` produced 34 distinct outputs." This avoids a small but visible mismatch inside the paper.

### 2. Medium: public Zenodo corpus v1.0.2 metadata is still stale at 226 cells

The local paper, README, and CFF now correctly cite corpus `v1.0.2` / DOI `10.5281/zenodo.20022111`, and the local corpus repository has the corrected 228-cell / 19,333-sample totals.

Clarification after rechecking the records: the older `v1.0.0` version record `10.5281/zenodo.20013520` / `https://zenodo.org/records/20013520` has already been edited and now says 228 model-route cells. The remaining stale metadata is on the `v1.0.2` record that the paper cites as its reference version: `10.5281/zenodo.20022111` / `https://zenodo.org/records/20022111`.

At the time of this check, the public Zenodo API record for `10.5281/zenodo.20022111` still reports:

```text
version: v1.0.2
description: ... 49 large language models across 226 model-route cells ...
```

This is now the most visible remaining external inconsistency. It does not affect the paper's local claims, but because the manuscript directs readers to the `v1.0.2` Zenodo corpus DOI, the `20022111` description should be updated from 226 to 228 cells before final publication announcement.

### 3. Low: a little release-provenance language remains outside the paper

The manuscript itself is no longer written like a logbook. The only remaining logbook-like phrasing I noticed in the publication-facing repo is mild and mostly outside the paper:

- `paper.tex:139`: "Two-cell M2 per-provider replication set (added 2026-05-04)" is defensible as collection-date provenance, but could be tightened to "Two-cell M2 per-provider replication set, collected 2026-05-04" to sound less like a changelog.
- GitHub release text for the latest public routing release (`v1.1.0`) still includes "What's new", "Numerical updates from corpus v1.0.1", and "What didn't change" release-note framing. That is fine for a release page, but if this exact text is treated as a publication landing page, it still reads more like a changelog than a stable publication summary.

I would not block publication on either item.

## Checks performed

### Local repository consistency

- `rg` over `paper.tex`, `README.md`, `CITATION.cff`, `scripts/`, and `.zenodo.json` found no remaining live `v1.0.1`, `v1.0.0`, "226 cells", or "rules out transient" references outside the preserved historical review files.
- `README.md` top metadata now points to corpus concept DOI `10.5281/zenodo.20013518` and version DOI `10.5281/zenodo.20022111` (`v1.0.2`, this paper's reference corpus version).
- `CITATION.cff` references the corpus DOI `10.5281/zenodo.20022111` and describes it as `v1.0.2`.
- The data-availability section in `paper.tex` now cites both the corpus concept DOI and the `v1.0.2` version DOI.
- The transient-anomaly wording in `paper.tex:363` now says the replication "argues against short-lived transient deployment anomalies" and explicitly does not rule out longer-lived stable policies/configurations.

### Script reproducibility

The routing-repo scripts now match the corpus scripts byte-for-byte:

```text
scripts/analyze_per_provider.py  == corpus copy
scripts/run_freeflow_multi.py    == corpus copy
scripts/values_route_compare.py  == corpus copy
```

This resolves the prior high-severity reproducibility issue. In particular, the local `analyze_per_provider.py` now includes the M2 six-cell family, excludes both `fireworks` and `dekallm`, and defaults/works correctly with `--probe both`.

### Analysis reruns

In the corpus repository:

```bash
python3 scripts/analyze_per_provider.py --probe both
```

completed successfully and reported the expected analysis set:

```text
[freeflow] minimax/minimax-m2 : 6 cells
[values  ] minimax/minimax-m2 : 0 cells
[glm-4-7/freeflow] excluded dekallm(125)
[glm-4-7/values]   excluded dekallm(120)
```

It regenerated `tables/per_provider_routing.tsv`, `tables/per_provider_pairs.tsv`, and `tables/per_provider_routing.md` with no git diff afterward.

```bash
python3 scripts/values_route_compare.py
```

also completed successfully and reproduced the matched-pair values table.

### Build

```bash
tectonic paper.tex
```

completed successfully. Remaining layout messages are minor:

```text
Overfull hbox 3.79523pt at paper.tex:199-210
Overfull hbox 3.98917pt at paper.tex:346-347
several underfull hbox warnings
```

The previous severe overfull (~94pt) is gone. I restored the generated `paper.pdf` afterward so the working tree was not dirtied by the compile.

### Public metadata checks

- Public corpus GitHub latest release: `v1.0.2`.
- Public corpus Zenodo `v1.0.0` record: DOI `10.5281/zenodo.20013520`, description now says 228 cells.
- Public corpus Zenodo `v1.0.2` record: DOI `10.5281/zenodo.20022111`, description still says 226 cells.
- Public routing GitHub latest release: `v1.1.0`, correctly says the paper has not yet been deposited to Zenodo and that the DOI placeholder is accurate.

## Bottom line

From the local repository's point of view, the prior serious issues are fixed. I would make the DekaLLM `125 vs 245` wording correction before final publication, and I would update the Zenodo corpus `v1.0.2` record (`10.5281/zenodo.20022111`) description from 226 to 228 cells. After those, I do not see remaining consistency or reproducibility concerns.
