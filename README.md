# 🚀 DevOps End-to-End Project (FULLY AUTOMATED – Jenkins + Docker + Kubernetes)

## 📌 Architecture

GitHub → Jenkins → Docker Build → Docker Push → Kubernetes Deploy → Access via NodePort

---

## 📌 Step 1: Repository Structure (GitHub)

```
repo/
 ├── app/
 │   ├── app.py
 │   └── requirements.txt
 ├── Dockerfile
 ├── deployment.yml
 ├── service.yml
 └── Jenkinsfile
```

---

## 📌 Step 2: Application Code

### app/app.py

```python
from flask import Flask
app = Flask(__name__)

@app.route("/")
def home():
    return "DevOps End-to-End Demo 🚀"

if __name__ == "__main__":
    app.run(host="0.0.0.0", port=5000)
```

### app/requirements.txt

```
flask==2.3.2
```

---

## 📌 Step 3: Dockerfile

```dockerfile
FROM python:3.10-slim

WORKDIR /app

COPY app/requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

COPY app/ .

EXPOSE 5000

CMD ["python3", "app.py"]
```

---

## 📌 Step 4: Kubernetes Deployment

### deployment.yml

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: devops-e2e-local
spec:
  replicas: 2
  selector:
    matchLabels:
      app: devops-app
  template:
    metadata:
      labels:
        app: devops-app
    spec:
      containers:
        - name: devops-container
          image: <your-dockerhub-username>/devsecops-demo:latest
          ports:
            - containerPort: 5000
```

---

### service.yml

```yaml
apiVersion: v1
kind: Service
metadata:
  name: devops-service
spec:
  type: NodePort
  selector:
    app: devops-app
  ports:
    - port: 80
      targetPort: 5000
      nodePort: 30007
```

---

## 📌 Step 5: Jenkinsfile (FULL AUTOMATION)

```groovy
pipeline {
    agent any

    environment {
        IMAGE = "<your-dockerhub-username>/devsecops-demo:latest"
    }

    options {
        skipDefaultCheckout(true)
    }

    stages {

        stage('Checkout') {
            steps {
                git branch: 'feature/devops-e2e-local',
                    url: 'https://github.com/DevOps-Local-Labs/devops-e2e-local.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t $IMAGE .'
            }
        }

        stage('Push Docker Image') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'docker-creds', usernameVariable: 'USER', passwordVariable: 'PASS')]) {
                    sh '''
                    echo $PASS | docker login -u $USER --password-stdin
                    docker push $IMAGE
                    '''
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                sh '''
                kubectl apply -f deployment.yml
                kubectl apply -f service.yml
                '''
            }
        }
    }
}
```

---

## 📌 Step 6: One-Time Setup (ONLY ONCE)

### Give Jenkins access to Kubernetes

```bash
sudo mkdir -p /var/lib/jenkins/.kube
sudo cp /root/.kube/config /var/lib/jenkins/.kube/config
sudo chown -R jenkins:jenkins /var/lib/jenkins/.kube
```

---

## 📌 Step 7: Trigger Pipeline

👉 Run Jenkins Job

Pipeline will:

✔ Pull code  
✔ Build Docker image  
✔ Push to DockerHub  
✔ Deploy to Kubernetes

---

## 📌 Step 8: Verify Deployment

```bash
kubectl get pods
kubectl get svc
```

---

## 📌 Step 9: Access Application (FINAL)

```
http://<VM-IP>:30007
```

Example:

```
http://192.168.56.51:30007
```

---

## 🎯 FINAL FLOW

```
Code Push → Jenkins Trigger → Build → Push → Deploy → Access App
```

---

## 💡 Key DevOps Concepts Covered

- CI/CD Pipeline
- Docker Build & Push
- Kubernetes Deployment
- Service Exposure (NodePort)
- Automation (No manual steps)

---

## 🎓 Trainer Note

Explain clearly:

| Stage    | What Happens               |
| -------- | -------------------------- |
| Checkout | Pulls code                 |
| Build    | Creates Docker image       |
| Push     | Uploads image              |
| Deploy   | Updates Kubernetes         |
| Access   | App available via NodePort |

---

## 🚀 Outcome

Students will understand:

✔ End-to-End DevOps flow  
✔ Real CI/CD pipeline  
✔ Production-like deployment
