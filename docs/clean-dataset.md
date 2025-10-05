### What this document is
- **Purpose**: Document the data cleaning and normalization steps implemented in `clean-dataset.ipynb`, aligned with conventions used across `Deliverable-1.ipynb`, `Deliverable-2.ipynb`, and `Deliverable-3.ipynb`.
- **Scope**: Flight-level, PNR-flight, PNR-remarks, Airports, and Bag-level datasets from `Dataset/`.

### Data inputs
- `Dataset/Flight_Level_Data.csv`
- `Dataset/PNR_Flight_Level_Data.csv`
- `Dataset/PNR_Remark_Level_Data.csv`
- `Dataset/Airports_Data.csv`
- `Dataset/Bag_Level_Data.csv`

### Global cleaning principles (consistent with deliverables)
- **Deduplicate** all inputs prior to transformations.
- **Type safety**: Convert date/time-like columns to `datetime`; numeric counts to numeric (coerce invalid to NA → filled or left as NA as appropriate).
- **Canonical identifiers**: Uppercase and trim string identifiers (`company_id`, `airport_iata_code`, `iso_country_code`, `flight_number`).
- **Boolean harmonization**: Map textual booleans ("true"/"false", "yes"/"no") to 1/0.
- **Graceful coercion**: Use `errors='coerce'` on datetime/numeric conversions; fill with median (for continuous ops fields) or zeros (for count fields) where appropriate.

### Flight level data cleaning
- Drop duplicates.
- Convert datetime columns when present:
  - `scheduled_departure_datetime_local`, `scheduled_arrival_datetime_local`,
  - `actual_departure_datetime_local`, `actual_arrival_datetime_local`.
- Convert numeric operational fields to numeric and impute missing with median:
  - `total_seats`, `scheduled_ground_time_minutes`, `actual_ground_time_minutes`, `minimum_turn_minutes`.
- Canonicalize identifiers: uppercase/trim `company_id`, `flight_number`.

### PNR flight level data cleaning
- Drop duplicates; print columns for visibility (development aid).
- Ensure passenger/count metrics are numeric with NA→0 fallback when present:
  - `total_pax`, `lap_child_count`, `basic_economy_pax` (if exists).
- Harmonize booleans to 1/0 (if present):
  - `is_child`, `is_stroller_user` with mapping `{true/yes: 1, false/no: 0}` after lowercasing strings.
- Canonicalize identifiers: uppercase/trim `company_id`.

### PNR remarks data cleaning
- Drop duplicates.
- Trim/canonicalize keys: `record_locator` trim; `flight_number` trim.
- Fill missing categorical text: `special_service_request` → "NONE".
- Coerce dates: `pnr_creation_date` to `datetime`.

### Airports data cleaning
- Drop duplicates.
- Canonicalize codes: uppercase/trim `airport_iata_code`, `iso_country_code`.

### Bag level data cleaning
- Drop duplicates.
- Normalize bag descriptors:
  - `bag_type` → title case + trim (e.g., `Origin`, `Transfer`, `Hot Transfer`).
- Coerce dates: `bag_tag_issue_date` to `datetime`.
- Canonicalize identifiers: uppercase/trim `company_id`.

### Cross-dataset harmonization
- For each of `[flights, pnr_flight, pnr_remarks, bags]`:
  - Ensure `flight_number` is string-trimmed when present.
  - Ensure `company_id` uppercase/trim when present.
  - Coerce `scheduled_departure_date_local` to `datetime` when present.

### Keys and join readiness (as used in deliverables)
- Identifiers prepared for building composite `flight_id` downstream (in analysis notebooks):
  - `company_id`, `flight_number`, `scheduled_departure_date_local`, origin and destination station codes.
- Cleaned fields enable deterministic joins across flights ↔ PNR flights/remarks ↔ bags ↔ airports.

### Output row counts from the cleaning run
- Flights: `8,099`
- PNR Flight: `687,878`
- PNR Remarks: `51,698`
- Airports: `5,612`
- Bags: `686,952`

### Rationale choices (aligned with the three deliverables)
- **Median imputation** for operational time/seat metrics avoids bias from extreme values while preserving distribution.
- **Zero-fill for counts** after coercion reflects that invalid/blank counts imply absence rather than unknown for aggregations.
- **Uppercase/trim for codes** prevents join fragmentation from casing/whitespace.
- **Datetime coercion** enables reliable time math (delays, ground-time, normalization by day).
- **Boolean normalization** supports consistent aggregation and scoring.

### What to expect after cleaning
- All temporal analyses (delays, ground-time pressure) are reproducible due to strict datetime typing.
- Passenger and baggage aggregations are stable (no mixed types).
- Cross-source joins produce high match rates due to canonicalized keys.
- Downstream notebooks (`Deliverable-1/2/3`) can safely compute: delay metrics, bag ratios, load factors, SSR effects, difficulty and risk scores, destination clustering, and recommendations.

### Notes for future iterations
- Consider persisting standardized `flight_id` in the cleaning step to simplify downstream merges.
- Add explicit schema validation (e.g., `pandera`/`pydantic`) to assert column presence and dtypes.
- Track and export basic data quality summaries (null rates, coercion counts) per file.
