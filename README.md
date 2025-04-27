Flask Kubernetes Deployment with Minikube
This project demonstrates how to containerize a Flask application and deploy it on a local Kubernetes cluster using Minikube.

Prerequisites
Docker

Minikube

kubectl

Project Structure
flask-kubernetes/  
├── app/  
│   ├── app.py                 # Flask application  
│   └── requirements.txt       # Python dependencies  
├── kubernetes/  
│   ├── deployment.yaml        # Kubernetes Deployment  
│   └── service.yaml           # Kubernetes Service  
└── Dockerfile                 # Docker configuration  
Quick Start
1. Build the Docker Image
bash
docker build -t flask-kubernetes .  
2. Start Minikube
bash
minikube start --driver=docker  
3. Deploy to Kubernetes
bash
kubectl apply -f kubernetes/deployment.yaml  
kubectl apply -f kubernetes/service.yaml  
4. Access the Application
bash
minikube service flask-service  
This will open the Flask app in your default browser.

Troubleshooting
Check Pod Status
bash
kubectl get pods  
View Pod Logs
bash
kubectl logs <pod-name>  
Delete & Redeploy
bash
kubectl delete -f kubernetes/deployment.yaml -f kubernetes/service.yaml  
kubectl apply -f kubernetes/deployment.yaml -f kubernetes/service.yaml  
Files Explained
1. app/app.py
A simple Flask application:

python
from flask import Flask  
app = Flask(__name__)  

@app.route('/')  
def home():  
    return "Hello from Flask on Kubernetes!"  

if __name__ == '__main__':  
    app.run(host='0.0.0.0', port=5000)  
2. Dockerfile
dockerfile
FROM python:3.8-slim  
WORKDIR /app  
COPY app/requirements.txt .  
RUN pip install -r requirements.txt  
COPY app/ .  
EXPOSE 5000  
CMD ["python", "app.py"]  
3. kubernetes/deployment.yaml
yaml
apiVersion: apps/v1  
kind: Deployment  
metadata:  
  name: flask-app  
spec:  
  replicas: 1  
  selector:  
    matchLabels:  
      app: flask-app  
  template:  
    metadata:  
      labels:  
        app: flask-app  
    spec:  
      containers:  
      - name: flask-app  
        image: flask-kubernetes  
        imagePullPolicy: Never  
        ports:  
        - containerPort: 5000  
4. kubernetes/service.yaml
yaml
apiVersion: v1  
kind: Service  
metadata:  
  name: flask-service  
spec:  
  selector:  
    app: flask-app  
  ports:  
    - protocol: TCP  
      port: 80  
      targetPort: 5000  
  type: LoadBalancer  
