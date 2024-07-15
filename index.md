Breast cancer survival analysis with R
================

<br>

### I. Project objective

Breast cancer is one of the most common cancer diagnosed in women. The
primary treatment recommended for most types of breast cancer is surgery
as it is the most effective means of breast cancer removal when it is
technically feasible to remove it. Furthermore, it is recommended in all
stages except the last.

Hence, the objective of this project was to investigate survival of
primary breast cancer patients based on several factors or variables
(patient’s characteristics, tumor characteristics, and treatment
received) and to provide estimates of each of the variable’s hazard
ratios. The starting point for the survival analysis was the patient’s
surgery date.

<br>

### II. Exploratory Data Analysis

**A. Data Collection**

In this project, the in-built dataset “rotterdam” from the survival
package in R was used. This dataset has 2982 records of patients with
primary breast cancers available in the Rotterdam tumor bank. Primary
breast cancer which is breast cancer that has not spread beyond the
breast or the lymph nodes under the arm. The dataset has 15
variables/covariates:

- `pid` : patient identifier
- `year` : year of surgery  
- `age` : patient’s age at surgery
- `meno` : patient’s menopausal status (0 = premenopausal, 1 =
  postmenopausal)
- `size` : the range of tumor sizes (\<=20, 20-50, 50\<)
- `grade` : differentiation grade of tumor
- `nodes` : number of lymph nodes noted to be positive
- `pgr` : biomarker of progesterone receptor (fmol/l)
- `er` : biomarker of estrogen receptor (fmol/l)
- `hormon` : indicates whether hormonal treatment was received or not (0
  = no, 1 = yes)
