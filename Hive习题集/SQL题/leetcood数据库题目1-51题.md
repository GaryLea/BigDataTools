> https://leetcode-cn.com/problemset/database/
>
> 题目都是leetcode 上了可以点击题目会有相应的链接
>
> 由于个人比较喜欢用开窗函数，所以都优先用了开窗 ，当然这些并不一定都是最优解，答案仅供参考
>
> 每道题后面都应相应的难度等级，如果没时间做的话 可以在leetcode 按出题频率刷题
>
> 569、571这两个题都是求中位数问题，我们平时没有做过，可以尝试下 挺有意思的
>
> 我是安顺序刷的题，后续还会继续更新
>
> 祝大家面试取得好的成绩

#### [175. 组合两个表](https://leetcode-cn.com/problems/combine-two-tables/)

难度简单

SQL架构

表1: `Person`

```
+-------------+---------+
| 列名         | 类型     |
+-------------+---------+
| PersonId    | int     |
| FirstName   | varchar |
| LastName    | varchar |
+-------------+---------+
PersonId 是上表主键
```

表2: `Address`

```
+-------------+---------+
| 列名         | 类型    |
+-------------+---------+
| AddressId   | int     |
| PersonId    | int     |
| City        | varchar |
| State       | varchar |
+-------------+---------+
AddressId 是上表主键
```

 

编写一个 SQL 查询，满足条件：无论 person 是否有地址信息，都需要基于上述两表提供 person 的以下信息：

 

```
FirstName, LastName, City, State
```



```sql
select FirstName,LastName,City,State
from Person  p
left join  Address a 
on a.PersonId = p.PersonId
```

#### [176. 第二高的薪水](https://leetcode-cn.com/problems/second-highest-salary/)

难度简单

SQL架构

编写一个 SQL 查询，获取 `Employee` 表中第二高的薪水（Salary） 。

```
+----+--------+
| Id | Salary |
+----+--------+
| 1  | 100    |
| 2  | 200    |
| 3  | 300    |
+----+--------+
```

例如上述 `Employee` 表，SQL查询应该返回 `200` 作为第二高的薪水。如果不存在第二高的薪水，那么查询应返回 `null`。

```
+---------------------+
| SecondHighestSalary |
+---------------------+
| 200                 |
+---------------------+
```

```sql
SELECT
    IFNULL(
      (SELECT DISTINCT Salary
       FROM Employee
       ORDER BY Salary DESC
        LIMIT 1 OFFSET 1),
    NULL) AS SecondHighestSalary
```

#### [177. 第N高的薪水](https://leetcode-cn.com/problems/nth-highest-salary/)

难度中等

编写一个 SQL 查询，获取 `Employee` 表中第 *n* 高的薪水（Salary）。

```
+----+--------+
| Id | Salary |
+----+--------+
| 1  | 100    |
| 2  | 200    |
| 3  | 300    |
+----+--------+
```

例如上述 `Employee` 表，*n = 2* 时，应返回第二高的薪水 `200`。如果不存在第 *n* 高的薪水，那么查询应返回 `null`。

```
+------------------------+
| getNthHighestSalary(2) |
+------------------------+
| 200                    |
+------------------------+
```



```sql
CREATE FUNCTION getNthHighestSalary(N INT) RETURNS INT
BEGIN
  RETURN (
      SELECT IFNULL(
		(select salary  
		from(
		select salary,
		rank() over(order by salary desc) rk
		from Employee
        group by salary
		)t1
		where rk=N),NULL) SecondHighestSalary
  );
END
```

#### [178. 分数排名](https://leetcode-cn.com/problems/rank-scores/)

难度中等

SQL架构

编写一个 SQL 查询来实现分数排名。

如果两个分数相同，则两个分数排名（Rank）相同。请注意，平分后的下一个名次应该是下一个连续的整数值。换句话说，名次之间不应该有“间隔”。

```
+----+-------+
| Id | Score |
+----+-------+
| 1  | 3.50  |
| 2  | 3.65  |
| 3  | 4.00  |
| 4  | 3.85  |
| 5  | 4.00  |
| 6  | 3.65  |
+----+-------+
```

例如，根据上述给定的 `Scores` 表，你的查询应该返回（按分数从高到低排列）：

```
+-------+------+
| Score | Rank |
+-------+------+
| 4.00  | 1    |
| 4.00  | 1    |
| 3.85  | 2    |
| 3.65  | 3    |
| 3.65  | 3    |
| 3.50  | 4    |
+-------+------+
```

**重要提示：**对于 MySQL 解决方案，如果要转义用作列名的保留字，可以在关键字之前和之后使用撇号。例如 **`Rank`**

```sql
select Score,
dense_rank() over(order by Score desc) `rank`
from Scores
```



#### [180. 连续出现的数字](https://leetcode-cn.com/problems/consecutive-numbers/)

难度中等

SQL架构

编写一个 SQL 查询，查找所有至少连续出现三次的数字。

```
+----+-----+
| Id | Num |
+----+-----+
| 1  |  1  |
| 2  |  1  |
| 3  |  1  |
| 4  |  2  |
| 5  |  1  |
| 6  |  2  |
| 7  |  2  |
+----+-----+
```

例如，给定上面的 `Logs` 表， `1` 是唯一连续出现至少三次的数字。

```sql
+-----------------+
| ConsecutiveNums |
+-----------------+
| 1               |
+-----------------+
```



```sql
select distinct Num ConsecutiveNums
from 
(
select
Num,
lead(Num,1,null) over(order by id) n2,
lead(Num,2,null) over(order by id) n3
from Logs
)t1
where Num = n2 and Num = n3
```

#### [181. 超过经理收入的员工](https://leetcode-cn.com/problems/employees-earning-more-than-their-managers/)

难度简单

SQL架构

`Employee` 表包含所有员工，他们的经理也属于员工。每个员工都有一个 Id，此外还有一列对应员工的经理的 Id。

```
+----+-------+--------+-----------+
| Id | Name  | Salary | ManagerId |
+----+-------+--------+-----------+
| 1  | Joe   | 70000  | 3         |
| 2  | Henry | 80000  | 4         |
| 3  | Sam   | 60000  | NULL      |
| 4  | Max   | 90000  | NULL      |
+----+-------+--------+-----------+
```

给定 `Employee` 表，编写一个 SQL 查询，该查询可以获取收入超过他们经理的员工的姓名。在上面的表格中，Joe 是唯一一个收入超过他的经理的员工。

```sql
+----------+
| Employee |
+----------+
| Joe      |
+----------+
```



```sql
select a.Name  Employee 
from Employee a 
join Employee b
on a.ManagerId = b.id
where a.Salary>b.Salary
```



#### [182. 查找重复的电子邮箱](https://leetcode-cn.com/problems/duplicate-emails/)

难度简单

SQL架构

编写一个 SQL 查询，查找 `Person` 表中所有重复的电子邮箱。

**示例：**

```
+----+---------+
| Id | Email   |
+----+---------+
| 1  | a@b.com |
| 2  | c@d.com |
| 3  | a@b.com |
+----+---------+
```

根据以上输入，你的查询应返回以下结果：

```
+---------+
| Email   |
+---------+
| a@b.com |
+---------+
```

**说明：**所有电子邮箱都是小写字母。



```sql
select Email
from Person
group by Email
having count(*)>1
```



#### [183. 从不订购的客户](https://leetcode-cn.com/problems/customers-who-never-order/)

难度简单

SQL架构

某网站包含两个表，`Customers` 表和 `Orders` 表。编写一个 SQL 查询，找出所有从不订购任何东西的客户。

`Customers` 表：

```
+----+-------+
| Id | Name  |
+----+-------+
| 1  | Joe   |
| 2  | Henry |
| 3  | Sam   |
| 4  | Max   |
+----+-------+
```

`Orders` 表：

```
+----+------------+
| Id | CustomerId |
+----+------------+
| 1  | 3          |
| 2  | 1          |
+----+------------+
```

例如给定上述表格，你的查询应返回：

```sql
+-----------+
| Customers |
+-----------+
| Henry     |
| Max       |
+-----------+
```

```sql
select  c.Name Customers
from Customers  c left join Orders  o
on c.id = o.CustomerId
where o.id is null
```



#### [184. 部门工资最高的员工](https://leetcode-cn.com/problems/department-highest-salary/)

难度中等

SQL架构

`Employee` 表包含所有员工信息，每个员工有其对应的 Id, salary 和 department Id。

```
+----+-------+--------+--------------+
| Id | Name  | Salary | DepartmentId |
+----+-------+--------+--------------+
| 1  | Joe   | 70000  | 1            |
| 2  | Henry | 80000  | 2            |
| 3  | Sam   | 60000  | 2            |
| 4  | Max   | 90000  | 1            |
+----+-------+--------+--------------+
```

`Department` 表包含公司所有部门的信息。

```
+----+----------+
| Id | Name     |
+----+----------+
| 1  | IT       |
| 2  | Sales    |
+----+----------+
```

编写一个 SQL 查询，找出每个部门工资最高的员工。例如，根据上述给定的表格，Max 在 IT 部门有最高工资，Henry 在 Sales 部门有最高工资。

```sql
+------------+----------+--------+
| Department | Employee | Salary |
+------------+----------+--------+
| IT         | Max      | 90000  |
| Sales      | Henry    | 80000  |
+------------+----------+--------+
```



```sql
select Department,Employee,Salary
from (
select d.Name  Department,e.Name Employee, e.Salary,
rank() over(partition by d.id order by Salary desc) rk
from Employee e join Department d
on e.DepartmentId=d.id
)tmp
where rk = 1
```



#### [185. 部门工资前三高的所有员工](https://leetcode-cn.com/problems/department-top-three-salaries/)

难度困难

SQL架构

`Employee` 表包含所有员工信息，每个员工有其对应的工号 `Id`，姓名 `Name`，工资 `Salary` 和部门编号 `DepartmentId` 。

```
+----+-------+--------+--------------+
| Id | Name  | Salary | DepartmentId |
+----+-------+--------+--------------+
| 1  | Joe   | 85000  | 1            |
| 2  | Henry | 80000  | 2            |
| 3  | Sam   | 60000  | 2            |
| 4  | Max   | 90000  | 1            |
| 5  | Janet | 69000  | 1            |
| 6  | Randy | 85000  | 1            |
| 7  | Will  | 70000  | 1            |
+----+-------+--------+--------------+
```

