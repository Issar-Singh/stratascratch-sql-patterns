## Pattern: Highest Aggregated Value per Group (Handling Ties)

### Problem Type
Find the entity/entities with the highest aggregated value
within each group (e.g., highest daily total, top seller per region),
including ties.

### When to Use
- Top customer per day
- Highest revenue per category
- Best-performing entity per time period
- When ties must be included

### SQL Template

```sql
WITH aggregated_values AS (
    SELECT
        group_key,
        entity_id,
        SUM(metric) AS total_metric
    FROM source_table
    WHERE date_column BETWEEN 'start_date' AND 'end_date'
    GROUP BY group_key, entity_id
),
ranked_values AS (
    SELECT
        *,
        RANK() OVER (
            PARTITION BY group_key
            ORDER BY total_metric DESC
        ) AS rnk
    FROM aggregated_values
)
SELECT *
FROM ranked_values
WHERE rnk = 1;
