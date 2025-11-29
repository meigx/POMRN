

# 🚀 POMRN: Partial-Order-based Meta-structure Representation Network

Official repository for **“Is Partial Order More Effective in Extracting Meaningful Semantic Relationships in Heterogeneous Graphs?” (WWW 2026 submission)**

POMRN is a principled heterogeneous graph learning framework grounded in **order theory**, constructing a meta-structure hierarchy under **type-preserving partial-order relations**, and performing **PSD-constrained spectral convolution** to preserve semantic consistency during high-order propagation.

This repository provides:  
- Full implementation of POMRN  
- Reproducible experiments (node classification & typed link prediction)  
- Meta-structure enumeration and partial-order construction code  
- Additional explanations addressing reviewers' comments for transparency  

---

# 📌 Overview

Heterogeneous information networks contain rich semantics encoded by typed nodes and relations. POMRN introduces:

### ✔ Partial-order relations among meta-structures  
Formalized via **type-preserving embeddings**, enabling hierarchical organization of structures.

### ✔ Structure-induced propagation operators  
Mapping meta-structures to sparse propagation matrices.

### ✔ PSD polynomial filtering  
Ensuring:
- numerical stability  
- semantics-preserving propagation  
- prevention of oscillatory or unstable behavior  

### ✔ Linear scalability  
Time and memory grow **linearly** with dataset size.

---

# 🧪 Datasets & Experimental Protocol

Evaluated on:

- **Node Classification**: DBLP, ACM, Aminer, IMDB  
- **Typed Link Prediction**: Amazon, PubMed  
- **Large-scale** benchmark: **ogbn-mag**

### Split Ratios  
- Node classification: **20% train / 30% val / 50% test**  
- Link prediction: **10% test mask per relation**, **10% validation**  
- All baselines use **identical splits** for fairness.

---

# 📝 Rebuttal-Based Clarifications  
Below are transparent responses to reviewers’ specific questions.

---

## **1. Readability & Conceptual Clarity (R1, R2)**  
We appreciate the request for stronger intuition. Our formal definitions—heterogeneous graphs, atomic/meta-structures, type-preserving embeddings, partial-order relations—are in Sec. 3.1–3.2, supported by illustrative figures (Fig. 5–6) and examples such as **A–P → A–P–T/V** derived from Def. 4.  
**Fig. 1 has been revised** to better explain semantic loss in random-walk methods and will appear in the updated version.

---

## **2. Scalability, Storage & Computational Complexity (R1, R2, R3, R5)**

### Hierarchy Construction  
- Bounded by **max meta-structure size** (2–4 typed edges)  
- ≤10 structures after pruning  
- One-time cost:  
  ```
  O(|S_max|^2)
  ```

### Propagation  
- Each \(P_S\): sparse adjacency-like matrix  
- Construction:  
  ```
  O(|L_sel| * m)
  ```
- PSD polynomial (K ≤ 3) with monomial caching:  
  ```
  O(|E| d + |V| d)
  ```
- Memory:  
  ```
  O(|E| + |V| d)
  ```

### Scaling with More Types  
Enumeration bounded by structure size ⇒ **no exponential growth**.

---

## **3. Motivation & Relation to Recent Baselines (R4)**  
Transformer-based models (HINormer, LMSPS, HHGT) rely on token/attribute-level attention, not typed-structure containment.  
POMRN introduces a **distinct semantic axis** via partial-order refinement—orthogonal to transformer architectures.  
Thus, the comparable baselines are meta-path, meta-graph, and spectral/relational-subgraph models.

---

## **4. Partial-Order Enforcement & PSD Semantics (R2, R3)**  
The PSD form \(g^\top g\):

- prevents sign-flipping  
- avoids oscillation and instability  
- mitigates over-smoothing  
- enforces monotone semantic aggregation  

Our non-PSD ablation (“w/o PSD”) shows consistent degradation.

Partial-order constraints persist because \(P_S\) encodes fixed typed-substructure relations; coefficient updates cannot violate these inclusions. Empirical evidence supporting semantic consistency appears in Sec. 4.4.

---

## **5. Experimental Clarity & Reproducibility (R1, R2, R4, R5)**  

### Large-Scale Benchmark  
POMRN runs efficiently on **ogbn-mag**; results are provided under `results/ogbn-mag/`.

### LLM Baselines  
LLM-based methods require textual node descriptions; the datasets here do not contain text.  
Thus LLMs are **orthogonal** rather than directly comparable.  
POMRN could complement LLM architectures in future work.

### Dataset Splitting & Deviations  
Differences from some prior papers arise from **unified preprocessing** and **identical splits for all baselines**, ensuring fair comparison.  
All edge masking, split ratios, and feature-handling details are available in `scripts/splits/`.

---

# 📦 Repository Structure

```
POMRN/
│── models/
│── operators/
│── data/
│── scripts/
│   ├── build_partial_order.py
│   ├── preprocess.py
│   └── run_experiments.sh
│── results/
│── README.md
│── requirements.txt
```

---

# ▶️ Running the Code

```bash
pip install -r requirements.txt
python scripts/run_experiments.py --dataset dblp
```

---

# 🙌 Citation  
(To be added after acceptance.)