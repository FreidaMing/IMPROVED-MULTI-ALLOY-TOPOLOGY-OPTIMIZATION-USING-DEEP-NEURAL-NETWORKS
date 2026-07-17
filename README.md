# A Neural Network-Based Level-Set Method for Multi-Alloy Topology Optimization

## Overview

Directed Energy Deposition (DED) and related metal additive manufacturing processes enable the fabrication of components with spatially varying alloy compositions. This capability creates opportunities to design graded multi-alloy structures that combine the desirable properties of several alloys within a single component.

However, conventional multi-material topology optimization (MMTO) methods generally do not account for the metallurgical compatibility constraints required for manufacturing multi-alloy structures. In prior work, a level-set-based Multi-Alloy Topology Optimization (MATO) framework was developed to integrate structural optimization with compatible alloy-transition requirements. Although effective, the original formulation becomes increasingly expensive as the number of candidate alloy compositions grows because each alloy phase, together with the void phase, requires its own level-set function.

This project introduces a **neural-network-driven reparameterization of the original MATO framework**. Instead of evolving each level-set function independently, a single neural network represents all alloy and void level-set fields. This unified representation enables end-to-end differentiability, automatic sensitivity computation through backpropagation, and improved scalability for high-dimensional multi-alloy design problems.

---

## Motivation

The original MATO formulation successfully incorporated metallurgical compatibility into multi-material topology optimization, but its computational cost increases rapidly with the number of alloy phases.

For a design containing \(M\) alloy phases, the original method requires:

- one level-set field for each alloy phase;
- an additional level-set field for the void phase;
- repeated finite-element analyses;
- separate sensitivity calculations; and
- sequential updates of multiple level-set functions.

As the number of candidate alloys increases, this formulation becomes increasingly difficult to scale.

The NN-based framework addresses this limitation by using a **single neural network to parameterize all level-set fields simultaneously**.

---

## Main Contributions

This work provides:

1. **A unified neural-network representation of all level-set functions**  
   A single neural network generates the level-set fields associated with all alloy phases and the void phase.

2. **End-to-end differentiable multi-alloy topology optimization**  
   Structural objectives, material constraints, compatibility requirements, and geometric regularization are integrated into a unified loss function.

3. **Automatic sensitivity computation**  
   Gradients are evaluated through backpropagation and automatic differentiation instead of requiring separate analytical sensitivity derivations for every design variable.

4. **Improved scalability for high-dimensional MATO problems**  
   The neural-network reparameterization reduces the need to evolve many independent level-set fields.

5. **Compatibility-aware graded-alloy design**  
   The method preserves the central MATO objective of generating structurally efficient layouts with feasible alloy transitions.

6. **Demonstrated computational efficiency gains**  
   Numerical examples show that the NN-based method can generate viable graded-alloy topologies more efficiently than the original level-set formulation.

---

## Methodology

### 1. Neural-Network Level-Set Representation

The spatial coordinates of each point in the design domain are provided to a neural network:

\[
\mathbf{x} = (x,y).
\]

The neural network outputs the level-set values for all alloy phases and the void phase:

\[
\boldsymbol{\phi}(\mathbf{x};\boldsymbol{\theta})
=
\left[
\phi_1(\mathbf{x}),
\phi_2(\mathbf{x}),
\ldots,
\phi_M(\mathbf{x}),
\phi_{\mathrm{void}}(\mathbf{x})
\right],
\]

where:

- \(M\) is the number of alloy phases;
- \(\boldsymbol{\theta}\) denotes the trainable neural-network parameters; and
- each output field controls the spatial distribution of one phase.

Instead of optimizing every level-set value independently, the optimization updates the shared neural-network parameters.

---

### 2. Unified Loss Function

The optimization problem is formulated as the minimization of a unified loss:

\[
\mathcal{L}
=
W_J \mathcal{L}_{\mathrm{struct}}
+
W_V \mathcal{L}_{\mathrm{volume}}
+
W_C \mathcal{L}_{\mathrm{compatibility}}
+
W_R \mathcal{L}_{\mathrm{regularization}},
\]

