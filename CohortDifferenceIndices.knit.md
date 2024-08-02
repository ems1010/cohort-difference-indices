---
title: "Generating Cohort and Difference Indices from linguistic panel data"
author: "Elena Sheard"
format: 
  html:
    theme: flatly
    toc: true
    toc-depth: 3
    toc-location: right
    code-summary: "To view code click here"
    anchor-sections: true
    number-sections: true
    cap-location: margin
    title-block-banner: true
    fig-responsive: true
    lang: 'en-US'
    execute:
    warning: false
    embed-resources: true
    editor: visual
---


# Useful code for applying the Cohort and Difference Indices

This document presents some code chunks that can be used to generate Cohort and Differences Indices from panel data. The approach can be applied to either stand alone panel data or panel data combined with complementary trend data.

## Used libraries


::: {.cell}

```{.r .cell-code}
library(tidyverse)
library(stats)
library(lme4)
library(confintr)
```
:::


## Example linear mixed effects model

The example model below is a linear mixed effects model with a continuous dependent variable (normalised F1 onset measurements). Relevant social and linguistic factors are included as fixed effects, and speaker and word are random intercepts.

As noted in the manuscript, it is essential that panelists are treated as different individuals at each time point in the data, that is, that they are represented as distinct levels within a `speaker` factor. This ensures that they have different intercepts for each time point, from which we can then calculate a difference between them. For example, in the `speaker` column in my data, the panel participants have suffixes indicating which time period the data was collected (e.g., Fabio_1970s and Fabio_2010s).


::: {.cell}

```{.r .cell-code}
model_name <- lmer(
  normalised_F1_onset ~
    age +
    class +
    gender +
    following_context +
    previous_context +
    speech_rate +
    vowel_duration +
    (1 | speaker) +
    (1 | word),
  data = dataframe
)
```
:::


## Extracting random effects and coefficients

The chunk below will extract the random intercept values for each speaker. These values are on a -1 to 1 scale and represent individual speakers' distance from the model's estimated mean of the dependent variable. Each value represents a speaker's distance from the estimated dependent variable for the relevant cohort.


::: {.cell}

```{.r .cell-code}
random_effects <- ranef(model_name)$speaker
names(random_effects)[1] <- "Intercept"
```
:::


## Changing the sign of random intercepts

The code below will swap the 'sign' of the random intercept values extracted with `ranef()`above. The aim is to standardize the meaning of a negative or positive random intercept, specifically, to have *positive* values for speakers ahead in the change, and *negative* values for speakers behind in the change. This step need only be applied when relevant to the variable in question. Namely, when a positive random intercept by default corresponds to a speaker being behind in the change and vice versa.

-   Likely situations where this step will be relevant:

    -   When the direction of change is higher or backer in the vowel space, because default positive random intercepts for F1/F2 measurements will not correspond to the direction of change (higher F1 = a lower vowel, higher F2 = a fronter vowel).

-   Likely situations where this step will not be relevant:

    -   When the direction of change is lower or fronter in the vowel space, because default positive random intercepts for F1/F2 measurements will correspond to the direction of change.


::: {.cell}

```{.r .cell-code}
random_effects$Intercept_Adjusted <-
  random_effects$Intercept * (-1)
```
:::


## Calculating Difference Indices

Once you have the Cohort Indices, you will need to select only the Indices from speakers with more than one intercept. The Difference Indices are most easily calculate using data in a wide format (see below), which can be created using the `pivot_wider()` from `tidyverse` or `dcast()` from `data.table` in R.

| Speaker  | Cohort Index #1(Standardised random intercept from earlier time point) | Cohort Index #2 (Standardised random intercept from later time point) |
|------------------|---------------------------|---------------------------|
| Speaker1 |                                                                        |                                                                       |
| Speaker2 |                                                                        |                                                                       |
| Speaker3 |                                                                        |                                                                       |

One you have the data in the necessary format, you can create a new column with the difference between the two intercepts for each speaker (i.e., the Difference Indices).


::: {.cell}

```{.r .cell-code}
dataframe$Difference_Index <-
  dataframe$RandomIntercept2 - dataframe$RandomIntercept1
```
:::


## Testing a correlation between position relative to change and degree and direction of change over time

It is then very straightforward to test the correlation between earlier linguistic behaviour (Cohort Index 1) and direction and extent of change in real time (Difference Index). I used `cor.test` from the `stats` package but there are a range of options. I also used `ci_cor()` from `confintr` to calculate confidence intervals based on a bootstrap procedure.


::: {.cell}

```{.r .cell-code}
cor.test(dataframe$Difference_Index, 
         dataframe$RandomIntercept1)

ci_cor(
  dataframe$Difference_Index, dataframe$RandomIntercept1,
  probs = c(0.025, 0.975),
  method = c("spearman"),
  type = c("bootstrap"),
  boot_type = c("bca"),
  R = 2000,
  seed = 3
)
```
:::
