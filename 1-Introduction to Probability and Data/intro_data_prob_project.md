---
title: "Exploring the BRFSS data"
output: 
  html_document: 
    fig_height: 4
    highlight: pygments
    theme: spacelab
---


### Load packages


```r
library(ggplot2)
library(dplyr)
```

### Load data


```r
load("brfss2013.RData")
```

* * *

## Part 1: Data

## The Behavioral Risk Factor Surveillance System (BRFSS) is a collaborative project between all of the States in the United States (US) and participating US territories and the Centers for Disease Control and Prevention (CDC). BRFSS is an ongoing surveillance system designed to measure behavioral risk factors for non-institutionalized adult population (18 years and older) residing in the US. The BRFSS was initiated in 1984, with 15 States collecting surveillance data on risk behaviors through monthly telephone interviews. Over time, the number of States participating in the survey increased, including also the District of Columbia, Puerto Rico, Guam, US Virgin Islands, American Samoa, Federated States of Micronesia, and Palau. They collect survey data over a limited point-in-time (usually one to three months). In this document, the term “state” is used to refer to all areas participating in BRFSS.

## Factors assessed by the BRFSS in 2013 include tobacco use, HIV/AIDS knowledge and prevention, exercise, immunization, health status, healthy days — health-related quality of life, health care access, inadequate sleep, hypertension awareness, cholesterol awareness, chronic health conditions, alcohol consumption, fruits and vegetables consumption, arthritis burden, and seatbelt use. In conducting the BRFSS landline telephone survey, interviewers collect data from a randomly selected adult in a household. In conducting the cellular telephone version of the BRFSS questionnaire, interviewers collect data from an adult who participates by using a cellular telephone and resides in a private residence or college housing.

## In 2013, additional question sets were included as optional modules to provide a measure for several childhood health and wellness indicators, including asthma prevalence for people aged 17 years or younger.

* * *

## Part 2: Research questions

**Research question 1:**
## Is there any relation between time spent Walking, Running, Jogging or Swimming, and education levels or sex?

**Research question 2:**
## I want to evaluate the possible correlation between sleep time vs depression + veterans. Is being veteran affecting depressive disorder and sleep balance or not?

**Research question 3:**
## How does fruit consumption vary based on income, health care coverage and routine checkup frequency?

* * *

## Part 3: Exploratory data analysis


**Research question 1:**
## First of all I'm going to rename the levels for the variable "educa". They're very very long! And then compare the result with summaries.

```r
summary(brfss2013$educa)
```

```
##                   Never attended school or only kindergarten 
##                                                          677 
##                              Grades 1 through 8 (Elementary) 
##                                                        13395 
##                        Grades 9 though 11 (Some high school) 
##                                                        28141 
##                       Grade 12 or GED (High school graduate) 
##                                                       142971 
## College 1 year to 3 years (Some college or technical school) 
##                                                       134197 
##                   College 4 years or more (College graduate) 
##                                                       170120 
##                                                         NA's 
##                                                         2274
```

```r
brfss2013$educa <- factor(brfss2013$educa, levels = c("Never attended school or only kindergarten", "Grades 1 through 8 (Elementary)", "Grades 9 though 11 (Some high school)", "Grade 12 or GED (High school graduate)", "College 1 year to 3 years (Some college or technical school)", "College 4 years or more (College graduate)"), labels = c("Never", "Elementary", "Some HS", "HS graduate", "Some college", "College graduate"))

summary(brfss2013$educa)
```

```
##            Never       Elementary          Some HS      HS graduate 
##              677            13395            28141           142971 
##     Some college College graduate             NA's 
##           134197           170120             2274
```
## A NA filtered selection assigned to "EduSport". Then a look for the first and last observations.

```r
EduSport <- select(brfss2013, X_state, educa, sex, exeroft1) %>%
     filter(!is.na(educa), !is.na(sex), !is.na(exeroft1))

head(EduSport)
```

```
##   X_state            educa    sex exeroft1
## 1 Alabama     Some college Female      105
## 2 Alabama      HS graduate Female      205
## 3 Alabama College graduate Female      102
## 4 Alabama      HS graduate Female      220
## 5 Alabama     Some college Female      102
## 6 Alabama College graduate   Male      102
```

```r
tail(EduSport)
```

```
##            X_state            educa    sex exeroft1
## 326871 Puerto Rico     Some college   Male      202
## 326872 Puerto Rico College graduate Female      103
## 326873 Puerto Rico College graduate   Male      202
## 326874 Puerto Rico     Some college Female      204
## 326875 Puerto Rico     Some college Female      104
## 326876 Puerto Rico College graduate   Male      103
```
## A barplot filtered by sex to visualize the distribution of sport. 
## For lower education levels sexes are equally distributed, but for higher levels there are much more females.

