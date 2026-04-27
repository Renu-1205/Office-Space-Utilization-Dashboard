/* ============================================
   OFFICE SPACE ANALYTICS - DAX MEASURES
   Based on processing logic from Python script
   ============================================ */

// 1. AVERAGE UTILIZATION PERCENTAGE
// Matches: df['space_efficiency_score'] = df['utilization_percentage'] / df['cost_per_hour']
Avg Utilization % = AVERAGE(office_data[utilization_percentage])

// 2. OCCUPANCY RATIO (Validated with capacity)
// Matches: df['occupancy_ratio'] = df['occupancy_count'] / df['space_capacity']
Occupancy Ratio = 
DIVIDE(
    SUM(office_data[occupancy_count]), 
    SUM(office_data[space_capacity]), 
    0
)

// 3. COST PER OCCUPANT (Handles zero occupancy)
// Matches: np.where(occupancy_count > 0, cost_per_hour / occupancy_count, np.nan)
Cost per Occupant = 
VAR ValidOccupancy = 
    FILTER(
        office_data, 
        office_data[occupancy_count] > 0
    )
RETURN
    DIVIDE(
        SUMX(ValidOccupancy, office_data[cost_per_hour]),
        SUMX(ValidOccupancy, office_data[occupancy_count]),
        BLANK()
    )

// 4. SPACE EFFICIENCY SCORE (Composite KPI)
// Matches: df['space_efficiency_score'] = df['utilization_percentage'] / df['cost_per_hour']
Space Efficiency Score = 
DIVIDE(
    [Avg Utilization %],
    [Cost per Occupant],
    0
)

// 5. DURATION IN HOURS (Average)
// Matches: df['duration_hours'] = (end_time - start_time).total_seconds() / 3600
Avg Duration Hours = 
AVERAGE(office_data[duration_hours])

// 6. UTILIZATION BY HOUR OF DAY
// Matches: df['start_hour'] = df['start_time'].dt.hour
Utilization by Hour = 
CALCULATE(
    [Avg Utilization %],
    VALUES(office_data[start_hour])
)

// 7. UTILIZATION BY DAY OF WEEK
// Matches: df['day_of_week'] = df['date'].dt.day_name()
Utilization by Weekday = 
CALCULATE(
    [Avg Utilization %],
    VALUES(office_data[day_of_week])
)

// 8. CAPACITY VIOLATION CHECK (Data Quality)
// Checks if occupancy exceeds capacity
Capacity Violations Count = 
COUNTROWS(
    FILTER(
        office_data, 
        office_data[occupancy_count] > office_data[space_capacity]
    )
)

// 9. DEPARTMENT RANKING by Utilization
Department Rank = 
RANKX(
    ALL(office_data[department_name]),
    [Avg Utilization %],
    ,
    DESC,
    Dense
)

// 10. UNDERUTILIZED FLAG (Below 15%)
// Threshold-based classification
Is Underutilized = 
IF(
    [Avg Utilization %] < 0.15,  -- 15% threshold
    "Underutilized",
    "Well Utilized"
)

// 11. TOTAL COST ANALYSIS
Total Cost = SUM(office_data[cost_per_hour])

// 12. COST PER HOUR (Weighted Average)
Avg Cost per Hour = 
AVERAGEX(
    office_data,
    office_data[cost_per_hour] / office_data[duration_hours]
)

