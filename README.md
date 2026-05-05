# Bellabeat Smart Device Analysis

Analyzing FitBit fitness tracker data to uncover behavioral trends among smart device users, and applying those insights to inform Bellabeat's marketing strategy for the **Leaf wellness tracker**.

![R](https://img.shields.io/badge/R-4.0%2B-276DC3?logo=r&logoColor=white)
![Python](https://img.shields.io/badge/Python-3.10%2B-3776AB?logo=python&logoColor=white)
![PostgreSQL](https://img.shields.io/badge/PostgreSQL-Supabase-4169E1?logo=postgresql&logoColor=white)
![tidyverse](https://img.shields.io/badge/tidyverse-1.3%2B-1A162D?logo=r&logoColor=white)
![Tableau](https://img.shields.io/badge/Tableau-E97627?logo=tableau&logoColor=white)
![Google Data Analytics](https://img.shields.io/badge/Google_Data_Analytics-Capstone-4285F4?logo=google&logoColor=white)
![License: MIT](https://img.shields.io/badge/License-MIT-green.svg)

🔗 **[View R Markdown Case Study →]([https://karen-kaiwen.github.io/bellabeat-case-study/](https://karen-kaiwen.github.io/bellabeat-case-study/How-Can-a-Wellness-Technology-Company-Play-It-Smart-V4.html))**
🐍 **[View Python + SQL Notebook (Kaggle) →](https://www.kaggle.com/code/kaiwenhuang123/bellabeat-case-study)**
📊 **[View Tableau Dashboard →](https://public.tableau.com/shared/22QGRHM8T?:display_count=n&:origin=viz_share_link)**

---

## Table of Contents
- [Project Overview](#project-overview)
- [Analysis Architecture](#analysis-architecture)
- [Tool Roles](#tool-roles)
- [Technical Challenges & Solutions](#technical-challenges--solutions)
- [Data Source](#data-source)
- [Key Findings](#key-findings)
- [Marketing Recommendations](#marketing-recommendations)
- [Known Limitations](#known-limitations)
- [License](#license)

---

## Project Overview

This project is the Capstone Case Study for the Google Data Analytics Certificate. It analyzes publicly available FitBit fitness tracker data to understand how people use smart devices — and how those behavioral patterns can inform the marketing strategy of Bellabeat, a wellness technology company for women.

The central business question:

> *What are the trends in smart device usage, and how can they influence Bellabeat's marketing strategy?*

The analysis follows the Google Data Analytics six-phase framework: **Ask → Prepare → Process → Analyze → Share → Act**.

This project uses four tools in a single integrated pipeline, each with a distinct role:

| Tool | Responsibility | Output |
|------|---------------|--------|
| **R** | Time-series cleaning & hourly rhythm analysis | `Daily_Holistic_Cleaned.csv`, `Hourly_Rhythm_Cleaned.csv` |
| **Python** | Data cleaning, type conversion, multi-file merging | `daily_activity`, `heartrate`, `hourly`, `sleep` loaded into PostgreSQL |
| **SQL (PostgreSQL / Supabase)** | User segmentation, sleep efficiency, wear frequency classification | `user_segments`, `user_sleep`, `user_time` |
| **Tableau** | Joins R outputs + SQL outputs into interactive dashboard | Final visualizations & stakeholder communication |

The full analysis is documented across two reports:

| Report | Tools | Link |
|--------|-------|------|
| R Markdown Case Study | R (tidyverse) | [View Report]([https://karen-kaiwen.github.io/bellabeat-case-study/](https://karen-kaiwen.github.io/bellabeat-case-study/How-Can-a-Wellness-Technology-Company-Play-It-Smart-V4.html)) |
| Python + SQL Notebook | Python (pandas, plotly), SQL (PostgreSQL) | [View on Kaggle](https://www.kaggle.com/code/kaiwenhuang123/bellabeat-case-study) |

---

## Analysis Architecture

```
FitBit Dataset (Kaggle, CC0)
  · dailyActivity_merged.csv
  · hourlyCalories / Steps / Intensities_merged.csv
  · sleepDay_merged.csv
  · heartrate_seconds_merged.csv
  · weightLogInfo_merged.csv
         │
         ├─────────────────────────────────────────────┐
         ▼                                             ▼
  R (tidyverse)                              Python + SQL (pandas / PostgreSQL)
  · Standardize date formats                 · Type conversion & duplicate removal
  · Remove duplicates                        · Heartrate downsampled: seconds → hourly avg
  · Heartrate: seconds → hourly avg          · Multi-file merge (hourly tables)
  · Master table construction:               · Load 4 tables into Supabase via SQLAlchemy:
    daily_activity, heartrate, hourly, sleep
    - Daily_Holistic                         · SQL Views (PostgreSQL):
      dailyActivity LEFT JOIN sleepDay         - user_segments  (CASE WHEN on AVG steps)
      → DayOfWeek, TotalActiveMinutes,         - user_time      (COUNT DISTINCT dates)
        RestlessMinutes                        - user_sleep     (sleep efficiency + LEAD())
    - Hourly_Rhythm                          · Plotly visualizations (Kaggle Notebook)
      hourlyCalories + Intensities + Steps
      → Hour (0–23)
    - Health_Metrics
      heartrate grouped by Id + Date + Hour
      → AvgHourlyHeartRate
         │                                             │
         ▼                                             ▼
  Daily_Holistic_Cleaned.csv             user_segments, user_sleep, user_time
  Hourly_Rhythm_Cleaned.csv
         │                                             │
         └─────────────────────────────────────────────┘
                               │
                               ▼
                    Tableau Dashboard
         (R outputs provide time-series metrics;
          SQL outputs provide user classification labels;
          Tableau JOINs both to enable segment-level analysis)
                               │
                  ┌────────────┼────────────┐
                  ▼            ▼            ▼
            KPI Overview   Behavior     Relationships
            · Avg Steps    Insights     Between Metrics
            · Avg Sleep    · Active     · Steps vs.
            · Wear Rate      time         Sleep Efficiency
                           · Calories   · Next-day Steps
                             by type      vs. Sleep
                           · Sedentary
                             by type
                               │
                               ▼
                  6 Key Findings + 5 Recommendations
```

---

## Tool Roles

Each tool handled a distinct, non-overlapping part of the pipeline:

| Tool | Responsibility | Key Output |
|------|---------------|------------|
| **R** | Time-series cleaning; hourly rhythm analysis; heart rate vs. calorie lag; master table construction | `Daily_Holistic_Cleaned.csv`, `Hourly_Rhythm_Cleaned.csv` |
| **Python** | Data cleaning automation; type conversion; duplicate removal; multi-file merging; Plotly visualizations | `daily_activity`, `heartrate`, `hourly`, `sleep` loaded into PostgreSQL |
| **SQL (PostgreSQL)** | User segmentation (CASE WHEN); sleep efficiency classification; LEAD() window function for next-day alignment; wear frequency | `user_segments`, `user_sleep`, `user_time` |
| **Tableau** | Joins R's time-series data with SQL's user labels to enable segment-level analysis; interactive stakeholder dashboard | Final dashboard |

---

## Technical Challenges & Solutions

**Defining a consistent user activity classification**

Problem: The raw dataset contains no activity level labels. A single day's step count is easily skewed by rest days, illness, or travel.

Solution: Used each user's **average daily step count** across all 31 days as the classification metric, referencing the Tudor-Locke step count framework to assign users to four segments: Sedentary / Lightly Active / Fairly Active / Very Active.

---

**Mismatched join keys between daily and hourly tables**

Problem: `dailyActivity` uses a date-only column, while hourly tables use full timestamps. A direct join inflates the dataset to 24× the expected row count.

Solution: Truncated hourly timestamps to date using `as.Date()` (R) and `pd.to_datetime(...).dt.date` (Python) before joining. Merged on `Id` + date using `left_join` / `pd.merge`.

---

**Estimating device wear time without explicit data**

Problem: The dataset does not record actual device wear time.

Solution: Used `COUNT(DISTINCT activitydate)` in SQL on the `daily_activity` table to count the number of days each user had recorded data. Categorized users as Low (≤15 days), Moderate (15–24 days), or High (25–31 days) wearers.

---

**Sleep quality measurement (V2)**

Problem: Sleep duration alone is an incomplete measure of quality — a user who spends 9 hours in bed but sleeps 6 is not well-rested.

Solution: Calculated **sleep efficiency** (total minutes asleep ÷ total time in bed). Users ≥90% efficiency classified as Good Sleep; below as Poor Sleep. Used PostgreSQL `LEAD()` window function to pair each night's sleep record with the following day's step count.

---

## Data Source

- **Dataset:** [FitBit Fitness Tracker Data](https://www.kaggle.com/datasets/arashnic/fitbit) (Kaggle, CC0 Public Domain)
- **Users:** 33 FitBit users
- **Period:** April 12 – May 12, 2016 (31 days)

| File | Contents |
|------|----------|
| `dailyActivity_merged.csv` | Daily steps, active minutes, sedentary minutes, calorie burn |
| `sleepDay_merged.csv` | Sleep duration and time in bed |
| `hourlySteps/Calories/Intensities_merged.csv` | Hourly activity breakdown |
| `heartrate_seconds_merged.csv` | Heart rate (downsampled to hourly averages) |
| `weightLogInfo_merged.csv` | Weight and BMI logs (8 users only) |

---

## Key Findings

**1. Peak activity occurs in the evening (5–7 PM)**
Users are most active between 5–7 PM, with a secondary peak around midday. This suggests that engagement nudges and challenges timed around the evening window are most likely to align with natural user behavior.

**2. Fairly Active users sit less than Very Active users**
Counterintuitively, Fairly Active users log fewer sedentary hours than Very Active users. Consistent moderate movement distributed throughout the day appears more effective at reducing total sitting time than intense but concentrated workouts.

**3. Users are evenly distributed across activity levels**
27% Fairly Active, 27% Lightly Active, 24% Sedentary, and 21% Very Active — suggesting Bellabeat should design features for all activity levels, not just highly active users.

**4. Activity level strongly correlates with calorie burn**
Very Active users burn approximately 537 more calories daily than Sedentary users (2,554 vs 2,017 kcal). The largest single jump occurs between Lightly Active and Fairly Active segments (332 kcal), suggesting this transition has the highest behavior change impact.

**5. 88% of users wear their device consistently**
29 out of 33 users wore the device for 25+ days. Device adoption is not the challenge — converting passive wearing into active behavior change is.

**6. Steps and sleep have a bidirectional relationship — and it differs by user type**

*→ [View interactive segment breakdown in Tableau Dashboard](https://public.tableau.com/shared/22QGRHM8T?:display_count=n&:origin=viz_share_link)*

The aggregate view shows Poor Sleepers have higher step counts overall. But broken down by segment, the pattern is more nuanced:

| Segment | Same-day: Steps → Sleep Efficiency | Next-day: Sleep Quality → Steps |
|---------|-----------------------------------|----------------------------------|
| Very Active | Poor Sleepers: steps ↑ → efficiency ↑ | Poor sleep → next-day steps ↑ |
| Fairly Active | Poor Sleepers: steps ↑ → efficiency ↑ | Poor sleep → next-day steps decline gradually |
| Lightly Active | Good Sleep and Poor Sleep both stable | Poor Sleep → next-day steps drop rapidly|
| Sedentary | Poor Sleepers: steps ↑ → efficiency surge | Poor sleep → next-day steps drop sharply|

**Interpretation:** For most segments, more steps correlate with better same-day sleep efficiency — movement is not the enemy of sleep. The exception is Sedentary users, whose sleep efficiency surges with higher steps, suggesting strong responsiveness to even modest activity increases. For next-day capacity, poor sleep hits Lightly Active and Sedentary users hardest — their step counts drop sharply the following day. Very Active Poor Sleepers, by contrast, maintain or increase next-day steps, possibly pushing through fatigue. Sleep and activity interact differently by fitness level — one universal step goal cannot address this complexity.

---

## Marketing Recommendations

| # | Focus | Recommendation |
|---|-------|----------------|
| 1 | **Personalized goals** | Set adaptive activity targets by segment — nudge Sedentary users toward gradual step increases (+500 steps/week) rather than pushing a universal 10,000-step goal that may disrupt their sleep |
| 2 | **Evening engagement** | Launch challenges, workouts, or push notifications between 5–7 PM to align with the natural peak activity window |
| 3 | **Recovery positioning** | Market the Leaf as a recovery monitor, not just an activity tracker — highlight sleep tracking as essential for Lightly Active and Sedentary users, where poor sleep directly reduces next-day movement capacity |
| 4 | **Segment conversion** | Focus behavior change efforts on the Lightly → Fairly Active transition, where the calorie benefit jump (332 kcal/day) is largest and most motivating |
| 5 | **Retention** | Re-engagement campaign targeting the 12% of Moderate/Low users with push notifications and streak rewards to build consistent wearing habits |

---

## Known Limitations

- **Small sample size (n = 33)** — findings are directional, not statistically generalizable
- **Data from 2016** — user behavior patterns may have shifted significantly in the past decade
- **No demographic metadata** — no gender, age, or location data; limits direct applicability to Bellabeat's female-focused audience
- **Potential self-selection bias** — participants who consented to share data may already be more health-conscious than average users
- **Sleep data coverage** — only 24 of 33 users have sleep records, limiting the sleep analysis sample

---

## License

MIT License — see [LICENSE](LICENSE) for details.
