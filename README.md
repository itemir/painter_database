# Painter and Art Movements Database
This is a open source database for painters, art movements and paintings that have been collected from the Internet.

You can import the database into MySQL in the following way:

```
~$ git clone https://github.com/itemir/painter_database
~$ cd painter_database
~/painter_database$ mysqladmin -u root -p create painters
Enter password:
~/painter_database$ mysql -u root -p painters < painter_database.sql
Enter password:
~/painter_database$ mysql -u root -p
Enter password:
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 197109
Server version: 5.7.32-0ubuntu0.18.04.1 (Ubuntu)

Copyright (c) 2000, 2020, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql> use painters;
Reading table information for completion of table and column names
You can turn off this feature to get a quicker startup with -A

Database changed
mysql> SHOW TABLES;
+------------------------+
| Tables_in_painters     |
+------------------------+
| art_movements          |
| painters               |
| painters_art_movements |
| paintings              |
+------------------------+
4 rows in set (0.00 sec)

mysql>
```

There are 68 art movements, 263 painters and 2146 paintings in the database:

```
mysql> SELECT COUNT(*) FROM painters;
+----------+
| COUNT(*) |
+----------+
|      263 |
+----------+
1 row in set (0.00 sec)

mysql> SELECT COUNT(*) FROM paintings;
+----------+
| COUNT(*) |
+----------+
|     2146 |
+----------+
1 row in set (0.00 sec)

mysql> SELECT COUNT(*) FROM art_movements;
+----------+
| COUNT(*) |
+----------+
|       68 |
+----------+
1 row in set (0.00 sec)

mysql>
```

You can query painters/artists and their paintings. Painting names are not populated (welcome to do so and submit pull requests).

```
mysql> SELECT * FROM painters WHERE LOWER(name) LIKE '%van gogh%';
+-----+------------------+-----------------------------------------------+
| id  | name             | url                                           |
+-----+------------------+-----------------------------------------------+
| 265 | Vincent van Gogh | http://en.wikipedia.org/wiki/Vincent_van_Gogh |
+-----+------------------+-----------------------------------------------+
1 row in set (0.00 sec)

mysql> SELECT COUNT(*) FROM paintings WHERE painter_id=265;
+----------+
| COUNT(*) |
+----------+
|       32 |
+----------+
1 row in set (0.01 sec)

mysql> SELECT * FROM paintings WHERE painter_id=265 LIMIT 3;
+------+------+----------------------------------------------------------------------------------------------------------------+------------+
| id   | name | url                                                                                                            | painter_id |
+------+------+----------------------------------------------------------------------------------------------------------------+------------+
| 1869 |      | http://upload.wikimedia.org/wikipedia/commons/a/a8/Van_Gogh_Self-Portrait_with_Straw_Hat_1887-Metropolitan.jpg |        265 |
| 1870 |      | http://upload.wikimedia.org/wikipedia/commons/b/b1/Van-willem-vincent-gogh-die-kartoffelesser-03850.jpg        |        265 |
| 1871 |      | http://upload.wikimedia.org/wikipedia/commons/8/84/Skull_with_a_Burning_Cigarette.jpg                          |        265 |
+------+------+----------------------------------------------------------------------------------------------------------------+------------+
3 rows in set (0.00 sec)

mysql>
```

You can also write more complex queries using JOINs and find painters associated with different art movements. Note that associations between painters and art movements are pretty loose.

```
mysql> SELECT art_movements.name, painters.name FROM painters
    -> INNER JOIN painters_art_movements ON painters.id=painters_art_movements.painter_id
    -> INNER JOIN art_movements ON art_movements.id=painters_art_movements.movement_id
    -> WHERE art_movements.name="Impressionism";
+---------------+---------------------------+
| name          | name                      |
+---------------+---------------------------+
| Impressionism | Albert Gleizes            |
| Impressionism | Alfred Sisley             |
| Impressionism | Berthe Morisot            |
| Impressionism | Camille Pissarro          |
| Impressionism | Childe Hassam             |
| Impressionism | Claude Monet              |
| Impressionism | Edgar Degas               |
| Impressionism | Eugène Boudin             |
| Impressionism | Frederick McCubbin        |
| Impressionism | Henri Matisse             |
| Impressionism | John Henry Twachtman      |
| Impressionism | Mary Cassatt              |
| Impressionism | Paul Cézanne              |
| Impressionism | Pierre Puvis de Chavannes |
| Impressionism | Pierre-Auguste Renoir     |
| Impressionism | Raoul Dufy                |
| Impressionism | Theodore Robinson         |
| Impressionism | Valentin Serov            |
| Impressionism | Édouard Manet             |
+---------------+---------------------------+
19 rows in set (0.00 sec)

mysql>
```
