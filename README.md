# DensiScope

## Densest Subgraph Discovery on Large Collaboration Networks

**DensiScope** is an Analysis and Algorithms project for discovering and analyzing dense communities in large real-world collaboration networks.

The project implements and compares four densest-subgraph algorithms:

1. **Charikar's Greedy Peeling Algorithm**
2. **Greedy++**
3. **Goldberg's Exact Max-Flow Algorithm**
4. **Exact Triangle-Density Densest Subgraph**

The system evaluates solution quality, runtime scalability, memory behavior, exact-safe preprocessing, computational growth, and real-time algorithm visualization.

---

## Network Category and Applications

DensiScope uses **Collaboration Networks**, where vertices represent researchers or authors and edges represent collaborative relationships such as co-authorship.

Dense subgraphs in collaboration networks can reveal:

- Strongly connected research groups
- Highly collaborative scientific communities
- Interdisciplinary research clusters
- Core groups of frequently collaborating authors
- Potential expert teams
- Influential collaboration structures

Analyzing dense communities in collaboration networks can help researchers understand how scientific collaboration is organized, identify tightly connected research groups, discover important scientific communities, and study collaboration patterns inside very large networks.

---

## 1. Problem Definition

For the ordinary **Densest Subgraph Problem (DSP)**, DensiScope maximizes:

$$
\rho(S)=\frac{|E(S)|}{|V(S)|}
$$

where:

- $V(S)$ is the set of vertices in the selected subgraph.
- $E(S)$ is the set of edges inside the selected subgraph.

For triangle-density optimization, the objective is:

$$
\tau(S)=\frac{T(S)}{|V(S)|}
$$

where $T(S)$ is the number of triangles completely contained inside the selected subgraph.

This allows DensiScope to compare ordinary **edge concentration** with higher-order **triangle-based structural cohesion**.

---

## 2. Algorithms

### 2.1 Charikar's Greedy Peeling Algorithm

Charikar's algorithm repeatedly removes the minimum-degree vertex while tracking the densest intermediate subgraph encountered during the peeling process.

It is computationally efficient and acts as the scalable baseline of the project.

---

### 2.2 Greedy++

Greedy++ extends greedy peeling across multiple rounds.

During each round, the priority of a vertex depends on:

$$
\text{priority}(v)=\text{load}(v)+\text{current degree}(v)
$$

The load stores information accumulated from previous peeling rounds.

The experiments in DensiScope use **10 Greedy++ rounds**.

Greedy++ generally requires more runtime than Charikar, but it can return a denser solution when the ordinary greedy method misses the optimum.

---

### 2.3 Goldberg Exact Algorithm

Goldberg's algorithm solves the ordinary edge-density densest subgraph problem exactly using:

- Binary search over candidate density values.
- Max-flow / min-cut decision problems.

To improve scalability, DensiScope applies an **exact-safe core reduction** before constructing the flow network.

This reduction removes vertices that cannot belong to a solution better than the current lower bound while preserving the global optimum.

---

### 2.4 Exact Triangle-Density Algorithm

The triangle-density algorithm maximizes:

$$
\tau(S)=\frac{\text{number of triangles in }S}{|V(S)|}
$$

DensiScope combines several techniques:

- Triangle-core preprocessing.
- Triangle greedy lower bounds.
- Triangle-component decomposition.
- Structural optimality certificates.
- Max-flow / min-cut when required.

These techniques allow large triangle-density instances to be solved without unnecessarily constructing extremely large auxiliary flow networks.

---

## 3. Datasets

DensiScope evaluates five real-world collaboration networks.

| Dataset | Vertices | Edges |
|---|---:|---:|
| ca-netscience | 379 | 914 |
| ca-CSphd | 1,882 | 1,740 |
| ca-GrQc | 4,158 | 13,422 |
| ca-HepTh | 11,204 | 117,619 |
| ca-dblp-2012 | 317,080 | 1,049,866 |

The graphs are stored using memory-efficient **SciPy CSR sparse matrices**.

Large raw and processed datasets are intentionally not stored in this GitHub repository. They are downloaded and prepared automatically by the notebook when required.

---

## 4. Main Results

### ca-netscience

All three edge-density algorithms found:

- **9 vertices**
- **36 edges**
- Edge density = **4.000000**

The returned structure is a complete graph, $K_9$.

Exact Triangle Density found:

- **9 vertices**
- **84 triangles**
- Triangle density = **9.333333**

