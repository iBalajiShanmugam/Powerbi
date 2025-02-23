# Super Store Data Analysis Project

## PART 1: Connecting & Shaping the Data

Open a new Power BI Desktop file and complete the following tasks:

### 1) Update Power BI Options & Settings:
- Disable "Autodetect new relationships after data is loaded" in the **Data Load** tab.
- Ensure that the **Locale for Import** is set to "English (United States)" under **Regional Settings**.

### 2) Connect to the `SuperStore_Customers.csv` file:
- Name the table **Customers** and confirm headers are properly promoted.
- Verify data types:
  - `customer_id` should be **whole numbers**.
  - `customer_acct_num` and `customer_postal_code` should be **text**.
- Add a calculated column `full_name`, merging `first_name` and `last_name` with a space separator.
- Create a column `birth_year` extracting the year from `birthdate` and formatting it as **text**.
- Add a conditional column `has_children`, setting it to **"N"** if `total_children` = 0, otherwise **"Y"**.

### 3) Connect to the `SuperStore_Products.csv` file:
- Name the table **Products** and confirm headers are promoted.
- Validate data types:
  - `product_id` should be **whole numbers**.
  - `product_sku` should be **text**.
  - `product_retail_price` and `product_cost` should be **decimal numbers**.
- Use the statistics tools to determine:
  - Number of **unique brands**.
  - Number of **distinct product names**.
  - Expected count: **111 brands, 1,560 product names**.
- Add a calculated column `discount_price`, equal to **90%** of `product_retail_price`, format as **fixed decimal** and round to **2 decimal places**.
- Group `product_brand` and calculate the **average retail price** by brand (`Avg Retail Price`).
- Expected values:
  - Washington products: **$2.18**
  - Green Ribbon products: **$2.21**
- Undo the last grouping step to return to the original table.
- Replace `null` values with **0** in both `recyclable` and `low-fat` columns.

### 4) Connect to the `SuperStore_Stores.csv` file:
- Name the table **Stores** and confirm headers are promoted.
- Ensure correct data types:
  - `store_id` and `region_id` should be **whole numbers**.
- Create a calculated column `full_address` by merging `store_city`, `store_state`, and `store_country` (separated by a comma and space).
- Extract the `area_code` by pulling the characters before the `-` in the `store_phone` field.

### 5) Connect to the `SuperStore_Regions.csv` file:
- Name the table **Regions** and confirm headers are promoted.
- Verify `region_id` is a **whole number**.

### 6) Connect to the `SuperStore_Calendar.csv` file:
- Name the table **Calendar** and confirm headers are promoted.
- Add the following calculated columns:
  - **Start of Week** (beginning Sunday).
  - **Day Name**.
  - **Start of Month**.
  - **Month Name**.
  - **Quarter of Year**.
  - **Year**.

### 7) Connect to the `SuperStore_Returns.csv` file:
- Name the table **Return_Data** and confirm headers are promoted.
- Verify all ID columns and `quantity` are **whole numbers**.

### 8) Load `SuperStore_Transactions_1997.csv` and `SuperStore_Transactions_1998.csv`:
- Place both files in a new folder named **SuperStore Transactions**.
- Connect to the folder and **Edit** (instead of Combine and Edit).
- Expand the `Content` column and remove the `Source.Name` column.
- Name the table **Transaction_Data**.
- Verify all ID columns and `quantity` are **whole numbers**.
- Spot check: `transaction_date` should range from **1/1/1997** to **12/30/1998**.

### 9) Exclude Non-Essential Tables from Auto Refresh:
- Disable "Include in Report Refresh" for all tables **except** `Transaction_Data` and `Return_Data`.
- Click **Close & Apply**.
- Ensure all **7 tables** are accessible in **Relationships View** and **Data View**.

### 10) Save the report as **SuperStore_Report.pbix**.

---

## PART 2: Creating the Data Model

### 1) Organize Tables in Model View:
- Place lookup tables **above** data tables.
- Establish relationships:
  - Connect `Transaction_Data` to `Customers`, `Products`, and `Stores`.
  - Connect `Transaction_Data` to `Calendar` (both date fields) with an **inactive** `stock_date` relationship.
  - Connect `Return_Data` to `Products`, `Calendar`, and `Stores`.
  - Connect `Stores` to `Regions` using a **snowflake schema**.

### 2) Validate Relationships:
- Ensure all relationships are **one-to-many**, with **primary keys (1)** on the lookup side and **foreign keys (*)** on the data side.
- Confirm **one-way filtering** (no bidirectional relationships).
- Verify that filters flow **downstream** from lookup tables to data tables.

### 3) Hide Unnecessary Columns:
- Hide all **foreign keys** in data tables.
- Hide `region_id` in the `Stores` table.

### 4) Format Data Fields:
- Set all date fields to **M/d/yyyy** format.
- Format `product_retail_price`, `product_cost`, and `discount_price` as **currency ($ English)**.
- Categorize location fields:
  - In `Customers`: Set `customer_city` as **City**, `customer_postal_code` as **Postal Code**, `customer_country` as **Country/Region**.
  - In `Stores`: Set `store_city` as **City**, `store_state` as **State/Province**, `store_country` as **Country/Region**, and `full_address` as **Address**.

