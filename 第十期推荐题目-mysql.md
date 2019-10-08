## 推荐题目



这次看看mysql类的题目.简单做了一下分类.有些题似乎需要会员,我这里尽量列出了题目

推荐使用typora查看,能看到目录.

<img src="assets/第十期推荐题目-mysql/image-20191008221744876.png" alt="image-20191008221744876" style="zoom:50%;" />

## Easy

### [584. 寻找用户推荐人](https://leetcode-cn.com/problems/find-customer-referee/) 

给定表 customer ，里面保存了所有客户信息和他们的推荐人。

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

写一个查询语句，返回一个编号列表，列表中编号的推荐人的编号都 不是 2。

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

```mysql
select name from customer where referee_id != 2 or referee_id is null
```

> 注意包含null 
> null 不能使用 = , != 比较，永远返回false， 应该使用`is null` , `is not null`



### [620. 有趣的电影](https://leetcode-cn.com/problems/not-boring-movies/)

作为该电影院的信息部主管，您需要编写一个 SQL查询，找出所有影片描述为非 boring (不无聊) 的并且 id 为奇数 的影片，结果请按等级 rating 排列。

```mysql
SELECT id, movie, description, rating
FROM cinema
WHERE description != 'boring'
	AND id % 2 = 1
ORDER BY rating DESC;

# 或者 mod(id, 2) = 1
```



## Union

**union等集合操作**

Union：对两个结果集进行并集操作，不包括重复行，同时进行默认规则的排序；

Union All：对两个结果集进行并集操作，包括重复行，不进行排序；

Intersect：对两个结果集进行交集操作，不包括重复行，同时进行默认规则的排序；

Minus：对两个结果集进行差操作，不包括重复行，同时进行默认规则的排序。

可以在最后一个结果集中指定Order by子句改变排序方式。

### [602. 好友申请 II ：谁有最多的好友](https://leetcode-cn.com/problems/friend-requests-ii-who-has-the-most-friends/)

在 Facebook 或者 Twitter 这样的社交应用中，人们经常会发好友申请也会收到其他人的好友申请。

表 request_accepted 存储了所有好友申请通过的数据记录，其中， requester_id 和 accepter_id 都是用户的编号。

| requester_id | accepter_id | accept_date |
| ------------ | ----------- | ----------- |
| 1            | 2           | 2016_06-03  |
| 1            | 3           | 2016-06-08  |
| 2            | 3           | 2016-06-08  |
| 3            | 4           | 2016-06-09  |

写一个查询语句，求出谁拥有最多的好友和他拥有的好友数目。对于上面的样例数据，结果为：

| id   | num  |
| ---- | ---- |
| 3    | 3    |

注意：

保证拥有最多好友数目的只有 1 个人。
好友申请只会被接受一次，所以不会有 requester_id 和 accepter_id 值都相同的重复记录。

解释：

编号为 ‘3’ 的人是编号为 ‘1’，’2’ 和 ‘4’ 的好友，所以他总共有 3 个好友，比其他人都多。



```mysql
SELECT r3.requester_id AS id, COUNT(r3.requester_id) AS num
FROM (
	SELECT r1.requester_id
	FROM request_accepted r1
	UNION ALL
	SELECT r2.accepter_id
	FROM request_accepted r2
) r3
GROUP BY r3.requester_id
ORDER BY COUNT(r3.requester_id) DESC
LIMIT 1
```





## Inner join

### [614. 二级关注者](https://leetcode-cn.com/problems/second-degree-follower/)

> 被关注者的关注者

在 facebook 中，表 follow 会有 2 个字段： followee, follower ，分别表示被关注者和关注者。

请写一个 sql 查询语句，对每一个关注者，查询他的关注者数目。

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
应该输出：

+-------------+------------+
| follower    | num        |
+-------------+------------+
|     B       |  2         |
|     D       |  1         |]
+-------------+------------+
```

解释：

B 和 D 都在在 follower 字段中出现，作为被关注者，B 被 C 和 D 关注，D 被 E 关注。A 不在 follower 字段内，所以A不在输出列表中。

```mysql
# 考虑重复情况
SELECT f1.follower, COUNT(DISTINCT f2.follower) AS num
FROM follow f1
 INNER	JOIN follow f2 ON f1.follower = f2.followee
