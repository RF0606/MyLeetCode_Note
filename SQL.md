# Question@Answers

## 连表查询：

inner join：2表值都存在

outer join：附表中值可能存在null的情况。

from子句中on条件主要用来连接表，其他不属于连接表的条件可以使用where子句来指定；

总结：

①A inner join B：取交集

②A left join B：取A全部，B没有对应的值，则为null

③A right join B：取B全部，A没有对应的值，则为null

④A full outer join B：取并集，彼此没有对应的值为null

上述4种的对应条件，在on后填写。

## 笛卡尔积：

假设集合A={a, b}，集合B={0, 1, 2}，则两个集合的笛卡尔积为{(a, 0), (a, 1), (a, 2), (b, 0), (b, 1), (b, 2)}。

假设集合A={a, b, c}, 则A和A的笛卡尔积为{(a,a), (a,b), (a,c), (b,a), (b,b), (b,c), (c,a), (c,b), (c,c)}

## 顺序：

on -> where -> group by -> having -> order by -> limit

## in: 

跟在where后面，where ..... in(xxx)，找到所有符合括号内的东西的 

## Distinct：

关键词 distinct用于返回唯一不同的值。

## limit & offset：

limit 1, 3 代表跳过第一个从2个开始取，然后取后边3个值

当 limit和offset组合使用的时候，limit后面只能有一个参数，表示要取的的数量,offset表示要跳过的数量 。

limit 3 offset 1 表示跳过1条数据,从第2条数据开始取，取3条数据，也就是取2,3,4三条数据

## Having:

在 SQL 中增加 HAVING 子句原因是，WHERE 关键字无法与合计函数一起使用。

## order by：

order by columnName，按照字段columnName来排序，默认是升序

ASC：升序

DESC：降序

![9ec0fddb2a50d3df105c8a32c558708](C:\Users\10602\AppData\Local\Temp\WeChat Files\9ec0fddb2a50d3df105c8a32c558708.png)

```sql
最下左图：
select ... from 表1 as a 
full outer join 表b as b
on a.列名 = b.列名

最下右图：
select ... from 表1 as a 
full outer join 表b as b
on a.列名 = b.列名
where a.列名 is null or b.列名 is null

```

## group by：

语句通常配合聚合函数(count, max,avg, min, sum)，根据一个或多个列的结果进行分组，重点是可以有多个元素

![29aa14236b0378548fa7ea5032f0920](C:\Users\10602\AppData\Local\Temp\WeChat Files\29aa14236b0378548fa7ea5032f0920.png)

## ifnull(x1, x2)：

如果x1为null，返回x2，否则返回x1

## When-then：

![2db52607b014c624d9fbb517520064a](C:\Users\10602\AppData\Local\Temp\WeChat Files\2db52607b014c624d9fbb517520064a.png)

```sql
其中第二种为拿condition中的与compare_value比较
```

还有就是不用else的话要when-end

## concat:

用法：

```sql
concat('xxx', 'bbb')
==> xxxbbb
```

## group_concat:

```sql
# product 为要连接的字段，其中product里有aa, bb, cc三个东西
group_concat(
    distinct product,
    order by product desc/asc
    separator ','
)

==> aa,bb,cc
```



#### [175. Combine Two Tables](https://leetcode.cn/problems/combine-two-tables/) Easy

```sql
Select firstName, lastName, city, state 
from Person left join Address
on Person.personId = Address.personId
```

#### [176. Second Highest Salary](https://leetcode.cn/problems/second-highest-salary/) Mid

```sql
select 
    (select DISTINCT salary 
    from Employee 
    order by salary DESC
    limit 1,1
    ) AS SecondHighestSalary
    
```

#### [177. Nth Highest Salary](https://leetcode.cn/problems/nth-highest-salary/)

取第N个高的工资，那就得跳过N-1个人，就得提前设置一下N = N-1;

```sql
CREATE FUNCTION getNthHighestSalary(N INT) RETURNS INT
BEGIN
    SET N = N-1;
  RETURN (
      # Write your MySQL query statement below.
      select DISTINCT salary 
      from employee
      order by salary DESC
      limit N, 1
  );
END
```

#### [178. Rank Scores](https://leetcode.cn/problems/rank-scores/)

