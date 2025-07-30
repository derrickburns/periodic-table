# Elements of System Design

System design is often taught through  solutions specific to particular domains, such as databases, operating systems, or computer architecture, each with its own methods and vocabulary. While this diversity is a strength, it can obscure cross-cutting principles that recur across domains. This paper proposes a preliminary taxonomy of system design principles distilled from several domains in computer systems. The goal is a shared, concise vocabulary that helps students, researchers, and practitioners reason about structure and trade-offs, compare designs across domains, and communicate choices more clearly.

## 1. INTRODUCTION

One of the rewards of working in computer systems is the field’s sheer diversity, spanning operating systems, databases, computer architecture, distributed systems, programming languages, networking, and more, each with a rich history. For newcomers, it can be challenging to spot connections across different domains due to the diversity of traditions and vocabularies: the same design principle may appear in different guises across domains.

For example, consider the classic paper on database isolation levels by Jim Gray et al. [17]. It offers a careful account of concurrency-control mechanisms and the trade-offs between correctness and performance. Yet without prior exposure to similar issues in operating systems or computer architecture, the ideas can appear narrowly “about databases.” In reality, the same design principle, "relaxation of consistency," reappears across systems in different guises, from weakly ordered memory hierarchies to eventual-consistency protocols in distributed systems. When each community uses its own terms and exemplars, newcomers may find it difficult to recognize the underlying design principles. This fragmentation increases cognitive overhead, as the same trade-off must be relearned in each context.

This is a broader pattern: systems research is rich in practical insight but lighter on shared conceptual scaffolding. Across domains, similar challenges recur, managing concurrency, ensuring consistency, and adapting to change, while the framing and vocabulary often differ. As a result, deep connections between seemingly disparate domains can remain relatively obscure.

This article is a small step toward bridging those gaps. Borrowing Mendeleev’s metaphor, we present a "periodic table" of recurring system design principles. The goal is not a rigid taxonomy but a working vocabulary: a concise way to annotate papers, lectures, and design documents with the fundamental principles they employ. The aim is to surface structure that already exists in computer systems, so that students can form a more coherent mental map, researchers can situate contributions with precision, and practitioners can discuss design choices across domains with greater clarity.

## 2. METHODOLOGY

We identified principles by going over 100+ influential papers across operating systems, computer architecture, databases, networking, programming languages, security, and other domains in computer systems. These papers were chosen for historical significance and ongoing relevance, such as classic papers on concurrency control [17] and consensus [25], and more recent work on using machine learning inside systems [22] and designing systems for the cloud [6]. 

For each paper we asked: what is the underlying high-level design principle? Across domains, independent systems often converged not on mechanisms but on shared design principles: for example, relaxing consistency to improve performance or lifting abstractions to enhance usability. 

To qualify as a system design principle, it must satisfy two conditions:

1. **Abstract** – The principle must be independent of specific technologies or implementations.  
2. **Generality** – The principle must show up across different domains (e.g., database systems, operating systems, programming languages).

This analysis does not aim to catalogue every principle, but to surface many of those with lasting, general-purpose value.

## 3. THE DESIGN PRINCIPLE TABLE

We have curated a structured set of 40+ general-purpose design principles distilled from the systems literature. As shown in Table 1, they are organised into thematic groups that mirror familiar axes of system design.

Each principle is tagged with a short symbol (e.g., `Co` for composability, `Op` for optimistic design) for quick reference. We emphasise **design intent** rather than prescribing mechanisms: instead of “use this locking protocol” or “optimise this query plan,” the principles state aims such as “preserve correctness under concurrency” or “prioritise the common case,” leaving concrete realisations to specific domains.

## Table of Contents

