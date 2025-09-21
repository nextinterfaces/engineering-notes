# 🧪 Experiment Tracking: MLflow & Weights & Biases  

## 1. Why Experiment Tracking?  

Imagine you’re a scientist in a kitchen lab 🍳.  
You try 100 recipes with different spices.  
- No notes? You’ll never remember the winning recipe.  
- Good notes? You can reproduce the dish perfectly.  

👉 In ML, tracking = **hyperparameters, metrics, artifacts, code versions**.  

```
[Idea] → [Experiment Run] → [Tracking System] → [Compare & Reproduce]
```  

---  

## 2. What to Track?  

- **Code version** (Git commit).  
- **Hyperparameters** (learning rate, batch size).  
- **Metrics** (accuracy, loss, F1, AUC).  
- **Artifacts** (models, plots, logs).  
- **Environment** (Python version, packages).  

---  

## 3. MLflow – The Open Source All-rounder 🌍  

- Run local or server mode.  
- Components: Tracking, Projects, Models, Registry.  

### Example: Log parameters & metrics  

```python
import mlflow
import mlflow.sklearn
from sklearn.ensemble import RandomForestClassifier
from sklearn.metrics import accuracy_score
from sklearn.model_selection import train_test_split

X, y = ..., ...
X_train, X_val, y_train, y_val = train_test_split(X, y, test_size=0.2)

with mlflow.start_run():
    model = RandomForestClassifier(n_estimators=100, max_depth=5)
    model.fit(X_train, y_train)
    acc = accuracy_score(y_val, model.predict(X_val))

    mlflow.log_param("n_estimators", 100)
    mlflow.log_param("max_depth", 5)
    mlflow.log_metric("accuracy", acc)
    mlflow.sklearn.log_model(model, "model")
```  

- ✅ Easy to set up.  
- ✅ Works with multiple frameworks.  
- ❌ UI less modern than W&B.  

---  

## 4. Weights & Biases (W&B) – The Collaboration Hub 🤝  

- Cloud-first platform.  
- Powerful UI for charts, comparisons, collaboration.  
- Great for teams working remotely.  

### Example: Log experiment with W&B  

```python
import wandb
import random

wandb.init(project="my_experiment", config={"lr": 0.001, "batch_size": 32})

for epoch in range(5):
    acc = random.random()
    loss = random.random()
    wandb.log({"epoch": epoch, "accuracy": acc, "loss": loss})

wandb.finish()
```  

- ✅ Fantastic dashboards.  
- ✅ Easy sweeps (hyperparameter search).  
- ❌ Requires cloud (though local mode exists).  

---  

## 5. Text Diagram: Comparing MLflow & W&B  

```
MLflow → Open-source, self-host, flexible, registry focus  
W&B    → Cloud-native, collaboration, visual dashboards  
```  

---  

## 6. Integration with CI/CD & MLOps  

- **MLflow** integrates well with Kubeflow, Airflow, Jenkins.  
- **W&B** integrates well with HuggingFace, PyTorch Lightning, Keras.  
- Both support APIs for automation & pipelines.  

---  

## 7. Game Time 🎲  

Q: You need to compare 100 hyperparameter runs with your teammate in another country. Which tool is better?  

👉 Answer: **Weights & Biases** (collaboration + cloud).  

---  

## 8. Recap 🎉  

- Tracking ensures **reproducibility** and **visibility**.  
- **MLflow**: open-source, self-host, registry for model lifecycle.  
- **W&B**: cloud-native, great dashboards, team collaboration.  

⚡ Together, they make experiment management robust & reliable.  
