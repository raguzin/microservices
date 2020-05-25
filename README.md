# Docker

docker version - версия клиента и сервера
docker info – информация о текущем состоянии docker daemon

## Управление контейнерами и образами
docker run hello-world - запуск тестового контейнера. Каждый раз запускает новый контейнер. Если не указывать флаг --rm при запуске docker run, то после остановки контейнер вместе с содержимым остается на диске.
docker start <u_container_id> - запускает остановленный (уже созданный) контейнер
docker attach <u_container_id> - подсоединяет терминал к созданному контейнеру
docker create используется, когда не нужно стартовать контейнер сразу
docker run = docker create + docker start + docker attach*
* при наличии опции -i
docker exec - Запускает новый процесс внутри контейнера.
$ docker exec -it <u_container_id> bash
docker commit - создает image из контейнера. Контейнер при этом остается запущенным.
$ docker commit <u_container_id> yourname/ubuntu-tmp
docker kill - безусловное завершение. docker kill $(docker ps -q)
docker stop - сигнал остановки приложения
docker rm - удаляет контейнер, можно добавить флаг -f, чтобы удалялся работающий container (будет послан sigkill)
$ docker rm (docker ps -a -q)
docker rmi - удаляет image, если от него не зависят запущенные контейнеры
$ docker rmi $(docker images -q)

• Через параметры передаются лимиты(cpu/mem/disk), ip, volumes
• -i – запускает контейнер в foreground режиме (docker attach)
• -d – запускает контейнер в background режиме
• -t создает TTY

## Информация о контейнерах и образах
docker ps - список запущенных контейнеров
docker ps -a - список всех контейнеров
docker images - список сохранненных образов
docker system df - отображает сколько дискового пространства занято образами, контейнерами и volume’ами. Отображает сколько из них не используется и возможно удалить.