GROUP BY f1.follower
```

> INNER JOIN 关键字在表中存在至少一个匹配时返回行。如果 “from1” 中的行在 “from2” 中没有匹配，就不会列出这些行。
>
> INNER JOIN 与 JOIN 是相同的。
>
> 对于重复会多次匹配。

### [613. 直线上的最近距离](https://leetcode-cn.com/problems/shortest-distance-in-a-line/)

表 point 保存了一些点在 x 轴上的坐标，这些坐标都是整数。

写一个查询语句，找到这些点中最近两个点之间的距离。

| x    |
| ---- |
| -1   |
| 0    |
| 2    |

最近距离显然是 ‘1’ ，是点 ‘-1’ 和 ‘0’ 之间的距离。所以输出应该如下：

| shortest |
| -------- |
| 1        |

```mysql
SELECT min(abs(p1.x - p2.x)) shortest
FROM  point p1 inner join point p2 
ON p1.x != p2.x
```

### [612. 平面上的最近距离](https://leetcode-cn.com/problems/shortest-distance-in-a-plane/)



```mysql
SELECT
    p1.x,
    p1.y,
    p2.x,
    p2.y,
    SQRT((POW(p1.x - p2.x, 2) + POW(p1.y - p2.y, 2))) AS distance
FROM
    point_2d p1
        JOIN
    point_2d p2 ON p1.x != p2.x OR p1.y != p2.y

```





### [197. 上升的温度](https://leetcode-cn.com/problems/rising-temperature/)

给定一个 Weather 表，编写一个 SQL 查询，来查找与之前（昨天的）日期相比温度更高的所有日期的 Id。

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

例如，根据上述给定的 Weather 表格，返回如下 Id:

```
+----+
| Id |
+----+
|  2 |
|  4 |
+----+
```





```mysql
# SELECT DATEDIFF('2008-12-30','2008-12-29') AS DiffDate =  1
select 
  w1.Id 
from 
  Weather w1 
   inner join 
  Weather w2 on datediff(w1.RecordDate,w2.RecordDate)=1 
    and 
  w1.Temperature>w2.Temperature;
```



### [603. 连续空余座位](https://leetcode-cn.com/problems/consecutive-available-seats/)

几个朋友来到电影院的售票处，准备预约连续空余座位。

你能利用表 cinema ，帮他们写一个查询语句，获取所有空余座位，并将它们按照 seat_id 排序后返回吗？

| seat_id | free |
| ------- | ---- |
| 1       | 1    |
| 2       | 0    |
| 3       | 1    |
| 4       | 1    |
| 5       | 1    |

对于如上样例，你的查询语句应该返回如下结果。

| seat_id |
| ------- |
| 3       |
| 4       |
| 5       |

注意：

seat_id 字段是一个自增的整数，free 字段是布尔类型（’1’ 表示空余， ‘0’ 表示已被占据）。
连续空余座位的定义是大于等于 2 个连续空余的座位。

两个连在一起就算连续

```mysql
select distinct a.seat_id
from cinema a join cinema b  # 自连接
  on abs(a.seat_id - b.seat_id) = 1
  and a.free = true and b.free = true
order by a.seat_id
;
```



## Left Join

### [75. 组合两个表](https://leetcode-cn.com/problems/combine-two-tables/)

编写一个 SQL 查询，满足条件：无论 person 是否有地址信息，都需要基于上述两表提供 person 的以下信息：

```mysql
SELECT
	p.FirstName,
	p.LastName,
	a.City,
	a.State 
FROM
	Person p
	LEFT JOIN Address a ON p.PersonId = a.PersonId
```



### [577. 员工奖金](https://leetcode-cn.com/problems/employee-bonus/)

选出所有 bonus < 1000 的员工的 name 及其 bonus。

Employee 表单

```
+-------+--------+-----------+--------+
| empId |  name  | supervisor| salary |
+-------+--------+-----------+--------+
|   1   | John   |  3        | 1000   |
|   2   | Dan    |  3        | 2000   |
|   3   | Brad   |  null     | 4000   |
|   4   | Thomas |  3        | 4000   |
+-------+--------+-----------+--------+
```

empId 是这张表单的主关键字
Bonus 表单

```
+-------+-------+
| empId | bonus |
+-------+-------+
| 2     | 500   |
| 4     | 2000  |
+-------+-------+
```

empId 是这张表单的主关键字
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

无论这个员工有没有奖金，这个员工都应被保留，使用左查询。

```mysql
SELECT e.name, b.bonus
FROM Employee e
	LEFT JOIN Bonus b ON e.empId = b.empId
WHERE b.bonus IS NULL
	OR b.bonus < 1000

```

### [608. 树节点](https://leetcode-cn.com/problems/tree-node/)

给定一个表 tree，id 是树节点的编号， p_id 是它父节点的 id 。

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

叶子：如果这个节点没有任何孩子节点。
根：如果这个节点是整棵树的根，即没有父节点。
内部节点：如果这个节点既不是叶子节点也不是根节点。

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

解释

节点 ‘1’ 是根节点，因为它的父节点是 NULL ，同时它有孩子节点 ‘2’ 和 ‘3’ 。
节点 ‘2’ 是内部节点，因为它有父节点 ‘1’ ，也有孩子节点 ‘4’ 和 ‘5’ 。
节点 ‘3’, ‘4’ 和 ‘5’ 都是叶子节点，因为它们都有父节点同时没有孩子节点。
样例中树的形态如下：

```
			  1
			/   \
	      2       3
	                /   \
	              4       5


