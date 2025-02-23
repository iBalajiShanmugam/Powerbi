# Space Missions Data Analysis Project

## **PART 1: Connecting & Shaping Data**

### 📌 **Tasks**
Before proceeding, try answering the following:

✅ How would you clean and transform the `Date` and `Time` fields?  
✅ How would you handle missing values in `Time` and `Price`?  
✅ How would you extract **Year, Month, and Quarter** from `Date`?  
✅ How would you classify missions as **Low Cost, Medium Cost, or High Cost**?  
✅ How would you flag whether a mission was **successful** or **failed**?  

<details>
  <summary>💡 Show Solutions</summary>

- **Convert Date & Time:**  
  `Launch Date = DATEVALUE(space_missions[Date])`  
  `Launch Time = TIMEVALUE(space_missions[Time])`  
- **Handle Missing Prices:** Replace `NULL` values with `"Unknown"` or the **average cost per company**.  
- **Extract Time-Based Data:**  
  `Launch Year = YEAR(space_missions[Date])`  
  `Launch Month = FORMAT(space_missions[Date], "MMMM")`  
  `Launch Quarter = "Q" & FORMAT(space_missions[Date], "Q")`  
- **Classify Mission Costs:**  
  ```DAX
  Cost Category = 
  IF(space_missions[Price] < 50, "Low Cost", 
    IF(space_missions[Price] < 200, "Medium Cost", "High Cost"))
  ```
- **Flag Mission Success:**  
  ```DAX
  Mission Success = IF(space_missions[MissionStatus] = "Success", "Yes", "No")
  ```
</details>  

---

## **PART 2: Creating a Calendar Table**

### 📌 **Tasks**
✅ How would you generate a **Calendar Table** in Power BI?  
✅ How would you establish a **relationship** between `space_missions[Date]` and the `Calendar[Date]`?  
✅ How would you create calculated columns for **Year, Month, and Quarter** in the Calendar Table?  
✅ How would you calculate **missions per quarter using the Calendar Table**?  

<details>
  <summary>💡 Show Solutions</summary>

- **Create Calendar Table:**  
  ```DAX
  Calendar = ADDCOLUMNS(
    CALENDAR(MIN(space_missions[Date]), MAX(space_missions[Date])),
    "Year", YEAR([Date]),
    "Month", FORMAT([Date], "MMMM"),
    "Quarter", "Q" & FORMAT([Date], "Q")
  )
  ```
- **Create Relationship:**  
  Link `Calendar[Date]` → `space_missions[Date]` in Model View.  
- **Missions per Quarter:**  
  ```DAX
  Missions Per Quarter = COUNT(space_missions[Mission])
  ```
</details>  

---

## **PART 3: Adding DAX Measures**

### 📌 **Key Measures**
Try creating the following before revealing the formulas:

✅ `Total Missions`  
✅ `Successful Missions`  
✅ `Failure Rate`  
✅ `Missions Per Year`  
✅ `Most Active Country for Launches`  
✅ `Company with Highest Success Rate`  
✅ `Total Cost of All Missions`  

<details>
  <summary>💡 Show DAX Measures</summary>

```DAX
Total Missions = COUNT(space_missions[Mission])  
Successful Missions = CALCULATE(COUNT(space_missions[Mission]), space_missions[MissionStatus] = "Success")  
Failure Rate = 1 - ([Successful Missions] / [Total Missions])  
Missions Per Year = COUNTROWS(VALUES(space_missions[Launch Year]))  
Most Active Country = TOPN(1, VALUES(space_missions[Location]), COUNT(space_missions[Mission]))  
Highest Success Rate = TOPN(1, VALUES(space_missions[Company]), [Successful Missions] / [Total Missions])  
Total Cost = SUM(space_missions[Price])  
```
</details>  

---

## **PART 4: Building the Report**

### 📊 **Dashboard Layout**

#### **🔹 KPI Section**
📌 **Total Missions:** 🚀 4,630  
📌 **Success Rate:** 🎉 89.89%  
📌 **Failure Rate:** 😞 10.11%  
📌 **Total Cost of Missions** (if available)  

#### **🔹 Visualizations**
📊 **Missions Over Time** → Line Chart  
📊 **Success vs. Failure** → Column Chart  
📊 **Missions by Country** → Map Visual  
📊 **Mission Costs by Category** → Pie Chart  
📊 **Top Companies by Success Rate** → Bar Chart  

---

### 🚀 **Advanced Features**

#### **🔹 Conditional Formatting**
🎨 **Success Rate** → White-to-Green Scale  
🎨 **Failure Rate** → White-to-Red Scale  
🎨 **Data Bars for Mission Count**  

#### **🔹 Drillthrough & Tooltips**
📌 Clicking on a **Company Name** filters **Missions by Company**.  
📌 Hovering over a **Mission** shows **Rocket, Location, and Cost Details**.  

---

### ✅ **Final Steps**
🎯 Implement these features in Power BI.  
🎯 Optimize **visual interactions**.  
🎯 Capture a **Final Report Screenshot**.  

---
