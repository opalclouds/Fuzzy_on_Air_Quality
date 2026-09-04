# 🌫️ Multi-Pollutant Fuzzy Air Quality Risk Evaluator

A non-linear **Fuzzy Inference System (FIS)** built with Python and `scikit-fuzzy` that evaluates multi-pollutant health risks from real-world environmental sensor logs. 

Standard AQI formulas isolate the single worst pollutant while ignoring compound toxicities. This model applies **Mamdani Fuzzy Inference** to evaluate simultaneous exposures of $\text{PM}_{2.5}$, $\text{NO}_2$, and $\text{O}_3$ and derive a unified **Health Hazard Score $[0, 100]$**.

---

## 📌 Key System Features
- **Compound Exposure Modeling:** Captures additive health risks when multiple pollutants exist at moderate levels.
- **Smooth Membership Transitions:** Replaces rigid step-function thresholds with continuous trapezoidal and triangular fuzzy sets.
- **Automated Data Pipeline:** Integrates directly with real-world sensor logs (e.g., Kaggle Indian City Air Quality Dataset) using Pandas.
- **Centroid Defuzzification:** Maps overlapping rule evaluations into a single continuous crisp metric.

---

## 📐 System Architecture & Mathematical Foundations

### 1. Membership Functions (Fuzzification)
Input variables are fuzzified across specified Universes of Discourse:

$$\text{PM}_{2.5} \in [0, 500] \, \mu g/m^3 \quad \vert{} \quad \text{NO}_2 \in [0, 401] \, \mu g/m^3 \quad \vert{} \quad \text{O}_3 \in [0, 401] \, \mu g/m^3$$

$$\text{Health Hazard Score} \, (H) \in [0, 100]$$

#### Membership Operators
* **Trapezoidal Set:**
  $$\mu_A(x; a, b, c, d) = \max\left(0, \min\left(\frac{x - a}{b - a}, 1, \frac{d - x}{d - c}\right)\right)$$
* **Triangular Set:**
  $$\mu_A(x; a, b, c) = \max\left(0, \min\left(\frac{x - a}{b - a}, \frac{c - x}{c - b}\right)\right)$$

---

### 2. Fuzzy Rule Engine
Rule evaluation utilizes Mamdani Minimum ($t$-norm) for conjunctions (AND) and Maximum ($s$-norm) for disjunctions (OR):

$$\mu_{A \cap B}(x) = \min(\mu_A(x), \mu_B(x)) \qquad \mu_{A \cup B}(x) = \max(\mu_A(x), \mu_B(x))$$

**Mamdani Rule Base:**
1. **Rule 1 (Safe Baseline):**  
   $$\text{IF } \text{PM}_{2.5} \text{ is Good } \mathbf{\wedge} \, \text{NO}_2 \text{ is Low } \mathbf{\wedge} \, \text{O}_3 \text{ is Low} \implies H \text{ is Safe}$$
2. **Rule 2 (Moderate Hazard):**  
   $$\text{IF } \text{PM}_{2.5} \text{ is Moderate } \mathbf{\vee} \, \text{NO}_2 \text{ is Moderate } \mathbf{\vee} \, \text{O}_3 \text{ is Moderate} \implies H \text{ is Moderate}$$
3. **Rule 3 (Compound High Risk):**  
   $$\text{IF } \text{PM}_{2.5} \text{ is Moderate } \mathbf{\wedge} \, (\text{NO}_2 \text{ is High } \mathbf{\vee} \, \text{O}_3 \text{ is High}) \implies H \text{ is High}$$
4. **Rule 4 (Critical Emergency):**  
   $$\text{IF } \text{PM}_{2.5} \text{ is Unhealthy } \mathbf{\vee} \, \text{NO}_2 \text{ is High } \mathbf{\vee} \, \text{O}_3 \text{ is High} \implies H \text{ is Critical}$$

---

### 3. Defuzzification
The aggregated fuzzy output set $\mu_H(y)$ is converted into a crisp hazard index $z^*$ using **Center of Gravity (Centroid)** defuzzification:

$$z^* = \frac{\int y \cdot \mu_H(y) \, dy}{\int \mu_H(y) \, dy}$$

---

## 📊 Visualizations & Output Artifacts

The system generates three primary diagnostic figures during evaluation:

### 1. Variable Membership Function Distribution
Shows the overlapping fuzzy partitions across input variables ($\text{PM}_{2.5}$, $\text{NO}_2$, $\text{O}_3$) and the output set ($H$).

![Fuzzy Membership Functions](<img width="1189" height="490" alt="image" src="https://github.com/user-attachments/assets/c28e2a50-c5aa-489c-b031-a3fbcbd3c2ff" />
)

### 2. Temporal Hazard Score Tracking
Tracking the continuous Fuzzy Health Hazard Score against raw $\text{PM}_{2.5}$ fluctuations across historical city sensor logs.

![Hazard Score Trend Over Time](hazard_trend.png)

### 3. 3D Decision Surface
3D surface visualization displaying the non-linear interaction between $\text{PM}_{2.5}$ and $\text{NO}_2$ mapped directly to the Health Hazard Score.

![3D Fuzzy Decision Surface](decision_surface_3d.png)

---

## 🛠️ Installation & Execution

```bash
# 1. Install dependencies
pip install scikit-fuzzy pandas numpy matplotlib

# 2. Run execution pipeline
python main.py
