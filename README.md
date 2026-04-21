# 🚀 Kubernetes Ingress Setup (Kind + NGINX Ingress)

## 📌 Goal

Access application using clean URL:

http://devops.local

Instead of:

http://IP:PORT

---

## 📌 Prerequisites

- Kubernetes cluster (Kind)
- Application already deployed
- Service created (ClusterIP)

---

## 📌 Step 1: Install NGINX Ingress Controller

```bash
kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/main/deploy/static/provider/kind/deploy.yaml
```

---

## 📌 Step 2: Verify Ingress Controller

```bash
kubectl get pods -n ingress-nginx
```

Expected:

```
ingress-nginx-controller   Running
```

---

## 📌 Step 3: Ensure Service is ClusterIP

```yaml
apiVersion: v1
kind: Service
metadata:
  name: devops-service
spec:
  type: ClusterIP
  selector:
    app: devops-e2e-local
  ports:
    - port: 80
      targetPort: 5000
```

Apply:

```bash
kubectl apply -f service.yml
```

---

## 📌 Step 4: Create Ingress Resource

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: devops-ingress
spec:
  ingressClassName: nginx
  rules:
    - host: devops.local
      http:
        paths:
          - path: /
            pathType: Prefix
            backend:
              service:
                name: devops-service
                port:
                  number: 80
```

Apply:

```bash
kubectl apply -f ingress.yml
```

---

## 📌 Step 5: Verify Ingress

```bash
kubectl get ingress
kubectl describe ingress devops-ingress
```

Expected:

- CLASS: nginx
- Host: devops.local
- Backend: devops-service

---

## 📌 Step 6: Expose Ingress (Kind Environment)

Run port-forward:

```bash
kubectl port-forward -n ingress-nginx svc/ingress-nginx-controller 80:80 --address 0.0.0.0
```

---

## 📌 Step 7: Configure Local DNS (Windows)

Edit hosts file:

```
C:\Windows\System32\drivers\etc\hosts
```

Add:

```
192.168.56.51 devops.local
```

---

## 📌 Step 8: Access Application

Open browser:

```
http://devops.local
```

---

## 🎯 Final Architecture

```
Browser → devops.local → Ingress → Service → Pod
```

---

## 💡 Key Concepts

- Ingress provides HTTP routing
- No need for NodePort
- Uses domain-based access
- Requires Ingress Controller (NGINX)

---

## ⚠️ Notes

- In Kind, Ingress requires port-forward (or port mapping)
- Always specify:

  ingressClassName: nginx

- Without ingressClassName → routing will not work

---

## 🚀 Outcome

✔ Clean URL access  
✔ Production-style routing  
✔ No ports exposed  
✔ Real DevOps architecture
