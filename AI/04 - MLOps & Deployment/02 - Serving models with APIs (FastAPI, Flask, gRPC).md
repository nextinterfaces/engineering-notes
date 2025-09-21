# 🌐 Serving Models with APIs: FastAPI, Flask, gRPC  

## 1. Why Serve Models with APIs?  

Imagine you built a **genius robot brain 🧠**.  
How do others use it?  
- Researchers? Business apps? Mobile apps?  

👉 By wrapping it in an **API** so clients can send data → get predictions.  

```
Client (data) ---> API ---> Model ---> Prediction ---> Response
```  

---  

## 2. Serving Options  

- **Flask** → Lightweight, classic Python web framework.  
- **FastAPI** → Modern, async, type hints, blazing fast 🚀.  
- **gRPC** → High-performance, strongly typed, ideal for microservices.  

---  

## 3. Flask Example (Classic & Simple)  

```python
from flask import Flask, request, jsonify
import joblib

app = Flask(__name__)
model = joblib.load("model.pkl")

@app.route("/predict", methods=["POST"])
def predict():
    data = request.get_json()
    x = [data[f] for f in sorted(data.keys())]
    pred = model.predict([x])
    return jsonify({"prediction": int(pred[0])})

if __name__ == "__main__":
    app.run(host="0.0.0.0", port=5000)
```  

- ✅ Easy to start.  
- ❌ Slower than FastAPI for async or high-load cases.  

---  

## 4. FastAPI Example (Modern & Fast)  

```python
from fastapi import FastAPI
from pydantic import BaseModel
import joblib

app = FastAPI()
model = joblib.load("model.pkl")

class Features(BaseModel):
    f1: float
    f2: float
    f3: float

@app.post("/predict")
def predict(features: Features):
    x = [features.f1, features.f2, features.f3]
    pred = model.predict([x])
    return {"prediction": int(pred[0])}

# Run with: uvicorn app:app --reload --host 0.0.0.0 --port 8000
```  

- ✅ Async, JSON schema, Swagger UI docs auto-generated.  
- ✅ Great for production APIs.  

---  

## 5. gRPC Example (High Performance)  

1. Define service in `.proto` file:  

```proto
syntax = "proto3";

service ModelService {
  rpc Predict (PredictRequest) returns (PredictReply);
}

message PredictRequest {
  repeated float features = 1;
}

message PredictReply {
  float prediction = 1;
}
```  

2. Generate Python stubs:  

```bash
python -m grpc_tools.protoc -I. --python_out=. --grpc_python_out=. model.proto
```  

3. Implement server:  

```python
import grpc
from concurrent import futures
import model_pb2, model_pb2_grpc, joblib

class ModelServicer(model_pb2_grpc.ModelServiceServicer):
    def __init__(self):
        self.model = joblib.load("model.pkl")
    def Predict(self, request, context):
        pred = self.model.predict([list(request.features)])
        return model_pb2.PredictReply(prediction=float(pred[0]))

server = grpc.server(futures.ThreadPoolExecutor(max_workers=10))
model_pb2_grpc.add_ModelServiceServicer_to_server(ModelServicer(), server)
server.add_insecure_port("[::]:50051")
server.start()
server.wait_for_termination()
```  

- ✅ Binary protocol, much faster than JSON.  
- ✅ Strong typing, multi-language support.  
- ❌ More complex than Flask/FastAPI.  

---  

## 6. Text Diagram: API Choices  

```
Flask      → Simple, synchronous REST
FastAPI    → Async, auto-docs, production ready
gRPC       → High-perf, microservices, polyglot
```  

---  

## 7. Deployment Flow 🚀  

1. Package model (pickle, ONNX, TorchScript, TF SavedModel).  
2. Choose serving API framework (Flask, FastAPI, gRPC).  
3. Containerize with Docker.  
4. Deploy with Kubernetes, ECS, or serverless (Cloud Run, Lambda).  
5. Add monitoring/logging.  

---  

## 8. Game Time 🎲  

Q: Your company needs **real-time low-latency predictions** shared with a **Java backend**. Which API style do you pick?  

👉 Answer: **gRPC** (fast, multi-language).  

---  

## 9. Recap 🎉  

- **Flask** = simple REST, good for demos/POCs.  
- **FastAPI** = production-ready REST, async, modern.  
- **gRPC** = high-performance, polyglot, best for microservices.  

⚡ Together, these cover most ML serving scenarios.  