```sql
select a.score as score,
    (select count(distinct b.score) from Scores as b where b.score >= a.score) as 'rank'
from Scores as a 
order by a.Score DESC

/*
其中：
select b.score from Scores as b where b.score >= a.score 是吧所有大于a.score的score都拉出来，然后我们只是为了得到个数所以要加count
并且不需要计算重复的个数，所以加 distinct 
那么，(select count(distinct b.score) from Scores as b where b.score >= a.score) as 'rank'的意思就是把所有大于a.score，按照不重复的办法
count一下，那么就出来了排名了

```

#### [180. Consecutive Numbers](https://leetcode.cn/problems/consecutive-numbers/)

```sql
SELECT DISTINCT l1.Num AS ConsecutiveNums
FROM Logs l1, Logs l2, Logs l3
WHERE
    l1.Id = l2.Id - 1
    AND l2.Id = l3.Id - 1
    AND l1.Num = l2.Num
    AND l2.Num = l3.Num
    
# 就是分别整出来三个logs表，然后连续3个以上数那就意思是num相同的同时id要连续的
```

#### [181. Employees Earning More Than Their Managers](https://leetcode.cn/problems/employees-earning-more-than-their-managers/)

```sql
两个解法：
select e1.name as Employee
from Employee as e1 
where e1.salary > (select salary from Employee e2 where e2.id = e1.managerId)

或者：
SELECT
     a.NAME AS Employee
FROM Employee AS a JOIN Employee AS b
     ON a.ManagerId = b.Id
     AND a.Salary > b.Salary
```

#### [182. Duplicate Emails](https://leetcode.cn/problems/duplicate-emails/)

```sql
select email 
from Person 
group by email
having count(email) > 1

# having和group by一般一起配合使用，目的是筛选出来group by以后符合的条件
```

#### [183. Customers Who Never Order](https://leetcode.cn/problems/customers-who-never-order/)

```sql
select c.name as Customers
from Customers as c left join Orders as o 
on c.id = o.customerId
where o.id is null

#不select不代表就不会有这个东西，所以o.id一直是会存在的，找是null的就行
```

#### [184. Department Highest Salary](https://leetcode.cn/problems/department-highest-salary/)

```sql
select d.name as Department, e.name as Employee, e.salary as Salary
from Employee as e join Department as d
on e.departmentId = d.id
where e.salary >= (select max(c.salary)
                  from Employee c
                  where c.departmentId = e.departmentId)
                  
# 另一种写法
SELECT
    d.name AS 'Department',
    e.name AS 'Employee',
    Salary
FROM
    Employee e
        JOIN
    Department d ON e.DepartmentId = d.Id
WHERE
    (e.DepartmentId , Salary) IN
    (   SELECT
            DepartmentId, MAX(Salary)
        FROM
            Employee
        GROUP BY DepartmentId
	)

# where下面的select选的是每个department的最大工资
```

#### [185. Department Top Three Salaries](https://leetcode.cn/problems/department-top-three-salaries/)

```sql
select d.name as Department, e.name as Employee, e.salary as Salary
from Employee as e join Department as d
on e.departmentId = d.id
where 3 > (select count(distinct c.salary) 
                    from Employee c 
                    where c.salary > e.salary and c.departmentId = e.departmentId
                    )

# where括号里 相当于依次判断e1表每一个薪资，找到大于此薪资的数量 小于3 的 ， 比如e1表第一名 ，大于他的为0，符合条件；第二名，大于他的为1，符合条件；第三名，大于他的为2，符合条件； 第四名，大于他的为3，不符合条件，这样就找到了前三；
```

#### [196. Delete Duplicate Emails](https://leetcode.cn/problems/delete-duplicate-emails/)

```sql
# 官方解法
DELETE p1 FROM Person p1,
    Person p2
WHERE
    p1.Email = p2.Email AND p1.Id > p2.Id

# 不产生笛卡尔积的解法
delete 
from Person p1
where id not in (select id from (select min(id) as id 
                 from Person 
                 group by email) as t)
#其中 select min(id) as id 是因为不as id的话查出来的名字叫做min(id)
```

#### [197. Rising Temperature](https://leetcode.cn/problems/rising-temperature/)

```sql
#第一种解法，用笛卡尔积，然后找 b 比 a 日期大1天并且温度还高的那天的id返回, DATEDIFF是有正反区分的
select 
    b.id
from 
    weather a 
inner join 
    weather b 

where DATEDIFF (b.recordDate, a.recordDate) = 1
    and b.Temperature > a.Temperature
    
    
# 也是笛卡尔积
select a.id
from Weather as a cross join Weather as b 
     on datediff(a.recordDate, b.recordDate) = 1
where a.Temperature > b.Temperature;
```

