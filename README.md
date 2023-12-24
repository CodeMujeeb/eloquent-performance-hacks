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
