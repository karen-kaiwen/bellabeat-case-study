# Bellabeat Smart Device Analysis
Analyzing FitBit fitness tracker data to uncover behavioral trends among smart device users, and applying those insights to inform Bellabeat's marketing strategy for the **Leaf wellness tracker**.
 
![R](https://img.shields.io/badge/R-4.0%2B-276DC3?logo=r&logoColor=white)
![tidyverse](https://img.shields.io/badge/tidyverse-1.3%2B-1A162D?logo=r&logoColor=white)
![Tableau](https://img.shields.io/badge/Tableau-E97627?logo=tableau&logoColor=white)
![Google Data Analytics](https://img.shields.io/badge/Google_Data_Analytics-Capstone-4285F4?logo=google&logoColor=white)
![License: MIT](https://img.shields.io/badge/License-MIT-green.svg)
 
🔗 **[View full case study →](https://karen-kaiwen.github.io/bellabeat-case-study/)**
 
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
- [What's Next](#whats-next)
- [License](#license)
---
 
## Project Overview
 
This project is the Capstone Case Study for the Google Data Analytics Certificate. It analyzes publicly available FitBit fitness tracker data to understand how people use smart devices — and how those behavioral patterns can inform the marketing strategy of Bellabeat, a wellness technology company for women.
 
The central business question:
 
> What are the trends in smart device usage, and how can they influence Bellabeat's marketing strategy?
 
The analysis follows the Google Data Analytics six-phase framework: **Ask → Prepare → Process → Analyze → Share → Act**.
 
Data cleaning and analysis were completed in **R (tidyverse)**. The interactive dashboard was built in **Tableau**.
 
---
 
## Analysis Architecture
 
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
 
## Features
 
| Feature | Description |
|---------|-------------|
| 🔍 User activity classification | Classifies 33 users into four activity types based on average daily step count |
| 📅 Date format standardization | Handles both timestamp and date-string formats for accurate cross-table joins |
| ✅ Pre-join data validation | Uses `stopifnot` to check for duplicate IDs before merging; halts execution if anomalies are found |
| 🔗 Cross-table join | Merges daily activity, hourly calories, and hourly heart rate tables on `Id` |
| 📊 User behavior analysis | Compares sedentary hours, active hours, and calorie burn across four user segments |
| 📈 Time-series analysis | Identifies hourly peak patterns and the lag between heart rate and calorie burn |
| 📤 Tableau export | Outputs cleaned data for Tableau dashboard visualization |
 
---
 
## Prerequisites
 
R 4.0 or above, with the following packages installed:
 
```r
install.packages(c("tidyverse", "lubridate", "readxl", "writexl"))
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
| `hourlyCalories_merged.csv` | Hourly calorie expenditure |
| `hourlyHeartrate_merged.csv` | Hourly average heart rate |
 
---
 
## Key Findings
 
**1. Active users burn ~1,000 more calories per day**
Very Active and Fairly Active users burn approximately 1,000 more calories daily than Sedentary and Lightly Active users. Even a moderate increase in daily movement has a meaningful impact on energy expenditure.
 
**2. Fairly Active users are less sedentary than Very Active users**
Counterintuitively, Fairly Active users log fewer sedentary hours than Very Active users. Consistent, moderate movement distributed throughout the day appears more effective at reducing total sitting time than intense but concentrated workouts.
 
**3. Heart rate peaks at noon; calorie burn peaks at 5–7 PM**
Heart rate reaches its daily high around 12–16h, while calorie burn peaks later at 17–19h. This 2–3 hour lag suggests that midday activity triggers elevated calorie burn in the hours that follow.
 
**4. 85% of users wear their device consistently**
The vast majority of users are heavy device wearers. Device adoption is not the challenge — converting passive wearing into active behavior change is.
 
---
 
## Marketing Recommendations
 
| # | Focus | Recommendation |
|---|-------|----------------|
| 1 | Messaging | Use the ~1,000 kcal gap to motivate low-activity users — frame small changes as meaningful progress rather than an overwhelming gap to close |
| 2 | Timing | Send in-app movement reminders at midday (12–14h) to leverage the natural heart rate peak and drive elevated calorie burn through the afternoon |
| 3 | Positioning | Market the Leaf as an "all-day movement companion" promoting frequent short activity breaks — not just a workout tracker — to reach users who don't identify as athletes |
 
---
 
## Known Limitations
 
- Small sample size (n = 33) — findings are directional, not statistically generalizable
- Data collected in 2016 — user behavior patterns may have shifted significantly
- No demographic metadata (gender, age, location) — limits applicability to Bellabeat's female-focused audience
- Potential self-selection bias — participants who consented to share data may already be more health-conscious than average users
---
 
## What's Next
 
This analysis was completed in R. I am currently rebuilding the data pipeline using **Python and SQL** to strengthen reproducibility and practice industry-standard workflows. The updated scripts will be added to this repository.
 
---
 
## License
 
MIT License