### 5) Save the report.


## PART 3: Adding DAX Measures

### 1) Create Calculated Columns:
- **In the Calendar table:**
  - `Weekend`: "Y" if the day is Saturday or Sunday, otherwise "N".
  - `End of Month`: Returns the last date of the current month for each row.
- **In the Customers table:**
  - `Current Age`: Calculates age using `birthdate` and `TODAY()`.
  - `Priority`: "High" if customer owns a home and has a Golden membership, otherwise "Standard".
  - `Short_Country`: First three letters of `customer_country` in uppercase.
  - `House Number`: Extracts house number from `customer_address` (using `SEARCH`).
- **In the Products table:**
  - `Price_Tier`: "High" if price > $3, "Mid" if > $1, "Low" otherwise.
- **In the Stores table:**
  - `Years_Since_Remodel`: Years since the last remodel using `TODAY()`.

### 2) Create Measures:

Before revealing the full DAX formulas, try creating the following measures on your own:

- `Quantity Sold`
- `Quantity Returned`
- `Total Transactions`
- `Total Returns`
- `Return Rate`
- `Weekend Transactions`
- `% Weekend Transactions`
- `All Transactions` & `All Returns`
- `Total Revenue`
- `Total Cost`
- `Total Profit`
- `Profit Margin`
- `Unique Products`
- `YTD Revenue`
- `60-Day Revenue`
- Last Month Metrics: `Last Month Transactions`, `Last Month Revenue`, `Last Month Profit`, `Last Month Returns`
- `Revenue Target`

Click the button below to reveal the DAX formulas for each measure.

<details>
  <summary>Show DAX Measures</summary>

- `Quantity Sold` = SUM of quantity in `Transaction_Data`.
- `Quantity Returned` = SUM of quantity in `Return_Data`.
- `Total Transactions` = COUNT of rows in `Transaction_Data`.
- `Total Returns` = COUNT of rows in `Return_Data`.
- `Return Rate` = (`Quantity Returned` / `Quantity Sold`) formatted as %.
- `Weekend Transactions` = SUM of transactions occurring on weekends.
- `% Weekend Transactions` = (`Weekend Transactions` / `Total Transactions`) formatted as %.
- `All Transactions` & `All Returns`: Compute grand total (ignoring filters).
- `Total Revenue` = SUMX(`Transaction_Data`, `quantity * product_retail_price`).
- `Total Cost` = SUMX(`Transaction_Data`, `quantity * product_cost`).
- `Total Profit` = `Total Revenue` - `Total Cost`.
- `Profit Margin` = (`Total Profit` / `Total Revenue`) formatted as %.
- `Unique Products` = DISTINCTCOUNT(`product_name`).
- `YTD Revenue` = TOTALYTD(`Total Revenue`, `Calendar[date]`).
- `60-Day Revenue` = Rolling 60-day sum of revenue.
- Last Month metrics: `Last Month Transactions`, `Last Month Revenue`, `Last Month Profit`, `Last Month Returns`.
- `Revenue Target` = `Last Month Revenue * 1.05` (5% increase).

</details>

### 3) Save the report.

---

## PART 4: Building the Report

### 1) Rename tab "Topline Performance" and insert the **Super Store** logo.

### 2) Matrix Visual:
- Display **Total Transactions, Total Profit, Profit Margin, and Return Rate** by `Product_Brand`.
- Apply **conditional formatting**:
  - Data bars on **Total Transactions**.
  - **Green-to-White** scale for Profit Margin.
  - **Red-to-White** scale for Return Rate.
- Filter: Show **Top 30 brands**, sort by Total Transactions.

### 3) KPI Cards:
- **Total Transactions** with `Start of Month` as the trend axis, `Last Month Transactions` as the goal.
- Duplicate for **Total Profit** (vs. `Last Month Profit`) and **Total Returns** (vs. `Last Month Returns`).
- Change color for **Returns KPI** (Low is Good).

### 4) Map Visual:
- Show **Total Transactions** by `store_city`.
- Add a **slicer for store country** (enable "Show Select All").
- Change **orientation** to horizontal.

### 5) Treemap:
- Show **Total Transactions** by `store_country`.
- Enable drill-down to `store_state` and `store_city`.

### 6) Column Chart:
- Show **Total Revenue by Week**.
- Apply report filter: **Only show 1998 data**.
- Title: "Weekly Revenue Trending".

### 7) Gauge Chart:
- Show **Total Revenue vs. Revenue Target**.
- Apply Top N filter: Show latest **Start of Month**.
- Remove data labels, title: "Revenue vs. Target".

### 8) Modify Visual Interactions:
- **Disable Treemap filtering** when selecting from the Matrix.

### 9) Bookmarks & Notes:
- Select **USA**, drill down to **Portland** in Treemap.
- Add **Bookmark**: "Portland 1000 Sales".
- Create a new page **Notes**, add a textbox: "Portland hits 1,000 sales in December".
- Insert a button linking to the **bookmark**.
- Add 2-3 more insights and create additional **bookmarks & notes**.

### 10) Experiment & Customize:
- Add new visuals, reports, and bookmarks to further explore the dataset.

### 11) Save the final report!

