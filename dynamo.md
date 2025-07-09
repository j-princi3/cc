
---

# ✅ **Experiment 10: Working with DynamoDB**

---

## 🎯 **Objective:**

Set up and operate a DynamoDB table for managing product data in an e-commerce platform. Learn how to create tables, insert data using multiple methods, and query/filter it using **console**, **JSON**, and **PartiQL** (SQL-like queries for DynamoDB).

---

## 🔧 **Task 1: Create “Products” Table with `ProductID` as Partition Key**

### ✅ Steps:

1. Go to **AWS Console → DynamoDB → Tables → Create table**
2. **Table Name**: `Products`
3. **Partition key**: `ProductID` (Type: String)
4. Leave sort key empty (not needed)
5. Capacity mode: **On-Demand** (good for unpredictable traffic)
6. Click **Create Table**

---

### 🧠 Why:

Using `ProductID` as a partition key ensures each product is uniquely identified. On-demand capacity mode avoids manual provisioning and scales automatically.

---

## 🔧 **Task 2: Insert 3 Product Records**

---

### 🅰️ **Insert via AWS Console Form**

**Record: Smartphone**

1. Go to **Products → Explore Table Items → Create Item**
2. Use the **Form view**
3. Fill in:

```plaintext
ProductID: P1001
Name: Galaxy S23
Category: Electronics
Price: 999
StockCount: 25
```

4. Click **Create item**

---

### 🧠 Why:

The console form is user-friendly for small, manual insertions and testing purposes.

---

### 🅱️ **Insert via JSON Format**

**Record: Laptop**

1. Click **Create item → JSON view**
2. Paste this JSON:

```json
{
  "ProductID": "P1002",
  "Name": "MacBook Air M2",
  "Category": "Electronics",
  "Price": 1299,
  "StockCount": 10
}
```

3. Click **Create item**

---

### 🧠 Why:

JSON allows bulk or programmatic insertions with copy-paste efficiency and structure.

---

### 🅾️ **Insert via PartiQL Editor**

**Record: Headphones**

1. Go to **DynamoDB → PartiQL editor**
2. Run this query:

```sql
INSERT INTO Products VALUE {
  'ProductID': 'P1003',
  'Name': 'Sony WH-1000XM5',
  'Category': 'Electronics',
  'Price': 349,
  'StockCount': 15
}
```

3. Click **Run**

---

### 🧠 Why:

PartiQL allows SQL-like commands, ideal for developers familiar with relational DBs. It can be used to read/write/query DynamoDB seamlessly.

---

## 🔎 **Task 3: Scan All Electronics Products**

1. Go to **PartiQL Editor**
2. Run:

```sql
SELECT * FROM Products WHERE Category = 'Electronics'
```

---

### 🧠 Why:

A `SCAN` reads all items and applies filters. It’s **inefficient** on large datasets, but useful for broad reads during testing or low-scale usage.

---

## 🔍 **Task 4: Query for `ProductID = 'P1005'`**

(Assume you’ve inserted product with ID `P1005` beforehand)

### ✅ Using PartiQL:

```sql
SELECT * FROM Products WHERE ProductID = 'P1005'
```

---

### 🧠 Why:

This is a **Query**, not Scan, because we’re searching **using the partition key**, which is indexed and optimized for fast lookups.

---

## 💡 **Task 5: PartiQL – Products under \$50 with stock available**

### ✅ Query:

```sql
SELECT * FROM Products 
WHERE Price < 50 AND StockCount > 0
```

---

### 🧠 Why:

This helps filter **budget products that are in stock**, useful for flash sales or limited-time offers. Though it's a Scan operation in DynamoDB, PartiQL makes the syntax **intuitive and SQL-like**.

---

## 🧠 You Now Understand:

| Skill                             | Purpose                                      |
| --------------------------------- | -------------------------------------------- |
| Table creation with partition key | Fast and structured access to items          |
| Multiple insert methods           | Flexibility for different user/dev personas  |
| Scan vs Query                     | Scan = wide filter, Query = key-based lookup |
| PartiQL usage                     | SQL-style DynamoDB access                    |

---

## 📦 Example Summary View

| ProductID | Name            | Category    | Price | StockCount |
| --------- | --------------- | ----------- | ----- | ---------- |
| P1001     | Galaxy S23      | Electronics | 999   | 25         |
| P1002     | MacBook Air M2  | Electronics | 1299  | 10         |
| P1003     | Sony WH-1000XM5 | Electronics | 349   | 15         |
| P1004     | USB Cable       | Accessories | 9.99  | 100        |
| P1005     | Keyboard        | Electronics | 45    | 12         |

---
