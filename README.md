# SAP IBP Time Profile Generator

A standalone browser-based tool for generating time profiles compatible with SAP Integrated Business Planning (IBP). No installation required — open the HTML file in any modern browser.

## Why This Exists

SAP IBP's built-in time profile configuration only supports Monday-start weeks and calendar-aligned periods. This tool removes those constraints:

- **Custom week start days** — Friday for Middle East, Saturday for retail, or any day you need
- **Technical weeks with month-boundary splits** — TWa/TWb periods for financial reconciliation
- **Fiscal retail calendars** — 4-4-5, 4-5-4, and 5-4-4 patterns with automatic leap week handling
- **All 6 period levels** generated in a single pass, ready for IBP upload

## Quick Start

1. Open `time-profile-generator.html` in your browser
2. Choose a calendar type:
   - **Gregorian (Standard)** — calendar-aligned months, quarters, and fiscal years
   - **Fiscal (Retail)** — week-based periods following a 4-4-5, 4-5-4, or 5-4-4 pattern
3. Set your **Start Date** and **End Date**
4. Select your **Week Start** day
5. Click **Generate Time Periods**
6. Download the CSV and upload into IBP

## Calendar Modes

### Gregorian (Standard)

Generates periods aligned to the Gregorian calendar:

| Level | Type | Description |
|-------|------|-------------|
| 1 | Day | Every calendar day |
| 2 | Technical Week | 7-day weeks that split at month boundaries (TWa/TWb) |
| 3 | Week | Full 7-day weeks |
| 4 | Month | Calendar months |
| 5 | Quarter | ~3 calendar months |
| 6 | Fiscal Year | ~12 calendar months |

Technical weeks split when a week crosses a month boundary — the portion in the first month becomes TWa, and the remainder becomes TWb. This is critical for financial disaggregation in IBP using the WEEKWEIGHT attribute.

### Fiscal (Retail)

Generates periods based on retail fiscal calendars where every period is an exact multiple of weeks:

| Level | Type | Description |
|-------|------|-------------|
| 1 | Day | Every calendar day |
| 2 | Technical Week | Identical to Week (no splits needed — fiscal months align to week boundaries) |
| 3 | Week | 7-day weeks numbered within each fiscal year |
| 4 | Month | 4 or 5 weeks per the selected pattern |
| 5 | Quarter | 13 weeks (91 days), or 14 weeks in a 53-week year |
| 6 | Fiscal Year | 52 or 53 weeks |

**Fiscal Pattern Options:**

- **4-4-5** — Quarter = 4-week month, 4-week month, 5-week month
- **4-5-4** — Quarter = 4-week month, 5-week month, 4-week month
- **5-4-4** — Quarter = 5-week month, 4-week month, 4-week month

**Leap Week Handling:** A 52-week fiscal year is 364 days, one day short of a calendar year. The tool automatically detects when cumulative drift reaches 7 days (~every 5-6 years) and adds a 53rd week to the last month of Q4.

**Month Naming:** Leave the prefix blank for approximate Gregorian month names (January, February, ...) or enter a prefix like `P` to get P01, P02, ... P12.

## CSV Output

The generated CSV has the following columns:

```
Level,PeriodID,StartDate,EndDate,Description,DayCount
```

- **Level** — Period hierarchy (1=Day through 6=Fiscal Year)
- **PeriodID** — Unique identifier prefixed by level (10001+ for Day, 20001+ for TW, etc.)
- **StartDate / EndDate** — ISO format (YYYY-MM-DD)
- **Description** — Human-readable label
- **DayCount** — Number of days in the period

## Uploading to IBP

1. Generate and download the CSV
2. Adjust description formats if your IBP instance requires specific naming conventions
3. Upload via IBP's time profile import functionality

## Requirements

- Any modern browser (Chrome, Firefox, Safari, Edge)
- No server, no dependencies, no installation
