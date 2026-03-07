# Risk-Aware IDS Placement using Security Twins

This repository contains a conceptual experimental demonstration of **risk-aware Intrusion Detection System (IDS) placement** using a simplified **Security Twin network model**.

The objective of the experiment is to illustrate how analyzing simulated attack paths can help identify **critical nodes in a network** where IDS probes should ideally be deployed.

This implementation supports the research presentation discussing Security Twins, IDS evaluation challenges, and explainable intrusion detection.

---

# Concept

Traditional IDS deployments often rely on static placement of sensors without considering how attackers actually move through a network.

However, real-world networks are complex and attackers typically follow specific **attack paths** in order to reach critical assets such as databases or internal services.

A **Security Twin** represents a digital abstraction of the network that allows security analysts to simulate attacks and study how threats propagate through the infrastructure.

By analyzing these simulated attack paths, it becomes possible to identify:

- nodes that attackers must traverse
- critical choke points in the network
- optimal locations for IDS probes

---

# Simulated Network Topology

The simplified network used in this experiment contains the following components:

- Gateway
- Load Balancer
- Application Server A
- Application Server B
- Internal Switch
- Database Server

The attacker attempts to reach the database through different valid attack paths.

---

# Attack Path Simulation

The experiment performs a **Monte Carlo simulation** where multiple attack attempts are generated.

For each simulated attack:

1. A valid attack path is randomly selected
2. Each node visited during the attack is recorded
3. Node traversal frequencies are calculated

Nodes that appear frequently across attack paths represent **higher risk nodes**.

---

# Risk Score Definition

The risk score for each node is defined as:

Risk(node) = Node Traversals / Total Simulated Attacks

Nodes with higher scores indicate that attackers are very likely to traverse them during attacks.

Such nodes represent **strategic monitoring points for IDS deployment**.

---

# IDS Placement Insight

Instead of randomly deploying IDS probes in the network, the Security Twin approach allows us to identify **topological choke points** where monitoring coverage is maximized.

This approach can help reduce:

- missed attack paths
- unnecessary IDS sensors
- operational monitoring cost

---

# Purpose of the Experiment

This project demonstrates the **decision logic behind Security-Twin-based IDS placement** rather than implementing a full production IDS system.

The goal is to provide a clear conceptual demonstration aligned with the research papers discussed in the presentation.

---

# Technologies Used

Python  
NetworkX  
Matplotlib

---
# The Presentation Link is here:
https://www.canva.com/design/DAGQo7WVfbI/VQArqZA0ypvtAuKTNdQEmw/edit?utm_content=DAGQo7WVfbI&utm_campaign=designshare&utm_medium=link2&utm_source=sharebutton
