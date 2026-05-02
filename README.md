**Fraud Detection using Machine Learning (Kaggle IEEE-CIS Competition)**

---

## ქომფეთიშენის მოკლე მიმოხილვა

ქომფეთიშენის მიზანია ონლაინ ტრანზაქციებში თაღლითური ოპერაციების გამოვლენა.
მონაცემები მოიცავს:

* `train_transaction`, `test_transaction` — ტრანზაქციების ინფორმაცია
* `train_identity`, `test_identity` — მომხმარებლის იდენტობის დამატებითი ფიჩერები

სამიზნე ცვლადია:

```text
isFraud
```

გამოტენებული მეტრიკა: **ROC-AUC**

---

## მიდგომები


დავატრენინგე შემდეგი მოდელები:

* Logistic Regression
* Random Forest
* AdaBoost
* XGBoost (Final Model)

მიზანი იყო არა მხოლოდ მაღალი შედეგი, არამედ:

სხვადასხვა მიდგომების შედარება
overfitting / underfitting ანალიზი
საინტერესო ექსპერიმენტების დოკუმენტაცია

---

## რეპოზიტორიის სტრუქტურა

```text
ML-fraud-detection/
│
├── model_experiment_logistic_regression.ipynb
├── model_experiment_random_forest.ipynb
├── model_experiment_adaboost.ipynb
├── model_experiment_xgboost.ipynb
│
├── model_inference.ipynb
│
├── README.md
```

---

## ფაილების განმარტება

### model_experiment_logistic_regression.ipynb

* საკმაოდ მძიმე preprocessing
* WOE encoding
* IV feature selection
* scaling
* ბევრი ჰიპერპარამეტრის ტესტირება

---

### model_experiment_random_forest.ipynb

* feature importance
* model complexity ანალიზი
* underfitting vs overfitting

---

### model_experiment_adaboost.ipynb

* boosting მოდელის ტესტირება
* underfitting-ის დემონსტრაცია

---

### model_experiment_xgboost.ipynb

* საბოლოო ძლიერი მოდელი
* feature importance + selection
* hyperparameter tuninგ

---

### model_inference.ipynb

* Model Registry-დან მოდელის ჩატვირთვა
* test set-ზე პროგნოზი
* submission გენერაცია

---

# Feature Engineering

გამოვიყენე რამდენიმე მნიშვნელოვანი ფიჩერი:

### 1. ლოგ ტრანსფორმაცია

რომელიც ამცირებს skew-ს

---

### 2. დროის ფიჩერები

პატერნების დაჭერა რადგან დრო მნიშნვლეოვანი ასპექტია თაღლითობის გამოსავლენად

---

### 3. Missing value count

ზგჯერ მისინგ ვალია შეიძლება განიხილოს როგორც სიგნალი საეჭვო ქმედებისა

---

# კატეგორიული ცვლადების დამუშავება

### გამოყენებული მეთოდი: Frequency Encoding

```text
category → frequency
```

მაგალითად:

```text
chrome → 12000
firefox → 8000
```

 საკმაოდ ეფექტურია high-cardinality features-ისთვისდა  უკეთ მუშაობს ვიდრე label encoding

---

# NaN მნიშვნელობების დამუშავება

გამოყენებული მიდგომები:

* > 90% missing columns → წაშლა
* numeric features → XGBoost-ში NaN შენარჩუნება
* ზოგ მოდელში → fillna(-999)

---

# Cleaning მიდგომები

სვეტების წაშლა მაღალი missing ratio-ს მიხედვით
სვეტებს ქონდა განსხვავებული სახელები და მომიწია ეს (`-` → `_`)
invalid values (inf, -inf) დამუშავება

---

# Feature Selection

გამოვიყენე რამდენიმე მეთოდი:

---

## 1. IV (Information Value) — Logistic Regression

## 2. Feature Importance — Random Forest / XGBoost

## 3. Correlation Filter

---

# Training

## ტესტირებული მოდელები

| მოდელი             | შედეგი | პრობლემა          |
| ------------------- | ------ | ------------------ |
| Logistic Regression | ~0.74  | linear limitations |
| AdaBoost            | ~0.68  | underfitting       |
| Random Forest       | ~0.88  | strong baseline    |
| XGBoost             | ~0.94  | best model         |

---

# ჰიპერპარამერების ოპტიმიზაცია

### Logistic Regression

* C
* penalty
* solver

### Random Forest

* max_depth
* n_estimators
* min_samples_leaf

### AdaBoost

* depth
* learning_rate
* n_estimators

### XGBoost

* max_depth
* learning_rate
* subsample
* colsample_bytree
* min_child_weight
* gamma
* reg_lambda

---

# საბოლოო მოდელის შერჩევა

საუკეთესო მოდელი: XGBoost

### შედეგები:

Validation ROC-AUC: ~0.948
Kaggle Public: 0.9309
Kaggle Private: 0.9053

---

# MLflow Tracking

### ჩაწერილი მეტრიკები

* train_auc
* valid_auc
* auc_gap

---

# Final Pipeline

Pipeline შეიცავს:

→ cleaning
→ feature engineering
→ encoding
→ feature selection
→ XGBoost

---
<img width="1492" height="171" alt="Screenshot 2026-05-02 224925" src="https://github.com/user-attachments/assets/1b727ff9-289c-4bb0-abad-9bdcf1c3a6fd" />