---

### ca-CSphd

Charikar:

- Edge density = **1.250000**

Greedy++:

- Edge density = **1.500000**

Goldberg Exact:

- Exact edge density = **1.500000**

This dataset demonstrates a case where **Greedy++ improves on Charikar and reaches the exact Goldberg optimum**.

---

### ca-GrQc

Charikar, Greedy++, and Goldberg Exact all found:

- **46 vertices**
- **1,030 edges**
- Edge density = **22.391304**

Exact Triangle Density found:

- Triangle density = **325.347826**

---

### ca-HepTh

All three edge-density algorithms found:

- **239 vertices**
- **28,441 edges**
- Edge density = **119.000000**

The returned graph is a complete $K_{239}$.

Exact Triangle Density found:

- **2,246,839 triangles**
- Triangle density = **9,401.000000**

The complete-core structure provides an exact certificate without requiring construction of a large triangle max-flow network.

---

### ca-dblp-2012

Original graph:

- **317,080 vertices**
- **1,049,866 edges**

Charikar:

- **114 vertices**
- **6,441 edges**
- Edge density = **56.500000**

Greedy++:

- **115 vertices**
- **6,505 edges**
- Edge density = **56.565217**

Goldberg Exact:

- **115 vertices**
- **6,505 edges**
- Exact edge density = **56.565217**

Exact Triangle Density:

- **114 vertices**
- **6,441 edges**
- Triangle density = **2109.333333**

This result demonstrates that **edge-density optimization and triangle-density optimization can prefer different communities in the same network**.

---

## 5. Exact-Safe Preprocessing

A major scalability improvement in DensiScope is the use of mathematically safe preprocessing before expensive exact optimization.

For Goldberg Exact on `ca-dblp-2012`, the graph is reduced from:

- **317,080 → 280 vertices**
- **1,049,866 → 13,609 edges**

This removes approximately **99.91% of the original vertices** before max-flow while preserving the exact global optimum.

The same strategy also substantially reduces the search space on the other datasets.

This is particularly important in Google Colab, where memory and runtime are limited.

---

## 6. Triangle Exact Certificates

DensiScope avoids constructing unnecessary large triangle-flow networks whenever the optimum can already be proven using a structural certificate.

### ca-HepTh

The safe triangle core is a complete $K_{239}$.

Therefore, its triangle-density optimum can be certified directly.

### ca-dblp-2012

For DBLP, the feasible triangle-density lower bound and a mathematically valid component upper bound both equal:

$$
2109.333333
$$

Because the lower and upper bounds match, the DBLP triangle-density optimum is certified exactly without constructing the full auxiliary max-flow network.

---

## 7. Approximation Quality

Goldberg Exact is used as the reference optimum for the ordinary edge-density Densest Subgraph Problem.

The experiments demonstrate that:

- **Charikar** is extremely fast and often reaches the exact optimum.
- **Greedy++** can improve the solution when Charikar misses the optimum.
- **Goldberg Exact** provides the reference optimal edge-density solution.
- Greedy++ requires more runtime because it performs multiple peeling rounds.

Two important examples are:

| Dataset | Charikar | Greedy++ | Goldberg Exact |
|---|---:|---:|---:|
| ca-CSphd | 1.250000 | 1.500000 | 1.500000 |
| ca-dblp-2012 | 56.500000 | 56.565217 | 56.565217 |

These experiments show the trade-off between **speed and solution quality**.

---

## 8. Computational Growth and Scalability

DensiScope experimentally studies how algorithm runtime changes as the network size increases.

The generated analysis includes:

- Runtime vs number of vertices.
- Runtime vs number of edges.
- Approximation quality vs Goldberg Exact.
- Additional peak RAM usage.
- Exact-safe preprocessing reduction.
- Comparison of approximate and exact algorithms.

Logarithmic axes are used for runtime-growth plots because the evaluated datasets range from hundreds to hundreds of thousands of vertices.

For exact methods, the reported end-to-end runtime includes prerequisite computations such as the Charikar lower-bound phase.

Generated plots are available in the [`figures/`](figures/) directory.

---

## 9. Visualization

DensiScope includes multiple visualization modes to demonstrate how the algorithms operate.

These include:

- Live Charikar peeling.
- Live Greedy++ peeling.
- Goldberg binary-search convergence.
- Exact Triangle binary-search convergence.
- MP4 algorithm animations.
- Dataset comparison videos.
- Interactive dataset and algorithm exploration.

