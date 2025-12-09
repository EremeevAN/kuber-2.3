### Домашнее задание к занятию «Настройка приложений и управление доступом в Kubernetes»
### Задание 1: Работа с ConfigMaps
Создать Deployment с двумя контейнерами
<details><summary>deployment.yaml</summary>

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginxmultitool
  annotations:
    container1: nginx
    container2: multitools
spec:
  replicas: 1
  strategy:
    rollingUpdate:
      maxSurge: 1
      maxUnavailable: 1
    type: RollingUpdate
  selector:
    matchLabels:
      app: nginx-multitool
  template:
    metadata:
      labels:
        app: nginx-multitool
    spec:
      containers:
        - name: nginx
          image: nginx:latest
          ports:
            - name: web
              containerPort: 80
          volumeMounts:
          - name: nginx-config
            mountPath: "/usr/share/nginx/html"
        - name: multitool
          image: praqma/network-multitool:alpine-extra
          resources:
          env:
            - name: HTTP_PORT
              value: "1181"
            - name: HTTPS_PORT
              value: "1443"
          ports:
            - name: http
              containerPort: 1181
              protocol: TCP
            - name: https
              containerPort: 1443
              protocol: TCP
      volumes:
      - name: nginx-config
        configMap:
          name: web
```
</details>
Подключить веб-страницу через ConfigMap

<details><summary>configmap-web.yaml</summary>

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: web
data:
  index.html: |
    <!DOCTYPE html>
    <html>
    <head>
      <title>Cтраница с Котиком</title>
    </head>
    <body>
      <h1>./\…/\
        (.‘•..•’.)
          ..=*=..
        (.\.||./.)~~** Тут живет котик</h1>
    </body>
    </html>
```
</details>
Проверить доступность

![image](https://github.com/EremeevAN/kuber-2.3/blob/main/1.png)

### Задание 2: Настройка HTTPS с Secrets
Создать Secret
<details><summary>secret.yaml</summary>

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: secret-tls
type: kubernetes.io/tls
data:
  tls.crt: 
  tls.key: 
```
</details>
Настроить Ingress
<details><summary>ingress.yaml</summary>

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: ingress-ssl
  labels:
    type: ingress
    ssl: enable
    app: nginx
  annotations:
    nginx.ingress.kubernetes.io/rewrite-target: /
spec:
  ingressClassName: "nginx"
  rules:
    - host: home.netology.local
      http:
        paths:
        - path: /
          pathType: Prefix
          backend:
            service:
              name: svc-ng-ssl
              port:
                name: nginx
  tls:
  - hosts:
      - home.netology.local
    secretName: secret-tls
```
</details>

Проверить HTTPS-доступ

![image](https://github.com/EremeevAN/kuber-2.3/blob/main/2.png)

### Задание 3: Настройка RBAC
Создать SSL-сертификат для пользователя

![image](https://github.com/EremeevAN/kuber-2.3/blob/main/3.png)

Создать Role (только просмотр логов и описания подов) и RoleBinding
<details><summary>role-pod-reader.yaml.yaml</summary>

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  name: pod-viewer 
  namespace: default 
rules:
- apiGroups: [""] 
  resources: 
    - pods 
    - pods/log 
  verbs: 
    - get 
    - list 
    - watch 
    - describe 
```
</details>

<details><summary>rolebinding-developer.yaml</summary>

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: pod-view-bind
  namespace: default
subjects:
  - kind: User
    name: user
    apiGroup: rbac.authorization.k8s.io
roleRef:
  kind: Role
  name: pod-viewer
  apiGroup: rbac.authorization.k8s.io
```
</details>
Проверить доступ

![image](https://github.com/EremeevAN/kuber-2.3/blob/main/4.png)

