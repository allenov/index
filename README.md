# index
<p>otus=# create table test as <br>
select generate_series as id<br>
,generate_series::text || (random() * 10)::text as col2<br>
, (array['Yes', 'No', 'Maybe'])[floor(random() * 3 + 1)] as is_okey<br>
from generate_series(1, 50000);<br>
SELECT 50000<br>
otus-# select id from test where id = 100;<br>
 QUERY PLAN <br>
-------------------------------------------------------<br>
 Seq Scan on test (cost=0.00..1008.00 rows=1 width=4)<br>
 Filter: (id = 100)<br>
(2 rows)</p>

<p>otus=# create index test_id on test(id);<br>
CREATE INDEX<br>
otus=# explain <br>
select id from test where id = 100;<br>
 QUERY PLAN <br>
-------------------------------------------------------------------------<br>
 Index Only Scan using test_id on test (cost=0.29..4.31 rows=1 width=4)<br>
 Index Cond: (id = 100)<br>
(2 rows)</p>

<br>
<p>otus=# create index index_id_is_okay on test(id, is_okey);<br>
CREATE INDEX</p>

<p>otus=# explain select * from test where id = 101 and is_okey = 'Yes';<br>
 QUERY PLAN <br>
------------------------------------------------------------------------------<br>
 Index Scan using index_id_is_okay on test (cost=0.29..8.31 rows=1 width=31)<br>
 Index Cond: ((id = 101) AND (is_okey = 'Yes'::text))<br>
(2 rows)</p>

<p>otus=# create index test_id_150 on test(id) where id <150;<br>
CREATE INDEX</p>

<p>otus=# explain select id from test where id=101;<br>
 QUERY PLAN <br>
-----------------------------------------------------------------------------<br>
 Index Only Scan using test_id_150 on test (cost=0.14..4.16 rows=1 width=4)<br>
 Index Cond: (id = 101)<br>
(2 rows)</p>

<p>demo=# select city, to_tsvector(city)<br>
demo-# from airports;</p>

<p> Магнитогорск | 'магнитогорск':1<br>
 Пермь | 'пермь':1<br>
 Сургут | 'сургут':1<br>
 Брянск | 'брянск':1<br>
 Минеральные Воды | 'воды':2 'минеральные':1<br>
 Ставрополь | 'ставрополь':1<br>
 Астрахань | 'астрахань':1<br>
 Нижневартовск | 'нижневартовск':1<br>
 Екатеринбург | 'екатеринбург':1<br>
 Москва | 'москва':1<br>
 Воронеж | 'воронеж':1<br>
 Москва | 'москва':1<br>
 Сыктывкар | 'сыктывкар':1<br>
 Самара | 'самара':1<br>
 Москва | 'москва':1<br>
 Тюмень | 'тюмень':1<br>
 Нижний Новгород | 'нижний':1 'новгород':2<br>
 Томск | 'томск':1<br>
 Усть-Илимск | 'илимск':3 'усть':2 'усть-илимск':1<br>
 Норильск | 'норильск':1<br>
 Архангельск | 'архангельск':1<br>
 Саратов | 'саратов':1<br>
 Новый Уренгой | 'новый':1 'уренгой':2<br>
 Ноябрьск | 'ноябрьск':1<br>
 Ухта | 'ухта':1<br>
 Усинск | 'усинск':1<br>
 Нарьян-Мар | 'мар':3 'нарьян':2 'нарьян-мар':1<br>
 Псков | 'псков':1<br>
 Когалым | 'когалым':1<br>
 Красноярск | 'красноярск':1<br>
 Урай | 'урай':1<br>
 Иваново | 'иваново':1<br>
 Удачный | 'удачный':1<br>
 Комсомольск-на-Амуре | 'амуре':4 'комсомольск':2 'комсомольск-на-амуре':1 'на':3<br>
 Анадырь | 'анадырь':1</p>

<p>select city, to_tsvector(city) @@ to_tsquery ('новгород') from airports;<br>
 Москва | f<br>
 Тюмень | f<br>
 Нижний Новгород | t<br>
 Томск | f</p>
