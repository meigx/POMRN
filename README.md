

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


