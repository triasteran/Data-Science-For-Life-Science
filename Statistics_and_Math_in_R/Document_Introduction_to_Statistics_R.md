# Week 5: Introduction to Statistics in R Worksheet.

A quick note on the layout of this document:

* The file type is a **markdown** file, which allows for Headers, block quotes, inline code and language specific code blocks. It can be opened in R Studio and other applications. If you are interested in generating your own markdown documents, I cannot recommend **Typora** highly enough. It compiles the markdown language in real time to produce nicely formatted code blocks (https://typora.io/). Markdown cheat sheets are widely available online (https://www.code2bits.com/assets/cheat-sheets/cheatsheet-markdown.pdf).
* The issues section of Github (and many other bioinformatics websites) and R Studio use markdown notation to produce nicely formatted posts. I would encourage you to test out markdown syntax.

* In some code blocks you will see I have appended output from R Studio code. It is usually denoted by `[1] output`. This is done for your benefit however:
  * You cannot execute code in a markdown document, 
  * We encourage you to copy and paste code into R Studio and play around with it. You will learn  faster by getting your hands dirty!

# Calculations

Let’s start with the basic syntax for mathematical calculations in R. R performs addition, subtraction, multiplication, division, exponentiation and modulo with `*` `-` `+` `/` `^` `%%`.

An example is given below:

```{R}
# Results in "500"
573 - 74 + 1
[1] 500

# Results in "50"
25 * 2
[1] 50

# Results in "2"
10 / 5
[1] 2
```

# Descriptive statistics 

Before starting any statistical anaylysis of your data, it's important to perform an exploratory analysis first. Usually there are simple functions in R which can help you with that. Let's start with some basics part of descriptive statistics in R. 

First of all, we are going to discuss descriptive statistics for a single group, which can be simply presented as vector.

Generation of dataframe with one column = variable (var):

```{r}
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
        labs(title="Histogram for Var", x="Length", y="Count") + theme(panel.grid.major = element_blank(), panel.grid.minor = element_blank(),
panel.background = element_blank(), axis.line = element_line(colour = "black"))
```

![alt text](https://github.com/triasteran/Data-Science-For-Life-Science/blob/master/Statistics_and_Math_in_R/pictures/dist_hist.png "Distribution of variable Var in form of Histogram")

Also you can use density plot to demonstate data distribution. In a density plot, we attempt to visualize the underlying probability distribution of the data by drawing an appropriate continuous curve which is estimated from the data (usually by kernel density estimation, which will not be covered in this document).

```{r}
ggplot(data=var, aes(var)) + 
  geom_density(col = 'green',
                 fill="green", 
                 alpha=.5) +
        labs(title="Density plot for Var", x="Length", y="Count") + theme(panel.grid.major = element_blank(), panel.grid.minor = element_blank(),
panel.background = element_blank(), axis.line = element_line(colour = "black"))
```




