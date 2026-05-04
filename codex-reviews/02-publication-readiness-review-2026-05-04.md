# Codex Review: Publication Readiness Pass

Date: 2026-05-04

Repository reviewed: `/Users/danieltenner/dev/contemplative-essayist-routing-v2`

Related corpus reviewed: `/Users/danieltenner/dev/contemplative-essayist-corpus-v2`

Public artefacts checked:
- GitHub: `swombat/model-personality-routing-v2`
- GitHub: `swombat/model-personality-corpus-v2`
- Zenodo concept DOI: `10.5281/zenodo.20013518`
- Zenodo corpus v1.0.0 DOI: `10.5281/zenodo.20013520`
- Zenodo latest corpus DOI observed during review: `10.5281/zenodo.20022111` (`v1.0.2`)

## Net Assessment

The central freeflow result is reproducible from the current corpus tables: the MiniMax M2 Google Vertex effect is present, the six-cell M2 within-OR family has eight Bonferroni-surviving Google-vs-non-Google pairs, Kimi K2-thinking AtlasCloud-vs-Google is the one smaller surviving per-provider effect, and the other open-weights ladders are null under correction. The paper also compiles with `tectonic paper.tex`; only layout warnings appear.

I would not call the artefact publication-ready yet. The major issues are not with the headline freeflow statistics, but with release metadata, public reproducibility links, one stale/misleading values-probe count explanation, and several places where the manuscript and README read like a changelog of prior paper states rather than a first-version publication.

## Findings

### 1. The manuscript incorrectly says the six M2 per-provider cells add 700 values samples

Severity: high

`paper.tex:227` says the six M2 per-provider cells contribute "a further 750 freeflow samples and 700 values samples", bringing the corpus-wide per-provider total to 63 freeflow cells / 7,826 freeflow samples / 6,828 values samples.

The current corpus does not contain per-provider values cells for `minimax-m2`. `scripts/analyze_per_provider.py` explicitly notes that MiniMax M2 is "Freeflow only -- no per-pin values cells were collected" (`scripts/analyze_per_provider.py:60-61` in the corpus repo). Re-running `python3 scripts/analyze_per_provider.py --probe both` reports:

- `minimax/minimax-m2`: 6 freeflow cells
- `minimax/minimax-m2`: 0 values cells
- total analyzed per-provider freeflow: 63 cells / 7,826 samples
- total analyzed per-provider values: 57 cells / 6,828 samples

So the totals are partly right, but the explanation is wrong. Correct framing: the non-M2 per-provider extension contributes 57 freeflow cells / 7,076 freeflow samples and 57 values cells / 6,828 values samples; the six M2 cells add 750 freeflow samples and zero values samples, producing 63 freeflow cells / 7,826 freeflow samples while values remain 57 cells / 6,828 samples. Combined analyzed per-provider samples are 14,654, not "roughly 13,900" once M2 freeflow is included.

Evidence:
- `paper.tex:63`
- `paper.tex:227`
- corpus `scripts/analyze_per_provider.py:60-61`
- corpus `tables/per_provider_routing.tsv`
- `find data/traces_values ...` shows only `minimax-m2-direct` and `minimax-m2-or`, not `minimax-m2-or-pin-*`

### 2. The public reproducibility repository link returns 404

Severity: high

The paper says the collection harness, analysis scripts, and paper sources live at `https://github.com/swombat/model-personality-probe-v2` (`paper.tex:419-421`, and again in the bibliography for companion papers). That URL returned HTTP 404 during this review. All other URLs extracted from `paper.tex` returned HTTP 200.

If the working repo is meant to be part of the reproducibility chain, it needs to be public before publication or the paper needs to point to a public archived source. If it is intentionally private, the paper should not say readers can verify "all relevant valid samples, all analysis scripts, and all intermediate outputs" from the linked repositories.

Evidence:
- `paper.tex:402`
- `paper.tex:419-421`
- `paper.tex:535-544`
- URL check: `https://github.com/swombat/model-personality-probe-v2` -> 404

### 3. Corpus DOI/version metadata is stale relative to Zenodo latest

Severity: medium-high

