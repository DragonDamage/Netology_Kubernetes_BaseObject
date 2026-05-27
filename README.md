# Домашнее задание к занятию «Базовые объекты K8S»

### Цель задания

В тестовой среде для работы с Kubernetes, установленной в предыдущем ДЗ, необходимо развернуть Pod с приложением и подключиться к нему со своего локального компьютера. 

------

### Чеклист готовности к домашнему заданию

1. Установленное k8s-решение (например, MicroK8S).
2. Установленный локальный kubectl.
3. Редактор YAML-файлов с подключенным Git-репозиторием.

------

### Инструменты и дополнительные материалы, которые пригодятся для выполнения задания

1. Описание [Pod](https://kubernetes.io/docs/concepts/workloads/pods/) и примеры манифестов.
2. Описание [Service](https://kubernetes.io/docs/concepts/services-networking/service/).

------

### Задание 1. Создать Pod с именем hello-world

1. Создать манифест (yaml-конфигурацию) Pod.
2. Использовать image - gcr.io/kubernetes-e2e-test-images/echoserver:2.2.
3. Подключиться локально к Pod с помощью `kubectl port-forward` и вывести значение (curl или в браузере).

------

### Задание 2. Создать Service и подключить его к Pod

1. Создать Pod с именем netology-web.
2. Использовать image — gcr.io/kubernetes-e2e-test-images/echoserver:2.2.
3. Создать Service с именем netology-svc и подключить к netology-web.
4. Подключиться локально к Service с помощью `kubectl port-forward` и вывести значение (curl или в браузере).

------

### Правила приёма работы

1. Домашняя работа оформляется в своем Git-репозитории в файле README.md. Выполненное домашнее задание пришлите ссылкой на .md-файл в вашем репозитории.
2. Файл README.md должен содержать скриншоты вывода команд `kubectl get pods`, а также скриншот результата подключения.
3. Репозиторий должен содержать файлы манифестов и ссылки на них в файле README.md.

------

### Критерии оценки
Зачёт — выполнены все задания, ответы даны в развернутой форме, приложены соответствующие скриншоты и файлы проекта, в выполненных заданиях нет противоречий и нарушения логики.

На доработку — задание выполнено частично или не выполнено, в логике выполнения заданий есть противоречия, существенные недостатки.

# Выполнение заданий

## Задание 1. Создать Pod с именем hello-world

1. Создать манифест (yaml-конфигурацию) Pod.
```bash
root@vm:/home/bogatyrevam/Desktop# nano pod.yml
```
```yml
apiVersion: v1
kind: Pod
metadata:
  labels:
    app: echo
  name: pod-hello-world
  namespace: default
spec:
  containers:
  - image: gcr.io/kubernetes-e2e-test-images/echoserver:2.2
    imagePullPolicy: IfNotPresent
    name: hello-world
```

2. Использовать image - gcr.io/kubernetes-e2e-test-images/echoserver:2.2.
```bash
root@vm:/home/bogatyrevam/Desktop# kubectl apply -f pod.yml
pod/pod-hello-world created
```
``` bash
root@vm:/home/bogatyrevam/Desktop# kubectl get pods
NAME              READY   STATUS    RESTARTS   AGE
pod-hello-world   1/1     Running   0          2m34s
```

3. Подключиться локально к Pod с помощью `kubectl port-forward` и вывести значение (curl или в браузере).
```bash
root@vm:/home/bogatyrevam/Desktop# kubectl port-forward pod/pod-hello-world 30000:8080
Forwarding from 127.0.0.1:30000 -> 8080
Forwarding from [::1]:30000 -> 8080
```
```bash
bogatyrevam@vm:/home/bogatyrevam# curl localhost:30000
```
```bash
Hostname: pod-hello-world

Pod Information:
	-no pod information available-

Server values:
	server_version=nginx: 1.12.2 - lua: 10010

Request Information:
	client_address=127.0.0.1
	method=GET
	real path=/
	query=
	request_version=1.1
	request_scheme=http
	request_uri=http://localhost:8080/

Request Headers:
	accept=*/*  
	host=localhost:30000  
	user-agent=curl/7.81.0  

Request Body:
	-no body in request-
```

<img width="1135" height="752" alt="image" src="https://github.com/user-attachments/assets/8ca1ced1-5545-4da2-8ccb-df26f054f517" />


## Задание 2. Создать Service и подключить его к Pod

1. Создать Pod с именем netology-web.
2. Использовать image — gcr.io/kubernetes-e2e-test-images/echoserver:2.2.
```bash
bogatyrevam@vm:~/Desktop# nano service-web.yml
```
```yml
apiVersion: v1
kind: Pod
metadata:
  labels:
    app: echo
  name: pod-netology-web
  namespace: default
spec:
  containers:
  - image: gcr.io/kubernetes-e2e-test-images/echoserver:2.2
    imagePullPolicy: IfNotPresent
    name: hello-world

---
apiVersion: v1
kind: Service
metadata:
  name: netology-svc
spec:
  ports:
    - name: web
      port: 80
      protocol: TCP
      targetPort: 8080
  selector:
    app: echo
```

3. Создать Service с именем netology-svc и подключить к netology-web.
```bash
root@vm:~/Desktop# microk8s kubectl apply -f service-web.yml
pod/pod-netology-web created
service/netology-svc created
```
```bash
root@vm:/home/bogatyrevam/Desktop# kubectl get pods
NAME               READY   STATUS    RESTARTS   AGE
pod-hello-world    1/1     Running   0          17m
pod-netology-web   1/1     Running   0          117s
```

4. Подключиться локально к Service с помощью `kubectl port-forward` и вывести значение (curl или в браузере).
```bash
root@vm:/home/bogatyrevam/Desktop# kubectl port-forward service/netology-svc 30000:80
```
```bash
Forwarding from 127.0.0.1:30000 -> 8080
Forwarding from [::1]:30000 -> 8080
```

```bash
root@vm:/home/bogatyrevam/Desktop# curl localhost:30000/
```
```bash
Hostname: pod-hello-world

Pod Information:
	-no pod information available-

Server values:
	server_version=nginx: 1.12.2 - lua: 10010

Request Information:
	client_address=127.0.0.1
	method=GET
	real path=/
	query=
	request_version=1.1
	request_scheme=http
	request_uri=http://localhost:8080/

Request Headers:
	accept=*/*  
	host=localhost:30000  
	user-agent=curl/7.81.0  

Request Body:
	-no body in request-
```

<img width="1718" height="928" alt="image" src="https://github.com/user-attachments/assets/b81133a6-0696-46bd-9fb2-17f3821933b1" />
