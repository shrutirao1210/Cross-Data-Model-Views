# XDM Views – Federated Cross Data Model Query Engine

A metadata-driven federated query engine that enables unified querying across heterogeneous databases such as Relational Databases (SQL/MySQL) and XML databases using logical views.

---

# Overview

XDM Views is a federated database system that allows a single logical query to retrieve and merge data from multiple heterogeneous data sources.

The project integrates:

- Relational databases (MySQL)
- XML documents
- SQL querying
- XPath querying
- Metadata-driven logical views
- Cross-database joins
- React-based visualization frontend

The system dynamically translates logical view definitions into SQL and XPath queries, executes them on their respective databases, and joins the results in memory to produce a unified tabular output.

---

# Core Concept

This project is based on the concept of **XDM (XML Data Model)** where heterogeneous data sources are abstracted through a common metadata layer.

Instead of writing hardcoded SQL/XPath queries directly, users define:

- entities
- relationships
- filters
- projections
- logical views

inside XML metadata files.

The query engine interprets these definitions dynamically at runtime.

---

# Key Features

- Federated query processing
- SQL + XML integration
- Metadata-driven architecture
- XPath-based XML querying
- Cross-database joins
- Dynamic query planning
- Logical view abstraction
- In-memory federated joins
- React frontend for schema/view editing and query execution
- API-driven backend execution engine

---

# System Architecture

## Three-Layer Metadata Architecture

### 1. Meta-Meta Schema (`metaMetaSchema.xml`)

Defines the structure and validation rules for the metadata system itself using XSD.

Acts as a schema for the MetaSchema.

---

### 2. Meta Schema (`MetaSchema.xml`)

Acts as the global schema registry for the entire system.

Defines:

- databases
- entities
- attributes
- relationships
- physical mappings

Supports both relational and XML entities.

---

### 3. Views (`views/views.xml`)

Defines logical query views independent of underlying database technologies.

Each view contains:

- projections
- filters
- joins
- base entities
- relationship references

Equivalent to logical SQL views spanning multiple databases.

---

# Tech Stack

- Python 3
- MySQL
- XML
- XPath
- React
- Vite
- ElementTree

---

# Project Structure

```text
.
├── frontend/
│   ├── src/
│   │   ├── App.jsx
│   │   ├── main.jsx
│   │   └── styles.css
│   ├── index.html
│   ├── vite.config.js
│   └── package.json
│
├── views/
│   └── views.xml
│
├── dummy_data/
│   ├── create_database.sql
│   ├── purchaseorders.xml
│   ├── customers.db
│   └── init_database.py
│
├── MetaSchema.xml
├── metaMetaSchema.xml
├── query_engine.py
├── xdm_frontend_server.py
├── .gitignore
└── README.md
```

---

# Databases Used

## Relational Database (MySQL)

### Customer Table

| Attribute | Type |
|---|---|
| customer_id | Integer |
| name | String |
| city | String |

---

### CustomerLoyalty Table

| Attribute | Type |
|---|---|
| loyalty_id | Integer |
| customer_id | Integer |
| membership_tier | String |
| reward_points | Integer |
| enrolled_on | Date |

---

## XML Database

### PurchaseOrders Structure

```xml
<PurchaseOrders>
  <PurchaseOrder>
    <order_id>101</order_id>
    <customer_id>1</customer_id>
    <amount>45000</amount>

    <item>
      <item_name>Laptop</item_name>
      <item_category>Electronics</item_category>
    </item>
  </PurchaseOrder>
</PurchaseOrders>
```

The XML database contains nested hierarchical structures which are dynamically flattened during query execution.

---

# Relationships Defined

## CustomerPurchaseJoin

Join between:

```text
Customer.customer_id = PurchaseOrder.customer_id
```

---

## CustomerLoyaltyJoin

Join between:

```text
Customer.customer_id = CustomerLoyalty.customer_id
```

---

# Views Implemented

| View | Description |
|---|---|
| HighValueCustomers | Customers with purchases greater than 10000 |
| CustomersByItem | Customers who purchased Laptop |
| CustomerPurchases | Purchases of a specific customer |
| Purchases of New York | Customers from New York and their purchases |
| HighValueNYOrders | New York customers with purchases > 5000 |
| Whole Data | Combined data from all entities |
| Customers with reward_points > 4000 | High loyalty customers |
| Customers with Gold membership | Gold tier loyalty customers |

---

