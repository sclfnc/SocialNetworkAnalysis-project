
# Collaboration gaps in citation-based networks: a community detection and hypergraph approach
### Social Network Analysis · University of Pisa · 2024–2025

## Research Question

Researchers at the same institution often work on closely related problems without knowing it. Can citation network analysis surface these hidden overlaps?

We build two graphs from University of Pisa publications (OpenAIRE, post-2020): **G_Cit** (direct citations only) and **G_BC** (expanded with bibliographic-coupling edges weighted by Jaccard similarity). Three community detection algorithms and a hypergraph representation reveal structure invisible to pairwise methods.


## Key Results

- Adding "shared-reference" links to the citation graph reveals **18 research groups** that looked separate under citations alone but are actually working on related topics.
- The hypergraph surfaces **~2 million paper pairs** that share a common reference pool without ever citing each other — potential collaboration gaps.
- Of the **top-100** most likely gaps, **95 are genuine** (5 duplicates removed): all 95 fall inside the same research cluster under three independent algorithms, against a random baseline near zero.
- Only **7 pairs** involve two recent papers (post-2020) — those are the ones where a missed collaboration could still happen today.

**Open questions.** The analysis is structural: shared references indicate thematic proximity, not a confirmed missed collaboration. Turning these candidates into actual recommendations would require resolving papers to individual researchers (e.g. via ORCID) and validating retrospectively whether flagged 2020 pairs eventually co-authored by 2025.


---


## Data

**Source:** [OpenAIRE Research Graph](https://graph.openaire.eu/)


---


## Pipeline

The analysis runs in five stages. Each notebook folder maps directly to a section of the report.

| Folder | Report section |
|--------|----------------|
| `notebooks/A_graph_creation/` | §2 Data Collection and Graph Creation |
| `notebooks/B_graph_analysis/` | §3 Network Characterization |
| `notebooks/C_community_detection/` | §4 Community Detection |
| `notebooks/D_hypergraph/` | §5 Hypergraph Analysis |
| `notebooks/E_open_question/` | §6 Latent Intellectual Communities in a Research Institution |


---


## Project Structure

```
├── data/          # graphs, hypergraph, intermediate and raw OpenAIRE data
├── notebooks/
│   ├── A_graph_creation/
│   ├── B_graph_analysis/
│   ├── C_community_detection/
│   ├── D_hypergraph/
│   └── E_open_question/
├── tex/           # LaTeX report source
└── report.pdf
```

#### Data folder

The data folder is too large to be hosted on GitHub. Download it from:

[https://unipiit-my.sharepoint.com/my?id=%2Fpersonal%2Ff%5Fsecoli%5Fstudenti%5Funipi%5Fit%2FDocuments%2FSNA&ga=1](https://unipiit-my.sharepoint.com/my?id=%2Fpersonal%2Ff%5Fsecoli%5Fstudenti%5Funipi%5Fit%2FDocuments%2FSNA&ga=1)

Unzip the archive in the project root. Intermediate artifacts in `data/processed/` are already included, so reconstructing the graphs from raw OpenAIRE dumps is not required.

> **Note:** raw relation dumps can be downloaded from [Zenodo](https://zenodo.org/records/17725827). At the time of writing, that URL was unreachable.


---


## Reproducibility

Set up the environment with `./notebooks/requirements.txt`.

Most time- and memory-intensive steps are cached in each notebook subfolder's `output/` directory and load automatically. To force recomputation, delete the cached file or set `force = True` (community detection notebooks only).


---


## Known Issues

### `BrokenProcessPool` on Windows with Jupyter

Running any notebook that uses `ProcessPoolExecutor` raises:

```
BrokenProcessPool: A process in the process pool was terminated abruptly while the future was running or pending.
```

**Root cause.** Windows uses `spawn` instead of `fork` for child processes. Inside Jupyter there is no `if __name__ == "__main__":` guard, so worker processes recursively re-execute notebook cells and crash immediately.

**Workarounds:**
1. **Rely on the cache** — pre-computed results load automatically. Recommended for Windows users.
2. **Replace `ProcessPoolExecutor` with `ThreadPoolExecutor`** — avoids the spawn issue, but the GIL limits CPU-bound parallelism for community detection.
3. **Remove all multiprocessing** *(not recommended)* — makes community detection prohibitively slow.


---


## Authors

Francesco Secoli · Elisa Calabrese · Tommaso Agostini
University of Pisa — Social Network Analysis, 2024–2025