```

所有的节点记录一定要留下，以左边为准，左连接。

```mysql
SELECT DISTINCT t1.id
	, CASE 
		WHEN t1.p_id IS NULL THEN 'Root'
		WHEN t2.id IS NOT NULL THEN 'Inner'
		ELSE 'Leaf'
	END AS Type
FROM tree t1
	LEFT JOIN tree t2 ON t1.id = t2.p_id
ORDER BY t1.id

```



> left join 语意： 左边的记录一定要保留





### [580. 统计各专业学生人数](https://leetcode-cn.com/problems/count-student-number-in-departments/)



一所大学有 2 个数据表，分别是 student 和 department ，这两个表保存着每个专业的学生数据和院系数据。

写一个查询语句，查询 department 表中每个专业的学生人数 （即使没有学生的专业也需列出）。

将你的查询结果按照学生人数降序排列。 如果有两个或两个以上专业有相同的学生数目，将这些部门按照部门名字的字典序从小到大排列。

student 表格如下：

| Column Name  | Type      |
| ------------ | --------- |
| student_id   | Integer   |
| student_name | String    |
| gender       | Character |
| dept_id      | Integer   |

其中， student_id 是学生的学号， student_name 是学生的姓名， gender 是学生的性别， dept_id 是学生所属专业的专业编号。

department 表格如下：

| Column Name | Type    |
| ----------- | ------- |
| dept_id     | Integer |
| dept_name   | String  |

dept_id 是专业编号， dept_name 是专业名字。

这里是一个示例输入：
student 表格：

| student_id | student_name | gender | dept_id |
| ---------- | ------------ | ------ | ------- |
| 1          | Jack         | M      | 1       |
| 2          | Jane         | F      | 1       |
| 3          | Mark         | M      | 2       |

department 表格：

| dept_id | dept_name   |
| ------- | ----------- |
| 1       | Engineering |
| 2       | Science     |
| 3       | Law         |

示例输出为：

| dept_name   | student_number |
| ----------- | -------------- |
| Engineering | 2              |
| Science     | 1              |
| Law         | 0              |

就算没学生～， 专业也在啊,所以用department left join student

```mysql
SELECT d.dept_name, COUNT(s.dept_id) AS 'student_number'
FROM department d
	LEFT JOIN student s ON d.dept_id = s.dept_id
GROUP BY d.dept_id
ORDER BY student_number DESC, dept_name;

```





### [181. 超过经理收入的员工](https://leetcode-cn.com/problems/employees-earning-more-than-their-managers/).  

Employee 表包含所有员工，他们的经理也属于员工。每个员工都有一个 Id，此外还有一列对应员工的经理的 Id。

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

给定 Employee 表，编写一个 SQL 查询，该查询可以获取收入超过他们经理的员工的姓名。在上面的表格中，Joe 是唯一一个收入超过他的经理的员工。

```
+----------+
| Employee |
+----------+
| Joe      |
+----------+

```



```mysql
SELECT
	a.NAME AS Employee 
FROM
	Employee a
	LEFT JOIN Employee b ON a.ManagerId = b.Id 
WHERE
	a.Salary > b.Salary;

```



## 交叉连接

示例：

```
+----+---------+
| Id | Email   |
+----+---------+
| 1  | a@b.com |
| 2  | c@d.com |
| 3  | a@b.com |
+----+---------+

```

### [182. 查找重复的电子邮箱](https://leetcode-cn.com/problems/duplicate-emails/)

```mysql
select Email from Person group by Email having count(Email) > 1;
select Email from Person group by Email having count(1) > 1;
select Email from Person group by Email having count(*) > 1;


```

### [196. 删除重复的电子邮箱](https://leetcode-cn.com/problems/delete-duplicate-emails/)

```mysql
# 表连接 交叉连接
SELECT p1.*
FROM Person p1,
    Person p2
WHERE
    p1.Email = p2.Email AND p1.Id > p2.Id
;

DELETE p1 FROM Person p1,
    Person p2
WHERE
    p1.Email = p2.Email AND p1.Id > p2.Id


# 子查询
DELETE from Person 
Where Id not in (
    Select Id 
    From(
    Select MIN(Id) as id
    From Person 
    Group by Email
   ) t
)

