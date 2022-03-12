# Independent t-test

This R markdown document provides an example of performing a regression
using the lm() function in R and compares the output with the ttestIS()
function in the jmv (Jamovi) package.

## Package management in R

``` r
# keep a list of the packages used in this script
packages <- c("tidyverse","rio","jmv")
```

This next code block has eval=FALSE because you don’t want to run it
when knitting the file. Installing packages when knitting an R notebook
can be problematic.

``` r
# check each of the packages in the list and install them if they're not installed already
for (i in packages){
  if(! i %in% installed.packages()){
    install.packages(i,dependencies = TRUE)
  }
  # show each package that is checked
  print(i)
}
```

``` r
# load each package into memory so it can be used in the script
for (i in packages){
  library(i,character.only=TRUE)
  # show each package that is loaded
  print(i)
}
```

    ## -- Attaching packages --------------------------------------- tidyverse 1.3.1 --

    ## v ggplot2 3.3.5     v purrr   0.3.4
    ## v tibble  3.1.6     v dplyr   1.0.7
    ## v tidyr   1.1.4     v stringr 1.4.0
    ## v readr   2.1.1     v forcats 0.5.1

    ## -- Conflicts ------------------------------------------ tidyverse_conflicts() --
    ## x dplyr::filter() masks stats::filter()
    ## x dplyr::lag()    masks stats::lag()

    ## [1] "tidyverse"
    ## [1] "rio"
    ## [1] "jmv"

## t-test is a linear model

The t-test is a type of linear model. We’re going to compare the output
from the lm() function in R with t-test output.To use a categorical
variable in a linear model it needs to be dummy coded. One group needs
to be coded as 0 and the other group needs to be coded as 1.If you
compare the values for F from lm() and t from the t-test you’ll see that
t^2 = F. You should also notice that the associated p values are equal.

## Open data file

The rio package works for importing several different types of data
files. We’re going to use it in this class. There are other packages
which can be used to open datasets in R. You can see several options by
clicking on the Import Dataset menu under the Environment tab in
RStudio. (For a csv file like we have this week we’d use either From
Text(base) or From Text (readr). Try it out to see the menu dialog.)

``` r
# Using the file.choose() command allows you to select a file to import from another folder.
dataset <- rio::import('Invisibility.sav')
# This command will allow us to import a file included in our project folder.
# dataset <- rio::import("Album Sales.sav")
```

## Get R code from Jamovi output

You can get the R code for most of the analyses you do in Jamovi.

1.  Click on the three vertical dots at the top right of the Jamovi
    window.
2.  Click on the Syndax mode check box at the bottom of the Results
    section.
3.  Close the Settings window by clicking on the Hide Settings arrow at
    the top right of the settings menu.
4.  you should now see the R code for each of the analyses you just ran.

## lm() function in R

Many linear models are calculated in R using the lm() function. We’ll
look at how to perform a regression using the lm() function since it’s
so common.

#### Visualization

``` r
ggplot(dataset, aes(x = Mischief))+
  geom_histogram(binwidth = 1, color = "black", fill = "white")+
  facet_grid(Cloak ~ .)
```

![](Independent-t-test-Assignment_files/figure-markdown_github/unnamed-chunk-5-1.png)

``` r
# Make a factor for the box plot
dataset <- dataset %>% mutate(Cloak_f = as.factor(Cloak))
levels(dataset$Cloak_f)
```

    ## [1] "0" "1"

``` r
ggplot(dataset, aes(x = Cloak_f, y = Mischief)) +
  geom_boxplot()
```

![](Independent-t-test-Assignment_files/figure-markdown_github/unnamed-chunk-7-1.png)

#### Computation

``` r
model <- lm(formula = Mischief ~ Cloak, data = dataset)
model
```

    ## 
    ## Call:
    ## lm(formula = Mischief ~ Cloak, data = dataset)
    ## 
    ## Coefficients:
    ## (Intercept)        Cloak  
    ##        3.75         1.25

#### Model assessment

