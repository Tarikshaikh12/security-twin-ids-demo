# Security Twin IDS Placement Experiment

This project demonstrates how a **Security Twin model** can be used to analyze attack propagation in a network and determine optimal placement of Intrusion Detection System (IDS) probes.

The main goal is to compare **risk-aware IDS placement** with **random IDS placement** and evaluate how effectively each strategy detects simulated attacks.

The implementation is written in Python and uses network simulation, Monte Carlo experiments, and visualization techniques to analyze attack behavior in both small and large network environments.

---

## Project Overview

Modern cloud infrastructures are complex and dynamic, making it difficult to determine where security monitoring components such as IDS sensors should be deployed.  
To explore this problem, a **Security Twin** model of a network is constructed and used to simulate potential attack paths.

The experiment follows these steps:

1. Construct a network model (Security Twin)
2. Simulate attacker movement through the network
3. Calculate node risk scores based on attack paths
4. Deploy IDS probes using different strategies
5. Compare detection performance

---

## Network Models

### Small Security Twin

A simple network architecture consisting of six components:

- Gateway
- Load Balancer
- Application Server A
- Application Server B
- Internal Switch
- Database

This smaller model helps demonstrate the basic behavior of attack propagation and IDS placement strategies.

---

### Large Security Twin

A larger and more realistic network is generated automatically using a **power-law cluster graph**, which produces properties commonly observed in real infrastructures:

- Scale-free topology
- Clustered connections
- Multiple possible attack paths

The large twin in this experiment contains approximately:

- **220 nodes**
- **650+ edges**

This allows more complex attack simulations and provides a more realistic environment for evaluating IDS placement.

---

## Attack Simulation

Attack behavior is simulated using a **Monte Carlo approach**.

Each simulation:

1. Starts from the **attacker node**
2. Moves through the network following directed edges
3. Uses probabilistic edge compromise values
4. Stops when the **target node** is reached or the attack fails

Thousands of attack simulations are executed to estimate how frequently each node appears in successful attack paths.

---

## Risk Scoring

Nodes are assigned a **risk score** based on how frequently they appear in successful attack paths.

Higher scores indicate nodes that are more likely to be involved in an attack propagation chain.

These scores are then used to guide IDS placement.

---

## IDS Placement Strategies

Two IDS deployment strategies are evaluated.

### Risk-Aware Placement

IDS probes are placed on nodes with the highest calculated risk scores.

This strategy attempts to monitor critical points in the attack propagation paths.

---

### Random Placement

IDS probes are placed randomly across the network.

This serves as a baseline to evaluate the benefit of risk-aware placement.

---

## Experimental Results

Detection performance is evaluated for both network sizes.

Example results from the experiment:

| Network | Risk-Aware Detection | Random Detection |
|-------|----------------------|----------------|
| Small Twin | ~0.40 | ~0.07 |
| Large Twin | ~0.02 | ~0.01 |

The results show that **risk-aware IDS placement consistently performs better than random placement**, especially in structured networks.

---

## Visualization

The experiment generates several visual outputs:

- Network **risk heatmaps**
- Highlighted **attacker and target nodes**
- **IDS probe locations**
- Example **attack propagation paths**
- Detection performance comparison charts

These visualizations help interpret how attacks move through the network and where monitoring should be placed.

---

## Technologies Used

- Python
- NetworkX
- Matplotlib
- Monte Carlo simulation

---

## Purpose of the Project

This project is a conceptual demonstration of how **Security Twin modeling** can support cybersecurity analysis and help determine effective IDS deployment strategies in complex networks.