- `chemo` : indicates whether chemotherapy was received or not
- \`rtime\`\` : days to relapse or lost-to-follow-up
- \`recur\`\` : indicates whether the patient has had a recurrence or
  not (0 = no relapse, 1 = relapse)
- `dtime` : duration of survival or days to death or last follow up
- `death` : indicates whether the patient is alive or not (0 = alive, 1
  = dead)

<br>

**B. Data Exploration**

An initial look at the structure of the dataset (`cancer_df`) shows:

    ## 'data.frame':    2982 obs. of  15 variables:
    ##  $ pid   : int  1 2 3 4 5 6 7 8 9 10 ...
    ##  $ year  : int  1992 1984 1983 1985 1983 1983 1993 1988 1988 1988 ...
    ##  $ age   : int  74 79 44 70 75 52 40 53 60 52 ...
    ##  $ meno  : int  1 1 0 1 1 0 0 1 1 0 ...
    ##  $ size  : Factor w/ 3 levels "<=20","20-50",..: 1 2 1 2 1 1 1 1 1 2 ...
    ##  $ grade : int  3 3 2 3 3 3 2 2 2 3 ...
    ##  $ nodes : int  0 0 0 0 0 0 0 0 0 5 ...
    ##  $ pgr   : int  35 36 138 0 260 139 13 1 627 316 ...
    ##  $ er    : int  291 611 0 12 409 303 4 4 151 976 ...
    ##  $ hormon: int  0 0 0 0 0 0 0 0 0 0 ...
    ##  $ chemo : int  0 0 0 0 0 0 0 0 0 0 ...
    ##  $ rtime : num  1799 2828 6012 2624 4915 ...
    ##  $ recur : int  0 0 0 0 0 0 0 0 0 0 ...
    ##  $ dtime : num  1799 2828 6012 2624 4915 ...
    ##  $ death : int  0 0 0 0 0 0 0 0 0 0 ...

The first 10 records of `cancer_df`:

    ##      pid year age meno  size grade nodes pgr  er hormon chemo rtime recur dtime
    ## 1393   1 1992  74    1  <=20     3     0  35 291      0     0  1799     0  1799
    ## 1416   2 1984  79    1 20-50     3     0  36 611      0     0  2828     0  2828
    ## 2962   3 1983  44    0  <=20     2     0 138   0      0     0  6012     0  6012
    ## 1455   4 1985  70    1 20-50     3     0   0  12      0     0  2624     0  2624
    ## 977    5 1983  75    1  <=20     3     0 260 409      0     0  4915     0  4915
    ## 617    6 1983  52    0  <=20     3     0 139 303      0     0  5888     0  5888
    ## 51     7 1993  40    0  <=20     2     0  13   4      0     0  2491     0  2491
    ## 473    8 1988  53    1  <=20     2     0   1   4      0     0  4150     0  4150
    ## 362    9 1988  60    1  <=20     2     0 627 151      0     0  3919     0  3919
    ## 2182  10 1988  52    0 20-50     3     5 316 976      0     0  3647     0  3647
    ##      death
    ## 1393     0
    ## 1416     0
    ## 2962     0
    ## 1455     0
    ## 977      0
    ## 617      0
    ## 51       0
    ## 473      0
    ## 362      0
    ## 2182     0

<br>

Values of some features were grouped as factors. The feature `age` was
factored as `age.group` with levels “24-60” and “61-90”. The feature
`nodes` was factored as `nodes.group` with levels “0-10”, “11-20”, and
“21-34”. Other features such as `grade`, `meno`, `hormon`, `chemo`,
`recur`, and `death` were also factored into levels “no” and “yes” (see
the ‘F’ added in the names to indicate factored).

Summary statistics of the resulting relevant features are shown below.
There are no missing data in each feature.

    ##       year           age        age.group        nodes        nodes.group 
    ##  Min.   :1978   Min.   :24.00   24-60:1907   Min.   : 0.000   0-10 :2770  
    ##  1st Qu.:1986   1st Qu.:45.00   61-90:1075   1st Qu.: 0.000   11-20: 190  
    ##  Median :1988   Median :54.00                Median : 1.000   21-34:  22  
    ##  Mean   :1988   Mean   :55.06                Mean   : 2.712               
    ##  3rd Qu.:1990   3rd Qu.:65.00                3rd Qu.: 4.000               
    ##  Max.   :1993   Max.   :90.00                Max.   :34.000

    ##       pgr               er            size      gradeF   menoF     
    ##  Min.   :   0.0   Min.   :   0.0   <=20 :1387   2: 794   No :1312  
    ##  1st Qu.:   4.0   1st Qu.:  11.0   20-50:1291   3:2188   Yes:1670  
    ##  Median :  41.0   Median :  61.0   >50  : 304                      
    ##  Mean   : 161.8   Mean   : 166.6                                   
    ##  3rd Qu.: 198.0   3rd Qu.: 202.8                                   
    ##  Max.   :5004.0   Max.   :3275.0

    ##  hormonF    chemoF     recurF     deathF    
    ##  No :2643   No :2402   No :1464   No :1710  
    ##  Yes: 339   Yes: 580   Yes:1518   Yes:1272

<br>

**Univariate plots**

<img src="index_files/figure-gfm/unnamed-chunk-9-1.png" style="display: block; margin: auto;" />
<br>

<img src="index_files/figure-gfm/unnamed-chunk-10-1.png" style="display: block; margin: auto;" />
<br>

<img src="index_files/figure-gfm/unnamed-chunk-11-1.png" style="display: block; margin: auto;" />
<br>

<img src="index_files/figure-gfm/unnamed-chunk-12-1.png" style="display: block; margin: auto;" />
<br>

**Survival data representations and censoring**

In survival analysis, the interest is on analyzing data where the
outcome variable is time to an event, measured from a defined starting
point. In this analysis, the outcome variable is `dtime` which is the
duration of survival or the days to death or last follow-up of breast
cancer patients. The event of interest is death as indicated by the
variable `death` (class 1/yes - dead) and the starting point is the
patient’s surgery indicated by the year of surgery (`year`)and age of
patient at surgery (`age`).

The figure below shows a histogram of `dtime` and count of patients
based on `death` status for the study period.

<img src="index_files/figure-gfm/unnamed-chunk-13-1.png" style="display: block; margin: auto;" />
<br>

In the dataset, censoring occurs when information about a patient’s
survival is incomplete based on the available data. Since there is no
missing value in the `year`, it can be said that there can be right
censoring in the dataset, i.e., some patients may not have died by the
end of study period (alive), some patients might have been lost to
follow up or have died after the study period (censored). These can be
seen in the graphical representations of survival data below.

The figure below shows survival data of randomly selected 30 breast
cancer patients from the dataset. The survival data is represented using
the triplet - entry age, exit age, event. From the starting point which
was the `age` at surgery, the patient was followed until they experience
the event (death) or they are censored. If the patient had a recurrence
(1-relapse) within the study period, it is marked marked by a ‘purple’
dot.

<img src="index_files/figure-gfm/unnamed-chunk-14-1.png" style="display: block; margin: auto;" />

<br>

Another way to present survival data of the 30 sampled patients is using
the pair: follow-up (survival) time, event. This is shown in the figure
below.

<img src="index_files/figure-gfm/unnamed-chunk-15-1.png" style="display: block; margin: auto;" />

Based on the dataset:

- Without recurrence: The longest time until death (death = 1) was 5830
  days (about 16 years) after surgery by a patient who had surgery in
  1983 at 66 years old (recur = 0)

- Without recurrence: The longest time recorded for alive/censored
  (death = 0) was 7043 days (about 19 years) after surgery by a patient
  who had surgery in 1981 at 49 years old (recur = 0).

- With recurrence: The longest time until death (death = 1) was 6233
  days (about 17 years) after surgery by a patient who had surgery in
  1983 at 64 years old. The patient had recurrence 3732 days (about 10
  years) after surgery (recur = 1)

- With recurrence: The longest time recorded for alive/censored (death
  = 0) was 6886 days (about 19 years) after surgery by a patient who had
  surgery in 1980 at 45 years old. The patient had recurrence 2570 days
  (about 7 years) after surgery (recur = 1)

Unfortunately,

- Without recurrence: The shortest time until death (death = 1) was 45
  days after surgery by a patient who had surgery in 1987 at 75 years
  old (recur = 0)

- Without recurrence: The longest time recorded for alive/censored
  (death = 0) was 36 days after surgery by a patient who had surgery in
  1992 at 36 years old (recur = 0). The patient was probably censored,
  loss to follow-up.

- With recurrence: The shortest time until death (death = 1) was 141
  days after surgery by a patient who had surgery in 1988 at 68 years
  old. The patient had recurrence 76 days after surgery (recur = 1)

- With recurrence: The longest time recorded for alive/censored (death
  = 0) was 129 days after surgery by a patient who had surgery in 1990
  at 53 years old. The patient had recurrence 87 days after surgery
  (recur = 1). The patient was probably censored, loss to follow-up.

<br> <br>

### III. Kaplan-Meier Survival Curves and Log Rank Tests

**A. Kaplan-Meier Curves**

Kaplan- Meier curves are non-parametric summary of the probability of
survival past a certain time. The KM survival curve of the breast cancer
patients from the dataset is shown below:

``` r
mod01_sfit <- survfit(Surv(dtime, death) ~ 1, data = cancer_df)
```

<img src="index_files/figure-gfm/unnamed-chunk-21-1.png" style="display: block; margin: auto;" />

    ##   strata median lower upper
    ## 1    All   4033  3888  4309

The median survival time for the breast cancer patients in the dataset
is 4033 days.

Using the KM curve, the 5-year survival rate for primary breast cancer
based on the available data in this dataset is 74.4% and the 10-year
survival rate is 55.2% as shown below.

``` r
summary(mod01_sfit, times = 1825)    #1825 days in 5 years
```

    ## Call: survfit(formula = Surv(dtime, death) ~ 1, data = cancer_df)
    ## 
    ##  time n.risk n.event survival std.err lower 95% CI upper 95% CI
    ##  1825   2084     753    0.744 0.00807        0.728         0.76

``` r
summary(mod01_sfit, times = 3650)    #3650 days in 10 years
```

    ## Call: survfit(formula = Surv(dtime, death) ~ 1, data = cancer_df)
    ## 
    ##  time n.risk n.event survival std.err lower 95% CI upper 95% CI
    ##  3650    687    1171    0.552  0.0104        0.532        0.573

<br>

Separate KM curves can also be plotted and compared for different
variables to explore if different values/levels of the variables affect
survival probabilities. Below are the KM curves for the features
`age.group`, `menoF`, `nodes.group`, `hormonF`, `size`, `chemoF`,
`gradeF`, and `recurF`.

<img src="index_files/figure-gfm/unnamed-chunk-24-1.png" style="display: block; margin: auto;" />
<br>

**B. Log Rank test**

Log rank test was used to determine if differences in the survival
curves of each feature/variable were significant. For instance, using
the log rank test for `gradeF`:

``` r
log.rank.grade <- coxph(Surv(dtime, death) ~ gradeF, data = cancer_df)
summary(log.rank.grade)
```

    ## Call:
    ## coxph(formula = Surv(dtime, death) ~ gradeF, data = cancer_df)
    ## 
    ##   n= 2982, number of events= 1272 
    ## 
    ##            coef exp(coef) se(coef)     z Pr(>|z|)    
    ## gradeF3 0.50600   1.65864  0.06945 7.286 3.19e-13 ***
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
    ## 
    ##         exp(coef) exp(-coef) lower .95 upper .95
    ## gradeF3     1.659     0.6029     1.448       1.9
    ## 
    ## Concordance= 0.55  (se = 0.006 )
    ## Likelihood ratio test= 58.27  on 1 df,   p=2e-14
    ## Wald test            = 53.09  on 1 df,   p=3e-13
    ## Score (logrank) test = 54.22  on 1 df,   p=2e-13

The result shows that the survival curves for `gradeF` are significantly
different for at least some time intervals (low p-value). The median
survival time for those who have tumor differentiation grade 2 was 5653
days while 3700 days for those with tumor grade 3 (shown below and also
in the KM curve).

    ##     strata median lower upper
    ## 1 gradeF=2   5653  4782    NA
    ## 2 gradeF=3   3700  3540  3977

<br>

The table below shows a summary of the log rank test results performed
on the features or covariates. The covariates `er` (estrogen receptor
biomarker) and `chemoF` (indicator of whether the patient received
chemotherapy) both have KM survival curves that have statistically
non-significant firrences.

<table style="border-collapse:collapse; border:none;">
<tr>
<th style="border-top: double; text-align:center; font-style:italic; font-weight:normal; padding:0.2cm; border-bottom:1px solid black; text-align:left; ">
Row
</th>
<th style="border-top: double; text-align:center; font-style:italic; font-weight:normal; padding:0.2cm; border-bottom:1px solid black; ">
Score..logrank..test
</th>
<th style="border-top: double; text-align:center; font-style:italic; font-weight:normal; padding:0.2cm; border-bottom:1px solid black; ">
df
</th>
<th style="border-top: double; text-align:center; font-style:italic; font-weight:normal; padding:0.2cm; border-bottom:1px solid black; ">
p.value
</th>
<th style="border-top: double; text-align:center; font-style:italic; font-weight:normal; padding:0.2cm; border-bottom:1px solid black; ">
Remark
</th>
</tr>
<tr>
<td style=" padding:0.2cm; text-align:left; vertical-align:top; text-align:left; ">
age.group
</td>
<td style=" padding:0.2cm; text-align:left; vertical-align:top; text-align:center; ">
53.4291
</td>
<td style=" padding:0.2cm; text-align:left; vertical-align:top; text-align:center; ">
1
</td>
<td style=" padding:0.2cm; text-align:left; vertical-align:top; text-align:center; ">
0.0000
</td>
<td style=" padding:0.2cm; text-align:left; vertical-align:top; text-align:center; ">
The survival curves have statistically significant differences.
</td>
</tr>
<tr>
<td style=" padding:0.2cm; text-align:left; vertical-align:top; text-align:left; ">
nodes.group
</td>
<td style=" padding:0.2cm; text-align:left; vertical-align:top; text-align:center; ">
220.5267
</td>
<td style=" padding:0.2cm; text-align:left; vertical-align:top; text-align:center; ">
2
</td>
<td style=" padding:0.2cm; text-align:left; vertical-align:top; text-align:center; ">
0.0000
</td>
<td style=" padding:0.2cm; text-align:left; vertical-align:top; text-align:center; ">
The survival curves have statistically significant differences.
</td>
</tr>
<tr>
<td style=" padding:0.2cm; text-align:left; vertical-align:top; text-align:left; ">
pgr
</td>
<td style=" padding:0.2cm; text-align:left; vertical-align:top; text-align:center; ">
23.6988
</td>
<td style=" padding:0.2cm; text-align:left; vertical-align:top; text-align:center; ">
1
</td>
<td style=" padding:0.2cm; text-align:left; vertical-align:top; text-align:center; ">
0.0000
</td>
<td style=" padding:0.2cm; text-align:left; vertical-align:top; text-align:center; ">
The survival curves have statistically significant differences.
</td>
</tr>
<tr>
<td style=" padding:0.2cm; text-align:left; vertical-align:top; text-align:left; ">
er
</td>
<td style=" padding:0.2cm; text-align:left; vertical-align:top; text-align:center; ">
0.5950
</td>
<td style=" padding:0.2cm; text-align:left; vertical-align:top; text-align:center; ">
1
</td>
<td style=" padding:0.2cm; text-align:left; vertical-align:top; text-align:center; ">
0.4405
</td>
<td style=" padding:0.2cm; text-align:left; vertical-align:top; text-align:center; ">
The survival curves have statistically non-significant differences
</td>
</tr>
<tr>
<td style=" padding:0.2cm; text-align:left; vertical-align:top; text-align:left; ">
size
</td>
<td style=" padding:0.2cm; text-align:left; vertical-align:top; text-align:center; ">
280.8799
</td>
<td style=" padding:0.2cm; text-align:left; vertical-align:top; text-align:center; ">
2
</td>
<td style=" padding:0.2cm; text-align:left; vertical-align:top; text-align:center; ">
0.0000
</td>
<td style=" padding:0.2cm; text-align:left; vertical-align:top; text-align:center; ">
The survival curves have statistically significant differences.
</td>
</tr>
<tr>
<td style=" padding:0.2cm; text-align:left; vertical-align:top; text-align:left; ">
gradeF
</td>
<td style=" padding:0.2cm; text-align:left; vertical-align:top; text-align:center; ">
54.2198
</td>
<td style=" padding:0.2cm; text-align:left; vertical-align:top; text-align:center; ">
1
</td>
<td style=" padding:0.2cm; text-align:left; vertical-align:top; text-align:center; ">
0.0000
</td>
<td style=" padding:0.2cm; text-align:left; vertical-align:top; text-align:center; ">
The survival curves have statistically significant differences.
</td>
</tr>
<tr>
<td style=" padding:0.2cm; text-align:left; vertical-align:top; text-align:left; ">
menoF
</td>
<td style=" padding:0.2cm; text-align:left; vertical-align:top; text-align:center; ">
55.0560
</td>
<td style=" padding:0.2cm; text-align:left; vertical-align:top; text-align:center; ">
1
</td>
<td style=" padding:0.2cm; text-align:left; vertical-align:top; text-align:center; ">
0.0000
</td>
<td style=" padding:0.2cm; text-align:left; vertical-align:top; text-align:center; ">
The survival curves have statistically significant differences.
</td>
</tr>
<tr>
<td style=" padding:0.2cm; text-align:left; vertical-align:top; text-align:left; ">
hormonF
</td>
<td style=" padding:0.2cm; text-align:left; vertical-align:top; text-align:center; ">
23.6860
</td>
<td style=" padding:0.2cm; text-align:left; vertical-align:top; text-align:center; ">
1
</td>
<td style=" padding:0.2cm; text-align:left; vertical-align:top; text-align:center; ">
0.0000
</td>
<td style=" padding:0.2cm; text-align:left; vertical-align:top; text-align:center; ">
The survival curves have statistically significant differences.
</td>
</tr>
<tr>
<td style=" padding:0.2cm; text-align:left; vertical-align:top; text-align:left; ">
chemoF
</td>
<td style=" padding:0.2cm; text-align:left; vertical-align:top; text-align:center; ">
0.4947
</td>
<td style=" padding:0.2cm; text-align:left; vertical-align:top; text-align:center; ">
1
</td>
<td style=" padding:0.2cm; text-align:left; vertical-align:top; text-align:center; ">
0.4818
</td>
<td style=" padding:0.2cm; text-align:left; vertical-align:top; text-align:center; ">
The survival curves have statistically non-significant differences
</td>
</tr>
<tr>
<td style=" padding:0.2cm; text-align:left; vertical-align:top; text-align:left; border-bottom: double; ">
recurF
</td>
<td style=" padding:0.2cm; text-align:left; vertical-align:top; text-align:center; border-bottom: double; ">
945.9441
</td>
<td style=" padding:0.2cm; text-align:left; vertical-align:top; text-align:center; border-bottom: double; ">
1
</td>
<td style=" padding:0.2cm; text-align:left; vertical-align:top; text-align:center; border-bottom: double; ">
0.0000
</td>
<td style=" padding:0.2cm; text-align:left; vertical-align:top; text-align:center; border-bottom: double; ">
The survival curves have statistically significant differences.
</td>
</tr>
</table>

<br> <br>

### IV. Cox proportional hazard regression models

Cox regression is used for modelling time-to-event data to relate the
outcome (survival duration or `dtime`) to one or more covariates.

For this anaysis, three Cox regression models were evaluated

1.  Model 1 - Reduced multivariable model
2.  Model 2 - Full multivariable model
3.  Model 3 - Stratified model

Note that Model 3 was developed as a result of the diagnostics test
after comparing Models 1 and 2. The models are detailed below.

<br>

**Model 1 - Reduced multivariable model**

This model fits a Cox regression model that relates time-to-event
(`dtime`, `death`) to a reduced list of relevant covariates, i.e.,
excluding `er` and `chemoF` in the model. The basis of this was the
results of the log rank tests performed above.

``` r
surv.model <- coxph(Surv(dtime, death) ~ age.group + nodes.group + pgr + size +
                      gradeF + menoF + hormonF + recurF, data = cancer_df)
