
```{r}
require(ggplot2)
require(dplyr)
require(tidyr)
require(psych)
require(ggpubr)
require(gridExtra)
require(car) #leven test for variance comparison
library(cowplot)

```

# 2-sample compariosn: T-test 

What if we conducted experment with the particular treatment, measured some characteristics and we want to understand whether our treatment works? 

We need to validate somehow our hypothesis. 

In a better world we could use the whole population (all people, all cells, etc.) to determine whether a hypothesis is true, but in practice it is often impossible and we only can deal with random samples - subsets of population. 

<b> Basic steps of statistical analysis </b>

Let's say that in our imaginary experiment we have cell cultures, which have been treated with antibiotic. We also have untreated  control group. You have been observing some parameter of these cultures, e.g. number of cells. 
 
Your question was: 'Has this treatment caused cell death?'

So now, we had the question (a hypithesis), we performed the experiment and now we can apply statistical analysis. 
The best way to understand is to show it on the data. 

Let's upload the data consisting of 2 groups: treated and control cell cultures. Each group is representing the number of alive cells in culture (we have managed somehow to put identical number of cells in each cell-culture dish).
In total, we have 50 obseravtions. It's just imaginary data, so it has nothing to do with real experiments. 

```{r}
df <- read.table('two_sample_ttest.txt')

head(df, 3)
#[1] treated  control
#[1]   1	11	     2		
#[1]   2	 7	     2		
#[1]   3	 8	     3		
```

It's always a good idea to plot your data first. You can get the idea of distribution shape and the difference between 2 groups visually. 

As you can see here, groups can be clearly separated, treated group is shifted to the right and has greater values. 

```{r}
p1 <- df %>% tidyr::gather(group, dead_cells) %>% 
  ggplot(aes(x=dead_cells))+
  geom_histogram(fill='grey', col='black', binwidth = 1) +
  facet_grid(group ~ .) +
  labs(title="Dist. of cell count", x="cell count") +
  theme(panel.grid.major = element_blank(), 
        panel.grid.minor = element_blank(),
        panel.background = element_blank(),
        axis.line = element_line(colour = "black")) +
  theme(text = element_text(size=20))

p2 <- df %>% tidyr::gather(group, dead_cells) %>% 
  ggplot(aes(x=dead_cells, fill=group))+
  geom_density(alpha=0.6) +
  labs(title="Dist. of cell count", x="cell count") +
  theme(panel.grid.major = element_blank(), 
        panel.grid.minor = element_blank(),
        panel.background = element_blank(),
        axis.line = element_line(colour = "black")) +
  theme(text = element_text(size=20))

p3 <- df %>% tidyr::gather(group, dead_cells) %>% 
  ggplot(aes(x = group, y=dead_cells, fill=group))+
  geom_boxplot() +
  theme(panel.grid.major = element_blank(), 
        panel.grid.minor = element_blank(),
        panel.background = element_blank(),
        axis.line = element_line(colour = "black")) +
  theme(text = element_text(size=20))


png("ttest1.png", width = 1300, height = 500)
plot_grid(p1, p2, p3, nrow = 1)
while (!is.null(dev.list()))  dev.off()
```

So, it seems like treated cell cultures have more dead cells than control cell cultures. But how significant this difference is? 
As you might recall from your past courses of statistics, in such case <b>T-test</b> can be used. <b>T-test</b> means that we are going to compare means of groups.  

Let's formulate hypotheses (it's one-sided type):

* H0: mean1 >= mean2
* H1: mean1 > mean2

where <i>mean1</i> is the mean of control and <i>mean2</i> is the mean of treated group. So, average number of alive cells in control is higher than in treated cultures, which means that treatment may cause cell death. 

Another One-sided type would be:

* H0: mean1 >= mean2
* H1: mean1 < mean2

Two-sided hypothesis would be:

* H0: mean1 = mean2
* H1: mean1 != mean2

In this case, H0 means that there is no difference between groups, and treatment does not cause any changes, and alternative one says that treatment can cause either cell reproduction or cell death.

Usually, one-sided type should be used if you discover a statistically significant difference in a particular direction, but not in the other direction. 

???????????
* H0: mean1=mean2
* H1: mean1<mean2
?????????

As a reminder:

* <i>null hypothesis</i> (H0) claims that sample observations (our data) result <i>purely from chance</i>. In the example, it
implies that there is likely to be no difference between groups, they are homogneous, and treatment do nothing.  
* <i>Alternative hypothesis</i> (H1) states that sample observations are <i>influenced by some non-random cause</i>. It means that there is a difference between groups (due to treatment). 

Besides, T-test could be paired on unpaired.

* Paired t-test compares study subjects at 2 different times (paired observations of the same subject)
* Unpaired t-test compares two different independent subjects


