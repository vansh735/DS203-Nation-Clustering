# Nation Clustering — DS203 Project (Team 18)

Do countries form meaningful socioeconomic clusters, and do the clusters match World Bank income groups and regions?
We cluster **186 countries (year 2019)** on 11 World Development Indicators with **K-means** and **hierarchical (agglomerative) clustering**, after EDA and PCA. Income group and region are used only to interpret the clusters, never as inputs.

| Team 18 | Roll no. |
|---|---|
| Vansh Goel | 25B3924 |
| Arjun Gupta | 25B3928 |
| Gaurish Bansal | 25B3916 |

## Data

* 11 indicators from `WDICSV.csv`: NY.GDP.PCAP.CD, NY.GDP.MKTP.KD.ZG, FP.CPI.TOTL.ZG, SL.UEM.TOTL.ZS, NE.TRD.GNFS.ZS, NE.GDI.TOTL.ZS, SP.DYN.LE00.IN, SP.URB.TOTL.IN.ZS, IT.NET.USER.ZS, EG.ELC.ACCS.ZS, SE.SEC.ENRR.
* Region, income group and lending category from `WDICountry.csv` (same download). This is the same World Bank classification that the Country API returns (we checked: identical income groups for all 217 countries; region names differ only in wording), and it can be read with `pd.read_csv`.

## Choices

| Step | Choice |
|---|---|
| Countries | 217 after removing aggregates (entries with no region) |
| Year | 2019 (good coverage, before COVID-19 and the 2022 inflation shock) |
| Fill | missing 2019 value taken from 2018, 2020, 2017 or 2021 (in that order), only for slow-moving indicators |
| Removal | countries with 3 or more of 11 indicators still missing (31 removed, 186 kept) |
| Transform | natural log of GDP per capita and trade; inflation and GDP growth capped at the 5th and 95th percentiles |
| Imputation | median (70 cells, 3.4%) |
| Scaling | standardization |
| K-means | k-means++, n_init = 50, random_state = 42, K = 4 (elbow + silhouette) |
| Hierarchical | agglomerative, Ward linkage (main), complete and average linkage for comparison, cut at 4 clusters |

## Main results

| K-means cluster | Countries | GDP per capita | Growth | Unemployment | Investment | Life expectancy |
|---|---|---|---|---|---|---|
| Low development | 34 | US$906 | 5.1% | 3.2% | 22.7% | 62.5 |
| Stagnating, high unemployment | 30 | US$3,785 | 0.5% | 16.1% | 16.0% | 69.5 |
| Dynamic emerging | 54 | US$4,490 | 4.4% | 5.4% | 26.6% | 72.2 |
| High development | 68 | US$30,541 | 1.9% | 4.5% | 23.0% | 81.4 |

* 92% of high-income countries are in "High development" and 86% of low-income countries in "Low development"; middle-income countries split into a dynamic and a stagnating group.
* Ward clustering agrees with K-means on low-development countries, separates a small group of fast-growing, high-investment Asian economies (China, India, Viet Nam, Indonesia, …) and splits rich countries into very rich and Central/Eastern European economies.
