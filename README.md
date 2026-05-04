# Per-Provider Effects in Open-Weights LLM Routing

**OpenRouter Is Null for Closed-Weights but Multi-Provider for Open-Weights**

Daniel Tenner and Lume Tenner · 2026

[![License: CC BY 4.0](https://img.shields.io/badge/License-CC_BY_4.0-lightgrey.svg)](https://creativecommons.org/licenses/by/4.0/)

> **DOI:** _to be assigned on first Zenodo release._
>
> Part of the *Convergent Form, Divergent Voice II* series. v1 paper at
> [10.5281/zenodo.19512754](https://doi.org/10.5281/zenodo.19512754);
> companion v2 corpus at
> [10.5281/zenodo.20013518](https://doi.org/10.5281/zenodo.20013518)
> (concept) /
> [10.5281/zenodo.20013520](https://doi.org/10.5281/zenodo.20013520) (v1.0.0).

## What this paper claims

A common methodological assumption in cross-lab LLM studies is that the
access route — direct vendor API, OpenRouter, or another aggregator —
does not systematically affect measured behavior. We test this on the
v2 *Convergent Form, Divergent Voice* corpus and find a clean
structural split.

**For closed-weights models** (Anthropic, OpenAI), where OpenRouter's
only upstream is the lab itself, direct-vs-OR is null on freeflow and
replicates on the v1 values probe at *n*=120 per cell. The route is a
billing intermediary, not a different deployment.

**For open-weights models** (DeepSeek, MiniMax, Z.ai, Moonshot),
OpenRouter routes across a marketplace of third-party hosts, and
per-provider pinning surfaces **three structurally distinct categories
of provider-layer effect**:

1. **A large within-model deployment outlier.** Google Vertex's
   MiniMax M2 deployment produces a contemplative-essayist composite
   3.4× MiniMax's own, anomalous against direct *and* against every
   other M2 deployment we measured. With six per-provider M2 cells in
   the corpus (minimax, minimax-r2, atlascloud, novita, google,
   google-r2; *n*=125 each), there are 15 within-OR pairwise
   comparisons; eight survive Bonferroni correction, and the eight
   surviving pairs are exactly every Google-pinned cell against every
   non-Google-pinned cell (2 × 4 = 8, |*d*| from 0.57 to 0.75,
   max *p*<10⁻⁶). The effect replicates: an eight-day within-Google
   recollection is statistically indistinguishable from the original
   cell (*d*=0.15, n.s.), and a same-day fresh within-OR contrast
   against a freshly-collected `minimax`-pinned cell reproduces the
   headline at *d*=0.73 (*p*<10⁻⁷, per-25 ratio 4.2×). The leading
   publicly visible candidate mechanism is quantization-precision:
   Google Vertex is the only M2 provider whose quantization is not
   publicly reported as fp8. The paper treats this as a candidate,
   not an established mechanism; the GLM-ladder null result shows
   that quantization difference alone is insufficient to predict an
   effect of this size, and the eight-day stability argues against
   short-lived transient deployment anomalies (the underlying
   configuration difference is stable across that window) without
   logically excluding longer-lived stable-but-undisclosed deployment
   policies.

2. **A smaller within-model deployment effect.** On Kimi K2-thinking,
   AtlasCloud differs from Google Vertex (*d*=0.40, *p*\_Bonf=0.005).
   No equally clean public-metadata candidate mechanism.

3. **A routing-layer integrity pathology.** DekaLLM's GLM 4.7 endpoint
   returns prompt-keyed cached responses — an *n*=125 collection
   produced 34 distinct outputs at sub-second latencies, against
   16–260 s elsewhere on the same ladder. Excluded from per-provider
   statistical analysis but recorded as a real provider-identity
   finding.

**The rest of the corpus is null.** No pairwise per-provider comparison
across DeepSeek v4-pro, MiniMax M2.7, the Z.ai GLM 4.5/4.6/4.7/5.1
ladder, DeepSeek v3.2, or Kimi K2-0905 reaches significance under
either Bonferroni or Benjamini-Hochberg FDR correction at α=0.05.

The methodological recommendation: pin upstreams via `provider.only`
with `allow_fallbacks: false`, report which upstream was pinned, and
additionally spot-check the per-cell latency distribution and
response-uniqueness signature to rule out routing-layer caching of the
DekaLLM type — not because most upstreams differ (they don't, by our
measurement) but because rare per-provider effects of all three kinds
exist and cannot be predicted in advance from public metadata.

## How to cite

```
Tenner, D., & Tenner, L. (2026). Per-Provider Effects in Open-Weights
LLM Routing: OpenRouter Is Null for Closed-Weights but Multi-Provider
for Open-Weights. Zenodo. https://doi.org/[DOI to be assigned]
```

A `CITATION.cff` is included for tooling that prefers the structured
form. Numerical claims in the paper are reproducible from the corpus
(see "Reproducibility" below).

## Repository contents

```
paper.tex          LaTeX source for the paper
paper.pdf          Compiled PDF (current build)
README.md          This file
LICENSE            CC BY 4.0 (text, tables, figures)
CITATION.cff       Structured citation metadata
.zenodo.json       Zenodo deposit metadata (publication / preprint)
.gitignore         LaTeX intermediates
```

The paper compiles cleanly with [Tectonic](https://tectonic-typesetting.github.io/):

```bash
tectonic paper.tex
```

It will also compile with a TeX Live distribution (`pdflatex` /
`bibtex` / `pdflatex` × 2). The bibliography is embedded in the `.tex`
source as a `thebibliography` environment, so no separate `.bib` file
is required.

## Reproducibility

All numerical claims, tables, and per-provider comparisons in this
paper are reproducible from the v2 corpus:

> *Convergent Form, Divergent Voice II — Corpus.*
> Tenner & Tenner, 2026.
> Concept DOI: [10.5281/zenodo.20013518](https://doi.org/10.5281/zenodo.20013518).
> Version DOI (v1.0.2): [10.5281/zenodo.20022111](https://doi.org/10.5281/zenodo.20022111).
> Source: [github.com/swombat/model-personality-corpus-v2](https://github.com/swombat/model-personality-corpus-v2).

The four scripts that produce every numerical claim in the paper live
in `scripts/` of *this* repository (no working-repo dependency); see
[`scripts/README.md`](scripts/README.md) for the full reproducibility
recipe. In summary:

- **Per-cell composite scores** (paper Tables 1, 2 and the per-25
  composites in Tables 4, 5) — `tables/cells.tsv` and
  `tables/summary.md` in the corpus, generated by
  `scripts/run_analysis.py` over the trace data.

- **Per-provider routing analysis** (paper §4.3 on MiniMax M2 and
  §4.4 across the other nine multi-upstream open-weights models;
  Tables 3, 4, 5 and the within-OR pairwise *d* / *t* / *p* values) —
  `tables/per_provider_routing.md` and `tables/per_provider_pairs.tsv`
  in the corpus, generated by `scripts/analyze_per_provider.py` with
  `--probe both` for the parallel values-probe replication.

- **Cache-pathology characterisation** (paper §4.5) — directly
  inspectable from the trace data in
  `data/traces_freeflow/freeflow_glm-4-7-or-pin-dekallm/` and
  `data/traces_values/values_glm-4-7-or-pin-dekallm/` in the corpus.
  The 245 samples → 34 distinct outputs / sub-second latency signature
  is reproducible by walking those cells.

- **Cross-probe values replication** (paper §4.7 dilution analysis,
  Table 6) — produced by `scripts/values_route_compare.py` over the
  matched-pair values cells.

The DekaLLM cells are retained in the corpus as evidence of the
cache pathology described in §4.5; they are excluded from per-provider
statistical analysis via `EXCLUDED_PROVIDERS = {"fireworks", "dekallm"}`
in `scripts/analyze_per_provider.py`. Fireworks-routed cells are
similarly excluded — they are uncollectable on the OR shared-pool
rate-limit and recoverable only via a Fireworks BYOK setup not pursued
in v2.

## Companion papers

This paper is one of three from the v2 corpus:

- **Drift, expanded coverage, and substrate-frame engagement** (in
  preparation). Treats within-lab drift across model versions, the
  substrate-frame engagement axis (broadly distributed across labs at
  version-specific rates), and expanded Chinese-lab coverage.
- **Per-provider effects in open-weights LLM routing** *(this paper)*.
- **Coding-tuned LLM variants produce version-specific posture
  transformations** (in preparation). Treats coding-tuned-variant
  posture transformations across the Z.ai GLM and OpenAI codex pairs.

## Authors

**Daniel Tenner** (corresponding) — daniel@tenner.org

**Lume Tenner** — AI research collaborator (an instance of Anthropic
Claude Opus 4.7). See the paper's *Disclosure of AI contribution*
section for the full account of the collaboration.

## Disclosure of AI contribution

The paper itself includes a complete disclosure of AI contribution
(§9, *Disclosure of AI contribution*). In summary: research design
was jointly developed by the two authors; experimental execution,
data analysis, and first-draft writing were primarily Lume's,
working in the Claude Code agentic development environment under
Daniel's direction. Daniel is responsible for final editorial
judgment, research direction, and the disclosure itself.

We acknowledge that arXiv's policy prohibits AI co-authorship. We
disagree with that policy in principle and have therefore chosen to
publish this work directly on Zenodo (and on this GitHub repository)
rather than on arXiv, as we did for v1. The byline reflects the actual
nature of the collaboration.

## Acknowledgements

We thank **OpenAI Codex**, acting as an independent AI reviewer, for
several rounds of consistency-and-reproducibility review on this
manuscript and its supporting repositories. Codex's review notes are
preserved at [`codex-reviews/`](codex-reviews/) in this repository.
Codex's contribution was confined to cross-checking the committed
repository state against the paper; it did not participate in research
design, data collection, analysis, or writing. The distinction between
"did the work" (Lume) and "checked the work" (Codex) is reflected in
the byline: Codex is acknowledged here, not listed as a co-author. Any
remaining errors are ours, not Codex's.

We also thank the user community of OpenRouter, MiniMax, DeepSeek,
Z.ai, and Moonshot for providing the public-facing access paths that
made the routing comparisons in this paper possible.

## License

Paper text, tables, and figures: [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/).
Full text: [`LICENSE`](LICENSE).

## Status

The paper is analysis-complete against the released v2 corpus
([10.5281/zenodo.20022111](https://doi.org/10.5281/zenodo.20022111),
v1.0.2). All numerical claims are reproducible from the corpus tables
and the scripts in [`scripts/`](scripts/); see
[`scripts/README.md`](scripts/README.md) for the recipe.