- [🟪 Group 1: Structure](#-group-1-structure): *How to carve and connect parts with clear boundaries and extension points.*
- [🟧 Group 2: Efficiency](#-group-2-efficiency): *Do less work, or do it cheaper, by focusing effort where it pays.*
- [🟨 Group 3: Semantics](#-group-3-semantics): *Specify behavior and interfaces precisely.*
- [⬛ Group 4: Distribution](#-group-4-distribution): *Coordinate work and data across distributed architectures.*
- [🟩 Group 5: Planning](#-group-5-planning): *Select plans automatically from goals, costs, and constraints.*
- [🟦 Group 6: Operability](#-group-6-operability): *Observe, adapt, and evolve running systems with minimal disruption.*
- [🟥 Group 7: Reliability](#-group-7-reliability): *Stay correct under faults, concurrency, and partial failure.*
- [🟫 Group 8: Security](#-group-8-security): *Bound authority and enforce isolation to preserve safety and integrity.*

**Legend:** `Code` = unique short symbol, `Name` = principle, `Intent` = short description.

<img width="408" height="423" alt="table" src="https://github.com/user-attachments/assets/f35f47fd-52b0-4ea9-9c85-5fd43d0030f6" />


🟪 Group 1: Structure

🟪 **Si – Simplicity**

Choose the simplest system design that meets current needs; resist complexity, such as additional layers, services, or generality added "just in case," until evidence shows benefit. 

**Example:**  The UNIX philosophy championed a small kernel with simple abstractions \[41], and the RISC architecture argued for a simpler instruction set to improve performance \[59].

🟪 **Mo – Modularity**

Partition the system into cohesive units with minimal interfaces. Each unit can be reasoned about, replaced, or evolved independently. 

**Example:**  The OSI model decomposes communication into layers \[48]. Parnas advocated information hiding for decomposing systems \[49].

🟪 **Co – Composability**

Design components that can be safely and flexibly recombined using explicit contracts. 

**Example:**  Unix programs like grep and sort compose via plain text streams \[41].

🟪 **Ex – Extensibility**

Allow user-defined extensions (e.g., plugins) without modifying the core system. 

**Example:**  Unix allows adding tools without kernel changes \[41].

🟪 **Pm – Policy/Mechanism Separation**

Separate policy (what) from mechanism (how). 

**Example:**  Hydra OS separated mechanisms in the kernel from policies in user modules \[32].

🟪 **Gr – Generalized Design**

Create a general core with explicit variation points. 

**Example:**  C++ STL uses templates; Postgres supports user-defined types \[45, 46].

🟪 **Ui – Uniform Interface**

Expose a consistent interface regardless of internal implementation. 

**Example:**  Unix and Plan 9 treat all resources as files \[41, 58].

🟧 Group 2: Efficiency

🟧 **Sc – Scalability**

Design the system to handle growth in data, traffic, or nodes with near-linear cost or latency. 

**Example:**  MapReduce divides work into parallel tasks with minimal coordination \[10]. Dynamo partitions data and replicates via peer-to-peer protocols \[12].

🟧 **Rc – Reuse of ComputationAvoid redundant work by caching or incrementally updating intermediate results. 

**Example:**  B+trees support reuse by preserving sorted order and enabling efficient searches \[2].

🟧 **Wv – Work Avoidance**

Skip computation that would not alter externally observable results. 

**Example:**  Lazy evaluation defers work until needed, avoiding unnecessary computation \[19].

🟧 **Cc – Common-Case Specialization**

Optimize for frequently executed paths or data cases. 

**Example:**  RISC architecture emphasized common instructions \[59]. JIT compilers cache fast-path methods \[5].


🟧 **Bo – Bottleneck-Oriented Optimization**

Identify and focus optimization efforts on the tightest system constraint. 

**Example:**  Replicating requests can reduce tail latencies caused by slow stragglers \[9].

🟧 **Ha – Hardware-Aware Design**

Tailor algorithms to match underlying hardware characteristics. 

**Example:**  BLAS libraries use hardware-tuned kernels for performance \[31].

🟧 **Op – Optimistic Design**

Proceed assuming common-case success; handle failure as an exception. 

**Example:**  Optimistic concurrency control executes transactions lock-free and rolls back on conflict \[24].

🟧 **La – Learned Approximation**

Use models trained on data to replace handcrafted algorithms. 

**Example:**  Perceptron branch predictors outperform static heuristics by learning from behavior \[22].


#🟨 Group 3: Semantics

🟨 **Al – Abstraction Lifting**

Wrap low-level operations behind a high-level interface or DSL that expresses intent. 

**Example:**  Codd’s relational model enables SQL to specify what to retrieve; the DBMS decides how \[44].

🟨 **Lu – Language Homogeneity**

Use a consistent intermediate representation to align semantics and simplify tooling. 

**Example:**  LLVM uses a typed, SSA-based IR shared across many languages \[30].

🟨 **Se – Semantically Explicit Interfaces**

Define observable semantics precisely (visibility, durability, ordering). 

**Example:**  SQL isolation levels and RPC semantics define guarantees \[3, 4].

🟨 **Fs – Formal Specification**

Use mathematical/logical models to describe behavior. 

**Example:**  TLA+ models concurrency and is used for formal verification \[27].

🟨 **Ig – Invariant-Guided Transformation**

Use formally defined invariants to safely optimize or refactor. 

**Example:**  SSA form in compilers and query rewrite rules in DBMSs \[8, 44].

⬛ Group 4: Distribution

⬛ **Lt – Location Transparency**

Hide the physical location of resources behind abstract handles. 

**Example:**  RPC systems allow invoking remote services as if local \[4].

⬛ **Dc – Decentralized Control**

Avoid central points of coordination to improve availability and scalability. 

**Example:**  Dynamo uses consistent hashing and gossip protocols to eliminate central coordination \[12].

⬛ **Fp – Function Placement**

Execute functionality where context is available to reduce overhead. 

**Example:**  End-to-end argument advocates placing reliability checks at communication endpoints [42].

⬛ **Lo – Locality of Reference**

Keep related data and computation close in space/time to improve performance. 

**Example:**  The working-set model formalizes temporal locality for memory \[11].

⬛ **Lc – Logical Centralization**

Use a centralized logic layer over a physically distributed system. 

**Example:**  SDN centralizes control for global network policy optimization \[53].

⬛ **Fa – Fate Sharing**

Design so that dependent components fail together. 

**Example:**  Internet design keeps state at endpoints; routers are stateless and can fail independently \[50].


🟩 Group 5: Planning

🟩 **Ep – Equivalence-Based Planning**

Use rewrite rules to explore logically equivalent alternatives and defer final decision to later cost analysis. 

**Example:**  Starburst query optimizer applies relational algebra equivalences before choosing plans \[39].

🟩 **Cm – Cost-Based Planning**

Use models of resource cost (e.g., I/O, CPU) to choose between implementation strategies. 

**Example:**  System R's optimizer selects query plans by estimating cost of each \[44].

🟩 **Cp – Constraint-Based Planning**

Encode constraints (hard or soft) and use solvers to generate valid or optimal configurations. 

**Example:**  Quincy formulates cluster scheduling as a constrained optimization problem \[21].

🟩 **Gd – Goal-Directed Planning**

From a declarative goal, synthesize an execution plan automatically. 

**Example:**  Cascades optimizer transforms SQL queries into efficient plans through rule-based search \[14].

🟩 **Bb – Black-Box Tuning**

Search parameter/configuration space by measuring candidate performance rather than modeling it. 

**Example:**  ATLAS tunes BLAS kernels by empirically testing candidate configurations \[47].

🟩 **Ah – Advisory Hinting**

Support non-binding user hints to guide system behavior. 

**Example:**  Lampson advocated optional hints that help performance but don’t affect correctness \[29].

🟦 Group 6: Operability

🟦 **Ad – Adaptive Processing**

Monitor runtime conditions and adjust system behavior accordingly. 

**Example:**  Eddies adapt query execution strategies in response to changing data characteristics \[1].

🟦 **Ec – Elasticity**

Dynamically allocate or reclaim resources based on demand. 

**Example:**  Hosting center systems scale resources based on utility functions and load \[6].

🟦 **Wa – Workload-Aware Optimization**

Adapt data layout, algorithms, or scheduling to the observed workload. 

**Example:**  Database cracking incrementally reorganizes data based on queries \[20].

🟦 **Au – Automation and Autonomy**

Automate system tuning, recovery, or adaptation without human intervention. 

**Example:**  AutoAdmin suggests indexes based on observed workloads \[7].

🟦 **Ho – Human Observability**

Expose internal state for debugging and performance analysis. 

**Example:**  Paxson’s end-to-end Internet measurements aid in fault diagnosis \[37].

🟦 **Ev – Evolvability**

Support ongoing system evolution with minimal disruption. 

**Example:**  Parnas’s modular design enables safe changes without breaking clients \[36, 49].

🟦 **Pi – Performance Isolation**

Ensure one workload does not degrade others in shared environments. 

**Example:**  VMware and container systems enforce resource limits across tenants \[55].


🟥 Group 7: Reliability

🟥 **Ft – Fault Tolerance**

Continue functioning (possibly in degraded mode) despite component failures. 

**Example:**  Tandem systems used replicated hardware; GFS recovers from faults through replication \[15, 51].

🟥 **Is – Isolation for Correctness**

Prevent unintended interference between concurrent components. 

**Example:**  Two-phase locking ensures ACID-compliant isolation in databases \[16].

🟥 **At – Atomic Execution**

Execute a set of operations indivisibly: all or none take effect. 

**Example:**  Transactional memory ensures that a memory block either commits or aborts atomically \[18].

🟥 **Cr – Consistency Relaxation**

Relax strict consistency guarantees to improve availability or performance. 

**Example:**  Dynamo offers eventual consistency; Spanner relaxes serializability using bounded time uncertainty \[12, 59].

🟥 **De – Deterministic Execution**

Ensure the system produces the same observable output for the same input. 

**Example:**  Deterministic replay systems like ReVirt and ODR support reproducible debugging \[52, 54].

🟥 **Cd – Crash-Only Design**

Design systems to safely crash and recover, avoiding complex error-handling logic. 

**Example:**  GFS and similar systems reboot into a clean state rather than attempting in-place repair \[13].

🟫 Group 8: Security

🟫 **Sy – Security via Isolation**

Use strong isolation boundaries to contain faults or malicious activity. 

**Example:**  Virtual machine monitors isolate guests and intercept privileged operations \[40].

🟫 **Ac – Access Control and Auditing**

Define and enforce permissions; log accesses for accountability. 

**Example:**  Lampson's taxonomy underpins ACLs, capabilities, and audit trails \[28].

🟫 **Lp – Least Privilege**

Grant only the minimum required authority to users or components. 

**Example:**  Seeley's postmortem on the 1988 worm highlighted excess privilege as a vector \[35].

🟫 **Tq – Trust via Quorum**

Require agreement among independent nodes to tolerate faults or attacks. 

**Example:**  Paxos relies on majority consensus to maintain correctness under failures \[26].

🟫 **Cf – Conservative Defaults**

Use restrictive defaults to minimize accidental exposure or risk. 

**Example:**  Default-deny access policies require explicit permission to grant access \[43].

🟫 **Sa – Safety by Construction**

Design code or APIs to eliminate classes of bugs by design. 

**Example:**  Rust’s ownership model prevents memory errors; seL4 is formally verified \[34, 56].

## 4. CASE STUDY

To illustrate how multiple design principles intersect in practice, consider the mapping from logical to physical operator plans in a relational database system.

- The database system translates declarative intent into executable steps (**Policy/Mechanism Separation**).
- SQL expresses the "what" (**Abstraction Lifting**) with precise semantics (**Semantically Explicit Interfaces**).
- The optimizer first rewrites the query using algebraic equivalences (**Equivalence-Based Planning**).
- It then chooses concrete physical operators using a cost model (**Cost-based Planning**).
- Physical operators are often optimized for underlying hardware features (**Hardware-Aware Design**).
- Predicate-pushdown illustrates **Work Avoidance**, while indexes enable **Reuse of Computation**.
- **Advisory Hints** can guide the optimizer, and newer database systems add runtime re-optimization (**Adaptive Processing**), learned models (**Learned Approximation**), and sampling (**Probabilistic Design**).

Thus, logical-to-physical operator mapping in database systems exemplifies how several design principles come together to efficiently process declarative SQL queries.

## 5. LIMITATIONS

Any attempt to organise a field as broad as computer systems involves trade-offs. This table is not a checklist or a universal theory; it is a shared vocabulary that highlights recurring principles and encourages structural reflection. That said, there are several limitations:

- **Orthogonality**: Principles can overlap, reinforce, or partially conflict; design is about balancing such tensions.
- **Subjectivity and granularity**: Deriving and mapping principles involves judgement; boundaries are fuzzy and different readers may tag the same system differently or interpret the same principle differently.
- **Not a formal taxonomy**: This is not a complete or minimal set of design principles. There is no attempt to derive the principles from a minimal core.

Ultimately, this table is a means to help students see recurring design principles more clearly, to assist system designers in communicating tradeoffs more precisely, and to help researchers recognize where their ideas fit into the broader landscape of system design.

## 6. CONCLUSION

System design spans diverse domains and vocabularies, which can make shared discussion harder. We inherit mechanisms, study tradeoffs, and build intuitions, yet concise terms for the underlying ideas are not always available. The “periodic table” of design principles offered here aims to provide a modest common language, naming recurring ideas so they are easier to teach, compare, and build upon.

## REFERENCES

[1] Ron Avnur and Joseph M. Hellerstein. Eddies: Continuously Adaptive Query Processing. SIGMOD, 2000.

[2] Rudolf Bayer and Edward McCreight. Organization and Maintenance of Large Ordered Indexes. Acta Informatica, 1972.

[3] Hal Berenson et al. A Critique of ANSI SQL Isolation Levels. SIGMOD, 1995.[4] Andrew D. Birrell and Bruce J. Nelson. Implementing Remote Procedure Calls. ACM TOCS, 1984.

[5] Craig Chambers and David Ungar. Customization: Optimizing Compiler Technology for SELF. PLDI, 1989.

[6] Jeffrey S. Chase et al. Managing Energy and Server Resources in Hosting Centers. SOSP, 2001.

[7] Surajit Chaudhuri and Vivek Narasayya. An Efficient, Cost-Driven Index Selection Tool for Microsoft SQL Server. VLDB, 1997.

[8] Ron Cytron et al. Efficiently Computing Static Single Assignment Form and the Control Dependence Graph. ACM TOPLAS, 1991.

[9] Jeff Dean and Luiz André Barroso. The Tail at Scale. CACM, 2013.

[10] Jeffrey Dean and Sanjay Ghemawat. MapReduce. OSDI, 2004.

[11] Peter J. Denning. The Working Set Model for Program Behavior. CACM, 1968.

[12] Giuseppe DeCandia et al. Dynamo. SOSP, 2007.

[13] George Candea and Armando Fox. Crash-Only Software. HOTOS, 2003.

[14] Goetz Graefe. Cascades Framework for Query Optimization. HPL-95-18, 1995.

[15] Jim Gray. Why Do Computers Stop and What Can Be Done About It? Tandem Tech Report, 1986.

[16] Jim Gray and Andreas Reuter. Transaction Processing. Morgan Kaufmann, 1993.

[17] Jim Gray et al. Granularity of Locks in a Shared Data Base. VLDB, 1975.

[18] Maurice Herlihy and J. Eliot B. Moss. Transactional Memory. ISCA, 1993.

[19] John Hughes. Why Functional Programming Matters. Addison-Wesley, 1990.[20] Stratos Idreos et al. Database Cracking. CIDR, 2007.

[21] Michael Isard et al. Quincy. SOSP, 2009.

[22] Daniel Jiménez and Calvin Lin. Dynamic Branch Prediction with Perceptrons. HPCA, 2001.

[24] H. T. Kung and John T. Robinson. On Optimistic Methods for Concurrency Control. ACM TODS, 1981.

[26] Leslie Lamport. The Part-Time Parliament. ACM TOCS, 1998.

[27] Leslie Lamport. Specifying Systems: The TLA+ Language. Addison-Wesley, 2002.

[28] Butler Lampson. Protection. ACM Operating Systems Review, 1974.

[29] Butler Lampson. Hints for Computer System Design. ACM OS Review, 1983.

[30] Chris Lattner and Vikram Adve. LLVM. CGO, 2004.

[31] C. L. Lawson et al. Basic Linear Algebra Subprograms. ACM TOMS, 1979.

[32] R. Levin et al. Policy/Mechanism Separation in Hydra. SOSP, 1975.

[33] Henry Lieberman. Your Wish is My Command: Programming by Example. Morgan Kaufmann, 2001.

[34] Nicholas Matsakis and Felix Klock. The Rust Language. ACM SIGAda, 2014.

[35] Donn Seeley. A Tour of the Worm. USENIX, 1989.

[36] David Parnas. Designing Software for Ease of Extension and Contraction. IEEE TSE, 1979.

[37] Vern Paxson. End-to-End Internet Packet Dynamics. IEEE/ACM TON, 1999.

[38] Ken Petersen et al. Flexible Update Propagation for Weakly Consistent Replication. SOSP, 1997.

[39] Hamid Pirahesh et al. Rule-Based Query Rewrite in Starburst. SIGMOD, 1992.

[40] Gerald Popek and Robert Goldberg. Formal Requirements for Virtualizable Architectures. CACM, 1974.

[41] Dennis Ritchie and Ken Thompson. The UNIX Time-Sharing System. CACM, 1974.

[42] J. H. Saltzer et al. End-to-End Arguments. ACM TOCS, 1984.

[43] Saltzer and Schroeder. Protection of Information in Computer Systems. Proc. IEEE, 1975.

[44] Patricia Selinger et al. Access Path Selection. SIGMOD, 1979.

[45] Alexander Stepanov and Meng Lee. The Standard Template Library. HP Tech Report, 1994.

[46] Michael Stonebraker and Lawrence Rowe. The Design of POSTGRES. SIGMOD, 1986.

[47] Clint Whaley and Jack Dongarra. ATLAS. SC, 1998.

[48] Hubert Zimmermann. OSI Reference Model. IEEE Comm, 1980.

[49] David Parnas. On the Criteria to Be Used in Decomposing Systems. CACM, 1972.

[50] David Clark. The DARPA Internet Protocols. SIGCOMM, 1988.

[51] George Candea and Armando Fox. Crash-Only Software. HOTOS, 2003.

[52] Sudipta Chattopadhyay et al. ODR: Output-Derived Replay. PLDI, 2010.

[53] Nick McKeown et al. OpenFlow. ACM SIGCOMM CCR, 2008.

[54] George Dunlap et al. ReVirt. OSDI, 2002.

[55] Carl Waldspurger. Memory Resource Management in VMware ESX. OSDI, 2002.

[56] Gerwin Klein et al. seL4: Formal Verification of an OS Kernel. SOSP, 2009.

[58] Rob Pike et al. Plan 9 from Bell Labs. UKUUG, 1990.

[59] James Corbett et al. Spanner: Google's Globally-Distributed Database. OSDI, 2012. David A. Patterson and Carlo H. Séquin. A Reduced Instruction Set Computer. ISCA, 1981.


## HOW TO CITE

If you find this analysis useful, please cite it as:

> Joy Arulraj. *Elements of System Design* arXiv preprint arXiv:TBD, 2025.

## HOW TO CONTRIBUTE

We welcome PRs that add, refine, or re-group principles, and PRs that add "classic" papers as examples. Open an issue describing:
  - **What** you are adding or changing.
  - **Why** (include a rationale for the orthogonality and generality of the principle).
  - **Citations** (classic papers preferred).