# Query Execution Workflow

```text
View Definition (XML)
        ↓
View Resolver
        ↓
Query Planner
        ↓
 SQL + XPath Execution
        ↓
Result Extraction
        ↓
Federated Join Processing
        ↓
Merged Unified Output
```

---

# XML Flattening Mechanism

The XML database contains nested hierarchical structures.

Example:

```xml
<item>
   <item_name>Laptop</item_name>
   <item_category>Electronics</item_category>
</item>
```

The system dynamically flattens XML hierarchy into relational-style rows using the `path` attribute defined in `MetaSchema.xml`.

Example:

```xml
<Attribute name="item_name" path="item/item_name"/>
```

The query engine navigates XML nodes step-by-step and extracts flat values dynamically.

Final flattened result:

```python
{
  "order_id": "101",
  "customer_id": "1",
  "item_name": "Laptop"
}
```

---

# Join Algorithm

The engine uses an in-memory **hash join** algorithm for federated joins.

### Workflow

1. Build hash index on right entity join key
2. Iterate through left rows
3. Lookup matching rows using hash map
4. Merge rows into unified output

### Complexity

```text
O(n + m)
```

instead of nested loop complexity:

```text
O(n × m)
```

---

# Setup Instructions

## 1. Clone Repository

```bash
git clone <repository-url>
```

---

## 2. Navigate to Repository

```bash
cd <repository-folder>
```

---

## 3. Create Virtual Environment

```bash
python3 -m venv venv
```

---

## 4. Activate Virtual Environment

### Linux / macOS

```bash
source venv/bin/activate
```

### Windows

```bash
venv\Scripts\activate
```

---

## 5. Install Backend Dependencies

```bash
pip install flask python-dotenv mysql-connector-python
```

---

## 6. Install Frontend Dependencies

```bash
cd frontend
npm install
```

Return to project root:

```bash
cd ..
```

---

# Database Setup

Run the SQL script inside MySQL:

```bash
mysql -u root -p < dummy_data/create_database.sql
```

This creates:

- Customer table
- CustomerLoyalty table
- Sample records

---

# Running the Project

## Terminal 1 — Start Backend Server

```bash
source venv/bin/activate
python xdm_frontend_server.py
```

Backend runs on:

```text
http://localhost:8000
```

---

## Terminal 2 — Start Frontend

```bash
cd frontend
npm run dev
```

Frontend runs on:

```text
http://localhost:5173
```

---

# Frontend Features

The React frontend provides:

- MetaSchema editor
- Views editor
- View selector dropdown
- Query execution
- Result visualization
- JSON output mode
- Table output mode
- Runtime statistics

---

# API Endpoints

| Method | Endpoint | Purpose |
|---|---|---|
| GET | `/api/health` | Health check |
| GET | `/api/bootstrap` | Load metadata + views |
| POST | `/api/inspect` | Parse/inspect XML definitions |
| POST | `/api/execute` | Execute selected view |

---

# Sample Output

```python
{
  'customer_id': '1',
  'name': 'Alice Johnson',
  'city': 'New York',
  'order_id': '101',
  'amount': '45000',
  'item_name': 'Laptop',
  'item_category': 'Electronics'
}
```

---

# Design Decisions

## Metadata-Driven Querying

All query logic is abstracted through XML metadata rather than hardcoded queries.

---

## Simplified XPath Engine

The engine implements a lightweight XPath interpreter using Python's built-in `ElementTree`.

---

## Type Normalization

XML values are strings while MySQL values are typed.

The engine normalizes types during joins to ensure:

```text
"5" == 5
```

works correctly.

---

## In-Memory Federated Joins

No data is physically copied between databases.

Results are merged dynamically during query execution.

---

# Limitations

Current implementation supports:

- Equi-joins only
- Limited XPath support
- In-memory XML processing
- No nested views
- No outer joins
- Single XML document source
- Manual SQL filter construction

---

# Future Enhancements

- Full XQuery support
- Query optimization
- Multiple XML sources
- JSON database support
- Query caching
- Nested logical views
- Distributed execution
- Advanced join types

---

# Conclusion

XDM Views demonstrates a metadata-driven federated query engine capable of integrating relational and XML databases through logical view abstraction.

The project highlights:

- federated query execution
- metadata-driven architecture
- XML hierarchy flattening
- cross-database joins
- dynamic SQL/XPath generation
- unified data abstraction

under a single extensible framework.
