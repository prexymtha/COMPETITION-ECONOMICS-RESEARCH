---
title: "COMPETITION-ECONOMICS-RESEARCH"
output: github_document
---



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

<img width="2233" height="1780" alt="image" src="https://github.com/user-attachments/assets/4c69eb05-af92-40a6-97cc-802bdb4dcb6f" />


<img width="4160" height="1984" alt="sa_merger_notifications" src="https://github.com/user-attachments/assets/af9fd4ff-d9a0-49cd-98d5-ed05577446aa" />

<img width="1935" height="1343" alt="image" src="https://github.com/user-attachments/assets/c0614390-5aa0-418d-9b54-b096a6baa745" />


# MACROECONOMIC VARIABLES 

Explain why we use Nominal GDP ( it's issues with conflating economic activity and price effects in representing changes in national accounts ) , how this links to sales and inventory and m&a activity in the economy , how real gdp solves this or the gdp deflator to account for local nexus and market capitalisation , is gnp an alternative and GDP measured in PPP and what is the economic rationale for choosing that ? Could we have used other variables ? And lastly market capitalisation .

<img width="1484" height="1331" alt="image" src="https://github.com/user-attachments/assets/6b1c76e3-14cc-4a5b-ba36-46b6ed32edee" />

<img width="2385" height="1916" alt="image" src="https://github.com/user-attachments/assets/d7d242ad-a572-4881-8e94-da04c65fa902" />

<img width="4160" height="1984" alt="image" src="https://github.com/user-attachments/assets/3b2cec42-cfb8-4174-b825-186bf29700c7" />



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



Construction of the Transaction Sample
Source
We evaluate four Bloomberg MA<GO> exports totalling 13,483 rows: two queries, split at 20 August 2015, each run twice with different columns. Matching on type, date, target and acquirer, the ticker exports strictly contain the others, which lose 710 deals and add none; shared values agree throughout. We take the ticker exports as the row universe, 7,105 rows.
Bloomberg data are compiled from filings, press releases, news wires and direct submissions, and unlisted firms are covered subject to disclosure, and coverage "can be thinner for smaller or non-reportable transactions". [Transactions near the intermediate threshold are therefore under-represented, so counts of deals sitting just below the notification boundary are lower bounds.] Sector fields return current, not point-in-time, classifications. [A firm acquired in 2003 carries its 2026 sector, so sector results describe classification today rather than at the deal date.]
Defects
Sixteen rows carry a negative turnover across only three distinct values and three firms; we read this as a sign error and correct it. [Left negative, these firms would fail the turnover route automatically and be recorded as asset-driven, so the correction changes which measure is decisive for them.] One deal value is overstated a thousandfold, at forty-eight times the acquirer’s balance sheet, which we flag and do not use. [Deal value enters no statutory test, so this affects descriptive statistics on transaction size only.]
The financial columns are firm-level: of 238 acquirers appearing three or more times, every one carries an identical assets figure across all its deals. [These are each firm’s latest accounts, not its position at announcement. The median transaction predates the export by fifteen years, so early deals are tested at present-day size against thresholds set in their own era, overstating how many cleared them.] And 11 per cent of targets are asset descriptions — mineral blocks, property portfolios, tower sites. [Missingness is concentrated on the target side by construction: 53 of the 70 three-value records lack a target-side figure, so transferred-firm results rest on a smaller, non-random subset.]
Selection
Section 12(1)(b) provides that a merger may be achieved through purchase of shares, an interest, or assets, so we retain deal types M&A, INV and AST. [Excluding asset acquisitions would have narrowed the population below the statutory definition.] We remove 133 repurchases and unbundlings, which record Shareholders as the acquirer and involve no acquisition of control, 106 joint ventures, 15 firms acquiring their own shares, and 166 naming no acquirer or a placeholder. [The repurchases are 28 complete records of large listed firms; retaining them would tilt the sample further towards that group. The placeholders carry no complete records, so that exclusion is immaterial.] We collapse repeated target–acquirer pairs announced within 365 days, keeping the fullest record. [This is the only judgement in the pipeline that materially moves the sample size; a revised bid left uncollapsed would double-weight the same firms.]
Result
This leaves 6,511 unique transactions, of which 476 carry three or four financial values: 406 complete and 70 with three, announced between February 1999 and July 2026. Thirty are Withdrawn or Proposed, which Bloomberg defines as rumoured or non-binding, with no definitive agreement signed; all are complete records and all post-date 2009. We retain them, identifiable by status. [They are not a random thirty: retaining them tilts the sample towards recent large listed transactions. Dropping them gives 446.]
Cleaning funnel
Step	Rows
Raw rows across the four exports	13,483
Ticker exports only	7,105
After exact and boundary duplicates	7,095
Deal types M&A, INV and AST	6,856
Acquirer and target distinct firms	6,841
Acquirer is a named firm	6,675
Unique transactions	6,511
  with 0 of 4 financial values	2,362
  with 1 of 4	398
  with 2 of 4	3,275
  with 3 of 4	70
  with 4 of 4	406
ANALYSIS SAMPLE (3 or 4 values)	476
  of which Withdrawn or Proposed	30
Source: Bloomberg MA<GO>.






