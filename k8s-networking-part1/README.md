# Домашнее задание: Сетевое взаимодействие в K8S. Часть 1 - Шаров Олег

## Задание 1. Доступ к контейнерам из другого Pod внутри кластера

### 1. Создание Deployment
Создан Deployment с 3 репликами. Каждый Pod содержит два контейнера: `nginx` (порт 80) и `multitool` (порт 8080).

**Манифест `deployment.yaml`:**
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-app-deployment
spec:
  replicas: 3
  selector:
    matchLabels:
      app: my-app
  template:
    metadata:
      labels:
        app: my-app
    spec:
      containers:
      - name: nginx
        image: nginx:latest
        ports:
        - containerPort: 80
      - name: multitool
        image: wbitt/network-multitool
        env:
        - name: HTTP_PORT
          value: "8080"
        ports:
        - containerPort: 8080
```

![Pod'ы запущены](screenshots/01-deployment-pods-running.png)

### 2. Создание Service (ClusterIP)
Создан Service для маршрутизации трафика на разные порты внутри Pod'а.

**Манифест `service.yaml`:**
```yaml
apiVersion: v1
kind: Service
metadata:
  name: my-app-service
spec:
  selector:
    app: my-app
  ports:
  - name: nginx
    port: 9001
    targetPort: 80
    protocol: TCP
  - name: multitool
    port: 9002
    targetPort: 8080
    protocol: TCP
```

![Service создан](screenshots/02-service-created.png)

### 3. Проверка доступа из другого Pod
Создан тестовый Pod `test-client` для проверки доступности приложения по DNS-имени сервиса.

![Проверка curl из Pod](screenshots/03-curl-test-from-pod.png)

---

## Задание 2. Доступ к приложениям снаружи кластера

### 1. Создание Service (NodePort)
Создан Service типа `NodePort` для открытия доступа к `nginx` снаружи кластера на порту 30080.

**Манифест `service-nodeport.yaml`:**
```yaml
apiVersion: v1
kind: Service
metadata:
  name: my-app-nodeport
spec:
  type: NodePort
  selector:
    app: my-app
  ports:
  - name: nginx
    port: 80
    targetPort: 80
    nodePort: 30080
```

### 2. Проверка доступа снаружи
Доступ проверен с локального компьютера с помощью `curl` и браузера.

![Проверка curl с хоста](screenshots/04-curl-nodeport.png)
![Проверка в браузере](screenshots/05-browser-nodeport.png)