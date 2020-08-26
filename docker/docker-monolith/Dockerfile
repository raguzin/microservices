# базовый образ
FROM ubuntu:16.04

# утсановка mongo и ruby
RUN apt-get update
RUN apt-get install -y mongodb-server ruby-full ruby-dev build-essential git
RUN gem install bundler

# Скачаем наше приложение в контейнер
RUN git clone -b monolith https://github.com/express42/reddit.git

# Скопируем файлы конфигурации в контейнер
COPY mongod.conf /etc/mongod.conf
COPY db_config /reddit/db_config
COPY start.sh /start.sh

# установить зависимости приложения и произвести настройку
RUN cd /reddit && bundle install
RUN chmod 0777 /start.sh

# старт сервиса при старте контейнера
CMD ["/start.sh"]