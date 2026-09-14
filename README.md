# COMPETITION-ECONOMICS-RESEARCH

This repository contains code for analysing the evolution of South Africa’s merger notification thresholds from 1999 to 2026. Using Bloomberg Mergers &amp; Acquisitions (M&amp;A) data, together with CPI, nominal and real GDP, and market capitalisation as benchmarks, we compare actual threshold paths to counterfactual scenarios against other countries

# DATA 
When validating our data do we look for proportions of intermediate to large or do we check how it matches the Commission's Data Set.
<img width="1313" height="590" alt="image" src="https://github.com/user-attachments/assets/ad2cdf29-9b28-4597-abf4-758a4e0538d5" />



# BLOOMBERG DATA 

With the bloomberg data I want to classify transactions that would meet the thresholds based on turnover alone , then based on assets alone , and those that would have based on the combined value.When comparing this to the macro variables we may need to use rolling averages , logging the data and structural breaks [check OECD paper for inspiration , they used 3-point rolling average ]

What values does our data take and how often ?

<img width="1634" height="2105" alt="image" src="https://github.com/user-attachments/assets/f32c030a-5ebf-42fb-a5ba-cc873ce5ed1d" />

<img width="2084" height="1781" alt="image" src="https://github.com/user-attachments/assets/9a45ef57-ee21-4443-8036-85f9bb2501a8" />

<img width="2382" height="1931" alt="image" src="https://github.com/user-attachments/assets/a30c716c-ebe0-49b9-abb0-6bf8e6576117" />


<img width="1933" height="882" alt="image" src="https://github.com/user-attachments/assets/716ad680-4a29-4dac-a505-bf26989f0c47" />



<img width="4160" height="1984" alt="sa_merger_notifications" src="https://github.com/user-attachments/assets/af9fd4ff-d9a0-49cd-98d5-ed05577446aa" />

# MACROECONOMIC VARIABLES 

Explain why we use Nominal GDP ( it's issues with conflating economic activity and price effects in representing changes in national accounts ) , how this links to sales and inventory and m&a activity in the economy , how real gdp solves this or the gdp deflator to account for local nexus and market capitalisation , is gnp an alternative and GDP measured in PPP and what is the economic rationale for choosing that ? Could we have used other variables ? And lastly market capitalisation .

<img width="1484" height="1331" alt="image" src="https://github.com/user-attachments/assets/6b1c76e3-14cc-4a5b-ba36-46b6ed32edee" />

Good general question to pause on. After initial inspection (structure, missingness, summary stats, distribution plots), the standard workflow moves through roughly these stages — I'll frame each with the R tools you'd use, since that's the point of this exercise:

**1. Data cleaning decisions, made explicit**
This is where you decide — and document — how to handle what inspection revealed: missing values (drop? impute? flag?), outliers (keep? cap? investigate individually, like we did with those investment-holding companies?), duplicates, and inconsistent categories (e.g. sector labels that mean the same thing but are spelled differently). The key discipline here is writing the *rule*, not just applying a fix once — `mutate()`, `filter()`, `case_when()` are your main tools, and every decision should be something you could defend in a methods section.

**2. Feature engineering / variable construction**
Building the variables your actual analysis needs from the raw columns — ratios, logs, growth rates, date-derived fields (year, quarter, regime), categorical bucketing. You've already done a version of this (log-transforming turnover/assets, YoY growth rates, threshold regime matching).

**3. Bivariate/relationship exploration**
Before modeling, look at how variables relate to each other — correlation matrices (`cor()`, `corrplot`), scatter plots, grouped summaries (`group_by() %>% summarise()`), cross-tabs for categoricals (`table()`). This is where you'd start asking "does deal size correlate with regime?" or "do INV and M&A deals have different turnover distributions?" — relationship questions, not just single-variable distribution questions.

**4. Formal statistical/econometric tests, where relevant**
Depending on your research question: normality tests if a method assumes it, stationarity tests (ADF/KPSS) if you're doing anything time-series, tests for structural breaks (relevant for your regime-change framing specifically).

**5. Model specification and estimation**
This is where your actual method lives — for your dissertation, that's likely the classification/threshold analysis itself, and potentially panel or regression work depending on how the paper argues its contribution. This stage depends entirely on your research question, which is why it comes last, not first.

**6. Diagnostics and robustness**
After estimating anything: residual checks, sensitivity to specification choices, robustness to alternative samples (e.g. 3-value vs 4-value-only subsets, INV vs M&A separately) — checking that your result isn't an artifact of one modeling choice.

**7. Communicating results**
Tables, figures, and writing them up in a way a reader can audit — which loops back to habits like the ones we've been building (documenting *why* a row was dropped, not just that it was).

Where are you hoping to go next — deeper into the exploration/relationship stage (stage 3), or are you ready to think about the classification/threshold test itself (stage 5)?


