# Задание
У вас SQL база с таблицами:
1) Users(userId, age)
2) Purchases (purchaseId, userId, itemId, date)
3) Items (itemId, price).

Напишите SQL запросы для расчета следующих метрик:
### А
Какую сумму в среднем в месяц тратит:
- пользователи в возрастном диапазоне от 18 до 25 лет включительно
- пользователи в возрастном диапазоне от 26 до 35 лет включительно
#### Решение
Для диапазона от 18 до 25 лет:
```
SELECT date_part('month', p.date) as Month, AVG(i.price)
FROM purchases AS p 
JOIN users AS u 
on p.userid = u.userid
JOIN items AS i
ON p.itemid = i.itemid
WHERE u.age BETWEEN 18 and 25
GROUP BY date_part('month', p.date)
```
Для диапазона от 26 до 35 лет заменим строку:
```
WHERE u.age BETWEEN 18 and 25
```
на
```
WHERE u.age BETWEEN 26 and 35
```
### Б
В каком месяце года выручка от пользователей в возрастном диапазоне 35+ самая большая
#### Решение
```
SELECT date
FROM purchases AS p 
JOIN (SELECT * FROM users where age >= 35) AS u 
on p.userid = u.userid
JOIN items AS i
ON p.itemid = i.itemid
GROUP BY p.date
ORDER BY SUM(i.price) DESC
LIMIT 1
```
### В
Какой товар обеспечивает наибольший вклад в выручку за последний год
#### Решение
```
SELECT p.itemid, SUM(i.price)
FROM purchases AS p 
JOIN users AS u 
on p.userid = u.userid
JOIN items AS i
ON p.itemid = i.itemid
WHERE date_part('year', p.date) ='2021'
GROUP BY p.itemid
ORDER BY SUM(i.price) DESC
LIMIT 1
```
### Г
Топ-3 товаров по выручке и их доля в общей выручке за любой год
#### Решение
```
SELECT p.itemid,sum(i.price), SUM(i.price)/SUM(SUM(i.price)) OVER () as ratio
FROM purchases as p 
JOIN users as u 
on p.userid = u.userid
JOIN items AS i
ON p.itemid = i.itemid
WHERE date_part('year',p.date) = '2021'
GROUP BY p.itemid
ORDER BY SUM(i.price) DESC
LIMIT 3
```
