# Week 5: Introduction to Statistics in R Worksheet.

A quick note on the layout of this document:

* The file type is a **markdown** file, which allows for Headers, block quotes, inline code and language specific code blocks. It can be opened in R Studio and other applications. If you are interested in generating your own markdown documents, I cannot recommend **Typora** highly enough. It compiles the markdown language in real time to produce nicely formatted code blocks (https://typora.io/). Markdown cheat sheets are widely available online (https://www.code2bits.com/assets/cheat-sheets/cheatsheet-markdown.pdf).
* The issues section of Github (and many other bioinformatics websites) and R Studio use markdown notation to produce nicely formatted posts. I would encourage you to test out markdown syntax.

* In some code blocks you will see I have appended output from R Studio code. It is usually denoted by `[1] output`. This is done for your benefit however:
  * You cannot execute code in a markdown document, 
  * We encourage you to copy and paste code into R Studio and play around with it. You will learn  faster by getting your hands dirty!

## Libraries

First, you need to install and include libraries. 

```{r}
require(ggplot2)
require(dplyr)
require(tidyr)
require(psych)
require(Rmisc)
```

## Descriptive statistics 

Before starting any statistical analysis of your data, it's important to perform an exploratory analysis first. Usually there are simple functions in R which can help you with that. Let's start with some basics part of descriptive statistics in R. 

First of all, we are going to discuss descriptive statistics for a single group, which can be simply presented as vector.

```{r}
# generation of data frame with one column = variable (var):
set.seed(123)
var <- data.frame(var = c(rnorm(40, mean=0.35, sd=1.05), rnorm(120, mean=0.8, sd=2)))
```

We can plot the distribution of this variable.
A <b> distribution </b> in statistics is a function that shows the possible values for a variable and how often they occur. E.g. you can plot it as histogram using ggplot:

```{r}
ggplot(data=var, aes(var)) + 
       geom_histogram(binwidth = 0.8,
                      col = 'green',
                      fill="green", 
                      alpha=.5) +
          labs(title="Histogram for Var", x="Length", y="Count") + 
	  theme(panel.grid.major = element_blank(), 
	  panel.grid.minor = element_blank(),
	  panel.background = element_blank(), 
	  axis.line = element_line(colour = "black"))
```

<img src="https://github.com/triasteran/Data-Science-For-Life-Science/blob/master/Statistics_and_Math_in_R/pictures/dist_hist.png" alt="drawing" width="200"/>

Also you can use density plot to demonstrate data distribution. In a density plot, we attempt to visualize the underlying probability distribution of the data by drawing an appropriate continuous curve which is estimated from the data (usually by kernel density estimation, which will not be covered in this document).

```{r}
ggplot(data=var, aes(var)) + 
  geom_density(col = 'green',
                 fill="green", 
                 alpha=.5) +
        labs(title="Density plot for Var", x="Length", y="Count") + 
	theme(panel.grid.major = element_blank(), 
	panel.grid.minor = element_blank(),
	panel.background = element_blank(), 
	axis.line = element_line(colour = "black"))
```

![alt text](https://github.com/triasteran/Data-Science-For-Life-Science/blob/master/Statistics_and_Math_in_R/pictures/dist_density.png "Distribution of variable Var in form of Density")

### Central tendency 

How would you describe a vector? What first comes to your mind? Most likely, you want to calculate some measure that reflects the 'middle' or the 'average' value of your data. Generally speaking, it's called measures of central tendency. 

The most popular measures are:

 * mean, the average value

```{r}
mean(var$var)
[1] 0.6470099

# if vector has NA:
tmp <- cbind(var$var, NA)

# mean of vector with NA will give NA!
mean(tmp)
[1] NA

# to avoid such behavior:
mean(tmp, na.rm = TRUE)
[1] 0.6470099
```

 * median, the middle value

```{r}
median(var$var)
[1] 0.5166295

# if vector has NA:
median(cbind(var$var, NA))
[1] NA

# to avoid this:
median(cbind(var$var, NA), na.rm = TRUE)
[1] 0.5166295
```

 * mode, the most frequent value.

```{r}
#strangely, but base R does not have a function for mode, so it can be obtained by custom functions, or in additional packages
mode <- function(v) {
   uniqv <- unique(v)
   uniqv[which.max(tabulate(match(v, uniqv)))]
}

apply(var, 2, mode)
[1]        var 
    -0.2384994
```


All this measures are shown on the plot below, red - mean, blue - median and black is mode. 

```{r}
ggplot(data=var, aes(var)) + 
  geom_histogram(breaks=seq(-3, 3, by=0.25),
                 col = 'green',
                 fill="green", 
                 alpha=.5) +
        labs(title="Histogram for Var", x="Length", y="Count") + theme(panel.grid.major = element_blank(), panel.grid.minor = element_blank(),
panel.background = element_blank(), axis.line = element_line(colour = "black")) +
        geom_vline(xintercept=mean(var$var), color='red') +
        geom_vline(xintercept=median(var$var), color='blue') +
        geom_vline(xintercept=apply(var, 2, mode)) 

[1] 
```

![alt text](https://github.com/triasteran/Data-Science-For-Life-Science/blob/master/Statistics_and_Math_in_R/pictures/central_tendency.png "Measures of central tendency")

### Variability 

Besides, you might also want to know how your data is spread out, what are the highest and lowest values. However, taking just minimum and maximum value (which is called range) is not the only way to describe variability of your data. 

* range (min and max of the data)

```{r}
range(var$var)
[1] -3.818338  5.174666
```


* variance

```{r}
var(var$var)
[1] 2.925926
```

* standard deviation 

```{r}
sd(var$var)
[1] 1.710534
```

You definitely need to remember some basic distributions.

##### Bernoulli

```{r}

```

##### Binomial

```{r}

```

##### Poisson

```{r}

```

##### Normal 

```{r}

```

### Quartiles, quantiles, percentiles 

Quartiles are 3 points dividing a dataset into four equal groups, each consisting of 25% of the data. There are lower quartile (Q1), middle quartile (median, Q2) and upper quartile (Q3). 

Surely, you can choose any number of groups to split your data. 
E.g. percentile, which divides a dataset into 100 equal groups. 

* The 5th percentile is the boundary between the smallest 5% of the data and the largest 95% of the data. 
* The median of a dataset is the 50th percentile of the dataset. 
* The 25th percentile is the first quartile, and the 75th percentile the third quartile.

Let's look at the example:

```{r}
#let's generate 22 observations with replacement from values 1:10
set.seed(789)
y <- sample(1:10, 22, replace = TRUE)
x <- data.frame(y = y)
```

In R you can simply use in-built function:

```{r}
#it gives you 0th, 25th, 50th, 75th and 100th percentiles
quantile(y)
[1] 0%   25%   50%   75%  100% 
 	1.00  3.00  5.00  7.75 10.00 

#you can also choose which quantile you want
quantile(y, probs = c(0.05, 0.3, 0.7, 0.96))
[1] 5%  30%  70%  96% 
 	2.0  3.3  6.7 10.0 
```

```{r}
#plot it. Red - lower quartile, blue - middle quartile, purple - upper one.
ggplot(x, aes(y)) + 
  geom_dotplot(binwidth=1, method='histodot') + 
  scale_y_continuous(name = "", breaks = NULL) +
  scale_x_continuous(breaks = seq(1, 10, 1)) +
  labs(title = 'Dotplot') +
  theme(panel.grid.major = element_blank(), 
        panel.grid.minor = element_blank(),
        panel.background = element_blank(), 
        axis.line = element_line(colour = "black")) +
        geom_vline(xintercept=quantile(y, probs=0.25), color='red') +
        geom_vline(xintercept=quantile(y, probs=0.5), color='blue') +
        geom_vline(xintercept=quantile(y, probs=0.75), color='purple') 

[1]
```

![alt text](https://github.com/triasteran/Data-Science-For-Life-Science/blob/master/Statistics_and_Math_in_R/pictures/quartiles.png "Quartiles for discrete distribution")

Let's look at the another example with continuous distribution.
On a plot you can see that Q1,  Q2 and Q3 divide data into 4 intervals with equal probabilities (25% each):

```{r}
#generate contrinuous distribution
set.seed(100)
v <- data.frame(v = rnorm(10000, 0, 1))

#calculate quantiles
Q1 <- quantile(v$v, probs = 0.25)
Q3 <- quantile(v$v, probs = 0.75)
Q2 <- quantile(v$v, probs = 0.5)


p <- ggplot(v, aes(v)) +
          geom_density(fill="white")

# subset region and plot
d <- ggplot_build(p)$data[[1]]
p + geom_area(data = subset(d, x > Q2), aes(x=x, y=y), fill="blue", alpha=0.1) +
  geom_area(data = subset(d, x < Q2), aes(x=x, y=y), fill="blue", alpha=0.1) +
  geom_area(data = subset(d, x > Q3), aes(x=x, y=y), fill="purple", alpha=0.2) +
  geom_area(data = subset(d, x < Q1), aes(x=x, y=y), fill="purple", alpha=0.2) +
geom_text(x=-1.1, y=0.1, label="25%") +
  geom_text(x=-0.3, y=0.1, label="25%") +
  geom_text(x=0.4, y=0.1, label="25%") +
  geom_text(x=1.1, y=0.1, label="25%") +
  geom_text(x=Q1, y=0.015, label="Q1") +
  geom_text(x=Q2, y=0.015, label="Q2") +
  geom_text(x=Q3, y=0.015, label="Q3") +
  theme(panel.grid.major = element_blank(), 
        panel.grid.minor = element_blank(),
        panel.background = element_blank(), 
        axis.line = element_line(colour = "black")) +
  scale_x_continuous(breaks = seq(-4, 4, 1))

[1] 
```

![alt text](https://github.com/triasteran/Data-Science-For-Life-Science/blob/master/Statistics_and_Math_in_R/pictures/quantiles2.png "Quartiles for continuous distribution")

Quartiles are used in the very popular and informative representation of data distribution - <b>boxplot</b>. Lower boundary of the box is Q1, middle (red) is median or Q2, upper boundary is Q3. 

Q3 - Q1 is an <b>interquartile range</b> which is another popular measure of variablility as well as variance and standard deviation. The higher height of the box (IQR = Q3 - Q1), the higher variability of the data. 

Upper dashed line ends up at Q3 + 1.5 * IQR and lower dashed line ends up at Q1 - 1.5 * IQR. All values above and below these boundaries are considered to be outliers - extremely high and low values. 



```{r}
#let's create boxplot using base R 
boxplot(v, medcol="red") 
text(x=1.25, y=Q1, labels='Q1')
text(x=1.3, y=Q2, labels='median')
text(x=1.25, y=Q3, labels='Q3')
text(x=1.25, y=Q1-1.5*(Q3-Q1), labels='Q1-1.5*IQR')
text(x=1.25, y=Q3+1.5*(Q3-Q1), labels='Q3+1.5*IQR')

[1]
```

![alt text](https://github.com/triasteran/Data-Science-For-Life-Science/blob/master/Statistics_and_Math_in_R/pictures/boxplot.png  "Boxplot")

```{r}

#show boxplot points in density plot
IQR <- Q3 - Q1
low_boundary <- Q1 - 1.5*IQR 
high_boundary <- Q3 + 1.5*IQR 

p <- ggplot(v, aes(v)) +
          geom_density(fill="white") + 
          geom_vline(xintercept = low_boundary, 
                color = "blue", size=0.5) + 
          geom_vline(xintercept = high_boundary,
                color = "blue", size=0.5)

# subset region and plot
# LP = Q1 - 1.5*IQR
# HP = Q3 + 1.5*IQR
d <- ggplot_build(p)$data[[1]]
p + geom_area(data = subset(d, x > Q2), aes(x=x, y=y), fill="blue", alpha=0.1) +
  geom_area(data = subset(d, x < Q2), aes(x=x, y=y), fill="blue", alpha=0.1) +
  geom_area(data = subset(d, x > Q3), aes(x=x, y=y), fill="purple", alpha=0.2) +
  geom_area(data = subset(d, x < Q1), aes(x=x, y=y), fill="purple", alpha=0.2) +
  geom_text(x=Q1, y=0.015, label="Q1") +
  geom_text(x=Q2, y=0.015, label="Q2") +
  geom_text(x=Q3, y=0.015, label="Q3") +
  geom_text(x=low_boundary, y=0.015, label="LP") +
  geom_text(x=high_boundary, y=0.015, label="HP") +
  theme(panel.grid.major = element_blank(), 
        panel.grid.minor = element_blank(),
        panel.background = element_blank(), 
        axis.line = element_line(colour = "black")) +
  scale_x_continuous(breaks = seq(-4, 4, 1))

[1]
```

![alt text](https://github.com/triasteran/Data-Science-For-Life-Science/blob/master/Statistics_and_Math_in_R/pictures/bozplot_expl.png  "Boxplot")

### Descriptive statistics for data frame

You can calculate basic different statistics for data frame, using function describe from package psych.  
Pay attention to categorical variables (they are denoted as * in output data frame), they can't have mean or median (it's for numeric variables only). So best practice is to delete them from data frame before applying function describe. 

```{r}
#generate some random dataframe
df <- data.frame(var1 = rbeta(100, 3, 5), 
                 var2 = rpois(100, 4), 
                 var3 = rbinom(100, 20, 0.5),
                 group_var1 = as.factor(sample(c(0,1), replace=TRUE, size=100)),
                 group_var2 = as.factor(sample(seq(1, 5, 1), replace=TRUE, size=100)))

describe(df[,-c(4, 5)])

[1] 
		vars  n mean      sd  median trimmed     mad     min     max
var1	1	100	0.39	0.15	0.39	0.39	0.17	0.07	0.83	
var2	2	100	3.90	1.69	4.00	3.80	1.48	1.00	9.00	
var3	3	100	9.82	2.44	10.00	9.90	1.48	4.00	16.00	
3 rows | 1-5 of 13 columns
```

Besides, descriptive statistics can be obtained for groups:

```{r}
describeBy(x = df[,-c(4, 5)], group = df$group_var1, mat=T, digits = 2)[0:5]

# output is too big, only part is shown
[1]
	 item group1  vars   n  mean      sd  median trimmed     mad
var11	1	   0	 1	49	0.38	0.15	0.35	0.37	0.14	
var12	2	   1	 1	51	0.41	0.16	0.41	0.40	0.15	
var21	3	   0	 2	49	3.65	1.59	3.00	3.51	1.48	

#group by 2 variables
describeBy(x = df[,-c(4, 5)], group = list(df$group_var1, df$group_var2), mat=T, digits = 2)

# output is too big, only part is shown
[1]
     item group1 group2 vars    n   mean      sd  median trimmed
var11	1	   0	  1	   1	9	0.39	0.16	0.37	0.39	
var12	2	   1	  1	   1	15	0.40	0.17	0.36	0.38	
var13	3	   0	  2	   1	9	0.32	0.16	0.33	0.32
```

# Central Limit Theorem (CLT)

<b>The central limit theorem</b> states that if you have a population with mean μ and standard deviation σ and take sufficiently large random samples from the population with replacement, then the distribution of the sample means will be approximately normally distributed with mean = μ and standard mean error (se) = σ/sqrt(n). The higher n, the lower variability of the sample means (distribution of sample means is narrower). 

Random sample should be sufficiently large if population is not normally distributed (n>=30).

Visualization of this theorem you can find here: https://istats.shinyapps.io/sampdist_cont/, https://istats.shinyapps.io/SampDist_discrete/

# Confidence interval for mean 

Let's dive into statistics applications. 

If we want to estimate a parameter, ideally we would compute its value from the whole population. However, in real world we have to deal only with samples from population. What's even worse, we usually have only one sample. 

Confidence intervals can help us. They are mainly used to find a population parameter from the sample data. 

They can be explained in a simple way. e.g. we calculate the 95% confidence interval for a population mean.  As we know from CLT, sample means are distributed normally with mean value equal to the population mean (μ).  So we can use it to determine confidence interval. 95% of all sample means are located in the interval (μ - 1.96*se, μ + 1.96*se) which is the 95%-confidence interval for the population mean (μ). 

The name of the interval sounds always misleading. 
Basically, it means that 95% of all confidence intervals for sample means (infinite number of samples should be drawn!) would include the mean of population. 
To better understand it visually, you can use this link: 
https://istats.shinyapps.io/ExploreCoverage/


```{r}
#let's generate random sample from normal population
set.seed(123)
sample <- rnorm(n=50, mean=0, sd=1)

#do it using base functions ?????????
#c(mean(sample) - qnorm(0.975)*sd/sqrt(n), mean(sample) + qnorm(0.975)*sd #/sqrt(n))

#Find 95% confidence interval
CI(sample, ci = 0.95)
[1] upper        mean       lower 
 0.29753289  0.03440355 -0.22872579 

#Find 99% confidence interval. Important to note, this interval is wider and gives us less narrow estimate. 
CI(sample, ci = 0.99)
[1] upper        mean       lower 
 0.38531053  0.03440355 -0.31650344 
```

