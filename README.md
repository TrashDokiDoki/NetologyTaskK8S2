# NetologyTaskK8S2

# Домашнее задание 10. Kubernetes. Pods и Services

## Задание 1. Создать Pod с именем `hello-world`

### 1. Создать манифест (YAML-конфигурацию) Pod

Создан манифест `hello-world-pod.yaml`, описывающий Pod с именем `hello-world`.

```
apiVersion: v1
kind: Pod
metadata:
  name: hello-world
  labels:
    app: hello-world
spec:
  containers:
    - name: echoserver
      image: gcr.io/kubernetes-e2e-test-images/echoserver:2.2
      ports:
        - containerPort: 8080
```

Применение манифеста:

```
kubectl apply -f hello-world-pod.yaml
```

[Манифест hello-world-pod.yaml](hello-world-pod.yaml)

### 2. Использовать image — `gcr.io/kubernetes-e2e-test-images/echoserver:2.2`

Для запуска контейнера использован образ `gcr.io/kubernetes-e2e-test-images/echoserver:2.2`.

Проверка состояния Pod:

```
kubectl get pods
```

Pod `hello-world` успешно создан и находится в состоянии `Running`. Контейнер готов к работе (`1/1`).

<img width="442" height="47" alt="image" src="https://github.com/user-attachments/assets/7f0ae3ee-ce96-428d-9877-2778304f2f95" />

### 3. Подключиться локально к Pod с помощью kubectl port-forward и вывести значение

Для организации локального доступа выполнен проброс порта:

```
kubectl port-forward pod/hello-world 8080:8080
```

После этого выполнен HTTP-запрос:

```
curl http://localhost:8080
```

В ответ получена информация от сервера echoserver, включая имя Pod, параметры HTTP-запроса и заголовки.

<img width="589" height="424" alt="image" src="https://github.com/user-attachments/assets/831aacc8-53a5-43b1-85ca-5534f618fe07" />

**Результат:** Pod `hello-world` успешно создан и запущен. Локальное подключение через `kubectl port-forward` работает, HTTP-запрос обработан успешно.

---

## Задание 2. Создать Service и подключить его к Pod

### 1. Создать Pod с именем `netology-web`

Создан манифест `netology-web.yaml`, описывающий Pod с именем `netology-web`.

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: netology-web
  labels:
    app: netology-web
spec:
  containers:
    - name: echoserver
      image: gcr.io/kubernetes-e2e-test-images/echoserver:2.2
      ports:
        - containerPort: 8080
```

Применение манифеста:

```bash
kubectl apply -f netology-web.yaml
```

[Манифест netology-web.yaml](netology-web.yaml)

### 2. Использовать image — `gcr.io/kubernetes-e2e-test-images/echoserver:2.2`

Для запуска контейнера использован указанный в задании образ:

`gcr.io/kubernetes-e2e-test-images/echoserver:2.2`

Проверка состояния Pod:

```
kubectl get pods
```

Оба Pod (`hello-world` и `netology-web`) успешно запущены и находятся в состоянии `Running`. Контейнеры готовы к работе (`1/1`).

<img width="434" height="61" alt="image" src="https://github.com/user-attachments/assets/0b8e256d-a856-4c8f-b4b5-036f5b58c09a" />

### 3. Создать Service с именем `netology-svc` и подключить его к `netology-web`

Создан манифест `netology-svc.yaml`:

```
apiVersion: v1
kind: Service
metadata:
  name: netology-svc
spec:
  selector:
    app: netology-web
  ports:
    - protocol: TCP
      port: 8080
      targetPort: 8080
  type: ClusterIP
```

Связь Service с Pod осуществляется через параметр `selector`, значение которого соответствует метке `app: netology-web`, указанной в манифесте Pod.

Применение манифеста:

```
kubectl apply -f netology-svc.yaml
```

Проверка созданного Service:

```
kubectl get services
```

Дополнительно для проверки связи Service с Pod можно использовать:

```
kubectl get endpoints netology-svc
```

<img width="626" height="59" alt="image" src="https://github.com/user-attachments/assets/c0b35fad-9cea-478c-863f-9f04ed02756f" />


[Манифест netology-svc.yaml](netology-svc.yaml)

### 4. Подключиться локально к Service с помощью kubectl port-forward и вывести значение

Для организации локального подключения к Service выполнен проброс порта:

```
kubectl port-forward service/netology-svc 8081:8080
```

После этого выполнен HTTP-запрос:

```
curl http://localhost:8081
```

В ответ получена информация от сервера echoserver.

В ответе присутствует строка:

```
Hostname: netology-web
```

Это подтверждает, что Service `netology-svc` успешно перенаправляет запросы на Pod `netology-web`.

<img width="597" height="418" alt="image" src="https://github.com/user-attachments/assets/d4c3447d-5d3e-4c7b-9a79-716d0b96a29d" />

**Результат:** Pod `netology-web` успешно создан, Service `netology-svc` настроен и связан с Pod. Локальное подключение через `kubectl port-forward` работает, HTTP-запрос обработан успешно.

---

## Итог

В ходе выполнения домашнего задания:

- Создан Pod `hello-world`.
- Выполнено локальное подключение к Pod через `kubectl port-forward`.
- Создан Pod `netology-web`.
- Создан Service `netology-svc` типа `ClusterIP`.
- Настроена связь Service с Pod через `selector`.
- Проверена доступность приложения через Service с локального компьютера.

Все поставленные задачи выполнены.
