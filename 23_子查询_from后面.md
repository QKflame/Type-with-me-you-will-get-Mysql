# 案例

1. 查询每个部门的平均工资的工资等级

   ```mysql
   SELECT
   	avg_salary.*, j.grade_level
   FROM
   	(
   		SELECT
   			AVG(salary) avg_value,
   			department_id
   		FROM
   			employees
   		GROUP BY
   			department_id
   	) avg_salary
   INNER JOIN job_grades j ON avg_salary.avg_value BETWEEN j.lowest
   AND highest_sal;
   ```

   