`Department` 表包含公司所有部门的信息。

```
+----+----------+
| Id | Name     |
+----+----------+
| 1  | IT       |
| 2  | Sales    |
+----+----------+
```

编写一个 SQL 查询，找出每个部门获得前三高工资的所有员工。例如，根据上述给定的表，查询结果应返回：

```
+------------+----------+--------+
| Department | Employee | Salary |
+------------+----------+--------+
| IT         | Max      | 90000  |
| IT         | Randy    | 85000  |
| IT         | Joe      | 85000  |
| IT         | Will     | 70000  |
| Sales      | Henry    | 80000  |
| Sales      | Sam      | 60000  |
+------------+----------+--------+
```

**解释：**

IT 部门中，Max 获得了最高的工资，Randy 和 Joe 都拿到了第二高的工资，Will 的工资排第三。销售部门（Sales）只有两名员工，Henry 的工资最高，Sam 的工资排第二。



```sql
select Department,Employee,Salary
from (
select d.Name  Department,e.Name Employee, e.Salary,
dense_rank() over(partition by d.id order by Salary desc) rk
from Employee e join Department d
on e.DepartmentId=d.id
)tmp
where rk <=3
```



#### [196. 删除重复的电子邮箱](https://leetcode-cn.com/problems/delete-duplicate-emails/)

难度简单

编写一个 SQL 查询，来删除 `Person` 表中所有重复的电子邮箱，重复的邮箱里只保留 **Id** *最小* 的那个。

```
+----+------------------+
| Id | Email            |
+----+------------------+
| 1  | john@example.com |
| 2  | bob@example.com  |
| 3  | john@example.com |
+----+------------------+
Id 是这个表的主键。
```

例如，在运行你的查询语句之后，上面的 `Person` 表应返回以下几行:

```
+----+------------------+
| Id | Email            |
+----+------------------+
| 1  | john@example.com |
| 2  | bob@example.com  |
+----+------------------+
```

 

**提示：**

- 执行 SQL 之后，输出是整个 `Person` 表。

- 使用 `delete` 语句。

  

```sql
DELETE p1 FROM Person p1,
    Person p2
WHERE
    p1.Email = p2.Email AND p1.Id > p2.Id
```

> 注意是删除 ，不是查询



#### [197. 上升的温度](https://leetcode-cn.com/problems/rising-temperature/)

难度简单

SQL架构

给定一个 `Weather` 表，编写一个 SQL 查询，来查找与之前（昨天的）日期相比温度更高的所有日期的 Id。

```
+---------+------------------+------------------+
| Id(INT) | RecordDate(DATE) | Temperature(INT) |
+---------+------------------+------------------+
|       1 |       2015-01-01 |               10 |
|       2 |       2015-01-02 |               25 |
|       3 |       2015-01-03 |               20 |
|       4 |       2015-01-04 |               30 |
+---------+------------------+------------------+
```

例如，根据上述给定的 `Weather` 表格，返回如下 Id:

```sql
+----+
| Id |
+----+
|  2 |
|  4 |
+----+
```



```sql
select 
Id
from 
(
select Id,RecordDate,Temperature,
lag(RecordDate,1,9999-99-99) over (order by RecordDate) yd,
lag(Temperature,1,999) over(order by RecordDate ) yt
from Weather 
)tmp
where Temperature >yt
and datediff(RecordDate,yd)=1
```



#### [262. 行程和用户](https://leetcode-cn.com/problems/trips-and-users/)

难度困难

SQL架构

`Trips` 表中存所有出租车的行程信息。每段行程有唯一键 Id，Client_Id 和 Driver_Id 是 `Users` 表中 Users_Id 的外键。Status 是枚举类型，枚举成员为 (‘completed’, ‘cancelled_by_driver’, ‘cancelled_by_client’)。

```
+----+-----------+-----------+---------+--------------------+----------+
| Id | Client_Id | Driver_Id | City_Id |        Status      |Request_at|
+----+-----------+-----------+---------+--------------------+----------+
| 1  |     1     |    10     |    1    |     completed      |2013-10-01|
| 2  |     2     |    11     |    1    | cancelled_by_driver|2013-10-01|
| 3  |     3     |    12     |    6    |     completed      |2013-10-01|
| 4  |     4     |    13     |    6    | cancelled_by_client|2013-10-01|
| 5  |     1     |    10     |    1    |     completed      |2013-10-02|
| 6  |     2     |    11     |    6    |     completed      |2013-10-02|
| 7  |     3     |    12     |    6    |     completed      |2013-10-02|
| 8  |     2     |    12     |    12   |     completed      |2013-10-03|
| 9  |     3     |    10     |    12   |     completed      |2013-10-03| 
| 10 |     4     |    13     |    12   | cancelled_by_driver|2013-10-03|
+----+-----------+-----------+---------+--------------------+----------+
```

`Users` 表存所有用户。每个用户有唯一键 Users_Id。Banned 表示这个用户是否被禁止，Role 则是一个表示（‘client’, ‘driver’, ‘partner’）的枚举类型。

```
+----------+--------+--------+
| Users_Id | Banned |  Role  |
+----------+--------+--------+
|    1     |   No   | client |
|    2     |   Yes  | client |
|    3     |   No   | client |
|    4     |   No   | client |
|    10    |   No   | driver |
|    11    |   No   | driver |
|    12    |   No   | driver |
|    13    |   No   | driver |
+----------+--------+--------+
```

写一段 SQL 语句查出 **2013年10月1日** 至 **2013年10月3日** 期间非禁止用户的取消率。基于上表，你的 SQL 语句应返回如下结果，取消率（Cancellation Rate）保留两位小数。

取消率的计算方式如下：(被司机或乘客取消的非禁止用户生成的订单数量) / (非禁止用户生成的订单总数)

```sql
+------------+-------------------+
|     Day    | Cancellation Rate |
+------------+-------------------+
| 2013-10-01 |       0.33        |
| 2013-10-02 |       0.00        |
| 2013-10-03 |       0.50        |
+------------+-------------------+
```



```sql
SELECT T.request_at AS `Day`, 
	ROUND(
			SUM(
				IF(T.STATUS = 'completed',0,1)
			)
			/ 
			COUNT(T.STATUS),
			2
	) AS `Cancellation Rate`
FROM trips AS T
WHERE 
T.Client_Id NOT IN (
	SELECT users_id
	FROM users
	WHERE banned = 'Yes'
)
AND
T.Driver_Id NOT IN (
	SELECT users_id
	FROM users
	WHERE banned = 'Yes'
)
AND T.request_at BETWEEN '2013-10-01' AND '2013-10-03'
GROUP BY T.request_at
```



#### [511. 游戏玩法分析 I](https://leetcode-cn.com/problems/game-play-analysis-i/)

难度简单

SQL架构

活动表 `Activity`：

```
+--------------+---------+
| Column Name  | Type    |
+--------------+---------+
| player_id    | int     |
| device_id    | int     |
| event_date   | date    |
| games_played | int     |
+--------------+---------+
表的主键是 (player_id, event_date)。
这张表展示了一些游戏玩家在游戏平台上的行为活动。
每行数据记录了一名玩家在退出平台之前，当天使用同一台设备登录平台后打开的游戏的数目（可能是 0 个）。
```

 

写一条 SQL 查询语句获取每位玩家 **第一次登陆平台的日期**。

查询结果的格式如下所示：

```
Activity 表：
+-----------+-----------+------------+--------------+
| player_id | device_id | event_date | games_played |
+-----------+-----------+------------+--------------+
| 1         | 2         | 2016-03-01 | 5            |
| 1         | 2         | 2016-05-02 | 6            |
| 2         | 3         | 2017-06-25 | 1            |
| 3         | 1         | 2016-03-02 | 0            |
| 3         | 4         | 2018-07-03 | 5            |
+-----------+-----------+------------+--------------+

Result 表：
+-----------+-------------+
| player_id | first_login |
+-----------+-------------+
| 1         | 2016-03-01  |
| 2         | 2017-06-25  |
| 3         | 2016-03-02  |
+-----------+-------------+
```

1.

```sql
select player_id ,event_date first_login
from (
select player_id ,event_date,
rank() over(partition by player_id order by event_date) rk
from Activity
) tmp
where rk = 1
```

2.最优 （选最小日期）

```sql
select player_id ,min(event_date) first_login
from Activity
group by player_id 
```





#### [512. 游戏玩法分析 II](https://leetcode-cn.com/problems/game-play-analysis-ii/)

难度简单

SQL架构

Table: `Activity`

```
+--------------+---------+
| Column Name  | Type    |
+--------------+---------+
| player_id    | int     |
| device_id    | int     |
| event_date   | date    |
| games_played | int     |
+--------------+---------+
(player_id, event_date) 是这个表的两个主键
这个表显示的是某些游戏玩家的游戏活动情况
每一行是在某天使用某个设备登出之前登录并玩多个游戏（可能为0）的玩家的记录
```

请编写一个 SQL 查询，描述每一个玩家首次登陆的设备名称

查询结果格式在以下示例中：

```
Activity table:
+-----------+-----------+------------+--------------+
| player_id | device_id | event_date | games_played |
+-----------+-----------+------------+--------------+
| 1         | 2         | 2016-03-01 | 5            |
| 1         | 2         | 2016-05-02 | 6            |
| 2         | 3         | 2017-06-25 | 1            |
| 3         | 1         | 2016-03-02 | 0            |
| 3         | 4         | 2018-07-03 | 5            |
+-----------+-----------+------------+--------------+

Result table:
+-----------+-----------+
| player_id | device_id |
+-----------+-----------+
| 1         | 2         |
| 2         | 3         |
| 3         | 1         |
+-----------+-----------+
```



```sql
select player_id ,device_id
from (
select player_id ,event_date,device_id,
rank() over(partition by player_id order by event_date) rk
from Activity
) tmp
where rk = 1
```

#### [534. 游戏玩法分析 III](https://leetcode-cn.com/problems/game-play-analysis-iii/)

难度中等20收藏分享切换为英文关注反馈

SQL架构

