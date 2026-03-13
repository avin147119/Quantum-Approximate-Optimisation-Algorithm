# QAOA Portfolio Optimisation

Implementation of the **Quantum Approximate Optimisation Algorithm (QAOA)** applied to financial portfolio selection.

Selects the optimal k-asset portfolio from an n-asset universe by encoding the Markowitz objective as a QUBO and solving it on a quantum circuit.

---

## What it does

- Encodes a 6-asset portfolio optimisation problem as a **QUBO matrix**
- Converts QUBO → **Ising Hamiltonian** (h, J coefficients)
- Builds and runs a **QAOA circuit** in PennyLane with p=1 to 5 layers
- Trains parameters γ, β using the **COBYLA** classical optimiser
- Benchmarks against classical brute-force over all valid portfolios
- Runs **noise analysis** using a depolarising channel model (IBM Eagle baseline)

---

## Files

```
qaoa-portfolio-optimisation/
├── qaoa_portfolio_optimisation.ipynb   ← Main notebook (run this)
├── requirements.txt
├── README.md
└── .gitignore
```

---

## Quick start

```bash
pip install -r requirements.txt
jupyter notebook qaoa_portfolio_optimisation.ipynb
```

---

## Asset universe

| Qubit | Asset | Return | Volatility |
|-------|-------|--------|------------|
| q0 | SGX Index | 8.2% | 18% |
| q1 | HK Tech | 14.3% | 28% |
| q2 | India Bonds | 5.1% | 7% |
| q3 | ASEAN REITs | 9.4% | 14% |
| q4 | EM IG Credit | 6.3% | 9% |
| q5 | Gold (USD) | 7.1% | 16% |

---

## QAOA formulation

**Objective:**

$$\min_{\mathbf{x}} \; \lambda \mathbf{x}^\top \Sigma \mathbf{x} - (1-\lambda)\boldsymbol{\mu}^\top \mathbf{x} + P\left(\textstyle\sum_i x_i - k\right)^2$$

**Circuit:**

$$|\psi(\boldsymbol{\gamma},\boldsymbol{\beta})\rangle = \prod_{l=1}^{p} e^{-i\beta_l H_M} e^{-i\gamma_l H_C} \, |{+}\rangle^{\otimes n}$$

| Component | Gate | Role |
|-----------|------|------|
| Initialisation | H⊗n | Equal superposition over all 2ⁿ states |
| Cost unitary U_C(γ) | IsingZZ + RZ | Encodes Ising Hamiltonian |
| Mixer unitary U_M(β) | RX | Transverse-field mixer |
| Optimiser | COBYLA | Minimises ⟨H_C⟩ classically |

---

## Results

| Depth p | Approximation ratio | Correct portfolio |
|---------|--------------------|--------------------|
| 1 | 81.2% | ❌ |
| 2 | 90.1% | ✅ |
| 3 | 95.4% | ✅ |
| 4 | 97.2% | ✅ |
| 5 | **98.9%** | ✅ |

**Optimal portfolio:** SGX Index + India Bonds + Gold  
Return: 6.8% · Risk: 9.2% · Sharpe: 0.315

**Noise (IBM Eagle, ε = 0.2%):** 98.6% of ideal energy

---

## Dependencies

```
pennylane>=0.38
numpy>=1.24
scipy>=1.11
matplotlib>=3.7
```

---

## References

- Farhi, E., Goldstone, J., & Gutmann, S. (2014). *A Quantum Approximate Optimization Algorithm*. [arXiv:1411.4028](https://arxiv.org/abs/1411.4028)
- Markowitz, H. (1952). Portfolio Selection. *Journal of Finance*, 7(1), 77–91.
- Cerezo, M. et al. (2021). Variational quantum algorithms. *Nature Reviews Physics*, 3, 625–644.
