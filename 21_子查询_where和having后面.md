# 子查询 - where 和 having 后面

## 1. 标量子查询（单行子查询）

1. 案例1：谁的工资比 Abel 高？

   ```mysql
   SELECT
   	*
   FROM
   	employees
   WHERE
   	salary > (
   		SELECT
   			salary
   		FROM
   			employees
   		WHERE
   			last_name = 'Abel'
   	);
   ```

2. 案例2：返回 job_id 与 141号员工相同，salary 比 143号员工多的员工姓名，job_id 和工资：

   ```mysql
   SELECT
   	last_name,
   	job_id,
   	salary
   FROM
   	employees
   WHERE
   	job_id = (
   		SELECT
   			job_id
   		FROM
   			employees
   		WHERE
   			employee_id = 141
   	)
   AND salary > (
   	SELECT
   		salary
   	FROM
   		employees
   	WHERE
   		employee_id = 143
   );
   ```

3. 案例3：返回公司工资最少的员工的工资的 last_name，job_id 和 salary

   ```mysql
   SELECT
   	last_name,
   	job_id,
   	salary
   FROM
   	employees
   WHERE
   	salary = (
   		SELECT
   			MIN(salary)
   		FROM
   			employees
   	);
   ```

4. 案例4：查询最低工资大于50号部门最低工资的部门ID和其最低工资

   ```mysql
   SELECT
   	depertment_id,
   	MIN(salary)
   FROM
   	employees
   GROUP BY
   	depertment_id
   HAVING
   	MIN(salary) > (
   		SELECT
   			MIN(salary)
   		FROM
   			employees
   		WHERE
   			department_id = 50
   	);
   ```

## 2. 列子查询（多行子查询）

- 返回多行

- 使用多行比较操作符

- | 操作符    | 含义                           |
  | --------- | ------------------------------ |
  | IN/NOT IN | 等于列表中的任意一个           |
  | ANY/SOME  | 和子查询返回的某一个值进行比较 |
  | ALL       | 和子查询返回的所有值比较       |

- 体会ANY和ALL的区别

**注意：**

最常用的是 IN/NOT IN。

1. 案例1：返回 location_id 是 1400 或 1700 的部门中的所有员工姓名。

   ```mysql
   SELECT
   	last_name
   FROM
   	employees
   WHERE
   	department_id IN (
   		SELECT DISTINCT
   			department_id
   		FROM
   			employees
   		WHERE
   			location_id IN (1400, 1700)
   	);
   ```

2. 案例2：返回其它部门中比 job_id 为 IT_PROG 部门任一工资低的员工的员工号、姓名、job_id 以及 salary。

   ```mysql
   SELECT
   	employee_id,
   	last_name,
   	job_id,
   	salary
   FROM
   	employees
   WHERE
   	salary < (
   		SELECT
   			MAX(salary)
   		FROM
   			employees
   		WHERE
   			job_id = 'IT_PROG'
   	)
   AND depertment_id NOT IN (
   	SELECT
   		department_id
   	FROM
   		employees
   	WHERE
   		job_id = 'IT_PROG'
   );
   ```

3. 案例3： 返回其它部门中比 job_id 为 IT_PROG 部门所有工资低的员工的员工号、姓名、job_id 以及 salary。

   ```mysql
   SELECT
   	employee_id,
   	last_name,
   	job_id,
   	salary
   FROM
   	employees
   WHERE
   	salary < (
   		SELECT
   			MIN(salary)
   		FROM
   			employees
   		WHERE
   			job_id = 'IT_PROG'
   	)
   AND depertment_id NOT IN (
   	SELECT
   		department_id
   	FROM
   		employees
   	WHERE
   		job_id = 'IT_PROG'
   );
   ```

## 3. 行子查询（一列多行或多行多列）

案例1：查询员工编号最小并且工资最高的员工信息

### 原始的解决方法

1. 查询最小的员工编号

   ```mysql
   SELECT
   	MIN(employee_id)
   FROM
   	employees
   ```

2. 查询最高工资

   ```mysql
   SELECT
   	MAX(salary)
   FROM
   	employees
   ```

3. 查询员工信息

   ```mysql
   SELECT
   	*
   FROM
   	employees
   WHERE
   	employee_id = (
   		SELECT
   			MIN(employee_id)
   		FROM
   			employees
   	)
   AND salary = (
   	SELECT
   		MAX(salary)
   	FROM
   		employees
   );
   ```

### 使用行子查询

两个筛选条件使用的都是等于号，则可将 sql语句修改为如下：

```mysql
SELECT
	*
FROM
	employees
WHERE
	(employee_id, salary) = (
		SELECT
			MIN(employ_id),
			MAX(salary)
		FROM
			employees
	);
```

## 特点

1. 子查询放在小括号内；

2. 子查询一般放在条件的右侧；

3. 变量子查询，一般搭配着单行操作符使用（>、<、>=、<=、<>）

   列子查询，一般搭配着多行操作符使用（IN、ANY/SOME、ALL）

4. 子查询的执行优先于主查询执行，主查询的条件用到了子查询的结果。

## 非法使用标量子查询

子查询的结果不是 **一行一列**都是对标量子查询的非法使用。