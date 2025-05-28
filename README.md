
# Python + MySQL App (Docker & Kubernetes with Minikube)

This project demonstrates a simple Python application that connects to a MySQL database. The app is containerized using Docker and deployed using Kubernetes with Minikube.

## 🚀 Features

- Python application that connects to MySQL
- Dockerized application
- MySQL containerized and deployed via Kubernetes
- Local Kubernetes deployment using Minikube

---

## 📁 Project Structure

```
python-mysql-app/
│
├── app.py                      # Python script to connect to MySQL
├── Dockerfile                  # Dockerfile for building the Python app image
├── requirements.txt            # Python dependencies
├── mysql-deployment.yaml      # Kubernetes manifest for MySQL deployment & service
├── python-app-deployment.yaml # Kubernetes manifest for Python app deployment
└── README.md                   # Project documentation
```

---

## ⚙️ Prerequisites

- [Docker](https://www.docker.com/products/docker-desktop/)
- [Minikube](https://minikube.sigs.k8s.io/docs/start/)
- [kubectl](https://kubernetes.io/docs/tasks/tools/)

---

## 🔧 Setup Instructions

### 1. Start Minikube with VirtualBox

```bash
minikube start --driver=virtualbox
```

### 2. Use Minikube's Docker daemon

```bash
minikube docker-env
@FOR /f "tokens=*" %i IN ('minikube -p minikube docker-env --shell cmd') DO @%i
```

### 3. Build the Python Docker image inside Minikube

```bash
docker build -t python-mysql-app .
```

### 4. Deploy MySQL

```bash
kubectl apply -f mysql-deployment.yaml
```

### 5. Deploy the Python Application

> Make sure `imagePullPolicy: Never` is set in `python-app-deployment.yaml`

```bash
kubectl apply -f python-app-deployment.yaml
```

---

## 🔍 Verify the Application

### View Python app logs

```bash
kubectl logs deploy/python-app
```

### Port-forward (if needed)

```bash
kubectl port-forward deployment/python-app 5000:5000
```

Then visit: [http://localhost:5000](http://localhost:5000)

---

## 🐳 Dockerfile (Python App)

```Dockerfile
FROM python:3.9-slim
WORKDIR /app
COPY requirements.txt .
RUN pip install -r requirements.txt
COPY . .
CMD ["python", "app.py"]
```

---

## 🧪 Example Python Script (app.py)

```python
import mysql.connector

config = {
    'host': 'mysql',
    'user': 'testuser',
    'password': 'testpassword',
    'database': 'testdb'
}

try:
    conn = mysql.connector.connect(**config)
    print("✅ Connected to MySQL database!")
except mysql.connector.Error as err:
    print(f"❌ Error: {err}")
```

---

## 📦 Sample `mysql-deployment.yaml`

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: mysql
spec:
  replicas: 1
  selector:
    matchLabels:
      app: mysql
  template:
    metadata:
      labels:
        app: mysql
    spec:
      containers:
      - name: mysql
        image: mysql:latest
        env:
        - name: MYSQL_ROOT_PASSWORD
          value: rootpassword
        - name: MYSQL_DATABASE
          value: testdb
        - name: MYSQL_USER
          value: testuser
        - name: MYSQL_PASSWORD
          value: testpassword
        ports:
        - containerPort: 3306
---
apiVersion: v1
kind: Service
metadata:
  name: mysql
spec:
  selector:
    app: mysql
  ports:
    - port: 3306
      targetPort: 3306
```

---

## 🌐 License

This project is licensed under the MIT License.
