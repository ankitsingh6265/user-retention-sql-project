# 📊 User Retention & Growth Analytics (SQL)

## 🚀 Overview

This project analyzes user engagement, retention, and churn using SQL queries. It demonstrates real-world analytics scenarios used in companies like Amazon, Uber, and Flipkart.

---

## 🔥 Key Features

* Daily Active Users (DAU)
* Day 1 Retention Analysis
* User Churn Detection
* Rolling Active Users
* Consecutive Login Streaks

---

## 🛠️ Tech Stack

* SQL (MySQL / PostgreSQL)
* Window Functions
* CTEs
* Aggregations

---

## 📂 Dataset

Table: `logins`

| user_id | login_date |
| ------- | ---------- |
| 1       | 2024-01-01 |
| 1       | 2024-01-02 |
| 2       | 2024-01-01 |

---

## 🧠 SQL Queries

### ✅ Daily Active Users

```sql
SELECT login_date, COUNT(DISTINCT user_id) AS dau
FROM logins
GROUP BY login_date;
```

---

### ✅ Day 1 Retention

```sql
WITH first_login AS (
    SELECT user_id, MIN(login_date) AS first_date
    FROM logins
    GROUP BY user_id
),
next_day_login AS (
    SELECT l.user_id
    FROM logins l
    JOIN first_login f 
    ON l.user_id = f.user_id
    AND l.login_date = DATE_ADD(f.first_date, INTERVAL 1 DAY)
)
SELECT COUNT(DISTINCT n.user_id) * 1.0 / COUNT(DISTINCT f.user_id)
FROM first_login f
LEFT JOIN next_day_login n 
ON f.user_id = n.user_id;
```

---

### ✅ User Streak (3 Days)

```sql
WITH cte AS (
    SELECT user_id, login_date,
    ROW_NUMBER() OVER (PARTITION BY user_id ORDER BY login_date) AS rn
    FROM logins
),
grp AS (
    SELECT *, DATE_SUB(login_date, INTERVAL rn DAY) AS grp_key
    FROM cte
)
SELECT user_id
FROM grp
GROUP BY user_id, grp_key
HAVING COUNT(*) >= 3;
```

---

### ✅ Churned Users

```sql
SELECT user_id
FROM logins
GROUP BY user_id
HAVING MAX(login_date) < CURRENT_DATE - INTERVAL 7 DAY;
```

---

## 📊 Insights

* Identifies active vs inactive users
* Measures retention and churn
* Detects user engagement patterns

---

## 👨‍💻 Author

Ankit Singh
