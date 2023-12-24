# Compute Totals Through Conditional Aggregates

When working with databases, calculating totals based on conditional aggregates is a common task. This is particularly useful when you want to count records meeting specific criteria. Here, we explore different ways to achieve this using Laravel Eloquent.

## Traditional SQL Query

A traditional SQL query for calculating counts based on different conditions might look like the following:

```sql
SELECT 
    COUNT(CASE WHEN status = 'Requested' THEN 1 END) AS request_count,
    COUNT(CASE WHEN status = 'Planned' THEN 1 END) AS planned_count,
    COUNT(CASE WHEN status = 'Completed' THEN 1 END) AS completed_count
FROM features;
```

## Eloquent Raw Query Approach

Leveraging Laravel Eloquent's raw query capabilities, you can achieve the same result with the following:

```php
Feature::toBase()
    ->selectRaw("COUNT(CASE WHEN status = 'Requested' THEN 1 END) AS request_count")
    ->selectRaw("COUNT(CASE WHEN status = 'Planned' THEN 1 END) AS planned_count")
    ->selectRaw("COUNT(CASE WHEN status = 'Completed' THEN 1 END) AS completed_count")
    ->first();
```

The use of `toBase()` is to ensure that only the count result is retrieved, and `first()` fetches the single row with count values.

## PostgreSQL Filter Classes

For PostgreSQL databases, an alternative and possibly more elegant approach involves using filter classes. This approach can lead to more concise and arguably clearer queries:

```php
Feature::selectRaw("COUNT(*) FILTER(WHERE status = 'Requested') AS request_count")
    ->selectRaw("COUNT(*) FILTER(WHERE status = 'Planned') AS planned_count")
    ->selectRaw("COUNT(*) FILTER(WHERE status = 'Completed') AS completed_count")
    ->first();
```

Utilizing filter classes allows for the use of various statements within the `WHERE` clause and can provide a performance boost over the `CASE` statements.

Choose the approach that aligns best with your database type and personal preference. Both methods offer efficient ways to calculate totals using conditional aggregates in Laravel Eloquent.
