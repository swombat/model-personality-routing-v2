# Codex Review: Routing Paper Split Repository

Date: 2026-05-03

Repository reviewed: `model-personality-routing-v2` local checkout at `/Users/danieltenner/dev/contemplative-essayist-routing-v2`

Related repositories consulted:
- `/Users/danieltenner/dev/contemplative-essayist-probe-v2`
- `/Users/danieltenner/dev/contemplative-essayist-corpus-v2`

Scope: consistency, overclaiming, missing/stale material after splitting the routing paper into its own repository, and reproducibility/documentation alignment.

## Net Assessment

The split paper is substantively coherent. The headline claim has the right shape and is supported by the current corpus repository: closed-weights OpenRouter access is treated as lab-upstream/null; open-weights access is treated as multi-provider; the three-provider-effect taxonomy (M2 Google Vertex, Kimi K2-thinking AtlasCloud-vs-Google, DekaLLM cache pathology) is consistently carried through abstract, results, discussion, and conclusion.

I did not find evidence that the split paper lost a major scientific finding from the unified/source version. The local split `paper.tex` is identical to `papers/routing/paper.tex` in the working probe repository. The current corpus repository has also incorporated the important DekaLLM/Fireworks exclusions in `scripts/analyze_per_provider.py` and in `tables/per_provider_routing.md`, which resolves the largest possible reproducibility concern I initially checked for.

The remaining issues are mostly release-consistency and claim-precision problems. The most important one is the stale paper date: the paper is dated April 2026 while describing data collected on 2026-05-01/02 and while the repo README says v1.0.0 is 2026-05-03.

## Findings

### 1. Paper date is stale and predates described data collection

Severity: medium-high

`paper.tex` sets `\date{April 2026}` at line 44, but the methods state that the per-provider extension was collected on `2026-05-01/02` at line 223. The README status says `v1.0.0 (2026-05-03)` at lines 216-220, and `CITATION.cff` uses `date-released: 2026-05-03`.

This makes the PDF look temporally impossible: a paper dated April reports May 1/2 collection. I would change the paper date to `May 2026`, or explicitly date it `May 3, 2026`.

Evidence:
- `paper.tex:44`
- `paper.tex:223`
- `README.md:216-220`
- `CITATION.cff:45`

### 2. README reproducibility section has stale section/table references

Severity: medium

The README still refers to the cross-probe values replication as "paper §4.5 dilution analysis, Table 7" at lines 136-137. In the current paper, §4.5 is the DekaLLM cache-pathology section, the cross-probe values replication is §4.7, and the values table is Table 6 (`tab:routing-values`), not Table 7.

This is easy to fix, but it matters because the README is the first thing a reader will use to reproduce the paper. It currently sends them to the wrong section.

Evidence:
- `README.md:128-139`
- `paper.tex:283-287` (`sec:routing-cache-pathology`)
- `paper.tex:297-320` (`sec:cross-probe`, Table `tab:routing-values`)

Suggested fix:
- Change "paper §4.5 dilution analysis, Table 7" to "paper §4.7 dilution analysis, Table 6".
- Consider tightening the earlier bullet at `README.md:121` as well: per-provider routing analysis mainly supports §§4.3-4.4 and the values-probe context in §4.7; §4.6 is the DeepSeek-v3.2 unresolvable case.

### 3. Corresponding-author email differs between PDF and README

Severity: medium

The PDF author footnote uses `daniel@danieltenner.com`, while the README author section uses `daniel@tenner.org`. This is not a scientific issue, but it is a visible publication-metadata inconsistency.

Evidence:
- `paper.tex:42`
- `README.md:168-175`

Suggested fix:
- Pick one corresponding email and use it in both places.

### 4. Closed-weights "null on freeflow" phrasing is mostly acceptable, but should remain carefully bounded

Severity: low-medium

The paper now does a good job in the body of separating three levels of evidence: first-round n=25 freeflow comparisons, a GPT-5.5 repeated freeflow follow-up, and values-probe replication at n=120 per cell. The abstract and README compress this into "direct-vs-OR is null on freeflow and replicates on the v1 values probe at n=120 per cell."

That wording is no longer clearly wrong, but it can still be read as "all closed-weights freeflow pairs were statistically tested at high power," which is not the design. The body is careful enough that I would not treat this as a blocking issue. If tightening before release, use one sentence closer to the body:

