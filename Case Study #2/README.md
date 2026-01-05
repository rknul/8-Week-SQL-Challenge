# Case Study #2 - Pizza Runner

<img width="400" height="400" alt="image" src="https://github.com/user-attachments/assets/b11764cb-41e9-451d-8490-91e2cda3bfaa" />



## Problem Statement

Danny wants us to clean the data and apply some basic calculations so he can give better instructions to his runners and optimize his business

## Entity Relationship Diagram

<img width="779" height="408" alt="image" src="https://github.com/user-attachments/assets/15c41ecb-58bb-4e75-8e27-f89dc8b5a6e6" />

---

## Questions

---

## Pizza Metrics

### ⭐ Question 1 - How many pizzas were ordered?
```

    SELECT COUNT(order_time) as pizzas_ordered
    FROM customer_orders

```

---

Explanation
- Simple aggregate use to count how many pizzas were ordered

Result

| pizzas_ordered |
| -------------- |
| 14             |

---

### ⭐ Question 2 - How many unique customer orders were made?

```
SELECT COUNT(*)
FROM(
	SELECT DISTINCT pizza_id, exclusions, extras
  FROM customer_orders
) as unique_pizza_orders

```

Explanation
- Counted all rows of subquery which found all unique pizza orders


Result
| count |
| --- |
| 10 |

---

## ⭐ Question 3 - How many successful orders were delivered by each runner?

```

SELECT COUNT(*)
FROM runner_orders
WHERE duration != 'null'

```

Explanation
- Since column 'duration' is how long it took to deliver the pizza, I simply counted how many rows where duration was not equal to null

Final Result

| count |
| --- |
| 8 |

---

## ⭐ Question 4 - How many of each type of pizza was delivered?

```

SELECT pizza_id, COUNT(pizza_id)
FROM customer_orders c
INNER JOIN runner_orders r ON c.order_id = r.order_id
WHERE r.duration != 'null'
GROUP BY pizza_id
ORDER BY pizza_id ASC

```
Explanation
- Joined runner_orders table to customer_orders and set the condition to successfully delivered orders by filtering out duration != null
- Used COUNT() aggregate function to count how many pizzas were delivered of each type

Result

| pizza_id | count |
| -------- | ----- |
| 1        | 9     |
| 2        | 3     |

---

## ⭐ Question 5 - How many Vegetarian and Meatlovers were ordered by each customer?

```

    SELECT customer_id, pizza_id, COUNT(*) as pizzas_ordered
    FROM customer_orders
    GROUP BY customer_id, pizza_id
    ORDER BY customer_id ASC, pizza_id ASC

```
Explanation
- Used COUNT() aggregate function and grouped by customer_id and pizza_id

Result

| customer_id | pizza_id | pizzas_ordered |
| ----------- | -------- | -------------- |
| 101         | 1        | 2              |
| 101         | 2        | 1              |
| 102         | 1        | 2              |
| 102         | 2        | 1              |
| 103         | 1        | 3              |
| 103         | 2        | 1              |
| 104         | 1        | 3              |
| 105         | 2        | 1              |

---

## ⭐ Question 6 - What was the maximum number of pizzas delivered in a single order?

```

SELECT c.order_id, COUNT(*) as pizzas_delivered
FROM customer_orders c
INNER JOIN runner_orders r ON c.order_id = r.order_id
WHERE r.duration != 'null'
GROUP BY c.order_id
ORDER BY pizzas_delivered DESC
LIMIT 1


```

Explanation
- Joined runner_orders and customer_orders and filtered which were delivered
- Grouped by order_id and counted how many pizzas were delivered by each group
- Ordered Count in descending order and limited it to 1 row

Result

| order_id | pizzas_delivered |
| -------- | ----- |
| 4        | 3     |

---

## ⭐ Question 7 - For each customer, how many delivered pizzas had at least 1 change and how many had no changes?

```



```

Explanation

CTE Result


Final Result


---

## ⭐ Question 8 - How many pizzas were delivered that had both exclusions and extras?

```


```

Explanation


Result


---

## ⭐ Question 9 - What was the total volume of pizzas ordered for each hour of the day?

```


```

Explanation

CTE Result


Final Result



## ⭐ Question 10 - What was the volume of orders for each day of the week?

```


```

Explanation

CTE Result


Final Result


---

## Runner and Customer Experience

### ⭐ Question 1 - How many runners signed up for each 1 week period? (i.e. week starts 2021-01-01)

```



```

Explanation

Result


---

### ⭐ Question 2 - What was the average time in minutes it took for each runner to arrive at the Pizza Runner HQ to pickup the order?

```



```

Explanation


Result


---

## ⭐ Question 3 - Is there any relationship between the number of pizzas and how long the order takes to prepare?

```


```

Explanation


Final Result



---

## ⭐ Question 4 - What was the average distance travelled for each customer?

```



```
Explanation


CTE Result



Final Result



---

## ⭐ Question 5 - What was the difference between the longest and shortest delivery times for all orders?

```



```
Explanation


CTE Result


Final Result


---

## ⭐ Question 6 - What was the average speed for each runner for each delivery and do you notice any trend for these values?

```



```

Explanation


CTE Result



Final Result



---

## ⭐ Question 7 - What is the successful delivery percentage for each runner?

```



```

Explanation

CTE Result


Final Result


---

## Ingredient Optimization

### ⭐ Question 1 - What are the standard ingredients for each pizza?

```



```

Explanation

Result


---

### ⭐ Question 2 - What was the most commonly added extra?

```



```

Explanation


Result


---

## ⭐ Question 3 - What was the most common exclusion?

```


```

Explanation


Final Result



---

## ⭐ Question 4 - Generate an order item for each record in the customers_orders table in the format of one of the following:
  Meat Lovers
  Meat Lovers - Exclude Beef
  Meat Lovers - Extra Bacon
  Meat Lovers - Exclude Cheese, Bacon - Extra Mushroom, Peppers

```



```
Explanation


CTE Result



Final Result



---

## ⭐ Question 5 - Generate an alphabetically ordered comma separated ingredient list for each pizza order from the customer_orders table and add a 2x in front of any relevant ingredients
  For example: "Meat Lovers: 2xBacon, Beef, ... , Salami"

```



```
Explanation


CTE Result


Final Result


---

## ⭐ Question 6 - What is the total quantity of each ingredient used in all delivered pizzas sorted by most frequent first?

```



```

Explanation


CTE Result



Final Result



---

## Ingredient Optimization

### ⭐ Question 1 - What are the standard ingredients for each pizza?

```



```

Explanation

Result


---

### ⭐ Question 2 - What was the most commonly added extra?

```



```

Explanation


Result


---

## ⭐ Question 3 - What was the most common exclusion?

```


```

Explanation


Final Result



---

## ⭐ Question 4 - Generate an order item for each record in the customers_orders table in the format of one of the following:
  Meat Lovers
  Meat Lovers - Exclude Beef
  Meat Lovers - Extra Bacon
  Meat Lovers - Exclude Cheese, Bacon - Extra Mushroom, Peppers

```



```
Explanation


CTE Result



Final Result



---

## ⭐ Question 5 - Generate an alphabetically ordered comma separated ingredient list for each pizza order from the customer_orders table and add a 2x in front of any relevant ingredients
  For example: "Meat Lovers: 2xBacon, Beef, ... , Salami"

```



```
Explanation


CTE Result


Final Result


---

## ⭐ Question 6 - What is the total quantity of each ingredient used in all delivered pizzas sorted by most frequent first?

```



```

Explanation


CTE Result



Final Result



---