#### [262. Trips and Users](https://leetcode.cn/problems/trips-and-users/)

```sql
select request_at as `Day`, 
       round(sum(if(t.status != 'completed', 1, 0))/count(*),2) as `Cancellation Rate`
from Trips t
inner join Users u 
on t.client_id = u.users_id 
inner join Users u2
on t.driver_id = u2.users_id
where request_at between "2013-10-01" and "2013-10-03"
and u.banned = 'No'
and u2.banned = 'No'
group by request_at
```

#### [511. Game Play Analysis I](https://leetcode.cn/problems/game-play-analysis-i/)

```sql
select 
    player_id,
    min(event_date) as first_login
from Activity
group by player_id
```

#### [550. Game Play Analysis IV](https://leetcode.cn/problems/game-play-analysis-iv/)

```sql
# 先查所有玩家第一次login的表，然后左关联，筛选次日存在的记录的比例
select round(avg(a.event_date is not null), 2) fraction
from 
    (select player_id, min(event_date) as login
    from activity
    group by player_id) p 
left join activity a 
on p.player_id=a.player_id and datediff(a.event_date, p.login)=1
```

#### [570. Managers with at Least 5 Direct Reports](https://leetcode.cn/problems/managers-with-at-least-5-direct-reports/)

```sql
#先把有>=5名下属的managerId搞出来
select e.name 
from employee as e
join
    (select managerId
     from employee
     group by managerId
     having count(managerId) >= 5) as t2
on e.id = t2.managerId
```

#### [584. Find Customer Referee](https://leetcode.cn/problems/find-customer-referee/)

```sql
# 还得单独考虑 null
select name 
from customer as c
where c.referee_id != 2 or c.referee_id is null
```

#### [586. Customer Placing the Largest Number of Orders](https://leetcode.cn/problems/customer-placing-the-largest-number-of-orders/)

```sql
# group by之后count(*)为个数
select customer_number 
from Orders 
group by customer_number
order by count(*) desc
limit 0, 1
```

#### [595. Big Countries](https://leetcode.cn/problems/big-countries/)

```sql
select name, population, area 
from World 
where area >= 3000000 or population >= 25000000
```

#### [596. 超过5名学生的课](https://leetcode.cn/problems/classes-more-than-5-students/)

```sql
select
    class
from 
    (select class, count(student) as num
    from Courses
    group by class) as t
where num >= 5
```

#### [601. 体育馆的人流量](https://leetcode.cn/problems/human-traffic-of-stadium/)

```sql
#查询人流量超过 100 的记录，然后t1 t2 和 t3分别有可能是高峰期的第1， 2， 3天

select distinct t1.*
from stadium t1, stadium t2, stadium t3
where t1.people >= 100 and t2.people >= 100 and t3.people >= 100
and
(
	  (t1.id - t2.id = -1 and t1.id - t3.id = -2 and t2.id - t3.id = -1)  -- t1, t2, t3
    or
    (t2.id - t1.id = -1 and t2.id - t3.id = -2 and t1.id - t3.id = -1) -- t2, t1, t3
    or
    (t3.id - t2.id = -1 and t2.id - t1.id = -1 and t3.id - t1.id = -2) -- t3, t2, t1
)
order by t1.id
```

#### [603. Consecutive Available Seats](https://leetcode.cn/problems/consecutive-available-seats/)

```sql
select distinct c1.seat_id 
from cinema as c1 join cinema as c2
    on abs(c2.seat_id - c1.seat_id) = 1
    and (c1.free = 1 and c2.free = 1)
    order by seat_id
```

#### [607. Sales Person](https://leetcode.cn/problems/sales-person/)

```sql
#先找谁跟red公司做过生意，然后刨除就行了
select name 
from salesPerson as s
where s.sales_id not in (select o.sales_id
                        from orders as o
                            left join company as c
                            on o.com_id = c.com_id
                        where c.name = 'red')

```

#### [608. 树节点](https://leetcode.cn/problems/tree-node/)

