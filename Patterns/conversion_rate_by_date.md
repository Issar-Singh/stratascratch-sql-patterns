# Conversion Rate by Date

## Pattern
Calculate a conversion rate where an initial event happens on one date
and the conversion may occur on a later date.

This pattern applies when:
- Base event is recorded (e.g., request sent)
- Conversion event may happen later (e.g., request accepted)
- Missing conversion rows imply non-conversion

---

## Core Idea
1. Identify base events (sent)
2. Identify converted events (accepted)
3. LEFT JOIN base → conversion on entity keys
4. Group by base event date
5. Conversion rate = converted / total
6. Filter dates with at least one conversion

---

## SQL Template
```sql
WITH base_events AS (
  SELECT date, sender_id, receiver_id
  FROM events
  WHERE action = 'sent'
),
conversions AS (
  SELECT DISTINCT sender_id, receiver_id
  FROM events
  WHERE action = 'accepted'
)
SELECT
  b.date,
  1.0 * COUNT(c.sender_id) / COUNT(*) AS conversion_rate
FROM base_events b
LEFT JOIN conversions c
  ON b.sender_id = c.sender_id
 AND b.receiver_id = c.receiver_id
GROUP BY b.date
HAVING COUNT(c.sender_id) > 0;
