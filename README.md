# Pizzahut-sales
A collection of SQL queries and scripts covering data retrieval, joins, aggregations, subqueries, window functions, and performance optimization using real-world datasets.
Create schema pizzahut;
use pizzahut;
create table orders(
order_id int not null,
order_date date not null,
order_time time not null,
primary key(order_id) );

create table order_details(
order_details_id int not null,
order_id int not null,
pizza_id varchar(100) not null,
Quantity int not null,
primary key (order_details_id) );


select * from order_details;

select count(order_id) from orders;
-- 21350 ordes


-- Calculate the total revenue generated from pizza sales.
SELECT 
    round(sum((p.price * o.quantity)),2) AS total_revenue
FROM
    order_details AS o
        JOIN
    pizzas AS p ON o.pizza_id = p.pizza_id;

-- Identify the highest-priced pizza.
select max(price) as highest_price_pizza from pizzas;

-- Identify the most common pizza size ordered.
SELECT 
    p.size, COUNT(od.order_details_id) AS order_count
FROM
    order_details od
        JOIN
    pizzas p ON od.pizza_id = p.pizza_id
GROUP BY (p.size)
ORDER BY order_count DESC;

-- List the top 5 most ordered pizza types along with their quantities.

SELECT 
    pt.name, SUM(od.quantity) AS total_order
FROM
    pizza_types pt
        JOIN
    pizzas p ON pt.pizza_type_id = p.pizza_type_id
        JOIN
    order_details od ON od.pizza_id = p.pizza_id
GROUP BY (pt.name)
ORDER BY (total_order) DESC
LIMIT 5;

-- Join the necessary tables to find the total quantity of each pizza category ordered.

SELECT 
    pt.category, SUM(od.quantity) AS total_Quantity
FROM
    order_details od
        JOIN
    pizzas p ON od.pizza_id = p.pizza_id
        JOIN
    pizza_types pt ON p.pizza_type_id = pt.pizza_type_id
GROUP BY pt.category
ORDER BY total_quantity DESC;



-- Determine the distribution of orders by hour of the day.

SELECT 
    *
FROM
    orders;	

SELECT 
    HOUR(order_time) AS hour_wise_time,
    COUNT(order_id) AS order_per_hour
FROM
    orders
GROUP BY hour_wise_time;

-- Join relevant tables to find the category-wise distribution of pizzas.

SELECT 
    category, COUNT(name)
FROM
    pizza_types
GROUP BY category;

-- Group the orders by date and calculate the average number of pizzas ordered per day

SELECT 
    AVG(quantities) as average_pizza_order
FROM
    (SELECT 
        o.order_date, SUM(od.quantity) AS Quantities
    FROM
        orders o
    JOIN order_details od ON o.order_id = od.order_id
    GROUP BY o.order_date) AS order_quantity;

-- Determine the top 3 most ordered pizza types based on revenue.

SELECT 
    pizza_types.name,
    SUM(order_details.quantity * pizzas.price) AS total_revenue
FROM
    pizza_types
        JOIN
    pizzas ON pizzas.pizza_type_id = pizza_types.pizza_type_id
        JOIN
    order_details ON order_details.pizza_id = pizzas.pizza_id
GROUP BY pizza_types.name
ORDER BY total_revenue DESC
LIMIT 3;


-- Calculate the percentage contribution of each pizza type to total revenue.
select
pizza_types.category,
    SUM(order_details.quantity * pizzas.price)  / (SELECT 
    round(sum((p.price * o.quantity)),2) AS total_revenue
FROM
    order_details AS o
        JOIN
    pizzas AS p ON o.pizza_id = p.pizza_id) *100  as total_revenue
FROM
    pizza_types
        JOIN
    pizzas ON pizzas.pizza_type_id = pizza_types.pizza_type_id
        JOIN
    order_details ON order_details.pizza_id = pizzas.pizza_id
GROUP BY pizza_types.category
ORDER BY total_revenue DESC;

