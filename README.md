# PizzaSales_Analysis-Project
This project analyzes sales data from a fictional pizza restaurant to extract actionable business insights. Using SQL(Structured Query Language), it explores customer preferences, peak order times, top-selling pizzas, and overall revenue trends.

### Data Sources 

Order_details : This dataset contain order_id, pizza_id, and quantity in the form of "order_details_csv".

pizza_type : This dataset contain name, category and ingrediants of Pizza in the form of "Pizza_type_csv".

pizza_csv : This dataset contain size and price of pizza.

order_csv : This dataset contain date and time of order.


### Tools 

- Excel - Data Cleaning[Download here](https://microsoft.com)

- SQL Server - Data Analysis


### Data Cleaning/Preparation

1. Data loading and inspection.
2. Handling missing values
3. Data cleaning and formating

### Data Analysis

Include some interesting question with their code.

Q 1. Retrieve the total number of orders placed.

```sql
select count(*) order_id from order_details;
```

Q 2. Calculate the total revenue generated from pizza sales.
```sql
select round(sum(order_details.quantity * pizza_csv.price),2) as total_revenue  from order_details
join pizza_csv
on order_details.pizza_id = pizza_csv.pizza_id;
```

Q 3. Identify the highest-priced pizza.
```sql
select pizza_type.name, pizza_csv.price from pizza_type
join pizza_csv
on pizza_type.pizza_type_id = pizza_csv.pizza_type_id
order by pizza_csv.price desc
limit 1;
```

Q 4.  Identify the most common pizza size ordered.
```sql
select pizza_csv.size, count(order_details.order_id) as common_pizza_size from pizza_csv
join order_details on
pizza_csv.pizza_id = order_details.pizza_id
group by pizza_csv.size
order by common_pizza_size desc;
```

Q 5. List the top 5 most ordered pizza types along with their quantities.
```sql
select  pizza_type.name, sum(order_details.quantity) as order_quantity from pizza_type
join pizza_csv
on pizza_type.pizza_type_id = pizza_csv.pizza_type_id
join order_details
on pizza_csv.pizza_id = order_details.pizza_id
group by pizza_type.name
order by order_quantity desc
limit 5;
```

Q 6. find the total quantity of each pizza category ordered.
```sql
select pizza_type.category, sum( order_details.quantity) as total_quantity from pizza_type
join pizza_csv
on pizza_type.pizza_type_id = pizza_csv.pizza_type_id
join order_details
on pizza_csv.pizza_id = order_details.pizza_id
group by pizza_type.category
order by total_quantity desc;
```

Q 7. Determine the distribution of orders by hour of the day.
```sql
select hour(time) as hour, count(order_id) as order_id from order_csv
group by  hour(time)
order by hour desc;
```

Q 8.  Join relevant tables to find the category-wise distribution of pizzas.
```sql
select pizza_type.category, count(order_details.order_id) as total_sell from pizza_type
join pizza_csv
on pizza_type.pizza_type_id = pizza_csv.pizza_type_id
join order_details
on pizza_csv.pizza_id = order_details.pizza_id
group by pizza_type.category;
 ```

 Q 9. Group the orders by date and calculate the average number of pizzas ordered per day
```sql
SELECT 
    AVG(avg_pizza_per_day) AS avg_pizzas_per_day
FROM (
    SELECT 
        order_csv.date,
        SUM(order_details.quantity) as avg_pizza_per_day
    FROM order_csv
    JOIN order_details  ON order_csv.order_id = order_details.order_id
    GROUP BY order_csv.date
) AS daily_counts;
```

Q 10. Determine the top 3 most ordered pizza types based on revenue.
```sql
 select pizza_type.name, round(sum(order_details.quantity * pizza_csv.price),2) as total_revenue  from order_details
join pizza_csv
on order_details.pizza_id = pizza_csv.pizza_id
join pizza_type
on pizza_csv.pizza_type_id = pizza_type.pizza_type_id
group by pizza_type.name
order by total_revenue desc
limit 3;
 ```

Q 11. Calculate the percentage contribution of each pizza type to total revenue
```sql
select pizza_type.category, round(sum(order_details.quantity * pizza_csv.price) /
( select sum(order_details.quantity * pizza_csv.price)  from order_details
join pizza_csv
on order_details.pizza_id = pizza_csv.pizza_id) * 100, 2) as revenue

from pizza_type join pizza_csv
on pizza_type.pizza_type_id = pizza_csv.pizza_type_id
join order_details on
order_details.pizza_id = pizza_csv.pizza_id
group by pizza_type.category
order by revenue desc;
 ```

Q 12. Determine the top 3 most ordered pizza types based on revenue for each pizza category.
```sql
 select name, revenue from
 (select category, name, revenue,
 rank() over (partition by category order by revenue desc)
 as rn
 from
 (select pizza_type.category, pizza_type.name,
 round(sum(order_details.quantity * pizza_csv.price), 2) as revenue
 from pizza_type join pizza_csv on
 pizza_type.pizza_type_id = pizza_csv.pizza_type_id
 join order_details on order_details.pizza_id =
 pizza_csv.pizza_id
 group by pizza_type.category, pizza_type.name) as a)
 as b
 where rn<=3order_csv
 limit 3;
 ```

Q 13. Analyze the cumulative revenue generated over time
```sql
select order_csv.date, round(sum(order_details.quantity * pizza_csv.price),2) as revenue from order_csv
join order_details on 
order_csv.order_id = order_details.order_id
join pizza_csv on 
order_details.pizza_id = pizza_csv.pizza_id
group by order_csv.date;
 ```

