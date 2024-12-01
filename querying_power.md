1. INNER JOIN Query to retrieve bookings and users:

```sql
SELECT 
    b.booking_id, 
    b.start_date, 
    b.end_date, 
    b.total_price,
    u.user_id, 
    u.first_name, 
    u.last_name, 
    u.email
FROM 
    booking b
INNER JOIN 
    "user" u ON b.user_id = u.user_id;
```


Explanation of INNER JOIN:

- An INNER JOIN returns only the rows that have matching values in both tables.
- In this case, it will return only bookings that have a corresponding user.
- If a booking exists without a user (which shouldn't happen with proper constraints), or a user has no bookings, those records will be excluded.
- It's like a strict intersection of the booking and user tables based on the user\_id.

2. LEFT JOIN Query to retrieve properties and their reviews:

```sql
    SELECT 
    p.property_id, 
    p.name AS property_name,
    p.description,
    r.review_id, 
    r.rating, 
    r.comment
FROM 
    property p
LEFT JOIN 
    review r ON p.property_id = r.property_id
ORDER BY 
    p.property_id, r.review_id;
```




Explanation of LEFT JOIN:

- A LEFT JOIN returns all rows from the left table (property), and the matching rows from the right table (review).
- If no matching review exists for a property, the review columns will be NULL.
- This ensures that ALL properties are returned, even those without any reviews.
- It's useful when you want to see all properties and know which ones have been reviewed.

3. FULL OUTER JOIN Query to retrieve all users and bookings:
```sql
 SELECT 
    u.user_id, 
    u.first_name, 
    u.last_name,
    b.booking_id, 
    b.start_date, 
    b.end_date,
    CASE 
        WHEN b.booking_id IS NULL THEN 'No Booking'
        WHEN u.user_id IS NULL THEN 'Booking without User'
        ELSE 'Booked'
    END AS booking_status
FROM 
    "user" u
FULL OUTER JOIN 
    booking b ON u.user_id = b.user_id
ORDER BY 
    u.user_id, b.booking_id;
```

Explanation of FULL OUTER JOIN:

- A FULL OUTER JOIN returns all rows when there is a match in either the user or booking table.
- It combines the results of both LEFT and RIGHT joins.
- Returns users with no bookings AND bookings with no associated users.
- This is the most inclusive join type, showing all possible combinations.
- In the example, I've added a case statement to help identify the status of each record.

Key Differences:

- INNER JOIN: Only matching records
- LEFT JOIN: All records from the left table, matching records from the right
- FULL OUTER JOIN: All records from both tables, with NULL values where no match exist

---

### **Correlated and Non-Correlated Queries in SQL**

Both **correlated** and **non-correlated** queries are types of subqueries used in SQL. Here's an explanation of each:

---

### **1. Correlated Queries**
- A **correlated query** is a subquery that depends on the outer query for its values. 
- The subquery is executed repeatedly, once for every row selected by the outer query.
- It **references columns** from the outer query in its WHERE clause, so it cannot be executed independently.

#### Example:
Find all users who have made more than 3 bookings:

```sql
SELECT user_id, user_name
FROM users u
WHERE (
    SELECT COUNT(*)
    FROM bookings b
    WHERE b.user_id = u.user_id
) > 3;
```

- **Explanation**:
  - For each row in the `users` table (outer query), the subquery (`SELECT COUNT(*)...`) is executed to count the number of bookings made by that specific user.
  - The subquery relies on `u.user_id` from the outer query to function.

- **Key Characteristics**:
  - Executes row-by-row.
  - Dependent on the outer query.
  - Can be less efficient for large datasets because of repeated execution.

---

### **2. Non-Correlated Queries**
- A **non-correlated query** is a subquery that is **independent** of the outer query.
- The subquery is executed **only once**, and its result is used by the outer query.
- It does **not reference columns** from the outer query and can be executed on its own.

#### Example:
Find all properties that have at least one review:

```sql
SELECT property_id, property_name
FROM properties
WHERE property_id IN (
    SELECT DISTINCT property_id
    FROM reviews
);
```

- **Explanation**:
  - The subquery (`SELECT DISTINCT property_id FROM reviews`) runs independently of the outer query.
  - Its result (a list of `property_id` values) is passed to the outer query to filter properties.

- **Key Characteristics**:
  - Executes only once.
  - Independent of the outer query.
  - More efficient compared to correlated subqueries.

---

### **Comparison Table**

| Feature               | Correlated Query                              | Non-Correlated Query                       |
|-----------------------|-----------------------------------------------|--------------------------------------------|
| **Dependency**         | Depends on the outer query.                  | Independent of the outer query.            |
| **Execution**          | Executes once for each row of the outer query. | Executes once for the entire query.        |
| **Performance**        | Slower for large datasets (row-by-row).       | Faster as it executes only once.           |
| **Example Use Case**   | Queries needing row-by-row comparisons.       | Queries with results reusable by the outer query. |

---

### **Key Takeaway**
- Use **non-correlated queries** wherever possible for better performance.
- Reserve **correlated queries** for cases where the subquery must use data from the outer query.
