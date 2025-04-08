# Pipelining

Sure! Here's a simple and clear explanation of the **pipeline logic and hardware computation** for the **Pythagoras Theorem** example, broken down step-by-step:

---

### 🧮 **1. Computation Goal: Pythagoras Theorem**

We want to calculate the hypotenuse `c` using the formula:

\[
c = \sqrt{a^2 + b^2}
\]

To do this in hardware, we need to:
- **Square** the values of `a` and `b`
- **Add** the squared results: \( a^2 + b^2 \)
- **Take the square root** of the sum: \( \sqrt{a^2 + b^2} \)

---


### 🧩 **3. Pipeline Concept: Spread the Work**

To make this work in hardware, we use **pipelining**, which means:
   🔁 **5. Pipeline Benefit**

- **High clock speed**: Each stage is simple enough to finish quickly.
- **Parallel processing**: After Stage 1 completes for one set of inputs, it can start working on the next inputs while Stages 2 and 3 handle the previous data.

