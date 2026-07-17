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
  <img width="900" alt="NN-based multi-alloy topology optimization result" src=<img width="772" height="305" alt="Screenshot 2026-07-17 at 2 49 16 PM" src="https://github.com/user-attachments/assets/157d1d94-cde2-48e5-bd1c-eb748335b7af" />'
">
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
