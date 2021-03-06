---
layout: post
title: BRFSS Data exploration
---

### Load packages

    library(ggplot2)
    library(dplyr)

### Load data

    load("brfss2013.RData")

------------------------------------------------------------------------

Part 1: Data
------------

The BRFSS has conducted monthly surveys since 1984 and these survey have
been conducted using landline and cellular (since 2011). It is mentioned
that landline surveys were randomly done, hence suggesting that there is
possibility we can search for correlation between variables in data.
This is not the case for cellular as the data obtained from cellular can
tend to be biased (since it is not randomly asked rather asked to a
specific group of people who reside in a private residence or college
housing).

No random assignment exists in BRFSS scenario, suggesting that there
would be no scope for causal relations. BRFSS dataset contains 330
variables and 491775 observations

------------------------------------------------------------------------

Part 2: Research questions
--------------------------

**Research quesion 1:** What state and gender has the highest
depression? This relation is of interest as this information can be used
to identify those states and gender with highest depression and address
it accordingly.

The variables of interest are 'addepev2'(Ever told you had a depressive
disorder), '\_state'(the state they were from), and 'sex' (gender of the
respondent).

**Research quesion 2:** For the year 2014 which state has the lowest
average sleep time? This question is of interest as low sleep time can
be a result of stress, health issues, etc

The variables of interest are 'iyear', '\_state', and 'sleptim1'.

**Research quesion 3:** Is there a relation between having blood
cholesterol checked, ever told blood pressure high and being diagnosed
with heart attack. We are interested in this analysis to see if
respondents who did not have them checked were diagnosed with heart
attack or not, if that is the case we want to have them checked more and
prevent heart attack.

The variables of interest are 'bphigh4','toldhi2', and 'cvdinfr4'.

------------------------------------------------------------------------

Part 3: Exploratory data analysis
---------------------------------

**Research quesion 1:** What state and gender has the highest
depression? (top 7)

    brfssq1<- brfss2013 %>% group_by(X_state, addepev2,sex) %>% summarise(count = n()) %>% filter(addepev2 == "Yes", !is.na(addepev2), !is.na(sex)) %>% arrange(desc(count))

    head(brfssq1,7)

    ## Source: local data frame [7 x 4]
    ## Groups: X_state, addepev2 [7]
    ## 
    ##         X_state addepev2    sex count
    ##          <fctr>   <fctr> <fctr> <int>
    ## 1       Florida      Yes Female  4645
    ## 2        Kansas      Yes Female  3085
    ## 3 Massachusetts      Yes Female  2310
    ## 4      Nebraska      Yes Female  2149
    ## 5     Minnesota      Yes Female  1961
    ## 6          Utah      Yes Female  1956
    ## 7          Ohio      Yes Female  1869

The above code has grouped state, depressed or not and gender and have
found the count in descending order. The top 7 states and gender where
depression is highest.

    ggplot(data = head(brfssq1,7), aes(x=X_state, y =count))+geom_bar(stat="identity")+geom_text(aes(label=sex),vjust=1.5, colour ="white")

![](BRFSS_files/figure-markdown_strict/unnamed-chunk-2-1.png)

As observed from the data, the top 7 states and genders are displayed
and it is keen to note that it is females that are facing depression
more in all these states.

**Research quesion 2:** For the year 2014 which state has the lowest
average sleep time? (lowest 7)

    brfssq2 <- brfss2013 %>% group_by(X_state)%>%filter(!is.na(sleptim1),!is.na(X_state),iyear=="2014") %>% summarise(Average=mean(sleptim1)) %>% arrange(Average)

    brfssq2

    ## # A tibble: 38 × 2
    ##      X_state  Average
    ##       <fctr>    <dbl>
    ## 1   Michigan 4.500000
    ## 2     Oregon 5.333333
    ## 3    Alabama 5.500000
    ## 4   Kentucky 6.000000
    ## 5   Arkansas 6.500000
    ## 6    Arizona 6.700000
    ## 7      Texas 6.700000
    ## 8       Guam 6.707317
    ## 9     Nevada 6.730769
    ## 10 Minnesota 6.800000
    ## # ... with 28 more rows

The above data represents the lowest average sleep time in each state
for the year 2014. The output is arranged in ascending order (lease to
highest) and saved into brfssq2

    ggplot(data = head(brfssq2,7),aes(x=X_state, y=Average, group= 1)) + geom_point() + geom_line(color = "blue") + geom_hline(yintercept = 6,linetype = "dashed", color="red")

![](BRFSS_files/figure-markdown_strict/unnamed-chunk-4-1.png)

Here we have considered a threshold average sleep time to be 6 hours and
sleep time below that is considered very less and there would some
health related problems existing or building up in those states.

**Research quesion 3:** Is there a relation between having blood
cholesterol checked, ever told blood pressure high and being diagnosed
with heart attack?

    brfssq3 <- brfss2013 %>% group_by(bphigh4,toldhi2, cvdinfr4) %>% summarise(count= n()) %>% filter(!is.na(bphigh4), !is.na(toldhi2), !is.na(cvdinfr4))

    brfssq3

    ## Source: local data frame [16 x 4]
    ## Groups: bphigh4, toldhi2 [8]
    ## 
    ##                                       bphigh4 toldhi2 cvdinfr4  count
    ##                                        <fctr>  <fctr>   <fctr>  <int>
    ## 1                                         Yes     Yes      Yes  15944
    ## 2                                         Yes     Yes       No  95250
    ## 3                                         Yes      No      Yes   5017
    ## 4                                         Yes      No       No  68463
    ## 5  Yes, but female told only during pregnancy     Yes      Yes     52
    ## 6  Yes, but female told only during pregnancy     Yes       No    760
    ## 7  Yes, but female told only during pregnancy      No      Yes     21
    ## 8  Yes, but female told only during pregnancy      No       No   1842
    ## 9                                          No     Yes      Yes   3422
    ## 10                                         No     Yes       No  64636
    ## 11                                         No      No      Yes   3160
    ## 12                                         No      No       No 154267
    ## 13        Told borderline or pre-hypertensive     Yes      Yes     98
    ## 14        Told borderline or pre-hypertensive     Yes       No   1850
    ## 15        Told borderline or pre-hypertensive      No      Yes     72
    ## 16        Told borderline or pre-hypertensive      No       No   2504

Here the code is grouping whether they were checked for high bp
(bphigh4), whether the were checked for high cholesterol (bloodcho), and
if they were diagnosed with heart attach (cvdinfr4). Some peculiar
observations about the results can be made, first is that blood
cholesteral is not high (bloodcho = 'No')and blood pressure is not high
(toldhi2='No'),the number of people diagnosed with heart attach is 3160.

If we modify the code a bit to see which state has more number of people
who have got heart attack when they had no blood pressure and no
cholesterol level, we get the following code:

    brfssq3plot <- brfss2013 %>% group_by(X_state, bphigh4,toldhi2, cvdinfr4) %>% summarise(count= n()) %>% filter(!is.na(bphigh4), !is.na(toldhi2), !is.na(cvdinfr4), bphigh4=="No", toldhi2=="No", cvdinfr4=="Yes") %>% arrange(desc(count))

    ggplot(data=head(brfssq3plot,7), aes(x=X_state, y= count, group=1))+geom_line()+ylim(0,300)

![](BRFSS_files/figure-markdown_strict/unnamed-chunk-7-1.png)

The plot explains states(top 7) where people got a heart attack even
though they were diagnosed with no blood pressure and no blood
cholesterol.
