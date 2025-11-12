# 🧠 QAOA for Max-Cut using Qiskit Runtime & Aer Simulator

## 📘 Overview

This project implements the **Quantum Approximate Optimization Algorithm (QAOA)** using **Qiskit** to solve the **Max-Cut** combinatorial optimization problem on a small, weighted graph.
It demonstrates a complete hybrid workflow — quantum circuits optimized by classical solvers — that can run either on a **local simulator** or **real IBM Quantum hardware** through the Qiskit Runtime environment.

You’ll see how to:

* Construct the Max-Cut Hamiltonian from a graph
* Build and optimize a QAOA ansatz
* Evaluate cost functions with the Qiskit **Estimator**
* Run the final circuit using the **Sampler**
* Visualize optimization progress and final result distributions

---

## ⚙️ Requirements

This script uses the **latest stable Qiskit ecosystem (≥ 1.2)** and IBM Runtime modules.
Install everything with:

```bash
pip install qiskit qiskit-aer qiskit-ibm-runtime rustworkx scipy matplotlib
```

---

## 🧩 How It Works

### 1. **Graph Setup**

A simple graph of 5 nodes is created using `rustworkx`, with weighted edges representing the connections in the Max-Cut instance.
These weights define the cost function the QAOA circuit will attempt to minimize.

### 2. **Max-Cut Hamiltonian**

The graph edges are converted into **Pauli ZZ interactions** via `SparsePauliOp`.
Each edge contributes a term to the cost Hamiltonian, encoding the Max-Cut energy landscape.

### 3. **Building the QAOA Ansatz**

The `QAOAAnsatz` circuit is generated using:

* `cost_operator` = Max-Cut Hamiltonian
* `reps` = 2 (number of alternating operator layers)

All qubits are measured at the end.

### 4. **Backend Selection**

You can choose between:

* **Local simulation:** `backend = AerSimulator()`
* **IBM Quantum QPU (Runtime):**

  ```python
  service = QiskitRuntimeService(channel="ibm_cloud")
  backend = service.least_busy(operational=True, simulator=False, min_num_qubits=127)
  ```

  Replace `"YOUR API"` and `"Your instance"` with your actual IBM Quantum credentials.

> 💡 *The script defaults to `AerSimulator()` for local execution. To use a real backend, comment out that line and enable the runtime code block.*

### 5. **Optimization Loop**

The script defines `cost_func_estimator()`, which:

* Builds a **circuit publication (pub)** combining the ansatz, Hamiltonian, and parameters.
* Sends it to the Qiskit **Estimator**, which computes the expected value of the Hamiltonian.
* Returns that cost to the classical optimizer (`COBYLA`) for parameter updates.

`objective_func_vals` stores all intermediate cost values, allowing visualization of the optimization convergence.

### 6. **Final Sampling**

Once optimal parameters are found:

* The circuit is re-parameterized.
* The **Sampler** runs it multiple times to get a probability distribution of bitstrings.
* The most probable bitstring represents the Max-Cut partition.

### 7. **Visualization**

Two plots are generated:

* **Cost vs Iteration:** Shows how the optimizer converges.
* **Final Distribution:** Displays probabilities of measured bitstrings (the highest bars correspond to the most likely cut).

---

## 🧠 Results

At the end of the script, you’ll see:

* The optimized circuit
* The final cost value
* The **most likely bitstring** (solution)
* A bar chart of bitstring probabilities

Example output:

```
Result bitstring: [1, 0, 1, 0, 1]
```

This indicates the optimal node partitioning for the given graph.

---

## 🚀 Switching to Real Hardware

If you have IBM Quantum Runtime access:

1. Log in to your IBM Cloud account.
2. Replace this section in the script:

   ```python
   backend = AerSimulator()
   ```

   with:

   ```python
   service = QiskitRuntimeService(channel="ibm_cloud")
   backend = service.least_busy(operational=True, simulator=False, min_num_qubits=127)
   ```
3. Ensure your account is linked with:

   ```python
   QiskitRuntimeService.save_account(
       channel="ibm_cloud",
       token="YOUR_API_KEY",
       instance="YOUR_INSTANCE_NAME",
       overwrite=True
   )
   ```

That’s it — your QAOA will run on the real quantum backend.

---

## 📊 Summary of Key Components

| Component              | Description                                          |
| ---------------------- | ---------------------------------------------------- |
| `rustworkx`            | Graph generation and visualization                   |
| `SparsePauliOp`        | Encodes Max-Cut as a quantum Hamiltonian             |
| `QAOAAnsatz`           | Builds parameterized quantum circuit                 |
| `Estimator`            | Evaluates expectation values (quantum cost function) |
| `Sampler`              | Collects output distributions                        |
| `COBYLA`               | Classical optimizer for variational parameters       |
| `AerSimulator`         | Local simulation backend                             |
| `QiskitRuntimeService` | Connects to IBM Quantum Runtime QPUs                 |

---

## 💡 Notes

* The default setup runs locally; to access real QPUs, you need an IBM Cloud Quantum instance with a paid or research plan.
* The code includes basic error mitigation options (twirling and dynamical decoupling) for when running on real hardware.
* You can increase `maxiter` or `reps` for higher accuracy at the cost of longer runtime.

---

## 🧬 License

This code is free to use for research, education, or experimentation. Attribution is appreciated but not required.

---

Would you like me to make this README styled in GitHub-flavored Markdown (with emojis, collapsible sections, and syntax highlighting), or more like a formal research report summary for a paper?
