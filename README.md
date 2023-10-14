# SQL_Interview_Questions
In this post we are going to discuss the SQL interview questions asked to me by a Startup for a data science role.

The format would be:
## Problem Statement 1
- Approach
- Solution

  ## Problem Statement 2
- Approach
- Solution


### Problem Statement 1:

Table: customer_orders
```
+-------------+---------+
| Column Name | Type    |
+-------------+---------+
| order_id    | int     |
| customer_id | int     |
| order_date  | date    |
| order_amount| int     |
+-------------+---------+
```

- `order_id`: An integer representing a unique identifier for each order.
- `customer_id`: An integer representing the unique identifier of the customer who placed the order.
- `order_date`: A date field indicating the date when the order was placed.
- `order_amount`: An integer representing the amount of the order.

#### Question: 
Write a SQL code that should contain the order_date, the count of new customers on that date (customers who placed their first order on that date), and the count of repeated customers (customers who placed more than one order on that date)
``````
Example:
Input: 
customer_orders table:
+----------+-------------+------------+--------------+
| order_id | customer_id | order_date | order_amount |
+----------+-------------+------------+--------------+
|        1 |         100 | 2022-01-01 |         2000 |
|        2 |         200 | 2022-01-01 |         2500 |
|        3 |         300 | 2022-01-01 |         2100 |
|        4 |         100 | 2022-01-02 |         2000 |
|        5 |         400 | 2022-01-02 |         2200 |
|        6 |         500 | 2022-01-02 |         2700 |
|        7 |         100 | 2022-01-03 |         3000 |
|        8 |         400 | 2022-01-03 |         1000 |
|        9 |         600 | 2022-01-03 |         3000 |
+----------+-------------+------------+--------------+
Output: 
+------------+--------------+-------------------+
| order_date | new_customer | repeated_customer |
+------------+--------------+-------------------+
| 2022-01-01 |            3 |                 0 |
| 2022-01-02 |            2 |                 1 |
| 2022-01-03 |            1 |                 2 |
+------------+--------------+-------------------+
Explanation: 
For '2022-01-01' all of them are new customers i.e, new_customer = 3 and repeated_customer = 0.
Similarly for '2022-01-02' we have 400 and 500 as new_cutomer and 100 as repeated_customer.
``````
#### Create Table Schema
```
-- Create table Query
create table customer_orders (
order_id integer,
customer_id integer,
order_date date,
order_amount integer
);

-- Insert table Query
insert into customer_orders values(1,100,cast('2022-01-01' as date),2000),(2,200,cast('2022-01-01' as date),2500),(3,300,cast('2022-01-01' as date),2100)
,(4,100,cast('2022-01-02' as date),2000),(5,400,cast('2022-01-02' as date),2200),(6,500,cast('2022-01-02' as date),2700)
,(7,100,cast('2022-01-03' as date),3000),(8,400,cast('2022-01-03' as date),1000),(9,600,cast('2022-01-03' as date),3000)

**NOTE**: To run the code with me copy and paste the Create & Insert query to this website.
``````

#### Approach

Section 1: Here we are creating a rank based on Customer_id and ordering by order_date, so that we can get the customer_id which is repeated more than 1 time.

Section 2: Here we are segregating the customers with rank 1 as new_customer and Rank greater than 1 as repeated_customer & grouping and ordering it on order_date

``````
-- Solution
with cte1 as(
select *, 
row_number() over(partition by customer_id order by order_date) as rn
from customer_orders)
select order_date,
sum(case when rn = 1 then 1 else 0 end) as new_customer,
sum(case when rn>1 then 1 else 0 end) as repeated_customer
from cte1
group by order_date
order by order_date;
``````


### Problem Statement 2:

``````
Table: fruits_table
+-------------+---------+
| Column Name | Type    |
+-------------+---------+
| sales_date  | date    |
| fruits      | Varchar |
| sold_num    | int     |
+-------------+---------+
``````

- `sales_date` (Type: Date): Stores the date when sales transactions occurred.
- `fruits` (Type: Varchar): Contains the names or types of fruits sold.
- `sold_num` (Type: Int): Represents the quantity or number of fruits sold in each transaction.

#### Question: 
Given a table of sales transactions with information about the date of the sale, the type of fruit (e.g., apples or oranges), and the quantity sold, how can we calculate the daily difference in the number of apples/oranges sold?
``````
Example:
Input: 
fruits_table table:
+------------+---------+----------+
| sales_date | fruits  | sold_num |
+------------+---------+----------+
| 2020-05-01 | apples  |       10 |
| 2020-05-01 | oranges |        8 |
| 2020-05-02 | apples  |       15 |
| 2020-05-02 | oranges |       15 |
| 2020-05-03 | apples  |       20 |
| 2020-05-03 | oranges |        0 |
| 2020-05-04 | apples  |       15 |
| 2020-05-04 | oranges |       16 |
+------------+---------+----------+
Output: 
+------------+------+
| sales_date | diff |
+------------+------+
| 2020-05-01 |    2 |
| 2020-05-02 |    0 |
| 2020-05-03 |   20 |
| 2020-05-04 |   -1 |
+------------+------+
Explanation: 
For '2020-05-01' apples = 10 and oranges are 8 so 10-8 = 2
similary '2020-05-02' apples = 15 and oranges are 15 so 15-15=0
``````

#### Create Table Schema
``````
-- Create table Query
CREATE TABLE fruits_table(
sales_date DATE,
fruits VARCHAR(25),
sold_num SMALLINT
);

-- Insert table Query
INSERT INTO fruits_table(sales_date, fruits, sold_num) VALUES
('2020-05-01', 'apples', 10),
('2020-05-01', 'oranges', 8),
('2020-05-02', 'apples', 15),
('2020-05-02', 'oranges', 15),
('2020-05-03', 'apples', 20),
('2020-05-03', 'oranges', 0),
('2020-05-04', 'apples', 15),
('2020-05-04', 'oranges', 16);
NOTE: To run the code with me copy and paste the Create & Insert query to this website.
``````

#### Solution
Section 1: Here we have only filtered for apples
Section 2: Here we have only filtered for oranges
Section 3: Merging the Section 1 and Section 2
Section 4: Subtract the apple_sold and orange_sold from merged cte

``````
-- Solution
with apple as(
select * from fruits_table where fruits = 'apples'),

orange as(
select * from fruits_table where fruits = 'oranges'),

merged as(
select a.sales_date, a.fruits as apple_fruit, o.fruits as orange_fruit,a.sold_num as apple_sold,
o.sold_num as orange_sold from apple a left join orange o on a.sales_date = o.sales_date)

select sales_date, (apple_sold - orange_sold) as diff
from merged;
``````