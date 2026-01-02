# Case Study #1 - Danny's Diner

<img width="400" height="400" alt="image" src="https://github.com/user-attachments/assets/4395854e-be1d-49fe-ba5a-f1eb87dc5426" />


## Problem Statement

Danny wants to use the data to answer a few simple questions about his customers. He plans on using these insights to help him create decisions.

## Entity Relationship Diagram

<img width="563" height="271" alt="image" src="https://github.com/user-attachments/assets/e42d6814-82c5-4ab8-9729-9d97601ceb08" />

Within Danny's dataset, we have three tables (sales, members, and menu). We also see how they are connected and relate to each other.

---

## Questions

### ⭐ Question 1 - What is the total amount each customer spent at the restaurant?

```

SELECT customer_id, SUM(price) as total_spending
FROM sales
LEFT JOIN menu ON sales.product_id = menu.product_id
GROUP BY customer_id
ORDER BY customer_id ASC

```

Explanation
- Joined sales table and menu table by product_id and LEFT JOIN. Note: If there were nulls for any product_id, I would INNER JOIN
- Grouped by customer_id because Danny wants to know the total amount each customer spent
- Used the SUM() aggregate function to calculate the total spending for each customer
- Ordered by ascending customer_id for readability

Result
| customer_id | total_spending|
| --- | --- |
| A | 76 |
| B | 74 |
| C | 36 |

---

### ⭐ Question 2 - How many days has each customer visited the restaurant?

```

SELECT customer_id, COUNT(DISTINCT order_date) as days_visited
FROM sales
GROUP BY customer_id
ORDER BY customer_id ASC

```

Explanation
- Selected customer_id from sales table
- Used COUNT aggregate function paired with the DISTINCT keyword to count how many unique dates were associated with each customer_id
- Grouped by customer_id
- Ordered by ascending customer_id for readability

Result
| customer_id | days_visited |
| --- | --- |
| A | 4 |
| B | 6 |
| C | 2 |

---

## ⭐ Question 3 - What was the first item from the menu purchased by each customer?

```
WITH cte_table AS (
SELECT customer_id, order_date, product_name, DENSE_RANK() OVER(PARTITION BY customer_id ORDER BY order_date ASC) as order_number
FROM sales
LEFT JOIN menu ON sales.product_id = menu.product_id
)
SELECT customer_id, order_date, product_name, order_number
FROM cte_table
WHERE order_number = '1'

```

Explanation
- Created a CTE and used Window Function DENSE_RANK() to rank each customer's order by their order date
- Joined menu table to get data on product
- Created Select query using CTE to pick customer's first orders

CTE Result

| customer_id | order_date | product_name | order_number |
| ----------- | ---------- | ------------ | ------------ |
| A           | 2021-01-01 | curry        | 1            |
| A           | 2021-01-01 | sushi        | 1            |
| A           | 2021-01-07 | curry        | 2            |
| A           | 2021-01-10 | ramen        | 3            |
| A           | 2021-01-11 | ramen        | 4            |
| A           | 2021-01-11 | ramen        | 4            |
| B           | 2021-01-01 | curry        | 1            |
| B           | 2021-01-02 | curry        | 2            |
| B           | 2021-01-04 | sushi        | 3            |
| B           | 2021-01-11 | sushi        | 4            |
| B           | 2021-01-16 | ramen        | 5            |
| B           | 2021-02-01 | ramen        | 6            |
| C           | 2021-01-01 | ramen        | 1            |
| C           | 2021-01-01 | ramen        | 1            |
| C           | 2021-01-07 | ramen        | 2            |

Final Result

| customer_id | order_date | product_name | order_number |
| ----------- | ---------- | ------------ | ------------ |
| A           | 2021-01-01 | curry        | 1            |
| A           | 2021-01-01 | sushi        | 1            |
| B           | 2021-01-01 | curry        | 1            |
| C           | 2021-01-01 | ramen        | 1            |
| C           | 2021-01-01 | ramen        | 1            |

---

## ⭐ Question 4 - What was the most purchased item on the menu and how many items was it purchased by each customer?

```

WITH cte_table AS (
SELECT product_id, COUNT(product_id) as purchase_count
FROM sales
GROUP BY product_id
ORDER BY purchase_count DESC
LIMIT 1
)
SELECT customer_id, menu.product_name, COUNT(sales.product_id) as times_bought
FROM sales
INNER JOIN cte_table ON sales.product_id = cte_table.product_id
INNER JOIN menu ON sales.product_id = menu.product_id
GROUP BY customer_id, menu.product_name
ORDER BY customer_id ASC

```
Explanation
- Created a CTE to find which product was purchased the most.
- Joined CTE with sales table and used COUNT() to find out how many times each customer bought that product
- Joined with menu table on product_id to display name of product in final table

