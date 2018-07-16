# SQL
## 1.Склеиваем результат запроса с табицей. Для получения имен по ID
```select n.poddomen, q1.`count(url)` from (select broker_id, count(url) from ch_users_stats_new where created_at >= date('2018-05-01') group by broker_id) as q1 join ch_brokers n on n.broker_id = q1.broker_id order by q1.`count(url)` desc```

## 2. Замена подстроки во всех строках
```update ch_brokers set custom_broker_pairs = replace(custom_broker_pairs, 'amp;', '')```

## 3. Выборка количества запросов по часам
```'select hour(created_at) as hour, count(*) as count from ch_users_stats_new where created_at >= :start and created_at <= :end and broker_id = :broker_id group by hour( created_at )';```

## 4. Применение переменных, выборка первых двух строк за каждый день. Нужно иметь правильные ключи, иначе будут выбираться 1 и 3 строки.
```select *, date_format(created_at, '%Y-%m-%d') as pd, @num := if(date_format(created_at, '%Y-%m-%d') = @dp, @num+1, 1) as `num`, @dp := date_format(created_at, '%Y-%m-%d') as dp where `type`=1 and `author_id` = 6 and `lang`=:lang and (`publication_date` < NOW() or `publication_date` is null) group by "pd, title" having `num` <= 2" order by "created_at desc";```

## 5. Копируем данные из одной таблицы в другую, подставляя текущее время
```insert into ch_signals_past (ma10, ma20, ma50, ma100, macd, bbands, ichimoku, stochastic, williams, zigzag, pair, group_id, `date`) select ma10, ma20, ma50, ma100, macd, bbands, ichimoku, stochastic, williams, zigzag, pair, group_id, NOW() as `date` from ch_signals where (pair like 'EURUSD' or pair like 'gbpusd' or pair like 'eurjpy') and group_id=5```

## 6. Создаём событие которе будет выполнятся каждый час.
### А) Включаем планировщик
```SET GLOBAL event_scheduler = ON;```
### Б) Добавляем событие
```CREATE EVENT `signals_history`
    ON SCHEDULE
      EVERY 1 HOUR
    DO
      insert into ch_signals_past (ma10, ma20, ma50, ma100, macd, bbands, ichimoku, stochastic, williams, zigzag, pair, group_id, `date`) select ma10, ma20, ma50, ma100, macd, bbands, ichimoku, stochastic, williams, zigzag, pair, group_id, NOW() as `date` from ch_signals where (pair like 'EURUSD' or pair like 'gbpusd' or pair like 'eurjpy') and group_id=5;```
