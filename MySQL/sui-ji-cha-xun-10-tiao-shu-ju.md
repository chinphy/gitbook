# 随机查询10条数据
---
思路就是生成10个有效随机ID
```sql
    SELECT
      *
    FROM
      t_user AS t1
    JOIN (
      SELECT
      (
        FLOOR(
          (
            RAND() * (
              (SELECT MAX(uid) FROM t_user) - (SELECT MIN(uid) FROM t_user)
             )
           )
         ) + (SELECT MIN(uid) FROM t_user)
       ) AS uid
      FROM
        t_user
      LIMIT 10
    ) AS t2 ON t1.uid = t2.uid
    ORDER BY
      t1.uid;
```