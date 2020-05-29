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
• docker inspect <login>/reddit:1.0 -f '{{.ContainerConfig.Cmd}}'<br>