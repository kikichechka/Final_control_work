# Задание
1. Используя команду cat в терминале операционной системы Linux, создать два файла Домашние животные (заполнив файл собаками, кошками, хомяками) и Вьючные животными заполнив файл Лошадьми, верблюдами и ослы), а затем объединить их. Просмотреть содержимое созданного файла. Переименовать файл, дав ему новое имя (Друзья человека).
cat > Домашние_животные
собака 
кошка 
хомяк
cat > Вьючные_животные
лошадь
верблюд
осел
cat > Домашние_животные Вьючные_животные Все_животные
cat Все животные
mv Все_животные Друзья_человека

2. Создать директорию, переместить файл туда
sudo mkdir Животные
sudo mv Друзья_человека Животные/

3. Подключить дополнительный репозиторий MySQL. Установить любой пакет из этого репозитория.
sudo wget -c https://dev.mysql.com/get/mysql-apt-config_0.8.24-1_all.deb
sudo dpkg -i mysql-apt-config_0.8.24-1_all.deb

4. Установить и удалить deb-пакет с помощью dpkg.
sudo dpkg -r mysql-server-8.0

5. Выложить историю команд в терминале ubuntu
history

6. Нарисовать диаграмму, в которой есть класс родительский класс, домашние животные и вьючные животные, в составы которых в случае домашних животных войдут классы: собаки, кошки, хомяки, а в класс вьючные животные войдут: Лошади, верблюды и ослы).
<a href="diagramm/ClassDiagramm.drawio.png" target="_blank">диаграмма</a>.

7. В подключенном MySQL репозитории создать базу данных “Друзья человека”
/home/ekaterina# mysql
mysql> show databases;
mysql> CREATE DATABASE human_friends;
mysql> show databases;

8. Создать таблицы с иерархией из диаграммы в БД
mysql> use human_friends;
Database changed
mysql> CREATE TABLE `animal`
    -> (
    -> `id` Int NOT NULL AUTO_INCREMENT,
    -> `animal_name` Varchar(20) NOT NULL,
    -> PRIMARY KEY (`id`)
    -> )
    -> ;

9. Заполнить низкоуровневые таблицы именами(животных), командами которые они выполняют и датами рождения
mysql> ALTER TABLE `mule` ADD CONSTRAINT `pack_to_mule` FOREIGN KEY (`id_pack`) REFERENCES `pack_animal` (`id`) ON DELETE RESTRICT ON UPDATE RESTRICT;
mysql> INSERT INTO pack_animal (pack_name, livePlace, id_animal) VALUES ('Вьючные', 'Скотный двор', 1);
mysql> INSERT INTO cat (cat_name, date_birth, commands, `color`, id_home)
    -> VALUES
    -> ('Мурка', '2020-09-09', 'сидеть', 'рыжий', 1),
    -> ('Барсик', '2022-12-12', 'стоять', 'серый', 1),
    -> ('Степка','2023-01-01','лежать','белый', 1);

10. Удалить из таблицы верблюдов, т.к. верблюдов решили перевезти в другой питомник на зимовку. Объединить таблицы лошади, и ослы в одну таблицу.
mysql> DELETE FROM camel;
mysql> SELECT id, name, commands, color, id_pack
    -> FROM horse
    -> UNION
    -> SELECT id, name, commands, color, id_pack
    -> FROM mule;

11. Создать новую таблицу “молодые животные” в которую попадут все животные старше 1 года, но младше 3 лет и в отдельном столбце с точностью до месяца подсчитать возраст животных в новой таблице

mysql> CREATE TABLE young_animal (
    -> id int NOT NULL,
    -> name varchar(60),
    -> date_birth Datetime,
    -> commands varchar(20),
    -> color varchar(20),
    -> age varchar(20));

mysql> INSERT INTO young_animal (id, name, date_birth, commands, color, age)
    -> SELECT id, cat_name, date_birth, commands, color,
    -> CONCAT(CAST(TIMESTAMPDIFF(YEAR, date_birth, NOW()) AS CHAR), " лет ",
    -> CAST(MOD(TIMESTAMPDIFF(NONTH, date_birth, NOW()), 12) AS CHAR), " мес." AS age
    -> FROM cat
    -> WHERE TIMESTAMPDIFF(MONTH, date_birth, NOW()) BETWEEN 12 AND 36; 

mysql> INSERT INTO young_animal (id, name, date birth, commands, color, age)
    -> SELECT id, dog_name, date_birth, commands, color,
    -> CONCAT(CAST(TIMESTAMPDIFF(YEAR, date_birth, NOW()) AS CHAR), " лет ",
    -> CAST(MOD(TIMESTAMPDIFF(MONTH, date_birth, NOW()), 12) AS CHAR), " мес.") AS age
    -> FROM dog
    -> WHERE TIMESTAMPDIFF(MONTH, date_birth, NOW()) BETWEEN 12 AND 36;

mysql> INSERT INTO young_animal (id, name, date_birth, commands, color, age)
    -> SELECT id, name, date_birth, commands, color,
    -> CONCAT(CAST(TIMESTAMPDIFF(YEAR, date_birth, NOW()) AS CHAR), " ner ",
    -> CAST(MOD(TIMESTAMPDIFF(MONTH, date_birth, NOW()), 12) AS CHAR), " мес.") AS age
    -> FROM horse
    -> WHERE TIMESTAMPDIFF(MONTH, date birth, NOW()) BETWEEN 12 AND 36
    -> UNION ALL
    -> SELECT id, name, date_birth, commands, color,
    -> CONCAT(CAST(TIMESTAMPDIFF(YEAR, date birth, NOW()) AS CHAR), " лет ", MOD(TIM  -> CAST(MOD(TIMESTAMPDIFF(MONTH, date birth, NOW()), 12) AS CHAR), " mec.") AS age
    -> FROM mule
    -> WHERE TIMESTAMPDIFF(MONTH, date_birth, NOW()) BETWEEN 12 AND 36;

12. Объединить все таблицы в одну, при этом сохраняя поля, указывающие на прошлую принадлежность к старым таблицам.
mysql>
mysql> SELECT * FROM (SELECT id, cat_name, dog_name, NULL as hamster_name, NULL as name, date birth, commands, color FROM (
-> SELECT id, cat_name, NULL as dog_name, NULL AS hamster_name, NULL AS name, date_birth, commands, color FROM cat
-> UNION all
-> SELECT id, NULL as cat_name, dog_name, NULL AS hamster_name, NULL AS name, date_birth, commands, color FROM dog) A
-> UNION ALL
-> SELECT id, NULL as cat_name, NULL as dog_name, hamster_name, NULL AS name, date_birth, commands, color FROM hamster) В
-> UNION ALL
-> SELECT * FROM (
-> SELECT id, NULL AS cat_name, NULL AS dog_name, NULL AS hamster_name, name, date_birth, commands, color FROM horse
-> UNION ALL
-> SELECT id, NULL AS cat_name, NULL AS dog_name, NULL AS hamster name, name, date_birth, commands, color FROM mule
-> ) C; 
