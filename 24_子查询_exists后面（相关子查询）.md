# 案例

``exists`` 查询的是布尔类型的值，查询结果为 **1** 或 **0**。

例：

```mysql
SELECT
	EXISTS (
		SELECT
			employee_id
		FROM
			employees
		WHERE
			salary = 300000
	);
```

1. 查询有员工的部门名

```mysql
SELECT
	department_name
FROM
	departments d
WHERE
	EXISTS (
		SELECT
			*
		FROM
			employees e
		WHERE
			e.department_id = e.departement_id
	);
```

使用内连接进行实现：

```mysql
SELECT
	e.department_id,
	department_name
FROM
	employees e
INNER JOIN departments d ON e.department_id = d.department_id;
```

使用 ``in`` 来实现：

```mysql
SELECT
	department_name
FROM
	departments d
WHERE
	d.department_id IN (
		SELECT
			department_id
		FROM
			employees
	);
```

2. 查询没有女朋友的男神信息

   使用 ``in`` 实现：

   ```mysql
   SELECT
   	boys.*
   FROM
   	boys
   WHERE
   	boys.id NOT IN (
   		SELECT
   			boy_friend_id
   		FROM
   			girls
   	);
   ```

   使用 ``exists`` 实现：

   ```mysql
   SELECT
   	boys.*
   FROM
   	boys
   WHERE
   	NOT EXISTS (
   		SELECT
   			girls.boy_friend_id
   		FROM
   			girls
   		WHERE
   			boys.id = girls.boy_friend_id
   	);
   ```

   