summary(surv.model)
```

    ## Call:
    ## coxph(formula = Surv(dtime, death) ~ age.group + nodes.group + 
    ##     pgr + size + gradeF + menoF + hormonF + recurF, data = cancer_df)
    ## 
    ##   n= 2982, number of events= 1272 
    ## 
    ##                        coef  exp(coef)   se(coef)      z Pr(>|z|)    
    ## age.group61-90    0.2240471  1.2511299  0.0767418  2.919  0.00351 ** 
    ## nodes.group11-20  0.5181303  1.6788857  0.0912903  5.676 1.38e-08 ***
    ## nodes.group21-34  0.0465676  1.0476689  0.2618326  0.178  0.85884    
    ## pgr              -0.0005280  0.9994722  0.0001174 -4.496 6.93e-06 ***
    ## size20-50         0.4245920  1.5289665  0.0640982  6.624 3.49e-11 ***
    ## size>50           0.8332948  2.3008871  0.0881264  9.456  < 2e-16 ***
    ## gradeF3           0.1908043  1.2102226  0.0704163  2.710  0.00674 ** 
    ## menoFYes          0.2422161  1.2740695  0.0794312  3.049  0.00229 ** 
    ## hormonFYes        0.0535491  1.0550088  0.0889749  0.602  0.54728    
    ## recurFYes         1.9372098  6.9393616  0.0789520 24.537  < 2e-16 ***
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
    ## 
    ##                  exp(coef) exp(-coef) lower .95 upper .95
    ## age.group61-90      1.2511     0.7993    1.0764    1.4542
    ## nodes.group11-20    1.6789     0.5956    1.4038    2.0078
    ## nodes.group21-34    1.0477     0.9545    0.6271    1.7502
    ## pgr                 0.9995     1.0005    0.9992    0.9997
    ## size20-50           1.5290     0.6540    1.3485    1.7336
    ## size>50             2.3009     0.4346    1.9359    2.7347
    ## gradeF3             1.2102     0.8263    1.0542    1.3893
    ## menoFYes            1.2741     0.7849    1.0904    1.4887
    ## hormonFYes          1.0550     0.9479    0.8862    1.2560
    ## recurFYes           6.9394     0.1441    5.9445    8.1007
    ## 
    ## Concordance= 0.774  (se = 0.006 )
    ## Likelihood ratio test= 1256  on 10 df,   p=<2e-16
    ## Wald test            = 960.4  on 10 df,   p=<2e-16
    ## Score (logrank) test = 1271  on 10 df,   p=<2e-16

<br>

**Model 2 - Full multivariable model**

This is a more complex model that fits a Cox regression model which
relates time-to-event (`dtime`, `death`) to all relevant covariates from
the dataset.

``` r
surv.model_full <- coxph(Surv(dtime, death) ~ age.group + nodes.group + pgr + er + size +
                      gradeF + menoF + hormonF + chemoF + recurF, data = cancer_df)

