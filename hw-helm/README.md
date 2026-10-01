# Домашнее задание к занятию «Helm» - Шаров Олег

## Цель задания
В тестовой среде Kubernetes необходимо установить и обновить приложения с помощью Helm.

## Чеклист готовности
- [x] Установленное k8s-решение (k3s).
- [x] Установленный локальный kubectl.
- [x] Установленный локальный Helm.
- [x] Редактор YAML-файлов с подключенным репозиторием GitHub.

---

## Задание 1. Подготовить Helm-чарт для приложения

Необходимо упаковать приложение (nginx) в чарт для деплоя в разные окружения. Каждый компонент приложения деплоится отдельным deployment'ом. В переменных чарта настроено изменение образа приложения для изменения версии.

### Структура чарта
![Структура чарта](screenshots/03-chart-structure.png)

### Настройка переменных (values.yaml)
Версия образа задается через переменную `image.tag`.
![values.yaml](screenshots/04-values-yaml.png)

### Шаблон Deployment (deployment.yaml)
Образ подставляется динамически из переменных чарта.
![deployment.yaml](screenshots/05-deployment-yaml.png)

### Валидация чарта
Проверка чарта на ошибки с помощью `helm lint`.
![helm lint](screenshots/06-helm-lint.png)

*Ссылки на полные тексты манифестов:*
- [Chart.yaml](nginx-chart/Chart.yaml)
- [values.yaml](nginx-chart/values.yaml)
- [templates/deployment.yaml](nginx-chart/templates/deployment.yaml)
- [templates/service.yaml](nginx-chart/templates/service.yaml)

---

## Задание 2. Запустить две версии в разных неймспейсах

Запущено три копии приложения с разными версиями образа в двух неймспейсах:
- Версия 1.24 в namespace `app1`
- Версия 1.25 в namespace `app1` (под другим именем релиза)
- Версия 1.26 в namespace `app2`

### 1. Создание неймспейсов
![Создание app1](screenshots/07-create-ns-app1.png)
![Создание app2](screenshots/08-create-ns-app2.png)

### 2. Установка релизов
Установка первой версии (1.24) в неймспейс `app1`:
![Install v1.24](screenshots/09-helm-install-v124.png)

Установка второй версии (1.25) в неймспейс `app1`:
![Install v1.25](screenshots/10-helm-install-v125.png)

Установка третьей версии (1.26) в неймспейс `app2`:
![Install v1.26](screenshots/11-helm-install-v126.png)

### 3. Демонстрация результата
Список релизов в неймспейсе `app1` (два релиза):
![Helm ls app1](screenshots/12-helm-ls-app1.png)

Список релизов в неймспейсе `app2` (один релиз):
![Helm ls app2](screenshots/13-helm-ls-app2.png)

Проверка подов в `app1`:
![Pods app1](screenshots/14-kubectl-pods-app1.png)

Проверка подов в `app2`:
![Pods app2](screenshots/15-kubectl-pods-app2.png)

### 4. Подтверждение версий образов
Проверка, что поды используют правильные версии nginx:

Версии в namespace `app1`:
![Verify images app1](screenshots/16-verify-images-app1.png)

Версия в namespace `app2`:
![Verify images app2](screenshots/17-verify-images-app2.png)

---

## Итог
Задание выполнено:
1. Создан Helm-чарт для nginx с возможностью изменения версии через переменные.
2. Запущены три разных версии приложения в двух неймспейсах.
3. Две версии в одном неймспейсе работают без конфликтов благодаря разным именам релизов Helm.