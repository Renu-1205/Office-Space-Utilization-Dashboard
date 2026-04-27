// ============================================
// POWER BI MEASURES - OFFICE SPACE ANALYTICS
// Based on Python processing logic
// ============================================

// ---------- CORE KPI MEASURES ----------

// Average utilization percentage
Utilization % = AVERAGE(office_data[utilization_percentage])

// Occupancy ratio (actual / capacity)
Occupancy Ratio = DIVIDE(SUM(office_data[occupancy_count]), SUM(office_data[space_capacity]), 0)

// Cost per occupant (handles zero occupancy)
Cost per Occupant = 
VAR ValidSpaces = FILTER(office_data, office_data[occupancy_count] > 0)
RETURN DIVIDE(SUMX(ValidSpaces, office_data[cost_per_hour]), SUMX(ValidSpaces, office_data[occupancy_count]), BLANK())

// Space efficiency score (composite KPI)
Space Efficiency Score = DIVIDE([Utilization %], [Cost per Occupant], 0)

// Average meeting duration in hours
Avg Duration Hours = AVERAGE(office_data[duration_hours])

// ---------- TIME ANALYSIS ----------

// Utilization by start hour
Utilization by Hour = CALCULATE([Utilization %], VALUES(office_data[start_hour]))

// Utilization by day of week
Utilization by Weekday = CALCULATE([Utilization %], VALUES(office_data[day_of_week]))

// ---------- DATA QUALITY ----------

// Count of capacity violations
Capacity Violations = COUNTROWS(FILTER(office_data, office_data[occupancy_count] > office_data[space_capacity]))

// ---------- RANKING ----------

// Department rank by utilization
Department Rank = RANKX(ALL(office_data[department_name]), [Utilization %], , DESC, Dense)

// ---------- CLASSIFICATION ----------

// Underutilized flag (below 15%)
Is Underutilized = IF([Utilization %] < 0.15, "Underutilized", "Well Utilized")

// ---------- COST ANALYSIS ----------

// Total cost sum
Total Cost = SUM(office_data[cost_per_hour])

// Weighted average cost per hour
Avg Cost per Hour = AVERAGEX(office_data, office_data[cost_per_hour] / office_data[duration_hours])
