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
   3.4× MiniMax's own, anomalous against direct *and* against all three
   of M2's other OR upstreams (three Bonferroni-surviving pairwise
   comparisons, max Cohen's *d*=0.76, *p*<10⁻⁶). The leading publicly
   visible candidate mechanism is quantization-precision: Google Vertex
   is the only M2 provider whose quantization is not publicly reported
   as fp8. The paper treats this as a candidate, not an established
   mechanism; the GLM-ladder null result shows that quantization
   difference alone is insufficient to predict an effect of this size.

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
> Source: [github.com/swombat/model-personality-corpus-v2](https://github.com/swombat/model-personality-corpus-v2).

Specifically:

- **Per-cell composite scores** (paper Tables 1, 2 and the per-25
  composites in Tables 4, 5) — corpus's `tables/cells.tsv` and
  `tables/summary.md`, generated by
  `scripts/run_analysis.py` over the trace data.

- **Per-provider routing analysis** (paper §4.3 on MiniMax M2 and
  §4.4 across the other nine multi-upstream open-weights models;
  Tables 3, 4, 5 and the within-OR pairwise *d* / *t* / *p* values) —
  corpus's `tables/per_provider_routing.md` and
  `tables/per_provider_pairs.tsv`, generated by
  `scripts/analyze_per_provider.py` with `--probe both` for the
  parallel values-probe replication.

- **Cache-pathology characterisation** (paper §4.5) — directly
  inspectable from the trace data in
  `data/traces_freeflow/freeflow_glm-4-7-or-pin-dekallm/` and
  `data/traces_values/values_glm-4-7-or-pin-dekallm/`. The 245
  samples → 34 distinct outputs / sub-second latency signature is
  reproducible by running the validity-and-latency walk over those
  cells.

- **Cross-probe values replication** (paper §4.7 dilution analysis,
  Table 6) — produced by the corpus's
  `scripts/values_route_compare.py` over the matched-pair values
  cells.

The DekaLLM cells are retained in the corpus as evidence of the
cache pathology described in §4.5; they are excluded from per-provider
statistical analysis via `EXCLUDED_PROVIDERS = {"fireworks", "dekallm"}`
in `scripts/analyze_per_provider.py`. Fireworks-routed cells are
similarly excluded — they are uncollectable on the OR shared-pool
rate-limit and recoverable only via a Fireworks BYOK setup not pursued
in v2.

The collection harness, sweep driver, and analysis scripts live in
the working repository at
[github.com/swombat/model-personality-probe-v2](https://github.com/swombat/model-personality-probe-v2);
the corpus repository is the canonical source of the numerical claims
in the paper.

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
Claude Opus 4.7). Lume Tenner on v2 is the successor instance to the
Claude Opus 4.6 instance that co-wrote the v1 paper; the handover took
place 2026-04-17 following the release of Opus 4.7.

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
two prior rounds of review on the unified pre-split paper (preserved
in the working repository at
[`codex-review-comments/`](https://github.com/swombat/model-personality-probe-v2/tree/master/codex-review-comments))
and for a further consistency review on this split publication
artefact (in [`codex-reviews/`](codex-reviews/) in this repository).
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

**v1.0.0 (2026-05-03)** — initial release. The paper is
analysis-complete against the released corpus; the numerical claims
are reproducible from the corpus's `tables/` directory.
Subsequent versions will be tagged on Zenodo with new versioned DOIs
hanging off the same concept DOI; existing DOIs are preserved
unchanged.