```





### [180. 连续出现的数字](https://leetcode-cn.com/problems/consecutive-numbers/)

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

例如，给定上面的 Logs 表， 1 是唯一连续出现至少三次的数字。

```
+-----------------+
| ConsecutiveNums |
+-----------------+
| 1               |
+-----------------+

```



表连接

需要添加关键字 `DISTINCT` ，因为如果一个数字连续出现超过 3 次，会返回重复元素。

```mysql
SELECT DISTINCT
    l1.Num AS ConsecutiveNums
FROM
    Logs l1,
    Logs l2,
    Logs l3
WHERE
    l2.Id = l1.Id + 1
    AND l2.Id = l3.Id - 1
    AND l1.Num = l2.Num
    AND l2.Num = l3.Num
;

```

用户变量

```mysql
select distinct Num as ConsecutiveNums
from (
  select Num, 
    case 
      when @prev = Num then @count := @count + 1
      when @prev := Num then @count := 1
    end as CNT
  from Logs, (select @prev := null,@count := null) as t
) as temp
where temp.CNT >= 3

```



### [601. 体育馆的人流量](https://leetcode-cn.com/problems/human-traffic-of-stadium/)

请编写一个查询语句，找出人流量的高峰期。高峰期时，至少连续三行记录中的人流量不少于100。

例如，表 stadium：

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

提示：
每天只有一行记录，日期随着 id 的增加而增加。



```mysql
# SELECT count(*) FROM stadium AS s1, stadium AS s2, stadium as s3  = 512
# Write your MySQL query statement below
SELECT s1.* FROM stadium AS s1, stadium AS s2, stadium as s3
    WHERE 
    ((s1.id + 1 = s2.id
    AND s1.id + 2 = s3.id)
    OR 
    (s1.id - 1 = s2.id
    AND s1.id + 1 = s3.id)
    OR
    (s1.id - 2 = s2.id
    AND s1.id - 1 = s3.id)
    )
    AND s1.people>=100 
    AND s2.people>=100
    AND s3.people>=100

 GROUP BY s1.id


```







## 子查询

**需要临时表完成操作**

### [183. 从不订购的客户](https://leetcode-cn.com/problems/customers-who-never-order/)

在订单表从来没有记录

```mysql
SELECT cu.Name AS Customers
FROM Customers cu where cu.id  not in  (select CustomerId from Orders);

```



### [619. 只出现一次的最大数字](https://leetcode-cn.com/problems/biggest-single-number/)

表 my_numbers 的 num 字段包含很多数字，其中包括很多重复的数字。

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

注意：

如果没有只出现一次的数字，输出 null 。



最外层的select是为了输出null

```mysql
SELECT (
		SELECT num
		FROM (
			SELECT num
			FROM my_numbers
			GROUP BY num
			HAVING COUNT(num) = 1
		) t
		ORDER BY t.num DESC
		LIMIT 1
	) AS num

```





### [585. 2016年的投资](https://leetcode-cn.com/problems/investments-in-2016/)

写一个查询语句，将 2016 年 (TIV_2016) 所有成功投资的金额加起来，保留 2 位小数。

对于一个投保人，他在 2016 年成功投资的条件是：

他在 2015 年的投保额 (TIV_2015) 至少跟一个其他投保人在 2015 年的投保额相同。
他所在的城市必须与其他投保人都不同（也就是说维度和经度不能跟其他任何一个投保人完全相同）。
输入格式:
表 insurance 格式如下：

| Column Name | Type          |
| ----------- | ------------- |
| PID         | INTEGER(11)   |
| TIV_2015    | NUMERIC(15,2) |
| TIV_2016    | NUMERIC(15,2) |
| LAT         | NUMERIC(5,2)  |
| LON         | NUMERIC(5,2)  |

PID 字段是投保人的投保编号， TIV_2015 是该投保人在2015年的总投保金额， TIV_2016 是该投保人在2016年的投保金额， LAT 是投保人所在城市的维度， LON 是投保人所在城市的经度。

样例输入

| PID  | TIV_2015 | TIV_2016 | LAT  | LON  |
| ---- | -------- | -------- | ---- | ---- |
| 1    | 10       | 5        | 10   | 10   |
| 2    | 20       | 20       | 20   | 20   |
| 3    | 10       | 30       | 20   | 20   |
| 4    | 10       | 40       | 40   | 40   |

样例输出null

| TIV_2016 |
| -------- |
| 45.00    |

解释

就如最后一个投保人，第一个投保人同时满足两个条件：

1. 他在 2015 年的投保金额 TIV_2015 为 ‘10’ ，与第三个和第四个投保人在 2015 年的投保金额相同。
2. 他所在城市的经纬度是独一无二的。

第二个投保人两个条件都不满足。他在 2015 年的投资 TIV_2015 与其他任何投保人都不相同。
且他所在城市的经纬度与第三个投保人相同。基于同样的原因，第三个投保人投资失败。

所以返回的结果是第一个投保人和最后一个投保人的 TIV_2016 之和，结果是 45 。



```mysql 
   select ROUND(SUM(TIV_2016),2) as  TIV_2016 
    from insurance
    where (LAT,LON) not IN
    (
        select LAT,LON from insurance
        group by LAT,LON
        having count(PID) > 1
    )
    and TIV_2015 IN
    (
        select TIV_2015 from insurance
        group by TIV_2015
        having count(PID) > 1
    )

