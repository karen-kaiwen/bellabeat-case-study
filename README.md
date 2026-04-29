# Bellabeat Smart Device Analysis
Analyzing FitBit fitness tracker data to uncover behavioral trends among smart device users, and applying those insights to inform Bellabeat's marketing strategy for the **Leaf wellness tracker**.
 
![R](https://img.shields.io/badge/R-4.0%2B-276DC3?logo=r&logoColor=white)
![Python](https://img.shields.io/badge/Python-3.10%2B-3776AB?logo=python&logoColor=white)
![PostgreSQL](https://img.shields.io/badge/PostgreSQL-Supabase-4169E1?logo=postgresql&logoColor=white)
![tidyverse](https://img.shields.io/badge/tidyverse-1.3%2B-1A162D?logo=r&logoColor=white)
![Tableau](https://img.shields.io/badge/Tableau-E97627?logo=tableau&logoColor=white)
![Google Data Analytics](https://img.shields.io/badge/Google_Data_Analytics-Capstone-4285F4?logo=google&logoColor=white)
![License: MIT](https://img.shields.io/badge/License-MIT-green.svg)
 
🔗 **[View R Markdown Case Study →](https://karen-kaiwen.github.io/bellabeat-case-study/)**
🐍 **[View Python + SQL Notebook (Kaggle) →](https://www.kaggle.com/code/kaiwenhuang123/bellabeat-case-study)**
📊 **[View Tableau Dashboard →](https://public.tableau.com/shared/22QGRHM8T?:display_count=n&:origin=viz_share_link)**
 
---
 
## Table of Contents
- [Project Overview](#project-overview)
- [Analysis Architecture](#analysis-architecture)
- [Technical Challenges & Solutions](#technical-challenges--solutions)
- [Features](#features)
- [Prerequisites](#prerequisites)
- [Data Source](#data-source)
- [Key Findings](#key-findings)
- [Marketing Recommendations](#marketing-recommendations)
- [Known Limitations](#known-limitations)
- [License](#license)
---
 
## Project Overview
 
This project is the Capstone Case Study for the Google Data Analytics Certificate. It analyzes publicly available FitBit fitness tracker data to understand how people use smart devices — and how those behavioral patterns can inform the marketing strategy of Bellabeat, a wellness technology company for women.
 
The central business question:
 
> What are the trends in smart device usage, and how can they influence Bellabeat's marketing strategy?
 
The analysis follows the Google Data Analytics six-phase framework: **Ask → Prepare → Process → Analyze → Share → Act**.
 
This project was completed in **two versions** to demonstrate proficiency across multiple tools:
 
| Version | Tools | Link |
|---------|-------|------|
| V1 | R (tidyverse), Tableau | [R Markdown Report](https://karen-kaiwen.github.io/bellabeat-case-study/) |
| V2 | Python (pandas, plotly), SQL (PostgreSQL / Supabase) | [Kaggle Notebook](https://www.kaggle.com/code/kaiwenhuang123/bellabeat-case-study) |
 
---
 
## Analysis Architecture
 
### V1 — R Pipeline
```
┌─────────────────────────────┐
│   FitBit Fitness Tracker    │
│   Dataset (Kaggle, CC0)     │
│                             │
│  · dailyActivity_merged     │
│  · hourlyCalories_merged    │
│  · hourlyHeartrate_merged   │
└──────────────┬──────────────┘
               │
               ▼
     Data Cleaning & Transformation (R)
     · Remove duplicate rows
     · Standardize date formats
     · Classify users by activity level
               │
               ▼
     Cross-table Join (left_join)
     by = "Id" + "ActivityDate"
               │
      ┌────────┴────────┐
      ▼                 ▼
  User Segment       Hourly Trend
  Analysis           Analysis
  · Sedentary hrs    · Heart rate vs. calories
  · Active hrs       · Peak hour identification
  · Calorie burn
      │                 │
      └────────┬────────┘
               ▼
       Tableau Dashboard
       · User Type Distribution
       · Device Engagement
       · Hourly Calorie Rhythm
       · Heart Rate vs. Calories
               │
               ▼
       3 Marketing Recommendations
```
 
### V2 — Python + SQL Pipeline
```
┌─────────────────────────────┐
│   FitBit Fitness Tracker    │
│   Dataset (Kaggle, CC0)     │
└──────────────┬──────────────┘
               │
               ▼
     Data Cleaning (Python / pandas)
     · Type conversion
     · Duplicate removal
     · Hourly downsampling (heartrate)
     · Multi-file merge (hourly tables)
               │
               ▼
     PostgreSQL on Supabase
     · 4 tables loaded via SQLAlchemy
               │
               ▼
     SQL Analysis (PostgreSQL)
     · User segmentation (CASE WHEN)
     · Calorie comparison (JOIN + AVG)
     · Wear frequency (COUNT DISTINCT)
     · Sleep efficiency (Window Function LEAD)
               │
               ▼
     Visualization (Python / Plotly)
     · Donut chart
     · Bar charts
     · Grouped bar chart
```
 
---
 
## Technical Challenges & Solutions
 
**Defining a consistent user activity classification**
 
Problem: The raw dataset contains no activity level labels. A single day's step count is easily skewed by rest days, illness, or travel, making it an unreliable classification basis.
 
Solution: Used each user's **average daily step count** across all 31 days as the classification metric, referencing the Tudor-Locke step count framework to assign users to four categories: Sedentary / Lightly Active / Fairly Active / Very Active. This approach smooths out single-day outliers.
 
---
 
**Mismatched join keys between daily and hourly tables**
 
Problem: `dailyActivity` uses a date-only column, while `hourlyCalories` and `hourlyHeartrate` use full timestamps. A direct join would expand each daily row into 24 hourly rows, inflating the dataset.
 
Solution: Truncated hourly timestamps to date using `as.Date()` before joining. All three tables were then merged on `Id` + date using `left_join`, preserving the correct row count.
 
---
 
**Estimating device wear time without explicit data**
 
Problem: The dataset does not record actual device wear time, making it impossible to directly classify users as heavy, moderate, or light wearers.
 
Solution: Estimated daily wear time by summing `SedentaryMinutes + LightlyActiveMinutes + FairlyActiveMinutes + VeryActiveMinutes`. Users were then categorized as Light [0–10h), Moderate [10–20h), or Heavy [20–24h] wearers based on their daily average.
 
---
 
**Sleep quality measurement (V2)**
 
Problem: Sleep duration alone is an incomplete measure of sleep quality — a user who spends 9 hours in bed but only sleeps 6 hours is not well-rested.
 
Solution: Calculated **sleep efficiency** (total minutes asleep ÷ total time in bed) as a more meaningful metric. Users above 90% efficiency were classified as Good Sleep; below as Poor Sleep. Used PostgreSQL `LEAD()` window function to align sleep data with the following day's activity.
 
---
 
## Features
 
| Feature | Description |
|---------|-------------|
| 🔍 User activity classification | Classifies 33 users into four activity types based on average daily step count |
| 📅 Date format standardization | Handles both timestamp and date-string formats for accurate cross-table joins |
| ✅ Pre-join data validation | Uses `stopifnot` to check for duplicate IDs before merging; halts execution if anomalies are found |
| 🔗 Cross-table join | Merges daily activity, hourly calories, and hourly heart rate tables on `Id` |
| 📊 User behavior analysis | Compares sedentary hours, active hours, and calorie burn across four user segments |
| 📈 Time-series analysis | Identifies hourly peak patterns and the lag between heart rate and calorie burn |
| 😴 Sleep efficiency analysis | Uses SQL LEAD() to correlate sleep efficiency with next-day activity (V2) |
| 🗄️ Cloud database integration | Loads cleaned data into PostgreSQL on Supabase via SQLAlchemy (V2) |
| 📤 Tableau / Plotly export | Interactive dashboards in both Tableau (V1) and Plotly (V2) |
 
---
 
## Prerequisites
 
**V1 — R:**
```r
install.packages(c("tidyverse", "lubridate", "readxl", "writexl"))
```
 
**V2 — Python:**
```bash
pip install pandas psycopg2-binary sqlalchemy python-dotenv plotly kagglehub
```
 
---
 
## Data Source
 
- **Dataset:** [FitBit Fitness Tracker Data](https://www.kaggle.com/datasets/arashnic/fitbit) (Kaggle, CC0 Public Domain)
- **Users:** 33 FitBit users
- **Period:** April 12 – May 12, 2016 (31 days)
- **Tables used:**
| File | Contents |
|------|----------|
| `dailyActivity_merged.csv` | Daily steps, active minutes, sedentary minutes, calorie burn |
| `sleepDay_merged.csv` | Sleep duration and time in bed |
| `hourlySteps/Calories/Intensities_merged.csv` | Hourly activity breakdown |
| `heartrate_seconds_merged.csv` | Heart rate (downsampled to hourly in V2) |
 
---
 
## Key Findings
 
**1. Active users burn ~537 more calories per day**
Very Active users burn approximately 537 more calories daily than Sedentary users (2,554 vs 2,017 kcal). The largest single jump occurs between Lightly Active and Fairly Active segments (332 kcal), suggesting this transition has the highest impact.
 
**2. Fairly Active users are less sedentary than Very Active users**
Counterintuitively, Fairly Active users log fewer sedentary hours than Very Active users. Consistent, moderate movement distributed throughout the day appears more effective at reducing total sitting time than intense but concentrated workouts.
 
**3. Heart rate peaks at noon; calorie burn peaks at 5–7 PM**
Heart rate reaches its daily high around 12–16h, while calorie burn peaks later at 17–19h. This 2–3 hour lag suggests that midday activity triggers elevated calorie burn in the hours that follow.
 
**4. High activity may reduce sleep efficiency (V2 — new finding)**
Users classified as Poor Sleep (efficiency < 90%) show significantly higher step counts both on the same day (10,469 vs 8,359) and the next day (9,320 vs 8,361). This suggests that excessive activity may impair sleep quality, highlighting the importance of balancing movement with recovery.
 
**5. 88% of users wear their device consistently**
29 out of 33 users wore their device for 25+ days out of 31. Device adoption is not the challenge — converting passive wearing into active behavior change is.
 
---
 
## Marketing Recommendations
 
| # | Focus | Recommendation |
|---|-------|----------------|
| 1 | Messaging | Use the calorie gap to motivate low-activity users — frame small changes as meaningful progress rather than an overwhelming gap to close |
| 2 | Timing | Send in-app movement reminders at midday (12–14h) to leverage the natural heart rate peak and drive elevated calorie burn through the afternoon |
| 3 | Positioning | Market the Leaf as an "all-day movement companion" promoting frequent short activity breaks — not just a workout tracker |
| 4 | Recovery | Promote Bellabeat's sleep tracking as a recovery monitor, not just a sleep logger — helping users find the optimal balance between activity and rest |
| 5 | Retention | Re-engagement campaign targeting the 12% of Moderate/Low users with push notifications and streak rewards to build consistent wearing habits |
 
---
 
## Known Limitations
 
- Small sample size (n = 33) — findings are directional, not statistically generalizable
- Data collected in 2016 — user behavior patterns may have shifted significantly
- No demographic metadata (gender, age, location) — limits applicability to Bellabeat's female-focused audience
- Potential self-selection bias — participants who consented to share data may already be more health-conscious than average users
---
 
## License
 
MIT License
