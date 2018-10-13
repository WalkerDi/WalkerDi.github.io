---
layout: post
title:  "MySQL删除重复记录只保留一条"
categories: MySQL 
tags:  MySQL 数据库
---

* content
{:toc}

## MySQL删除重复记录只保留一条

如何删除数据库中重复数据，只保留其中一条记录，下面sql语句是删除用户表`ltb_user`中手机号`phone_number`不为空且企业id`enterprise_id`是256的重复的记录，但保留主键`ltb_user_id`最小的一条，这里可以保留最大一条，可以用函数max()，sql语句如下：





```sql
DELETE
FROM
	ltb_user
WHERE
	phone_number IN (
		SELECT
			phone_number
		FROM
			(
				SELECT
					phone_number
				FROM
					ltb_user
				WHERE
					phone_number != ''
				AND enterprise_id = 265
				GROUP BY
					phone_number
				HAVING
					count(phone_number) > 1
			) a
	)
AND ltb_user_id NOT IN (
	SELECT
		ltb_user_id
	FROM
		(
			SELECT
				min(ltb_user_id) AS ltb_user_id
			FROM
				ltb_user
			WHERE
				phone_number != ''
			AND enterprise_id = 265
			GROUP BY
				phone_number
			HAVING
				count(phone_number) > 1
		) b
)
```