
# 📐 DAX Measures Documentation

## 1. Utilization Percentage (Baseline KPI)

Utilization % = AVERAGE(office_data[utilization_percentage])


## 2. Occupancy Ratio (Validated)

Occupancy Ratio = DIVIDE(SUM(office_data[occupancy_count]), SUM(office_data[space_capacity]), 0)


## 3. Cost per Occupant

Cost per Occupant = DIVIDE(SUM(office_data[cost_per_hour]), SUM(office_data[occupancy_count]), 0)


## 4. Space Efficiency Score

Space Efficiency = DIVIDE([Utilization %], [Cost per Occupant], 0)


## 5. Capacity Violation Check (Data Quality)

Capacity Violations = COUNTROWS(FILTER(office_data, office_data[occupancy_count] > office_data[space_capacity]))


## 6. Utilization by Hour

Utilization by Hour = CALCULATE([Utilization %], VALUES(office_data[start_hour]))


## 7. Department Ranking

Department Rank = RANKX(ALL(office_data[department_name]), [Utilization %], , DESC, Dense)


## 8. Underutilized Flag (Below 15%)
Is Underutilized = IF([Utilization %] < 15, "Underutilized", "Well Utilized")
**Note:** Replace `office_data` with your actual table name if different.
```

---

