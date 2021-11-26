先放在这里，以后有时间再整理

```sql
SELECT
 a.date AS date,
 a.sum AS daily,
 @i := @i + a.sum AS daily_quantity 
FROM
 (
 SELECT
  DATE_FORMAT( create_time, '%Y-%m-%d' ) AS date,
  sum( cans_number ) AS sum 
 FROM
  hyproca_member_cans_number 
 WHERE
  is_deleted = 0 
  AND cans_number > 0 
  AND create_time >= '2021-08-09' 
 GROUP BY
  DATE_FORMAT( create_time, '%Y-%m-%d' ) 
 ) AS a,
 ( SELECT @i := 0 ) AS b
```
