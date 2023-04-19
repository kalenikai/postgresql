# Урок 1
## Развертывание 
Развернут PostgresPro 15.2 на Ubuntu Server 22.10 под Hyper-V.
Открыто две сессии используя Putty и psql. 
## Заполнение данными
***psql -U postgres -h localhost -d postgres***

***\SET AUTOCOMMIT OFF***

***create table persons(
    id serial, 
    first_name text, 
    second_name text);
    commit;***

Посмотреть таблицу.

***\dt***

 
 ***insert into persons(first_name, second_name) values('ivan', 'ivanov'); 
 insert into persons(first_name, second_name) values('petr', 'petrov'); 
 commit;***

Просмотр данных
***select * from persons; commit;***

посмотреть текущий уровень изоляции: 

***show transaction isolation level;***

## Упражнение №1 READ COMMITTED
Сессия №1

***psql -U postgres -h localhost -d postgres***

***begin;***

***insert into persons(first_name, second_name) values('sergey', 'sergeev');***

Сессия №2

***select * from persons***

Видите ли вы новую запись и если да то почему?

Нет. Из-за уровня изолированности. На нем не видны незакомиченные транзакции.

Сессия №1

***commit;***

Сессия №2

***select * from persons***

Видите ли вы новую запись и если да то почему?

Да. Транзакция закомечена.

## Упражнение №2 REPEATABLE READ

Начать новые но уже repeatable read транзации

Сессия №2

***begin;***

***set transaction isolation level repeatable read;***

***select * from persons***
Видно 3 записи.

Сессия №1

***begin;***

***insert into persons(first_name, second_name) values('sveta', 'svetova');***

***commit;***

Сессия №2

***select * from persons***

Видно 3 записи.
Хотя запись в первой сессии закомичена, мы ее не видим поскольку утсановлен уровень изолированности repeatable read и читающая транзакция не закрыта, гарантирующий неизменность данных одного и того же набора внутри транзакции.

Сессия №2

***commit;***

***select * from persons***

Видно 4 записи. Поскольку мы вышли из транзакции с уровнем repeatable read.

