# NRL-Fantasy-data-analysis
# NRL Fantasy Data Analysis (2021–2026)

A personal data analysis project exploring six seasons of my own NRL Fantasy performance — round scores, overall rank, round rank, and trade activity — using Python and SQL.

This project was built primarily as a way to practice and demonstrate data cleaning, database design, and SQL querying skills on a real, personally meaningful dataset.

> **Note:** The 2026 season is still in progress. Figures and findings below reflect completed rounds only and will be updated as the season continues.

## The Data

Source data was exported season-by-season from my own NRL Fantasy team, then consolidated into a single master Google Sheet in long/tidy format (one row per round per season) before being cleaned and loaded for analysis.

Each row captures, for a given season and round:
- `round_score` — points scored that round
- `overall_score` — cumulative season score to date
- `overall_rank` — overall competition rank after that round
- `round_rank` — rank for that round specifically
- `trades_used` — trades made that round
- `trades_remaining` — trades left for the rest of the season

**Coverage:** 2021–2026 (2026 in progress, incomplete rounds excluded), 152 completed rounds total.

**Data quality notes:**
- Raw yearly exports had inconsistent formatting (extra currency columns, comma-formatted numbers, inconsistent header rows) — resolved by consolidating into one clean master sheet.
- NRL Fantasy's trade rules and caps changed between seasons (e.g. 2021: 34 total trades; 2022: 36 total; 2023–2026: 44 total).
- Season length also changed: 2021 and 2022 ran for 25 rounds, while 2023–2026 run for 27 rounds — worth keeping in mind when comparing season totals (e.g. `overall_score`) directly, since later seasons have two extra rounds to accumulate points.

## Pipeline

1. **Export** — Google Sheet → CSV
2. **Load** — Read into a pandas DataFrame (Google Colab)
3. **Clean** — Drop incomplete/unplayed rounds, convert columns to integer types, rename columns to snake_case
4. **Store** — Load into a single SQLite database table (`nrl_fantasy`), with `(season, round)` as the primary key
5. ** Analyse ** — Query the database directly with SQL
6. **Visualize** — Chart key trends using Matplotlib

## Schema

```sql
CREATE TABLE nrl_fantasy (
    season INTEGER,
    round INTEGER,
    round_score INTEGER,
    overall_score INTEGER,
    overall_rank INTEGER,
    round_rank INTEGER,
    trades_used INTEGER,
    trades_remaining INTEGER,
    PRIMARY KEY (season, round)
);
```

## Key Findings

- **Best-ever rank vs best-ever scoring season aren't the same thing.** 2022 was my best-ranked season (best overall rank: 1,140th), but not my highest-scoring one. 2025 had my highest average round score (897), yet a comparatively worse best rank (8,264th) — a reminder that rank depends on the whole player pool's scoring, not just mine.
- **2024 was my weakest season overall** — lowest average score (820) and a mediocre best rank, likely worth revisiting for what went wrong.
- **Big scoring rounds (950+) have become more frequent in recent seasons.** Of 19 rounds where I scored 1,000+, the majority came in 2023–2026, versus just 1 each in 2021 and 2022.
- **Score distribution:** across all 152 rounds, ~67% fall in the 700–950 "medium" range, ~22% are 950+ ("high"), and ~11% are sub-700 ("low") — a reasonably consistent scoring history skewed toward the upside.
- **Most "low" scoring rounds coincide with the State of Origin period**, when several players are unavailable, and the team is effectively down to 13 players or fewer — a structural scoring dip rather than poor team selection.
- **Trade activity:** Round 1 always shows zero trades (as expected), with late-season zero-trade rounds common in most years — likely reflecting exhausted trade budgets or locking in a settled team for finals.

## Visualizations

Built using Matplotlib, directly from SQL query results:

- **Average round score by season** — bar chart comparing scoring performance across all six seasons
- **Overall rank progression (2022 season)** — line chart with an inverted y-axis, so a falling (improving) rank reads visually as an upward trend, matching the intuitive "up = good" reading of a score chart

More visuals (season comparisons, score-tier breakdown, Origin-period impact) planned as the project continues.

## Skills Demonstrated

- **Python/pandas** — data cleaning, type conversion, handling missing/incomplete data
- **SQL** — `SELECT`, `WHERE`, `GROUP BY`, aggregate functions (`AVG`, `MIN`, `COUNT`), `CASE` statement bucketing
- **Database design** — schema design, primary keys, long/tidy data modelling
- **Data visualization** — Matplotlib line charts, axis customisation (e.g. inverted axes for rank data)
- **Data quality investigation** — identifying and resolving inconsistencies in real-world, self-collected data across multiple years

## Tools

Google Sheets, Google Colab, Python (pandas, Matplotlib), SQLite

## Next Steps

- Additional visualisations (season comparisons, score tier breakdown, Origin-period scoring impact)
- Tableau dashboard visualising score trends, rank trajectory, and trade activity
- Deeper analysis of trade timing vs scoring outcomes
- Publish to GitHub once the 2026 season is complete
