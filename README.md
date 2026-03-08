# Security Digital Twin — IDS Placement & Attack Simulation

This project implements a **Security Digital Twin** for enterprise networks, using CVE/CVSS-based vulnerability modeling, Monte Carlo attack simulation, and cost-aware IDS placement optimization across four experimental phases.

---

## Project Overview

Modern enterprise infrastructures are complex and dynamic, making it difficult to determine where Intrusion Detection System (IDS) sensors should be deployed for maximum coverage under a limited budget.

This project builds a Security Digital Twin — a simulation model of an enterprise network — and uses it to:

- Model realistic attack probabilities using real CVE/CVSS data
- Simulate attacker movement using four distinct attacker strategies
- Score node risk based on Monte Carlo attack path analysis
- Optimize IDS placement under a monetary budget constraint
- Evaluate detection performance including false positives, Precision/Recall/F1
- Stress-test the system under dynamic network changes and at scale

---

## Architecture

The network twin models a layered enterprise topology:

```
Internet → DMZ → Application → Internal → Database / SCADA
```

Each layer contains multiple nodes assigned roles, services, and CVE-based compromise probabilities. Edges carry action types (pivot, lateral, cred_theft, priv_esc) and probabilistic compromise weights derived from real CVE entries using a noisy-OR aggregation model.

---

## CVE/CVSS Vulnerability Model

Every edge probability is computed from a real offline CVE catalog covering 12 service types including web_app, dbms, auth_service, rdp, ssh, scada_gateway, and others.

The compromise probability pipeline:

1. Each CVE entry contributes a probability: `p = (cvss_base / 10) × (exploitability / 10)`
2. Multiple CVEs per service are combined via noisy-OR: `p = 1 − ∏(1 − pᵢ)`
3. Multiple services per node are combined via noisy-OR
4. Role multipliers and action multipliers are applied
5. A fallback proxy CVSS score is used when no CVE entries exist for a service
6. Final probability is clamped to `[0.05, 0.98]`

---

## Attacker Models

Four attacker strategies are implemented:

| Mode | Description |
|------|-------------|
| Random | Weighted random walk proportional to edge compromise probability |
| MPP (Most Probable Path) | Dijkstra on −log(p) weights; follows the statistically optimal path |
| Stealthy | Prefers low-degree nodes to avoid detection |
| Noisy | Prefers high-degree nodes; aggressive and fast |

---

## IDS Placement Strategies

All strategies operate under a **monetary budget** (not just a node count limit). Each node has a role-based monitoring cost with random jitter.

| Strategy | Method |
|----------|--------|
| Risk-aware | Greedy value/cost ratio using Monte Carlo risk scores |
| Degree | Greedy value/cost ratio using node degree |
| Betweenness | Greedy value/cost ratio using betweenness centrality |
| PageRank | Greedy value/cost ratio using PageRank scores |
| Random | Random selection within budget |
| Knapsack-Exact | Exact 0/1 DP with greedy fallback when table size exceeds safety bound |

---

## Experimental Phases

### Phase 1 — Baseline IDS Placement

- Builds small (≈30 nodes) and large (≈220 nodes) network twins
- Computes Monte Carlo risk scores
- Compares risk-aware vs random IDS placement on node-count budget
- Outputs risk heatmaps and detection vs budget curves

---

### Phase 2 — Cost-Aware Placement on Enterprise Twin

- Builds a full layered enterprise twin (≈235 nodes)
- Introduces monetary budget constraint with role-based monitoring costs
- Compares all 5 placement strategies across budget levels from 1% to 20%
- Plots detection rate vs monetary budget curves for all strategies

---

### Phase 3 — Dynamic Reconfiguration + Scalability

**Dynamic Reconfiguration:**
- Injects a zero-day vulnerability (sets selected edge probabilities to 0.99)
- Removes high-degree nodes simulating node failures
- Compares detection: Before change | After change (old IDS) | After change (re-optimized IDS)

**Scalability Benchmark:**
- Tests network sizes N ∈ {100, 500, 1000, 5000, 10000}
- Measures total pipeline runtime (Monte Carlo + placement + detection)
- Produces a log-log runtime plot

---

### Phase 4C — Precision / Recall / F1

- Introduces a false alarm probability for benign traffic flows
- Sweeps IDS sensitivity from 0.2 to 1.0
- Computes Precision, Recall, and F1 at each sensitivity level
- Plots a full Precision–Recall curve and reports the best F1 point

---

### Phase 4D — Stackelberg Worst-Case Detection

- Models the scenario where the attacker knows the IDS placement and adapts
- For each placement strategy, evaluates detection under all 4 attacker modes
- Reports worst-case detection (min over attacker modes) per strategy
- Compares strategies including Knapsack-Exact if Phase 4E has been run

---

### Phase 4E — Exact Knapsack IDS Placement

- Implements exact 0/1 knapsack DP for IDS node selection under monetary budget
- Cost discretisation with configurable unit (default: hundreds)
- Greedy fallback when n × W exceeds a configurable state limit (default: 2,000,000)
- Automatically detected and included by Phase 4D when loaded first

---

### Phase 4F — Memory Profiling + CSV Export

- Re-runs the scalability benchmark from Phase 3 with RSS memory tracking via psutil
- Produces separate log-log plots for runtime and memory delta
- Exports all measurements to CSV

---

### Phase 4G — Attacker Skill Sweep

- Fixes the IDS placement (risk-aware, 5% budget)
- Evaluates detection rate independently for each of the 4 attacker modes
- Shows how placement quality degrades against more sophisticated attackers

---

## Run Order

The phases must be run in the following order inside the notebook:

```
Phase 1  (standalone)
Phase 2  (standalone)
Phase 3  ← defines all core globals used by Phase 4
Phase 4E ← must run before 4D to enable Knapsack-Exact
Phase 4C, 4D, 4F, 4G  (any order, after Phase 3 and 4E)
```

Each Phase 4 cell includes a dependency guard that raises a clear error if Phase 3 has not been run first.

---

## Configuration

Key parameters at the top of each phase file:

| Parameter | Default | Description |
|-----------|---------|-------------|
| `SEED` | 42 | Global random seed for reproducibility |
| `FAST_DEMO` | False | Reduces simulation counts for quick iteration |
| `ATTACKER_MODE` | "random" | Default attacker for risk scoring |
| `NUM_ATTACKS_LARGE` | 8000 | Monte Carlo simulations per experiment |
| `P_DETECT_ON_IDS` | 0.95 | Imperfect IDS detection probability |
| `BETWEENNESS_K` | 200 | Approximate betweenness sample size (clamped to |V|) |

---

## Technologies Used

- Python 3
- NetworkX — graph construction and centrality computation
- Matplotlib — visualization and figure export
- psutil — RSS memory tracking (Phase 4F)
- Monte Carlo simulation — risk scoring and detection evaluation

---

## Key Design Decisions

**CVE-based probabilities** — edge weights are derived from real CVE entries rather than arbitrary values, making the model grounded in actual vulnerability data.

**Monetary budget** — IDS placement is constrained by cost, not just node count, reflecting real deployment constraints.

**Imperfect detection** — IDS nodes detect attacks with probability 0.95, not 1.0, making results more realistic.

**Safe sampling** — all random sampling uses a custom `safe_sample` function that clamps k to population size, preventing crashes on small network layers.

**Betweenness clamping** — approximate betweenness uses `k_eff = min(BETWEENNESS_K, |V|)` to prevent a ValueError inside NetworkX on small graphs.

**Stackelberg framing** — Phase 4D models the defender/attacker interaction as a game where the attacker best-responds to the IDS placement, giving a worst-case detection bound.
