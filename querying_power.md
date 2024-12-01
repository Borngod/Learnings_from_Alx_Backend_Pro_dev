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
