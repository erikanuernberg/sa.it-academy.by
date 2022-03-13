### 12. Kubernetes. First deployment
## Main task (Screenshots:
![image](https://github.com/Buharevich/sa.it-academy.by/blob/md-sa2-19-22/Vlad_Buharevich/12.%20Kubernetes.%20First%20deployment/Screenshots/app.k8s-4.sa.png?raw=true)
![image](https://github.com/Buharevich/sa.it-academy.by/blob/md-sa2-19-22/Vlad_Buharevich/12.%20Kubernetes.%20First%20deployment/Screenshots/app.k8s-3.sa.png?raw=true)

##Config:
```bash
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: webserver
  labels:
    app: simple-web
spec:
  replicas: 3
  selector:
    matchLabels:
      app: simple-web
  template:
    metadata:
      labels:
        app: simple-web
    spec:
      containers:
      - name: nginx-server
        image: nginx
        ports:
        - containerPort: 80
        resources:
          requests:
            cpu: 50m
            memory: 50Mi
          limits:
            cpu: 100m
            memory: 100Mi
        volumeMounts:
        - name: index-config-mount
          mountPath: /usr/share/nginx/html/
      volumes:
      - name: index-config-mount
        configMap:
          name: index-config
---
apiVersion: v1
kind: Service
metadata:
  name: simple-web-service
  labels:
    run: simple-web-service
spec:
  ports:
  - port: 80
    protocol: TCP
  selector:
    app: simple-web
---
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: ingress-sa
  annotations:
    kubernetes.io/ingress.class: nginx
    nginx.ingress.kubernetes.io/server-alias: "app.k8s-4.sa"
spec:
  rules:
    - host: app.k8s-3.sa
      http:
        paths:
          - path: /
            pathType: Prefix
            backend:
              service:
                name: simple-web-service
                port:
                  number: 80
---
apiVersion: v1
kind: ConfigMap
metadata:
  name: index-config
data:
  index.html: |
    <html>
    <head><title>Kubernates nginx</title></head>
    <body>Hello from Kubernates nginx</body>
    </html>
```