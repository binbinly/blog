# MySQL自增主键一定是连续的吗

众所周知，自增主键可以让聚集索引尽量地保持递增顺序插入，避免了随机查询，从而提高了查询效率。

但实际上，MySQL 的自增主键并不能保证一定是连续递增的。

> [原文](https://github.com/Snailclimb/JavaGuide/blob/main/docs/database/mysql/mysql-auto-increment-primary-key-continuous.md)