CTE Result

| product_id | purchase_count |
| --- | --- |
| 3 | 8 |

Final Result

| customer_id | product_name | times_bought |
| ----------- | ------------ | ------------ |
| A           | ramen        | 3            |
| B           | ramen        | 2            |
| C           | ramen        | 3            |

---

## ⭐ Question 5 - Which item was the most popular for each customer?

```

WITH cte_table AS (
SELECT customer_id, product_id, COUNT(*) as times_bought, DENSE_RANK() OVER (PARTITION BY customer_id ORDER BY COUNT(*) DESC) AS ranking
FROM sales
GROUP BY customer_id, product_id
)
SELECT customer_id, product_name as product_most_bought, times_bought
FROM cte_table
INNER JOIN menu ON cte_table.product_id = menu.product_id
WHERE ranking = 1
ORDER BY customer_id ASC

```
Explanation
- Created a CTE to rank which item was purchased the most using DENSE_RANK() which is partitioned by customer id and ordered by count of appearances of customer id and product id in descending order
- Joined menu table for product name and selected all rows from CTE where ranking = 1

CTE Result

| customer_id | product_id | times_bought | ranking |
| ----------- | ---------- | ------------ | ------- |
| A           | 3          | 3            | 1       |
| A           | 2          | 2            | 2       |
| A           | 1          | 1            | 3       |
| B           | 3          | 2            | 1       |
| B           | 1          | 2            | 1       |
| B           | 2          | 2            | 1       |
| C           | 3          | 3            | 1       |

Final Result

| customer_id | product_most_bought | times_bought |
| ----------- | ------------ | ------------ |
| A           | ramen        | 3            |
| B           | sushi        | 2            |
| B           | curry        | 2            |
| B           | ramen        | 2            |
| C           | ramen        | 3            |

---

## ⭐ Question 6 - Which item was purchased first by the customer after they became a member?

```

WITH cte_table AS (
SELECT sales.customer_id, members.join_date, sales.product_id, sales.order_date, DENSE_RANK() OVER(PARTITION BY sales.customer_id ORDER BY sales.order_date ASC) as ranking
FROM sales
INNER JOIN members ON sales.customer_id = members.customer_id AND sales.order_date >= members.join_date
ORDER BY sales.customer_id
)
SELECT customer_id, join_date, product_id, order_date FROM cte_table
WHERE ranking = 1

```

Explanation
- Created a CTE to rank orders placed by their date and also joined the sales and members table on dates greater than/equal to each member's join date.
- Selected from the CTE rows whose rankings = 1

CTE Result

| customer_id | join_date  | product_id | order_date | ranking |
| ----------- | ---------- | ---------- | ---------- | ------- |
| A           | 2021-01-07 | 2          | 2021-01-07 | 1       |
| A           | 2021-01-07 | 3          | 2021-01-10 | 2       |
| A           | 2021-01-07 | 3          | 2021-01-11 | 3       |
| A           | 2021-01-07 | 3          | 2021-01-11 | 3       |
| B           | 2021-01-09 | 1          | 2021-01-11 | 1       |
| B           | 2021-01-09 | 3          | 2021-01-16 | 2       |
| B           | 2021-01-09 | 3          | 2021-02-01 | 3       |

Final Result

| customer_id | join_date  | product_id | order_date |
| ----------- | ---------- | ---------- | ---------- |
| A           | 2021-01-07 | 2          | 2021-01-07 |
| B           | 2021-01-09 | 1          | 2021-01-11 |

---

## ⭐ Question 7 - Which item was purchased just before the customer became a member?

```

WITH cte_table AS (
  SELECT sales.customer_id, members.join_date, sales.product_id, sales.order_date, DENSE_RANK() OVER(PARTITION BY sales.customer_id ORDER BY sales.order_date DESC) as ranking
FROM sales
INNER JOIN members ON sales.customer_id = members.customer_id AND sales.order_date < members.join_date
ORDER BY sales.customer_id
)
SELECT * FROM cte_table
WHERE ranking = 1
ORDER BY customer_id ASC

```

Explanation
- Similar to Question 6, except we modify the JOIN in the CTE to have order dates < than member's join date.
- Also, since we're finding the order the customer placed right before joining, we would need to change the DENSE_RANK() to rank orders by order_date decreasing

CTE Result

| customer_id | join_date  | product_id | order_date | ranking |
| ----------- | ---------- | ---------- | ---------- | ------- |
| A           | 2021-01-07 | 1          | 2021-01-01 | 1       |
| A           | 2021-01-07 | 2          | 2021-01-01 | 1       |
| B           | 2021-01-09 | 1          | 2021-01-04 | 1       |
| B           | 2021-01-09 | 2          | 2021-01-02 | 2       |
| B           | 2021-01-09 | 2          | 2021-01-01 | 3       |

