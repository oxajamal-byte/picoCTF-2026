# Secret Box — 200 pts

**Category:** Web Exploitation  
**Author:** Janice He

## Overview

A web app that lets users create and view secrets. The hint says SQL injection so I went straight for the source code.

## Approach

The app runs on PostgreSQL. Looking through the source I found the vulnerability in the `/secrets/create` endpoint. The INSERT statement builds the query with string concatenation instead of parameterized queries which means user input goes directly into the SQL.

This isnt your typical login bypass. The injection point is inside an INSERT not a SELECT, so `' OR 1=1 --` doesnt apply here. Instead I needed to use PostgreSQL's concatenation operator `||` to pull data from another table into a field I could read back.

The payload:

```sql
' || (SELECT secret FROM secrets WHERE username='admin') || '
```

Submitted that through the create secrets form. The injected SQL ran a subquery that grabbed the admin's secret and concatenated it into my own entry. Viewed my secrets and the flag was sitting right there.

Worth noting that PostgreSQL uses `||` for concatenation while MySQL uses `CONCAT()`. Knowing which database youre dealing with matters when crafting injections.

## The fix

Parameterized queries. Thats it. Every modern framework supports prepared statements out of the box. There is genuinely no reason for SQL injection to exist in any application written today, but it still keeps showing up.
