# 案例

1. 查询每个部门的员工个数

   ```mysql
   SELECT
   	d.*, (
   		SELECT
   			COUNT(*)
   		FROM
   			employees e
   		WHERE
   			e.department_id = d.department_id
   	) 个数
   FROM
   	deparements d;
   ```

2. 查询员工号=102的部门名

   ```mysql
   SELECT
   	(
   		SELECT
   			department_name
   		FROM
   			departments d
   		INNER JOIN employees e ON d.department_id = e.department_id
   		WHERE
   			e.employee_id == 102
   	) 部门名;
   ```

# 特点

放在 select 后面的子查询只支持标量子查询（一行一列）。