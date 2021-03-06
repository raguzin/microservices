# Docker

docker version - версия клиента и сервера<br>
docker info – информация о текущем состоянии docker daemon<br>

## Управление контейнерами и образами
docker run hello-world - запуск тестового контейнера. Каждый раз запускает новый контейнер. Если не указывать флаг --rm при запуске<br> docker run, то после остановки контейнер вместе с содержимым остается на диске.<br>
docker start <u_container_id> - запускает остановленный (уже созданный) контейнер<br>
docker attach <u_container_id> - подсоединяет терминал к созданному контейнеру<br>
docker create используется, когда не нужно стартовать контейнер сразу<br>
docker run = docker create + docker start + docker attach (при наличии опции -i)<br>
docker exec - Запускает новый процесс внутри контейнера.<br>
$ docker exec -it <u_container_id> bash<br>
docker commit - создает image из контейнера. Контейнер при этом остается запущенным.<br>
$ docker commit <u_container_id> yourname/ubuntu-tmp<br>
docker kill - безусловное завершение. docker kill $(docker ps -q)<br>
docker stop - сигнал остановки приложения<br>
docker rm - удаляет контейнер, можно добавить флаг -f, чтобы удалялся работающий container (будет послан sigkill)<br>
$ docker rm (docker ps -a -q)<br>
docker rmi - удаляет image, если от него не зависят запущенные контейнеры<br>
$ docker rmi $(docker images -q)<br>

• Через параметры передаются лимиты(cpu/mem/disk), ip, volumes<br>
• -i – запускает контейнер в foreground режиме (docker attach)<br>
• -d – запускает контейнер в background режиме<br>
• -t создает TTY<br>

## Информация о контейнерах и образах

docker ps - список запущенных контейнеров<br>
docker ps -a - список всех контейнеров<br>
docker images - список сохранненных образов<br>
docker system df - отображает сколько дискового пространства занято образами, контейнерами и volume’ами. Отображает сколько из них не используется и возможно удалить.<br>

## Создание docker host

docker-machine - встроенный в докер инструмент для создания хостов и установки на них docker engine. Имеет поддержку облаков и систем виртуализации (Virtualbox, GCP и др.)<br>
docker-machine create <имя><br>
docker-machine ls - просмотр созданных хостов.<br>
eval $(docker-machine env <имя>) - переключение между именами.<br>
eval $(docker-machine env --unset) - переключение на локальный докер.<br>
docker-machine rm <имя> - удаление.<br><br>

docker-machine create --driver google --google-machine-image https://www.googleapis.com/compute/v1/projects/ubuntu-os-cloud/global/images/family/ubuntu-1604-lts --google-machine-type n1-standard-1 --google-zone europe-west1-b docker-host --google-project [project-id] <br>

### создание образа
./docker-monolith/ \<br>
Dockerfile - текстовое описание нашего образа<br>
mongod.conf - подготовленный конфиг для mongodb<br>
db_config - содержит переменную окружения со ссылкой на mongodb<br>
start.sh - скрипт запуска приложения<br>

$ docker build -t reddit:latest .<br>
• Точка в конце указывает на путь до Docker-контекста<br>
• Флаг -t задает тег для собранного образа<br><br>

docker run --name reddit -d --network=host reddit:latest - запуск контейнера из созданного образа<br>

### загрузка образа на Docker Hub

$ docker tag reddit:latest <login>/reddit:1.0<br>
$ docker push <login>/otus-reddit:1.0<br><br>

## Проверка

• docker logs reddit -f<br>
• docker inspect <login>/reddit:1.0<br>
• docker inspect <login>/reddit:1.0 -f '{{.ContainerConfig.Cmd}}'<br><br>

## Сети в Docker. Network Drivers

### None

$ docker run --network none<br><br>

• Для контейнера создается свой network namespace<br>
• У контейнера есть только loopback интерфейс<br>
• Сеть контейнера полностью изолирована<br><br>

### Host

$ docker run --network host<br><br>

• Контейнер использует network namespace хоста<br>
• Сеть не управляется самим Docker<br>
• Два сервиса в разных контейнерах не могут слушать один и тот же порт<br>
• Производительность сети контейнера равна производительности сети хоста<br>

### Bridge

$ docker network create NETWORKNAME --driver bridge<br>
$ docker run -d --network=NETWORKNAME<br><br>