```{r}
ttest <- t.test(x = df$treated, #first group
       y = df$control, #second group
       alternative = 'two.sided', #type of the hypotheses
       paired = F, #unpaired test, becuase it's independent cell cultures 
       var.equal =  T #one assumption which should be checked before applying test 
      )


ttest

#[1]Two Sample t-test

#[1]data:  df$treated and df$control
#[1]t = 4.7647, df = 98, p-value = 6.562e-06
#[1]alternative hypothesis: true difference in means is not equal to 0
#[1]95 percent confidence interval:
#[1] 1.388739 3.371261
#[1]sample estimates:
#[1]mean of x mean of y 
#[1]     8.22      5.84 

# we can access different data from t test output, e.g. pvalue: 
ttest$p.value

# T statistic
ttest$statistic

# confidence interval:
ttest$conf.int
```
  
Let's interprete the output.
Generally, you look at only p-value, and draw conclusions like this: "p-value < 0.05, so we have evidence to reject H0 at this level, and it means there is a difference between groups caused not only by chance". But what about t = 4.7647, df = 98 and 95 percent confidence interval? What can these metrics tell us and why are they important too? 

When you perform a t-test, you calculate a specific measure called T-statistic. It basically measures the size of the difference between sample mean of group1 and sample mean of group2 relative to the variation in the sample data. In other words, T-statistic is simply the calculated difference represented in units of standard error. Just look at the formula (for simplification, denominator is just SE, without details, but it represents standard error of 2 samples):

