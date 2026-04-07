# Identifying Collaboration Opportunities in Academic Institutions since 2020 through Comparative Community Detection on Citation Networks

## Research Question

Researchers at the same university often work on closely related problems without ever collaborating. Can citation network analysis make these hidden overlaps visible?

We address this by building two citation graphs centered on the University of Pisa — one restricted to internal publications, one expanded with bibliographic coupling — and comparing their community structure across three algorithms. A hypergraph representation then surfaces multi-way relationships invisible to pairwise methods.

## Key Findings

- **BC preserves modular structure while reorganizing it.** Community count drops ~20% (InfoMap) with no loss in semantic purity (FOS L4 entropy improves).
- **34 merges confirmed by all three algorithms** (Leiden, InfoMap, ANGEL). These are properties of the graph, not artifacts of any single method.
- **72% of merge events are intra-disciplinary** — BC primarily reconnects fragmented sub-communities within the same field. Cross-disciplinary merges (28%) are rarer but structurally anchored.
- **Hypergraph co-referencing predicts citation with z=284** (degree-preserving null model, p<0.01). Papers sharing ≥2 external references are 55× more likely to have a direct citation link.
- **92% of top hypergraph gap candidates** land in the same Leiden combined community (random baseline: 0.3%), confirming convergence between pairwise and higher-order methods.
- **2M collaboration gap candidates** identified — paper pairs that cite the same external literature but lack direct citation between them.

## Data

Source: [OpenAIRE Research Graph](https://graph.openaire.eu/) (345M+ records). Filtered to 13 University of Pisa organizations, ~57K research products (since 2020), ~750K edges after bibliographic coupling.

## Pipeline

```
A. Data Collection    →  B. Graph Analysis  →  C. Community Detection  →  D. Hypergraph
   & Graph Construction                          (3 algorithms + compare)    (construction + analysis)
```

### A. Data Collection & Graph Construction

| Notebook | Purpose |
|---|---|
| `A1_organizations.ipynb` | Query OpenAIRE for Pisa-affiliated organizations, identify 13 relevant entities |
| `A2_relations.ipynb` | Extract citation relationships from 7B+ record dumps via streaming |
| `A3_research_products.ipynb` | Collect metadata: authors, FOS, SDG, keywords, publication dates |
| `A4_graph_creation.ipynb` | Build raw citation graph (4.9M nodes, 8.3M edges) |
| `A5_final_graph_creation.ipynb` | Filter to papers from 2020 onwards, compute bibliographic coupling (Jaccard), blend edge weights (α=0.35), produce `graph_internal_2020` and `graph_combined_2020` |

### B. Graph Analysis

| Notebook | Purpose |
|---|---|
| `B1_basic_measures.ipynb` | Degree distributions, clustering, path structure, centrality, comparison with ER/BA null models, small-world index (σ=535–807) |

### C. Community Detection

| Notebook | Purpose |
|---|---|
| `C1_Leiden.ipynb` | `RBConfigurationVertexPartition` with resolution sweep, 100-run ensemble, medoid selection via NMI, Gini permutation test |
| `C1_InfoMap.ipynb` | Map Equation optimization, 100-run ensemble with parallelized medoid selection |
| `C1_Angel.ipynb` | Overlapping communities, parameter calibration sweep, 30-run ensemble, bridge paper topology analysis |
| `C2_compare.ipynb` | Cross-algorithm merge consensus, multi-hop Gini significance test, FOS co-occurrence gap analysis with permutation test, semantic purity validation |

### D. Hypergraph

| Notebook | Purpose |
|---|---|
| `D1_hg_construction.ipynb` | Build hypergraph from external references (512K deduplicated hyperedges, 61K nodes), hub filtering, node attribute enrichment |
| `D2_hg_analysis.ipynb` | S-line graph sensitivity, gap candidate extraction (2M pairs), degree-preserving null model, debiased shortlist, connection to Leiden merge zones, semantic profiling |

## Project Structure

```
├── data/
│   ├── graph/
│   │   ├── graph_internal_2020.gpickle    # G_Cit: 55K nodes, 218K citation edges
│   │   ├── graph_combined_2020.gpickle    # G_BC:  58K nodes, 745K edges (citation + BC)
│   │   └── A_graph_creation/
│   │       └── A_source_graph.gpickle     # Raw citation graph (4.9M nodes)
│   ├── hypergraph/
│   │   ├── incidence_final.csv            # Filtered, deduplicated incidence table
│   │   ├── node_attrs.pkl                 # FOS, SDG, year per node
│   │   ├── families.csv                   # Hyperedge dedup mapping
│   │   └── build_meta.json
│   ├── processed/                         # Intermediate JSON artifacts
│   └── raw/                               # OpenAIRE data dumps (01/–14/)
│
├── notebooks/
│   ├── A_graph_creation/                  # 5 notebooks + output/
│   ├── B_graph_analysis/                  # 1 notebook + output/
│   ├── C_community_detection/             # 4 notebooks + output/ (cached partitions)
│   └── D_hypergraph/                      # 2 notebooks + output/
│
├── SNA Final Project.pdf                  # Full report
└── README.md
```

## Reproducibility

All community detection results are cached in `output/` directories as pickle files. Re-running a notebook loads from cache automatically; pass `force=True` to recompute.

Required packages: `networkx`, `igraph`, `leidenalg`, `infomap`, `cdlib`, `hypernetx`, `scikit-learn`, `scipy`, `pandas`, `numpy`, `matplotlib`.

## Authors

- Agostini  Tommaso   
- Calabrese Elisa      
- Secoli    Francesco 


University of Pisa — Social Network Analysis, 2024–2025