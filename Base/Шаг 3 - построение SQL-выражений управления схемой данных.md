2022-10-2320:14
Tags: #DataBase #MySQL 

__
# Шаг 3 - построение SQL-выражений управления схемой данных

Вот выражение для создания таблицы Person
```MySQL
CREATE TABLE person
(person_id SMALLINT UNSIGNED,
 fname VARCHAR(20),
 lname VARCHAR(20),
 gender CHAR(1),
 birth_date DATE,
 address VARCHAR(30),
 city VARCHAR(20),
 state VARCHAR(20),
 country VARCHAR(20),
 postal_code VARCHAR(20)
 CONSTRAINT pk_person PRIMARY KEY (peson_id)
);
```
В этом выражении должно быть понятно всё, кроме последнего элемента. При описании таблицы необходимо сообщить серверу БД, какой столбец или столбцы будут играть роль первичного ключа таблицы. Осуществляется это путем создания  *ограничения (constraint)* для таблицы. В описании таблицы можно добавить ограничение одного из первичных типов. Даннгое ограничение является *ограничением первичного ключа (primary-key constraint)*. Оно накладывается на столбец person_id и получается имя pk_person. 

Говоря об ограничениях. Можно добавить ограничение для допустимых значений определенных столбцов. Это *проверочное ограничение (check constaint)*, ограничивающее допустимые значения конкретного столбца. MySQL позволяет сделать так
```MySQL
gender CHAR(1) CHECK (gender in ('M', 'F')),
```

На большинстве сервером БД проверочные ограничения работают соответствующим образом, а сервер MySQL допускает описание проверочных ограничений, но не выполняется их проверку. Но MySQL предоставляет другой символьный типа данных - enum, который вводит проверочное ограничение в описании типа. Вот так выглядело бы для описание gender
```MySQL
gender ENUM('M', 'F'),
```

Вот так выглядит создание таблицы Person с введением типов данных enum для стобца gender
```MySQL
mysql> CREATE TABLE person
    -> (person_id SMALLINT UNSIGNED,
    -> fname VARCHAR(20),
    -> lname VARCHAR(20),
    -> gender ENUM('M', 'F'),
    -> birth_date DATE,
    -> address VARCHAR(30),
    -> city VARCHAR(20),
    -> state VARCHAR(20),
    -> country VARCHAR(20),
    -> postal_code VARCHAR(20),
    -> CONSTRAINT pk_person PRIMARY KEY (person_id)
    -> );
```
Результат
```MySQL
Query OK, 0 rows affected (0.01 sec)
```

Если потребуется убедиться, что таблица person действительно существует, можно использоваться командой describe
```MySQL
mysql> DESC person;
```
Результат
```MySQL
+-------------+-------------------+------+-----+---------+-------+
| Field       | Type              | Null | Key | Default | Extra |
+-------------+-------------------+------+-----+---------+-------+
| person_id   | smallint unsigned | NO   | PRI | NULL    |       |
| fname       | varchar(20)       | YES  |     | NULL    |       |
| lname       | varchar(20)       | YES  |     | NULL    |       |
| gender      | enum('M','F')     | YES  |     | NULL    |       |
| birth_date  | date              | YES  |     | NULL    |       |
| address     | varchar(30)       | YES  |     | NULL    |       |
| city        | varchar(20)       | YES  |     | NULL    |       |
| state       | varchar(20)       | YES  |     | NULL    |       |
| country     | varchar(20)       | YES  |     | NULL    |       |
| postal_code | varchar(20)       | YES  |     | NULL    |       |
+-------------+-------------------+------+-----+---------+-------+

10 rows in set (0.01 sec)
```
- 1, 2 столбцы очевидны. 
- 3 столбец показывается, можно ли пропустить значение (занести туда NULL) при вводе данных в таблицу. 
- 4 столбец показывает какое поле(поля) является первичным ключом. 
- 5 столбец показывает, будет ли определенный столбец заполнен значение по умолчанию, если его значение будет пропущенно. 
- 6 столбец содержит любую другую дополнительную информацию.

Самое время создать таблицу favorite_food
```MySQL
mysql> CREATE TABLE favorite_food
    -> (person_id SMALLINT UNSIGNED,
    -> food VARCHAR(20),
    -> CONSTRAINT fk_favorite_food PRIMARY KEY (person_id, food),
    -> CONSTRAINT fk_person_id FOREIGN KEY (person_id)
    -> REFERENCES person (person_id)
    -> );

Query OK, 0 rows affected (0.01 sec)
```
Очень похоже на создание person, за несколькими исключениями
- Так как у человека могут быть несколько любимых блюд, поэтому первичный ключ составной (состоит из person_id и food)
- Таблица favorite_food содержит другой тип ограничений - *ограничение внешнего ключа (foreign-key constraint*. Оно ограничивает значения стобца person_id таблицы favorite_food, позволяя ему включать только те значения, которые есть в таблице person. При таком ограничении не получиться включить в таблицу favorite_food строку, показывающую, что person_id = 27 любит пиццу, если в таблице person нет строки со значение 27 для person_id.

*Если во время создания таблицы ограничение внешнего ключа не было указано, то его можно указать вызвав alter table*

```MySQL
mysql> DESC favorite_food;

+-----------+-------------------+------+-----+---------+-------+
| Field     | Type              | Null | Key | Default | Extra |
+-----------+-------------------+------+-----+---------+-------+
| person_id | smallint unsigned | NO   | PRI | NULL    |       |
| food      | varchar(20)       | NO   | PRI | NULL    |       |
+-----------+-------------------+------+-----+---------+-------+

2 rows in set (0.00 sec)

```
Результат.
__
### Zero-Links
- [[Cоздание таблиц MySQL]]

__
### Links
- 

