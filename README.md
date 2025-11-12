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

MIT licence, it's free to use for any purposes name mentioning is not required but would be appreciated.  

---

---

## 📘 توضیحات پروژه

### 🔹 عنوان:

**شبیه‌سازی الگوریتم QAOA برای مسئله‌ی Max-Cut با استفاده از Qiskit و IBM Runtime**

---

### 🔹 هدف:

این پروژه برای پیاده‌سازی و شبیه‌سازی الگوریتم **QAOA (Quantum Approximate Optimization Algorithm)** طراحی شده است.
هدف، حل یکی از مسائل کلاسیک بهینه‌سازی ترکیبی به نام **Max-Cut** روی یک گراف است — با استفاده از ابزارهای **Qiskit**، **IBM Quantum Runtime** و **شبیه‌ساز AerSimulator**.

---

### 🔹 خلاصه عملکرد:

کد شما مراحل زیر را طی می‌کند:

1. **ساخت گراف (Graph):**
   ابتدا یک گراف با ۵ نود ساخته می‌شود و یال‌های آن با وزن ۱.۰ تعریف می‌شوند. این گراف همان داده‌ی ورودی برای مسئله Max-Cut است.

2. **تبدیل گراف به همیلتونی (Hamiltonian):**
   با استفاده از تابع `build_max_cut_paulis`، هر یال گراف به اپراتورهای پاولی نوع `ZZ` تبدیل می‌شود تا بتوان مسئله را به زبان مکانیک کوانتومی بیان کرد. نتیجه‌ی این بخش یک شیء از نوع `SparsePauliOp` است.

3. **ساخت مدار QAOA:**
   با استفاده از `QAOAAnsatz`، مدار کوانتومی الگوریتم QAOA ساخته می‌شود. این مدار پارامترهایی دارد که باید در فرآیند بهینه‌سازی تنظیم شوند.

4. **بهینه‌سازی پارامترها (Optimization):**
   با استفاده از تابع `scipy.optimize.minimize` و الگوریتم **COBYLA**، پارامترهای QAOA طوری تنظیم می‌شوند که مقدار تابع هدف (انرژی مورد انتظار) حداقل شود.
   از شیء `Estimator` برای محاسبه انرژی میانگین (expected value) استفاده شده است.

5. **شبیه‌سازی و اجرای نهایی:**
   چون حساب کاربری فعلی IBM از پلن رایگان استفاده می‌کند، اجرای نهایی روی **شبیه‌ساز AerSimulator** انجام می‌شود.
   اما اگر دسترسی QPU واقعی داشته باشید، می‌توانید با تغییر خط:

   ```python
   backend = AerSimulator()
   ```

   به:

   ```python
   backend = service.least_busy(operational=True, simulator=False)
   ```

   مستقیماً روی سخت‌افزار IBM اجرا کنید.

6. **نمونه‌گیری و تحلیل خروجی:**
   پس از بهینه‌سازی، مدار نهایی با پارامترهای بهینه اجرا می‌شود و توزیع نتایج اندازه‌گیری (bitstring outcomes) رسم می‌گردد.
   محتمل‌ترین بیت‌استرینگ، پاسخ تقریبی الگوریتم برای مسئله‌ی Max-Cut است.

---

### 🔹 کتابخانه‌های مورد استفاده:

* `qiskit_ibm_runtime` → برای ارتباط با سرویس‌های کوانتومی IBM
* `qiskit_aer` → برای شبیه‌سازی کوانتومی روی CPU
* `rustworkx` → برای ایجاد و تحلیل گراف‌ها
* `scipy.optimize` → برای انجام بهینه‌سازی کلاسیک
* `matplotlib` → برای رسم نمودارها

---

### 🔹 ورودی و خروجی:

**ورودی:**

* گرافی شامل نودها و یال‌ها با وزن مشخص
* پارامترهای اولیه برای الگوریتم QAOA

**خروجی:**

* انرژی کمینه تخمینی
* پارامترهای بهینه
* توزیع احتمالات نتایج اندازه‌گیری
* نمایش گرافیکی از روند بهینه‌سازی و توزیع نتایج

---

### 🔹 نکات فنی:

* اجرای شبیه‌سازی نیاز به نصب Qiskit نسخه‌ی جدید دارد (حداقل `qiskit>=1.2.0` و `qiskit-ibm-runtime>=0.25.0`).
* اگر از QPU واقعی استفاده می‌کنید، باید توکن و instance خود را در بخش:

  ```python
  QiskitRuntimeService.save_account(
      channel="ibm_cloud",
      token="YOUR_API",
      instance="YOUR_INSTANCE",
      overwrite=True
  )
  ```

  جایگزین کنید.
* برای کاهش خطا در اجرای واقعی، از تنظیمات **twirling** و **dynamical decoupling (XY4)** استفاده شده است.

---

### 🔹 نتیجه:

این پروژه یک چارچوب کامل برای اجرای QAOA بر روی مسئله‌ی Max-Cut فراهم می‌کند.
اگر روی شبیه‌ساز اجرا شود، می‌تواند برای آموزش و تحلیل دقیق الگوریتم به کار رود.
اگر روی QPU اجرا شود، می‌تواند نتایج تجربی نزدیک به سخت‌افزار واقعی را ارائه دهد.


