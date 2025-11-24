# E-Commerce Inventory Analytics (Zepto Dataset)

## Overview
This project simulates how data analysts in the **e-commerce and retail industries** use SQL to clean, explore, and analyze messy catalog data. The dataset was sourced from Kaggle and originally scraped from **Zepto’s official product listings**, mimicking a real-world inventory system.

The goal is to demonstrate **end-to-end SQL workflows**:
- Setting up a messy, real-world e-commerce inventory database  
- Performing **Exploratory Data Analysis (EDA)** to explore product categories, availability, and pricing inconsistencies  
- Implementing **Data Cleaning** to handle null values, remove invalid entries, and convert pricing from paise to rupees  
- Writing **business-driven SQL queries** to derive insights around pricing, inventory, stock availability, revenue, and more  

---

## Dataset Description
Each row represents a unique SKU (Stock Keeping Unit). Duplicate product names exist because the same product may appear multiple times in different package sizes, weights, discounts, or categories.

**Columns:**
- `sku_id`: Unique identifier (synthetic primary key)  
- `name`: Product name as listed in the app  
- `category`: Product category (Fruits, Snacks, Beverages, etc.)  
- `mrp`: Maximum Retail Price (converted from paise to ₹)  
- `discountPercent`: Discount applied on MRP  
- `discountedSellingPrice`: Final price after discount (converted to ₹)  
- `availableQuantity`: Units available in inventory  
- `weightInGms`: Product weight in grams  
- `outOfStock`: Boolean flag for stock availability  
- `quantity`: Number of units per package (mixed with grams for loose produce)  

---

## Database Setup

### Table Creation
```sql
CREATE TABLE zepto (
  sku_id SERIAL PRIMARY KEY,
  category VARCHAR(120),
  name VARCHAR(150) NOT NULL,
  mrp NUMERIC(8,2),
  discountPercent NUMERIC(5,2),
  availableQuantity INTEGER,
  discountedSellingPrice NUMERIC(8,2),
  weightInGms INTEGER,
  outOfStock BOOLEAN,
  quantity INTEGER
);
```

### Data Import
Using `pgAdmin` or `psql`:
```sql
\copy zepto(category,name,mrp,discountPercent,availableQuantity,
            discountedSellingPrice,weightInGms,outOfStock,quantity)
FROM 'data/zepto_v2.csv'
WITH (FORMAT csv, HEADER true, DELIMITER ',', QUOTE '"', ENCODING 'UTF8');
```

---

## Workflow

### 1. Exploratory Data Analysis (EDA)
- Counted total records  
- Viewed random samples to understand structure  
- Checked for null values across columns  
- Identified distinct product categories  
- Compared in-stock vs out-of-stock counts  
- Detected duplicate products (same name, different SKUs)  

### 2. Data Cleaning
- Removed rows where `mrp = 0` or `discountedSellingPrice = 0`  
- Converted `mrp` and `discountedSellingPrice` from paise → rupees  
- Standardized null handling (e.g., imputed missing weights with category averages)  

### 3. Business Insights
Key SQL queries included:

- **Top 10 best-value products** by discount percentage  
- **High-MRP products out of stock** (supply chain risk)  
- **Potential revenue per category**  
- **Expensive products (MRP > ₹500) with minimal discount**  
- **Top 5 categories with highest average discounts**  
- **Price per gram analysis** for value-for-money products  
- **Weight-based grouping** (Low, Medium, Bulk)  
- **Total inventory weight per category**  

---

## Example Queries

### Best-Value Products
```sql
SELECT name, category, discountPercent
FROM zepto
ORDER BY discountPercent DESC
LIMIT 10;
```

### Potential Revenue by Category
```sql
SELECT category,
       SUM(discountedSellingPrice * availableQuantity) AS potential_revenue
FROM zepto
GROUP BY category
ORDER BY potential_revenue DESC;
```

### Price per Gram
```sql
SELECT name, category,
       discountedSellingPrice / weightInGms AS price_per_gram
FROM zepto
WHERE weightInGms > 0
ORDER BY price_per_gram ASC
LIMIT 10;
```

---

## Key Takeaways
- Real-world catalog data is **messy**: duplicates, nulls, inconsistent units.  
- SQL cleaning ensures **business-ready datasets**.  
- Insights reveal:  
  - High-discount products drive customer acquisition  
  - Out-of-stock premium items highlight supply chain risks  
  - Category-level revenue and discount analysis informs pricing strategies  
  - Weight-based grouping supports logistics and packaging decisions  

---

## Conclusion
This project demonstrates how SQL can transform raw e-commerce catalog data into **strategic business insights**. It covers the full workflow from **database setup → cleaning → EDA → business queries**, simulating the work of actual data analysts in retail/e-commerce.

---

## How to Use
1. Clone this repository  
2. Set up PostgreSQL and create the `zepto` table  
3. Import the dataset using `\copy`  
4. Run the SQL scripts in `/queries` to reproduce analysis  

---

## Future Improvements
- Automate cleaning with stored procedures  
- Build dashboards (e.g., Power BI, Tableau) for visualization  
- Extend analysis to customer purchase behavior  

---