![equation](http://latex.codecogs.com/gif.latex?T-Statistics%3D%5Cfrac%7B(M1-M2)-0%7D%7B(SE)%7D)   

where M1 - mean of group1 and M2 - mean of group2 (in our example, M1 for treated and M2 for control)

As you can see, the greater T, the greater the difference between group means, and the greater evidence against the null hypothesis. The closer T is to 0, the more likely there isn't a significant difference (M1 - M2 = 0 => T = 0, no difference).

 When you perform a t-test for a single study, you obtain a single t-value. However, if we drew multiple random samples of the same size from the same population and performed the same t-test, we would obtain many t-values and we could plot a distribution of all of them. T-statistics follows (surprise!) Student's T-distribution with parameter df = degrees of freedom (it depends on the sample size).

The T-distribution is symmetric and bell-shaped, like the normal distribution, but has heavier tails, meaning that it is more prone to producing values that fall far from its mean: 

```{r}
# png("tdist.png", width = 600, height = 500)

# this is another way to plot distribution in ggplot
# you can define range in dataframe in the main body of ggplot
# and then add stat_function(fun=dt)
# in args you can define parameters of distribution
# e.g. if you want to plot normal distribution, you can use stat_function(fun=dnorm)
ggplot(data.frame(x = c(-5, 5)), aes(x = x)) +
    stat_function(fun = dt, args = list(df = 1)) +
  theme(panel.grid.major = element_blank(), 
        panel.grid.minor = element_blank(),
        panel.background = element_blank(),
        axis.line = element_line(colour = "black")) +
  theme(text = element_text(size=20)) +
  labs(title='T distribution')

#while (!is.null(dev.list()))  dev.off()
```

Let's talk about <i>p-value</i>. T-statistic and p-value are tightly linked. 

T-distributions assume that you draw repeated random samples from a population where <b>the null hypothesis is true</b>.
The peak of the graph is right at zero, which indicates that obtaining a sample value close to the null hypothesis is the most likely.

You place the t-value from your study in the t-distribution to determine how consistent your results are with the null hypothesis. If its far from zero, closer to any tail, it means that it is unlikely to get such samples that are so different from null hypothesis. 

Let's look at the plot with our example.
T-statistic has df=98, and it takes value 4.7647 (as you may have noticed that it's definetely far from 0). 
p-value = 6.562e-06 (super small!). 
If we put value of T-statistic on plot (purple line), we can see, how far it is from 0: 

```{r}
png("ttest2.png", width = 600, height = 500)

set.seed(100)
v <- data.frame(v = rt(90000, df=98))

#calculate quantiles
upper_quantile <- quantile(v$v, probs = 0.95) 

p <- ggplot(v, aes(v)) + geom_density(fill="white")

d <- ggplot_build(p)$data[[1]]

p + geom_area(data = subset(d, x > upper_quantile), 
                   aes(x=x, y=y), 
                   fill="purple", 
                   alpha=0.2) +
  geom_text(x=3, y=0.04, label="5%", size=5) +
  geom_text(x=6.5, y=0.1, label="6.562e-06", size=5) + 
  geom_text(x=6, y=0.4, label="T=4.76", size=5) + 
  geom_vline(xintercept = 4.7647, color = "purple", size=1.5)  + 
  theme(panel.grid.major = element_blank(), 
        panel.grid.minor = element_blank(),
        panel.background = element_blank(), 
        axis.line = element_line(colour = "black")) + xlim(-7, 7) + 
  theme(text = element_text(size=20)) +
  geom_segment(data=data.frame(x = 6, y = 0.08, xend = 5.5, yend = 0.005), mapping=aes(x=x, y=y, xend=xend, yend=yend), arrow=arrow(), size=2, color="darkblue") +
  labs(title='T dist with df=98') 

while (!is.null(dev.list()))  dev.off()

  

```







T-statistic values of larger magnitudes (either negative or positive) are less likely. The far left and right "tails" of the T-distribution curve represent examples of obtaining extreme values of T, far from 0.








As you may remember, the statistic of T-test is distributed by T-distribution. 

This is a formula of T-test statistics (for simplification, denominator is just SE, without details):








In this course we are not going to discuss how to derive this formula.



but basic inderstanding of this is that T-statistics is proportional to the difference between means 

t = 4.7647, df = 98, p-value = 6.562e-06


These are the lower and upper bound of the confidence interval for the mean: 
[1] 95 percent confidence interval:
[1] 1.388739 3.371261

sample estimates:
mean of x mean of y 
     8.22      5.84 








Before applying T-test, we must check underlying assumptions of this test:

* the data are normally distributed. To test this we can use Shapiro-Wilk’s significance test: 
```{r}
shapiro.test(df$treated)
#[1] Shapiro-Wilk normality test

#[1] data:  df$treated
#[1] W = 0.96324, p-value = 0.1215


shapiro.test(df$control)
#[1] Shapiro-Wilk normality test

#[1] data:  df$control
#[1] W = 0.93943, p-value = 0.01279
```

For both groups p-value > 0.05 implying that the distribution of the data are not significantly different from normal distribution. In other words, we can assume normality.

Even if the data are not normal, with large enough sample sizes (n >= 30) the violation of the normality assumption should not cause major problems according to Central Limit Theorem.

Also, important to note, that normality test is sensitive to sample size. Small samples most often pass normality tests. So, it's better to add visual inspection to significance test. 

For this purpose we can use qqPlot (quantile-quantile plot).
It represents the correlation between a given sample and the normal distribution. If all points fall approximately across reference line (a 45-degree line), it means normality. In our case we can say that data are normal.

```{r}
p1 <- ggqqplot(df$treated) + labs(title='treated') + theme(text = element_text(size=30))

p2 <- ggqqplot(df$control) + labs(title='control') + theme(text = element_text(size=30))

#png("qqplot1.png", width = 1000, height = 500)
plot_grid(p1, p2, nrow = 1)
#while (!is.null(dev.list()))  dev.off()

#example with non-normal data
#notice how the points do not fall across the line at the upper end 
set.seed(44)
x <- rgamma(40, shape = 4, rate = 4)
den <- density(x)
dat <- data.frame(x = den$x, y = den$y)
p3 <- ggplot(data = dat, aes(x = x, y = y)) + 
  geom_point(size = 3) +
  theme_classic() + labs(title='density') + theme(text = element_text(size=20))

p4 <- ggqqplot(rgamma(40, 4, 4)) + labs(title='qqplot') +theme(text = element_text(size=20))

#png("qqplot2.png", width = 1000, height = 500)
plot_grid(p3, p4, nrow = 1 )
#while (!is.null(dev.list()))  dev.off()
```

* and the variances of the groups to be compared are homogeneous (equal).

If the samples, being compared, follow normal distribution, to asses the equality of variances you can use Bartlett’s Test or Levene’s Test. 

For Bartlett’s test the data must be normally distributed. The Levene test is an alternative to the Bartlett test that is less sensitive to departures from normality.


```{r}
#p-value is 0.2098, it is not less than 0.05 level of significance, 
#so we can't reject the null hypothesis that the variance is different between groups
bartlett.test(list(df$treated, df$control))

#[1] Bartlett test of homogeneity of variances

#[1] data:  list(df$treated, df$control)
#[1] Bartlett's K-squared = 3.3475, df = 1, p-value = 0.06731
```

Bartlett’s test has the null hypothesis that variances across samples are equal. As p-value (0.06731) > 0.05, we cannot reject the null hypothesis that the variance is the same for both groups. 

We can also test it using Leven test (it’s considered more robust since it is less sensitive to data deviations from normal distribution): 

```{r}
df %>% gather(group, count) %>% mutate(group = factor(group)) -> tmp
leveneTest(tmp$count, tmp$group)

#[1] Levene's Test for Homogeneity of Variance (center = median)
#[1]      Df F value  Pr(>F)  
#[1]group  1  3.0742 0.08267 .
#[1]      98                  
#[1]---
#[1]Signif. codes:  0 ‘***’ 0.001 ‘**’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1
```

P-value (0.08267) > 0.05, there is no evidence to reject null hypothesis about equal variances in populations. 








