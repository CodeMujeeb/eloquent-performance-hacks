# Laravel Eloquent Performance Optimization

This repository provides a comprehensive guide and implementation for optimizing memory performance and handling `hasMany` relationships efficiently in Laravel Eloquent.

## Table of Contents
1. [Minimize Memory Performance](#1-minimize-memory-performance)
2. [Getting Records from hasMany Relationships](#2-getting-records-from-hasmany-relationships)
    - [Issue: N+1 Problem](#issue-n1-problem)
    - [Solution: Eager Loading](#solution-eager-loading)
    - [Issue: Excessive Database Queries](#issue-excessive-database-queries)
    - [Solution: Caching and Denormalization](#solution-caching-and-denormalization)
    - [Solution: Subqueries](#solution-subqueries)
        - [Subqueries with Time Casting](#subqueries-with-time-casting)
        - [Refactor to Model Scope](#refactor-to-model-scope)

## 1. Minimize Memory Performance

To optimize memory usage, ensure to select only the essential data that you need to interact with. This practice helps in reducing unnecessary memory overhead.

## 2. Getting Records from hasMany Relationships

### Issue: N+1 Problem

When retrieving records from a `hasMany` relationship, there is a common issue known as the N+1 problem. For example, fetching the latest login information for users using `$user->last_logins->latest()->first()->created_at->diffForHumans()` can lead to N+1 problems, resulting in duplicated queries.

### Solution: Eager Loading

Eager loading can be used to address the N+1 issue. By utilizing the `with` method, you can eager load related data efficiently. For instance:

```php
$user->last_logins->latest()->first()->created_at->diffForHumans()
```

## Issue: Excessive Database Queries

Even with eager loading, there may be a problem with excessive database queries. For instance, loading 15 users with a related model containing 3555 records can lead to performance issues and increased memory usage.

## Solution: Caching and Denormalization

To mitigate this, consider using caching or denormalization. Adding a last_login_id column to the users table or caching results can significantly reduce the number of queries.

## Solution: Subqueries

An elegant solution is to use subqueries. Subqueries allow you to add extra columns to a query computed from another table, reducing the need for multiple queries.
Subqueries with Time Casting
Implementing a subquery with time casting can be achieved using the following example:
```php
User::query()
    ->addSelect(['last_login_at' => Login::select('created_at')
        ->whereColumn('user_id', 'users.id')
        ->latest()
        ->take(1)
    ])
    ->orderBy('name')
    ->paginate(10)
```
#### Refactor to Model Scope
```php
// User model
public function scopeWithLastLoginAt($query)
{
    $query->addSelect(['last_login_at' => Login::select('created_at')
        ->whereColumn('user_id', 'users.id')
        ->latest()
        ->take(1)
    ]);
}

// Controller
User::query()->withLastLoginAt()->orderBy('name')->paginate(10);
```



```For better code organization and reusability, move the subquery logic to a scope in the model. This allows you to call withLastLoginAt() on the query builder.```

