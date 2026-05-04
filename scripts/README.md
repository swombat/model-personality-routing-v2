# Reproducibility scripts

These four scripts reproduce every numerical claim in the paper from the
released corpus trace data. They are exact copies of the working-repo
versions (kept here to remove the working-repo as a dependency for
reproducing this paper).

| Script | What it does |
|---|---|
| `analyze_all.py` | The v1 freeflow composite-scoring instrument, used unchanged. Computes per-cell composite totals from the released trace data. |
| `analyze_per_provider.py` | Per-provider Welch's *t*-tests with Bonferroni and Benjamini–Hochberg FDR corrections within each model's pairwise family. Produces `tables/per_provider_routing.tsv` and `tables/per_provider_pairs.tsv`. |
| `values_route_compare.py` | Cross-probe values-probe comparison for the matched direct/OR pairs (paper §4.7). |
| `run_freeflow_multi.py` | The collection harness used to produce the per-provider trace data. Reads API keys from environment variables; documented inline. Not needed for reproducing analyses from the released traces. |
| `run_per_provider_sweep.py` | The sweep driver that wraps `run_freeflow_multi.py` to collect per-provider cells across the multi-upstream open-weights catalogue. |
| `run_analysis.py` | Convenience driver that runs `analyze_all.py` over every cell directory and writes `tables/summary.md` and `tables/cells.tsv`. |

## Reproducing the analysis

The scripts expect the corpus trace data at
`<repo-root>/data/traces_freeflow/` and `<repo-root>/data/traces_values/`.
The canonical location of that data is the corpus repository:

> *Convergent Form, Divergent Voice II — Corpus.*
> Tenner & Tenner, 2026.
> Concept DOI: [10.5281/zenodo.20013518](https://doi.org/10.5281/zenodo.20013518).
> Version DOI (v1.0.2): [10.5281/zenodo.20022111](https://doi.org/10.5281/zenodo.20022111).
> Source: [github.com/swombat/model-personality-corpus-v2](https://github.com/swombat/model-personality-corpus-v2).

The simplest reproducibility recipe runs the scripts in the corpus
repository directly (the corpus already ships the same scripts):

```bash
git clone https://github.com/swombat/model-personality-corpus-v2.git
cd model-personality-corpus-v2

# Per-provider pairwise t-tests with Bonferroni/FDR corrections
python3 scripts/analyze_per_provider.py --probe both

# Cross-probe values comparison
python3 scripts/values_route_compare.py
```

Outputs are written to `tables/per_provider_routing.{md,tsv}`,
`tables/per_provider_pairs.tsv`, and to stdout.

If you would prefer to run the scripts from a clone of *this* repository
(the routing paper repo), symlink the corpus data:

```bash
git clone https://github.com/swombat/model-personality-routing-v2.git
git clone https://github.com/swombat/model-personality-corpus-v2.git
cd model-personality-routing-v2
ln -s ../model-personality-corpus-v2/data data
ln -s ../model-personality-corpus-v2/tables tables
python3 scripts/analyze_per_provider.py --probe both
```

Either workflow produces byte-identical tables.

## Verifying paper claims

The paper's headline findings are reproducible as follows:

- **Per-cell composites** (paper Tables 2–4 and the §4.3 per-25 numbers):
  read directly from `tables/per_provider_routing.tsv` and
  `tables/cells.tsv` in the corpus, or regenerate them with
  `analyze_per_provider.py` and `run_analysis.py`.

- **Within-OR pairwise tests** (paper §4.3 family-of-15 / 8-surviving;
  §4.4 Kimi K2-thinking AtlasCloud-vs-Google; GLM ladder largest
  non-surviving effect at d≈0.41):
  `tables/per_provider_pairs.tsv` is the source-of-truth output of
  `analyze_per_provider.py`. Filter on `model == minimax/minimax-m2` for
  the M2 family-of-15 picture; on `model == moonshotai/kimi-k2-thinking`
  for the K2-thinking pairwise comparisons.

- **Eight-day replication** (`google-r2` vs `google`, *d*=0.15, *p*=0.25):
  the replication trace data is in
  `data/traces_freeflow/freeflow_minimax-m2-or-pin-google-r2/` (and
  `-pin-minimax-r2/`); the *t*-test against the original `-pin-google`
  cell is the corresponding row in `tables/per_provider_pairs.tsv`.

- **Cache-pathology characterisation** (paper §4.5): directly
  inspectable from the trace data in
  `data/traces_freeflow/freeflow_glm-4-7-or-pin-dekallm/`. The 245
  samples → 34 distinct outputs / sub-second latency signature is
  reproducible by walking the cell.

- **Cross-probe values replication** (paper §4.7): produced by
  `values_route_compare.py` over the matched-pair values cells.

## Notes on the collection harness

`run_freeflow_multi.py` and `run_per_provider_sweep.py` are included
for transparency about how the trace data was produced. They are *not*
required for reproducing the paper's numerical analyses — those run
entirely from the released traces. Re-running the probes against the
same models will produce substantively similar but not bit-identical
outputs (the underlying models are stochastic at temperature 1.0, and
day-to-day sampling variance for *n*=25 freeflow runs is non-trivial).