Table: `Activity`

```
+--------------+---------+
| Column Name  | Type    |
+--------------+---------+
| player_id    | int     |
| device_id    | int     |
| event_date   | date    |
| games_played | int     |
+--------------+---------+
（player_id，event_date）是此表的主键。
这张表显示了某些游戏的玩家的活动情况。
每一行是一个玩家的记录，他在某一天使用某个设备注销之前登录并玩了很多游戏（可能是 0 ）。
```

 

编写一个 SQL 查询，同时报告每组玩家和日期，以及玩家到目前为止玩了多少游戏。也就是说，在此日期之前玩家所玩的游戏总数。详细情况请查看示例。

查询结果格式如下所示：

```
Activity table:
+-----------+-----------+------------+--------------+
| player_id | device_id | event_date | games_played |
+-----------+-----------+------------+--------------+
| 1         | 2         | 2016-03-01 | 5            |
| 1         | 2         | 2016-05-02 | 6            |
| 1         | 3         | 2017-06-25 | 1            |
| 3         | 1         | 2016-03-02 | 0            |
| 3         | 4         | 2018-07-03 | 5            |
+-----------+-----------+------------+--------------+

Result table:
+-----------+------------+---------------------+
| player_id | event_date | games_played_so_far |
+-----------+------------+---------------------+
| 1         | 2016-03-01 | 5                   |
| 1         | 2016-05-02 | 11                  |
| 1         | 2017-06-25 | 12                  |
| 3         | 2016-03-02 | 0                   |
| 3         | 2018-07-03 | 5                   |
+-----------+------------+---------------------+
对于 ID 为 1 的玩家，2016-05-02 共玩了 5+6=11 个游戏，2017-06-25 共玩了 5+6+1=12 个游戏。
对于 ID 为 3 的玩家，2018-07-03 共玩了 0+5=5 个游戏。
请注意，对于每个玩家，我们只关心玩家的登录日期。
```

开窗

```sql
select player_id,event_date ,
sum(games_played) over(partition by player_id order by  event_date )games_played_so_far
from Activity
```

自连接

```sql
select 
    a1.player_id,
    a1.event_date,
    sum(a2.games_played) games_played_so_far
from Activity a1,Activity a2
where a1.player_id=a2.player_id and 
      a1.event_date>=a2.event_date
group by 1,2;
```



#### [550. 游戏玩法分析 IV](https://leetcode-cn.com/problems/game-play-analysis-iv/)

难度中等17收藏分享切换为英文关注反馈

SQL架构

Table: `Activity`

```
+--------------+---------+
| Column Name  | Type    |
+--------------+---------+
| player_id    | int     |
| device_id    | int     |
| event_date   | date    |
| games_played | int     |
+--------------+---------+
（player_id，event_date）是此表的主键。
这张表显示了某些游戏的玩家的活动情况。
每一行是一个玩家的记录，他在某一天使用某个设备注销之前登录并玩了很多游戏（可能是 0）。
```

 

编写一个 SQL 查询，报告在首次登录的第二天再次登录的玩家的分数，四舍五入到小数点后两位。换句话说，您需要计算从首次登录日期开始至少连续两天登录的玩家的数量，然后除以玩家总数。

查询结果格式如下所示：

```
Activity table:
+-----------+-----------+------------+--------------+
| player_id | device_id | event_date | games_played |
+-----------+-----------+------------+--------------+
| 1         | 2         | 2016-03-01 | 5            |
| 1         | 2         | 2016-03-02 | 6            |
| 2         | 3         | 2017-06-25 | 1            |
| 3         | 1         | 2016-03-02 | 0            |
| 3         | 4         | 2018-07-03 | 5            |
+-----------+-----------+------------+--------------+

Result table:
+-----------+
| fraction  |
+-----------+
| 0.33      |
+-----------+
只有 ID 为 1 的玩家在第一天登录后才重新登录，所以答案是 1/3 = 0.33
```



```sql
select round(avg(a.event_date is not null), 2) fraction
from 
    (select player_id, min(event_date) as login
    from activity
    group by player_id) p 
left join activity a 
on p.player_id=a.player_id and datediff(a.event_date, p.login)=1
```

> 这个avg用的妙
>
> is not null判断后，有eventdate值的返回1，null的返回0，avg相当于求和后(即符合条件的id个数)除以总id数即所求比例



#### [569. 员工薪水中位数](https://leetcode-cn.com/problems/median-employee-salary/)

难度困难

SQL架构

`Employee` 表包含所有员工。`Employee` 表有三列：员工Id，公司名和薪水。

```
+-----+------------+--------+
|Id   | Company    | Salary |
+-----+------------+--------+
|1    | A          | 2341   |
|2    | A          | 341    |
|3    | A          | 15     |
|4    | A          | 15314  |
|5    | A          | 451    |
|6    | A          | 513    |
|7    | B          | 15     |
|8    | B          | 13     |
|9    | B          | 1154   |
|10   | B          | 1345   |
|11   | B          | 1221   |
|12   | B          | 234    |
|13   | C          | 2345   |
|14   | C          | 2645   |
|15   | C          | 2645   |
|16   | C          | 2652   |
|17   | C          | 65     |
+-----+------------+--------+
```

请编写SQL查询来查找每个公司的薪水中位数。挑战点：你是否可以在不使用任何内置的SQL函数的情况下解决此问题。

```
+-----+------------+--------+
|Id   | Company    | Salary |
+-----+------------+--------+
|5    | A          | 451    |
|6    | A          | 513    |
|12   | B          | 234    |
|9    | B          | 1154   |
|14   | C          | 2645   |
+-----+------------+--------+
```



```sql
select Id,Company,Salary
from (
select Id,Company,Salary,
ROW_NUMBER() over(partition by Company order by Salary) rk,
count(*) over(partition by Company) cnt
from Employee
)t1
where rk IN (FLOOR((cnt + 1)/2), FLOOR((cnt + 2)/2))
```

> 中位数：
>
> +1向下取整 +2 向下取整数



#### [570. 至少有5名直接下属的经理](https://leetcode-cn.com/problems/managers-with-at-least-5-direct-reports/)

难度中等

SQL架构

`Employee` 表包含所有员工和他们的经理。每个员工都有一个 Id，并且还有一列是经理的 Id。

```
+------+----------+-----------+----------+
|Id    |Name 	  |Department |ManagerId |
+------+----------+-----------+----------+
|101   |John 	  |A 	      |null      |
|102   |Dan 	  |A 	      |101       |
|103   |James 	  |A 	      |101       |
|104   |Amy 	  |A 	      |101       |
|105   |Anne 	  |A 	      |101       |
|106   |Ron 	  |B 	      |101       |
+------+----------+-----------+----------+
```

给定 `Employee` 表，请编写一个SQL查询来查找至少有5名直接下属的经理。对于上表，您的SQL查询应该返回：

```
+-------+
| Name  |
+-------+
| John  |
+-------+
```

**注意:**
没有人是自己的下属。

```sql
select Name
from Employee
where Id in (
select ManagerId
from Employee
group by ManagerId
having count(*)>=5
)
```



#### [571. 给定数字的频率查询中位数](https://leetcode-cn.com/problems/find-median-given-frequency-of-numbers/)

难度困难

SQL架构

`Numbers` 表保存数字的值及其频率。

```
+----------+-------------+
|  Number  |  Frequency  |
+----------+-------------|
|  0       |  7          |
|  1       |  1          |
|  2       |  3          |
|  3       |  1          |
+----------+-------------+
```

在此表中，数字为 `0, 0, 0, 0, 0, 0, 0, 1, 2, 2, 2, 3`，所以中位数是 `(0 + 0) / 2 = 0`。

```
+--------+
| median |
+--------|
| 0.0000 |
+--------+
```

请编写一个查询来查找所有数字的中位数并将结果命名为 `median` 。





```sql
select 
    avg(cast(number as float)) median
from 
    (
        select 
            Number,
            Frequency,
            sum(Frequency) over(order by Number) - Frequency prev_sum,
            sum(Frequency) over(order by Number) curr_sum
        from Numbers
    ) t1,
    (
        select 
            sum(Frequency) total_sum
        from Numbers
    ) t2
where 
    t1.prev_sum <= (cast(t2.total_sum as float) / 2) and 
    t1.curr_sum >= (cast(t2.total_sum as float) / 2)
```

如果 n1.Number 为中位数，n1.Number（包含本身）前累计的数字应大于等于总数/2 同时n1.Number（不包含本身）前累计数字应小于等于总数/2

例如：0, 0, 0, 0, 0, 0, 0, 1, 2, 2, 2, 3 共12个数

 中位数0（包含本身）前累计的数字 7 >=6  0（不包含本身）前累计数字 0 <=6
例如：0，0，0，3，3，3 共6个数

 中位数0（包含本身）前累计的数字 3 >=3  0（不包含本身）前累计数字 0 <=3
                 
 中位数3（包含本身）前累计的数字 6 >=3  3（不包含本身）前累计数字 3 <=3

```sql
SELECT 
AVG(Number)median 
FROM
(SELECT n1.Number FROM Numbers n1 JOIN Numbers n2 ON n1.Number>=n2.Number 
 GROUP BY 
 n1.Number 
 HAVING 
 SUM(n2.Frequency)>=(SELECT SUM(Frequency) FROM Numbers)/2 
 AND 
 SUM(n2.Frequency)-AVG(n1.Frequency)<=(SELECT SUM(Frequency) FROM Numbers)/2
)s
```

#### [574. 当选者](https://leetcode-cn.com/problems/winning-candidate/)

难度中等

SQL架构

表: `Candidate`

```
+-----+---------+
| id  | Name    |
+-----+---------+
| 1   | A       |
| 2   | B       |
| 3   | C       |
| 4   | D       |
| 5   | E       |
+-----+---------+  
```

表: `Vote`

```
+-----+--------------+
| id  | CandidateId  |
+-----+--------------+
| 1   |     2        |
| 2   |     4        |
| 3   |     3        |
| 4   |     2        |
| 5   |     5        |
+-----+--------------+
id 是自动递增的主键，
CandidateId 是 Candidate 表中的 id.
```

请编写 sql 语句来找到当选者的名字，上面的例子将返回当选者 `B`.

