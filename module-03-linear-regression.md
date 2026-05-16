---
marp: true
theme: mahidol
paginate: true
size: 16:9
footer: "BDAT501 Data Science Fundamentals | Module 03 — Linear Regression | มหาวิทยาลัยมหิดล"
math: katex
---

<!-- _class: lead -->

<style scoped>
.logo-bar { position: absolute; top: 36px; right: 64px; display: flex; align-items: center; gap: 16px; }
.logo-bar img { width: 100px; height: 100px; object-fit: contain; }
</style>

<div class="logo-bar">
  <img src="fig/logos/mahidol.svg" alt="Mahidol University">
  <img src="fig/logos/logo-bdi.png" alt="BDI">
</div>

# การถดถอยเชิงเส้น

<div class="subtitle">Module 03 — Linear Regression</div>

**BDAT501** Data Science Fundamentals
คณะวิศวกรรมศาสตร์ · มหาวิทยาลัยมหิดล

ผู้สอน: **ผศ.ดร. ทวีศักดิ์ สมานชื่น**

---

## วัตถุประสงค์การเรียนรู้

เมื่อจบ module นี้ นักศึกษาสามารถ:

1. **อธิบาย** หลักการของ Linear Regression และ Ordinary Least Squares (OLS)
2. **อธิบาย** สมมติฐาน (assumptions) ที่จำเป็นสำหรับ Linear Regression
3. **ประยุกต์ใช้** Simple และ Multiple Linear Regression กับข้อมูลจริง
4. **ประยุกต์ใช้** scikit-learn เพื่อสร้างและประเมินโมเดล
5. **วิเคราะห์** ผลลัพธ์โมเดลด้วย $R^2$, MAE, RMSE และ Residual Plot

---

## เนื้อหาใน Module นี้

1. **แนวคิดและแรงจูงใจ** — ทำไมต้องใช้ Regression?
2. **Simple Linear Regression** — สมการ, การประมาณค่า OLS
3. **Multiple Linear Regression** — ตัวแปรอิสระหลายตัว, Multicollinearity
4. **Worked Example** — ทำนายราคาบ้านด้วย Boston Housing Dataset
5. **การประเมินผลโมเดล** — $R^2$, MAE, RMSE, Residual Analysis

---

<!-- _class: divider -->

## 01
## แนวคิดและแรงจูงใจ

Why Regression? What Problem Does It Solve?

---

## Regression คืออะไร?

### นิยาม

**Regression** คือกลุ่มของวิธีการทางสถิติที่ใช้อธิบาย**ความสัมพันธ์**  
ระหว่างตัวแปรตาม $y$ กับตัวแปรอิสระ $x$ หนึ่งตัวหรือมากกว่า

### ตัวอย่างปัญหาจริงที่ใช้ Regression

- *ราคาบ้าน* จากพื้นที่ใช้สอย, ทำเล, จำนวนห้อง
- *ยอดขาย* จากงบโฆษณา, ฤดูกาล
- *ความเสี่ยงโรค* จากอายุ, BMI, ระดับน้ำตาลในเลือด

> **ความแตกต่างจาก Classification:** Regression ทำนายค่า**ต่อเนื่อง** ไม่ใช่ป้ายกำกับ (label)

---

## Supervised Learning Framework

### โครงสร้างของปัญหา Regression

<div class="columns">

**Input (Features) $X$**
- ตัวแปรอิสระ $x_1, x_2, \ldots, x_p$
- เรียกว่า predictors หรือ covariates
- ชนิด: ตัวเลข, Categorical (ต้องแปลง)

**Output (Target) $y$**
- ตัวแปรตาม (continuous)
- คือสิ่งที่เราต้องการทำนาย
- ตัวอย่าง: ราคา, อุณหภูมิ, คะแนน

</div>

**เป้าหมาย:** หาฟังก์ชัน $\hat{f}$ ที่ดีที่สุด เพื่อให้ $\hat{y} = \hat{f}(X)$ ใกล้เคียง $y$ มากที่สุด

