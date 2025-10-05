### What this notebook does
- Builds a flight-level “operational difficulty” score by combining flight schedule, passenger, baggage, and special service data.
- Normalizes features by day, computes a weighted difficulty score, ranks flights daily, classifies into Easy/Medium/Difficult, analyzes drivers, and exports results and visuals.

### Data inputs
- Dataset/Flight_Level_Data.csv (8,099 rows)
- Dataset/PNR_Flight_Level_Data.csv (687,878 rows)
- Dataset/PNR_Remark_Level_Data.csv (51,698 rows)
- Dataset/Airports_Data.csv (5,612 rows)
- Dataset/Bag_Level_Data.csv (687,245 rows)

### How it works (step-by-step)
1. Setup
   - Imports pandas/numpy/plotting libs; sets display/style; suppresses warnings.

2. Load data
   - Reads the five CSVs; prints shapes and samples to validate.

3. Preprocess
   - Converts datetime columns to proper types across flights, PNR, and bag data.
   - Creates flight_id as the unique key: company_id_flight_number_date_origin_dest.

4. Flight-level core metrics
   - Computes departure_delay_minutes and is_delayed.
   - Derives scheduled flight duration → haul_type (Short/Medium/Long) and haul_complexity_score.
   - Defines fleet_size_category from total_seats → fleet_complexity_score.
   - Encodes carrier (Express/Mainline) → carrier_complexity_score.
   - Extracts departure_hour → time_of_day (Morning_Peak/Midday/Evening_Peak/Off_Peak) → time_complexity_score.
   - Merges airports_data for arrival iso_country_code → is_international.
   - Computes ground time pressure and flags:
     - ground_time_pressure = scheduled_ground_time_minutes / minimum_turn_minutes
     - is_tight_turnaround (<= 110% of minimum)
     - is_very_tight_turnaround (<= minimum)

5. Bag-level aggregation (per flight_id)
   - Total bags; unstack counts by bag_type (Origin→checked_bags, Transfer→transfer_bags, Hot Transfer).
   - Ratios: transfer_ratio = transfer_bags / checked_bags (fallbacks when zero); hot_transfer_ratio.
   - Note: hot transfers are proxied; exact connection-time logic is marked for future enhancement.

6. PNR-level aggregation (per flight_id)
   - Sums: total_passengers, total_lap_children, children, basic economy, stroller users.
   - SSR: joins PNR remarks to PNR flights to get flight_id; counts total SSR and wheelchair SSR.

7. Build master dataset
   - Left-joins bag, PNR, and SSR features to flights.
   - Derived ratios:
     - load_factor = total_passengers / total_seats (capped at 1.0)
     - bags_per_passenger
     - children_ratio
     - special_service_ratio
     - wheelchair_ratio
   - Completes missing numeric features with 0 where appropriate; validates missingness.

8. Difficulty scoring (why and how)
   - Normalizes selected features by day (min-max within each date) so difficulty is relative to the day’s operating context. This prevents a day with generally “easy” flights from getting inflated scores.
   - Weighted sum over normalized features using FEATURE_WEIGHTS:
     - Ground time pressure (negative weight on normalized pressure so lower buffer is worse), tight-turn flags, load factor, SSR ratios, wheelchair/children ratios, baggage complexity, fleet/time/haul scores, international, carrier type, stroller users.
     - Weights reflect domain intuition of operational load (passenger service/composition, turnaround constraints, baggage mix, and flight characteristics).
   - Scales the aggregate score to 0–100 for interpretability.

9. Daily ranking and classification
   - Ranks flights by difficulty_score within each day and computes daily_percentile.
   - Labels: top 25% = Difficult, bottom 40% = Easy, rest = Medium.

10. Analyses
    - Destination difficulty aggregates and ranking.
    - Correlation analysis to identify difficulty drivers.
    - Difficult vs Easy averages across key metrics.
    - Daily stats (avg/median/max difficulty, count, avg delay).
    - EDA answers around delays, ground-time risk, transfer bags, load factors, SSR vs delays (controlling for load).
    - Advanced visualizations (heatmap, box plots, scatter matrix, radar, moving average).

11. Outputs
    - Curated flight-level CSV with IDs, scores, ranks, features, ops metrics; sorted by date and rank.
    - Summary statistics and operational recommendations.
    - Operational impact summary table (and CSV).

### Why these steps exist
- Normalize-by-day: Ensures fair intra-day comparisons, avoiding spurious variability due to schedule mix changes across days.
- Mix of features: Captures operational load drivers: time pressure on turns, service complexity, passenger load, baggage logistics, and flight characteristics that demand more coordination.
- Weights: Encodes domain judgement about what strains operations most; tunable with stakeholder feedback.
- Daily ranks/percentiles: Produces actionable, day-specific prioritization for ops teams.

### Key results (from this run)
- Difficulty score scaled stats: mean 41.55, std 11.81, min 0, max 100.
- Classification:
  - Difficult 2,036 (25.1%), Medium 2,829 (34.9%), Easy 3,234 (39.9%).
- Top difficulty drivers (correlation with difficulty):
  - is_tight_turnaround (0.471), haul_complexity_score (0.460), is_international (0.446), children_ratio (0.382), load_factor (0.350).
- Delay context:
  - Avg departure delay 21.18 min; 49.6% late; >15 min: 26.7%.
  - High SSR flights average +7.71 min more delay overall; effect varies by load bin.
- Ground time:
  - Tight turnarounds: 9.6%; very tight: 8.1%; insufficient time ratio <1.0 on 630 flights.
- Destination difficulty (examples):
  - Highest avg difficulty includes BRU, AUA, NAS, GUA, FRA, DUB, MBJ, CDG, GRU, HND.
- Difficult vs Easy comparisons:
  - Difficult flights: higher load factor, more service intensity (SSR, wheelchair, children), much higher tight-turn rate, higher delays.

### Files generated by the notebook
- team_sarthak.csv (flight-level results with scores, ranks, categories, and key features)
- Visuals:
  - difficulty_score_overview.png
  - destinations_difficulty.png
  - feature_importance.png
  - daily_trends.png
  - eda_delays.png
  - eda_ground_time.png
  - eda_bags.png
  - eda_load_factor.png
  - eda_special_service.png
  - difficulty_heatmap.png
  - difficulty_by_destination_boxplot.png
  - scatter_matrix.png
  - complexity_radar.png
  - difficulty_trend.png
- Tables:
  - operational_impact_summary.csv

### End product you use
- The ranked, categorized flight list with a 0–100 difficulty score per flight/day (team_sarthak.csv) plus dashboards and summary tables. These enable staffing, turnaround planning, and targeted mitigations (e.g., extra ground crew for high-difficulty, tight-turn, high-load flights; pre-positioning SSR resources; baggage handling surge for baggage-heavy flights).