```
+------+
| Name |
+------+
| B    |
+------+
```



用了order by 全局排序 不够好

```sql
select 
    Name
from Candidate c
    left join Vote v
    on c.id = v.CandidateId
    group by Name
    order by count(*) desc
    limit 1   
```

先过滤再  效率高很多

```sql
select Name
from Candidate
where id =
(
select CandidateId
    from    
        (
        select CandidateId, 
        count(*) over(partition by CandidateId ) cnt
        from Vote
        order by cnt desc 
        limit 1
        )t1
)
```



#### [577. 员工奖金](https://leetcode-cn.com/problems/employee-bonus/)

难度简单

SQL架构

选出所有 bonus < 1000 的员工的 name 及其 bonus。

`Employee` 表单

```
+-------+--------+-----------+--------+
| empId |  name  | supervisor| salary |
+-------+--------+-----------+--------+
|   1   | John   |  3        | 1000   |
|   2   | Dan    |  3        | 2000   |
|   3   | Brad   |  null     | 4000   |
|   4   | Thomas |  3        | 4000   |
+-------+--------+-----------+--------+
empId 是这张表单的主关键字
```

`Bonus` 表单

```
+-------+-------+
| empId | bonus |
+-------+-------+
| 2     | 500   |
| 4     | 2000  |
+-------+-------+
empId 是这张表单的主关键字
```

输出示例：

```
+-------+-------+
| name  | bonus |
+-------+-------+
| John  | null  |
| Dan   | 500   |
| Brad  | null  |
+-------+-------+
```



```sql
select name,bonus
from Employee e
left join Bonus b on  e.empId=b.empId
where bonus<1000 or bonus is null
```



#### [578. 查询回答率最高的问题](https://leetcode-cn.com/problems/get-highest-answer-rate-question/)

难度中等3收藏分享切换为英文关注反馈

SQL架构

从 `survey_log` 表中获得回答率最高的问题，`survey_log` 表包含这些列**：id**, **action**, **question_id**, **answer_id**, **q_num**, **timestamp**。

id 表示用户 id；action 有以下几种值："show"，"answer"，"skip"；当 action 值为 "answer" 时 answer_id 非空，而 action 值为 "show" 或者 "skip" 时 answer_id 为空；q_num 表示当前会话中问题的编号。

请编写 SQL 查询来找到具有最高回答率的问题。

 

**示例：**

```
输入：
+------+-----------+--------------+------------+-----------+------------+
| id   | action    | question_id  | answer_id  | q_num     | timestamp  |
+------+-----------+--------------+------------+-----------+------------+
| 5    | show      | 285          | null       | 1         | 123        |
| 5    | answer    | 285          | 124124     | 1         | 124        |
| 5    | show      | 369          | null       | 2         | 125        |
| 5    | skip      | 369          | null       | 2         | 126        |
+------+-----------+--------------+------------+-----------+------------+
输出：
+-------------+
| survey_log  |
+-------------+
|    285      |
+-------------+
解释：
问题 285 的回答率为 1/1，而问题 369 回答率为 0/1，因此输出 285 。
```



```sql
select question_id  survey_log
from (
  select
      question_id,
      sum(if(action = 'answer', 1, 0)) as AnswerCnt,
      sum(if(action = 'show', 1, 0)) as ShowCnt
  from
      survey_log
  group by question_id
) as tbl
order by (AnswerCnt / ShowCnt) desc
limit 1
```

直接不嵌套

```sql
select question_id  survey_log
from survey_log
group by question_id
order by sum(if(action = 'answer', 1, 0)) / sum(if(action = 'show', 1, 0)) desc
limit 1
```



#### [579. 查询员工的累计薪水](https://leetcode-cn.com/problems/find-cumulative-salary-of-an-employee/)

难度困难

SQL架构

**Employee** 表保存了一年内的薪水信息。

请你编写 SQL 语句，对于每个员工，查询他除最近一个月（即最大月）之外，剩下每个月的近三个月的累计薪水（不足三个月也要计算）。

结果请按 `Id` 升序，然后按 `Month` 降序显示。

 

**示例：**
**输入：**

| Id | Month | Salary |
|----|-------|--------|
| 1  | 1     | 20     |
| 2  | 1     | 20     |
| 1  | 2     | 30     |
| 2  | 2     | 30     |
| 3  | 2     | 40     |
| 1  | 3     | 40     |
| 3  | 3     | 60     |
| 1  | 4     | 60     |
| 3  | 4     | 70     |

**输出：**

| Id | Month | Salary |
|----|-------|--------|
| 1  | 3     | 90     |
| 1  | 2     | 50     |
| 1  | 1     | 20     |
| 2  | 1     | 20     |
| 3  | 3     | 100    |
| 3  | 2     | 40     |

 

**解释：**

员工 '1' 除去最近一个月（月份 '4'），有三个月的薪水记录：月份 '3' 薪水为 40，月份 '2' 薪水为 30，月份 '1' 薪水为 20。

所以近 3 个月的薪水累计分别为 (40 + 30 + 20) = 90，(30 + 20) = 50 和 20。

| Id | Month | Salary |
|----|-------|--------|
| 1  | 3     | 90     |
| 1  | 2     | 50     |
| 1  | 1     | 20     |

员工 '2' 除去最近的一个月（月份 '2'）的话，只有月份 '1' 这一个月的薪水记录。

| Id | Month | Salary |
|----|-------|--------|
| 2  | 1     | 20     |

员工 '3' 除去最近一个月（月份 '4'）后有两个月，分别为：月份 '4' 薪水为 60 和 月份 '2' 薪水为 40。所以各月的累计情况如下：

| Id | Month | Salary |
|----|-------|--------|
| 3  | 3     | 100    |
| 3  | 2     | 40     |



```sql
select Id,Month,
sum(Salary) over(partition by Id order by Month ROWS BETWEEN 2 PRECEDING AND CURRENT ROW) Salary
from 
(
    select Id,Month,Salary,
    lead(Month,1,0) over(partition by Id order by Month) lm
    from Employee 
)t1
where lm != 0
order by  Id,Month desc
```



#### [580. 统计各专业学生人数](https://leetcode-cn.com/problems/count-student-number-in-departments/)

难度中等

SQL架构

一所大学有 2 个数据表，分别是 ***student*** 和 ***department*** ，这两个表保存着每个专业的学生数据和院系数据。

写一个查询语句，查询 ***department*** 表中每个专业的学生人数 （即使没有学生的专业也需列出）。

将你的查询结果按照学生人数降序排列。 如果有两个或两个以上专业有相同的学生数目，将这些部门按照部门名字的字典序从小到大排列。

***student\*** 表格如下：

| Column Name  | Type      |
|--------------|-----------|
| student_id   | Integer   |
| student_name | String    |
| gender       | Character |
| dept_id      | Integer   |

其中， student_id 是学生的学号， student_name 是学生的姓名， gender 是学生的性别， dept_id 是学生所属专业的专业编号。

***department\*** 表格如下：

| Column Name | Type    |
|-------------|---------|
| dept_id     | Integer |
| dept_name   | String  |

dept_id 是专业编号， dept_name 是专业名字。

这里是一个示例输入：
***student\*** 表格：

| student_id | student_name | gender | dept_id |
|------------|--------------|--------|---------|
| 1          | Jack         | M      | 1       |
| 2          | Jane         | F      | 1       |
| 3          | Mark         | M      | 2       |

***department\*** 表格：

| dept_id | dept_name   |
|---------|-------------|
| 1       | Engineering |
| 2       | Science     |
| 3       | Law         |

示例输出为：

| dept_name   | student_number |
|-------------|----------------|
| Engineering | 2              |
| Science     | 1              |
| Law         | 0              |





```sql
select  dept_name ,count(student_id) student_number
from department d left join student s
on d.dept_id=s.dept_id 
group by dept_name
order by student_number desc
```





#### [584. 寻找用户推荐人](https://leetcode-cn.com/problems/find-customer-referee/)

难度简单9收藏分享切换为英文关注反馈

SQL架构

给定表 `customer` ，里面保存了所有客户信息和他们的推荐人。

```
+------+------+-----------+
| id   | name | referee_id|
+------+------+-----------+
|    1 | Will |      NULL |
|    2 | Jane |      NULL |
|    3 | Alex |         2 |
|    4 | Bill |      NULL |
|    5 | Zack |         1 |
|    6 | Mark |         2 |
+------+------+-----------+
```

写一个查询语句，返回一个编号列表，列表中编号的推荐人的编号都 **不是** 2。

对于上面的示例数据，结果为：

```
+------+
| name |
+------+
| Will |
| Jane |
| Bill |
| Zack |
+------+
```



```sql
SELECT name FROM customer WHERE referee_id != 2 OR referee_id IS NULL;
```

> MySQL 使用三值逻辑 —— TRUE, FALSE 和 UNKNOWN。任何与 NULL 值进行的比较都会与第三种值 UNKNOWN 做比较。这个“任何值”包括 NULL 本身！这就是为什么 MySQL 提供 IS NULL 和 IS NOT NULL 两种操作来对 NULL 特殊判断。
>
> 因此，在 WHERE 语句中我们需要做一个额外的条件判断 `referee_id IS NULL'。
>

#### [585. 2016年的投资](https://leetcode-cn.com/problems/investments-in-2016/)

难度中等14收藏分享切换为英文关注反馈

SQL架构

写一个查询语句，将 2016 年 (**TIV_2016**) 所有成功投资的金额加起来，保留 2 位小数。

对于一个投保人，他在 2016 年成功投资的条件是：

1. 他在 2015 年的投保额 (**TIV_2015**) 至少跟一个其他投保人在 2015 年的投保额相同。
2. 他所在的城市必须与其他投保人都不同（也就是说维度和经度不能跟其他任何一个投保人完全相同）。

**输入格式:**
表 ***insurance\*** 格式如下：

| Column Name | Type          |
|-------------|---------------|
| PID         | INTEGER(11)   |
| TIV_2015    | NUMERIC(15,2) |
| TIV_2016    | NUMERIC(15,2) |
| LAT         | NUMERIC(5,2)  |
| LON         | NUMERIC(5,2)  |

**PID** 字段是投保人的投保编号， **TIV_2015** 是该投保人在2015年的总投保金额， **TIV_2016** 是该投保人在2016年的投保金额， **LAT** 是投保人所在城市的维度， **LON** 是投保人所在城市的经度。

**样例输入**

| PID | TIV_2015 | TIV_2016 | LAT | LON |
|-----|----------|----------|-----|-----|
| 1   | 10       | 5        | 10  | 10  |
| 2   | 20       | 20       | 20  | 20  |
| 3   | 10       | 30       | 20  | 20  |
| 4   | 10       | 40       | 40  | 40  |

**样例输出**

| TIV_2016 |
|----------|
| 45.00    |

**解释**

```
就如最后一个投保人，第一个投保人同时满足两个条件：
1. 他在 2015 年的投保金额 TIV_2015 为 '10' ，与第三个和第四个投保人在 2015 年的投保金额相同。
2. 他所在城市的经纬度是独一无二的。

