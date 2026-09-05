# DensiScope

## Densest Subgraph Discovery on Large Collaboration Networks

**DensiScope** is an Analysis and Algorithms project for discovering and analyzing dense communities in large real-world collaboration networks.

The project implements and compares four densest-subgraph algorithms:

1. **Charikar's Greedy Peeling Algorithm**
2. **Greedy++**
3. **Goldberg's Exact Max-Flow Algorithm**
4. **Exact Triangle-Density Densest Subgraph**

The system evaluates solution quality, runtime scalability, memory behavior, exact-safe preprocessing, and real-time algorithm visualization.

---

## 1. Problem Definition

For the ordinary Densest Subgraph Problem, DensiScope maximizes:

\[
\rho(S)=\frac{|E(S)|}{|V(S)|}
\]

where:

- \(V(S)\) is the set of vertices in the selected subgraph
- \(E(S)\) is the set of edges inside the selected subgraph

For triangle-density optimization, the objective is:

\[
\tau(S)=\frac{T(S)}{|V(S)|}
\]

where \(T(S)\) is the number of triangles completely contained inside the selected subgraph.

This allows the project to compare ordinary edge concentration with higher-order structural cohesion.

---

## 2. Algorithms

### Charikar

Charikar repeatedly removes the minimum-degree vertex while tracking the densest intermediate subgraph.

It provides a fast approximation and acts as the project's scalable baseline.

---

### Greedy++

Greedy++ extends greedy peeling across multiple rounds.

The priority of a vertex depends on:

\[
\text{load}(v)+\text{current degree}(v)
\]

The load stores information from previous peeling rounds.

The experiments use **10 Greedy++ rounds**.

---

### Goldberg Exact

Goldberg solves the ordinary edge-density problem exactly using:

- Binary search over density
- Max-flow / min-cut decision problems

DensiScope improves its scalability using an **exact-safe core reduction** before constructing the flow network.

---

### Exact Triangle Density

This algorithm maximizes:

\[
\frac{\text{number of triangles}}{\text{number of vertices}}
\]

DensiScope combines:

- Triangle-core preprocessing
- Triangle greedy lower bounds
- Triangle-component decomposition
- Structural optimality certificates
- Max-flow / min-cut when required

This allows large triangle-density instances to be solved without unnecessarily creating enormous flow networks.

---

## 3. Datasets

Five real collaboration networks are evaluated.

| Dataset | Vertices | Edges |
|---|---:|---:|
| ca-netscience | 379 | 914 |
| ca-CSphd | 1,882 | 1,740 |
| ca-GrQc | 4,158 | 13,422 |
| ca-HepTh | 11,204 | 117,619 |
| ca-dblp-2012 | 317,080 | 1,049,866 |

Graphs are stored using memory-efficient **SciPy CSR sparse matrices**.

---

## 4. Main Results

### ca-netscience

All edge-density algorithms found:

- 9 vertices
- 36 edges
- Edge density = **4.0**

The returned structure is a complete \(K_9\).

Exact Triangle Density found:

- 9 vertices
- 84 triangles
- Triangle density = **9.333333**

---

### ca-CSphd

Charikar:

- Edge density = **1.25**

Greedy++:

- Edge density = **1.50**

Goldberg Exact:

- Exact edge density = **1.50**

This demonstrates a case where Greedy++ improves on Charikar and reaches the exact optimum.

---

### ca-GrQc

Charikar, Greedy++, and Goldberg all found:

- 46 vertices
- 1,030 edges
- Edge density = **22.391304**

Exact Triangle Density found triangle density:

- **325.347826**

---

### ca-HepTh

All edge-density algorithms found:

- 239 vertices
- 28,441 edges
- Edge density = **119**

The returned graph is a complete \(K_{239}\).

Exact Triangle Density found:

- 2,246,839 triangles
- Triangle density = **9,401**

The complete-core structure provides an exact certificate without requiring the large triangle max-flow construction.

---

### ca-dblp-2012