Generated videos are available in the [`videos/`](videos/) directory.

For extremely dense returned subgraphs, only a subset of edges may be **displayed** to maintain visual readability.

This affects visualization only.

**All reported numerical metrics are always calculated using the complete, unsampled solution graph.**

---

## 10. Interactive DensiScope Explorer

The Google Colab notebook contains an interactive dashboard for exploring the experimental results.

The user can select:

- Dataset.
- Algorithm.
- Whether vertex IDs should be displayed.

The dashboard dynamically displays:

- Original graph size.
- Returned dense-subgraph size.
- Number of returned edges.
- Edge density.
- Triangle density when applicable.
- Approximation quality.
- End-to-end runtime.
- Solver mode.
- Visualization of the returned dense community.

This provides a real-time interactive interface for comparing the different algorithms and datasets.

---

## 11. Memory Efficiency

DensiScope is specifically designed to run within the resource limits of **Google Colab**.

Memory-management strategies include:

- SciPy CSR sparse matrices.
- Loading one large dataset at a time.
- Explicit garbage collection.
- Compact NumPy integer arrays.
- Indexed heaps without stale heap entries.
- Triangle counting without initially storing every triangle.
- Exact-safe graph reductions.
- Structural certificates before expensive max-flow.
- Visualization focused on returned dense communities for very large graphs.

These optimizations allow experiments on networks containing more than **300,000 vertices and one million edges**.

---

## 12. Repository Structure

```text
DensiScope/
│
├── DensiScope_FINAL.ipynb
│   Main Google Colab notebook containing the complete implementation,
│   experiments, benchmarking, and interactive visualization.
│
├── README.md
│   Project documentation and experimental summary.
│
├── requirements.txt
│   Python package dependencies.
│
├── .gitignore
│   Prevents large datasets and temporary files from being committed.
│
├── figures/
│   Experimental plots and computational-growth figures.
│
├── results/
│   Benchmark tables and generated solution files.
│
└── videos/
    Algorithm animations and visualization outputs.
```

The `data/` directory is generated locally during execution and is intentionally excluded from the repository to avoid storing large downloaded datasets on GitHub.

---

## 13. Running DensiScope

The recommended environment is **Google Colab**.

### Step 1 — Open the notebook

Open:

```text
DensiScope_FINAL.ipynb
```

in Google Colab.

### Step 2 — Install Dependencies

The required Python packages are listed in:

```text
requirements.txt
```

The notebook also installs or verifies the required dependencies automatically where necessary.

### Step 3 — Run the Notebook

In Colab, select:

```text
Runtime → Run all
```

The notebook will:

1. Prepare the required collaboration datasets.
2. Build memory-efficient sparse graph representations.
3. Run Charikar.
4. Run Greedy++.
5. Run Goldberg Exact.
6. Run Exact Triangle Density.
7. Generate benchmark tables.
8. Generate scalability and approximation plots.
9. Generate algorithm visualizations and videos.
10. Display the interactive DensiScope Explorer.

For the largest datasets, execution may require additional time because exact algorithms and triangle computations are significantly more expensive than approximation algorithms.

---

## 14. Generated Outputs

The final execution produces several types of output.

### Figures

Stored in:

```text
figures/
```

These include runtime growth, memory behavior, approximation quality, and preprocessing comparisons.

### Results

Stored in:

```text
results/
```

These contain benchmark measurements and algorithm solution information.

### Videos

Stored in:

```text
videos/
```

These demonstrate algorithm progress and dense-subgraph discovery visually.

---

## 15. Key Conclusions

The experiments demonstrate several important algorithmic trade-offs:

- **Charikar** provides an excellent speed/quality trade-off and frequently reaches the exact optimum.
- **Greedy++** can improve solution quality when ordinary greedy peeling misses the optimum.
- **Goldberg Exact** provides exact edge-density solutions but requires more computational resources.
- **Exact Triangle Density** identifies communities with strong higher-order cohesion that may differ from edge-density solutions.
- **Exact-safe preprocessing** can reduce extremely large networks to very small exact search spaces.
- **Structural certificates** can eliminate the need for expensive flow-network construction.
- Sparse graph representations and memory-aware implementation make large-scale experiments practical in Google Colab.

Overall, DensiScope demonstrates how approximation algorithms, exact algorithms, graph preprocessing, sparse data structures, and visualization can be combined to analyze dense structures in large real-world collaboration networks.