```

### [178. 分数排名](https://leetcode-cn.com/problems/rank-scores/)

编写一个 SQL 查询来实现分数排名。如果两个分数相同，则两个分数排名（Rank）相同。请注意，平分后的下一个名次应该是下一个连续的整数值。换句话说，名次之间不应该有“间隔”。

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

例如，根据上述给定的 Scores 表，你的查询应该返回（按分数从高到低排列）：

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

```mysql
SELECT
	Score,
	( SELECT count( DISTINCT score ) FROM Scores WHERE score >= s.score ) AS Rank 
FROM
	Scores s 
ORDER BY
	Score DESC;

```



### [185. 部门工资前三高的员工](https://leetcode-cn.com/problems/department-top-three-salaries/)

Employee 表包含所有员工信息，每个员工有其对应的工号 Id，姓名 Name，工资 Salary 和部门编号 DepartmentId 。

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

Department 表包含公司所有部门的信息。

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

解释：

IT 部门中，Max 获得了最高的工资，Randy 和 Joe 都拿到了第二高的工资，Will 的工资排第三。销售部门（Sales）只有两名员工，Henry 的工资最高，Sam 的工资排第二。



在where中使用子查询。

```mysql
SELECT d.Name AS Department, e.Name AS Employee, e.Salary AS Salary
FROM Employee e
	INNER JOIN Department d on e.DepartmentId = d.Id
WHERE (
	SELECT COUNT( distinct Salary)
	FROM Employee
	WHERE Salary > e.Salary
		AND DepartmentId = e.DepartmentId
) < 3
ORDER BY Department, Salary DESC

```



## Group by

**根据其他条件筛选某一列。**

### [586. 订单最多的客户](https://leetcode-cn.com/problems/customer-placing-the-largest-number-of-orders/)

group by

```mysql
SELECT customer_number
FROM orders
GROUP BY customer_number
ORDER BY COUNT(order_number) DESC
LIMIT 1

```



### [596. 超过5名学生的课](https://leetcode-cn.com/problems/classes-more-than-5-students/)

```mysql
		SELECT class
		FROM courses
		GROUP BY class
		HAVING COUNT(DISTINCT student) >= 5

```





### [1045. 买下所有产品的客户](https://leetcode-cn.com/problems/customers-who-bought-all-products/)

```
Customer 表：

+-------------+---------+
| Column Name | Type    |
+-------------+---------+
| customer_id | innullt     |
| product_key | int     |
+-------------+---------+
product_key 是 Product 表的外键。
Product 表：

+-------------+---------+
| Column Name | Type    |
+-------------+---------+
| product_key | int     |
+-------------+---------+
product_key 是这张表的主键。

```

写一条 SQL 查询语句，从 Customer 表中查询购买了 Product 表中所有产品的客户的 id。

示例：

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

```

购买了所有产品（5 和 6）的客户的 id 是 1 和 3 。

写一条 SQL 查询语句，从 `Customer` 表中查询购买了 `Product` 表中所有产品的客户的 id。

```mysql
SELECT customer_id
FROM Customer
GROUP BY customer_id
HAVING COUNT(DISTINCT product_key) = (
	SELECT COUNT(DISTINCT product_key)
	FROM Product
);

```



### [574. 当选者/票数最高的人](https://leetcode-cn.com/problems/winning-candidate/)

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

**注意:**

1. 你可以假设**没有平局**，换言之，**最多**只有一位当选者。



```mysql
SELECT Name
FROM Candidate
WHERE id = (
	SELECT CandidateId
	FROM Vote
	GROUP BY CandidateId
	ORDER BY count(1) DESC
	LIMIT 1
) 


```



### [578. 查询回答率最高的问题](https://leetcode-cn.com/problems/get-highest-answer-rate-question/)

从 survey_log 表中获得回答率最高的问题，`survey_log` 表包含这些列：**uid, action, question_id, answer_id, q_num, timestamp**

uid 表示用户 id；action 有以下几种值：“show”，“answer”，“skip”；当 action 值为 “answer” 时 answer_id 非空，而 action 值为 “show” 或者 “skip” 时 answer_id 为空；q_num 表示当前会话中问题的编号。

