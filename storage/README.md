# Домашнее задание: Хранение в K8s - Шаров Олег

## Задание 1. Volume: обмен данными между контейнерами в поде

### Описание
Создан Deployment с двумя контейнерами (busybox и multitool), обменивающимися данными через emptyDir volume.

- **busybox (writer)**: записывает текущую дату в файл `/shared/data.txt` каждые 5 секунд
- **multitool (reader)**: читает файл в реальном времени через `tail -f`

### Манифест
[containers-data-exchange.yaml](containers-data-exchange.yaml)

### Скриншоты

**Рисунок 1:** Статус пода — оба контейнера запущены (2/2 Ready)
![Pod Status](screenshots/01-data-exchange-pod-running.png)

**Рисунок 2:** Демонстрация обмена данными — вывод команды `tail -f`
![File Exchange](screenshots/02-tail-f-data-exchange.png)

**Рисунок 3:** Описание пода — информация о контейнерах и монтировании volumes
![Describe Pod Part 1](screenshots/03-describe-pod-containers.png)

**Рисунок 4:** Описание пода — информация о volumes (EmptyDir) и событиях
![Describe Pod Part 2](screenshots/04-describe-pod-volumes-events.png)

---

## Задание 2. PV, PVC

### Описание
Создан PersistentVolume (PV) с использованием `hostPath` на локальной ноде и PersistentVolumeClaim (PVC) для его использования в поде.

**Ключевые параметры:**
- `persistentVolumeReclaimPolicy: Retain` — данные сохраняются после удаления PVC
- `storageClassName: ""` — ручная привязка PV и PVC без использования StorageClass

### Манифест
[pv-pvc.yaml](pv-pvc.yaml)

### Скриншоты

**Рисунок 5:** Поды запущены и используют PVC
![PVC Pods Running](screenshots/05-pvc-pods-running.png)

**Рисунок 6:** Удаление Deployment и PVC
![Delete Deployment PVC](screenshots/06-delete-deployment-pvc.png)

**Рисунок 7:** PV и PVC связаны (статус Bound)
![PV PVC Bound](screenshots/07-pv-pvc-bound.png)

**Рисунок 8:** Чтение данных из хранилища
![Tail F Storage](screenshots/08-tail-f-storage-data.png)

**Рисунок 9:** PV перешел в статус Released после удаления PVC
![PV Released](screenshots/09-pv-released-after-pvc-delete.png)

**Рисунок 10:** Файл сохранился на диске ноды после удаления PVC
![File On Host After PVC Delete](screenshots/10-file-on-host-after-pvc-delete.png)

**Рисунок 11:** Файл остался на диске после удаления PV
![File On Host After PV Delete](screenshots/11-file-on-host-after-pv-delete.png)

### Пояснения

**Почему PV перешел в статус `Released` после удаления PVC?**

В манифесте PV указано `persistentVolumeReclaimPolicy: Retain`. Эта политика означает, что при удалении PVC сам PV не удаляется автоматически, а переходит в статус `Released`, сохраняя данные на диске. Это необходимо для того, чтобы администратор кластера мог вручную решить, что делать с данными (например, перепривязать PV к другому PVC или удалить его).

**Почему файл `data.txt` остался на диске после удаления PV?**

PV с типом `hostPath` — это не самостоятельное хранилище, а абстракция Kubernetes, которая ссылается на реальную директорию на ноде (`/home/oleg/github/k8s-pv-data/`). Удаление объекта PV из Kubernetes удаляет только метаданные в etcd, но не трогает файлы на хосте. Данные сохраняются до тех пор, пока их не удалит администратор вручную.

---

## Задание 3. StorageClass

### Описание
Создан собственный StorageClass `local-sc` для работы с локальным хранилищем. В отличие от Задания 2, здесь PV и PVC связываются через указание `storageClassName`.

**Параметры StorageClass:**
- `provisioner: kubernetes.io/no-provisioner` — ручное создание PV
- `volumeBindingMode: WaitForFirstConsumer` — PV привязывается только при создании пода

### Манифест
[sc.yaml](sc.yaml)

### Скриншоты

**Рисунок 12:** StorageClass, PV и PVC созданы и связаны
![StorageClass PV PVC Bound](screenshots/12-storageclass-pv-pvc-bound.png)

**Рисунок 13:** Поды запущены с использованием StorageClass
![SC Pods Running](screenshots/13-sc-pods-running.png)

**Рисунок 14:** Чтение данных из хранилища
![SC Tail F Data](screenshots/14-sc-tail-f-data.png)

**Рисунок 15:** Файл создан на диске ноды
![SC File On Host](screenshots/15-sc-file-on-host.png)

---

## Итоги

В ходе выполнения домашнего задания были освоены:
1. **EmptyDir volumes** — временное хранилище для обмена данными между контейнерами в пределах одного пода
2. **PersistentVolume и PersistentVolumeClaim** — механизм постоянного хранения данных с ручной привязкой
3. **StorageClass** — декларативный способ управления хранилищами через классы

Все три механизма позволяют решать разные задачи: от временного обмена данными до постоянного хранения с различными политиками управления.