---

<!-- _class: divider -->

## 02
## Simple Linear Regression

One Feature, One Target

---

## สมการ Simple Linear Regression

### รูปแบบสมการ

$$
y = \beta_0 + \beta_1 x + \varepsilon
$$

- $y$ — ตัวแปรตาม (target)
- $x$ — ตัวแปรอิสระ (feature)
- $\beta_0$ — จุดตัดแกน $y$ (intercept)
- $\beta_1$ — ความชัน (slope): การเปลี่ยนแปลงของ $y$ เมื่อ $x$ เพิ่ม 1 หน่วย
- $\varepsilon$ — ความคลาดเคลื่อน (error term), สมมติว่า $\varepsilon \sim \mathcal{N}(0, \sigma^2)$

---

## Ordinary Least Squares (OLS)

### เป้าหมาย: หา $\hat{\beta}_0, \hat{\beta}_1$ ที่ minimize

$$
\text{RSS} = \sum_{i=1}^{n} (y_i - \hat{y}_i)^2 = \sum_{i=1}^{n} (y_i - \hat{\beta}_0 - \hat{\beta}_1 x_i)^2
$$

### Closed-form Solution

$$
\hat{\beta}_1 = \frac{\sum_{i=1}^n (x_i - \bar{x})(y_i - \bar{y})}{\sum_{i=1}^n (x_i - \bar{x})^2}
\qquad
\hat{\beta}_0 = \bar{y} - \hat{\beta}_1 \bar{x}
$$

> **Residual** $e_i = y_i - \hat{y}_i$ คือระยะห่างแนวตั้งระหว่างจุดข้อมูลกับเส้นถดถอย

---

## Assumptions ของ Linear Regression

### สมมติฐาน LINE

| ตัวอักษร | สมมติฐาน | วิธีตรวจสอบ |
|---|---|---|
| **L**inearity | ความสัมพันธ์เป็นเส้นตรง | Scatter plot, Residual vs Fitted |
| **I**ndependence | ข้อมูลแต่ละตัวอิสระต่อกัน | Durbin-Watson test |
| **N**ormality | Error มีการแจกแจงปกติ | Q-Q plot, Shapiro-Wilk |
| **E**qual variance | Homoscedasticity | Scale-Location plot |

---

<!-- _class: divider -->

## 03
## Multiple Linear Regression

More Features, Better Predictions?

---

## สมการ Multiple Linear Regression

### รูปแบบ Matrix

$$
\mathbf{y} = \mathbf{X}\boldsymbol{\beta} + \boldsymbol{\varepsilon}
$$

### รูปแบบทั่วไป

$$
y = \beta_0 + \beta_1 x_1 + \beta_2 x_2 + \cdots + \beta_p x_p + \varepsilon
$$

### OLS Solution แบบ Matrix

$$
\hat{\boldsymbol{\beta}} = (\mathbf{X}^\top \mathbf{X})^{-1} \mathbf{X}^\top \mathbf{y}
$$

---

## Multicollinearity

### ปัญหาเมื่อตัวแปรอิสระสัมพันธ์กันสูง

- ค่า coefficient ไม่เสถียร — เปลี่ยนมากเมื่อเพิ่ม/ลบตัวแปร
- Standard error ของ coefficient สูงผิดปกติ
- ตีความ $\beta_j$ ได้ยาก

### ตรวจสอบด้วย Variance Inflation Factor (VIF)

$$
\text{VIF}_j = \frac{1}{1 - R^2_j}
$$

- **VIF < 5** — ยอมรับได้
- **VIF ≥ 10** — มีปัญหา Multicollinearity รุนแรง

---

<!-- _class: divider -->

## 04
## Worked Example

ทำนายราคาบ้านด้วย California Housing Dataset

---

## Dataset: California Housing

### ข้อมูล