请编写SQL查询来找到具有最高回答率的问题。

示例:

输入:

```
+------+-----------+--------------+------------+-----------+------------+
| uid  | action    | question_id  | answer_id  | q_num     | timestamp  |
+------+-----------+--------------+------------+-----------+------------+
| 5    | show      | 285          | null       | 1         | 123        |
| 5    | answer    | 285          | 124124     | 1         | 124        |
| 5    | show      | 369          | null       | 2         | 125        |
| 5    | skip      | 369          | null       | 2         | 126        |
+------+-----------+--------------+------------+-----------+------------+
输出:
+-------------+
| survey_log  |
+-------------+
|    285      |
+-------------+

```

解释:
问题285的回答率为 1/1，而问题369回答率为 0/1，因此输出285。

解释:
问题285的回答率为 1/1，而问题369回答率为 0/1，因此输出285。

注意: 回答率最高的含义是：同一问题编号中回答数占显示数的比例。



```mysql
SELECT question_id AS survey_log
FROM survey_log
GROUP BY question_id
ORDER BY SUM(CASE 
	WHEN action = 'answer' THEN 1
	ELSE 0
END) / SUM(CASE 
	WHEN action = 'show' THEN 1
	ELSE 0
END) DESC
LIMIT 1;

# 或者count
SELECT question_id AS survey_log
FROM survey_log
GROUP BY question_id
ORDER BY COUNT(action = 'answer'
OR NULL) / COUNT(action = 'show'
OR NULL) DESC
LIMIT 1;

```



## 筛选至少

思路都是group by/子查询

### [1050. 合作过至少三次的演员和导演](https://leetcode-cn.com/problems/actors-and-directors-who-cooperated-at-least-three-times/)   

ActorDirector 表：

```
+-------------+---------+
| Column Name | Type    |
+-------------+---------+
| actor_id    | int     |
| director_id | int     |
| timestamp   | int     |
+-------------+---------+

```

timestamp 是这张表的主键.

写一条SQL查询语句获取合作过至少三次的演员和导演的 id 对 `(actor_id, director_id)`

示例：

ActorDirector 表：

```
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

```

Result 表：

```
+-------------+-------------+
| actor_id    | director_id |
+-------------+-------------+
| 1           | 1           |
+-------------+-------------+

```

对where语句使用子查询

唯一的 id 对是 (1, 1)，他们恰好合作了 3 次。

```mysql
SELECT DISTINCT actor_id AS ACTOR_ID, director_id AS DIRECTOR_ID
FROM ActorDirector a
WHERE (
	SELECT COUNT(1)
	FROM ActorDirector
	WHERE actor_id = a.actor_id
		AND director_id = a.director_id
) >= 3

```

> distinct可以作用于多列

group by

```mysql
SELECT DISTINCT actor_id AS ACTOR_ID, director_id AS DIRECTOR_ID
FROM ActorDirector a
GROUP BY a.actor_id, a.director_id
HAVING COUNT(1) >= 3

```



### [570. 至少有5名直接下属的经理](https://leetcode-cn.com/problems/managers-with-at-least-5-direct-reports/)

Employee 表包含所有员工和他们的经理。每个员工都有一个 Id，并且还有一列是经理的 Id。

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

给定 Employee 表，请编写一个SQL查询来查找至少有5名直接下属的经理。对于上表，您的SQL查询应该返回：

```
+-------+
| Name  |
+-------+
| John  |
+-------+

```



对where语句使用子查询

```mysql
SELECT DISTINCT Name
FROM Employee e
WHERE (
	SELECT COUNT(1)
	FROM Employee
	WHERE ManagerId = e.id
) >= 5

```



group by

```mysql
SELECT Name
FROM Employee
WHERE Id IN (
	SELECT ManagerId
	FROM Employee e
	GROUP BY e.ManagerId
	HAVING COUNT(1) >= 5
)

```



## CASE WHEN 

case when() then else end

可以用在任何需要的地方

### [610. 判断三角形](https://leetcode-cn.com/problems/triangle-judgement/)

```mysql
SELECT x, y, z
	, CASE 
		WHEN (x + y > z
		AND x + z > y
		AND z + y > x) THEN 'Yes'
		ELSE 'No'
	END AS triangle
FROM triangle

```



### [627. 交换工资](https://leetcode-cn.com/problems/swap-salary/) 

给定一个 salary 表，如下所示，有 m = 男性 和 f = 女性 的值。交换所有的 f 和 m 值（例如，将所有 f 值更改为 m，反之亦然）。要求只使用一个更新（Update）语句，并且没有中间的临时表。

注意，您必只能写一个 Update 语句，请不要编写任何 Select 语句。

