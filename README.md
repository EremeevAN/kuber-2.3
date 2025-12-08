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
  tls.crt: LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSURIVENDQWdXZ0F3SUJBZ0lVUVlyQUM0Z1FRQU55Snp1TVJvQXdMZHR6QkVJd0RRWUpLb1pJaHZjTkFRRUwKQlFBd0hqRWNNQm9HQTFVRUF3d1RhRzl0WlM1dVpYUnZiRzluZVM1c2IyTmhiREFlRncweU5URXlNRGd5TVRVMwpORGRhRncweU5qRXlNRGd5TVRVM05EZGFNQjR4SERBYUJnTlZCQU1NRTJodmJXVXVibVYwYjJ4dloza3ViRzlqCllXd3dnZ0VpTUEwR0NTcUdTSWIzRFFFQkFRVUFBNElCRHdBd2dnRUtBb0lCQVFEWTZSSTltbXhSQmR0UGdjK3EKQzg0RFdBZjM5VlBoT0tjci9pNkd6cmN3d2xTKzFZNTN2Zk54VjI2SzBoN3o2N2VGV0xrbUM1Qllic01zM3pNaQpuRXlRUCt5T0o2c1pLUGhFVnlLcVQrOWthTE56S3lEbjZ0am1EajBXWngvRkdEUThCYVZtOThicTZ0WjBsN0NlCmVuQWNlWncrLzlTV3o4N2QvNGoyRzFzVUdtMG1kbTB4eHdOYWtpQVYzWmJ0VG1QQ29OMHNFeUMwQjhjM2t5SDcKblJXcWNBdUtaK1NKd3ErZmRpa3MzNVBsSk4xZkYzd0RpVndudUlaT0xuZDVDd1FRMkhydm8yeGtYTGxwS29VeApFczVIb2VJc1drRDlBd1JUQkd1ZUJmSmhld2lSVVRKU1k1ZzZaTVFEMlZqbVU4UkRkdVVGNHAvUXhOQ0kxbzl4CkVzZUJBZ01CQUFHalV6QlJNQjBHQTFVZERnUVdCQlNXT3JXRjgrTmdBSDArd3Zla2RvdzVMSWkvakRBZkJnTlYKSFNNRUdEQVdnQlNXT3JXRjgrTmdBSDArd3Zla2RvdzVMSWkvakRBUEJnTlZIUk1CQWY4RUJUQURBUUgvTUEwRwpDU3FHU0liM0RRRUJDd1VBQTRJQkFRQ2tPMGxPSFRveVhoV0dwbGxkaXV3NXp4ZjNROWtnZW9BNnladWZXd08zCjg4TDFvV2pBaUcrb2FGdUNsbitzS0NHMnE0UzY1OXRTMXE5cVhZallOMmdhaXRJL0dlcms3TWpGYWREeE1tWHoKWnJyaXV5UnBuVmdsb0xVdDRDZEdhZStpTnp2cmxMNFMyRzh5c3FrbDhsRVZlVnVlQ3VjcGpNT2htemxpR1ExRwp4TkIweWUyM2RGaHdHdzJHbHVTcFRjZlhleVMwVnc1cmp2b0tSTkFRQmlxK1liblhwRytWUDlCNVAvbDMvK0J5CnVjemxyRXBmd29rVlVYeW1qenIzSTJ3OVE1Y2IwZGVRd2ozMDVNeGduMEhKd3RwQU1FWklqQmRNVmhQUCt3a0QKc285NlNqY1RLMjhIQnJQV3ZiRVBEN053YXVZV2NqUzgzQzJkTXFNTFJiZDkKLS0tLS1FTkQgQ0VSVElGSUNBVEUtLS0tLQo=
  tls.key: LS0tLS1CRUdJTiBQUklWQVRFIEtFWS0tLS0tCk1JSUV2Z0lCQURBTkJna3Foa2lHOXcwQkFRRUZBQVNDQktnd2dnU2tBZ0VBQW9JQkFRRFk2Ukk5bW14UkJkdFAKZ2MrcUM4NERXQWYzOVZQaE9LY3IvaTZHenJjd3dsUysxWTUzdmZOeFYyNkswaDd6NjdlRldMa21DNUJZYnNNcwozek1pbkV5UVAreU9KNnNaS1BoRVZ5S3FUKzlrYUxOekt5RG42dGptRGowV1p4L0ZHRFE4QmFWbTk4YnE2dFowCmw3Q2VlbkFjZVp3Ky85U1d6ODdkLzRqMkcxc1VHbTBtZG0weHh3TmFraUFWM1pidFRtUENvTjBzRXlDMEI4YzMKa3lIN25SV3FjQXVLWitTSndxK2ZkaWtzMzVQbEpOMWZGM3dEaVZ3bnVJWk9MbmQ1Q3dRUTJIcnZvMnhrWExscApLb1V4RXM1SG9lSXNXa0Q5QXdSVEJHdWVCZkpoZXdpUlVUSlNZNWc2Wk1RRDJWam1VOFJEZHVVRjRwL1F4TkNJCjFvOXhFc2VCQWdNQkFBRUNnZ0VCQUpNL1BSTzJwYnNtMTZjVWZ5MTNVQVd6RjgyNnE1TmppeEJ4UDVKaDk3ZlEKNUFpY0hsZXBDZjZ5RktlR1ZsN05jRXJFWFFPM3EraHNZSEF3b0p6cEw0eG82S1BqeCtHUGoyR05LVW9tYzJOZApOQnlGZFpRYU45Mk1ZdW0zWGJqRllvZ3dhUVVLUk8ycU42VDBhSUJjcTRpWkFYays0RWowandEaS9FM2RhaW10Ck9yMmlXS3ZjY0wxTkRYMTZnekFDQ3l0UDZFV3NYWkpjUlhlVVE5bSs2aWNyWlViUmpKZ3BrOFNobGIramRpNDYKUU4rNWpyenJOMjZPRlExdmVPTUtkNERkR3pGYTllbGRiQVhob2pzOU42Wmg2Q2Z4dTRldk9yRDNxUDBXaEJLaQpxa1ZHaTYzVEh2NTcwdmVSTTg3c1ovdDBNSU16Z3NqSlJWMGd3MUlIa3ZVQ2dZRUE4eWFCK1A0cXRxb2xnWUZUCjQxQm43Z0hXaVBvYnhDVk5lSjViOHBIcjdPbXBEbkEvQTlkMGdUV0lLZzNxd0l2Mk1jZDdmSjB4L1R2eHBlcW0KM0tpclMvVFFZQ0ZUaklqQUxwbG1ZRXQwbWJnV1M4bWM3Rm5nK0FvV1hVb1Z2blkzSEdXMjQ2VXRNcHB2TnI4awpQK2RaVHhETjI0U0JzNEEwSVFJa0JEeXFOQ01DZ1lFQTVGK1NqTGFqdHB2WkxoT0JSNExLdmFadzk5TXR5NUJoClVDV3g5ZlR3cFczeW50WExickNJT0FpZUtucFBMM0RkUGRlR1h0OEpXOUNtQVQ0U2lIWDBwQ3pJMHpuekpMcDMKRjJvYnNEZ1l1Q09uN2dBZnkxYkFzaHZ6NjNibDZjODBGT2tla3dqbFU1OURrNXp4R3pwWmFpZ0ZEYUVBOUVENwpmcTBiTUFrdTdnc0NnWUVBMUFIMGU5MXA4dUdDV0Z3aWYrWmc2RWJULzVWTVZvZEwrR3Jqc3lxR3NvaTk3aWZ5CjJlK24xdTJOTDFYNUpUQWtWeDBmVC9Wa1cvQkRjQzZjbFhQQUFEZVM1TzdLQVpSUE5aSnRrSExhVlJvTTNzSUoKUUkvUnQ1UmNFYldDSmhLL1ZOUmZWamgwbzFYQ3VOS0swZWx2bHFBSlRtbElDZkkwQWIvekZYcXIwVThDZ1lCTgpzWllSK1RESk4wd3p1TDhLclJ4OFdOdWw5RnBvSHI1OG5kWmxidWRQNEkvaUthb0VCbHJSZFYwWjVuSjZHVk9yCmJsOXdkMENmMCtRbUdCQUdETnNsMzNhVEpldnFXdVdaT2FnaDAzUFZjWXY0RkdLOHNzN3J5VWE2bk1DclFxcmQKVWxIc2crSkJDTFhjeWsvY2k0VlA4RDJIM1hhTm9tM3RNc2RGR0ZxMjhRS0JnQUp0UkJySElSNE9QMmRwSTI0TQpsV3B0WVYwU1UxZGw2bzdYNW0ySENlaVR5OEUwemE5aVgwa2FjbTVIY0Z5Tk5USFFFbHVsVlF6TkhqU3Y2THl0CkFFbkdiZ0lUQWl5TWI3UEtwU21tUHlyNU1EUE5iQ0VsOTAwcEZTWjEzVXJsQTYvbzRIWW41ZEVCelBTeVBHNTcKM2xqaG54NjJqY2ZCUzFsbVU3LzAzb0Y5Ci0tLS0tRU5EIFBSSVZBVEUgS0VZLS0tLS0K
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