```sql
# 用union把三个都接起来
#root没有父节点
SELECT
    id, 'Root' AS Type
FROM
    tree
WHERE
    p_id IS NULL

UNION

#leaf有父节点但没子节点，意味着自己的id不会出现在p_id里，同时拥有父节点(p_id)
SELECT
    id, 'Leaf' AS Type
FROM
    tree
WHERE
    id NOT IN (SELECT DISTINCT
            p_id
        FROM
            tree
        WHERE
            p_id IS NOT NULL)
        AND p_id IS NOT NULL

UNION

#inner有父节点和子节点，意味着自己的id会出现在p_id里，同时拥有父节点(p_id)
SELECT
    id, 'Inner' AS Type
FROM
    tree
WHERE
    id IN (SELECT DISTINCT
            p_id
        FROM
            tree
        WHERE
            p_id IS NOT NULL)
        AND p_id IS NOT NULL
ORDER BY id;

```

#### [626. 换座位](https://leetcode.cn/problems/exchange-seats/)

```sql
SELECT
    (CASE
        WHEN MOD(id, 2) != 0 AND counts != id THEN id + 1
        WHEN MOD(id, 2) != 0 AND counts = id THEN id
        ELSE id - 1
    END) AS id,
    student
FROM
    seat,
    (SELECT
        COUNT(*) AS counts
    FROM
        seat) AS seat_counts
ORDER BY id ASC;
```

#### [627. 变更性别](https://leetcode.cn/problems/swap-salary/)

```sql
update Salary
set sex = if(sex = 'f', 'm','f')
```

#### [1045. 买下所有产品的客户](https://leetcode.cn/problems/customers-who-bought-all-products/)

```sql
# 只要group by之后的客户id里的product_key数量等于来自Product表的所有产品的数量就ok
select customer_id 
from Customer 
group by customer_id  
having count(distinct product_key) in 
    (select count(distinct product_key)
    from Product)
```

#### [1050. 合作过至少三次的演员和导演](https://leetcode.cn/problems/actors-and-directors-who-cooperated-at-least-three-times/)

```sql
# 用having看，group by那两个的话，count(timestamp)能看出来timestamp有几个，>= 三个就行了
select actor_id, director_id
from ActorDirector 
group by actor_id, director_id
having count(timestamp) >= 3
```

#### [1068. 产品销售分析 I](https://leetcode.cn/problems/product-sales-analysis-i/)

```sql
select product_name, year, price 
from Sales as s 
left join Product as p
on s.product_id = p.product_id
```

#### [1077. 项目员工 III](https://leetcode.cn/problems/project-employees-iii/)

```sql
#先把每个project_id和对应的工龄最大的employee_id 查出来
select 
    p.project_id, 
    e.employee_id
from 
    project as p
join employee as e
on p.employee_id = e.employee_id
where (p.project_id, e.experience_years) in
## 子查询内容
    (select
        p.project_id, 
        max(experience_years)
    from project as p
    join employee as e
    on p.employee_id = e.employee_id
    group by p.project_id
    )

```

#### [1084. 销售分析III](https://leetcode.cn/problems/sales-analysis-iii/)

```sql
# 一定是count里加入or null，因为只有 null不会被计数
select  s.product_id,  p.product_name
from Sales as s
left join Product as p
on s.product_id = p.product_id
group by s.product_id
having count(sale_date between '2019-01-01' and '2019-03-31' or null) = count(sale_date)
```

#### [1141. 查询近30天活跃用户数](https://leetcode.cn/problems/user-activity-for-the-past-30-days-i/)

```sql
#包含当天所以 >= 0
select activity_date as day, count(distinct user_id) as active_users
from activity as a
group by activity_date
having 
    datediff('2019-07-27', a.activity_date) < 30 and 
    datediff('2019-07-27', a.activity_date) >= 0
```

#### [1148. 文章浏览 I](https://leetcode.cn/problems/article-views-i/)

```sql
select distinct viewer_id as id 
from views
where author_id = viewer_id
order by viewer_id 
```

#### [1158. 市场分析 I](https://leetcode.cn/problems/market-analysis-i/)

```sql
select user_id as buyer_id, join_date, ifnull(b.num, 0) as orders_in_2019
from Users
left join(
    select buyer_id, count(order_id) as num
    from orders
    where (order_date between '2019-01-01' and '2019-12-31')
    group by buyer_id
) as b
on Users.user_id = b.buyer_id
```

#### [1211. 查询结果的质量和占比](https://leetcode.cn/problems/queries-quality-and-percentage/)