``` r
summary(model)
```

    ## 
    ## Call:
    ## lm(formula = Mischief ~ Cloak, data = dataset)
    ## 
    ## Residuals:
    ##    Min     1Q Median     3Q    Max 
    ## -3.750 -1.000  0.125  1.250  3.000 
    ## 
    ## Coefficients:
    ##             Estimate Std. Error t value Pr(>|t|)    
    ## (Intercept)   3.7500     0.5158   7.270 2.79e-07 ***
    ## Cloak         1.2500     0.7295   1.713    0.101    
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
    ## 
    ## Residual standard error: 1.787 on 22 degrees of freedom
    ## Multiple R-squared:  0.1177, Adjusted R-squared:  0.07764 
    ## F-statistic: 2.936 on 1 and 22 DF,  p-value: 0.1007

#### Standardized residuals from lm()

You might notice lm() does not provide the standardized residuals. Those
must me calculated separately.

``` r
standardized = lm(scale(Mischief) ~ scale(Cloak), data=dataset)
summary(standardized)
```

    ## 
    ## Call:
    ## lm(formula = scale(Mischief) ~ scale(Cloak), data = dataset)
    ## 
    ## Residuals:
    ##      Min       1Q   Median       3Q      Max 
    ## -2.01544 -0.53745  0.06718  0.67181  1.61235 
    ## 
    ## Coefficients:
    ##               Estimate Std. Error t value Pr(>|t|)
    ## (Intercept)  7.110e-18  1.960e-01   0.000    1.000
    ## scale(Cloak) 3.431e-01  2.003e-01   1.713    0.101
    ## 
    ## Residual standard error: 0.9604 on 22 degrees of freedom
    ## Multiple R-squared:  0.1177, Adjusted R-squared:  0.07764 
    ## F-statistic: 2.936 on 1 and 22 DF,  p-value: 0.1007

## function in Jamovi

Compare the output from the lm() function with the output from the
function in the jmv package.

``` r
jmv::ttestIS(
  formula = Mischief ~ Cloak,
  data = dataset,
  vars = Mischief,
  welchs = TRUE,
  mann = TRUE,
  norm = TRUE,
  qq = TRUE,
  eqv = TRUE,
  meanDiff = TRUE,
  effectSize = TRUE,
  desc = TRUE,
  plots = TRUE)
```

    ## 
    ##  INDEPENDENT SAMPLES T-TEST
    ## 
    ##  Independent Samples T-Test                                                                                                                           
    ##  ---------------------------------------------------------------------------------------------------------------------------------------------------- 
    ##                                  Statistic    df          p            Mean difference    SE difference                                 Effect Size   
    ##  ---------------------------------------------------------------------------------------------------------------------------------------------------- 
    ##    Mischief    Student's t       -1.713459    22.00000    0.1006863          -1.250000        0.7295183    Cohen's d                     -0.6995169   
    ##                Welch's t         -1.713459    21.54141    0.1009847          -1.250000        0.7295183    Cohen's d                     -0.6995169   
    ##                Mann-Whitney U     47.00000                0.1491979          -1.000033                     Rank biserial correlation      0.3472222   
    ##  ---------------------------------------------------------------------------------------------------------------------------------------------------- 
    ## 
    ## 
    ##  ASSUMPTIONS
    ## 
    ##  Normality Test (Shapiro-Wilk)          
    ##  -------------------------------------- 
    ##                W            p           
    ##  -------------------------------------- 
    ##    Mischief    0.9649735    0.5460756   
    ##  -------------------------------------- 
    ##    Note. A low p-value suggests a
    ##    violation of the assumption of
    ##    normality
    ## 
    ## 
    ##  Homogeneity of Variances Test (Levene's)            
    ##  --------------------------------------------------- 
    ##                F            df    df2    p           
    ##  --------------------------------------------------- 
    ##    Mischief    0.5448916     1     22    0.4682143   
    ##  --------------------------------------------------- 
    ##    Note. A low p-value suggests a violation of
    ##    the assumption of equal variances
    ## 
    ## 
    ##  Group Descriptives                                                           
    ##  ---------------------------------------------------------------------------- 
    ##                Group    N     Mean        Median      SD          SE          
    ##  ---------------------------------------------------------------------------- 
    ##    Mischief    0        12    3.750000    4.000000    1.912875    0.5521995   
    ##                1        12    5.000000    5.000000    1.651446    0.4767313   
    ##  ----------------------------------------------------------------------------

![](Independent-t-test-Assignment_files/figure-markdown_github/unnamed-chunk-11-1.png)![](Independent-t-test-Assignment_files/figure-markdown_github/unnamed-chunk-11-2.png)