- **Source:** `sklearn.datasets.fetch_california_housing()`
- **จำนวน:** 20,640 ตัวอย่าง · 8 features
- **Target:** ราคาบ้านเฉลี่ย (median house value, หน่วย $100,000)


| Feature | คำอธิบาย |
|---|---|
| `MedInc` | รายได้เฉลี่ยของครัวเรือน |
| `HouseAge` | อายุบ้านเฉลี่ย |
| `AveRooms` | จำนวนห้องเฉลี่ยต่อครัวเรือน |
| `Latitude`, `Longitude` | พิกัดภูมิศาสตร์ |

---

## โค้ดตัวอย่าง: Load & Explore

```python
from sklearn.datasets import fetch_california_housing
import pandas as pd
import matplotlib.pyplot as plt

# โหลดข้อมูล
housing = fetch_california_housing(as_frame=True)
df = housing.frame

print(df.shape)       # (20640, 9)
print(df.describe())  # สถิติเบื้องต้น

```

---

## โค้ดตัวอย่าง: Load & Explore (ต่อ)

```python
# ดูความสัมพันธ์กับ target
df.corr()["MedHouseVal"].sort_values(ascending=False)
```

**ผลลัพธ์:** `MedInc` มีความสัมพันธ์สูงที่สุดกับราคาบ้าน ($r \approx 0.69$)

---

## โค้ดตัวอย่าง: Train & Evaluate

```python
from sklearn.linear_model import LinearRegression
from sklearn.model_selection import train_test_split
from sklearn.metrics import mean_absolute_error, root_mean_squared_error, r2_score
from sklearn.preprocessing import StandardScaler

X, y = df.drop("MedHouseVal", axis=1), df["MedHouseVal"]
X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.2, random_state=42)

scaler = StandardScaler()
X_train_s = scaler.fit_transform(X_train)
X_test_s  = scaler.transform(X_test)


```
---

## โค้ดตัวอย่าง: Train & Evaluate

```python
model = LinearRegression().fit(X_train_s, y_train)
y_pred = model.predict(X_test_s)

print(f"R²   = {r2_score(y_test, y_pred):.3f}")
print(f"MAE  = {mean_absolute_error(y_test, y_pred):.3f}")
print(f"RMSE = {root_mean_squared_error(y_test, y_pred):.3f}")
```
---

<!-- _class: divider -->

## 05
## การประเมินผลโมเดล

Evaluation Metrics & Residual Analysis

---

## Regression Metrics

| Metric | สูตร | ความหมาย | ช่วงค่า |
|---|---|---|---|
| **MAE** | $\frac{1}{n}\sum|y_i - \hat{y}_i|$ | ความผิดพลาดเฉลี่ย | $[0, \infty)$ ↓ ดี |
| **RMSE** | $\sqrt{\frac{1}{n}\sum(y_i-\hat{y}_i)^2}$ | ลงโทษ outlier มากกว่า | $[0, \infty)$ ↓ ดี |
| **$R^2$** | $1 - \frac{\text{RSS}}{\text{TSS}}$ | สัดส่วน variance ที่อธิบายได้ | $(-\infty, 1]$ ↑ ดี |

### ผลลัพธ์จาก California Housing

| Metric | Train | Test |
|---|---|---|
| $R^2$ | 0.613 | 0.606 |
| RMSE | 0.726 | 0.727 |

---

## Residual Analysis

### Residual Plot ที่ดี vs. ไม่ดี

```python
import matplotlib.pyplot as plt
residuals = y_test - y_pred

fig, axes = plt.subplots(1, 2, figsize=(12, 4))

# Residuals vs Fitted
axes[0].scatter(y_pred, residuals, alpha=0.3, s=10)
axes[0].axhline(0, color='red', linestyle='--')
axes[0].set(xlabel='Predicted', ylabel='Residuals', title='Residuals vs Fitted')
```

