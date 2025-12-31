# Case Study #1 - Danny's Diner

<img width="400" height="400" alt="image" src="https://github.com/user-attachments/assets/4395854e-be1d-49fe-ba5a-f1eb87dc5426" />


## Problem Statement

Danny wants to use the data to answer a few simple questions about his customers. He plans on using these insights to help him create decisions.

## Entity Relationship Diagram

<img width="563" height="271" alt="image" src="https://github.com/user-attachments/assets/e42d6814-82c5-4ab8-9729-9d97601ceb08" />

Within Danny's dataset, we have three tables (sales, members, and menu). We also see how they are connected and relate to each other.

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