第二个投保人两个条件都不满足。他在 2015 年的投资 TIV_2015 与其他任何投保人都不相同。
且他所在城市的经纬度与第三个投保人相同。基于同样的原因，第三个投保人投资失败。

所以返回的结果是第一个投保人和最后一个投保人的 TIV_2016 之和，结果是 45 。
```



```sql
select sum(TIV_2016) TIV_2016
from (
    select PID,TIV_2016,cnt,
    count(*) over(partition by loc ) lcnt
    from (
        select PID,TIV_2016,
        count(TIV_2015) over(partition by TIV_2015 ) cnt,
        concat_ws(",",LAT,LON) loc
        from insurance 
    )t1
)t2
where lcnt=1 and cnt!=1
```

> 注意去重顺序 不要先对TIV_2015去重  不然 local去重时会丢失数据

优化 窗口

```sql
SELECT 
    ROUND(SUM(TIV_2016), 2) as TIV_2016
FROM(
    SELECT
        *,
        count(*) over(partition by TIV_2015) as cnt_1,
        count(*) over(partition by LAT, LON) as cnt_2
    FROM
        insurance
) a 
WHERE a.cnt_1 > 1 AND a.cnt_2 < 2
```



#### [586. 订单最多的客户](https://leetcode-cn.com/problems/customer-placing-the-largest-number-of-orders/)

难度简单

SQL架构

在表 **orders** 中找到订单数最多客户对应的 **customer_number** 。

数据保证订单数最多的顾客恰好只有一位。

表 ***orders\*** 定义如下：

| Column            | Type      |
|-------------------|-----------|
| order_number (PK) | int       |
| customer_number   | int       |
| order_date        | date      |
| required_date     | date      |
| shipped_date      | date      |
| status            | char(15)  |
| comment           | char(200) |

**样例输入**

| order_number | customer_number | order_date | required_date | shipped_date | status | comment |
|--------------|-----------------|------------|---------------|--------------|--------|---------|
| 1            | 1               | 2017-04-09 | 2017-04-13    | 2017-04-12   | Closed |         |
| 2            | 2               | 2017-04-15 | 2017-04-20    | 2017-04-18   | Closed |         |
| 3            | 3               | 2017-04-16 | 2017-04-25    | 2017-04-20   | Closed |         |
| 4            | 3               | 2017-04-18 | 2017-04-28    | 2017-04-25   | Closed |         |

**样例输出**

| customer_number |
|-----------------|
| 3               |

**解释**

```
customer_number 为 '3' 的顾客有两个订单，比顾客 '1' 或者 '2' 都要多，因为他们只有一个订单
所以结果是该顾客的 customer_number ，也就是 3 。
```

**进阶：** 如果有多位顾客订单数并列最多，你能找到他们所有的 customer_number 吗？



```sql
select customer_number
from orders
group by customer_number 
order by count(*)  desc
limit 1
```

> 如果 数据量很大 order by 不太好



#### [595. 大的国家](https://leetcode-cn.com/problems/big-countries/)

难度简单

SQL架构

这里有张 `World` 表

```
+-----------------+------------+------------+--------------+---------------+
| name            | continent  | area       | population   | gdp           |
+-----------------+------------+------------+--------------+---------------+
| Afghanistan     | Asia       | 652230     | 25500100     | 20343000      |
| Albania         | Europe     | 28748      | 2831741      | 12960000      |
| Algeria         | Africa     | 2381741    | 37100000     | 188681000     |
| Andorra         | Europe     | 468        | 78115        | 3712000       |
| Angola          | Africa     | 1246700    | 20609294     | 100990000     |
+-----------------+------------+------------+--------------+---------------+
```

如果一个国家的面积超过300万平方公里，或者人口超过2500万，那么这个国家就是大国家。

编写一个SQL查询，输出表中所有大国家的名称、人口和面积。

例如，根据上表，我们应该输出:

```
+--------------+-------------+--------------+
| name         | population  | area         |
+--------------+-------------+--------------+
| Afghanistan  | 25500100    | 652230       |
| Algeria      | 37100000    | 2381741      |
+--------------+-------------+--------------+
```



```sql
select  name ,population,area  
from World
where area  >3000000 or population >25000000
```



#### [596. 超过5名学生的课](https://leetcode-cn.com/problems/classes-more-than-5-students/)

难度简单

SQL架构

有一个`courses` 表 ，有: **student (学生)** 和 **class (课程)**。

请列出所有超过或等于5名学生的课。

例如,表:

```
+---------+------------+
| student | class      |
+---------+------------+
| A       | Math       |
| B       | English    |
| C       | Math       |
| D       | Biology    |
| E       | Math       |
| F       | Computer   |
| G       | Math       |
| H       | Math       |
| I       | Math       |
+---------+------------+
```

应该输出:

```
+---------+
| class   |
+---------+
| Math    |
+---------+
```

**Note:**
学生在每个课中不应被重复计算。

```sql
select  class 
from courses
group by class 
having count(distinct student)>=5
```

> 一个学生可能多次选课。。记得distinct



#### [597. 好友申请 I ：总体通过率](https://leetcode-cn.com/problems/friend-requests-i-overall-acceptance-rate/)

难度简单21收藏分享切换为英文关注反馈

SQL架构

在 Facebook 或者 Twitter 这样的社交应用中，人们经常会发好友申请也会收到其他人的好友申请。现在给如下两个表：

表： friend_request

| sender_id | send_to_id | request_date |
| --------- | ---------- | ------------ |
| 1         | 2          | 2016_06-01   |
| 1         | 3          | 2016_06-01   |
| 1         | 4          | 2016_06-01   |
| 2         | 3          | 2016_06-02   |
| 3         | 4          | 2016-06-09   |


表： request_accepted

| requester_id | accepter_id | accept_date |
| ------------ | ----------- | ----------- |
| 1            | 2           | 2016_06-03  |
| 1            | 3           | 2016-06-08  |
| 2            | 3           | 2016-06-08  |
| 3            | 4           | 2016-06-09  |
| 3            | 4           | 2016-06-10  |


写一个查询语句，求出好友申请的通过率，用 2 位小数表示。通过率由接受好友申请的数目除以申请总数。

对于上面的样例数据，你的查询语句应该返回如下结果。

| accept_rate |
| ----------- |
| 0.80        |


注意:

通过的好友申请不一定都在表 friend_request 中。在这种情况下，你只需要统计总的被通过的申请数（不管它们在不在原来的申请中），并将它除以申请总数，得到通过率
一个好友申请发送者有可能会给接受者发几条好友申请，也有可能一个好友申请会被通过好几次。这种情况下，重复的好友申请只统计一次。
如果一个好友申请都没有，通过率为 0.00 。


解释： 总共有 5 个申请，其中 4 个是不重复且被通过的好友申请，所以成功率是 0.80 。

进阶:

你能写一个查询语句得到每个月的通过率吗？
你能求出每一天的累计通过率吗？

```sql
select
round(
    ifnull(
    (select count(*) from (select distinct requester_id, accepter_id from request_accepted) as A)
    /
    (select count(*) from (select distinct sender_id, send_to_id from friend_request) as B),
    0)
, 2) as accept_rate;
```



#### [601. 体育馆的人流量](https://leetcode-cn.com/problems/human-traffic-of-stadium/)

难度困难113收藏分享切换为英文关注反馈

SQL架构

X 市建了一个新的体育馆，每日人流量信息被记录在这三列信息中：**序号** (id)、**日期** (visit_date)、 **人流量** (people)。

请编写一个查询语句，找出人流量的高峰期。高峰期时，至少连续三行记录中的人流量不少于100。

例如，表 `stadium`：

```
+------+------------+-----------+
| id   | visit_date | people    |
+------+------------+-----------+
| 1    | 2017-01-01 | 10        |
| 2    | 2017-01-02 | 109       |
| 3    | 2017-01-03 | 150       |
| 4    | 2017-01-04 | 99        |
| 5    | 2017-01-05 | 145       |
| 6    | 2017-01-06 | 1455      |
| 7    | 2017-01-07 | 199       |
| 8    | 2017-01-08 | 188       |
+------+------------+-----------+
```

对于上面的示例数据，输出为：

```
+------+------------+-----------+
| id   | visit_date | people    |
+------+------------+-----------+
| 5    | 2017-01-05 | 145       |
| 6    | 2017-01-06 | 1455      |
| 7    | 2017-01-07 | 199       |
| 8    | 2017-01-08 | 188       |
+------+------------+-----------+ 
```

**提示：**
每天只有一行记录，日期随着 id 的增加而增加。

3表相连(244 ms)

```sql
select distinct t1.*
from stadium t1, stadium t2, stadium t3
where t1.people >= 100 and t2.people >= 100 and t3.people >= 100
and
(
		(t1.id - t2.id = 1 and t1.id - t3.id = 2 and t2.id - t3.id =1)  -- t1, t2, t3
    or
    (t2.id - t1.id = 1 and t2.id - t3.id = 2 and t1.id - t3.id =1) -- t2, t1, t3
    or
    (t3.id - t2.id = 1 and t2.id - t1.id =1 and t3.id - t1.id = 2) -- t3, t2, t1
)
order by t1.id
```

窗口函数(272 ms)

```sql
select id,visit_date,people from
(
    select id
    ,lead(people,1) over(order by id) ld
    ,lead(people,2) over(order by id) ld2
    ,visit_date
    ,lag(people,1) over(order by id) lg
    ,lag(people,2) over(order by id) lg2
    ,people
    from stadium
    ) a
