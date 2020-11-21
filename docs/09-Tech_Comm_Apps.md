---
title: "09-App_Tech_Comm"
author: "Tim Anderson"
date: "July 4, 2017"
output:
  html_document: default
  pdf_document: default
---




# Revisiting _Technology Commercialization_

## Introduction

In 2002, Sten Thore edited a book, *Technology Commercialization:  DEA and Related Analytical Methods for Evaluating the Use and Implementation of Technological Innovation.*  This book compiled a series of cases applying Data Envelopment Analysis to realworld technology management situations.  This chapter revisits some of those cases. We will provide a snapshot of the cases and the interested reader is referred to Thore's book for more details.

Before we begin, let's install some small helper functions.  These are described in more detail in an Appendix.


```r
source("Helperfiles.R")
# knitr::read_chunk('Helperfiles.R') <<poscolfunct>> This reads in a chunk that
# defines the poscol function This function will filter out columns that are zero
# More precisely, it factors out column with column sums that are zero.  This is
# helpful for tables of lambda values in DEA.
source("Helperfiles.R")
# knitr::read_chunk('Helperfiles.R') <<DrawIOdiagramfunction>>
```

## Prioritizing R&D Activities

### Directional Drilling R&D Projects

Chapter 2 of Thore, by Thore and Rich, covers a series of small cases covers around R&D projects.  The first case begins on page 62 with an application from Baker Hughes Corporation.  They were considering 14 cases related "directional drilling", which as a technology, combined with hydraulic facturing, years later resulted in the natural gas boom.  

Thore and Rich use a one-input, four-output variable returns to scale DEA model.  The input was expected cost in millions of dollars.  The outputs are estimated market size in millions of dollars (Y1), strategic compatability with existing products (Y2), projected market demand in millions of dollars (Y3), and competitive intensity (Y4).  

Let's start by defining the data from page 63 of Thore and Rich.    


```r
NX <- 1
NY <- 4
ND <- 14

DMUnames <- list(c(LETTERS[1:ND]))  # DMU names: A, B, ...
Xnames <- lapply(list(rep("X", NX)), paste0, 1:NX)  # Input names: x1, ...
Ynames <- lapply(list(rep("Y", NY)), paste0, 1:NY)  # Output names: y1, ...
Vnames <- lapply(list(rep("v", NX)), paste0, 1:NX)  # Input weight names: v1, ...
Unames <- lapply(list(rep("u", NY)), paste0, 1:NY)  # Output weight names: u1, ...
SXnames <- lapply(list(rep("sx", NX)), paste0, 1:NX)  # Input slack names: sx1, ...
SYnames <- lapply(list(rep("sy", NY)), paste0, 1:NY)  # Output slack names: sy1, ...
Lambdanames <- lapply(list(rep("L_", ND)), paste0, LETTERS[1:ND])

XBH <- matrix(c(1.07, 1.06, 0.325, 1.6, 0.55, 0.2, 0.35, 0.53, 0.21, 0.16, 0.07, 
    1.95, 5.59, 3.1), ncol = NX, dimnames = c(DMUnames, Xnames))

YBH <- matrix(c(32, 50, 40, 30, 25, 8, 2, 12, 10, 0.8, 3, 300, 60, 240, 8.2, 7.6, 
    7.6, 7.1, 7, 6, 5.9, 5.8, 5.8, 5.4, 5.3, 6.8, 6.2, 6.2, 7.5, 7.2, 7.1, 7.2, 7, 
    6.1, 6.2, 5.8, 5.8, 5.6, 5.4, 6.1, 6.9, 6.6, 8, 6.4, 5.3, 5.5, 5.1, 6.9, 6.6, 
    5.4, 4.7, 6.1, 6.5, 6.4, 6.8, 7.1), ncol = NY, dimnames = c(DMUnames, Ynames))

XFigNames <- "X1 ($M)"
YFigNames <- c("Y1 - Market Size ($M)", "Y2 - Strategic Compat. (1-10)", "Y3 - Market Demand ($M)", 
    "Y4 - Compet. Intensity (1-10)")
Figure <- DrawIOdiagram(XFigNames, YFigNames, "\"\n\nBCC-IO\n\n \"")

tmp <- capture.output(rsvg_png(charToRaw(export_svg(Figure)), "IO-BakerHughes.png", 
    height = 1440))
```

