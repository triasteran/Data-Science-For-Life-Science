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