where:

- \(\mathcal{L}_{\mathrm{struct}}\) represents the structural objective, such as compliance;
- \(\mathcal{L}_{\mathrm{volume}}\) enforces material-volume requirements;
- \(\mathcal{L}_{\mathrm{compatibility}}\) promotes feasible graded-alloy transitions;
- \(\mathcal{L}_{\mathrm{regularization}}\) controls geometric complexity and interface smoothness; and
- \(W_J\), \(W_V\), \(W_C\), and \(W_R\) are weighting coefficients.

A representative augmented-Lagrangian form for the volume constraints is

\[
\mathcal{L}_{\mathrm{volume}}
=
\sum_{i=1}^{M}
\left[
\lambda_i \left(V_i-V_{i,\max}\right)
+
\frac{\mu_i}{2}
\left(V_i-V_{i,\max}\right)^2
\right].
\]

---

### 3. Alloy-Hierarchy Constraint

To prevent incompatible alloys from being placed in direct contact, the alloy phases can be arranged according to a predefined compatible sequence.

A representative hierarchy loss is

\[
\mathcal{L}_{\mathrm{hier}}
=
\sum_{i=2}^{M}
\frac{1}{|D|}
\int_D
\left[
\phi_i(\mathbf{x})
-
\left(\phi_{i-1}(\mathbf{x})-d_i\right)
\right]_+^2
\,d\Omega,
\]

where:

- \([x]_+ = \max(0,x)\);
- \(d_i\) controls the required separation or ordering between neighboring level-set fields; and
- the penalty discourages violations of the prescribed alloy hierarchy.

This formulation promotes gradual and metallurgically feasible transitions between neighboring alloy compositions.

---

### 4. Finite-Element Analysis

For each optimization iteration:

1. The neural network predicts all level-set fields.
2. The level-set outputs are converted into phase indicators or material fractions.
3. Element stiffness properties are assembled from the local phase distribution.
4. The finite-element equilibrium problem is solved.
5. Structural responses, such as displacement, compliance, and stress, are evaluated.
6. The total loss is computed.
7. Gradients of the loss with respect to the neural-network parameters are obtained by backpropagation.
8. The network parameters are updated using a gradient-based optimizer.

---

## Computational Workflow

```text
Design coordinates and material properties
                    |
                    v
       Single neural network
                    |
                    v
   Alloy and void level-set fields
                    |
                    v
 Phase indicators / material fractions
                    |
                    v
       Finite-element analysis
                    |
                    v
 Structural objective and constraints
                    |
                    v
          Unified loss function
                    |
                    v
 Automatic differentiation and backpropagation
                    |
                    v
       Neural-network parameter update
                    |
                    v
             Convergence check
                    |
                    v
 Optimized topology and graded-alloy distribution
```

---

## Comparison with the Original MATO Method

| Feature | Original Level-Set MATO | NN-Based MATO |
|---|---|---|
| Level-set representation | Separate field for each phase | Single neural network for all fields |
| Sensitivity computation | Explicit or separately implemented | Automatic differentiation |
| Number of optimized variables | Grows directly with mesh and phase count | Controlled through shared NN parameters |
| Scalability | Limited for many alloy phases | Improved for higher-dimensional problems |
| Interface representation | Explicit level-set evolution | Neural-network-generated level sets |
| Optimization framework | Sequential field updates | Unified loss minimization |
| Compatibility treatment | Explicit alloy-ordering constraints | Compatibility penalties integrated into the loss |

---

## Repository Structure

A possible repository layout is:

```text
.
├── README.md
├── main.py
├── config.py
├── models/
│   └── level_set_network.py
├── fem/
│   ├── mesh.py
│   ├── stiffness.py
│   └── solver.py
├── optimization/
│   ├── loss_functions.py
│   ├── constraints.py
│   └── train.py
├── material_data/
│   └── alloy_properties.csv
├── examples/
├── results/
└── requirements.txt
```