![Baker Hughes Input-Output Model](IO-BakerHughes.png){#fig:IO-BakerHughes width=50% }


```r
pander(cbind(XBH, YBH), caption = "Data for Baker Hughes Corporation case.")
```


----------------------------------------
 &nbsp;    X1     Y1    Y2    Y3    Y4  
-------- ------- ----- ----- ----- -----
 **A**    1.07    32    8.2   7.5    8  

 **B**    1.06    50    7.6   7.2   6.4 

 **C**    0.325   40    7.6   7.1   5.3 

 **D**     1.6    30    7.1   7.2   5.5 

 **E**    0.55    25     7     7    5.1 

 **F**     0.2     8     6    6.1   6.9 

 **G**    0.35     2    5.9   6.2   6.6 

 **H**    0.53    12    5.8   5.8   5.4 

 **I**    0.21    10    5.8   5.8   4.7 

 **J**    0.16    0.8   5.4   5.6   6.1 

 **K**    0.07     3    5.3   5.4   6.5 

 **L**    1.95    300   6.8   6.1   6.4 

 **M**    5.59    60    6.2   6.9   6.8 

 **N**     3.1    240   6.2   6.6   7.1 
----------------------------------------

Table: Data for Baker Hughes Corporation case.

```r
# Displays table of inputs and outputs
```


Now, let's run DEA.  Feel free to pick a package.  We explored some packages in chapter 8.  For now, let's try the MultiplierDEA package.  Let's look over the results.


```r
library(MultiplierDEA)
```

```
## Loading required package: lpSolveAPI
```

```r
resBH <- DeaMultiplierModel(XBH, YBH, rts = "vrs", orientation = "input")

# Rename some of the results row and column labels
dimnames(resBH$Lambda) <- c(Lambdanames, Lambdanames)
dimnames(resBH$vx) <- c(DMUnames, Xnames)
dimnames(resBH$uy) <- c(DMUnames, Ynames)

pander(poscol(cbind(resBH$Efficiency, resBH$Lambda)), caption = "Envelopment results for Baker Hughes Corporation analysis.")
```


-----------------------------------------------------------------------
 &nbsp;    Eff      L_A      L_C       L_F       L_K       L_L     L_N 
-------- -------- -------- -------- --------- --------- --------- -----
 **A**      1        1        0         0         0         0       0  

 **B**    0.6544   0.3848   0.5612   0.00323      0      0.0507     0  

 **C**      1        0        1         0         0         0       0  

 **D**    0.3195    0.25     0.75       0         0         0       0  

 **E**    0.5636     0      0.9412      0      0.05882      0       0  

 **F**      1        0        0         1         0         0       0  

 **G**    0.596      0      0.1645   0.7434    0.09211      0       0  

 **H**    0.2488     0      0.2349      0      0.7641    0.00104    0  

 **I**    0.619      0      0.2353      0      0.7647       0       0  

 **J**    0.625      0      0.1177      0      0.8823       0       0  

 **K**      1        0        0         0         1         0       0  

 **L**      1        0        0         0         0         1       0  

 **M**    0.1363   0.3708   0.2809   0.2315       0      0.1168     0  

 **N**      1        0        0         0         0         0       1  
-----------------------------------------------------------------------

Table: Envelopment results for Baker Hughes Corporation analysis.

The results are consistent with those reported in Sten and Thore.  Note that projects (DMU's) A, C, and F are efficient and all other projects use those three projects in setting their own targets of performance as denoted by non-zero values of lambda.  

Now, let's look at the other side of the analysis - the multiplier model. 


```r
pander(cbind(resBH$Efficiency, resBH$vx, resBH$uy), caption = "Weights for Baker Hughes Corporation analysis.")
```


------------------------------------------------------------------
 &nbsp;    Eff       X1       Y1        Y2        Y3        Y4    
-------- -------- -------- --------- --------- --------- ---------
 **A**      1      0.9346   0.00546   0.1946       0      0.2308  

 **B**    0.6544   0.9434   0.00605      0      0.2986     0.234  

 **C**      1      3.077    0.01932   0.03027      0         0    

 **D**    0.3195   0.625       0         0       1.164       0    

 **E**    0.5636   1.818       0         0      0.2727       0    

 **F**      1        5         0         0      0.8487    0.1398  

 **G**    0.596    2.857       0         0       0.485    0.07989 

 **H**    0.2488   1.887    0.01189      0      0.02432      0    

 **I**    0.619    4.762       0         0      0.7143       0    

 **J**    0.625     6.25       0         0      0.9375       0    

 **K**      1      14.29       0         0         0      0.1538  

 **L**      1      0.5128   0.00333      0         0         0    

 **M**    0.1363   0.1789   0.00115      0      0.05662   0.04437 

 **N**      1      0.3226   0.00865      0         0       1.271  
------------------------------------------------------------------

Table: Weights for Baker Hughes Corporation analysis.

The envelopment and multiplier results are intricately related by duality.  In this case, we can see that certain outputs are "ignored" by certain projects by placing a zero weight on that output.  This is perfectly permissable in a DEA study when we don't know the relative value outputs and is why we refer to DEA scores as technical efficiency or relative efficiency. On the other hand, if we had more information on relative values of outputs that could or should be incorporated, this can be done.  The impact is that it would generally decrease the scores of some (but not necessarily all) projects (DMUs) whose original results violate these restrictions.
The efficiency scores match those reported by Thore and Rich but they didn't examine the output weights.  A lot of discussion could be had about relative weights.  We will leave that to the interested reader to pontificate upon.

## NASA Aeronautical Projects 

The next case in the book was comparing NASA aeronautics projects.  



Now that we have entered the data, let's run an input-oriented, variable returns-to-scale (BCC-IO) analysis.  


```r
resNASA <- DeaMultiplierModel(XNASA, YNASA, rts = "vrs", orientation = "input")
```

```
## Warning, data has DMU's with outputs that are zero, this may cause
##       numerical problems
```

```r
pander(cbind(XNASA, YNASA, resNASA$Efficiency))
```


---------------------------------------------
 &nbsp;    x1     y1    y2    y3       Eff   
-------- ------ ------ ---- ------- ---------
 **A1**   15.5   1.8    7     18     0.4775  

 **A2**    23    2.7    7     45     0.3927  

 **A3**   39.5   2.7    7     7.2    0.2286  

 **A4**    80     9     7     108    0.2555  

 **A5**   14.5   1.35   7     6.3    0.4542  

 **A6**   13.5   2.25   7    40.5    0.6086  

 **B1**    30    9.6    8     240    0.7176  

 **B2**   220     16    10    160       1    

 **B3**   180    6.8    10    64     0.5033  

 **B4**   980    25.2   9     560       1    

 **C1**   1050   20.7   6    1170       1    

 **C2**    15    4.5    6     18     0.8194  

 **C3**    40    19.8   6    544.5      1    

 **D1**   5.5    0.75   10     1        1    

 **E1**   110     0     8      0      0.05   

 **E2**   350     0     8      0     0.01571 

 **E3**   350     0     8      0     0.01571 

 **E4**   110     0     8      0      0.05   
---------------------------------------------

Again, the results match those of Thore and Rich.  Their discussion of results emphasized the comparison of projects to each other by looking at the lambda values to see how the targets of comparison were made.


```r
resNASA <- DeaMultiplierModel(XNASA, YNASA, rts = "vrs", orientation = "input")
```

```
## Warning, data has DMU's with outputs that are zero, this may cause
##       numerical problems
```

```r
# pander(poscol(cbind(resNASA$Efficiency,resNASA$vx,resNASA$uy)),
# caption='Multiplier Model Results for NASA projects.')
pander(poscol(cbind(resNASA$Efficiency, resNASA$Lambda)), caption = "Envelopment Model Results for NASA projects.")
```


--------------------------------------------------------
 &nbsp;     Eff       B2     B4   C1     C3        D1   
-------- --------- -------- ---- ---- --------- --------
 **A1**   0.4775      0      0    0    0.05512   0.9449 

 **A2**   0.3927      0      0    0    0.1024    0.8976 

 **A3**   0.2286      0      0    0    0.1024    0.8976 

 **A4**   0.2555      0      0    0    0.4331    0.5669 

 **A5**   0.4542      0      0    0    0.0315    0.9685 

 **A6**   0.6086      0      0    0    0.07874   0.9213 

 **B1**   0.7176      0      0    0    0.4646    0.5354 

 **B2**      1        1      0    0       0        0    

 **B3**   0.5033    0.3967   0    0       0      0.6033 

 **B4**      1        0      1    0       0        0    

 **C1**      1        0      0    1       0        0    

 **C2**   0.8194      0      0    0    0.1968    0.8032 

 **C3**      1        0      0    0       1        0    

 **D1**      1        0      0    0       0        1    

 **E1**    0.05       0      0    0       0        1    

 **E2**   0.01571     0      0    0       0        1    

 **E3**   0.01571     0      0    0       0        1    

 **E4**    0.05       0      0    0       0        1    
--------------------------------------------------------

Table: Envelopment Model Results for NASA projects.

## Possible to-Do Items for this Chapter
* More data sets and cases=
* Perhaps fix naming of outputs in first case to be P1, P2,... rather than A, B, C,... to match Thore
* Perhaps generalize naming of projects for NASA case to match Thore
* Create helper function for names to pass numbers of DMUs, inputs, outputs, and output naming objects
* Define # of digits for pander tables

## To-Do Items for Packages (some for later)
* Add data sets to package(s)
* Naming of results to reflect lambdas, etc.