where (a.ld>=100 and a.lg>=100 and a.people>=100)
or (a.ld>=100 and a.ld2>=100 and a.people>=100)
or (a.lg>=100 and a.lg2>=100 and a.people>=100)
```



#### [602. 好友申请 II ：谁有最多的好友](https://leetcode-cn.com/problems/friend-requests-ii-who-has-the-most-friends/)

难度中等

SQL架构

在 Facebook 或者 Twitter 这样的社交应用中，人们经常会发好友申请也会收到其他人的好友申请。

表 `request_accepted` 存储了所有好友申请通过的数据记录，其中， **requester_id** 和 **accepter_id** 都是用户的编号。

| requester_id | accepter_id | accept_date|
|--------------|-------------|------------|
| 1            | 2           | 2016_06-03 |
| 1            | 3           | 2016-06-08 |
| 2            | 3           | 2016-06-08 |
| 3            | 4           | 2016-06-09 |

写一个查询语句，求出谁拥有最多的好友和他拥有的好友数目。对于上面的样例数据，结果为：

| id | num |
|----|-----|
| 3  | 3   |

**注意：**

- 保证拥有最多好友数目的只有 1 个人。
- 好友申请只会被接受一次，所以不会有 **requester_id** 和 **accepter_id** 值都相同的重复记录。

 

**解释：**

编号为 '3' 的人是编号为 '1'，'2' 和 '4' 的好友，所以他总共有 3 个好友，比其他人都多。

 

**进阶：**

在真实世界里，可能会有多个人拥有好友数相同且最多，你能找到所有这些人吗？

```sql
select rid as `id`,count(aid) as `num`
from
(
    select R1.requester_id as rid,R1.accepter_id as aid
    from request_accepted as R1
    UNION all
    select R2.accepter_id as rid,R2.requester_id as aid
    from request_accepted as R2
) as A
group by rid
order by num desc
limit 0,1
```



#### [603. 连续空余座位](https://leetcode-cn.com/problems/consecutive-available-seats/)

难度简单

SQL架构

几个朋友来到电影院的售票处，准备预约连续空余座位。

你能利用表 `cinema` ，帮他们写一个查询语句，获取所有空余座位，并将它们按照 seat_id 排序后返回吗？

| seat_id | free |
|---------|------|
| 1       | 1    |
| 2       | 0    |
| 3       | 1    |
| 4       | 1    |
| 5       | 1    |

对于如上样例，你的查询语句应该返回如下结果。

| seat_id |
|---------|
| 3       |
| 4       |
| 5       |

**注意：**

- seat_id 字段是一个自增的整数，free 字段是布尔类型（'1' 表示空余， '0' 表示已被占据）。
- 连续空余座位的定义是大于等于 2 个连续空余的座位。

```sql
select seat_id
from (
select seat_id,
lag(seat_id,1,-99) over(order by seat_id) ls,
lead(seat_id,1,-99) over(order by seat_id) rs
from cinema
where free=1
)t1
where  seat_id-ls = 1 or rs-seat_id =1
```

#### [607. 销售员](https://leetcode-cn.com/problems/sales-person/)

难度简单

SQL架构

**描述**

给定 3 个表： `salesperson`， `company`， `orders`。
输出所有表 `salesperson` 中，没有向公司 'RED' 销售任何东西的销售员。

**示例：**
**输入**

表： `salesperson`

```
+----------+------+--------+-----------------+-----------+
| sales_id | name | salary | commission_rate | hire_date |
+----------+------+--------+-----------------+-----------+
|   1      | John | 100000 |     6           | 4/1/2006  |
|   2      | Amy  | 120000 |     5           | 5/1/2010  |
|   3      | Mark | 65000  |     12          | 12/25/2008|
|   4      | Pam  | 25000  |     25          | 1/1/2005  |
|   5      | Alex | 50000  |     10          | 2/3/2007  |
+----------+------+--------+-----------------+-----------+
```

表 `salesperson` 存储了所有销售员的信息。每个销售员都有一个销售员编号 **sales_id** 和他的名字 **name** 。

表： `company`

```
+---------+--------+------------+
| com_id  |  name  |    city    |
+---------+--------+------------+
|   1     |  RED   |   Boston   |
|   2     | ORANGE |   New York |
|   3     | YELLOW |   Boston   |
|   4     | GREEN  |   Austin   |
+---------+--------+------------+
```

表 `company` 存储了所有公司的信息。每个公司都有一个公司编号 **com_id** 和它的名字 **name** 。

表： `orders`

```
+----------+------------+---------+----------+--------+
| order_id | order_date | com_id  | sales_id | amount |
+----------+------------+---------+----------+--------+
| 1        |   1/1/2014 |    3    |    4     | 100000 |
| 2        |   2/1/2014 |    4    |    5     | 5000   |
| 3        |   3/1/2014 |    1    |    1     | 50000  |
| 4        |   4/1/2014 |    1    |    4     | 25000  |
+----------+----------+---------+----------+--------+
```

表 `orders` 存储了所有的销售数据，包括销售员编号 **sales_id** 和公司编号 **com_id** 。

**输出**

```
+------+
| name | 
+------+
| Amy  | 
| Mark | 
| Alex |
+------+
```

**解释**

根据表 `orders` 中的订单 '3' 和 '4' ，容易看出只有 'John' 和 'Pam' 两个销售员曾经向公司 'RED' 销售过。

所以我们需要输出表 `salesperson` 中所有其他人的名字。\

```sql
select name
from salesperson
where sales_id not in
(
    select sales_id
    from orders
    where com_id =
            (
            select com_id 
            from company
            where name ='RED'
            )
)
```



#### [608. 树节点](https://leetcode-cn.com/problems/tree-node/)

难度中等

SQL架构

给定一个表 `tree`，**id** 是树节点的编号， **p_id** 是它父节点的 **id 。**

```
+----+------+
| id | p_id |
+----+------+
| 1  | null |
| 2  | 1    |
| 3  | 1    |
| 4  | 2    |
| 5  | 2    |
+----+------+
```

树中每个节点属于以下三种类型之一：

- 叶子：如果这个节点没有任何孩子节点。
- 根：如果这个节点是整棵树的根，即没有父节点。
- 内部节点：如果这个节点既不是叶子节点也不是根节点。

 

写一个查询语句，输出所有节点的编号和节点的类型，并将结果按照节点编号排序。上面样例的结果为：

```
+----+------+
| id | Type |
+----+------+
| 1  | Root |
| 2  | Inner|
| 3  | Leaf |
| 4  | Leaf |
| 5  | Leaf |
+----+------+
```

 

**解释**

- 节点 '1' 是根节点，因为它的父节点是 NULL ，同时它有孩子节点 '2' 和 '3' 。

- 节点 '2' 是内部节点，因为它有父节点 '1' ，也有孩子节点 '4' 和 '5' 。

- 节点 '3', '4' 和 '5' 都是叶子节点，因为它们都有父节点同时没有孩子节点。

- 样例中树的形态如下： 

  ​			  1
  ​			/   \
  ​          2       3
  ​        /   \
  ​     4       5

 

**注意**

如果树中只有一个节点，你只需要输出它的根属性。



```sql
select id,
    (case when p_id is null then "Root"
    when id not in (select ifnull(p_id,0) from tree) then "Leaf"
    else "Inner" end)  Type
from tree

```



#### [610. 判断三角形](https://leetcode-cn.com/problems/triangle-judgement/)

难度简单

SQL架构

一个小学生 Tim 的作业是判断三条线段是否能形成一个三角形。

然而，这个作业非常繁重，因为有几百组线段需要判断。



假设表 `triangle` 保存了所有三条线段的三元组 x, y, z ，你能帮 Tim 写一个查询语句，来判断每个三元组是否可以组成一个三角形吗？

| x  | y  | z  |
|----|----|----|
| 13 | 15 | 30 |
| 10 | 20 | 15 |

对于如上样例数据，你的查询语句应该返回如下结果：

| x  | y  | z  | triangle |
|----|----|----|----------|
| 13 | 15 | 30 | No       |
| 10 | 20 | 15 | Yes      |



```sql
select x,y,z, 
if(x+y>z && x+z>y && y+z>x,'Yes','No') triangle
from triangle
```



#### [612. 平面上的最近距离](https://leetcode-cn.com/problems/shortest-distance-in-a-plane/)

难度中等

SQL架构

表 `point_2d` 保存了所有点（多于 2 个点）的坐标 (x,y) ，这些点在平面上两两不重合。

写一个查询语句找到两点之间的最近距离，保留 2 位小数。

| x  | y  |
|----|----|
| -1 | -1 |
| 0  | 0  |
| -1 | -2 |

最近距离在点 (-1,-1) 和(-1,2) 之间，距离为 1.00 。所以输出应该为： 

| shortest |
|----------|
| 1.00     |

**注意：**任意点之间的最远距离小于 10000 。

```sql
SELECT
    ROUND(SQRT(MIN((POW(p1.x - p2.x, 2) + POW(p1.y - p2.y, 2)))), 2) AS shortest
FROM
    point_2d p1
        JOIN
    point_2d p2 ON p1.x != p2.x OR p1.y != p2.y
```

优化 ：减少重复计算

```sql
SELECT
    ROUND(SQRT(MIN((POW(p1.x - p2.x, 2) + POW(p1.y - p2.y, 2)))),2) AS shortest
FROM
    point_2d p1
        JOIN
    point_2d p2 ON (p1.x <= p2.x AND p1.y < p2.y)
        OR (p1.x <= p2.x AND p1.y > p2.y)
        OR (p1.x < p2.x AND p1.y = p2.y)
