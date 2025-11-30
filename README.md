

# 🚀 POMRN: Partial-Order-based Meta-structure Representation Network

Official repository for **“Is Partial Order More Effective in Extracting Meaningful Semantic Relationships in Heterogeneous Graphs?” (The Web Conference 2026 submission)**

POMRN is a principled heterogeneous graph learning framework grounded in **order theory**, constructing a meta-structure hierarchy under **type-preserving partial-order relations**, and performing **PSD-constrained spectral convolution** to preserve semantic consistency during high-order propagation.

This repository provides:  
- Full implementation of POMRN  
- Additional explanations addressing reviewers' comments   
---

# 📌 Additional explanations addressing reviewers' comments


### RESPONSE TO REVIEW 1  

Additional explanations addressing reviewer 1's comments:

  - **Response to R1-W3: Definition of “most informative” meta-structures**
  
    In Section 4.2, “the most informative meta-structures” refers to the subset $\mathcal{L}_{\mathrm{sel}}$ selected from the partial-order hierarchy $\mathbb{L}(\mathcal{G})$ based on two principled criteria grounded in our framework: 

    1. **Atomic coverage:**  
   We prioritize meta-structures that contain a richer combination of atomic
   patterns (e.g., PA/PT/PV in DBLP). These structures capture more fine-grained
   typed relations and thus exhibit stronger representational capacity.

    2. **Partial-order depth and semantic enrichment:**  
   Within the hierarchy, structures located at higher levels encode strictly
   richer semantics by type-preserving extensions. Therefore, a structure is
   considered more informative if it corresponds to a higher semantic level
   under $\preceq_{\mathcal{G}}$, while still maintaining type-consistent embeddings.

    A central goal of this work is to **identify and select such informative meta-structures for downstream tasks**. This motivates using partial-order relations to characterize semantic evolution and guide principled structure selection. 

    Empirically, Figure 6 verifies that structures with greater atomic diversity and deeper semantic extension yield consistently higher Macro-/Micro-F1 on DBLP and IMDB, demonstrating that our definition of “informative” aligns with both theoretical semantics and experimental outcomes.

  - **Response to R1-W6: Explanation of the chosen evaluation metrics**
    
    For **semi-supervised node classification**, we report **Macro-F1** and **Micro-F1**.  
    - **Macro-F1** equally weights each class and reflects per-class semantic discrimination ability.  
    $$
    \text{Macro-F1} = \frac{1}{C} \sum_{c=1}^{C}
    \frac{2  \text{Precision}_c  \text{Recall}_c} {\text{Precision}_c + \text{Recall}_c},
    $$
    where 
    $$\text{Precision}_c = \frac{TP_c}{TP_c + FP_c}, \quad \text{Recall}_c    = \frac{TP_c}{TP_c + FN_c}.$$
    
    - **Micro-F1** reflects overall performance and is robust to imbalancedm distributions.
     $$\text{Micro-F1} = \frac{2TP}{2TP + FP + FN},$$
    where $TP, FP, FN$ are computed globally across all classes.

    For **typed link prediction**, we report **ROC-AUC** and **MRR**, following the
    evaluation protocol of PSHGCN and R-HGNN.  
    - **ROC-AUC** measures the model’s ability to distinguish positive vs. negative typed edges under type-consistent sampling. The ROC-AUC measures the area under the ROC curve:
    $$\text{AUC} = \int_{0}^{1} TPR(FPR) \, d(FPR),$$
    where  $TPR = \frac{TP}{TP + FN}, FPR = \frac{FP}{FP + TN}.$


    - **MRR** captures ranking quality, which is essential for typed relational
      inference in multi-relational HINs.
      $$\text{MRR} = \frac{1}{|\mathcal{Q}|} \sum_{(u,v)\in\mathcal{Q}} \frac{1}{\text{rank}(u,v)},$$
      where $\text{rank}(u,v)$ is the position of the true positive edge among all candidate edges scored under relation $r$.
  
    Together, these metrics provide a complete view of classification accuracy, ranking capability, and robustness to class/edge imbalances.



