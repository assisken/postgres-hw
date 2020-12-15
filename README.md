# Домашняя работа по дисциплине "Проектирование и сопровождение баз данных"

## Ссылки

- [Домашняя работа 1](#домашняя-работа-1)
- [Домашняя работа 2](#домашняя-работа-2)
- [Домашняя работа 3](#домашняя-работа-3)
- [Домашняя работа 4](#домашняя-работа-4)
- [Домашняя работа 5](#домашняя-работа-5)
- [Домашняя работа 6](#домашняя-работа-6)
- [Домашняя работа 7](#домашняя-работа-7)
- [Домашняя работа 8](#домашняя-работа-8)
- [Домашняя работа 9](#домашняя-работа-9)
- [Домашняя работа 10](#домашняя-работа-10)
- [Домашняя работа 11](#домашняя-работа-11)
- [Финальная работа 1](#финальная-работа-1)
- [Финальная работа 2](#финальная-работа-2)
- [Финальная работа 3](#финальная-работа-3)
- [Финальная работа 4](#финальная-работа-4)

## Домашняя работа 1

<details>
<summary>Сама работа</summary>

---

### Задание

Подумать над выбором предметной области для выполнения финальной (экзаменационной) работы.
Выбирайте предметную область, которая вам интересна
и в которой вы разбираетесь или хотите разобраться.

Сделать краткое описание выбранной предметной области (1-2 страницы).
Если описание получится более объемным, не беда.
Ведь это описание затем войдет в финальный отчет.

Попытаться сформулировать требования к будущей базе данных.

### Работа

#### Цель работы

Описать словесно выбранную предметную область – "Расписание кафедры".

#### Описание предметной области

В настоящее время весь документооборот перемещается в электронный вид.
Требуется хранить различные документы, данные и поддерживать их связность.
Например, необходимо хранить учебное расписание,
обеспечить её связность с учебными группами,
преподавателями и аудиториями,
чтобы можно было в любой момент создать расписание учебных групп, преподавателей и аудиторий.
Это и является основной целью и задачей выбранной предметной области.

Можно хранить расписание в слабо структурированном виде в формате JSON,
но тогда не получится просто извлекать расписание конкретной группы, преподавателя или аудитории,
придётся для этого писать дополнительный ненужный код.
Такую задачу можно положить на реляционную базу данных при правильном проектирований связей таблиц.

При посещении веб-страницы расписания группы пользователь выбирает номер группы и номер недели.
База данных должна, соответственно, предоставить набор предметов на выбранную неделю по выбранной группе,
включить информацию о месте проведения занятий, ФИО преподавателя, который будет вести их.

При посещении веб-страницы расписания преподавателя
пользователь выбирает ФИО преподавателя и номер недели.
База данных должна, соответственно предоставить набор предметов на выбранную неделю по выбранному ФИО,
включить информацию о месте проведения занятий, номер групп, для которых занятие проводится.

[Наверх](#ссылки)

---

</details>

## Домашняя работа 2

<details>
<summary>Сама работа</summary>

---

### Задание

Глава 3 (упражнения 1-4)

### Работа

#### Упражнение 1

Выполним запрос:

```sql
demo=# insert into aircrafts values ('SU9', 'Sukhoi SUperJet-100', 300);
ERROR:  0A000: cannot insert into column "model" of view "aircrafts"
DETAIL:  View columns that are not columns of their base relation are not updatable.
```

Ошибка нам говорит о том, что
колонки, не являющимися базовыми колонками своего отношения не могут обновляться через представление.

Посмотрим, какие колонки не являются базовыми для отношения:

```sql
demo=# \d+ aircrafts
                                       View "bookings.aircrafts"
    Column     |     Type     | Collation | Nullable | Default | Storage  |         Description
---------------+--------------+-----------+----------+---------+----------+-----------------------------
 aircraft_code | character(3) |           |          |         | extended | Aircraft code, IATA
 model         | text         |           |          |         | extended | Aircraft model
 range         | integer      |           |          |         | plain    | Maximal flying distance, km
View definition:
 SELECT ml.aircraft_code,
    ml.model ->> lang() AS model,
    ml.range
   FROM aircrafts_data ml;
```

Такой колонкой является `model`, соответственно.

#### Упражнение 2

Пример запроса:

```sql
demo=# select * from aircrafts order by range desc;
 aircraft_code |        model        | range
---------------+---------------------+-------
 773           | Боинг 777-300       | 11100
 763           | Боинг 767-300       |  7900
 319           | Аэробус A319-100    |  6700
 320           | Аэробус A320-200    |  5700
 321           | Аэробус A321-200    |  5600
 733           | Боинг 737-300       |  4200
 SU9           | Сухой Суперджет-100 |  3000
 CR2           | Бомбардье CRJ-200   |  2700
 CN1           | Сессна 208 Караван  |  1200
(9 rows)
```

#### Упражнение 3

Пример такого запроса:

```sql
demo=# update aircrafts set range = range * 2 where model ~ 'Сухой Суперджет*';
UPDATE 1
```

#### Упражнение 4

Пример такого запроса:

```sql
demo=# delete from aircrafts where model = 'ЭТО НЕ САМОЛЁТ';
DELETE 0
```

[Наверх](#ссылки)

---

</details>

## Домашняя работа 3

<details>
<summary>Сама работа</summary>

---

### Задание

Глава 4 (упражнения 2, 4, 8, 12, 15, 21, 30, 33, 35)

### Работа

#### Упражнение 2

Создадим таблицу, наполним её данными и сделаем выборку по ней.

```sql
demo=# create table test_numeric (measurement numeric, description text);
CREATE TABLE

demo=# insert into test_numeric values
demo-# (1234567890.0987654321, 'Точность 20 знаков, масштаб 10 знаков'),
demo-# (1.5, 'Точность 2 знака, масштаб 1 знак'),
demo-# (0.12345678901234567890, 'Точность 21 знак, масштаб 20 знаков'),
demo-# (1234567890, 'Точность 10 знаков, масштаб 0 знаков (целое число)');
INSERT 0 4

demo=# select * from test_numeric;
      measurement       |                    description
------------------------+----------------------------------------------------
  1234567890.0987654321 | Точность 20 знаков, масштаб 10 знаков
                    1.5 | Точность 2 знака, масштаб 1 знак
 0.12345678901234567890 | Точность 21 знак, масштаб 20 знаков
             1234567890 | Точность 10 знаков, масштаб 0 знаков (целое число)
(4 rows)
```

Да, точность сохранена именно в том виде, в который мы записывали изначально.

#### Упражнение 4

Выполним интересные эксперименты с плавающей точкой:

```sql
demo=# select 2e-45::real > 1e-45::real;
 ?column?
----------
 f
(1 row)

demo=# select 4e-324::double precision > 3e-324::double precision;
 ?column?
----------
 f
(1 row)
```

#### Упражнение 8

```sql
demo=# create table test_serial (id serial primary key, name text);
CREATE TABLE

demo=# INSERT INTO test_serial (name) VALUES ('Вишневая');
INSERT 0 1
```

При выполнении этой команды СУБД выдаст сообщение об ошибке. Почему?

```sql
demo=# INSERT INTO test_serial (id, name) VALUES (2, 'Прохладная');
INSERT 0 1
```

В версии СУБД PostgreSQL 12.4 не выдаёт ошибку.

```sql
demo=# INSERT INTO test_serial (name) VALUES ('Грушевая');
ERROR:  23505: duplicate key value violates unique constraint "test_serial_pkey"
DETAIL:  Key (id)=(2) already exists.
demo=# INSERT INTO test_serial (name) VALUES ('Грушевая');
INSERT 0 1
demo=# INSERT INTO test_serial (name) VALUES ('Зеленая');
INSERT 0 1
demo=# DELETE FROM test_serial WHERE id = 4;
DELETE 1
demo=# INSERT INTO test_serial (name) VALUES ('Луговая');
INSERT 0 1
```

Зато выдала ошибку, что такой ключ уже существует.
Но счётчик `test_serial_pkey` всё равно инкрементировался.

Теперь сделаем выборку.

```sql
demo=# SELECT * FROM test_serial;
 id |    name
----+------------
  1 | Вишневая
  2 | Прохладная
  3 | Грушевая
  5 | Луговая
(4 rows)
```

#### Упражнение 12

```sql
demo=# show datestyle;
 DateStyle
-----------
 ISO, DMY
(1 row)

demo=# set datestyle to 'MDY';
SET

demo=# show datestyle;
 DateStyle
-----------
 ISO, MDY
(1 row)

demo=# SELECT '18-05-2016'::date;
ERROR:  22008: date/time field value out of range: "18-05-2016"
LINE 1: SELECT '18-05-2016'::date;
               ^
HINT:  Perhaps you need a different "datestyle" setting.

demo=# SELECT '05-18-2016'::date;
    date    
------------
 2016-05-18
(1 row)

demo=# SELECT '05-18-2016'::date;
    date    
------------
 2016-05-18
(1 row)

demo=# SET datestyle TO 'Postgres, DMY';
SET

demo=# show datestyle;
   DateStyle
---------------
 Postgres, DMY
(1 row)

demo=# select current_date;
 current_date
--------------
 04-10-2020
(1 row)

demo=# set datestyle to 'SQL, DMY';
SET
demo=# select current_date;
 current_date
--------------
 04/10/2020
(1 row)

demo=# set datestyle to 'German, DMY';
SET
Time: 0.519 ms
demo=# select current_date;
 current_date
--------------
 04.10.2020
(1 row)
```

#### Упражнение 15

Проэкспериментируем с преобразованием даты в строку.

```sql
demo=# SELECT to_char(current_timestamp, 'ddыmm');
 to_char
---------
 04ы10
(1 row)

demo=# SELECT to_char(current_timestamp, 'MM/DD/YYYY');
  to_char
------------
 10/04/2020
(1 row)

demo=# SELECT to_char(current_timestamp, 'DD-MM-YYYY')::date;
  to_char
------------
 2020-10-04
(1 row)
```

#### Упражнение 21

Предположу, что интервал "1 месяц" добавит единицу ко времени месяца.
В первом случае выведет 2016-02-29, а во втором — 2016-03-29.

```sql
demo=# SELECT ('2016-01-31'::date +'1 mon'::interval) AS new_date;
      new_date
---------------------
 2016-02-29 00:00:00
(1 row)

demo=# SELECT ('2016-02-29'::date +'1 mon'::interval) AS new_date;
      new_date
---------------------
 2016-03-29 00:00:00
(1 row)
```

Отлично, я угадал! =)

#### Упражнение 30

Предположения:

```sql
INSERT INTO test_bool VALUES (TRUE, 'yes');
INSERT INTO test_bool VALUES (yes, 'yes');
-- "yes" — невалидный boolean

INSERT INTO test_bool VALUES ('yes', true);
-- "'yes'" — то же самое, но в виде строки.
-- Если в SQL слабая типизация, то ошибка будет как выше.
-- Если сильная — ошибка типа.
-- Ну и true — невалидная строка.

INSERT INTO test_bool VALUES ('yes', TRUE);
-- То же самое, как выше.

INSERT INTO test_bool VALUES ('1', 'true');
-- '1' — вообще строка.
-- Если типизация сильная, ошибка типа.
-- Если типисация слабая, то будет '1' -> 1 -> true, т.е. без ошибки.

INSERT INTO test_bool VALUES (1, 'true');
-- Зависит от силы типизации, см. выше.

INSERT INTO test_bool VALUES ('t', 'true');
-- Типизация, но надо смотреть, является ли t — булевым значением.

INSERT INTO test_bool VALUES ('t', truth);
-- Ошибка со строкой. Надо в кавычки заворачивать.

INSERT INTO test_bool VALUES (true, true);
-- Ошибка со строкой. Надо в кавычки заворачивать.

INSERT INTO test_bool VALUES (1::boolean, 'true');
-- Явное преобразование. Скорее всего, ошибки не будет.

INSERT INTO test_bool VALUES (111::boolean, 'true');
-- Явное преобразование. Скорее всего, ошибка будет,
-- т.к. не понятно, можно ли 111 привести в boolean.
```

Итог:

```sql
demo=# INSERT INTO test_bool VALUES (TRUE, 'yes');
INSERT 0 1

demo=# INSERT INTO test_bool VALUES (yes, 'yes');
ERROR:  42703: column "yes" does not exist
LINE 1: INSERT INTO test_bool VALUES (yes, 'yes');
                                      ^

demo=# INSERT INTO test_bool VALUES ('yes', true);
INSERT 0 1

demo=# INSERT INTO test_bool VALUES ('yes', TRUE);
INSERT 0 1

demo=# INSERT INTO test_bool VALUES ('1', 'true');
INSERT 0 1

demo=# INSERT INTO test_bool VALUES (1, 'true');
ERROR:  42804: column "a" is of type boolean but expression is of type integer
LINE 1: INSERT INTO test_bool VALUES (1, 'true');
                                      ^
HINT:  You will need to rewrite or cast the expression.

demo=# INSERT INTO test_bool VALUES ('t', 'true');
INSERT 0 1

demo=# INSERT INTO test_bool VALUES ('t', truth);
ERROR:  42703: column "truth" does not exist
LINE 1: INSERT INTO test_bool VALUES ('t', truth);
                                           ^

demo=# INSERT INTO test_bool VALUES (true, true);
INSERT 0 1

demo=# INSERT INTO test_bool VALUES (1::boolean, 'true');
INSERT 0 1

demo=# INSERT INTO test_bool VALUES (111::boolean, 'true');
INSERT 0 1
```

Вывод: типизация слабая, но неявные преобразования идут через строковые значения.
Целые числа надо преобразовывать явно.

#### Упражнение 33

```sql
demo=# CREATE TABLE food(food text[][]);
CREATE TABLE
demo=# insert into food values ('{ { "сосиска", "макароны", "кофе" },{ "котлета", "каша", "кофе" },{ "сосиска", "каша", "кофе" },{ "котлета", "каша", "чай" } }'::text[][]);
demo=# select food[1][1] from food;
  food
---------
 сосиска
(1 row)
```

#### Упражнение 35

```sql
demo=# SELECT '[{"мяч":"футбол"},{"бита":"бейсбол"},{"ракетка":"тенис"}]'::json -> 2;
      ?column?
---------------------
 {"ракетка":"тенис"}
(1 строка)


demo=#
demo=# SELECT '[{"мяч":"футбол"},{"бита":"бейсбол"},{"ракетка":"тенис"}]'::json -> 'мяч';
 ?column?
----------

(1 строка)


demo=#
demo=# SELECT '["мяч","бита","ракетка"]'::json ->> 0;
 ?column?
----------
 мяч
(1 строка)


demo=#
demo=# SELECT '{"мяч":"футбол","бита":"бейсбол","ракетка":"тенис"}'::json ->> 'бита';
 ?column?
----------
 бейсбол
(1 строка)


demo=#
demo=# SELECT '{"игры": {"компьютерные": ["dark","souls"], "спортивные":4}}'::json #> '{игры,компьютерные,0}';
 ?column?
----------
 "dark"
(1 строка)


demo=#
demo=# SELECT '{"игры": {"компьютерные": ["dark","souls"], "спортивные":4}}'::json #>> '{игры,компьютерные,0}';
 ?column?
----------
 dark
(1 строка)
```

[Наверх](#ссылки)

---

</details>

## Домашняя работа 4

<details>
<summary>Сама работа</summary>

---

### Задание

Глава 5 (упражнения 2, 9, 17, 18)

### Работа

#### Упражнение 2

Посмотрите, какие ограничения уже наложены на атрибуты таблицы «Успеваемость» (progress).
В качестве примера рассмотрим такой вариант. Добавьте в таблицу progress еще один атрибут — «Форма проверки знаний» (test_form), который может принимать только два значения: «экзамен» или «зачет». Тогда набор допустимых значений атрибута «Оценка» (mark) будет зависеть от того, экзамен или зачет предусмотрены по данной дисциплине. Если предусмотрен экзамен, тогда допускаются значения 3, 4, 5, если зачет — тогда 0 (не зачтено) или 1 (зачтено).

```sql
                                Таблица "public.progress"
   Столбец   |         Тип          | Правило сортировки | Допустимость NULL | По умолчанию
-------------+----------------------+--------------------+-------------------+------
 record_book | numeric(5,0)         |                    | not null          |
 subject     | text                 |                    | not null          |
 acad_year   | text                 |                    | not null          |
 term        | numeric(1,0)         |                    | not null          |
 mark        | numeric(1,0)         |                    | not null          | 5
 test_form   | character varying(7) |                    |                   |
Ограничения-проверки:
    "progress_mark_check" CHECK (mark >= 3::numeric AND mark <= 5::numeric)
    "progress_term_check" CHECK (term = 1::numeric OR term = 2::numeric)

edu=# ALTER TABLE progress ADD test_form VARCHAR(7);
ALTER TABLE

edu=# ALTER TABLE progress ADD CHECK (test_form IN ('зачет','экзамен'));
ALTER TABLE
```

Проверьте, как будет работать новое ограничение в модифицированной таблице progress. Для этого выполните команды INSERT, как удовлетворяющие ограничению, так и нарушающие его.

```sql
edu=# INSERT INTO students VALUES (7, 'student 1', 658, 7685);
INSERT 0 1
edu=# INSERT INTO progress VALUES (4, 'Mathematics', '2',2, 4, 'экзамен');
INSERT 0 1
edu=# INSERT INTO progress VALUES (4, 'Mathematics', '2',2, 0, 'зачет');
ОШИБКА: новая строка в отношении “progress” нарушает ограничение-проверку "progress_mark_check"
ПОДРОБНОСТИ: Ошибочная строка содержит (4, 'Mathematics', '2',2, 0, 'зачет').
```

В таблице уже было ограничение на допустимые значения атрибута mark. Как вы думаете, не будет ли оно конфликтовать с новым ограничением? Проверьте эту гипотезу. Если ограничения конфликтуют, тогда удалите старое ограничение и снова попробуйте добавить строки в таблицу.

Они конфликтуют, в таком случае удалим ограничение, и попробуем добавить строки снова.

```sql
edu=# ALTER TABLE progress DROP CONSTRAINT progress_mark_check;
edu=# INSERT INTO progress VALUES (4, 'Mathematics', '2',2, 0, 'зачет');

INSERT 0 1
```

#### Упражнение 9

В таблице «Студенты» (students) есть текстовый атрибут name, на который наложено ограничение NOT NULL. Как вы думаете, что будет, если при вводе новой строки в эту таблицу дать атрибуту name в качестве значения пустую строку?

```sql
edu=# INSERT INTO students VALUES (2, '', 979, 74673);
INSERT 0 1
```

Добавим ограничение ( name <> '' )

```sql
edu=# ALTER TABLE students ADD CHECK ( name <> '' );
ALTER TABLE
edu=# INSERT INTO students VALUES (3, '', 979, 74673);
ОШИБКА: новая строка в отношении “progress” нарушает ограничение-проверку
"students_name_check"
```

Посмотрим что теперь будет при вставке строки с пустым значением

```sql
edu=# INSERT INTO students VALUES (3, ' ', 979, 74673);
INSERT 0 1
```

Добавим ограничение ( trim (name) <> '' )

```sql
edu=# ALTER TABLE students ADD CHECK (trim (name) <> '');
ALTER TABLE
edu=# INSERT INTO students VALUES (3, ' ', 979, 74673);
ОШИБКА: новая строка в отношении “progress” нарушает ограничение-проверку
"students_name_check"
```

Есть ли подобные слабые места в таблице «Успеваемость» (progress)?
- Есть, с поля с текстовым типом могут быть вставлены пустые строки.

#### Упражнение 17

Подумайте, какие представления было бы целесообразно создать для нашей базы данных «Авиаперевозки». Необходимо учесть наличие различных групп пользователей, например: пилоты, диспетчеры, пассажиры, кассиры. Создайте представления и проверьте их в работе.

Диспетчер

```sql
demo=# CREATE VIEW dispatcher_info AS SELECT
demo-#  flight_no,
demo-#  scheduled_departure,
demo-#  status,
demo-#  model,
demo-#  range
demo-# FROM flights f
demo-# LEFT JOIN aircrafts a on f.aircraft_code = a.aircraft_code;
CREATE VIEW
demo=# SELECT * FROM  dispatcher_info;
 flight_no |  scheduled_departure   |  status   |        model        | range
-----------+------------------------+-----------+---------------------+-------
 PG0405    | 2016-09-13 08:35:00+03 | Arrived   | Airbus A321-200     |  5600
 PG0404    | 2016-10-03 18:05:00+03 | Arrived   | Airbus A321-200     |  5600
 PG0405    | 2016-10-03 08:35:00+03 | Arrived   | Airbus A321-200     |  5600
 PG0402    | 2016-11-07 11:25:00+03 | Scheduled | Airbus A321-200     |  5600
 PG0405    | 2016-10-14 08:35:00+03 | On Time   | Airbus A321-200     |  5600
 PG0404    | 2016-10-14 18:05:00+03 | Scheduled | Airbus A321-200     |  5600
 PG0403    | 2016-10-14 10:25:00+03 | Delayed   | Airbus A321-200     |  5600
 PG0402    | 2016-10-14 11:25:00+03 | On Time   | Airbus A321-200     |  5600
 PG0405    | 2016-10-23 08:35:00+03 | Scheduled | Airbus A321-200     |  5600
 PG0402    | 2016-10-21 11:25:00+03 | Scheduled | Airbus A321-200     |  5600
 PG0403    | 2016-10-21 10:25:00+03 | Scheduled | Airbus A321-200     |  5600
 PG0404    | 2016-10-21 18:05:00+03 | Scheduled | Airbus A321-200     |  5600
 PG0405    | 2016-10-21 08:35:00+03 | Scheduled | Airbus A321-200     |  5600
 PG0402    | 2016-10-04 11:25:00+03 | Arrived   | Airbus A321-200     |  5600
 PG0402    | 2016-09-25 11:25:00+03 | Arrived   | Airbus A321-200     |  5600
```

#### Упражнение 18

Подумайте, какие еще таблицы было бы целесообразно дополнить столбцами типа json/jsonb. Вспомните, что, например, в таблице «Билеты» (tickets) уже есть столбец такого типа — contact_data. Выполните модификации таблиц и измените в них одну-две строки для проверки правильности ваших решений.

В таблицу bookings в качестве json поля можно добавить информамцию о периоде действия брони.

```sql
demo=# ALTER TABLE bookings ADD COLUMN booking_period jsonb;
ALTER TABLE

demo=# UPDATE bookings
demo-# SET booking_period='{"booking_start": "06.10.2020", "booking_end": "16.10.2020"}'
demo-# WHERE book_ref='000181';
UPDATE 1

demo=# SELECT * FROM bookings WHERE book_ref='000181';
 book_ref |       book_date        | total_amount |   booking_period
----------+------------------------+--------------+-------------------------------------------------------------
 000181   | 2016-10-08 12:28:00+03 |    131800.00 | {"booking_end": "16.10.2020", "booking_start": "06.10.2020"}
(1 строка)
```

[Наверх](#ссылки)

---

</details>

## Домашняя работа 5

<details>
<summary>Сама работа</summary>

---

### Задание

Глава 6 (упражнения 2, 7, 9, 13, 19, 21, 23)

### Работа

#### Упражнение 2

Этот запрос выбирает из таблицы «Билеты» (tickets) всех пассажиров с именами, состоящими из трех букв (в шаблоне присутствуют три символа «_»): 

```sql
SELECT passenger_name
FROM tickets
WHERE passenger_name LIKE '___ %';
```

Предложите шаблон поиска в операторе LIKE для выбора из этой таблицы всех пассажиров с фамилиями, состоящими из пяти букв.

```sql
demo=# SELECT passenger_name
demo-# FROM tickets
demo-# WHERE passenger_name LIKE '_____ %';
   passenger_name
--------------------
 ALENA ABRAMOVA
 ALENA ABRAMOVA
 ALENA ABRAMOVA
 ALENA ABRAMOVA
 ALENA ABRAMOVA
 ALENA AFANASEVA
 ALENA AFANASEVA
 ALENA AFANASEVA
 ALENA AFANASEVA
```

#### Упражнение 7

Самые крупные самолеты в нашей авиакомпании — это Boeing 777-300. Выяснить, между какими парами городов они летают, поможет запрос: 

```sql
SELECT DISTINCT departure_city, arrival_city
FROM routes r
JOIN aircrafts a ON r.aircraft_code = a.aircraft_code
WHERE a.model = 'Boeing 777-300'
ORDER BY 1;
```

Модифицируйте запрос таким образом, чтобы каждая пара городов была выведена только один раз

```sql
demo=# SELECT DISTINCT r.departure_city, r.arrival_city
demo-# FROM routes r
demo-# JOIN routes rr ON r.arrival_city = rr.departure_city
demo-# AND rr.arrival_city = r.departure_city and r.arrival_city > rr.arrival_city
demo-# JOIN aircrafts a ON r.aircraft_code = a.aircraft_code
demo-# WHERE a.model = 'Boeing 777-300'
demo-# ORDER BY 1;
 departure_city | arrival_city
----------------+--------------
 Екатеринбург   | Москва
 Москва         | Новосибирск
 Москва         | Пермь
 Москва         | Сочи
(4 строки)
```

#### Упражнение 9

Для ответа на вопрос, сколько рейсов выполняется из Москвы в Санкт-Петербург, можно написать совсем простой запрос: 

```sql
SELECT count( * )
FROM routes
WHERE departure_city = 'Москва'
AND arrival_city = 'Санкт-Петербург'
```

А с помощью какого запроса можно получить результат в таком виде?

```sql
 departure_city |  arrival_city   | count
----------------+-----------------+-------
 Москва         | Санкт-Петербург |    12

demo=# SELECT  departure_city, arrival_city, count(*)
demo-# FROM routes
demo-# WHERE departure_city = 'Москва'
demo-# AND arrival_city = 'Санкт-Петербург'
demo-# GROUP BY departure_city, arrival_city;
 departure_city |  arrival_city   | count
----------------+-----------------+-------
 Москва         | Санкт-Петербург |    12
(1 строка)
```

#### Упражнение 13

Ответить на вопрос о том, каковы максимальные и минимальные цены билетов на все направления, может такой запрос: 

```sql
SELECT
f.departure_city,
f.arrival_city,
max( tf.amount ),
min( tf.amount )
FROM flights_v f
JOIN ticket_flights tf ON f.flight_id = tf.flight_id
GROUP BY 1, 2
ORDER BY 1, 2;
```

А как выявить те направления, на которые не было продано ни одного билета?

```sql
demo=# SELECT
demo-# f.departure_city,
demo-# f.arrival_city,
demo-# max( tf.amount ),
demo-# min( tf.amount )
demo-# FROM flights_v f
demo-# LEFT JOIN ticket_flights tf ON f.flight_id = tf.flight_id
demo-# GROUP BY 1, 2
demo-# ORDER BY 1, 2;

      departure_city      |       arrival_city       |    max    |   min
--------------------------+--------------------------+-----------+----------
 Абакан                   | Архангельск              |           |
 Абакан                   | Грозный                  |           |
 Абакан                   | Кызыл                    |           |
 Абакан                   | Москва                   | 101000.00 | 33700.00
 Абакан                   | Новосибирск              |   5800.00 |  5800.00
 Абакан                   | Томск                    |   4900.00 |  4900.00
 Анадырь                  | Москва                   | 185300.00 | 61800.00
 Анадырь                  | Хабаровск                |  92200.00 | 30700.00
 Анапа                    | Белгород                 |  18900.00 |  6300.00
 Анапа                    | Москва                   |  36600.00 | 12200.00
```

#### Упражнение 19

В разделе 6.4 мы использовали рекурсивный алгоритм в общем табличном выражении. Изучите этот пример, чтобы лучше понять работу рекурсивного алгоритма:

```sql
WITH RECURSIVE ranges ( min_sum, max_sum )
AS (
VALUES( 0, 100000 ),
( 100000, 200000 ),
( 200000, 300000 )
UNION ALL
SELECT min_sum + 100000, max_sum + 100000
FROM ranges
WHERE max_sum < ( SELECT max( total_amount ) FROM bookings )
)
SELECT * FROM ranges;
```

##### Задание 1

Модифицируйте запрос, добавив в него столбец level (можно назвать его и iteration). Этот столбец должен содержать номер текущей итерации, поэтому нужно увеличивать его значение на единицу на каждом шаге. Не забудьте задать начальное значение для добавленного столбца в предложении VALUES.

```sql
demo=# WITH RECURSIVE ranges ( min_sum, max_sum, iter )
demo-# AS (
demo(# VALUES( 0, 100000, 1 ),
demo(# ( 100000, 200000, 1 ),
demo(# ( 200000, 300000, 1 )
demo(# UNION ALL
demo(# SELECT min_sum + 100000, max_sum + 100000 , iter + 1
demo(# FROM ranges
demo(# WHERE max_sum < ( SELECT max( total_amount ) FROM bookings )
demo(# )
demo-# SELECT * FROM ranges;
 min_sum | max_sum | iter
---------+---------+------
       0 |  100000 |    1
  100000 |  200000 |    1
  200000 |  300000 |    1
  100000 |  200000 |    2
  200000 |  300000 |    2
  300000 |  400000 |    2
  200000 |  300000 |    3
  300000 |  400000 |    3
  400000 |  500000 |    3
  300000 |  400000 |    4
  400000 |  500000 |    4
  500000 |  600000 |    4
  400000 |  500000 |    5
  500000 |  600000 |    5
  600000 |  700000 |    5
```

##### Задание 2

Для завершения экспериментов замените UNION ALL на UNION и выполните запрос. Сравните этот результат с предыдущим, когда мы использовали UNION ALL.

```sql
demo=# WITH RECURSIVE ranges ( min_sum, max_sum )
demo-# AS (
demo(# VALUES( 0, 100000 ),
demo(# ( 100000, 200000 ),
demo(# ( 200000, 300000 )
demo(# UNION
demo(# SELECT min_sum + 100000, max_sum + 100000
demo(# FROM ranges
demo(# WHERE max_sum < ( SELECT max( total_amount ) FROM bookings )
demo(# )
demo-# SELECT * FROM ranges;
 min_sum | max_sum
---------+---------
       0 |  100000
  100000 |  200000
  200000 |  300000
  300000 |  400000
  400000 |  500000
  500000 |  600000
  600000 |  700000
  700000 |  800000
  800000 |  900000
  900000 | 1000000
 1000000 | 1100000
 1100000 | 1200000
 1200000 | 1300000
(13 строк)
```

#### Упражнение 21

В тексте главы был приведен запрос, выводящий список городов, в которые нет рейсов из Москвы.

```sql
SELECT DISTINCT a.city
FROM airports a
WHERE NOT EXISTS (
SELECT * FROM routes r
WHERE r.departure_city = 'Москва'
AND r.arrival_city = a.city
)
AND a.city <> 'Москва'
ORDER BY city;
```

Можно предложить другой вариант, в котором используется одна из операций над множествами строк: объединение, пересечение или разность.
Вместо знака «?» поставьте в приведенном ниже запросе нужное ключевое слово — UNION, INTERSECT или EXCEPT — и обоснуйте ваше решение.

```sql
SELECT city
FROM airports
WHERE city <> 'Москва'
?
SELECT arrival_city
FROM routes
WHERE departure_city = 'Москва'
ORDER BY city;
```

Будем использовать EXCEPT, потому что требуется исключить те города, в которые нет рейсов из Москвы.

#### Упражнение 22

В тексте главы мы рассматривали такой запрос: получить перечень аэропортов в тех городах, в которых больше одного аэропорта.

```sql
SELECT aa.city, aa.airport_code, aa.airport_name
FROM (
SELECT city, count( * )
FROM airports
GROUP BY city
HAVING count( * ) > 1
) AS a
JOIN airports AS aa ON a.city = aa.city
ORDER BY aa.city, aa.airport_name;
```

Как вы думаете, обязательно ли наличие функции count в подзапросе в предложении SELECT или можно написать просто SELECT city FROM airports ?

 - Да, обязательно, так как без count выведутся только те города, в которых имеется только один аэропорт.

```sql
demo=# SELECT aa.city, aa.airport_code, aa.airport_name, count
demo-# FROM (
demo(# SELECT city, count( * ) as count
demo(# FROM airports
demo(# GROUP BY city
demo(# ) AS a
demo-# JOIN airports AS aa ON a.city = aa.city
demo-# ORDER BY aa.city, aa.airport_name;
           city           | airport_code |     airport_name     | count
--------------------------+--------------+----------------------+-------
 Абакан                   | ABA          | Абакан               |     1
 Анадырь                  | DYR          | Анадырь              |     1
 Анапа                    | AAQ          | Витязево             |     1
 Архангельск              | ARH          | Талаги               |     1
 Астрахань                | ASF          | Астрахань            |     1
 Барнаул                  | BAX          | Барнаул              |     1
 Белгород                 | EGO          | Белгород             |     1
 Белоярский               | EYK          | Белоярский           |     1
 Благовещенск             | BQS          | Игнатьево            |     1
 Братск                   | BTK          | Братск               |     1
 Брянск                   | BZK          | Брянск               |     1
 Бугульма                 | UUA          | Бугульма             |     1
```

#### Упражнение 23

Предположим, что департамент развития нашей авиакомпании задался вопросом: каким будет общее число различных маршрутов, которые теоретически можно проложить между всеми городами? Если в каком-то городе имеется более одного аэропорта, то это учитывать не будем, т. е. маршрутом будем считать путь между городами, а не между аэропортами. Здесь мы используем соединение таблицы с самой собой на основе неравенства значений атрибутов.

```sql
SELECT count( * )
FROM ( SELECT DISTINCT city FROM airports ) AS a1
JOIN ( SELECT DISTINCT city FROM airports ) AS a2
ON a1.city <> a2.city;
```

Перепишите этот запрос с общим табличным выражением.

```sql
WITH city_from AS
( SELECT DISTINCT city FROM airports )
SELECT count( * )
FROM city_from f
JOIN ( SELECT DISTINCT city FROM airports ) AS a2
ON f.city <> a2.city;
```

[Наверх](#ссылки)

---

</details>

## Домашняя работа 6

<details>
<summary>Сама работа</summary>

---

### Задание

Глава 7 (упражнения 1, 2, 4)

### Работа

#### Упражнение 1

Добавьте в определение таблицы aircrafts_log значение по умолчанию current_timestamp и соответствующим образом измените команды INSERT, приведенные в тексте главы.

```sql
demo=# CREATE TEMP TABLE aircrafts_log AS
demo-# SELECT * FROM aircrafts WITH DATA;
SELECT 9
demo=# ALTER TABLE aircrafts_log
demo-# ADD COLUMN log_timestamp TIMESTAMP DEFAULT (current_timestamp);
ALTER TABLE
demo=# SELECT * FROM aircrafts_log;
 aircraft_code |        model        | range |       log_timestamp
---------------+---------------------+-------+----------------------------
 773           | Boeing 777-300      | 11100 | 2020-12-06 23:06:36.980049
 763           | Boeing 767-300      |  7900 | 2020-12-06 23:06:36.980049
 320           | Airbus A320-200     |  5700 | 2020-12-06 23:06:36.980049
 321           | Airbus A321-200     |  5600 | 2020-12-06 23:06:36.980049
 319           | Airbus A319-100     |  6700 | 2020-12-06 23:06:36.980049
 733           | Boeing 737-300      |  4200 | 2020-12-06 23:06:36.980049
 CN1           | Cessna 208 Caravan  |  1200 | 2020-12-06 23:06:36.980049
 CR2           | Bombardier CRJ-200  |  2700 | 2020-12-06 23:06:36.980049
 SU9           | Sukhoi SuperJet-100 |  6000 | 2020-12-06 23:06:36.980049
(9 строк)

demo=# TRUNCATE aircrafts_log;
TRUNCATE TABLE
demo=#
demo=# WITH add_row AS
demo-# ( INSERT INTO aircrafts_tmp
demo(# SELECT * FROM aircrafts
demo(# RETURNING *
demo(# )
demo-# INSERT INTO aircrafts_log
demo-# SELECT add_row.aircraft_code, add_row.model, add_row.range
demo-# FROM add_row;
INSERT 0 9
demo=#
demo=# SELECT * FROM aircrafts_log;
 aircraft_code |        model        | range |       log_timestamp
---------------+---------------------+-------+----------------------------
 773           | Boeing 777-300      | 11100 | 2020-12-06 23:09:54.005097
 763           | Boeing 767-300      |  7900 | 2020-12-06 23:09:54.005097
 320           | Airbus A320-200     |  5700 | 2020-12-06 23:09:54.005097
 321           | Airbus A321-200     |  5600 | 2020-12-06 23:09:54.005097
 319           | Airbus A319-100     |  6700 | 2020-12-06 23:09:54.005097
 733           | Boeing 737-300      |  4200 | 2020-12-06 23:09:54.005097
 CN1           | Cessna 208 Caravan  |  1200 | 2020-12-06 23:09:54.005097
 CR2           | Bombardier CRJ-200  |  2700 | 2020-12-06 23:09:54.005097
 SU9           | Sukhoi SuperJet-100 |  6000 | 2020-12-06 23:09:54.005097
(9 строк)
```

#### Упражнение 2

В предложении RETURNING можно указывать не только символ «∗», означающий выбор всех столбцов таблицы, но и более сложные выражения, сформированные на основе этих столбцов. В тексте главы мы копировали содержимое таблицы «Самолеты» в таблицу aircrafts_tmp, используя в предложении RETURNING именно «∗». Однако возможен и другой вариант запроса:

```sql
WITH add_row AS
( INSERT INTO aircrafts_tmp
SELECT * FROM aircrafts
RETURNING aircraft_code, model, range,
current_timestamp, 'INSERT'
)
INSERT INTO aircrafts_log
SELECT ? FROM add_row;
```

Что нужно написать в этом запросе вместо вопросительного знака?

- Можно написать «add_row.aircraft_code, add_row.model, add_row.range, current_timestamp, 'INSERT'»

#### Упражнение 4

В тексте главы в предложениях ON CONFLICT команды INSERT мы использовали только выражения, состоящие из имени одного столбца.
Однако в таблице «Места» (seats) первичный ключ является составным и включает два столбца.
Напишите команду INSERT для вставки новой строки в эту таблицу и предусмотрите возможный конфликт добавляемой строки со строкой, уже имеющейся в таблице.
Сделайте два варианта предложения ON CONFLICT: первый — с использованием перечисления имен столбцов для проверки наличия дублирования, второй — с использованием предложения ON CONSTRAINT. Для того чтобы не изменить содержимое таблицы «Места», создайте ее копию и выполняйте все эти эксперименты с таблицей-копией.

Для выполнения задания сделаем еще одну такую же таблицу

```sql
demo=# CREATE TEMP TABLE seats_tmp
demo-# AS SELECT * FROM SEATS;
SELECT 1339
demo=# ALTER TABLE seats_tmp
demo-# ADD PRIMARY KEY (aircraft_code, seat_no);
ALTER TABLE

demo=# \d seats_tmp
                                  Таблица "pg_temp_3.seats_tmp"
     Столбец     |          Тип          | Правило сортировки | Допустимость NULL | По умолчанию
-----------------+-----------------------+--------------------+-------------------+
 aircraft_code   | character(3)          |                    | not null          |
 seat_no         | character varying(4)  |                    | not null          |
 fare_conditions | character varying(10) |                    |                   |
Индексы:
    "seats_tmp_pkey" PRIMARY KEY, btree (aircraft_code, seat_no)

demo=# INSERT INTO seats_tmp
demo-# SELECT aircraft_code, seat_no, fare_conditions
demo-# FROM seats ON CONFLICT DO NOTHING;
INSERT 0 0

demo=# INSERT INTO seats_tmp
demo-# VALUES ( 319, '2A', 'Business' )
demo-# ON CONFLICT ON CONSTRAINT seats_tmp_pkey
demo-# DO UPDATE SET aircraft_code = excluded.aircraft_code,
demo-# seat_no = excluded.seat_no
demo-# RETURNING *;
 aircraft_code | seat_no | fare_conditions
---------------+---------+-----------------
 319           | 2A      | Business
(1 строка)
```

[Наверх](#ссылки)

---

</details>

## Домашняя работа 7

<details>
<summary>Сама работа</summary>

---

### Задание

Глава 8 (упражнения 1, 3)

### Работа

[Наверх](#ссылки)

---

</details>

## Домашняя работа 8

<details>
<summary>Сама работа</summary>

---

### Задание

Глава 9 (упражнения 2, 3)

### Работа

[Наверх](#ссылки)

---

</details>

## Домашняя работа 9

<details>
<summary>Сама работа</summary>

---

### Задание

Глава 10 (упражнения 3, 6, 8)

### Работа

[Наверх](#ссылки)

---

</details>

## Домашняя работа 10

<details>
<summary>Сама работа</summary>

---

### Предисловие

Программирование на стороне сервера.

Выполняется на основе [презентации](https://edu.postgrespro.ru/sqlprimer/sqlprimer-2019-msu-09.pdf)
и главы 4 учебного пособия "Администрирование информационных систем".
Учебную базу данных можно наполнить информацией, функциями и триггерами
с помощью команд, выполняемых в командной строке Debian:

```bash
createdb ais -U postgres
psql -d ais -f adj_list.sql -U postgres
```

Файл `adj_list.sql` находится в составе архива исходных текстов, прилагаемых к учебному пособию.
Они находятся [здесь](http://www.morgunov.org/docs/inf_sys_admin_prg.tgz)

Эти исходные тексты также есть в виртуальной машине ОС Debian
в каталоге `/home/WORK/Databases/Admin_DB/UTF-8`.
В составе этих примеров есть и те,
которые не приведены в тексте пособия,
но они могут быть полезными при выполнении финального задания.

### Задание

Нужно проделать упражнения 12-18 на стр. 86-88 учебного пособия "Администрирование информационных систем".
Почти все эти упражнения являются простыми, ознакомительными, не требующими программирования.

_Прим. В настоящее время я пишу вторую часть учебника по SQL (продвинутую).
Скорее всего, презентацию по программированию на стороне сервера
я дополню более сложными примерами, тогда и задание будет откорректировано._

### Работа

[Наверх](#ссылки)

---

</details>

## Домашняя работа 11

<details>
<summary>Сама работа</summary>

---

### Предисловие

Полнотекстовый поиск.

Задание выполняется на основе презентации 10 "Полнотекстовый поиск"
и главы 12 документации на [Постгрес](https://postgrespro.ru/docs/postgresql/12/textsearch)

### Задание

Придумать и реализовать пример использования полнотекстового поиска,
аналогичный (можно более простой или более сложный) тому примеру с библиотечным каталогом,
который был приведен в презентации.
Можно использовать исходные тексты, приведенные в [презентации](https://edu.postgrespro.ru/sqlprimer/sqlprimer-2019-msu-10.tgz).

### Работа

[Наверх](#ссылки)

---

</details>

## Финальная работа 1

<details>
<summary>Сама работа</summary>

---

### Задание

Спроектировать базу данных для выбранной предметной области.

Самые первые этапы разработки вы выполните в первом задании (см. выше).
А в финальном задании выполняете остальные этапы.

При этом нужно следовать общей методологии проектирования баз данных:
сначала необходимо создать концептуальную модель данных с использованием ER-диаграмм,
затем построить логическую модель, выполнив отображение сущностей и связей
в отношения, в завершение нужно выполнить физическое проектирование, создав
реляционные таблицы в среде целевой СУБД PostgreSQL. Можно использовать
ту нотацию, которая вам больше нравится: П. Чена, "вороньи лапки"
или UML-нотацию, как в лекции и в учебнике Т. Коннолли. Для рисования диаграмм
можно использовать любой бесплатный редактор, умеющий рисовать ER-диаграммы.
Можно нарисовать их в Word'е или аккуратно от руки (и сфотографировать).

Число таблиц должно быть равно 8-10.

Почему дается интервал (8-10)? Потому что число таблиц может быть 8, а не 10,
но при этом могут использоваться более разнообразные типы данных и может быть
больше столбцов в таблицах.

Обязательно должны быть созданы триггеры (и триггерные функции к ним)
и хранимые функции (процедуры) на языке PL/pgSQL.

- Число триггеров должно быть не менее 2.
- Число хранимых функций должно быть не менее 2.

Эти функции и триггеры не обязательно должны быть сложными. Цель -- научиться
их применять с пользой для дела.

### Работа

[Наверх](#ссылки)

---

</details>

## Финальная работа 2

<details>
<summary>Сама работа</summary>

---

### Задание

Ввести небольшое количество записей в таблицы базы данных, чтобы можно было
продемонстрировать типичные запросы к базе данных. В запросах должны быть
использованы:

- подзапросы;
- общие табличные выражения (CTE) (хотя бы одно) и
- оконные функции (Window functions) (хотя бы одна).

### Работа

[Наверх](#ссылки)

---

</details>

## Финальная работа 3

<details>
<summary>Сама работа</summary>

---

### Задание

Создавать приложение (интерфейс пользователя) не требуется.

Конечно, создать его не запрещается, но это не означает, что при отсутствии
интерфейса пользователя оценка будет снижена, а при его наличии повышена.
Мотивом для его создания может быть стремление к совершенству, желание превратить
эту учебную разработку в полноценный продукт и т. д.

В том случае, если приложение не разрабатывалось, нужно заранее подготовить
несколько типичных запросов к базе данных и сохранить их в отдельных текстовых
файлах. Для демонстрации этих запросов их можно вызывать как извне утилиты psql,
так и изнутри нее. Например, для выполнения запроса, содержащегося в файле
`file_with_request.sql`, нужно сделать так:

```bash
psql -d your_database -f file_with_request.sql -U postgres
```

Для выполнения запроса, сохраненного в файле, изнутри утилиты psql, можно
сделать так (конечно, нужно учитывать, в каком каталоге находится этот файл,
возможно, будет удобнее и проще написать полный путь к файлу):

```sql
\i  file_with_request.sql
```

### Работа

[Наверх](#ссылки)

---

</details>

## Финальная работа 4

<details>
<summary>Сама работа</summary>

---

### Задание

Подготовить отчет.

Нужно поместить в него:

- краткое описание предметной области и требования к базе данных
  (т. е. результат выполнения самого первого домашнего задания);
- концептуальную, логическую и физическую схемы базы данных;
- типичные запросы к базе данных, сохраненные в виде отдельных текстовых файлов;
- резервную копию базы данных, созданную с помощью утилиты pg_dump, входящей
  в состав PostgreSQL. С помощью этой копии можно будет быстро восстановить вашу
  базу данных и те данные, которые вы в нее ввели.

Краткое описание предметной области, концептуальную и логическую схемы базы
данных нужно поместить в документ в формате doc или pdf (как вам удобнее).

Физическую схему базы данных нужно представить в отчете в виде текстового
файла, содержащего SQL-команды для создания таблиц, представлений (если они
используются), триггеров и триггерных функций. В этом файле должны быть сделаны
комментарии. Имея такой файл, создать все объекты базы данных можно будет таким
образом:

```bash
psql -d your_database -f file_with_SQL_commands.sql -U postgres
```

В качестве примера такого файла можно посмотреть файл `adj_list.sql`
в каталоге `/home/WORK/Databases/Admin_DB/UTF-8`.

Для каждого отношения (таблицы) необходимо указать номер нормальной формы,
в которой это отношение находится, и кратко (буквально, в два слова) обосновать,
из чего это следует. Если какое-либо отношение не находится хотя бы в 3НФ,
необходимо обосновать, почему принято такое проектное решение.

### Работа

[Наверх](#ссылки)

---

</details>
