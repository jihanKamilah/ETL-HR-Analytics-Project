# From Raw HR Data to Insights (ETL Project)

An end-to-end ETL pipeline that transforms messy HR data into clean, structured, and analysis-ready datasets — and turns them into meaningful business insights.

---

## 📌 Project Background

In many organizations, data exists — but it’s not usable.

HR teams often collect large amounts of employee data:
- hiring records  
- performance evaluations  
- salary information  
- attendance & absences  

But the problem is:
> The data is messy, inconsistent, and stored in a single flat table.

This makes it difficult to answer important business questions like:
- Why do employees leave?
- Does a manager impact performance?
- Is the company truly diverse?
- Which recruitment channels are effective?

This project solves that problem by building a complete **ETL pipeline** and restructuring the data into a scalable format.

---

## ⚙️ What is ETL?

ETL stands for:

- **Extract** → Get the data  
- **Transform** → Clean and reshape it  
- **Load** → Store it for analysis  

Think of it like cooking:

- Extract = buying ingredients 🛒  
- Transform = cooking 🍳  
- Load = plating 🍽️  

Without ETL, your data is just raw ingredients.

---

## 📊 Dataset

This project uses a public HR dataset from Kaggle:
https://www.kaggle.com/datasets/rhuebner/human-resources-data-set

It includes:
- Employee information (name, gender, age)
- Job details (department, position, salary)
- Performance scores & absences
- Hiring & termination dates

And most importantly:
> It’s messy — which makes it perfect for learning.

---

## 🏗️ Data Architecture (Star Schema)

Instead of using one large table, the data is transformed into a **star schema**:

### 🟦 Fact Table
- `fact_employees`

### 🟨 Dimension Tables
- `dim_department`
- `dim_position`
- `dim_salary`
- `dim_manager`
- `dim_recruitment`
- `dim_performance`
- `dim_absence`

### 📊 Analytics Table
- `agg_department`

### Why this matters:
- Reduces redundancy  
- Improves query performance  
- Makes analysis easier and scalable  

---

## 🔄 ETL Pipeline

### 1️⃣ Extract

```python
pd.read_csv()
```

Loads raw data from a CSV file into a DataFrame.

This step is designed to be modular, so the pipeline can easily adapt to other data sources such as APIs, databases, or cloud storage.

---

### 2️⃣ Transform (The Core Step)

This is where the real work happens.

Instead of directly analyzing raw data, we:

#### 🧹 Data Cleaning
- Remove unnecessary spaces in column names  
- Convert date columns into proper datetime format  
- Handle missing values using median (robust to outliers)  

#### 🧠 Feature Engineering
- **Age** → calculated from DOB  
- **Tenure** → number of years an employee has worked  
- **Attrition Flag** → whether an employee has left  

#### 🧪 Data Validation
- Clip unrealistic values (e.g., negative salary, invalid age)  
- Ensure consistency across columns  

#### 🏗️ Data Modeling (Star Schema)
We restructure the dataset into:

- **Fact Table** → core employee data  
- **Dimension Tables** → descriptive attributes:
  - Department  
  - Position  
  - Salary  
  - Manager  
  - Recruitment Source  
  - Performance  
  - Absences  

Each dimension uses **surrogate keys** to reduce redundancy and improve performance.

#### 📊 Data Enrichment
- Salary is segmented into:
  - Low  
  - Medium  
  - High  
Using `qcut()` for balanced distribution.

---

### 3️⃣ Load

```python
df.to_csv(path, index=False)
```

The transformed data is stored into structured folders:

```
data/
├── processed/   # fact & dimension tables
├── analytics/   # aggregated tables
```

Tables containing `"agg"` are automatically categorized as analytics outputs, while others are stored as core processed data.

This ensures the pipeline is:
- organized  
- scalable  
- reusable for future analysis  

---

### 🚀 Why This Pipeline Matters

Instead of working with one large, messy table, we now have:

- structured data  
- reduced redundancy  
- faster queries  
- easier analysis  

In other words:

> We didn’t just clean the data — we made it usable.

## 📊 Insights & Analysis

### 👨‍💼 Does Manager Affect Performance?

To analyze whether managers influence employee performance, we compared performance score distributions across different managers.

```python
df_analysis.groupby('ManagerName')['performance_score'] \
    .value_counts(normalize=True)
```

We also visualized it:

```python
sns.countplot(data=df_analysis, x='ManagerName', hue='performance_score')
```

### 💡 Insight

- Most employees fall into the **"Fully Meets"** category  
- High performers (**"Exceeds"**) are spread across multiple managers  
- Low performance (**"Needs Improvement" / "PIP"**) appears in small numbers  

👉 Conclusion:  
There is **no strong evidence** that a specific manager significantly impacts performance.

---

### 🌍 Diversity Profile

#### Race Distribution

```python
df_analysis['RaceDesc'].value_counts(normalize=True)
```

```python
sns.countplot(data=df_analysis, y='RaceDesc')
```

#### Gender Distribution

```python
df_analysis['gender'].value_counts(normalize=True)
```

```python
df_analysis['gender'].value_counts().plot(kind='pie', autopct='%1.1f%%')
```

### 💡 Insight

- Workforce is **predominantly White (~60%)**  
- Followed by **Black/African American (~26%)** and **Asian (~9%)**  
- Gender distribution is **relatively balanced**  

👉 Conclusion:  
Gender diversity looks healthy, but **racial diversity still has room to improve**.

---

### 📢 Best Recruiting Sources for Diversity

```python
cross = pd.crosstab(
    df_analysis['RecruitmentSource'],
    df_analysis['RaceDesc'],
    normalize='index'
)
```

```python
cross.plot(kind='bar', stacked=True)
```

### 💡 Insight

- **Diversity Job Fair** → highly targeted (100% specific group)  
- **LinkedIn & Indeed** → more balanced diversity sources  
- Some channels → lack diversity entirely  

👉 Conclusion:  
Not all recruitment sources contribute equally to diversity.

---

## 🤖 Bonus: Attrition Prediction

Using **Random Forest Classifier**

### 📊 Model Performance

- **Accuracy:** 71%  
- **Recall (Attrition):** 59%  

### 🔢 Confusion Matrix

```
[[32  9]
 [ 9 13]]
```

### 🔍 Key Drivers

- **Tenure** → most important (~47%)  
- **Salary** → second (~23%)  
- **Absences** → moderate impact  
- **Age** → least impact  

### 💡 Insight

The model captures general patterns in employee behavior, but it is not highly reliable yet — especially for predicting employees who will leave.

---

## ▶️ How to Run

Run the pipeline locally with:

```bash
pip install -r requirements.txt
python src/pipeline.py
```

### 📂 Output

After running, the data will be saved into:

```
data/
├── processed/   # fact & dimension tables
├── analytics/   # aggregated tables
```

---

## 👩‍💻 Created By

Built with curiosity, messy data, and lots of debugging ✨  

**Jihan Kamilah**  
- GitHub: https://github.com/jihanKamilah  
- LinkedIn: https://www.linkedin.com/in/jihan-kamilah/