### RESPONSE TO REVIEW 2  
Additional explanations addressing reviewer 2's comments:
  - **Response to R2-W3: Toy example for partial-order embedding**
  
    To provide more intuition, we include here a small running example consistent with the definitions in Sec. 3. and the hierarchy illustrated in Fig. 3. of the paper.

    Consider a toy HIN with three atomic meta-structures:
     **PA:** Author → Paper, **PT:** Paper → Term, **PV:** Paper → Venue. These are the atomic structures shown in Fig. 3.


    #### **Step 1: Atomic structures**
    Each atomic pattern corresponds to a minimal meta-structure: $s_{PA} = (A \!-\! P)$, $s_{PT} = (P \!-\! T)$, $s_{PV} = (P \!-\! V)$.

    #### **Step 2: Embedding into composite structures**
    A composite structure $s_{APT} = (A \!-\! P \!-\! T)$ *contains* both atomic
    patterns $s_{PA}$ and $s_{PT}$.  
    According to Definition 4 (partial order over meta-structures), we have:
    $$
    s_{PA} \preceq_{\mathcal{G}} s_{APT},\qquad
    s_{PT} \preceq_{\mathcal{G}} s_{APT}.
    $$

    Likewise, the composite pattern $s_{APV} = (A \!-\! P \!-\! V)$ satisfies:
    $$
    s_{PA} \preceq_{\mathcal{G}} s_{APV},\qquad
    s_{PV} \preceq_{\mathcal{G}} s_{APV}.
    $$

      #### **Step 3: Hierarchical paths as sequences of semantic refinement**
    A hierarchical route such as: $\text{PA} \;\rightarrow\; \text{APV} \;\rightarrow\; \text{APTV},$ represents *semantic refinement*:
    1. **PA** — author writes a paper  
    2. **APV** — adds venue semantics  
    3. **APTV** — adds topical semantics (term information)

    This mirrors the partial-order graph in Fig. 4 and the deeper routes analyzed
    in Fig. 6 of the paper (e.g., the orange route). Thus, a path in the hierarchy corresponds directly to how semantic meaning is gradually enriched through type-preserving extension.

  - **Response to R2-W4: Why PSD is desirable and comparison to non-PSD filters**
    #### **1. Stability and avoidance of oscillation**
    Unconstrained noncommutative polynomials 
    $$g(\{P_S\}) = \sum w_{S_1,\dots,S_k} P_{S_1}\cdots P_{S_k}$$
    may introduce **sign-flipping** or **oscillatory responses** during propagation because the operators \(P_S\) are sparse and non-symmetric.
    The PSD construction: $g^{\top} g \succeq 0$ removes these oscillations and ensures *stable, monotone accumulation* of semantic information.

    #### **2. Empirical comparison with non-PSD variant**
    The “w/o PSD” variant in our ablation study (Sec. 5.4) implements an unconstrained polynomial. Across DBLP and IMDB, removing the PSD constraint yields the largest performance drop among all ablations, confirming: reduced semantic consistency, poorer optimization stability, and degraded high-order composition ability.



    ### RESPONSE TO REVIEW 3  
    Additional explanations addressing reviewer 3's comments: 
  - **Response to R3-W2: Why PSD is desirable and comparison to non-PSD filters**
  
    We agree that qualitative evidence helps illustrate why partial-order–guided meta-structure selection captures “meaningful semantic relationships.” Although the main paper focuses on quantitative performance, several existing figures implicitly provide qualitative insight:
    1. **Figure 4 (Partial-order hierarchy):**  
   The hierarchy groups meta-structures according to their typed semantic extensions. Each upward step adds meaningful attributes (venue, topic), directly reflecting progressive semantic enrichment in the academic network schema.
    2. **Figure 6 (Route analysis):**  
   The orange route consistently yields the highest accuracy because it traverses structures that combine PT- and PV-related semantics, aligning with how paper topics and venues jointly influence author classification inDBLP. This shows that the semantic logic encoded by the hierarchy corresponds to real-world domain semantics.

    3. **Atomic-combination examples in Table 4:**  
   The PT+PV combination being most effective reflects that topic and venue information are jointly predictive for academic categories—showing that partial-order–based merging is semantically meaningful.
  
      Together, these observations provide qualitative support that the partial-order hierarchy organizes meta-structures in a way that aligns with real semantics, not arbitrary graph patterns. Additional qualitative examples will be included in the camera-ready version.

  - **Response to R3-W3: Growth of candidate meta-structures with schema size**
  
    In our framework, the number of candidate meta-structures does **not** grow exponentially with the number of node or edge types because enumeration is explicitly bounded by the **maximum allowed meta-structure size**. 
    Let:
    - $|\mathcal{A}|$ = number of node types  
    - $|\mathcal{R}|$ = number of edge types  
    - $L_{\max}$ = maximum structure length (2–4 edges in our experiments)
    Then the upper bound of the candidate space is:
    $$|\mathcal{S}_{\max}| = O(|\mathcal{R}|^{\,L_{\max}}),$$
    where $L_{\max}$ is a **small constant**, fixed in all experiments. Thus, the growth is **polynomial** rather than exponential in schema size.
    In practice (DBLP/ACM/Aminer/IMDB schemas): $|\mathcal{R}|$ ≤ 4, $L_{\max} = 3$ or $4$, resulting in only **6–10 meta-structures**.


    ### RESPONSE TO REVIEW 4  
    Additional explanations addressing reviewer 4's comments: 
  - **Response to R4-W1: Motivation & missing recent baselines**
   
    We appreciate the reviewer’s comment regarding the motivation and the omission of several recent meta-path–free models (PSHGCN, HINormer, LMSPS, HHGT). Our work focuses on a *different research question*: **identifying which meta-structures are most effective for downstream tasks**.  

    Meta-path–free architectures, including transformer-style HIN models, operate primarily on: token/attribute-level attention, relation-only propagation (e.g., PSHGCN), or sequence modeling of typed tokens (HINormer/LMSPS/HHGT), but **do not explicitly model structure-level semantic refinement**, nor do they analyze which higher-order typed structures are most predictive. Therefore, such methods do not align with our motivation, which is **structure semantics selection**, not relation-only or token-only modeling.

  - **Response to R4-W2: Manual atomic meta-structure selection & semantic logic justification**

    The reviewer raises an important point. Here we clarify thatPOMRN does not rely on manual selection of composite meta-structures.  
    Our method constructs the entire candidate space automatically from atomic typed edges using **partial-order constraints**.  
    - The **hierarchy is algorithmically determined**, not handcrafted.  
    - The **only choice** is the set of **atomic meta-structures**, which are inherent to the schema itself.  
    - Higher-order structures are then **derived automatically** by partial-order–guided merging.
  
    Our experiments merely evaluate **which automatically derived structures are most effective** for downstream performance.

    Regarding semantic logic: Partial-order relations are grounded in **typed-substructure containment**. A meta-structure is “more semantic” precisely because it *extends* another through type-preserving embeddings; this corresponds to deeper semantic refinement (illustrated in Table 4 and Figure 6).  




  - **Response to R4-W3: Dataset splits, deviations from prior reports**
    
    We thank the reviewer for pointing this out. Our dataset splits **strictly follow the split protocols provided by the dataset creators and used in the original benchmark papers**, ensuring full reproducibility:
    - DBLP / ACM / IMDB (ndoe classification): **20% training / 30% validation / 50% testing**, exactly as defined in the HAN and MAGNN datasets.
    - Amazon / PubMed (typed link prediction): **10% masked edges per relation for testing**, **10% for validation**, **remaining for training**.
  
    Differences from prior results in SeHGNN/PSHGCN arise from **unified preprocessing**, which we apply consistently to all baselines to ensure fair comparison.




  - **Response to R4-W4: Public code availability**
  
    We agree with the reviewer on the importance of reproducibility.

  - **Response to R4-W5: Minor issues**

    1. **Definition 1 clarity (node types)**  
   We agree that node types $\mathcal{A}$ should be explicitly stated in the heterogeneous graph formulation.  

    2. **PSD polynomial filter citation**  
   Section 4.2 will be updated to include prior references for PSD spectral filtering.

    3. **Figure 5(a) mixing ACM/DBLP results**  
   We appreciate the reviewer catching this. The figure is corrected and separated clearly.


    ### RESPONSE TO REVIEW 5  

  - **Response to R5-W1&W3: Scalability and experiments on larger HINs**
  We appreciate the reviewer’s concern regarding scalability.  
  Although the main paper reports results on moderate-size benchmarks, the proposed POMRN framework 
  is designed to scale to large HINs for the following reasons, grounded in the implementation described in Sec. 3. of the paper:
    1. **Propagation is linear in graph size.**  
    Each operator $P_S$ is a sparse adjacency-like matrix. PSD convolution uses low-degree monomials (≤3), and with monomial caching the forward pass is $O(|E|d + |V|d)$, matching the complexity of existing spectral models such as PSHGCN.
  
    2. **Operator family size is bounded.**  
   The number of meta-structures is **not tied to graph size**, but to a small 
   fixed upper bound $L_{\max}$ (2–4 edges). This keeps $|\mathcal{L}_{\mathrm{sel}}|$ extremely small (6–10 structures in practice).

    3. **Construction of operators is efficient.**  
   Each $P_S$ is computed by a single traversal over typed adjacency matrices, 
   requiring:$O(|L_{\mathrm{sel}}| \cdot m)$，$|L_{\mathrm{sel}}|\text{ fixed}$, where $m$ is the number of edges.

    To further validate scalability, we are adding results on **ogbn-mag**, a large-scale heterogeneous benchmark.
  
  - **Response to R5-W2&W4: Computational cost of partial-order hierarchy construction & comparison with baselines**

    We thank the reviewer for highlighting the absence of explicit computational cost analysis.

       **1. Cost of partial-order hierarchy construction**
      
      As described in Sec. 3, POMRN enumerates meta-structures only up to a small maximum size $L_{\max}=3$ or $4$.  Thus the total candidate space is:
      $$|\mathcal{S}_{\max}| = O(|\mathcal{R}|^{\,L_{\max}}),$$
    where $|\mathcal{R}| \le 4$ for all datasets in the paper. Therefore: $|\mathcal{S}_{\max}| \le 10$.

    Partial-order determination among all structures requires: $O(|\mathcal{S}_{\max}|^2) \le O(100)$.
    This cost is **negligible**, and is a one-time preprocessing step.

      **2. Computational cost compared with baselines**
  
    Relation-based GNNs build per-relation operators. POMRN builds per-structure operators, but the number of structures is similarly small and fixed.
  
    PSHGCN computes adjacency monomials up to fixed degree $K$. POMRN performs the same computational pattern, but over $\{P_S\}$ instead of $\{A_r\}$. Hence, **the cost matches PSHGCN up to a small constant factor**.

    Transformer-based HIN models (HINormer, LMSPS) scale with $O(|V|d^2)$ attention complexity. POMRN avoids attention and retains **linear complexity**.

    Overall, the computational overhead of partial-order construction is tiny, and the runtime/memory cost of POMRN is comparable to PSHGCN and significantly lighter than transformer-based heterogeneous models. 



