# Pizza Sales Data Analysis Project

## PART 1: Connecting & Shaping Data

### Tasks:
Before proceeding, try answering the following:

1. How would you load and clean data from the given CSV files in Power BI?
2. How would you ensure that all tables have the correct data types?
3. How would you handle missing or null values in the datasets?
4. How would you create a new column that extracts only the hour from the "time" field in the `orders` table?
5. How would you create a new column in `pizza_types` that extracts only the first ingredient from the ingredient list?

<details>
  <summary>Show Solutions</summary>

- **Load Data:** Connect to all CSV files in Power BI and promote headers.
- **Ensure Correct Data Types:**  
  - `order_id`, `order_details_id`, `pizza_id` → Whole Numbers  
  - `date` → Date Format  
  - `time` → Time Format  
  - `price` → Decimal  
- **Handle Missing Values:** Replace nulls with default values or use `Remove Nulls`.
- **Extract Hour from Time:**  
  `Hour = HOUR(orders[time])`
- **Extract First Ingredient:**  
  `First Ingredient = LEFT(pizza_types[ingredients], FIND(",", pizza_types[ingredients]) - 1)`

</details>

---

## PART 2: Creating the Data Model

### Tasks:
Before revealing the solutions, try to answer:

1. How would you arrange the tables in **Model View** to create relationships?
2. What primary and foreign key relationships exist between tables?
3. What kind of cardinality would you define between `order_details` and `orders`?
4. Would you create any inactive relationships? If yes, where?
5. Should all filters flow in one direction, or are bidirectional filters required?

<details>
  <summary>Show Solutions</summary>

- **Arrange Tables:** Place lookup tables (`pizza_types`, `pizzas`) above transaction tables (`order_details`, `orders`).
- **Relationships:**
  - `orders[order_id]` → `order_details[order_id]` (One-to-Many)
  - `pizzas[pizza_id]` → `order_details[pizza_id]` (One-to-Many)
  - `pizza_types[pizza_type_id]` → `pizzas[pizza_type_id]` (One-to-Many)
- **Cardinality:** One-to-Many (Orders → Order Details).
- **Inactive Relationships:** None required.
- **Filter Direction:** One-way filtering from lookup tables to transaction tables.

</details>

---

## PART 3: Adding DAX Measures

### Tasks:
Try creating the following DAX measures before revealing the formulas:

- `Total Orders`
- `Total Pizzas Sold`
- `Total Revenue`
- `Average Order Value`
- `Most Popular Pizza Type`
- `Least Ordered Pizza Type`
- `Total Revenue by Category`
- `Cumulative Revenue`

<details>
  <summary>Show DAX Measures</summary>

- `Total Orders = COUNT(orders[order_id])`
- `Total Pizzas Sold = SUM(order_details[quantity])`
- `Total Revenue = SUMX(order_details, order_details[quantity] * pizzas[price])`
- `Average Order Value = [Total Revenue] / [Total Orders]`
- `Most Popular Pizza Type = TOPN(1, VALUES(order_details[pizza_id]), [Total Pizzas Sold])`
- `Least Ordered Pizza Type = TOPN(1, VALUES(order_details[pizza_id]), [Total Pizzas Sold], ASC)`
- `Total Revenue by Category = SUMX(order_details, order_details[quantity] * pizzas[price])`
- `Cumulative Revenue = CALCULATE([Total Revenue], FILTER(ALL(orders[date]), orders[date] <= MAX(orders[date])))`

</details>

---

## PART 4: Building the Report

### Tasks:
Before proceeding, answer these questions:

1. Which visualization would best show **Total Revenue by Pizza Category**?
2. How would you visualize **Most Popular Pizza Types**?
3. How would you implement **Drillthrough Filters** to analyze specific pizzas?
4. How would you add a **KPI card** to track total revenue?
5. How would you create a **date slicer** to filter orders by time period?

<details>
  <summary>Show Solutions</summary>

- **Total Revenue by Category:** Use a **Treemap** or **Bar Chart**.
- **Most Popular Pizza Types:** Use a **Top N filter** in a **Column Chart**.
- **Drillthrough Filters:** Set up a **Drillthrough page** using `pizza_id`.
- **KPI Card:** Use a **Card Visual** for `Total Revenue` with conditional formatting.
- **Date Slicer:** Use a **Date Range Slicer** on `orders[date]`.

</details>

---

### Final Steps:
- Save the Power BI report.
- Test interactions between visuals.
- Add tooltips, slicers, and formatting for a polished dashboard.

---
