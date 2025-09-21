# 🚀 MLOps & Deployment: Model Lifecycle  


## 1. Why MLOps?  

Imagine building a **robot chef 🤖🍳**.  
- Training = teaching it recipes.  
- Validation = taste-test with critics.  
- Deployment = open the restaurant.  
- Monitoring = ensure food quality every day.  

👉 Without MLOps → chaos (bad dishes, unhappy customers).  
👉 With MLOps → reproducible, scalable, reliable ML in production.  

---  

## 2. Model Lifecycle Stages 🌀  

```
[Data] → [Training] → [Validation] → [Deployment] → [Monitoring] → back to [Data]
```  

### Stages  
1. **Training**: Build the model using historical data.  
2. **Validation**: Test model on unseen data, tune hyperparams.  
3. **Deployment**: Ship model into production (API, batch, edge).  
4. **Monitoring**: Track drift, errors, performance.  

---  

## 3. Training (Build the Brain 🧠)  

- Input: raw data → pipelines (ETL/ELT).  
- Feature engineering: scaling, encoding, embeddings.  
- Algorithms: deep nets, gradient boosting, etc.  
- Tools: PyTorch Lightning, TensorFlow/Keras, scikit-learn.  

```python
# Example: Train simple classifier
from sklearn.ensemble import RandomForestClassifier
from sklearn.model_selection import train_test_split
from sklearn.metrics import accuracy_score

X, y = ..., ...  # features + labels
X_train, X_val, y_train, y_val = train_test_split(X, y, test_size=0.2)

model = RandomForestClassifier(n_estimators=100)
model.fit(X_train, y_train)
y_pred = model.predict(X_val)
print("Accuracy:", accuracy_score(y_val, y_pred))
```  

---  

## 4. Validation (Taste Test 👨‍🍳)  

- Hold-out test set, cross-validation, A/B testing.  
- Metrics depend on task: accuracy, F1, AUC, perplexity.  
- Hyperparameter optimization: Optuna, Ray Tune.  

```python
# Example: Hyperparameter tuning with Optuna
import optuna

def objective(trial):
    n_estimators = trial.suggest_int("n_estimators", 50, 200)
    max_depth = trial.suggest_int("max_depth", 3, 20)
    model = RandomForestClassifier(n_estimators=n_estimators, max_depth=max_depth)
    model.fit(X_train, y_train)
    return accuracy_score(y_val, model.predict(X_val))

study = optuna.create_study(direction="maximize")
study.optimize(objective, n_trials=30)
```  

---  

## 5. Deployment (Ship the Brain 🚢)  

### Options  
- **Batch**: offline predictions (e.g., nightly fraud scoring).  
- **Real-time API**: Flask/FastAPI/TorchServe/TF Serving.  
- **Edge deployment**: TF Lite, ONNX, CoreML.  

```python
# Example: FastAPI deployment
from fastapi import FastAPI
import joblib

app = FastAPI()
model = joblib.load("model.pkl")

@app.post("/predict")
def predict(features: dict):
    x = [features[f] for f in sorted(features.keys())]
    pred = model.predict([x])
    return {"prediction": int(pred[0])}
```  

👉 Containerize with **Docker** + orchestrate with **Kubernetes** for scaling.  

---  

## 6. Monitoring (Health Check 🩺)  

- **Data Drift**: distribution of input features changes.  
- **Concept Drift**: relationship between X and y changes.  
- **Performance Metrics**: accuracy, latency, resource usage.  
- **Tools**: Prometheus + Grafana, EvidentlyAI, WhyLogs.  

```python
# Example: Monitoring with Evidently
from evidently.report import Report
from evidently.metrics import DataDriftPreset

report = Report(metrics=[DataDriftPreset()])
report.run(reference_data=X_train, current_data=X_val)
report.save_html("drift_report.html")
```  

Alert if drift or performance drops beyond threshold.  

---  

## 7. Text Diagram: Continuous ML Ops Flow  

```
         ┌───────────┐
         │   Data    │
         └─────┬─────┘
               ↓
        ┌───────────────┐
        │   Training    │
        └─────┬─────────┘
               ↓
        ┌───────────────┐
        │  Validation   │
        └─────┬─────────┘
               ↓
        ┌───────────────┐
        │  Deployment   │
        └─────┬─────────┘
               ↓
        ┌───────────────┐
        │  Monitoring   │
        └─────┬─────────┘
               ↓
             (Loop)
```  

---  

## 8. Tooling Landscape 🛠️  

- **Experiment Tracking**: MLflow, Weights & Biases, Neptune.ai  
- **CI/CD**: GitHub Actions, GitLab CI, Jenkins.  
- **Orchestration**: Airflow, Kubeflow, Prefect.  
- **Serving**: TF Serving, TorchServe, BentoML, Seldon Core.  
- **Monitoring**: EvidentlyAI, Prometheus/Grafana, Arize AI.  

---  

## 9. Game Time 🎲  

Q: Your fraud model shows 95% accuracy offline, but in production performance drops to 70%. What likely happened?  

👉 Answer: **Data drift / concept drift** → Monitoring would have caught it.  

---  

## 10. Recap 🎉  

- **Training** = build model from data.  
- **Validation** = test/tune for generalization.  
- **Deployment** = ship model via APIs, batch jobs, or edge devices.  
- **Monitoring** = ensure long-term reliability & fairness.  

⚡ MLOps = continuous feedback loop for reliable ML in production.  
