# ms-homework 3 - Инфраструктурные паттерны
#### 
- Команды приведены для кластера minikube (windows)
- Команды запускаем из директории kubernetes, где лежат манифесты

#### 1 - Сделаем маппинг arch.homework на IP minikube кластера:
$ `minikube ip`<br/>
hosts: `172.23.222.239 arch.homework `
<br/>

#### 2 - Установим Ingress NGINX через HELM:
$ `kubectl create namespace m && helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx/ && helm repo update && helm install nginx ingress-nginx/ingress-nginx --namespace m -f daemon_set.yaml`
<br/>

#### 3 - Установим PostgreSQL через HELM:
$ `helm repo add bitnami https://charts.bitnami.com/bitnami` <br/>
$ `helm install my-release bitnami/postgresql --set global.postgresql.auth.username=postgres --set global.postgresql.auth.password=pass --set global.postgresql.auth.database=postgres` <br/>

#### 4 - Скопируем в буффук IP сервиса my-release-postgresql:
$ `kubectl get service my-release-postgresql` <br/>
| NAME......................... | CLUSTER-IP | <br/>
| my-release-postgresql | 10.107.6.221 | <br/>

#### 5 - Создадим secrets и вставим в переменную postgres.url IP из буфера обмена:
$ `kubectl create secret generic ms-hw-3-secrets \
--from-literal=postgres.url=jdbc:postgresql://10.107.6.221:5432/postgres \
--from-literal=postgres.user=postgres \
--from-literal=postgres.password=pass`
<br/>
Альтернативный путь - вставить IP в переменную postgres.url, заэнкодить в BASE64, вставить значение в secret.yaml и применить `kubectl apply -f secret.yaml`
<br/>

#### 6 - Запуск манифесты - Config Map, Deployment и NGINX:
$ `kubectl apply -f config_map.yaml` <br/>
$ `kubectl apply -f deployment.yaml` <br/>
$ `kubectl apply -f nginx-ingress.yaml` <br/>
<br/>

#### Из корневой директории newman-ом запускаем postman коллекцию ms-hw-3.postman.json:
`newman run ms-hw-3.postman.json` <br/>