```r
ggplot(data = EduSport, aes(x = educa, col = sex)) +
        geom_bar() +
        facet_grid(. ~ sex) +
        theme(axis.text.x = element_text(angle = 45)) +
        xlab("Education Level") +
        ylab("Count") +
        labs(color = "Sex")
```

![plot of chunk unnamed-chunk-3](figure/unnamed-chunk-3-1.png)
## Finally, using mean function on plot, the outcome is that males spend a bit more time than females. The only exception is for the lowest level.

```r
ggplot(data = EduSport) +
        geom_bar(aes(x = educa, y = exeroft1, col = sex), stat = "summary", fun.y = "mean") +
        facet_grid(. ~ sex) +
        theme(axis.text.x = element_text(angle = 45)) +
        xlab("Education Level") +
        ylab("Amount Sport") +
        labs(color = "Sex")
```

![plot of chunk unnamed-chunk-4](figure/unnamed-chunk-4-1.png)


**Research question 2:**
## To begin, selection assigned to "VeteranDep" and check for first six rows

```r
VeteranDep <- brfss2013 %>%
     select(addepev2, sleptim1, veteran3) %>%
     filter(!is.na(addepev2), !is.na(sleptim1), !is.na(veteran3))
head(VeteranDep)
```

```
##   addepev2 sleptim1 veteran3
## 1      Yes        6       No
## 2      Yes        9       No
## 3       No        8       No
## 4       No        6       No
## 5       No        8       No
## 6       No        7       No
```
## Looking at jitter plot, seems there's no evidence of correlation among being veteran with depression/sleep disorder

```r
ggplot(VeteranDep, aes(x = veteran3, y = sleptim1, col = addepev2)) + 
        geom_jitter() +
        xlab("Veteran") +
        ylab("Sleep Time") +
        labs(color = "Depression")
```

![plot of chunk unnamed-chunk-6](figure/unnamed-chunk-6-1.png)
## Confirmed by table below.

```r
VeteranDep %>%
     group_by(veteran3, addepev2) %>%
     summarise(count = n(), MeanSleep = mean(sleptim1)) %>%
     mutate(freq = count / sum(count))
```

```
## Source: local data frame [4 x 5]
## Groups: veteran3 [2]
## 
##   veteran3 addepev2  count MeanSleep      freq
##     <fctr>   <fctr>  <int>     <dbl>     <dbl>
## 1      Yes      Yes   9757  6.753408 0.1618345
## 2      Yes       No  50533  7.172382 0.8381655
## 3       No      Yes  84001  6.893549 0.1993502
## 4       No       No 337373  7.082058 0.8006498
```
## Looking in another way. There's no difference between veterans and other people. Who sufferes a depressive disorder in general sleeps a little bit less. Confirmed as per below.

```r
VeteranDep %>%
      group_by(addepev2, veteran3) %>%
      summarise(count = n(), MeanSleep = mean(sleptim1)) %>%
      mutate(freq = count / sum(count))
```

```
## Source: local data frame [4 x 5]
## Groups: addepev2 [2]
## 
##   addepev2 veteran3  count MeanSleep      freq
##     <fctr>   <fctr>  <int>     <dbl>     <dbl>
## 1      Yes      Yes   9757  6.753408 0.1040658
## 2      Yes       No  84001  6.893549 0.8959342
## 3       No      Yes  50533  7.172382 0.1302713
## 4       No       No 337373  7.082058 0.8697287
```


**Research question 3:**
## First check: any NA? Yes. 
## After, factors' names' lenght reduction for "income2" and "checkup1"
## Third: selection assigned to "fich" (Fruit Income Check up Health coverage)

```r
anyNA(brfss2013$income2)
```

```
## [1] TRUE
```

```r
anyNA(brfss2013$fruit1)
```

```
## [1] TRUE
```

```r
anyNA(brfss2013$hlthpln1)
```

```
## [1] TRUE
```

```r
anyNA(brfss2013$checkup1)
```

```
## [1] TRUE
```


## Then a jitter plot with facet grid, in order to cross the four variables.
## Looking from bottom left to the up right corner we appreciate an increase of fruit consumption in the wealthier groups, with more recurrent routine checkups. 
## Having Healt Care Coverage increases fruit consumption in almost all of the groups, but the bottom left facet grids.

```r
ggplot(fich, aes(x = fruit1, y = hlthpln1, col = hlthpln1)) +
        geom_jitter(position = position_jitter(height = 0)) +
        facet_grid(checkup1 ~ income2) +
        xlab("Fruit Consumption") +
        ylab("Health Care Coverage") +
        labs(color = "") +
        theme(axis.text.x = element_text(angle = 90))
```

![plot of chunk unnamed-chunk-10](figure/unnamed-chunk-10-1.png)

