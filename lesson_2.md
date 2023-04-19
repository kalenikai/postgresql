# Создание докера
Установка ПО докера 
***curl -fsSL https://get.docker.com -o get-docker.sh && sudo sh get-docker.sh && rm get-docker.sh && sudo usermod -aG docker $USER***
По задумке вышеприведенного кода этот кусок ***sudo usermod -aG docker $USER*** должен был исключить использованием ***sudo*** для дальнейшей конфигурации докера, но эьл не сработало.
Создаем сеть net1.

***sudo docker network create pg_net*** 

Просмотреть, что сеть создалась.

***sudo docker network ls***
 
Она есть 

***NETWORK ID     NAME      DRIVER    SCOPE***

***4ce91f53aa90   bridge    bridge    local***

***b4f200d0b20a  host      host      local***

***3da8bade250e   none      null      local***

***732115d94122   pg-net    bridge    local***

Сеть pg_net создана как мост между локальным хостом и докером. Она будет выполнять функции NAT при трансляции адресов пакетов между хостом и докером.

Подключаем созданную сеть к контейнеру сервера Postgres. Но поскольку я не буду удалять хостовый Postgresql, в качестве внешенго порта я укажу порт отлчный от стандартного 5432. В данном случае 5433.

***sudo docker run --name pg-server --network pg-net -e POSTGRES_PASSWORD=postgres -d -p 5433:5432 -v /var/lib/postgres:/var/lib/postgresql/data postgres:14***

Войдем прямо с хоста используя внешний порт 5433 для подключения

***psql -U postgres -d postgres -h localhost -p 5433***

Создадим базу

***CREATE DATABSE test_doc1;***

Выйдем из сервера в контейнере

***\q***

Зайти внутрь контейнера

***sudo docker exec -it pg-server bash***

Запустим PSQL и подключимся к серверу внутри контейнера

***psql -U postgres***

Посмотрим базу, которую создали до этого

***\l test_doc1***

Все нормально, она на месте.

List of databases

   Name    |  Owner   | Encoding |  Collate   |   Ctype    | Access privileges

-----------+----------+----------+------------+------------+-------------------

 test_doc1 | postgres | UTF8     | en_US.utf8 | en_US.utf8 |

Создать таблицу и добавть две строки.

***create table t1 (id int, name varchar(40));***

***insert into t1 (id, name) values (1, 'Sasha');***

***insert into t1 (id, name) values (2, 'Misha');***

Подключение с ноутбука используя PgAdmin к контейнеру на порт 5433.

![Подключение с ноутбука используя PgAdmin к контейнеру](1.jpg)


Как видим база на месте

![Подключение с ноутбука используя PgAdmin к контейнеру](2.jpg)

Остановим контейнер

***sudo docker stop pg-server***

Удалим контейнер.

***sudo docker rm pg-server***