```sql
select query_name, 
	   round(avg(rating/position), 2) as quality, 
	   round(sum(if(rating < 3, 1, 0))*100/count(position), 2) as poor_query_percentage
from queries
group by query_name
```

#### [1378. 使用唯一标识码替换员工ID](https://leetcode.cn/problems/replace-employee-id-with-the-unique-identifier/)

```sql
select unique_id, name 
from EmployeeUNI as eu
right join Employees as e
on e.id = eu.id
```

#### [1484. 按日期分组销售产品](https://leetcode.cn/problems/group-sold-products-by-the-date/)

```sql
#仔细看 group_concat 的用法

select 
    sell_date, 
    count(distinct product) as num_sold, 
    group_concat(
        distinct product
        order by product
        separator ','
    ) as products
from Activities
group by sell_date
order by sell_date
```

#### [1527. 患某种疾病的患者](https://leetcode.cn/problems/patients-with-a-condition/)

```SQL
#用like 就行

select patient_id, patient_name, conditions
from Patients
where conditions like 'DIAB1%' or conditions like '% DIAB1%'
```

#### [1581. 进店却未进行过交易的顾客](https://leetcode.cn/problems/customer-who-visited-but-did-not-make-any-transactions/)

```sql
select distinct customer_id, count(visit_id) as count_no_trans
from visits
where visit_id not in 
    (select distinct visit_id 
    from transactions as t
    )
group by customer_id

#或者用left join
select distinct customer_id, count(customer_id) as count_no_trans
from visits as v
left join transactions as t
on v.visit_id = t.visit_id
where transaction_id is null
group by customer_id
```

#### [1661. 每台机器的进程平均运行时间](https://leetcode.cn/problems/average-time-of-process-per-machine/)

```sql
# 因为算一次时间要进出2个数，所以avg比实际的多除了1倍，要乘回去

select 
    machine_id, 
    round(avg(if(activity_type = 'start', -timestamp, timestamp))*2, 3) as processing_time
from activity
group by machine_id
```

#### [1667. 修复表中的名字](https://leetcode.cn/problems/fix-names-in-a-table/)

```sql
# concat可以拼接字符串，upper和lower是大小写函数，left和right是从左从右取个数
select user_id, 
       CONCAT(UPPER(left(name, 1)), LOWER(RIGHT(name, length(name) - 1))) as name
from Users
order by user_id
```

#### [1693. 每天的领导和合伙人](https://leetcode.cn/problems/daily-leads-and-partners/)

```sql
# 一定记得group by可以有多个元素的

select 
    date_id,  
    make_name,
    count(distinct lead_id) as unique_leads,
    count(distinct partner_id) as unique_partners
from dailysales
group by date_id, make_name

```

#### [1699. 两人之间的通话次数](https://leetcode.cn/problems/number-of-calls-between-two-persons/)

```sql
#先把from_id，to_id 转换为 person_1 person_2
select 
    if(from_id > to_id, to_id, from_id) as person1,
    if(from_id < to_id, to_id, from_id) as person2,
    count(duration) as call_count,
    sum(duration) as total_duration
from calls
group by person1, person2

```

#### [1757. 可回收且低脂的产品](https://leetcode.cn/problems/recyclable-and-low-fat-products/)

```sql
select product_id 
from products
where low_fats = 'Y' and recyclable = 'Y'
```

#### [1795. 每个产品在不同商店的价格](https://leetcode.cn/problems/rearrange-products-table/) 行转列

```sql
SELECT product_id, 'store1' as store, store1 as price FROM products WHERE store1 IS NOT NULL
UNION
SELECT product_id, 'store2' as store, store2 as price FROM products WHERE store2 IS NOT NULL
UNION
SELECT product_id, 'store3' as store, store3 as price FROM products WHERE store3 IS NOT NULL;
group by product_id

#列转行
SELECT 
  product_id,
  SUM(IF(store = 'store1', price, NULL)) as 'store1',
  SUM(IF(store = 'store2', price, NULL)) as 'store2',
  SUM(IF(store = 'store3', price, NULL)) as 'store3' 
FROM
  Products1 
GROUP BY product_id ;
```

#### [1873. 计算特殊奖金](https://leetcode.cn/problems/calculate-special-bonus/)

```sql
select
    employee_id,
    if(
        employee_id%2=1 and name not like 'M%',
        salary,
        0
    ) as bonus
from employees
order by employee_id;
```

#### 
