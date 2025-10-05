### What it is
- *Purpose*: Post-analysis to identify difficult destinations, understand drivers of difficulty, segment destinations into operational profiles, flag high-risk flights, and produce concrete operational recommendations and investment priorities.
- *Inputs*: team_sarthak.csv (master with per-flight difficulty scores and features) plus raw Dataset/* CSVs.
- *Assumptions*: Flight-level columns exist such as difficulty_score_scaled, difficulty_category, departure_delay_minutes, load_factor, ground_time_pressure, bags_per_passenger, transfer_ratio, special_service_ratio, wheelchair_ratio, is_tight_turnaround, is_international, haul_type, time_of_day, etc.

### How it works (pipeline)
- *Data load + overview*
  - Loads master and supporting datasets; converts dates; prints distributions and stats.
  - Key context (from run): 8,099 flights, 188 destinations, Aug 1–15, 2025.

- *Destination difficulty ranking*
  - Aggregates per-destination metrics and filters to ≥5 flights.
  - Builds a composite score: 40% avg difficulty + 30% % difficult flights + 15% consistency + 15% avg delay.
  - Exports top list:
    - Output: top_difficult_destinations.csv
    - Plot: included in difficult_destinations_analysis.png

- *Difficulty profiling*
  - Labels destinations as:
    - Chronically Difficult (≥40% difficult), Moderately Difficult (≥25%), Sporadically Difficult (avg ≥60), or Generally Manageable.
  - Flags “problem destinations” = chronically or moderately difficult.

- *Visualization set 1*
  - Top-15 difficult barh, difficulty vs % difficult scatter (size=flights, color=delay), profile pie, top delay barh.
  - Output: difficult_destinations_analysis.png

- *Driver analysis (top-10 difficult)*
  - Compares destination averages to global means via simple z-score-like deltas across: ground pressure (inverted), load factor, bags per pax, transfer ratio, SSR, wheelchair, tight turnaround %, international %.
  - Produces top-3 drivers per destination; aggregates driver frequencies.
  - Outputs:
    - destination_driver_analysis.csv
    - Summary frequencies shown later in visuals.

- *Difficult vs easy comparison*
  - Compares feature means for top-10 difficult vs bottom-10 easy destinations (delays, load, etc.).

- *Visualization set 2*
  - Driver frequency barh, percent-diff barh (difficult vs easy), load factor and ground pressure distributions.
  - Output: driver_patterns_analysis.png

- *Clustering (KMeans)*
  - Aggregates destination-level features, standardizes, runs KMeans (K=2..7 elbow; selects K=4).
  - Names clusters using rules-of-thumb (e.g., Delay-Prone Routes, Standard Operations, High Service Demand, etc.).
  - Outputs:
    - destination_clusters.csv
    - cluster_analysis.png (PCA scatter with centroids, elbow curve, cluster heatmap, summary bars)

- *Risk scoring (flight-level)*
  - Builds a simple additive risk score (tight turnaround, high load ≥0.85, high SSR/transfer/bags > 75th pct).
  - Categorizes flights: Critical/High/Moderate/Low.
  - Aggregates high-risk counts by destination.
  - Visualization set 3:
    - Risk category distribution, top high-risk destinations, time-of-day and day-of-week risk patterns.
    - Output: risk_patterns_analysis.png

- *Staffing recommendations (time-based)*
  - Uses time-of-day averages of difficulty/risk, tight turnarounds, and SSR to recommend staffing level by period (High/Medium/Standard) with targeted notes.

- *Schedule optimization*
  - Identifies flight-number pairs with chronic delays (≥3 occurrences, avg delay >20m).
  - Suggests actions (increase ground time, shift to off-peak, pre-flight ops briefing).
  - Output: schedule_optimization_recommendations.csv

- *Investment prioritization matrix*
  - Quantifies issue impact by mapping recommendations back to affected flights and average delay impact.
  - Computes a priority score = Destinations × Flights × Delay Impact.
  - Outputs:
    - investment_priorities.csv
    - Included in visualization set 4.

- *Visualization set 4 (recommendations summary)*
  - Issue frequency, investment matrix scatter (size=color=priority), priority ranking barh, severity pie.
  - Output: operational_recommendations_summary.png

- *Operational impact summary*
  - Summarizes totals, delay split, average delays by category, number of problem destinations, count of recommendations, and a potential-improvement scenario (e.g., 30% reduction on difficult flights).
  - Output: operational_impact_summary.csv

- *Action plan*
  - Immediate (0–30d), Short-term (1–3m), Long-term (3–12m) actions with concrete steps tied to findings.

- *Executive summary*
  - Narrative report printed and saved.
  - Output: post_analysis.log

### Why these steps
- *Ranking*: Isolates where difficulty is consistently high to focus attention.
- *Profiling and drivers*: Translates “where” into “why” by surfacing operational contributors.
- *Clustering*: Segments destinations into operationally meaningful archetypes for playbooks.
- *Risk scoring*: Flags individual flights most likely to strain operations on a given day.
- *Temporal analysis*: Aligns staffing with demand/risk patterns to improve OTP.
- *Schedule optimization*: Targets systemic delay sources at the flight-number level.
- *Investment matrix*: Prioritizes resources by measurable impact.
- *Impact summary*: Quantifies current pain and potential gains to drive decisions.

### Key results from the current run
- *Scale*: 8,099 flights, 188 destinations (Aug 1–15, 2025).
- *Difficulty mix*: 25.1% flights Difficult; mean difficulty 41.5.
- *Top difficult destinations*: Predominantly international (e.g., BRU, FRA, CDG, GRU, DUB).
- *Primary drivers (top-10 difficult set)*:
  - International nature, tight turnarounds, and heavy baggage/transfer bags recur most often.
- *Clusters (K=4)*: Majority in Standard Ops; a clear “Delay-Prone Routes” segment emerges; a small “High Service Demand” segment.
- *Risk*: ~49.6% flights High or Critical risk by heuristic; clear time-of-day/day-of-week patterns; HIGH staffing recommended for Midday and Evening Peak periods.
- *Chronic delays*: 237 flight-number routes flagged for schedule review.
- *Potential improvement*: Reducing difficult-flight delays by 30% saves ~22,682 minutes over the 15-day window (~9,199 hours annualized).
- *Deliverables generated*:
  - CSVs: top_difficult_destinations.csv, destination_driver_analysis.csv, destination_clusters.csv, destination_recommendations.csv, schedule_optimization_recommendations.csv, investment_priorities.csv, operational_impact_summary.csv
  - PNGs: difficult_destinations_analysis.png, driver_patterns_analysis.png, cluster_analysis.png, risk_patterns_analysis.png, operational_recommendations_summary.png
  - Log/Report: post_analysis.log