```

#### [613. 直线上的最近距离](https://leetcode-cn.com/problems/shortest-distance-in-a-line/)

难度简单

SQL架构

表 `point` 保存了一些点在 x 轴上的坐标，这些坐标都是整数。

 

写一个查询语句，找到这些点中最近两个点之间的距离。

 

| x   |
|-----|
| -1  |
| 0   |
| 2   |

 

最近距离显然是 '1' ，是点 '-1' 和 '0' 之间的距离。所以输出应该如下：

 

| shortest|
|---------|
| 1       |

 

**注意：**每个点都与其他点坐标不同，表 `table` 不会有重复坐标出现。

 

**进阶：**如果这些点在 x 轴上从左到右都有一个编号，输出结果时需要输出最近点对的编号呢？

 开窗方法 178m

```sql
select min(l-x) shortest
from( 
select x,lead(x,1,null) over(order by x) l
from point
)t1
```

join方法 268m

```sql
SELECT
    MIN(ABS(p1.x - p2.x)) AS shortest
FROM
    point p1
        JOIN
    point p2 ON p1.x != p2.x
;
```





#### [614. 二级关注者](https://leetcode-cn.com/problems/second-degree-follower/)

难度中等

SQL架构

在 facebook 中，表 `follow` 会有 2 个字段： **followee**, **follower** ，分别表示被关注者和关注者。

请写一个 sql 查询语句，对每一个关注者，查询关注他的关注者的数目。

比方说：

```
+-------------+------------+
| followee    | follower   |
+-------------+------------+
|     A       |     B      |
|     B       |     C      |
|     B       |     D      |
|     D       |     E      |
+-------------+------------+
```

应该输出：

```
+-------------+------------+
| follower    | num        |
+-------------+------------+
|     B       |  2         |
|     D       |  1         |
+-------------+------------+
```

**解释：**

B 和 D 都在在 **follower** 字段中出现，作为被关注者，B 被 C 和 D 关注，D 被 E 关注。A 不在 **follower** 字段内，所以A不在输出列表中。

 

**注意：**

- 被关注者永远不会被他 / 她自己关注。
- 将结果按照字典序返回。

 

```sql
select followee follower,count(distinct follower) num
from follow
where followee  in (
    select follower
    from follow
    group by follower
)
group by followee
order by follower 
```

> 这里出现了重复关注 ，需要去重



#### [615. 平均工资：部门与公司比较](https://leetcode-cn.com/problems/average-salary-departments-vs-company/)

难度困难

SQL架构

给如下两个表，写一个查询语句，求出在每一个工资发放日，每个部门的平均工资与公司的平均工资的比较结果 （高 / 低 / 相同）。

 

表： `salary`

| id | employee_id | amount | pay_date   |
|----|-------------|--------|------------|
| 1  | 1           | 9000   | 2017-03-31 |
| 2  | 2           | 6000   | 2017-03-31 |
| 3  | 3           | 10000  | 2017-03-31 |
| 4  | 1           | 7000   | 2017-02-28 |
| 5  | 2           | 6000   | 2017-02-28 |
| 6  | 3           | 8000   | 2017-02-28 |

 

**employee_id** 字段是表 `employee` 中 **employee_id** 字段的外键。

| employee_id | department_id |
|-------------|---------------|
| 1           | 1             |
| 2           | 2             |
| 3           | 2             |

 

对于如上样例数据，结果为：

| pay_month | department_id | comparison  |
|-----------|---------------|-------------|
| 2017-03   | 1             | higher      |
| 2017-03   | 2             | lower       |
| 2017-02   | 1             | same        |
| 2017-02   | 2             | same        |

 

**解释**

在三月，公司的平均工资是 (9000+6000+10000)/3 = 8333.33...

由于部门 '1' 里只有一个 **employee_id** 为 '1' 的员工，所以部门 '1' 的平均工资就是此人的工资 9000 。因为 9000 > 8333.33 ，所以比较结果是 'higher'。

第二个部门的平均工资为 **employee_id** 为 '2' 和 '3' 两个人的平均工资，为 (6000+10000)/2=8000 。因为 8000 < 8333.33 ，所以比较结果是 'lower' 。

在二月用同样的公式求平均工资并比较，比较结果为 'same' ，因为部门 '1' 和部门 '2' 的平均工资与公司的平均工资相同，都是 7000 。

```sql
select 
    pay_month,
    department_id,
     (case when avgs>ts then 'higher'
            when avgs<ts then 'lower'
            else 'same' end) as comparison
from 
(
    select 
        date_format(pay_date,'%Y-%m')pay_month,
        department_id,
        avg(amount) over(partition by date_format(pay_date,'%Y-%m') )ts,
        avg(amount) over(partition by date_format(pay_date,'%Y-%m'),department_id) avgs
    from salary s
    left join employee e
    on s.employee_id = e.employee_id
   
)t1
 group by pay_month, department_id
```



> 也可以用if

```sql
IF(avgs>ts,'higher',IF(avgs=ts,'same','lower')) AS comparison
```

#### [618. 学生地理信息报告](https://leetcode-cn.com/problems/students-report-by-geography/)

难度困难

SQL架构

一所美国大学有来自亚洲、欧洲和美洲的学生，他们的地理信息存放在如下 `student` 表中。

 

| name   | continent |
|--------|-----------|
| Jack   | America   |
| Pascal | Europe    |
| Xi     | Asia      |
| Jane   | America   |

 

写一个查询语句实现对大洲（continent）列的 [透视表](https://zh.wikipedia.org/wiki/透视表) 操作，使得每个学生按照姓名的字母顺序依次排列在对应的大洲下面。输出的标题应依次为美洲（America）、亚洲（Asia）和欧洲（Europe）。数据保证来自美洲的学生不少于来自亚洲或者欧洲的学生。

对于样例输入，它的对应输出是：

| America | Asia | Europe |
|---------|------|--------|
| Jack    | Xi   | Pascal |
| Jane    |      |        |

 

**进阶：**如果不能确定哪个大洲的学生数最多，你可以写出一个查询去生成上述学生报告吗？





开窗

```sql
select
max(if(continent='America',name,null)) America,
max(if(continent='Asia',name,null)) Asia,
max(if(continent='Europe',name,null)) Europe
from 
    (select *, row_number() over(partition by continent order by name) rk
    from student) t
group by rk
```



变量

```sql
SELECT 
    America, Asia, Europe
FROM
    (SELECT @as:=0, @am:=0, @eu:=0) t,
    (SELECT 
        @as:=@as + 1 AS asid, name AS Asia
    FROM
        student
    WHERE
        continent = 'Asia'
    ORDER BY Asia) AS t1
        RIGHT JOIN
    (SELECT 
        @am:=@am + 1 AS amid, name AS America
    FROM
        student
    WHERE
        continent = 'America'
    ORDER BY America) AS t2 ON asid = amid
        LEFT JOIN
    (SELECT 
        @eu:=@eu + 1 AS euid, name AS Europe
    FROM
        student
    WHERE
        continent = 'Europe'
    ORDER BY Europe) AS t3 ON amid = euid
```

> 官方给出的。。同下方开窗

```sql
select America,Asia,Europe 
from(
    select row_number() over(order by name) as rn,name as America from student
    where continent='America'
) a
left join(
    select row_number() over(order by name) as rn,name as Asia from student
    where continent='Asia'
) b on a.rn=b.rn
left join(
    select row_number() over(order by name) as rn,name as Europe from student
    where continent='Europe'
) c on a.rn=c.rn
```



#### [619. 只出现一次的最大数字](https://leetcode-cn.com/problems/biggest-single-number/)

难度简单

SQL架构

表 `my_numbers` 的 **num** 字段包含很多数字，其中包括很多重复的数字。

你能写一个 SQL 查询语句，找到只出现过一次的数字中，最大的一个数字吗？

```
+---+
|num|
+---+
| 8 |
| 8 |
| 3 |
| 3 |
| 1 |
| 4 |
| 5 |
| 6 | 
```

对于上面给出的样例数据，你的查询语句应该返回如下结果：

```
+---+
|num|
+---+
| 6 |
```

**注意：**

如果没有只出现一次的数字，输出 **null** 。

```sql
SELECT
    MAX(num) AS num
FROM
    (SELECT
        num
    FROM
        my_numbers
    GROUP BY num
    HAVING COUNT(num) = 1) t1
```



#### [620. 有趣的电影](https://leetcode-cn.com/problems/not-boring-movies/)

难度简单86收藏分享切换为英文关注反馈

SQL架构

某城市开了一家新的电影院，吸引了很多人过来看电影。该电影院特别注意用户体验，专门有个 LED显示板做电影推荐，上面公布着影评和相关电影描述。

作为该电影院的信息部主管，您需要编写一个 SQL查询，找出所有影片描述为**非** `boring` (不无聊) 的并且 **id 为奇数** 的影片，结果请按等级 `rating` 排列。

 

例如，下表 `cinema`:

```
+---------+-----------+--------------+-----------+
|   id    | movie     |  description |  rating   |
+---------+-----------+--------------+-----------+
|   1     | War       |   great 3D   |   8.9     |
|   2     | Science   |   fiction    |   8.5     |
|   3     | irish     |   boring     |   6.2     |
|   4     | Ice song  |   Fantacy    |   8.6     |
|   5     | House card|   Interesting|   9.1     |
+---------+-----------+--------------+-----------+
```

对于上面的例子，则正确的输出是为：

```
+---------+-----------+--------------+-----------+
|   id    | movie     |  description |  rating   |
+---------+-----------+--------------+-----------+
|   5     | House card|   Interesting|   9.1     |
|   1     | War       |   great 3D   |   8.9     |
+---------+-----------+--------------+-----------+
```



```sql
select  id,movie,description,rating 
from cinema
where id%2=1 and description !='boring'
order by rating desc,id,movie,description
```



#### [626. 换座位](https://leetcode-cn.com/problems/exchange-seats/)

难度中等

SQL架构

小美是一所中学的信息科技老师，她有一张 `seat` 座位表，平时用来储存学生名字和与他们相对应的座位 id。

其中纵列的 **id** 是连续递增的

小美想改变相邻俩学生的座位。

你能不能帮她写一个 SQL query 来输出小美想要的结果呢？ 

**示例：**

```
+---------+---------+
|    id   | student |
+---------+---------+
|    1    | Abbot   |
|    2    | Doris   |
|    3    | Emerson |
|    4    | Green   |
|    5    | Jeames  |
+---------+---------+
```

假如数据输入的是上表，则输出结果如下：

```
+---------+---------+
|    id   | student |
+---------+---------+
|    1    | Doris   |
|    2    | Abbot   |
|    3    | Green   |
|    4    | Emerson |
|    5    | Jeames  |
+---------+---------+
```

**注意：**

如果学生人数是奇数，则不需要改变最后一个同学的座位。

开窗

```sql
select id,
(case when id%2=0 then f
      when id%2=1 && b is not null then b
      else student end) student
