
# Welch's T-test - Lab

## Introduction 

Now that you've gotten a brief introduction to Welch's T-test, it's time to practice your NumPy and math programming skills to write your own Welch's T-test function! 

## Objectives
You will be able to:
* Write a function computing Welch's t-test using Numpy


### Welch's t-Test

Recall that Welch's t-Test is given by  

<img src="images/Welchs-test-for-unequal-variances.png">

where $\bar{X_i}$ , $s_i$, and $N_i$ are the sample mean, sample variance, and sample size, respectively, for sample i.

Write a function for calculatying Welch's t-statistic using two samples a, and b. To help, 2 potential samples are defined below.


```python
import numpy as np

np.random.seed(82)
a = np.random.randn(10) + 2 # +2 for shifting mean 
b = np.random.randn(10) + 2
```


```python
def welch_t(a, b):
    
    """ Calculate Welch's t statistic for two samples. """

    numerator = a.mean() - b.mean()
    
    # “ddof = Delta Degrees of Freedom”: the divisor used in the calculation is N - ddof, 
    #  where N represents the number of elements. By default ddof is zero.
    
    denominator = np.sqrt(a.var(ddof=1)/a.size + b.var(ddof=1)/b.size)
    
    return numerator/denominator

welch_t(a,b)
# 0.41037468596041143
```




    0.41037468596041143



## Degrees of Freedom
Once you have the t-score, you also need to calculate the degrees of freedom to determine the appropriate t-distribution and convert this score into a p-value. The effective degrees of freedom can be calculated using the formula:

<img src="images/Welch-degree of freedom.JPG">


$N_i$ - sample size of sample i  
$s_i$ - variance of sample i  
$v_i$ - degrees of freedom for sample i (equivalent to $N_i$-1   
  
Write a second function to calculate degree of freedom for above samples:


```python
def welch_df(a, b):
    
    """ Calculate the effective degrees of freedom for two samples. """
    
    s1 = a.var(ddof=1) 
    s2 = b.var(ddof=1)
    n1 = a.size
    n2 = b.size
    
    numerator = (s1/n1 + s2/n2)**2
    denominator = (s1/ n1)**2/(n1 - 1) + (s2/ n2)**2/(n2 - 1)
    
    return numerator/denominator

welch_df(a, b)
# 17.86731104513857
```




    17.86731104513857



Now calculate the welch-t score and degrees of freedom from the samples, a and b, using your functions.


```python
#Your code here; calculate t and the degrees of freedom for the two samples, a and b
t = welch_t(treatment, control)
df = welch_df(treatment, control)
print(t,df)
# (1.325675538604432, 8.95372010421334)
```




    (1.325675538604432, 8.95372010421334)



## Converting to a p-Value

Great! Now that you have the t-score and the degrees of freedom, it's time to convert those values into a p-value (for a one-sided $t$-test). Remember that the easiest way to do this is to use the cumulative distribution function associated with your particular t-distribution.  

Calculate the p-value associated with this experiment.


```python
p = 1 - stats.t.cdf(t, df)
p
# 0.10888620005627703
```




    0.10888620005627703



In this case, there is a 10% probability you would see t equal to or greater than what you saw from the data. Given that alpha was set at 0.05, this would not constitute sufficient evidence to reject the null hypothesis.

Building on this, you can also write a function that calculates the p-value for given samples with a two-sided test by taking advantage of the symmetry of the t-distribution to calculate only one side. The two-tailed p-value is simply twice the one-tailed value, because you want the probability:  
>$t<−|t̂|$ and  $t>|t̂|$ , where t̂  is the t-statistic calculated from our data  

With that, define a summative function `p_val_welch(a, b, two_sided=False)` which takes in two samples a and b, as  well as an optional binary variable to allow you to toggle between a one and two-sided Welch's $t$-test.   

> The default behavior should be set to one-sided as indicated above. If the parameter two_sided is set to True, the function should return the p-value for a two-sided $t$-test, as oppossed to a one-sided $t$-test.


```python
def p_value(a, b, two_sided=False):

    "Two-sided t-test for two samples a and b."

    t = welch_t(a, b)
    df = welch_df(a, b)
    
    p = stats.t.cdf(-np.abs(t), df)
    
    if two_sided:
        return 2*p
    else:
        return p
```

Now briefly test your function.


```python
p_value(treatment, control)
#0.10888620005627703
```




    (1.325675538604432, 0.21777240011255405)




```python
p_value(treatment, control, two_side=True)
#0.21777240011255405
```

## Summary

Nice work! In this lab, you practiced implementing formulas for Welch's $t$-test for when sample variances or sample sizes differ in your experimental groups. You also got to review converting $t$-scores into p-values. All of this should continue to build on your abilities to effectively design and carry out statistically valid hypothesis tests.