The paper says it relies on corpus `v1.0.1`, lists only the concept DOI and `v1.0.0` version DOI, and says the concept DOI resolves to the latest deposited version (`paper.tex:412-417`, `paper.tex:530-533`). At review time, the concept DOI resolves to Zenodo record `20022111`, version `v1.0.2`, DOI `10.5281/zenodo.20022111`. The corpus GitHub latest release is also `v1.0.2`.

The v1.0.2 GitHub release says there are no data/table/script changes from v1.0.1, so this is not a scientific mismatch. It is still a publication metadata problem: a reader following the concept DOI lands on v1.0.2, while the paper cites v1.0.1 and only provides the v1.0.0 version DOI.

There is also stale Zenodo metadata on the latest corpus record: the Zenodo v1.0.2 description says "226 model-route cells", but the local/public corpus and paper correctly say 228 cells / 19,333 samples. The GitHub README has the corrected 228-cell count; Zenodo does not.

Suggested fix:
- Cite the actual latest version DOI `10.5281/zenodo.20022111` if v1.0.2 is the public deposited corpus.
- Or explicitly pin a deposited v1.0.1 DOI if one exists.
- Update Zenodo metadata description from 226 cells to 228 cells.

Evidence:
- `paper.tex:132`
- `paper.tex:412-417`
- `paper.tex:530-533`
- GitHub corpus latest release API: `v1.0.2`, "No data changes from v1.0.1"
- Zenodo latest record observed: `10.5281/zenodo.20022111`, version `v1.0.2`, description still says 226 cells

### 4. Routing-paper DOI/release status is inconsistent

Severity: medium-high

The local/public README still says "DOI: to be assigned on first Zenodo release" and the citation block uses `[DOI to be assigned]` (`README.md:9`, `README.md:80-84`). `CITATION.cff` has no DOI. `.zenodo.json` is present but of course has no DOI until deposit.

At the same time, the public GitHub latest release for `swombat/model-personality-routing-v2` is `v1.1.0` and says "**First Zenodo-deposit release.**" A Zenodo API search for the exact title and title phrase returned no routing-paper record during this review.

If the paper has been deposited, add the DOI everywhere: README, citation block, `CITATION.cff`, and preferably the paper's data/code availability or title-page metadata. If it has not been deposited, the GitHub release text is premature and should not claim first Zenodo deposit.

Evidence:
- `README.md:9`
- `README.md:80-84`
- `CITATION.cff`
- `.zenodo.json`
- GitHub release body for `model-personality-routing-v2` v1.1.0
- Zenodo API search returned zero records for the routing-paper title

### 5. README and CITATION.cff still summarize the M2 result as the old three-pair family

Severity: medium

The manuscript now correctly frames the canonical M2 within-OR result as a six-cell family with 15 comparisons and eight Bonferroni-surviving Google-vs-non-Google pairs (`paper.tex:51`, `paper.tex:214`, `paper.tex:329`, `paper.tex:383`).

The README and CFF still foreground the older four-cell/three-pair summary:

- `README.md:36-40`: "against all three of M2's other OR upstreams (three Bonferroni-surviving pairwise comparisons...)"
- `CITATION.cff:14-18`: "Cohen's d=0.68-0.75 across three Bonferroni-surviving pairs"

That is historically true for the original four-cell comparison, but stale for the current paper's headline and abstract. The README should match the paper's current framing: every comparison between the two Google-pinned cells and the four non-Google-pinned cells survives correction (8 pairs, `|d|=0.57-0.75`). If you want to retain the original four-cell result, put it in a clearly marked provenance/release-note paragraph rather than the top-line claim.

Evidence:
- `paper.tex:51`
- `paper.tex:214`
- `paper.tex:329`
- `README.md:36-40`
- `CITATION.cff:14-18`
- corpus `tables/per_provider_pairs.tsv:17-31`

### 6. Several sections fall into "logbook of prior states" rather than publishable findings

Severity: medium

The user specifically asked about this, and yes: the paper currently contains several passages that read like internal release history or process notes for a versioned working paper. For a first-version publication, these should be tightened to the scientific state at publication time.

Most visible examples:

