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

Here is a full list of painters and number of paintings captured for each:
```
mysql> SELECT painters.name, COUNT(paintings.id) FROM painters
    -> INNER JOIN paintings ON painters.id=paintings.painter_id
    -> GROUP BY painters.name;
+----------------------------------+---------------------+
| name                             | COUNT(paintings.id) |
+----------------------------------+---------------------+
| Ad Reinhardt                     |                   7 |
| Agnes Martin                     |                   3 |
| Albert Bierstadt                 |                   8 |
| Albert Gleizes                   |                  12 |
| Albert Marquet                   |                   7 |
| Albert Pinkham Ryder             |                   9 |
| Albrecht Dürer                   |                   9 |
| Aleksander Balos                 |                   2 |
| Alex Katz                        |                   7 |
| Alexej von Jawlensky             |                  11 |
| Alfred Henry Maurer              |                   4 |
| Alfred Sisley                    |                  16 |
| Amedeo Modigliani                |                  14 |
| André Masson                     |                   2 |
| Andrea Mantegna                  |                   8 |
| Andrew Wyeth                     |                   6 |
| Angelica Kauffman                |                  10 |
| Anthony van Dyck                 |                  16 |
| Antoine Watteau                  |                   7 |
| Antoine-Jean Gros                |                   6 |
| Arnold Böcklin                   |                   9 |
| Arshile Gorky                    |                   5 |
| Artemisia Gentileschi            |                   5 |
| Arthur Dove                      |                   7 |
| Arthur Streeton                  |                   5 |
| August Macke                     |                   7 |
| Bahram Alivandi                  |                   3 |
| Balthus                          |                   2 |
| Barnett Newman                   |                   2 |
| Ben Shahn                        |                   2 |
| Berthe Morisot                   |                  15 |
| Bradley Walker Tomlin            |                   6 |
| Brice Marden                     |                   3 |
| Bridget Riley                    |                   3 |
| Camille Pissarro                 |                  31 |
| Caravaggio                       |                  18 |
| Caspar David Friedrich           |                  11 |
| Chaim Soutine                    |                   7 |
| Charles Camoin                   |                  10 |
| Charles Conder                   |                   5 |
| Charles Demuth                   |                  12 |
| Childe Hassam                    |                   9 |
| Claude Lorrain                   |                   6 |
| Claude Monet                     |                  48 |
| Clyfford Still                   |                   5 |
| Conrad Marca-Relli               |                   6 |
| Cy Twombly                       |                   6 |
| David Alfaro Siqueiros           |                   4 |
| Diego Rivera                     |                  33 |
| Diego Velázquez                  |                  12 |
| Edgar Degas                      |                  33 |
| Édouard Manet                    |                  40 |
| Edvard Munch                     |                  41 |
| Edward Hopper                    |                  10 |
| Egon Schiele                     |                  24 |
| El Greco                         |                   9 |
| Ellsworth Kelly                  |                   8 |
| Emil Nolde                       |                   4 |
| Eric Fischl                      |                   9 |
| Erich Heckel                     |                   2 |
| Ernst Ludwig Kirchner            |                  20 |
| Esteban Vicente                  |                   9 |
| Eugène Boudin                    |                   7 |
| Eugène Delacroix                 |                  31 |
| Fairfield Porter                 |                  11 |
| Félicien Rops                    |                   8 |
| Fernand Léger                    |                   9 |
| Filippo Lippi                    |                   4 |
| Fra Angelico                     |                   5 |
| Francis Bacon                    |                   6 |
| Francis Hayman                   |                   4 |
| Francis Picabia                  |                   9 |
| Francisco Goya                   |                   8 |
| François Boucher                 |                  10 |
| Frank Auerbach                   |                   5 |
| Frank Stella                     |                   6 |
| Frans Hals                       |                  23 |
| František Kupka                  |                   3 |
| Franz Kline                      |                   7 |
| Franz Marc                       |                   8 |
| Frederic Edwin Church            |                  10 |
| Frederick McCubbin               |                   5 |
| Frida Kahlo                      |                   9 |
| Friedel Dzubas                   |                   9 |
| Fritz Bleyl                      |                   1 |
| Gabriele Münter                  |                   9 |
| Gene Davis                       |                   4 |
| George Bellows                   |                  24 |
| George Grosz                     |                   5 |
| George Tooker                    |                   4 |
| Georges Braque                   |                   8 |
| Georges Mathieu                  |                   6 |
| Georges Rouault                  |                   7 |
| Georges Seurat                   |                  14 |
| Georgia O'Keeffe                 |                   4 |
| Gino Severini                    |                   8 |
| Giorgio de Chirico               |                   4 |
| Giorgio Morandi                  |                   8 |
| Giorgione                        |                  14 |
| Giovanni Battista Tiepolo        |                   4 |
| Giovanni Bellini                 |                  11 |
| Grace Hartigan                   |                   3 |
| Grant Wood                       |                   7 |
| Gustav Klimt                     |                  16 |
| Gustave Courbet                  |                  29 |
| Gustave Moreau                   |                   7 |
| Hans Hofmann                     |                   7 |
| Hans Holbein the Younger         |                  15 |
| Hans Richter                     |                   7 |
| Hedda Sterne                     |                   6 |
| Helen Frankenthaler              |                  12 |
| Henri Fantin-Latour              |                  21 |
| Henri Matisse                    |                  26 |
| Henri Rousseau                   |                  19 |
| Hieronymus Bosch                 |                   6 |
| Hilma af Klint                   |                   6 |
| Ilya Repin                       |                   6 |
| Ivan Aivazovsky                  |                  13 |
| J. M. W. Turner                  |                  10 |
| Jack Tworkov                     |                  11 |
| Jackson Pollock                  |                   2 |
| Jacques-Louis David              |                  32 |
| James Abbott McNeill Whistler    |                   7 |
| James Brooks                     |                   6 |
| James Ensor                      |                   4 |
| Jan Toorop                       |                   5 |
| Jan van Eyck                     |                   4 |
| Jasper Johns                     |                   4 |
| Jean Arp                         |                   1 |
| Jean Auguste Dominique Ingres    |                  18 |
| Jean Dubuffet                    |                   5 |
| Jean Metzinger                   |                  16 |
| Jean-Baptiste-Camille Corot      |                   6 |
| Jean-Baptiste-Siméon Chardin     |                   4 |
| Jean-Honoré Fragonard            |                  12 |
| Jean-Michel Basquiat             |                   2 |
| Jim Dine                         |                   2 |
| Jimmy Baker                      |                  10 |
| Jimmy Ernst                      |                   5 |
| Joan Miró                        |                   5 |
| Joan Mitchell                    |                   6 |
| Joaquín Torres García            |                   2 |
| Johannes Vermeer                 |                   6 |
| John Constable                   |                   8 |
| John Frederick Kensett           |                   7 |
| John Henry Twachtman             |                   4 |
| John Marin                       |                   1 |
| John Singer Sargent              |                  16 |
| John Singleton Copley            |                  11 |
| John Steuart Curry               |                   3 |
| Jorge Azar                       |                   2 |
| José Clemente Orozco             |                   5 |
| Josef Albers                     |                   5 |
| Joshua Reynolds                  |                  18 |
| Juan Gris                        |                  12 |
| Jusepe de Ribera                 |                   6 |
| Karl Bryullov                    |                   2 |
| Karl Schmidt-Rottluff            |                   5 |
| Käthe Kollwitz                   |                   9 |
| Katrin Alvarez                   |                   4 |
| Kay Sage                         |                   5 |
| Kazimir Malevich                 |                   4 |
| Kees van Dongen                  |                   1 |
| Kenneth Noland                   |                   2 |
| Kurt Schwitters                  |                   4 |
| Larry Poons                      |                   1 |
| Larry Zox                        |                   5 |
| Leonardo da Vinci                |                   8 |
| Leonora Carrington               |                   5 |
| Louise Élisabeth Vigée Le Brun   |                   9 |
| Lucian Freud                     |                   4 |
| Lyonel Feininger                 |                   3 |
| Man Ray                          |                   2 |
| Marc Chagall                     |                  11 |
| Marcel Duchamp                   |                   4 |
| Marianne von Werefkin            |                   1 |
| Mariano Akerman                  |                   4 |
| Mark Rothko                      |                   4 |
| Mark Tobey                       |                   2 |
| Marsden Hartley                  |                   5 |
| Marshall P. Baron                |                   3 |
| Mary Cassatt                     |                  20 |
| Masaccio                         |                   3 |
| Maurice de Vlaminck              |                   1 |
| Maurice Quentin de La Tour       |                   8 |
| Max Beckmann                     |                   2 |
| Max Ernst                        |                   2 |
| Max Pechstein                    |                   1 |
| Michelangelo                     |                   6 |
| Mikhail Vrubel                   |                   9 |
| Milton Avery                     |                   1 |
| Milton Resnick                   |                   1 |
| Morgan Russell                   |                   5 |
| Morris Louis                     |                   1 |
| Morten Andersen                  |                   6 |
| Nicolas Poussin                  |                  14 |
| Odilon Redon                     |                  14 |
| Oskar Kokoschka                  |                   2 |
| Othon Friesz                     |                   2 |
| Otto Dix                         |                   5 |
| Otto Mueller                     |                   7 |
| Pablo Picasso                    |                  21 |
| Paolo Uccello                    |                   4 |
| Patrick Henry Bruce              |                  11 |
| Paul Cézanne                     |                  14 |
| Paul Gauguin                     |                  19 |
| Paul Klee                        |                  13 |
| Pedro Nel Gómez                  |                   1 |
| Peter Paul Rubens                |                  12 |
| Philip Guston                    |                   1 |
| Piero della Francesca            |                   5 |
| Pierre Bonnard                   |                   5 |
| Pierre Puvis de Chavannes        |                   8 |
| Pierre-Auguste Renoir            |                   8 |
| Piet Mondrian                    |                   4 |
| Pieter Bruegel the Elder         |                   6 |
| Raoul Dufy                       |                   7 |
| Raphael                          |                  12 |
| Rembrandt                        |                   9 |
| Remedios Varo                    |                   7 |
| René Magritte                    |                   8 |
| Richard Anuszkiewicz             |                   2 |
| Richard Diebenkorn               |                   7 |
| Richard Estes                    |                   4 |
| Richard Pousette-Dart            |                   1 |
| Richard Tuttle                   |                   1 |
| Robert Campin                    |                   4 |
| Robert Delaunay                  |                  10 |
| Robert Motherwell                |                   1 |
| Robert Rauschenberg              |                   4 |
| Rogier van der Weyden            |                   4 |
| Ronald Davis                     |                   1 |
| Ronnie Landfield                 |                   5 |
| Salvador Dalí                    |                  10 |
| Salvator Rosa                    |                   4 |
| Sam Francis                      |                   1 |
| Sandro Botticelli                |                   4 |
| Santiago Martínez Delgado        |                   2 |
| Serge Poliakoff                  |                   1 |
| Sonia Delaunay                   |                   6 |
| Stanton Macdonald-Wright         |                   1 |
| Stuart Davis                     |                   1 |
| Susan Rothenberg                 |                   1 |
| Theo van Doesburg                |                   1 |
| Théodore Géricault               |                   9 |
| Theodore Robinson                |                   7 |
| Thomas Cole                      |                  18 |
| Thomas Eakins                    |                   7 |
| Thomas Gainsborough              |                  11 |
| Thomas Hart Benton               |                   5 |
| Thomas Moran                     |                   5 |
| Tintoretto                       |                   6 |
| Titian                           |                   8 |
| Tom Wesselmann                   |                   2 |
| Valentin Serov                   |                  15 |
| Vincent van Gogh                 |                  32 |
| Wassily Kandinsky                |                  11 |
| Willem de Kooning                |                   2 |
| William Baziotes                 |                   1 |
| William Hogarth                  |                  14 |
| Winslow Homer                    |                  22 |
| Yves Tanguy                      |                   6 |
| Zdzisław Beksiński               |                   6 |
+----------------------------------+---------------------+
263 rows in set (0.01 sec)

mysql>
```