例如：

| id   | name | sex  | salary |
| ---- | ---- | ---- | ------ |
| 1    | A    | m    | 2500   |
| 2    | B    | f    | 1500   |
| 3    | C    | m    | 5500   |
| 4    | D    | f    | 500    |

运行你所编写的更新语句之后，将会得到以下表:

| id   | name | sex  | salary |
| ---- | ---- | ---- | ------ |
| 1    | A    | f    | 2500   |
| 2    | B    | m    | 1500   |
| 3    | C    | f    | 5500   |
| 4    | D    | m    | 500    |

```mysql
UPDATE salary
SET
    sex = CASE sex
        WHEN 'm' THEN 'f'
        ELSE 'm'
    END;


```



### [626. 换座位](https://leetcode-cn.com/problems/exchange-seats/)

小美是一所中学的信息科技老师，她有一张 seat 座位表，平时用来储存学生名字和与他们相对应的座位 id。

其中纵列的 id 是连续递增的

小美想改变相邻俩学生的座位。

你能不能帮她写一个 SQL query 来输出小美想要的结果呢？

示例：

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



```mysql
SELECT (CASE 
            WHEN MOD(id,2) = 1 AND id = (SELECT COUNT(*) FROM seat) THEN id
            WHEN MOD(id,2) = 1 THEN id+1
            ElSE id-1
        END) AS id, student
FROM seat
ORDER BY id;

```







## Limit offset，count

#### [176.第二高的薪水](https://leetcode-cn.com/problems/second-highest-salary) 

limit offset

```mysql
# 函数
SELECT
    IFNULL(
      (SELECT DISTINCT Salary
       FROM Employee
       ORDER BY Salary DESC
        LIMIT 1 OFFSET 1),                       # limit 1,1 
    NULL) AS SecondHighestSalary;


# 子查询 注意distinct 的作用
SELECT
    (SELECT DISTINCT
            Salary
        FROM
            Employee
        ORDER BY Salary DESC
        LIMIT 1 OFFSET 1) AS SecondHighestSalary
;


```

#### [177. 第N高的薪水](https://leetcode-cn.com/problems/nth-highest-salary/)

和第二高的薪水一样的，稍微麻烦一点的是，是在mysql函数中，需要Set M = N-1;

```mysql
CREATE FUNCTION getNthHighestSalary(N INT) RETURNS INT
BEGIN
# 定义M为INT类型
Declare M INT;
# M为N-1，在查询语句直接写(N-1)会报语法错误
Set M = N-1;
  RETURN (
      # Write your MySQL query statement below.
         SELECT
      ( SELECT DISTINCT
            Salary
        FROM
            Employee
        ORDER BY Salary DESC
        LIMIT M,1) 
  );
END


```



## 统计xx率

### [262. 行程和用户](https://leetcode-cn.com/problems/trips-and-users/)

Trips 表中存所有出租车的行程信息。每段行程有唯一键 Id，Client_Id 和 Driver_Id 是 Users 表中 Users_Id 的外键。Status 是枚举类型，枚举成员为 (‘completed’, ‘cancelled_by_driver’, ‘cancelled_by_client’)。

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

Users 表存所有用户。每个用户有唯一键 Users_Id。Banned 表示这个用户是否被禁止，Role 则是一个表示（‘client’, ‘driver’, ‘partner’）的枚举类型。

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

写一段 SQL 语句查出 2013年10月1日 至 2013年10月3日 期间非禁止用户的取消率。基于上表，你的 SQL 语句应返回如下结果，取消率（Cancellation Rate）保留两位小数。

```
+------------+-------------------+
|     Day    | Cancellation Rate |
+------------+-------------------+
| 2013-10-01 |       0.33        |
| 2013-10-02 |       0.00        |
| 2013-10-03 |       0.50        |
+------------+-------------------+

```



```mysql
# 子查询
select t.request_at Day,   (
        round(count(if(status != 'completed', status, null)) / count(status), 2)
    ) as 'Cancellation Rate' 
    
    from Trips t  
where
    t.Request_at >= '2013-10-01'
and
    t.Request_at <= '2013-10-03'
and (select Banned from Users where Users_Id = t.Client_Id) != 'Yes' 
group by Request_at;


# 表连接
select
    t.request_at Day, 
    (
        round(count(if(status != 'completed', status, null)) / count(status), 2)
    ) as 'Cancellation Rate'
from
    Users u inner join Trips t
on
    u.Users_id = t.Client_Id
and
    u.banned != 'Yes'
where
    t.Request_at >= '2013-10-01'
and
    t.Request_at <= '2013-10-03'
group by
    t.Request_at;

```





