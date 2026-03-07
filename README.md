# Security Twin IDS Placement Simulation

This repository contains a conceptual experimental simulation
demonstrating risk-aware Intrusion Detection System (IDS) placement
using a Security Twin abstraction.

The experiment models a simplified network topology and performs
Monte Carlo attack path simulations to measure node traversal
frequency across the network.

Nodes that frequently appear in attack paths are identified as
topological choke points and recommended locations for IDS probes.

The code also visualizes:

- network topology
- node risk heatmap
- risk score distribution

This experiment demonstrates the decision logic of Security-Twin-based
IDS placement rather than implementing a full operational IDS.