Modify this section to match the actual repository structure.

---

## Installation

Clone the repository:

```bash
git clone <repository-url>
cd <repository-name>
```

Create and activate a Python environment, then install the required packages:

```bash
python -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt
```

For Windows:

```bash
.venv\Scripts\activate
pip install -r requirements.txt
```

---

## Running the Code

A typical execution command is:

```bash
python main.py
```

Before running the optimization, review the configuration file and specify:

- design-domain dimensions;
- mesh resolution;
- boundary conditions;
- applied loads;
- candidate alloy properties;
- compatible alloy sequence;
- volume limits;
- neural-network architecture;
- learning rate;
- penalty weights;
- convergence criteria; and
- output frequency.

---

## Expected Outputs

The framework can generate:

- optimized structural topology;
- graded multi-alloy distribution;
- alloy and void level-set fields;
- compliance history;
- volume-constraint histories;
- hierarchy-loss history;
- total-loss convergence;
- displacement field;
- von Mises stress distribution; and
- computational-efficiency comparisons with the original MATO method.

Example:

```html
<p align="center">
  <img width="900" alt="NN-based multi-alloy topology optimization result" src="PASTE_IMAGE_URL_HERE">
</p>
```

Replace `PASTE_IMAGE_URL_HERE` with the GitHub-generated image URL.

---

## Numerical Studies

The numerical examples are intended to evaluate:

- whether the NN-based representation can generate viable graded-alloy topologies;
- whether alloy-ordering and compatibility constraints are satisfied;
- whether structural performance remains comparable to the original method;
- how the solution changes as the number of candidate alloys increases;
- the effect of neural-network architecture and loss weights; and
- the computational-efficiency improvement relative to the original MATO framework.

---

## Applications

Potential applications include:

- multi-alloy Directed Energy Deposition;
- Wire Arc Additive Manufacturing;
- functionally graded structures;
- high-temperature components;
- lightweight structural systems;
- wear- and corrosion-resistant components;
- spatially tailored stiffness and thermal response; and
- large-scale topology optimization with many candidate alloys.

---

## Limitations and Future Work

Possible future directions include:

- stricter minimum-feature and minimum-layer-thickness control;
- adaptive loss weighting;
- improved treatment of inequality constraints;
- extension to three-dimensional design domains;
- thermomechanical and multiphysics objectives;
- manufacturing-path constraints;
- uncertainty quantification for alloy properties;
- experimental validation of optimized alloy gradients; and
- GPU acceleration for large-scale models.

---

## Publication

This repository supports the work:

> **A Neural Network-Based Level-Set Method for Enforcing Alloy Compatibility in Multi-Alloy Topology Optimization**

Replace the placeholder information below with the final publication record:

```bibtex
@inproceedings{shu_nn_mato,
  title     = {A Neural Network-Based Level-Set Method for Enforcing Alloy Compatibility in Multi-Alloy Topology Optimization},
  author    = {Shu, Yalan and others},
  booktitle = {Proceedings of the ASME International Design Engineering Technical Conferences and Computers and Information in Engineering Conference},
  year      = {2026},
  doi       = {TO_BE_ADDED}
}
```

---

## Related Work

This work extends the original MATO framework:

> **A Method for Enforcing Alloy Compatibility in Multi-Alloy Topology Optimization**

The original method introduced alloy-compatibility-aware level-set topology optimization, while the current work focuses on improving scalability through a differentiable neural-network representation.

---

## Contact

**Yalan Shu**  
Texas A&M University  
Email: `shuyl@tamu.edu`

---

## License

Add the license approved for this repository, such as the MIT License or BSD 3-Clause License.

---

## Disclaimer

This repository contains research code developed for academic use. Results may depend on mesh resolution, neural-network architecture, initialization, loss weights, optimization settings, material data, and alloy-compatibility assumptions. All results should be independently verified before engineering or manufacturing use.
