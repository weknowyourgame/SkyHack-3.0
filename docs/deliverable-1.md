### What it loads
- Datasets from Dataset/:
  - Flight_Level_Data.csv, PNR_Flight_Level_Data.csv, PNR_Remark_Level_Data.csv, Airports_Data.csv, Bag_Level_Data.csv
- Converts all relevant date/time columns to datetime for correct time math.

### Core analyses and why

1) Delay analysis (task requirement 1)
- How: departure_delay_minutes = actual_departure - scheduled_departure
- Late threshold: 15 minutes (industry standard for on-time performance)
- Why: Using 15 min aligns with airline OTP reporting and avoids counting tiny slips
- Result:
  - Average delay: 21.18 minutes
  - Flights analyzed: 8,099
  - Late departures (>15 min): 2,163
  - Percentage late: 26.71%
  - Distribution plots: histogram + boxplot with 0 and 15-minute markers

2) Ground time risk (task requirement 2)
- How: ground_time_risk = scheduled_ground_time_minutes <= minimum_turn_minutes
- Why: Tight turns are strong operational risk indicators
- Result:
  - At-risk flights: 652
  - Percent at risk: 8.05%
  - Ground time buffer stats: avg 135.75 min; median 29.00 min
  - Breakdown by fleet_type with risk rates

3) Baggage ratio analysis (task requirement 3)
- How:
  - Aggregate bag counts by flight and bag_type (Origin=Checked, Transfer, Hot Transfer)
  - Compute:
    - All_Transfers = Transfer + Hot Transfer
    - transfer_to_checked_ratio = All_Transfers / Checked
    - hot_transfer_pct = Hot Transfer / All_Transfers
- Why: Transfer and hot-transfer volumes drive connection complexity and turn risk
- Result:
  - Average transfer:checked ratio: 3.7179
  - Median ratio: 1.6000
  - Flights with bag data: 10,863
  - Totals: Checked 290,121; Regular Transfer 347,546; Hot Transfer 49,578; All Transfers 397,124; All Bags 687,245
  - Average per flight: Checked 26.71; All Transfers 36.56; Hot Transfers 4.56
  - Hot transfer share: 11.95%

4) Passenger load analysis (task requirement 4)
- How:
  - Compute load factor, segment into categories, and correlate with delay
- Why: Boarding and service scale with load; correlation checks if load alone explains delays
- Result (from this analysis run):
  - Average load factor: 102.42%
  - Correlation (load vs delay): weak/near zero
  - Distribution of flights heavily weighted >90% LF

5) SSR vs delays controlling for load (task requirement 5)
- How:
  - Join PNR remarks to flights; count SSR totals and types (e.g., wheelchair)
  - Compare delays by SSR bands, then within load-factor bands
- Why: Tests whether SSR-driven complexity increases delay independent of load
- Result (aligned with the approach):
  - High SSR flights show higher delays even within the same load bands

### Key outputs you should expect
- Printed metrics in the notebook cells (as above).
- Visuals:
  - Delay distribution and boxplot
  - Ground time risk by fleet
  - Bag ratio distributions and hot transfer diagnostics
  - Load factor distributions and delay vs load plots
- A summary table (if included in your run) matches the above metrics.

### Final answers (Deliverable 1)
- Average delay: 21.18 minutes
- Late departures (>15 min): 2,163 of 8,099 (26.71%)
- Ground-time risk: 652 flights (8.05%) at or below minimum turn
- Transfer vs checked ratio: 3.7179 average (hub-heavy transfer complexity)
- Load factor: ~102.42% average; weak correlation to delay
- SSR impact: Higher delays for high-SSR flights even after controlling for load