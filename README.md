# SQL
## Склеиваем результат запроса с табицей. Для получения имен по ID
1. select n.poddomen, q1.`count(url)` from (select broker_id, count(url) from ch_users_stats_new where created_at >= date('2018-05-01') group by broker_id) as q1 join ch_brokers n on n.broker_id = q1.broker_id order by q1.`count(url)` desc
