---
title: MySQL
date: 2020-07-01 20:00:48
categories: 
-  MySQL
tags:
-  MySQL
---

1.  组合两个表 
  
![enter description here](./images/1622292610820.png)

2. 第二高的薪水

``` javascript
select max(Salary) SecondHighestSalary
from employee
where
salary<(select max(salary) from employee)
```
3. 第N高的薪水
  

``` javascript
CREATE FUNCTION getNthHighestSalary(N INT) RETURNS INT
BEGIN
  SET N = N-1;
  IF N < 0 THEN
  RETURN NULL;
  ELSE
  RETURN (
      # Write your MySQL query statement below.
      SELECT IFNULL(
          (
          SELECT
          DISTINCT Salary
          FROM Employee
          ORDER BY Salary DESC
          LIMIT N, 1
          ), NULL)
      AS getNthHighestSalary
  );
  END IF;
END
```
4. 整体分数排名,同分同名  ，排名连续

``` javascript
SELECT DENSE_RANK() over(ORDER BY score DESC) number,score FROM yy
```
5. 分数排名,同分同名  ，排名不连续
  RANK() 排名

``` sql
  SELECT RANK() over(ORDER BY score DESC) number,score FROM yy
```

  
  6. 连续出现的数字
     DENSE_RANK() 用于排名

``` javascript
    select distinct Num as ConsecutiveNums
from (
  select Num, 
    case 
      when @prev = Num then @count := @count + 1
      when (@prev := Num) is not null then @count := 1 # 赋初始值
    end as CNT
  from Logs, (select @prev := null,@count := null) as t
) as temp
where temp.CNT >= 3
```

7. 组内排名  ，同分同名  ，排名连续
  partition by 用于分组
  DENSE_RANK() 用于排名

``` javascript
SELECT
	Department, Employee, Salary
FROM
	(
        SELECT 
        d.Name Department,e.Name Employee ,Salary,DENSE_RANK() over(partition by            DepartmentId           ORDER BY
	Salary DESC) as rk
        FROM Employee e LEFT JOIN Department d
        ON e.DepartmentId = d.Id        
    ) AS a
    where rk <= 3
```


