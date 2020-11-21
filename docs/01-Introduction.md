---
output:
  pdf_document: default
  html_document: default
---
bibliography: "Z5_R_Resources.bib"
biblio-style: "apalike"
link-citations: true
---

# Introduction

Purpose of this Book
-----------------------------

R is a powerful analytical environment and Data Envelopment Analysis or DEA is a widely adopted methodology for providing insights to applications on performance improvement.  This naturally makes the combination a good option. 

This book does not try to be a full  introduction to R or a comprehensive mathematical coverage of DEA.  In fact, one book by Bogetoft and Otto already serves as a good companion to their package, Benchmarking, and provides a relatively and thorough coverage of many aspects of DEA.  

This book is intended to help use other people's DEA packages for R, how to write R code for doing DEA, and conducting DEA applications.  Using existing packages is relatively straightforward and this is demonstrated multiple times and results are periodically compared.  Writing R code allows one to dig deeper into how things work and means that a researcher could then implement appropriate variations or extensions to DEA rather than only relying on the functions written by others.  Lastly, a variety of applications are provided based on both published and unpublished work that show how to wrestle with data, interpret results, and dig deeper into where models breakdown.  

People familiar with optimization or taking an operations research course should be able to digest the material as long as they were willing to roll up their sleeves and get their hands dirty.  Everything presented here should run with a current version of R and appropriate packages.  Feel free to copy and paste code into your R instance and run it alongside me. Note that I strongly recommend using RStudio as an integrated development environment, IDE, in order to simplify the workflow.  This book was written using RStudio, rmarkdown, and bookdown.  

This book is intended to be a living resource and updated.  It is a very rough draft and currently only intended for members of the Extreme Technology Analytics Research Group at Portland State.  For more information on this research group, visit www.tfdea.com or contact me at tim.anderson@pdx.edu. 

## Resources

R is a compehensive system for statistical analysis that has rapidly become a standard tool for analytics.  Like every powerful tool, it has a bit of a learning curve.  There are many excellent books on R.  Two of my favorites are:  _R in Action_ [@AdlerNutshell2012] and _The Art of R Programming_ [@MatloffArtprogramming2011].

_R in Action_ provides a comprehensive coverage of R discussing how to use it for statistics progressing through a variety of deeper topics including programming.  The second edition of _R in Action_ adds important topics such as package development and interactive documentation using knitr.  It is available as a paperback or ebook and deals are often available from the [publisher.](http://www.manning.com/kabacoff2/)

Peter Bogetoft and Lars Otto have written a very thorough book on DEA and other econometric methods, _Benchmarking with DEA, SFA, and R_ [@BogetoftBenchmarkingDEASFA2011].  It is equal parts a mathematical book on frontier estimation and a manual for their R DEA package, Benchmarking.  It is an excellent book, just unfortunately a bit on the [pricey side](http://www.amazon.com/Benchmarking-International-Operations-Research-Management/dp/1441979603/ref=sr_1_fkmr1_1?ie=UTF8&qid=1396922272&sr=8-1-fkmr1&keywords=bogetoft+otto+benchmarking). We will revisit this and other packages later but for this week, you can concentrate on the code in later chapters.

This book  presumes some basic familiarity with R but will also walk through a lot of commands along the way.  It is not intended to be a standalone R programming tutorial.  

_The Art of R Programming_ takes a different perspective on R by emphasizing it as a programming environment.  People with experience in software development in some other language may find this eases the transition to using R as a rich computational environment.  It was [published](http://shop.oreilly.com/product/9781593273842.do) in 2011 so it is getting a little dated but still a nice resource.

In addition to R, be sure to download RStudio.  There are a lot of other ways to work with R including emacs, Eclipse, etc. but RStudio provides a very rich toolset for live markdown  documents incorporating text and R code as well support for organizational tools such as github.  It can even be accessed from a browser so that nothing needs to be installed locally.

## Status

This book is currently in a very rough draft state.  As such, the writing needs more polish as well.  Some items are coded in a less efficient manner for a variety of reasons, sometimes for simplicity of the reader.  If you have suggestions on how to improve this book, please let me know.  The current book is rapidly evolving.  

Topics to consider in the future:

* Matrix construction of DEA LPs (Chapter 3)
* Output-oriented model (Half done in Chapter 4)
* Beautifying display of information
* Extracting weights from the dual results
* Multiplier model (Partially done in Chapter 5)
* Cross-efficiency (Begun now in Chapter 6)
* Technology Forecasting using DEA
* Malmquist Productivity Indices
* Network approaches
* Shiny Application
* Other DEA packages in R
    + Use of other packages
    + Comparison of results
* Adding more references

Some issues to consider about the book are the following:

* Should data frames be used instead of matrices?
* Potential publication outlet(s)
* Git/GitHub
* Convert some chunks to functions where appropriate

Comments are welcome.

## Acknowledgements

This book has benefitted from the interactions of many people over the years.  In particular, I would like to thank the following students and graduates from over the recent years that have made direct or indirect contributions this effort.  

* Dr. Dong-Joon Lim
* Tim Calderwood
* Nina Chaichi
* Maoloud Dabab
* William "Ike" Eisenhauer
* Jiali Ju
* Aurobindh Kalathil Puthanpura
* Tom Shott
* Kevin van Blommestein

I would also like to thank earlier doctoral graduates for rich mutual interactions:

* Dr. Janice Forrester
* Dr. Gerald Williams
* Dr. Lane Inman