> For closed-weights models, first-round freeflow route deltas sit within the n=25 noise floor; a repeated GPT-5.5 freeflow follow-up confirms the null directly; and the values probe replicates the null at n=120 per cell.

Evidence:
- `paper.tex:51`
- `paper.tex:172-180`
- `paper.tex:297-320`
- `README.md:26-29`

### 5. Quantization is framed carefully in the body, but the README headline version is slightly too causal

Severity: low-medium

The paper body is appropriately cautious: it repeatedly says quantization is a leading publicly visible candidate, not an established mechanism, and explicitly says GLM-ladder nulls show quantization difference is insufficient by itself. The README's bullet says "The leading publicly visible candidate mechanism is quantization-precision" and then immediately explains the metadata basis. That is basically correct, but the README does not include the later caveat that GLM evidence weakens quantization as a standalone explanation.

This is not a contradiction, but because README summaries tend to be quoted, I would add a short caveat after lines 40-43:

> The paper treats this as a candidate, not an established mechanism; the GLM-ladder null shows quantization difference alone is insufficient.

Evidence:
- `README.md:36-43`
- `paper.tex:96`
- `paper.tex:342-344`
- `paper.tex:359`
- `paper.tex:383`

### 6. Acknowledgement text in split repo points to old review filenames not present here

Severity: low

The paper's AI-contribution disclosure and acknowledgements cite review files at `codex-review-comments/01-codex-review-findings.md` and `02-codex-review-findings.md`. Those files exist in the working probe repository, not this split publication repository. The README similarly points to the working repo's `codex-review-comments/`.

This may be intentional: the paper says the reviews were committed in the "repository root" of the v2 working repository, and the README links there. But now that this split repo has its own `codex-reviews/` folder, future readers may expect the cited review files to exist here too.

Suggested options:
- Leave the historical reference as-is, but add a sentence that the split-repo review notes live in `codex-reviews/`.
- Or mirror the relevant prior review files into this split repo if you want a self-contained publication artefact.

Evidence:
- `paper.tex:395`
- `paper.tex:429`
- `README.md:195-199`
- this file: `codex-reviews/01-routing-paper-consistency-review.md`

### 7. README DOI placeholder is expected pre-Zenodo, but should be the last release checklist item

Severity: low

The README still says "DOI: to be assigned on first Zenodo release" and the citation block contains `[DOI to be assigned]`. This is fine before deposition, but since the repo status says v1.0.0 and the corpus DOI is already present, this should be checked immediately after Zenodo deposit.

Evidence:
- `README.md:9`
- `README.md:69-75`

## Checks That Passed

- `paper.tex` in the split repo is identical to `papers/routing/paper.tex` in `/Users/danieltenner/dev/contemplative-essayist-probe-v2`; no split-specific prose drift was introduced.
- The current corpus repository excludes `fireworks` and `dekallm` from `scripts/analyze_per_provider.py` via `EXCLUDED_PROVIDERS = {"fireworks", "dekallm"}`.
- The current corpus `tables/per_provider_routing.md` reports GLM 4.7 with 10 providers and 45 pairs, matching the paper's exclusion of DekaLLM from the per-provider statistical tables.
- The current corpus `tables/per_provider_pairs.tsv` gives GLM 4.7 phala-vs-siliconflow as `d=0.408`, raw `p=0.001465`, Bonferroni `0.06593`, FDR `0.06593`, matching the paper's rounded `d=0.41` and `q=0.066`.
- The M2 Google Vertex claim is supported by corpus `tables/per_provider_pairs.tsv`: google-vs-novita `d=0.762`, google-vs-minimax `d=0.659`, atlascloud-vs-google `d=-0.564`, all Bonferroni-surviving.
- The Kimi K2-thinking claim is supported by corpus `tables/per_provider_routing.md`: AtlasCloud per-25 `48.8`, Google per-25 `27.8`, and AtlasCloud-vs-Google `d=0.40`, raw `p=0.0018`, Bonferroni `0.005`.
- The paper compiles successfully with `tectonic paper.tex`. Only minor underfull/overfull box warnings appear; no missing refs or citations surfaced.

## Overall Recommendation

I would fix findings 1-3 before tagging a final release. Findings 4-7 are polishing/reader-navigation issues rather than threats to the main claim. The scientific throughline is intact, and the paper is now much more careful than the earlier unified version about the key methodological point: pin upstreams for open-weights models because rare provider effects exist, not because most upstreams differ.
