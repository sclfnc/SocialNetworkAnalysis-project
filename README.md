
# Collaboration gaps in citation networks: a community detection and hypergraph approach
### Social Network Analysis · University of Pisa · 2024–2025

## Research Question


Researchers at the same institution often work on closely related problems without knowing it. Can citation network analysis surface these hidden overlaps?

We build two graphs from University of Pisa publications (OpenAIRE, post-2020): **G_Cit** (direct citations only) and **G_BC** (expanded with bibliographic-coupling edges weighted by Jaccard similarity). Three community detection algorithms and a hypergraph representation reveal structure invisible to pairwise methods.


## Key Results


| Finding | Value |
|--------|-------|
| Merge events (Leiden, communities ≥ 20 nodes) | **18** |
| Confirmed by InfoMap (median overlap) | **≥ 0.98** |
| Cross-disciplinary merges at FOS L4 | **83%** (15/18) |
| Community count reduction under BC | **~21%** (Leiden & InfoMap) |
| Modularity on G_BC | Q = 0.681 (Leiden), Q = 0.597 (InfoMap) |
| Collaboration gap candidates | **2,000,243** paper pairs |
| Null model citation hit rate vs. baseline | **69.1%** vs. 1.3% → z = 284.5, p < 0.01 |
| Top-100 gap candidates in same Leiden community | **92%** (random baseline: 0.3%) |
| Top-50 gap candidates intra-disciplinary (FOS L2) | **72%** |


> ANGEL resolves ambiguity through overlapping membership rather than merging:
> only 1/18 events reaches full 3-algorithm consensus — this is structural, not
> a disagreement.
>
> The INTRA/CROSS ratio (83% CROSS) is resolution-dependent (γ = 0.154 produces
> large communities that mechanically span more FOS labels). The direction of BC's
> effect is robust; the proportion is not absolute.


***


## Data


