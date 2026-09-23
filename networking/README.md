# Домашнее задание: Сетевое взаимодействие в Kubernetes - Шаров Олег

## Задание 1: Настройка Service (ClusterIP и NodePort)

### Развернутые манифесты:
- [deployment-multi-container.yaml](deployment-multi-container.yaml)
- [service-clusterip.yaml](service-clusterip.yaml)
- [service-nodeport.yaml](service-nodeport.yaml)

### Доказательства работы:

**1. Поды в статусе Running:**
![01-deployment-pods-running](screenshots/01-deployment-pods-running.png)

**2. Service ClusterIP создан:**
![02-service-clusterip-created](screenshots/02-service-clusterip-created.png)

**3. Проверка доступа изнутри кластера (test-pod):**
![03-curl-test-pod-clusterip](screenshots/03-curl-test-pod-clusterip.png)

**4. Service NodePort создан:**
![04-service-nodeport-created](screenshots/04-service-nodeport-created.png)

**5. Проверка доступа снаружи кластера (curl с хоста):**
![05-curl-nodeport-access](screenshots/05-curl-nodeport-access.png)

---

## Задание 2: Настройка Ingress

*Примечание: В связи с использованием k3s (Traefik) вместо MicroK8s (NGINX), вместо аннотации `nginx.ingress.kubernetes.io/rewrite-target` был создан Traefik Middleware `strip-api-prefix` для корректного удаления префикса `/api` перед отправкой запроса в backend.*

### Развернутые манифесты:
- [deployment-frontend.yaml](deployment-frontend.yaml)
- [deployment-backend.yaml](deployment-backend.yaml)
- [service-frontend.yaml](service-frontend.yaml)
- [service-backend.yaml](service-backend.yaml)
- [traefik-middleware.yaml](traefik-middleware.yaml)
- [ingress.yaml](ingress.yaml)

### Доказательства работы:

**1. Поды frontend и backend в статусе Running:**
![06-deployments-frontend-backend-running](screenshots/06-deployments-frontend-backend-running.png)

**2. Services для frontend и backend созданы:**
![07-services-frontend-backend-created](screenshots/07-services-frontend-backend-created.png)

**3. Ingress-контроллер (Traefik) запущен:**
![08-traefik-ingress-controller-running](screenshots/08-traefik-ingress-controller-running.png)

**4. Ingress ресурс создан:**
![09-ingress-created](screenshots/09-ingress-created.png)

**5. Traefik Middleware создан:**
![11-traefik-middleware-created](screenshots/11-traefik-middleware-created.png)

**6. Финальная проверка маршрутизации (curl / и /api):**
![12-ingress-final-test-success](screenshots/12-ingress-final-test-success.png)