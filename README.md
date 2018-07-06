# SQL
## 1.Склеиваем результат запроса с табицей. Для получения имен по ID
```select n.poddomen, q1.`count(url)` from (select broker_id, count(url) from ch_users_stats_new where created_at >= date('2018-05-01') group by broker_id) as q1 join ch_brokers n on n.broker_id = q1.broker_id order by q1.`count(url)` desc```

## 2. Замена подстроки во всех строках
```update ch_brokers set custom_broker_pairs = replace(custom_broker_pairs, 'amp;', '')```

## 3. Выборка количества запросов по часам
```'select hour(created_at) as hour, count(*) as count from ch_users_stats_new where created_at >= :start and created_at <= :end and broker_id = :broker_id group by hour( created_at )';```

## 4. Применение переменны, выборка первых двух строк за каждый день. Нужно иметь правильные ключи, иначе будут выбираться 1 и 3 строки.
```select *, date_format(created_at, '%Y-%m-%d') as pd, @num := if(date_format(created_at, '%Y-%m-%d') = @dp, @num+1, 1) as `num`, @dp := date_format(created_at, '%Y-%m-%d') as dp where `type`=1 and `author_id` = 6 and `lang`=:lang and (`publication_date` < NOW() or `publication_date` is null) group by "pd, title" having `num` <= 2" order by "created_at desc";
```
