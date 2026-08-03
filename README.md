# NRL Fantasy Data Analysis (2021–2026)

**Status:** 🚧 In Progress — actively updated as new NRL Fantasy rounds are played and new analysis is added

A personal data analysis project exploring six seasons of my own NRL Fantasy performance — round scores, overall rank, round rank, and trade activity — using Python and SQL.

This project was built primarily to practice and demonstrate data cleaning, database design, and SQL querying skills on a real, personally meaningful dataset.

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
- Season length also changed: 2021 and 2022 ran for 25 rounds, while 2023–2026 ran for 27 rounds — worth keeping in mind when comparing season totals (e.g. `overall_score`) directly, since later seasons have two extra rounds to accumulate points.

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

  | Season | Best overall rank |
  |--------|-------------------|
  | 2021 | 14,730 |
  | 2022 | 1,140 |
  | 2023 | 3,019 |
  | 2024 | 5,121 |
  | 2025 | 8,264 |
  | 2026 | 3,944 (season in progress) |

- **2021 was by far my worst finishing season** (final overall rank: ~29,445th), followed by a sharp turnaround to my best-ever finish in 2022 (~2,412th). Final rank has drifted gradually worse since then (2023–2026), even though average scoring has generally improved — again pointing to rank being relative to the whole player pool, not just personal output.

- **2024 was my weakest season overall** — lowest average score (820) and a mediocre best rank, likely worth revisiting for what went wrong.
- **Big scoring rounds (1,000+) have become more frequent in recent seasons.** Of 19 rounds where I scored 1,000+, the majority came in 2023–2026, versus just 1 each in 2021 and 2022.
- **Score distribution:** using score tiers of Low (&lt;800), Medium (800–1000), and High (1000+), across all 152 rounds ~59% fall in the Medium range, ~13% are High, and ~29% are Low — a wider spread than a narrower band would suggest, though still skewed toward Medium/High overall.
- **Most "low" scoring rounds coincide with the State of Origin period**, when several players are unavailable, and the team is effectively down to 13 players or fewer — a structural scoring dip rather than poor team selection.
- **Trade activity:** Round 1 always shows zero trades (as expected), with late-season zero-trade rounds common in most years — likely reflecting exhausted trade budgets or locking in a settled team for finals.

## Visualizations

Built using Matplotlib, directly from SQL query results:

- **Average round score by season** — bar chart comparing scoring performance across all six seasons
- **Overall rank progression (2022 season)** — line chart with an inverted y-axis, so a falling (improving) rank reads visually as an upward trend, matching the intuitive "up = good" reading of a score chart
- **Final overall rank by season** — line chart with an inverted y-axis (lower rank = better), so an upward line reads as an improving finish

More visuals (season comparisons, score-tier breakdown, Origin-period impact) planned as the project continues.

## Skills Demonstrated

- **Python/pandas** — data cleaning, type conversion, handling missing/incomplete data
- **SQL** — `SELECT`, `WHERE`, `GROUP BY`, aggregate functions (`AVG`, `MIN`, `COUNT`), `CASE` statement bucketing, correlated subqueries
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