Original graph:

- 317,080 vertices
- 1,049,866 edges

Charikar:

- 114 vertices
- 6,441 edges
- Edge density = **56.500000**

Greedy++:

- 115 vertices
- 6,505 edges
- Edge density = **56.565217**

Goldberg Exact:

- 115 vertices
- 6,505 edges
- Exact edge density = **56.565217**

Exact Triangle Density:

- 114 vertices
- 6,441 edges
- Triangle density = **2109.333333**

This shows that edge-density and triangle-density optimization can prefer different communities.

---

## 5. Exact-Safe Preprocessing

A major scalability improvement in DensiScope is the use of mathematically safe preprocessing.

For Goldberg Exact, DBLP is reduced from:

- **317,080 → 280 vertices**
- **1,049,866 → 13,609 edges**

This removes approximately **99.91% of the vertices** before max-flow while preserving the global optimum.

The same strategy substantially reduces the other datasets as well.

---

## 6. Triangle Exact Certificates

DensiScope avoids constructing unnecessary large triangle-flow networks whenever the optimum can already be certified.

### ca-HepTh

The safe triangle core is a complete \(K_{239}\).

Therefore the optimum is directly certified.

### ca-dblp-2012

A feasible triangle-density lower bound and a mathematically valid component upper bound both equal:

\[
2109.333333
\]

Therefore the DBLP triangle optimum is exactly certified without building the full auxiliary max-flow network.

---

## 7. Approximation Quality

Goldberg Exact is used as the reference optimum for ordinary edge-density DSP.

The experiments show:

- Charikar is extremely fast and often reaches the exact optimum.
- Greedy++ can improve Charikar when Charikar misses the optimum.
- Greedy++ requires substantially more runtime because it performs repeated peeling rounds.

Examples:

- `ca-CSphd`: Charikar = 1.25, Greedy++ = Exact = 1.50
- `ca-dblp-2012`: Charikar = 56.50, Greedy++ = Exact = 56.565217

---

## 8. Scalability Analysis

DensiScope produces experimental growth plots for:

- Runtime vs number of vertices
- Runtime vs number of edges
- Approximation quality vs Goldberg Exact
- Additional peak RAM usage
- Exact-safe preprocessing reduction

Logarithmic axes are used for runtime growth because the datasets range from hundreds to hundreds of thousands of vertices.

Exact-method end-to-end runtime includes the prerequisite Charikar lower-bound computation.

---

## 9. Visualization

DensiScope includes several visualization modes:

- Live Charikar peeling
- Live Greedy++ peeling
- Goldberg binary-search convergence
- Exact Triangle binary-search convergence
- MP4 algorithm animations
- Dataset comparison videos
- Interactive dataset/algorithm dashboard

For extremely dense returned subgraphs, only a subset of edges may be **displayed** for readability.

All reported metrics always use the complete, unsampled solution graph.

---

## 10. Interactive DensiScope Explorer

The Colab notebook contains an interactive dashboard that allows the user to select:

- Dataset
- Algorithm
- Whether vertex IDs are displayed

The dashboard dynamically displays:

- Original graph size
- Returned dense-subgraph size
- Edge density
- Triangle density when applicable
- Approximation quality
- End-to-end runtime
- Solver mode

---

## 11. Memory Efficiency

The project is designed for Google Colab.

Key memory-management strategies include:

- SciPy CSR sparse matrices
- One dataset loaded at a time
- Explicit garbage collection
- Compact NumPy integer arrays
- Indexed heaps without stale entries
- Triangle counting without storing triangles initially
- Exact-safe graph reductions
- Structural certificates before expensive max-flow
- Visualization only on returned dense communities for huge graphs

---

## 12. Project Structure

```text
DensiScope/
├── data/
│   ├── raw/
│   └── processed/
│
├── figures/
│
├── videos/
│
├── results/
│   ├── tables/
│   └── solutions/
│
├── DensiScope_FINAL.ipynb
├── README.md
└── requirements.txt