### [597. 好友申请 I ：总体通过率](https://leetcode-cn.com/problems/friend-requests-i-overall-acceptance-rate/)

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



注意,每张表都可能有重复



```mysql
SELECT round(ifnull((
		SELECT COUNT(DISTINCT concat(requester_id, '-', accepter_id))
		FROM request_accepted
	) / (
		SELECT COUNT(DISTINCT concat(sender_id, '-', send_to_id))
		FROM friend_request
	), 0), 2) AS accept_rate

```





## 子查询与表连接

这两个往往可以转换

### [607. 销售员](https://leetcode-cn.com/problems/sales-person/)

描述

给定 3 个表： salesperson， company， orders。
输出所有表 salesperson 中，没有向公司 ‘RED’ 销售任何东西的销售员。

解释输入

表： salesperson

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

表 salesperson 存储了所有销售员的信息。每个销售员都有一个销售员编号 sales_id 和他的名字 name 。

表： company

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

表 company 存储了所有公司的信息。每个公司都有一个公司编号 com_id 和它的名字 name 。

表： orders

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

表 orders 存储了所有的销售数据，包括销售员编号 sales_id 和公司编号 com_id 。

输出

```
+------+
| name | 
+------+
| Amy  | 
| Mark | 
| Alex |
+------+

```

解释

根据表 orders 中的订单 ‘3’ 和 ‘4’ ，容易看出只有 ‘John’ 和 ‘Pam’ 两个销售员曾经向公司 ‘RED’ 销售过。

所以我们需要输出表 salesperson 中所有其他人的名字。

表连接

```mysql
SELECT name
FROM salesperson
WHERE sales_id NOT IN (
	SELECT DISTINCT sales_id
	FROM (
		SELECT name, sales_id
		FROM orders o
			LEFT JOIN company c ON o.com_id = c.com_id
	) t
	WHERE t.name = 'RED'
);

```

子查询

```mysql
SELECT name
FROM salesperson
WHERE sales_id NOT IN (
	SELECT DISTINCT sales_id
	FROM orders
	WHERE com_id = (
		SELECT com_id
		FROM company
		WHERE name = 'RED'
	)
);

```





### [184. 部门工资最高的员工](https://leetcode-cn.com/problems/department-highest-salary/)

Employee 表包含所有员工信息，每个员工有其对应的 Id, salary 和 department Id。

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

Department 表包含公司所有部门的信息。

```
+----+----------+
| Id | Name     |
+----+----------+
| 1  | IT       |
| 2  | Sales    |
+----+----------+

```

编写一个 SQL 查询，找出每个部门工资最高的员工。例如，根据上述给定的表格，Max 在 IT 部门有最高工资，Henry 在 Sales 部门有最高工资。

```
+------------+----------+--------+
| Department | Employee | Salary |
+------------+----------+--------+
| IT         | Max      | 90000  |
| Sales      | Henry    | 80000  |
+------------+----------+--------+

```



```mysql
SELECT d.Name AS Department, e.Name AS Employee, e.Salary AS Salary
FROM Employee e
	INNER JOIN Department d on e.DepartmentId = d.Id
WHERE (
	SELECT COUNT( distinct Salary)
	FROM Employee
	WHERE Salary > e.Salary
		AND DepartmentId = e.DepartmentId
) < 1
ORDER BY Department, Salary DESC


# 另外的方式
select 
    d.Name as Department,
    e.Name as Employee,
    e.Salary 
from 
    Employee e,Department d 
where
    e.DepartmentId=d.id 
    and
    (e.Salary,e.DepartmentId) in (select max(Salary),DepartmentId from Employee group by DepartmentId);

```





## 用户变量

可以增加一个rank列。

中位数其实就是`(totalcount + 1) / 2`，`(totalcount + 2) / 2`这个是通用的。

### [569. 员工薪水中位数](https://leetcode-cn.com/problems/median-employee-salary/)

Employee 表包含所有员工。Employee 表有三列：员工Id，公司名和薪水。

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



```mysql
SELECT Ranking.Id, Ranking.Company, Ranking.Salary
FROM (
	SELECT e.Id, e.Salary, e.Company
		,@Rank:= IF(@prev = e.Company,  @Rank + 1,  1) AS rank
		, @prev := e.Company
	FROM Employee e, (
			SELECT @prev := NULL, @Rank := 0
		) temp
	ORDER BY e.Company, e.Salary, e.Id
) Ranking
	INNER JOIN (
		SELECT COUNT(1) AS totalcount, Company
		FROM Employee
		GROUP BY Company
	) Companycount
	ON Companycount.Company = Ranking.Company
WHERE Rank = FLOOR((totalcount + 1) / 2)
	OR Rank = FLOOR((totalcount + 2) / 2)

```