from(
    select id,student,
    lag(student,1,null) over(order by id) f,
    lead(student,1,null) over(order by id) b
    from seat
)t1
```

非嵌套

```sql
select 
    if(id%2=0,
        id-1,
        if(id=(select count(distinct id) from seat),
            id,
            id+1)) 
    as id,student 
from seat 
order by id;
```

用异或

```sql
select b.id,a.student from 
seat as a,seat as b,(select count(*) as cnt from seat) as c 
where b.id=1^(a.id-1)+1
-- where a.id=1^(b.id-1)+1; 也可以这样写，更容易理解
 || (c.cnt%2 && b.id=c.cnt && a.id=c.cnt);
```

#### [627. 交换工资](https://leetcode-cn.com/problems/swap-salary/)

难度简单

SQL架构

给定一个 `salary` 表，如下所示，有 m = 男性 和 f = 女性 的值。交换所有的 f 和 m 值（例如，将所有 f 值更改为 m，反之亦然）。要求只使用一个更新（Update）语句，并且没有中间的临时表。

注意，您必只能写一个 Update 语句，请不要编写任何 Select 语句。

**例如：**

| id | name | sex | salary |
|----|------|-----|--------|
| 1  | A    | m   | 2500   |
| 2  | B    | f   | 1500   |
| 3  | C    | m   | 5500   |
| 4  | D    | f   | 500    |

运行你所编写的更新语句之后，将会得到以下表:

| id | name | sex | salary |
|----|------|-----|--------|
| 1  | A    | f   | 2500   |
| 2  | B    | m   | 1500   |
| 3  | C    | f   | 5500   |
| 4  | D    | m   | 500    |

```sql
UPDATE salary
SET
    sex = CASE sex
        WHEN 'm' THEN 'f'
        ELSE 'm'
    END;

```

> Update 和set的使用

#### [1045. 买下所有产品的客户](https://leetcode-cn.com/problems/customers-who-bought-all-products/)

难度中等

SQL架构

`Customer` 表：

```
+-------------+---------+
| Column Name | Type    |
+-------------+---------+
| customer_id | int     |
| product_key | int     |
+-------------+---------+
product_key 是 Customer 表的外键。
```

`Product` 表：

```
+-------------+---------+
| Column Name | Type    |
+-------------+---------+
| product_key | int     |
+-------------+---------+
product_key 是这张表的主键。
```

 

写一条 SQL 查询语句，从 `Customer` 表中查询购买了 `Product` 表中所有产品的客户的 id。

**示例：**

```
Customer 表：
+-------------+-------------+
| customer_id | product_key |
+-------------+-------------+
| 1           | 5           |
| 2           | 6           |
| 3           | 5           |
| 3           | 6           |
| 1           | 6           |
+-------------+-------------+

Product 表：
+-------------+
| product_key |
+-------------+
| 5           |
| 6           |
+-------------+

Result 表：
+-------------+
| customer_id |
+-------------+
| 1           |
| 3           |
+-------------+
购买了所有产品（5 和 6）的客户的 id 是 1 和 3 。
```



```sql
select customer_id
from Customer
group by customer_id
having count(distinct product_key)=(
select count(*) cnt
from Product)
```



#### [1050. 合作过至少三次的演员和导演](https://leetcode-cn.com/problems/actors-and-directors-who-cooperated-at-least-three-times/)

难度简单

SQL架构

`ActorDirector` 表：

```
+-------------+---------+
| Column Name | Type    |
+-------------+---------+
| actor_id    | int     |
| director_id | int     |
| timestamp   | int     |
+-------------+---------+
timestamp 是这张表的主键.
```

 

写一条SQL查询语句获取合作过至少三次的演员和导演的 id 对 `(actor_id, director_id)`

**示例：**

```
ActorDirector 表：
+-------------+-------------+-------------+
| actor_id    | director_id | timestamp   |
+-------------+-------------+-------------+
| 1           | 1           | 0           |
| 1           | 1           | 1           |
| 1           | 1           | 2           |
| 1           | 2           | 3           |
| 1           | 2           | 4           |
| 2           | 1           | 5           |
| 2           | 1           | 6           |
+-------------+-------------+-------------+

Result 表：
+-------------+-------------+
| actor_id    | director_id |
+-------------+-------------+
| 1           | 1           |
+-------------+-------------+
唯一的 id 对是 (1, 1)，他们恰好合作了 3 次。
```



```sql
select actor_id,director_id
from ActorDirector
group by actor_id,director_id
having count(*)>=3
```

#### [1068. 产品销售分析 I](https://leetcode-cn.com/problems/product-sales-analysis-i/)

难度简单

SQL架构

销售表 `Sales`：

```
+-------------+-------+
| Column Name | Type  |
+-------------+-------+
| sale_id     | int   |
| product_id  | int   |
| year        | int   |
| quantity    | int   |
| price       | int   |
+-------------+-------+
(sale_id, year) 是销售表 Sales 的主键.
product_id 是产品表 Product 的外键.
注意: price 表示每单位价格
```

产品表 `Product`：

```
+--------------+---------+
| Column Name  | Type    |
+--------------+---------+
| product_id   | int     |
| product_name | varchar |
+--------------+---------+
product_id 是表的主键.
```

写一条SQL 查询语句获取产品表 `Product` 中所有的 **产品名称 product name** 以及 该产品在 `Sales` 表中相对应的 **上市年份 year** 和 **价格 price**。

示例：

```
Sales 表：
+---------+------------+------+----------+-------+
| sale_id | product_id | year | quantity | price |
+---------+------------+------+----------+-------+ 
| 1       | 100        | 2008 | 10       | 5000  |
| 2       | 100        | 2009 | 12       | 5000  |
| 7       | 200        | 2011 | 15       | 9000  |
+---------+------------+------+----------+-------+

Product 表：
+------------+--------------+
| product_id | product_name |
+------------+--------------+
| 100        | Nokia        |
| 200        | Apple        |
| 300        | Samsung      |
+------------+--------------+

Result 表：
+--------------+-------+-------+
| product_name | year  | price |
+--------------+-------+-------+
| Nokia        | 2008  | 5000  |
| Nokia        | 2009  | 5000  |
| Apple        | 2011  | 9000  |
+--------------+-------+-------+
```



```sql
select product_name,year,price
from Sales s left join Product p
on s.product_id  = p.product_id 
```



#### [1069. 产品销售分析 II](https://leetcode-cn.com/problems/product-sales-analysis-ii/)

难度简单

SQL架构

销售表：`Sales`

```
+-------------+-------+
| Column Name | Type  |
+-------------+-------+
| sale_id     | int   |
| product_id  | int   |
| year        | int   |
| quantity    | int   |
| price       | int   |
+-------------+-------+
sale_id 是这个表的主键。
product_id 是 Product 表的外键。
请注意价格是每单位的。
```

产品表：`Product`

```
+--------------+---------+
| Column Name  | Type    |
+--------------+---------+
| product_id   | int     |
| product_name | varchar |
+--------------+---------+
product_id 是这个表的主键。
```

 

编写一个 SQL 查询，按产品 id `product_id` 来统计每个产品的销售总量。

 

查询结果格式如下面例子所示:

```
Sales 表：
+---------+------------+------+----------+-------+
| sale_id | product_id | year | quantity | price |
+---------+------------+------+----------+-------+ 
| 1       | 100        | 2008 | 10       | 5000  |
| 2       | 100        | 2009 | 12       | 5000  |
| 7       | 200        | 2011 | 15       | 9000  |
+---------+------------+------+----------+-------+

Product 表：
+------------+--------------+
| product_id | product_name |
+------------+--------------+
| 100        | Nokia        |
| 200        | Apple        |
| 300        | Samsung      |
+------------+--------------+

Result 表：
+--------------+----------------+
| product_id   | total_quantity |
+--------------+----------------+
| 100          | 22             |
| 200          | 15             |
+--------------+----------------+
```

```sql
select s.product_id,sum(quantity) total_quantity
from Sales s left join Product p
on s.product_id  = p.product_id
group by s.product_id
```



#### [1070. 产品销售分析 III](https://leetcode-cn.com/problems/product-sales-analysis-iii/)

难度中等

SQL架构

销售表 `Sales`：

```
+-------------+-------+
| Column Name | Type  |
+-------------+-------+
| sale_id     | int   |
| product_id  | int   |
| year        | int   |
| quantity    | int   |
| price       | int   |
+-------------+-------+
sale_id 是此表的主键。
product_id 是产品表的外键。
请注意，价格是按每单位计的。
```

产品表 `Product`：

```
+--------------+---------+
| Column Name  | Type    |
+--------------+---------+
| product_id   | int     |
| product_name | varchar |
+--------------+---------+
product_id 是此表的主键。
```

 

编写一个 SQL 查询，选出每个销售产品的 **第一年** 的 **产品 id**、**年份**、**数量** 和 **价格**。

查询结果格式如下：

```
Sales table:
+---------+------------+------+----------+-------+
| sale_id | product_id | year | quantity | price |
+---------+------------+------+----------+-------+ 
| 1       | 100        | 2008 | 10       | 5000  |
| 2       | 100        | 2009 | 12       | 5000  |
| 7       | 200        | 2011 | 15       | 9000  |
+---------+------------+------+----------+-------+

Product table:
+------------+--------------+
| product_id | product_name |
+------------+--------------+
| 100        | Nokia        |
| 200        | Apple        |
| 300        | Samsung      |
+------------+--------------+

Result table:
+------------+------------+----------+-------+
| product_id | first_year | quantity | price |
+------------+------------+----------+-------+ 
| 100        | 2008       | 10       | 5000  |
| 200        | 2011       | 15       | 9000  |
+------------+------------+----------+-------+
```

```sql
select product_id,year first_year,quantity,price
from (
select s.product_id,year,quantity,price,
rank() over(partition by product_id order by year) rk
from Sales s left join Product p
on s.product_id  = p.product_id
)t1
where rk = 1
```

