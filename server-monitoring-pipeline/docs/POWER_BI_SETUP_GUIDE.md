# Power BI Dashboard Setup Guide
## Server Monitoring Analytics Dashboard

---

## STEP 5.1-5.3: Connect to Azure SQL Database

### Prerequisites
- Power BI Desktop installed ([Download](https://powerbi.microsoft.com/en-us/desktop/))
- Azure SQL credentials ready

### Connection Steps

1. **Open Power BI Desktop**
2. Click `Get Data` → `More...` → Search "Azure SQL Database"
3. Enter connection details:
   ```
   Server: praveensql.database.windows.net
   Database: server-monitoring-db
   ```
4. Click `OK`
5. When prompted, select **Database** authentication:
   ```
   Username: azureadmin
   Password: Praveen@9390
   ```
6. Select `server_metrics` table and click `Load`

---

## STEP 5.4: Page 1 - System Overview

### Create KPI Cards with DAX Formulas

#### KPI 1: Total Servers
- **Visual Type:** Card
- **Field:** `DISTINCTCOUNT(server_metrics[server_id])`
- Or use simple field count with distinct count aggregation

**Steps:**
1. Visualizations → Card
2. Drag `server_id` to Fields
3. Change aggregation to `Count (Distinct)`
4. Add title: "Total Servers"

#### KPI 2: Average CPU Usage
- **Visual Type:** Card
- **DAX Formula:**
```dax
Average CPU = AVERAGE(server_metrics[cpu_usage])
```

**Steps:**
1. Go to Data → New Measure
2. Paste the formula above
3. Add Card visual
4. Drag `Average CPU` to Fields
5. Format to show 2 decimal places with % symbol
6. Title: "Average CPU Usage"

#### KPI 3: Average Memory Usage
- **Visual Type:** Card
- **DAX Formula:**
```dax
Average Memory = AVERAGE(server_metrics[memory_usage])
```

**Steps:**
1. Create new measure (same as above)
2. Paste formula
3. Format: 2 decimal places with % symbol
4. Title: "Average Memory Usage"

#### KPI 4: Total Alerts Count
- **Visual Type:** Card
- **DAX Formula:**
```dax
Total Alerts = COUNTA(server_metrics[alert])
```

**Steps:**
1. Create new measure
2. Paste formula
3. Format as number (no decimals)
4. Title: "Total Alerts"

#### KPI 5: Critical Alerts Count
- **Visual Type:** Card with warning color
- **DAX Formula:**
```dax
Critical Alerts = COUNTIF(server_metrics[alert], "Critical*")
```

Or use:
```dax
Critical Alerts = CALCULATE(
    COUNTA(server_metrics[alert]),
    server_metrics[alert] = "CPU Critical"
) + CALCULATE(
    COUNTA(server_metrics[alert]),
    server_metrics[alert] = "Memory Critical"
)
```

**Layout:**
- Arrange KPI cards in a row at top of Page 1
- Use Card formatting with appropriate backgrounds

---

## STEP 5.5: CPU Trend Chart (Line Chart)

**Visual Type:** Line Chart

**Configuration:**
- **X-axis:** `timestamp` (Date hierarchy: Date)
- **Y-axis:** `cpu_usage` (Aggregate: Average)
- **Legend:** `server_id`

**Steps:**
1. Visualizations → Line Chart
2. Drag `timestamp` to X-axis
3. Drag `cpu_usage` to Y-axis (ensure it's set to Average)
4. Drag `server_id` to Legend
5. Title: "CPU Usage Trend Over Time"
6. Add data labels: Right-click visual → Turn ON data labels

**Formatting:**
- Line thickness: 2-3px
- Show legend: Right (or Top)
- Y-axis title: "CPU Usage (%)"

---

## STEP 5.6: Memory Monitoring Chart (Area Chart)

**Visual Type:** Stacked Area Chart

**Configuration:**
- **X-axis:** `timestamp` (Date hierarchy: Date)
- **Y-axis:** `memory_usage` (Aggregate: Average)
- **Legend:** `server_id`

**Steps:**
1. Visualizations → Area Chart (Stacked)
2. Drag `timestamp` to X-axis
3. Drag `memory_usage` to Y-axis
4. Drag `server_id` to Legend
5. Title: "Memory Usage Over Time"
6. Enable transparency for better visibility

---

## STEP 5.7: Server Performance Comparison (Bar Chart)

**Visual Type:** Column/Bar Chart

**Configuration:**
- **X-axis:** `server_id`
- **Y-axis:** `cpu_usage` (Aggregate: Average)

**Steps:**
1. Visualizations → Column Chart
2. Drag `server_id` to X-axis
3. Drag `cpu_usage` to Y-axis
4. Sort by Y-axis descending
5. Title: "Average CPU Usage by Server"
6. Color bars based on value (green < 50%, yellow 50-75%, red > 75%)

**Add data labels:** Values on bars

---

## STEP 5.8: Alert Monitoring Table with Conditional Formatting

**Visual Type:** Table

**Columns:**
1. `timestamp`
2. `server_id`
3. `cpu_usage`
4. `memory_usage`
5. `disk_usage`
6. `alert`

**Steps:**
1. Visualizations → Table
2. Drag all above fields to Values
3. Right-click on table → Conditional Formatting

**Conditional Formatting Rules:**

For `cpu_usage` column:
- Rules:
  - **Critical** (≥ 90): Red background
  - **Warning** (70-89): Yellow background
  - **Normal** (< 70): Green background

For `memory_usage` column:
- Rules:
  - **Critical** (≥ 85): Red background
  - **Warning** (65-84): Yellow background
  - **Normal** (< 65): Green background

For `alert` column:
- Rules:
  - **Contains "Critical"**: Red background + White text
  - **Contains "Warning"**: Yellow background + Black text
  - **Contains "Normal"**: Green background + Dark text

**Steps for Conditional Formatting:**
1. Select column header → Conditional Formatting → Background Color
2. Choose "Value" rule
3. Set conditions based on thresholds
4. Click OK

---

## STEP 5.9: Location-based Map Visual (Advanced)

**Visual Type:** Map (Filled Map or Bubble Map)

**Configuration:**
- **Location:** `location`
- **Size:** `cpu_usage`
- **Color saturation:** `memory_usage`

**Steps:**
1. Visualizations → Map (Filled Map) or Bubble Map
2. Drag `location` to Location field
3. Drag `cpu_usage` to Size (or drag to saturation)
4. Drag `memory_usage` to Color saturation
5. Title: "Server Load by Geographic Location"

**Formatting:**
- Show data labels
- Color scheme: Green (low) → Red (high)

---

## STEP 5.10: Add Interactive Filters (Slicers)

**Create 4 Slicer Filters:**

### Slicer 1: Server ID
1. Visualizations → Slicer
2. Drag `server_id` to Field
3. Orientation: Horizontal (or Vertical)
4. Title: "Select Server"
5. Position: Top-left

### Slicer 2: Location
1. New Slicer
2. Drag `location` to Field
3. Orientation: Horizontal
4. Title: "Select Location"
5. Position: Top-center

### Slicer 3: OS Type
1. New Slicer
2. Drag `os_type` to Field
3. Orientation: Horizontal
4. Title: "Select OS Type"
5. Position: Top-right

### Slicer 4: Date Range
1. New Slicer
2. Drag `timestamp` to Field
3. Type: Between (Date)
4. Title: "Date Range"
5. Position: Bottom of page

**Make Filters Editable:**
- Right-click each slicer → Slicer Settings
- Enable "Multi-select with Ctrl"

---

## STEP 5.11: Dashboard Structure (3 Pages)

### **PAGE 1: OVERVIEW**
- **Top:** 5 KPI Cards (Total Servers, Avg CPU, Avg Memory, Total Alerts, Critical Alerts)
- **Left (40%):** CPU Trend Chart (Line)
- **Right (60%):** Memory Trend Chart (Area)
- **Bottom:** Date range slicer

### **PAGE 2: PERFORMANCE MONITORING**
- **Slicers at top:** Server, Location, OS Type
- **Left:** CPU by Server (Bar Chart)
- **Right:** Memory by Server (Column Chart)
- **Bottom:** Server Performance Comparison
- **Feature:** Drill-down capability by server_id

### **PAGE 3: ALERTS & ANOMALIES**
- **Top Slicer:** Location and Alert Type filters
- **Main:** Alert Monitoring Table (with conditional formatting)
- **Right:** Critical Server Detection (KPI)
- **Bottom:** Alert trend over time
- **Red highlighting:** For critical alerts

---

## Advanced DAX Formulas (Optional)

### Formula 1: Alert Severity Indicator
```dax
Alert Severity = 
IF(HASVALUE(server_metrics[alert]),
    IF(SEARCH("Critical", server_metrics[alert], 1, 0) > 0, 3,
    IF(SEARCH("Warning", server_metrics[alert], 1, 0) > 0, 2, 1)),
    0)
```

### Formula 2: CPU Health Score
```dax
CPU Health Score = 
IF(AVERAGE(server_metrics[cpu_usage]) < 50, "Good",
IF(AVERAGE(server_metrics[cpu_usage]) < 75, "Fair", "Poor"))
```

### Formula 3: Memory Health Score
```dax
Memory Health Score = 
IF(AVERAGE(server_metrics[memory_usage]) < 65, "Good",
IF(AVERAGE(server_metrics[memory_usage]) < 85, "Fair", "Poor"))
```

### Formula 4: Server Status (Real-time)
```dax
Server Status = 
IF(MAX(server_metrics[cpu_usage]) > 90 || MAX(server_metrics[memory_usage]) > 85, 
    "🔴 CRITICAL", 
IF(MAX(server_metrics[cpu_usage]) > 70 || MAX(server_metrics[memory_usage]) > 65, 
    "🟡 WARNING", 
    "🟢 HEALTHY"))
```

---

## Dashboard Formatting Best Practices

### Color Scheme
- **Critical/Errors:** Red (#E74C3C)
- **Warnings:** Orange/Yellow (#F39C12)
- **Normal/Good:** Green (#27AE60)
- **Neutral:** Gray (#BDC3C7)
- **Background:** Light Gray (#ECF0F1)

### Typography
- **Title font:** 18pt, Bold, Dark Gray
- **Card values:** 24pt, Bold
- **Axis labels:** 10pt, Regular
- **Legend:** 9pt, Regular

### Layout Principles
- **Top to bottom:** Critical info above, details below
- **Left to right:** Most important visuals on left
- **White space:** 2-3% gap between visuals
- **Consistent sizing:** Use 60x40 grid layout

---

## STEP 5.13: Save Dashboard

1. **File → Save As**
2. **Location:** `C:\Users\91939\Desktop\Neostats\server-monitoring-pipeline\dashboard\`
3. **Filename:** `server_monitoring_dashboard.pbix`
4. **Format:** Power BI Desktop file (.pbix)

---

## Publishing to Power BI Service (Optional)

For sharing with team:

1. File → Publish
2. Select workspace
3. Configure refresh schedule (daily/hourly)
4. Share link with stakeholders

---

## Verification Checklist

- [ ] All 5 KPI cards display correct values
- [ ] CPU Trend chart shows 200 data points
- [ ] Memory chart shows trends over time
- [ ] Alert table displays with color coding
- [ ] Location map renders correctly
- [ ] All 4 slicers filter data independently
- [ ] Cross-filtering works between visuals
- [ ] Dashboard loads in < 3 seconds
- [ ] All titles are clear and professional
- [ ] File saved as .pbix format

---

## Troubleshooting

**Issue:** "Missing data in visuals"
- **Solution:** Verify data refresh in Power BI → Refresh now

**Issue:** "Slicers not filtering other visuals"
- **Solution:** Right-click slicer → Edit interactions → Check all visuals

**Issue:** "Conditional formatting not showing"
- **Solution:** Ensure column has numeric data type, check color rules

**Issue:** "Map visual shows locations as dots"
- **Solution:** Ensure location data is geocoded (try using standard country/city names)

---

## Next Steps

1. ✅ Save dashboard as .pbix file
2. ✅ Test all interactive features
3. ✅ Share with stakeholders
4. ✅ Set up automatic daily refresh
5. ✅ Configure alerts for critical events