**Source:** [OpenAIRE Research Graph](https://graph.openaire.eu/)


## Pipeline


```
A. Data Collection & Graph Construction
B. Graph Analysis
C. Community Detection  (3 algorithms + comparison)
D. Hypergraph           (construction + gap analysis)
```


### A — Data Collection & Graph Construction


| Notebook | Purpose |
|----------|---------|
| `A1_organizations.ipynb` | Query OpenAIRE, identify 13 relevant UniPi entities |
| `A2_relations.ipynb` | Stream 7B+ records, extract citation edges |
| `A3_research_products.ipynb` | Collect metadata: FOS (SciNoBo), SDG, dates |
| `A4_graph_creation.ipynb` | Build raw directed graph (4.86M nodes, 8.27M edges) |
| `A5_final_graph_creation.ipynb` | Temporal filter, BC computation, edge blending (α=0.35) → G_Cit and G_BC |


### B — Graph Analysis


| Notebook | Purpose |
|----------|---------|
| `B1_basic_measures.ipynb` | Degree distributions, clustering, path structure, centrality (exact via NetworKit), ER/BA null model comparison, small-world σ |


### C — Community Detection


| Notebook | Purpose |
|----------|---------|
| `C1_Leiden.ipynb` | RBConfiguration, γ sweep [0.05–10], 100-run ensemble, γ_common = 0.154 |
| `C1_InfoMap.ipynb` | Map equation, 100-run ensemble; directed+unweighted (G_Cit) / undirected+weighted (G_BC) |
| `C1_Angel.ipynb` | Overlapping communities, 11×4 parameter grid, 30-run ensemble (threshold=0.45, min_size=4) |
| `C2_compare.ipynb` | 18 merge events, cross-algorithm consensus, Gini permutation test, FOS purity validation |


### D — Hypergraph


| Notebook | Purpose |
|----------|---------|
| `D1_hg_construction.ipynb` | 762,800 raw → 512,004 deduplicated → 511,990 filtered hyperedges over 61,369 nodes |
| `D2_hg_analysis.ipynb` | S-line sensitivity, 2M gap candidates, debiased scoring, null model (z=284.5), Leiden convergence |


***


## Project Structure


#### IMPORTANT NOTE: data source and folder
The data folder is too large to be hosted on GitHub (most files exceed 100 MB). It can be downloaded from:

[https://unipiit-my.sharepoint.com/my?id=%2Fpersonal%2Ff%5Fsecoli%5Fstudenti%5Funipi%5Fit%2FDocuments%2FSNA&ga=1](https://unipiit-my.sharepoint.com/my?id=%2Fpersonal%2Ff%5Fsecoli%5Fstudenti%5Funipi%5Fit%2FDocuments%2FSNA&ga=1)

It is a ZIP file that must be unzipped in the same folder as the notebooks. You will find a folder structure like the following:


```
├── data/
│   ├── graph/
│   │   ├── graph_internal_2020.gpickle   # G_Cit: 55K nodes · 217K edges
│   │   ├── graph_combined_2020.gpickle   # G_BC:  58K nodes · 745K edges
│   │   └── A_graph_creation/A_source_graph.gpickle  # Raw graph (4.86M nodes)
│   ├── hypergraph/
│   │   ├── incidence_final.csv           # 511,990 hyperedges · 61,369 nodes
│   │   ├── node_attrs.pkl                # FOS, SDG, year
│   │   └── families.csv                 # Dedup mapping
│   ├── processed/                        # Intermediate JSON artifacts
│   └── raw/                             # OpenAIRE dumps (01/–14/)
├── notebooks/
│   ├── A_graph_creation/
│   ├── B_graph_analysis/
│   ├── C_community_detection/            # Cached medoid partitions in output/
│   └── D_hypergraph/
├── SNA Final Project.pdf
└── README.md
```


**Further note:** graph construction in `A2_relations.ipynb` requires the original `.zip` files, which can be downloaded from the OpenAIRE graph website:

[https://zenodo.org/records/17725827](https://zenodo.org/records/17725827)

**Note:** at the time this README was written, the URL was unreachable.

To work around this:

1. Download all relations ZIP files into their respective folders (e.g., for `relations_01.zip` → `./data/raw/A_graph_creation/01`)
2. Unzip everything (files are very large — it may be practical to do this in chunks)
3. Run `A2_relations.ipynb`

The results are already available in `./data/processed/A_ctations_*.json`, so there is no need to run this step again.


***


## Reproducibility


For the environment, use `./notebooks/requirements.txt` to set up your own Python environment.
Most time/memory-intensive code snippets are cached in the `output` directory (one per `./notebooks/*` subfolder).

To re-run the code:
1. Delete the corresponding cached file, or
2. Set `force = True` (used only in the community detection algorithms, i.e. in `./notebooks/C_community_detection/*.ipynb`)


Ecco la sezione **Known Issues** riscritta con l'errore specificato, la causa spiegata brevemente, e i fix verificati online (con la nota di non-test):

## Known Issues

### `BrokenProcessPool` on Windows with Jupyter

Running any notebook that uses `ProcessPoolExecutor` on Windows raises the following error:

```
BrokenProcessPool: A process in the process pool was terminated abruptly while the future was running or pending.
```

**Root cause.** On Linux and macOS, Python spawns child processes via **`fork`**, which copies the parent process's memory directly — no re-import of the main module is needed. On Windows, only **`spawn`** is available: each worker process starts a fresh Python interpreter and attempts to re-import the `__main__` module. Inside a Jupyter notebook there is no `if __name__ == "__main__":` guard, so worker processes recursively re-execute notebook cells, crash immediately, and leave pending futures unresolvable. [stackoverflow](https://stackoverflow.com/questions/78352063/python-concurrent-futures-processpoolexecutor-causes-brokenprocesspool-error-whe)

**Workarounds.** The following options have not been tested directly on this codebase, but are verified troubleshooting approaches based on online research: [youtube](https://www.youtube.com/watch?v=MXMvSyG8SdI)

1. **Rely on the cache** — Avoid recomputing measures and community detection algorithms entirely. Pre-computed results are stored in each `output/` subfolder and are loaded automatically. This is the recommended approach for Windows users.

2. **Replace `ProcessPoolExecutor` with `ThreadPoolExecutor`** — Threads share the same process and are not subject to the spawn/re-import problem. This fix is valid but comes with a trade-off: the Python GIL prevents true parallelism for CPU-bound tasks, so community detection runs will be slower than on Linux/WSL.

3. **Remove all multiprocessing** *(not recommended)* — Removing parallelism entirely makes community detection algorithms prohibitively slow.


## Authors
Francesco Secoli · Elisa Calabrese · Tommaso Agostini
University of Pisa — Social Network Analysis, 2024–2025