• Если нужно отделить контейнер или группу контейнеров<br>
• Контейнер может быть подключен к нескольким Bridge сетям (без рестарта)<br>
• Работает Service Discovery<br>
• Произвольные диапазоны IP-адресов<br><br>

$ docker network create -d bridge --subnet 10.0.0.0/24 my_bridge<br>
$ docker run --name c1 ubuntu<br>
$ docker run —-network my_bridge --name c2 ubuntu<br>
$ docker run —-network my_bridge --name c3 ubuntu<br><br>

$ docker run -P (--publish-all)<br>
• Распознает строки с ключевым словом EXPOSE в Dockerfilе и флаг --expose при запуске контейнера<br>
• Привязывает доступный порт на хосте из диапазона 32768-61000<br>
• Для избежания неявно открытых портов не рекомендуется использовать<br><br>

$ docker run -p PORT (--publish=PORT)<br>
PORT может быть в формате:<br>
• ip_addr:hostPort:containerPort/protoc
• ip_addr:hostPort:containerPort<br>
• hostPort:containerPort<br>
• containerPort (будет работать как -P)<br><br>

default bridge network<br>
• Назначается по умолчанию для контейнеров<br>
• Нельзя вручную назначать IP-адреса<br>
• Нет Service Discovery<br>

### Macvlan

• Работает на основе sub-interfaces Linux<br>
• Более производительный, чем bridge<br>
• Если нужно подключить контейнер к локальной сети<br>
• Поддерживается тегирование VLAN (802.1Q)<br>

### Overlay

• Позволяет объединить в одну сеть контейнеры нескольких Docker хостов<br>
• Работает поверх VXLAN<br>
• Необходимо хранить состояние распределенной сети<br>

# Docker-Compose

• Отдельная утилита<br>
• Декларативное описание Docker инфраструктуры в YAML-формате<br>
• Управление многоконтейнерными приложениями<br><br>

Dockerfile – конфигурация сборки окружения и приложений в нем<br>
docker-compose.yml - конфигурация проекта<br><br>

Один docker-compose.yml на все окружения (под проект или приложение)<br>
Файл docker-compose.override.yml c:<br>
• Произвольными точки монтирования для быстрого изменения кода внутри контейнера<br>
• ENV переменными, характерными для окружения<br>
• Заменой command инструкций для образов<br>
• Перезаписью выводимых наружу портов<br><br>

$ docker-compose up -d<br>
$ docker-compose ps<br>
$ docker-compose down<br>

# Gilab

Добавлен файл .gitlab-ci.yml с описанием инструкций CI по развертыванию и тестированию приложения в Gitlab.<br>
Не закончено.<br>

# Мониторинг

docker/docker-composemonitoring.yml - сервисы мониторинга.<br><br>

Сборка образов производится при помощи скриптов docker_build.sh в директории каждого сервиса.<br>
/src/ui $ bash docker_build.sh<br>
/src/post-py $ bash docker_build.sh<br>
/src/comment $ bash docker_build.sh<br><br>
Или сразу все из корня репозитория:<br>
for i in ui post-py comment; do cd src/$i; bash docker_build.sh; cd -; done<br>

## Prometheus 
prometheus.yml - содержи инструкции для prometheus по мониторингу работы приложения.<br>
Prometheus для прстоты разворачивается также в Docker-контейнере prom/prometheus:v2.1.0.<br><br>

## cAdvisor 
Собирает информацию о ресурсах потребляемых контейнерами и характеристиках их работы.<br><br>

## Grafana
Визуализации данных из Prometheus.<br>

## Alertmanager
Сервис для системы мониторинга Prometheus, который отвечает за первичную обработку алертов и дальнейшую отправку оповещений по заданному назначению.<br>
monitoring/alertmanager/config.yml - конфигурационный файл для отправки нотификаций в тестовый слак канал.<br><br>

# Логирование

docker/docker-compose-logging.yml - сервисы логирования.<br><br>

## EFK elasticsearch/fluentd/kibana
fluentd - инструмент, который используется для отправки, агрегации и парсинга логов сервисов приложения.<br>
logging/fluentd/fluent.conf - файл конфигурации fluentd.<br>
http://dockerhost:5601 - web-интерфейс kibana<br><br>

## Zipkin
- сервис распределенного трейсинга.<br>
http://dockerhost:9411 - web-интерфейс zipkin<br><br>