---
```python

# Distribution of residuals
axes[1].hist(residuals, bins=50, edgecolor='white')
axes[1].set(xlabel='Residuals', title='Distribution of Residuals')

plt.tight_layout()
```
> **Residuals ที่ดี:** กระจายสม่ำเสมอรอบศูนย์ — ไม่มีรูปแบบ (pattern)

---

<!-- _class: divider -->

## 06
## แบบฝึกหัดและ Workshop

Exercise & Hands-on

---

<!-- _class: highlight -->

## แบบฝึกหัด 3.1 — Simple Linear Regression

### โจทย์

ให้นักศึกษาสร้าง Simple Linear Regression โดยใช้ **เฉพาะ `MedInc`** เป็น feature เดียว เพื่อทำนาย `MedHouseVal`

1. Split data (80/20), ไม่ต้อง scale สำหรับ Simple LR
2. Fit `LinearRegression` และดูค่า `coef_` กับ `intercept_`
3. Plot scatter plot พร้อมเส้น regression และคำนวณ $R^2$ และ RMSE บน test set
4. เปรียบเทียบผลกับ Multiple LR — ต่างกันอย่างไร?

**Dataset:** `sklearn.datasets.fetch_california_housing()`

---

<!-- _class: highlight -->

## แบบฝึกหัด 3.2 — Regularization (ขั้นสูง)

### โจทย์

เปรียบเทียบ Linear Regression กับ **Ridge** และ **Lasso** บน California Housing

1. Import `Ridge`, `Lasso` จาก `sklearn.linear_model`
2. ทดลอง `alpha ∈ {0.01, 0.1, 1, 10, 100}` ด้วย 5-fold CV
3. Plot กราฟ **coefficient path** เมื่อ `alpha` เปลี่ยน
4. เลือก `alpha` ที่ดีที่สุดและรายงาน $R^2$ / RMSE บน test set
5. สังเกต: feature ไหนที่ Lasso ตัดออก (coefficient = 0)?

**Dataset:** California Housing · **Library:** `sklearn`, `matplotlib`

---

<!-- _class: divider -->

## 07
## สรุปและบทเรียนถัดไป

Summary & What's Next

---

## สรุป Module 03

### สิ่งที่เรียนรู้ใน Module นี้

- ✅ หลักการ Linear Regression และการประมาณค่าด้วย OLS
- ✅ สมมติฐาน LINE และวิธีตรวจสอบด้วย Diagnostic Plots
- ✅ Multiple Linear Regression และปัญหา Multicollinearity (VIF)
- ✅ การสร้างโมเดลด้วย scikit-learn บน California Housing Dataset
- ✅ ประเมินโมเดลด้วย $R^2$, MAE, RMSE และ Residual Analysis

### Module ถัดไป

**Module 04 — Logistic Regression & Classification**
เมื่อ target เป็น categorical — Binary classification, Sigmoid function, Confusion Matrix

---

## แหล่งอ้างอิงและอ่านเพิ่มเติม

### อ่านเพิ่มเติม

- James et al. (2023). *Introduction to Statistical Learning with Python* — Ch. 3
- Géron (2022). *Hands-On Machine Learning with Scikit-Learn* — Ch. 4
- Montgomery et al. (2021). *Introduction to Linear Regression Analysis* — Ch. 2–3

### แหล่งข้อมูลออนไลน์

- [scikit-learn: LinearRegression](https://scikit-learn.org/stable/modules/generated/sklearn.linear_model.LinearRegression.html)
- [Statquest: Linear Regression](https://www.youtube.com/watch?v=nk2CQITm_eo)
- [California Housing Dataset](https://scikit-learn.org/stable/datasets/real_world.html#california-housing-dataset)

---

<!-- _class: lead -->

# คำถาม?

<div class="subtitle">Questions & Discussion</div>

**ผศ.ดร. ทวีศักดิ์ สมานชื่น**
taweesak.sam@mahidol.ac.th

คณะวิศวกรรมศาสตร์ · มหาวิทยาลัยมหิดล

*สไลด์และ notebook: github.com/bdi-mahidol/BDAT501*