- `paper.tex:42`: the author footnote says Lume is a successor instance to the Claude Opus 4.6 instance that co-wrote v1, with a 2026-04-17 handover. This is inside the byline footnote, so it is highly prominent. Consider reducing it to "AI research collaborator; an instance of Claude Opus 4.7 (Anthropic). See disclosure."
- `paper.tex:394`: the AI disclosure repeats the handover story and continuity from the v1 repository. Keep the contribution disclosure, but remove handover chronology unless it is essential to reader interpretation.
- `paper.tex:399` and `paper.tex:435`: the Codex disclosure/acknowledgement lists prior review rounds and specific bugs caught in earlier manuscript states. This is classic logbook prose. A publication-facing acknowledgement can simply say Codex reviewed the manuscript/repository for consistency and reproducibility; the detailed list of caught stale dates, stale cross-references, and pre-split inconsistencies belongs in `codex-reviews/` or release notes, not the paper.
- `paper.tex:417`: "v1.0.1 adds two M2..." is useful in data availability, but should be phrased as "The dataset version used here contains..." unless you intentionally want a changelog in the paper.
- `README.md:227-246`: the status section is a release changelog, not a stable landing-page summary. It is fine for GitHub releases, but for the README of a publication artifact it should be shorter and should avoid "v1.0.0 initial release / v1.1.0 strengthened release" framing if this is meant to be version 1 of the paper.

### 7. "Rules out transient deployment anomalies" is too strong in release/README prose

Severity: low-medium

The paper body is mostly careful: it says an A/B rollout, load-balancer epoch, or temporary checkpoint variant "would not be expected" to reproduce across eight days (`paper.tex:218`). The README/status language is stronger: the replication "rules out transient deployment anomalies as a candidate mechanism" (`README.md:236-238`). The public GitHub release says the same.

An eight-day replication strongly narrows the mechanism space, but it does not logically rule out all transient or rollout-based explanations. A persistent A/B treatment, staged rollout, or provider-specific stable-but-undisclosed deployment policy could still last eight days. Prefer "argues against short-lived/transient deployment anomalies" or "makes a one-off transient anomaly unlikely."

Evidence:
- `paper.tex:218`
- `README.md:236-238`
- GitHub release body for `model-personality-routing-v2` v1.1.0

### 8. One overfull box is publication-visible

Severity: low

`tectonic paper.tex` compiles successfully, but it reports an overfull hbox of about 101 pt at `paper.tex:435-436`, in the acknowledgement paragraph with long `codex-review-comments/...` paths. This is likely visible in the PDF margin. Tightening the acknowledgement as suggested in finding 6 may fix it naturally. If the detailed paths remain, wrap them more aggressively or move them into a footnote/review-file reference.

Evidence:
- `tectonic paper.tex`
- warning: `paper.tex:436: Overfull \hbox (101.02779pt too wide)`

## Checks That Passed

- `tectonic paper.tex` completes and writes `paper.pdf`; no missing citations or references were reported, only layout warnings.
- Re-running `python3 scripts/corpus_summary.py` in the corpus repo regenerated the existing corpus summary with no git diff.
- Re-running `python3 scripts/analyze_per_provider.py --probe both` regenerated `tables/per_provider_routing.{md,tsv}` and `tables/per_provider_pairs.tsv` with no git diff.
- The core freeflow claims match the corpus tables:
  - M2 has six freeflow provider cells and eight Bonferroni-surviving Google-vs-non-Google pairs.
  - `google-r2` vs `minimax-r2` is `d=0.726`, raw `p=5.521e-08`.
  - `google` vs `google-r2` is non-significant (`d=-0.146`, raw `p=0.2483`).
  - Kimi K2-thinking AtlasCloud-vs-Google is `d=0.401`, Bonferroni `0.005304`.
  - GLM 4.7 phala-vs-siliconflow is the largest non-surviving effect (`d=0.408`, FDR/Bonferroni about `0.066`).
- All external URLs extracted from `paper.tex` returned HTTP 200 except `https://github.com/swombat/model-personality-probe-v2`, which returned 404.

## Recommendation

Fix findings 1-5 before publication. Finding 6 is also worth addressing before release because it directly affects how polished and first-version-like the paper feels. Findings 7-8 are smaller polish items.

The scientific result is close; the publication wrapper is doing most of the wobbling.
