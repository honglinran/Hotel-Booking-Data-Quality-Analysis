# Hotel Booking Data Quality & Analysis

**Python · pandas · Jupyter Notebook · Data validation · Feature engineering**

Preparing hotel booking and capacity data for reliable revenue, booking-behaviour and occupancy analysis.

## Project overview

Hotel reports depend on consistent dates, valid guest counts and trustworthy revenue and capacity figures. This project profiles booking data, investigates anomalies and creates analysis-ready fields while retaining records that require further review.

The supplied project notebook export documents **134,590 booking records**, **25 hotel dimension records**, **4 room categories** and **9,200 aggregate booking records**. The work demonstrates data cleaning, business-rule validation, referential-integrity checks and feature engineering in Python.

**Status:** Python preparation is documented in the source PDF. This repository includes a newly reconstructed script and companion notebook based on that work. SQL analysis and a Power BI dashboard are planned extensions.

## Business questions

- How do booking volumes and realized revenue vary by hotel, room category and booking platform?
- How far in advance do customers book, and how long are their scheduled stays?
- Which data issues could distort revenue or capacity reporting?
- Once capacity exceptions are resolved, how does booking utilisation vary by property and date?

These questions could support hotel operations, revenue management and reporting teams. The current results establish data quality; they do not demonstrate revenue uplift or completed dashboard findings.

## Findings documented in the original notebook

| Check or observation | Recorded result | Interpretation |
|---|---:|---|
| Booking rows | 134,590 | Booking-level dataset |
| Duplicate booking IDs | 0 | No duplicate IDs detected |
| Invalid guest counts before cleaning | 9 | Non-positive values converted to missing |
| Missing guest counts after cleaning | 12 | 3 original missing values plus 9 invalid values |
| Revenue anomalies | 5 | Generated / realized revenue ratio above 10 |
| Approximate 1,000× revenue discrepancies | 3 | Divided by 1,000 in a separate candidate-clean column |
| Missing capacity before imputation | 2 | Filled using property-and-room median |
| Missing capacity after imputation | 0 | Confirmed by the final displayed output |
| Successful bookings above original capacity | 6 | Unresolved capacity exceptions |
| Invalid hotel / room references | 0 / 0 | Checked against dimension IDs |
| Average booking lead time | 3.71 days | Median: 3 days; range: 0–24 |
| Average scheduled length of stay | 2.37 nights | Median: 2 nights; range: 1–6 |

**Evidence:** Figures are transcribed from the supplied notebook PDF, not recomputed from source CSVs. Raw CSVs and the original editable notebook were not supplied. See [source notes](docs/source-notes.md) for cell references and limitations.

Short lead times may warrant examining last-minute demand. Scheduled stay lengths may help segment bookings. These are exploratory directions, not proven commercial recommendations.

## Cleaning and validation

1. Profile table sizes, column types and missing values.
2. Parse mixed date formats using day-first interpretation.
3. Check booking IDs, date ordering and hotel/room references.
4. Convert non-positive guest counts to missing nullable integers, retaining the bookings.
5. Derive booking lead time, lead-time bands and scheduled length of stay.
6. Flag generated-to-realized revenue ratios above 10.
7. Preserve original revenue and calculate candidate corrections for ratios between 999 and 1,001.
8. Impute missing capacity using the median for the same property and room category.
9. Export cleaned tables, validation counts and exception records.

The reconstructed implementation adds explicit missing-date checks, raw guest-count preservation, zero-denominator handling and a post-correction review flag. These safeguards were added during repository preparation.

**Revenue corrections remain provisional:** an approximate 1,000× discrepancy suggests a scaling issue but does not prove one. Confirm it with source records before using candidate-clean revenue in financial reporting. Two other flagged anomalies have no documented correction.

## Repository contents

| Path | Purpose |
|---|---|
| `src/prepare_data.py` | Reconstructed cleaning pipeline and command-line entry point |
| `notebooks/01_hotel_data_quality.ipynb` | Companion notebook to run and inspect the pipeline |
| `data/raw/` | Place your original CSV files here; excluded from Git |
| `data/processed/` | Generated outputs; excluded from Git |
| `reports/pdf_validation_snapshot.csv` | Manually transcribed validation evidence |
| `docs/data-dictionary.md` | Expected input schema and derived fields |
| `docs/source-notes.md` | Evidence, assumptions and unresolved issues |
| `docs/next-steps.md` | SQL and Power BI development plan |
| `docs/github-setup.md` | Instructions for publishing this folder |

## Run locally

Use Python 3.10 or later. From this repository folder:

```bash
python -m pip install -r requirements.txt
python src/prepare_data.py --input-dir data/raw --output-dir data/processed
```

Supply these four CSVs, renaming your local copies if necessary:

- `fact_bookings.csv`
- `fact_aggregated_bookings.csv`
- `dim_hotels.csv`
- `dim_rooms.csv`

The source PDF also profiles a date dimension and a seven-row August extract. They are not required by this cleaning script and are not merged into the booking fact: the August extract has a different grain.

To use the companion notebook:

```bash
jupyter notebook notebooks/01_hotel_data_quality.ipynb
```

Outputs include cleaned booking and capacity tables, booking/capacity validation reports, revenue exceptions and capacity exceptions. Generated files overwrite files with the same names in the output directory.

The script has been smoke-tested with synthetic edge cases. Full-data reproduction requires the original CSVs. The notebook intentionally contains no fabricated execution outputs.

## Data model and reporting considerations

The intended booking grain is one row per booking ID. The intended aggregate grain is property × check-in date × room category; validate its uniqueness before modelling. Hotel and room dimensions should have unique keys.

Use shared dimensions to filter both facts. Avoid joining the booking and aggregate fact tables directly, which can multiply rows and inflate totals.

- Calculate weighted utilisation from summed successful bookings and aligned capacity, after reviewing exceptions. Confirm what “successful bookings” represents before naming the measure occupancy.
- ADR and RevPAR require validated sold-room-night and available-room-night definitions.
- Booking-level revenue is not automatically nightly revenue.
- Scheduled stays can include cancelled or no-show bookings; they are not a count of nights actually stayed.
- Costs are absent, so profit and margin cannot be established.
- Dataset currency, geographic scope, original publisher and redistribution terms are unverified.

## Roadmap

- [x] Profile and validate booking data in Python
- [x] Engineer booking lead time and stay length
- [x] Document revenue and capacity exceptions
- [x] Package reconstructed, reusable preparation code
- [ ] Reproduce outputs with the original CSVs
- [ ] Verify revenue corrections and resolve capacity exceptions
- [ ] Load validated tables into a relational database
- [ ] Add SQL business analysis
- [ ] Build and validate a Power BI model and dashboard

## Data availability and attribution

This repository does not distribute the source dataset. Supply your own authorised copies. Record the original dataset URL, publisher, currency, geographic scope and licence before distributing data or publishing attribution claims.

Project author: **Honglin Ran**. Repository packaging and reconstructed code were prepared with AI assistance from the supplied project PDF. No open-source licence has been assigned; add one if you choose to license your own code.
