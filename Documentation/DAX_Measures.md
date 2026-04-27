Utilization % = AVERAGE(office_data[utilization_percentage])

Occupancy Ratio = DIVIDE(SUM(office_data[occupancy_count]), SUM(office_data[space_capacity]), 0)

Cost per Occupant = 
VAR ValidSpaces = FILTER(office_data, office_data[occupancy_count] > 0)
RETURN DIVIDE(SUMX(ValidSpaces, office_data[cost_per_hour]), SUMX(ValidSpaces, office_data[occupancy_count]), BLANK())

Space Efficiency Score = DIVIDE([Utilization %], [Cost per Occupant], 0)

Avg Duration Hours = AVERAGE(office_data[duration_hours])

Utilization by Hour = CALCULATE([Utilization %], VALUES(office_data[start_hour]))

Utilization by Weekday = CALCULATE([Utilization %], VALUES(office_data[day_of_week]))

Capacity Violations = COUNTROWS(FILTER(office_data, office_data[occupancy_count] > office_data[space_capacity]))

Department Rank = RANKX(ALL(office_data[department_name]), [Utilization %], , DESC, Dense)

Is Underutilized = IF([Utilization %] < 0.15, "Underutilized", "Well Utilized")

Total Cost = SUM(office_data[cost_per_hour])

Avg Cost per Hour = AVERAGEX(office_data, office_data[cost_per_hour] / office_data[duration_hours])