summary(surv.model_full)
```

    ## Call:
    ## coxph(formula = Surv(dtime, death) ~ age.group + nodes.group + 
    ##     pgr + er + size + gradeF + menoF + hormonF + chemoF + recurF, 
    ##     data = cancer_df)
    ## 
    ##   n= 2982, number of events= 1272 
    ## 
    ##                        coef  exp(coef)   se(coef)      z Pr(>|z|)    
    ## age.group61-90    0.2577827  1.2940576  0.0781703  3.298 0.000975 ***
    ## nodes.group11-20  0.5116511  1.6680431  0.0914657  5.594 2.22e-08 ***
    ## nodes.group21-34  0.0011451  1.0011458  0.2627343  0.004 0.996522    
    ## pgr              -0.0004742  0.9995260  0.0001215 -3.903 9.48e-05 ***
    ## er               -0.0001919  0.9998081  0.0001128 -1.701 0.088857 .  
    ## size20-50         0.4224261  1.5256585  0.0641180  6.588 4.45e-11 ***
    ## size>50           0.8167511  2.2631351  0.0884674  9.232  < 2e-16 ***
    ## gradeF3           0.1936178  1.2136324  0.0704742  2.747 0.006008 ** 
    ## menoFYes          0.3036060  1.3547351  0.0834620  3.638 0.000275 ***
    ## hormonFYes        0.0568856  1.0585347  0.0889748  0.639 0.522598    
    ## chemoFYes         0.1347615  1.1442639  0.0802104  1.680 0.092938 .  
    ## recurFYes         1.9395115  6.9553524  0.0789935 24.553  < 2e-16 ***
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
    ## 
    ##                  exp(coef) exp(-coef) lower .95 upper .95
    ## age.group61-90      1.2941     0.7728    1.1102    1.5083
    ## nodes.group11-20    1.6680     0.5995    1.3943    1.9956
    ## nodes.group21-34    1.0011     0.9989    0.5982    1.6755
    ## pgr                 0.9995     1.0005    0.9993    0.9998
    ## er                  0.9998     1.0002    0.9996    1.0000
    ## size20-50           1.5257     0.6555    1.3455    1.7300
    ## size>50             2.2631     0.4419    1.9029    2.6916
    ## gradeF3             1.2136     0.8240    1.0571    1.3934
    ## menoFYes            1.3547     0.7382    1.1503    1.5955
    ## hormonFYes          1.0585     0.9447    0.8891    1.2602
    ## chemoFYes           1.1443     0.8739    0.9778    1.3391
    ## recurFYes           6.9554     0.1438    5.9577    8.1200
    ## 
    ## Concordance= 0.775  (se = 0.006 )
    ## Likelihood ratio test= 1262  on 12 df,   p=<2e-16
    ## Wald test            = 963.3  on 12 df,   p=<2e-16
    ## Score (logrank) test = 1275  on 12 df,   p=<2e-16

<br>

**Comparing Models 1 and 2**

A likelihood ratio test was used to compare nested models 1 and 2
(below). The result showed preference for the reduced and simpler model
(Model 1) providing adequate fit (based on p-value).

    ## Analysis of Deviance Table
    ##  Cox model: response is  Surv(dtime, death)
    ##  Model 1: ~ age.group + nodes.group + pgr + size + gradeF + menoF + hormonF + recurF
    ##  Model 2: ~ age.group + nodes.group + pgr + er + size + gradeF + menoF + hormonF + chemoF + recurF
    ##    loglik  Chisq Df Pr(>|Chi|)  
    ## 1 -8899.1                       
    ## 2 -8896.1 5.9876  2     0.0501 .
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1

<br>

**Model Diagnostics**

A key assumption for the Cox regression model is proportional hazards. A
statistical test based on Schoenfeld residuals was used to check if
Model 1 violates the proportionality assumption..

``` r
stat.test <- cox.zph(surv.model)
stat.test
```

    ##               chisq df       p
    ## age.group    8.0457  1  0.0046
    ## nodes.group  4.6195  2  0.0993
    ## pgr         25.1103  1 5.4e-07
    ## size         1.9310  2  0.3808
    ## gradeF       2.4021  1  0.1212
    ## menoF        2.6874  1  0.1011
    ## hormonF      1.4150  1  0.2342
    ## recurF       0.0164  1  0.8982
    ## GLOBAL      39.3971 10 2.2e-05

The result above showed that `age.group` and `pgr` variables violated
the proportional hazard assumption (based on individual and global
p-values \<0.05). Plots of Schoenfeld residuals against `dtime` aare
shown below:

<img src="index_files/figure-gfm/unnamed-chunk-33-1.png" style="display: block; margin: auto;" />

<br>

**Model 3 - Stratified model**

This model fits a Cox regression model that attempts to resolve the
violation of proportionality assumption of Model 1 by stratification,
i.e., splitting the data of violating covariates (`age.group` and `pgr`)
into groups termed strata.

    ## Call:
    ## coxph(formula = Surv(dtime, death) ~ strata(age.group) + nodes.group + 
    ##     strata(pgr) + size + gradeF + menoF + hormonF + recurF, data = cancer_df)
    ## 
    ##   n= 2982, number of events= 1272 
    ## 
    ##                      coef exp(coef) se(coef)      z Pr(>|z|)    
    ## nodes.group11-20  0.58249   1.79049  0.12519  4.653 3.27e-06 ***
    ## nodes.group21-34 -0.23913   0.78731  0.33827 -0.707   0.4796    
    ## size20-50         0.42045   1.52265  0.08476  4.961 7.03e-07 ***
    ## size>50           0.80707   2.24134  0.11721  6.886 5.76e-12 ***
    ## gradeF3           0.18142   1.19892  0.09815  1.848   0.0645 .  
    ## menoFYes          0.20441   1.22681  0.09834  2.079   0.0377 *  
    ## hormonFYes       -0.03654   0.96412  0.11513 -0.317   0.7509    
    ## recurFYes         2.29017   9.87663  0.11157 20.526  < 2e-16 ***
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
    ## 
    ##                  exp(coef) exp(-coef) lower .95 upper .95
    ## nodes.group11-20    1.7905     0.5585    1.4009     2.288
    ## nodes.group21-34    0.7873     1.2701    0.4057     1.528
    ## size20-50           1.5227     0.6567    1.2896     1.798
    ## size>50             2.2413     0.4462    1.7813     2.820
    ## gradeF3             1.1989     0.8341    0.9891     1.453
    ## menoFYes            1.2268     0.8151    1.0117     1.488
    ## hormonFYes          0.9641     1.0372    0.7694     1.208
    ## recurFYes           9.8766     0.1012    7.9367    12.291
    ## 
    ## Concordance= 0.786  (se = 0.012 )
    ## Likelihood ratio test= 858.8  on 8 df,   p=<2e-16
    ## Wald test            = 547.6  on 8 df,   p=<2e-16
    ## Score (logrank) test = 781.9  on 8 df,   p=<2e-16

<br>

Result of the statistical test as shown below (all p-values \>0.05)
confirmed that the Model 3 resolves the violation and selected as the
best-fitting model.

``` r
cox.zph(surv.model.stratified)
```

    ##               chisq df    p
    ## nodes.group 3.84023  2 0.15
    ## size        0.26048  2 0.88
    ## gradeF      2.31471  1 0.13
    ## menoF       0.00695  1 0.93
    ## hormonF     1.95171  1 0.16
    ## recurF      0.02358  1 0.88
    ## GLOBAL      9.03427  8 0.34

<br>

**Hazard Ratio**

The Cox regression model gives estimates of the **hazard ratios**. The
hazard rate describes, in this analysis, the relative probability of
`death` occuring at `dtime`, given that `death` hasn’t happened up until
time `dtime`.

The figure below shows the forest plot for Model 3 presenting the hazard
ratios of the covariates for breast cancer patients in the dataset.

<img src="index_files/figure-gfm/unnamed-chunk-36-1.png" style="display: block; margin: auto;" />
<br>

The following can be interpreted:

- In general, every hazard ratio (HR) represents a relative risk of
  death that compares one of the categories of the covariate to the
  baseline “reference”. A HR \<1 means that there is a decreased hazard
  (or risk) of death (the outcome event) and a HR \>1 means that there
  is an increased hazard (or risk) of death, relative to the reference.
  Hence, this also implies that if the 95% confidence interval for HR
  include the value ‘1’, it means that the resulting p-value will be
  high (\> 0.05) which would mean that the difference to the baseline
  “reference” is not significant. This is true for `nodes.group` 21-34
  category, `gradeF` grade 3 category, and `hormonF` Yes category.

- In terms of number of positive lymph nodes (`nodes.group`): After
  adjusting to other variables, the hazard of dying is 79% higher for
  patients with 11-20 positive nodes compared to those with 0-10
  positive nodes (HR = 1.79, 95% CI: 1.4-2.3).

- In terms of tumor size range (`size`): After adjusting to other
  variables, the hazard of dying is 52% higher for patients with tumor
  size range of 20-50 compared to those with tumor size range of \<= 20
  (HR = 1.52, 95% CI: 1.29-1.8). The hazard of dying is 124% higher for
  patients with tumor size range of \>= 50 compared to those with tumor
  size range of \<= 20 (HR = 2.24, 95% CI: 1.78-2.8).

- In terms of patient’s menopausal status (`menoF`): After adjusting to
  other variables, the hazard of dying is 23% higher for patients
  postmenopausal (1-Yes) compared to those premenopausal (0-No) (HR =
  1.23, 95% CI: 1.01-1.5).

- In terms of whether the patient had a recurrence or not (`recurF`):
  After adjusting to other variables, the hazard of dying is very high,
  about 888% higher (almost 9x higher) for who had relapse compared to
  those who did not (HR = 9.88, 95% CI: 7.94-12.3).

<br> <br>

### V. Insights

In this project, the survival rate of primary breast cancer patients
were determined based on the available data in the dataset. The hazard
ratios for each variable (covariate) were estimated with a stratified
Cox regression model. From the results, the number of positive lymph
nodes, tumor size, menaupausal status and recurrence (relapse) are the
main factors that have significant effect on the survival rate of the
patients as shown in the above findings in terms of hazard ratios .

<br> <br> <br> <br>
