## 🧠 What is DynamoDB?

**Amazon DynamoDB** is a **fully managed NoSQL database service** by AWS that offers:

* High speed and low latency
* No need to manage servers
* Automatic scaling
* Great for key-value or document-based data

> Unlike SQL (relational) databases, DynamoDB doesn’t need tables with fixed schemas or JOINs. It uses **primary keys** to retrieve and organize data.

---

## ✅ Goal of the Experiment

You will:

1. Create a DynamoDB table
2. Perform basic **CRUD** operations:

   * **C**reate item
   * **R**ead item
   * **U**pdate item
   * **D**elete item
3. Delete the whole table

---

## 🚀 Step-by-Step Guide

---

### 🔸 Step 1: Create DynamoDB Table

1. Go to **AWS Console → DynamoDB**
2. Click **"Create table"**

**Fill in the following:**

* **Table name**: `MyDynamoTable` (you can choose any name)
* **Partition key**: `id` → **Type**: `String`

✅ Leave the other options default (no sort key, no secondary index)

3. Click **"Create Table"**

🧠 **Why?**
The **partition key** (like `id`) is used to uniquely identify each item in the table — it’s required.

---

### 🔸 Step 2: Create Item (Insert data)

Once your table is active:

1. Click on the table name → Select **"Explore table items"** from the tabs
2. Click **"Create item"**

Now enter key-value pairs:

```json
{
  "id": "101",
  "name": "Alice",
  "department": "Engineering"
}
```

3. Click **"Create item"**

🧠 **Why?**
This adds a record (item) to your NoSQL table. You can add as many attributes as you want — DynamoDB is schema-less.

---

### 🔸 Step 3: Read Item (View Data)

After inserting:

* You’ll see your item under the “Explore table items” section
* Click the row to expand and read the attributes and values

🧠 **Why?**
Reading is just viewing — no need to run queries for simple key-based lookups in the UI.

---

### 🔸 Step 4: Update Item

1. Select the item row → Click **Actions > Edit item**

2. You can:

   * Change a value (e.g., `"department": "R&D"`)
   * Add new attributes (e.g., `"email": "alice@example.com"`)

3. Click **Save and Close**

🧠 **Why?**
DynamoDB allows partial updates — just change what you need.

---

### 🔸 Step 5: Delete Item

1. Select the item row → Click **Actions > Delete item**
2. Confirm deletion

🧠 **Why?**
Removes just this single record from the table.

---

### 🔸 Step 6: Delete the Whole Table

1. In the left sidebar, go back to the **Tables** list
2. Select your table → Click **"Delete table"**
3. Type `delete` to confirm and click **Delete**

🧠 **Why?**
This removes the table and all its data. It’s irreversible.

---

## ✅ Summary: CRUD Operations in DynamoDB

| **Action**   | **How?**                | **Why?**                    |
| ------------ | ----------------------- | --------------------------- |
| Create       | "Create item" button    | Add new records             |
| Read         | View in "Explore items" | Fetch/view data             |
| Update       | "Edit item"             | Change existing data        |
| Delete       | "Delete item"           | Remove one record           |
| Delete Table | "Delete table"          | Remove all data + structure |

---