Final Result

| customer_id | join_date  | product_id | order_date | ranking |
| ----------- | ---------- | ---------- | ---------- | ------- |
| A           | 2021-01-07 | 1          | 2021-01-01 | 1       |
| A           | 2021-01-07 | 2          | 2021-01-01 | 1       |
| B           | 2021-01-09 | 1          | 2021-01-04 | 1       |

---

## ⭐ Question 8 - What is the total items and amount spent for each member before they became a member?

```

SELECT s.customer_id, COUNT(*) as total_items, SUM(m.price) as total_spent
FROM sales s
INNER JOIN members mem ON s.customer_id = mem.customer_id AND s.order_date < mem.join_date
INNER JOIN menu m ON s.product_id = m.product_id
GROUP BY s.customer_id
ORDER BY s.customer_id ASC

```

Explanation
- Used COUNT and SUM aggregate functions to find total items and sum of those items
- Inner Joined members table on customer id and order dates less than the member's join date
- Inner joined menu to allow calculation of menu items
- Grouped by customer id

Result

| customer_id | total_items | total_spent |
| ----------- | ----------- | ----------- |
| A           | 2           | 25          |
| B           | 3           | 40          |

---

## ⭐ Question 9 - If each $1 spent equates to 10 points and sushi has a 2x points multiplier - how many points would each customer have?

```

WITH cte_table AS(
  SELECT s.customer_id, m.product_name, 
  CASE
      WHEN m.product_name = 'sushi' THEN m.price * 20
      ELSE m.price * 10
  END AS points
  FROM sales s
  INNER JOIN menu m ON s.product_id = m.product_id
  ORDER BY s.customer_id
)
SELECT customer_id, SUM(points)
FROM cte_table
GROUP BY customer_id
ORDER BY customer_id

```

Explanation
- Created a CTE coupled with a switch case statement to calculate the points for each product
- Used the SUM() aggregate function on the CTE to calculate the total points

CTE Result

| customer_id | product_name | points |
| ----------- | ------------ | ------ |
| A           | curry        | 150    |
| A           | ramen        | 120    |
| A           | ramen        | 120    |
| A           | ramen        | 120    |
| A           | sushi        | 200    |
| A           | curry        | 150    |
| B           | sushi        | 200    |
| B           | sushi        | 200    |
| B           | curry        | 150    |
| B           | curry        | 150    |
| B           | ramen        | 120    |
| B           | ramen        | 120    |
| C           | ramen        | 120    |
| C           | ramen        | 120    |
| C           | ramen        | 120    |

Final Result

| customer_id | sum |
| ----------- | --- |
| A           | 860 |
| B           | 940 |
| C           | 360 |


## ⭐ Question 10 - In the first week after a customer joins the program (including their join date) they earn 2x points on all items, not just sushi - how many points do customer A and B have at the end of January?

```

WITH cte_table AS(
  SELECT s.customer_id, s.order_date, s.product_id, m.join_date, me.product_name, me.price,
  CASE
  	WHEN s.order_date BETWEEN m.join_date AND m.join_date + 6 THEN me.price * 20
  	WHEN me.product_name = 'sushi' THEN me.price * 20
  	ELSE me.price * 10
  END as points
  FROM sales s
  INNER JOIN members m ON s.customer_id = m.customer_id AND s.order_date >= m.join_date
  INNER JOIN menu me ON s.product_id = me.product_id
  WHERE s.order_date < CAST('2/1/2021' AS DATE)
  ORDER BY s.customer_id ASC, s.order_date ASC
)
SELECT customer_id, SUM(points) as total_points
FROM cte_table
GROUP BY customer_id
ORDER BY customer_id ASC

```

Explanation
- Created CTE to calculate points based on product name and join date
- Outside Select statement to sum all points based on customer id

CTE Result

| customer_id | order_date | product_id | join_date  | product_name | price | points |
| ----------- | ---------- | ---------- | ---------- | ------------ | ----- | ------ |
| A           | 2021-01-07 | 2          | 2021-01-07 | curry        | 15    | 300    |
| A           | 2021-01-10 | 3          | 2021-01-07 | ramen        | 12    | 240    |
| A           | 2021-01-11 | 3          | 2021-01-07 | ramen        | 12    | 240    |
| A           | 2021-01-11 | 3          | 2021-01-07 | ramen        | 12    | 240    |
| B           | 2021-01-11 | 1          | 2021-01-09 | sushi        | 10    | 200    |
| B           | 2021-01-16 | 3          | 2021-01-09 | ramen        | 12    | 120    |

Final Result

| customer_id | total_points |
| ----------- | ------------ |
| A           | 1020         |
| B           | 320          |

---


