# Bellabeat Smart Device Analysis
### Google Data Analytics Capstone — Case Study 2

**Tools:** R · Tableau &nbsp;|&nbsp; **Data:** FitBit Fitness Tracker Dataset (Kaggle, CC0) &nbsp;|&nbsp; **Focus product:** Bellabeat Leaf

🔗 **[View full case study →](https://karen-kaiwen.github.io/bellabeat-case-study/)**

---

## Overview

This case study analyzes FitBit smart device usage data to uncover behavioral trends among fitness tracker users. The goal is to apply these insights to Bellabeat's marketing strategy, specifically for the **Leaf wellness tracker** — a health-focused wearable designed for women.

The analysis follows the Google Data Analytics six-phase framework: **Ask → Prepare → Process → Analyze → Share → Act**.

---

## Business Task

> How can trends in smart device usage inform Bellabeat's marketing strategy?

Bellabeat's co-founder Urška Sršen believes analyzing third-party smart device data can unlock new growth opportunities. This analysis focuses on understanding how users engage with their fitness trackers — and what that means for Bellabeat's product positioning and campaign targeting.

---

## Data Source

- **Dataset:** [FitBit Fitness Tracker Data](https://www.kaggle.com/datasets/arashnic/fitbit) (Kaggle, CC0 Public Domain)
- **Coverage:** 33 FitBit users, April 12 – May 12, 2016 (31 days)
- **Contents:** Daily activity, hourly steps, hourly calories, hourly heart rate, sleep logs

**Known limitations:**
- Small sample size (n = 33) — limited statistical generalizability
- No demographic metadata (gender, age, location)
- Data collected in 2016 — behavioral patterns may have shifted
- Potential self-selection bias among participants

---

## Process

### Tools
- **R** — data cleaning, transformation, and exploratory analysis
- **Tableau** — interactive dashboard and visualization

### Cleaning steps
- Removed duplicate rows across all data tables
- Standardized date and time formats for consistent merging
- Created derived columns to classify users into four activity types based on average daily steps
- Merged daily activity table with hourly calorie and heart rate tables on User ID and date

---

## Key Findings

**1. Active users burn ~1,000 more calories per day**
Very Active and Fairly Active users burn approximately 1,000 more daily calories than Sedentary and Lightly Active users — a significant gap that highlights how even moderate increases in movement can meaningfully impact energy expenditure.

**2. Fairly Active users sit less than Very Active users**
Counterintuitively, Fairly Active users log fewer sedentary hours than Very Active users. Consistent, moderate movement distributed throughout the day appears more effective at reducing total sitting time than intense but concentrated exercise sessions.

**3. Heart rate peaks at noon; calorie burn peaks at 5–7 PM**
Heart rate reaches its daily high around 12–16h, while calorie burn peaks later at 17–19h. This delayed metabolic response suggests that midday activity can maximize calorie burn in the hours that follow.

**4. 85% of users are heavy device wearers**
The vast majority of users wear their tracker consistently throughout the day. The engagement challenge is not adoption — it is converting passive wearing into active behavior change.

---

## Recommendations

| # | Focus | Recommendation |
|---|-------|----------------|
| 1 | Audience | Use the ~1,000 kcal gap as a motivational message for low-activity users, framing small changes as meaningful progress |
| 2 | Timing | Send in-app movement reminders at midday (12–14h) when the body is metabolically primed for activity |
| 3 | Product | Position the Leaf as an "all-day movement companion" promoting frequent short activity breaks, not just dedicated workouts |

---

## Dashboard

The Tableau dashboard is available here: *(Tableau Public link — coming soon)*

![Dashboard preview](dashboard.png)

---

## Repository Structure

```
bellabeat-case-study/
├── index.html          # Full case study portfolio page (GitHub Pages)
├── README.md           # This file
├── dashboard.png       # Tableau dashboard screenshot
└── analysis/           # R scripts (coming soon)
```

---

## What's Next

This analysis was completed in R. I am currently rebuilding the data pipeline using **Python and SQL** to strengthen reproducibility and practice industry-standard workflows. The updated scripts will be added to this repository.

---

## Contact

**Kai-Wen Huang**
[LinkedIn](https://www.linkedin.com/in/kai-wen-huang) · [GitHub](https://github.com/karen-kaiwen)
