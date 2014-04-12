---
title       : Introduction to Data Jujitsu in R
subtitle    : 
author      : Edgaras Dunajevas
job         : Adform
framework   : io2012      # {io2012, html5slides, shower, dzslides, ...}
highlighter : highlight.js  # {highlight.js, prettify, highlight}
hitheme     : tomorrow      # 
widgets     : [mathjax]            # {mathjax, quiz, bootstrap}
mode        : selfcontained # {standalone, draft}

---
## Why R?
<p>  </br> 
</p>
## Jujitsu 

### Graphics + Modelling + Data Munging

http://simplystatistics.org/2013/12/30/some-things-r-can-do-you-might-not-be-aware-of/

### Also, `Hello World` is easy

```r
print("Hello World")
```

```
## [1] "Hello World"
```



---

## What is R?

- R is a free software programming language and software environment for statistical computing and graphics
- S was created by John Chambers while at Bell Labs 1976
- R was created by Ross Ihaka and Robert Gentleman 1993

![alt text][id]

[id]: backgrounds/ross.png "Title"

---

## Main data types

- Vectors
- Matrices
- Data frames
- Lists

```r
a <- 601:609
b <- letters[1:9]
m <- matrix(a, nrow = 3)
df <- data.frame(a, b)
l <- list(a, b, m, df)
```


---

## Some general stuff

* good place to start http://tryr.codeschool.com
* good place to further http://adv-r.had.co.nz
* good place to catch up http://www.r-bloggers.com
* case study/introduction book "Machine Learning for Hackers" By Drew Conway, John Myles White
* general book "The Art of R Programming" by Norman Matloff
* data analysis course by Jeff Leek https://www.coursera.org/course/dataanalysis
* statistical learning course by Trevor Hastie & Robert Tibshirani  
http://online.stanford.edu/course/statistical-learning-winter-2014
- What can you do - CRAN task views http://cran.r-project.org/web/views/


--- .class #id 

## R and others
<div style='text-align: center;'>
    <img height='480' src='backgrounds/lang.png' />
</div>

---
## Install

- base R http://cran.rstudio.com/
- RStudio https://www.rstudio.com/ (optional)
- install additional libraries, load, get help and examples


```r
install.packages("mcmc")
library(mcmc)
?metrop
example("metrop")
```

- Some packages have vignettes which are good source of information

```r
vignette("package name")
```

---

## RStudio
<div style='text-align: center;'>
    <img height='480' src='backgrounds/metropolis.png' />
</div>




--- .part1-slide
## Graphs

---

## Two things you should know
* base `plot` has methods for many objects, e.g. `plot(lm(y ~ x1 + x2))`
* `qplot` from `ggplot2` is easier to use for more advanced graphics (panels, coloring, smoothing, statistics, legends, themes)

```r
plot(x = diamonds$carat, y = diamonds$price, col = diamonds$cut)
library(ggplot2)
qplot(x = carat, y = price, color = cut, data = diamonds)
```

![plot of chunk unnamed-chunk-6](assets/fig/unnamed-chunk-6.png) 



---

## I don't like how it looks...


```r
library(ggthemes)
qplot(x = carat, y = price, color = cut, data = diamonds) + theme_gdocs()
```

![plot of chunk unnamed-chunk-7](assets/fig/unnamed-chunk-7.png) 

```r
# theme_bw(), theme_economist(), theme_tufte(), theme_wsj(), etc
```

* [book](http://ggplot2.org/book/)
* [documentation](http://docs.ggplot2.org/current/)



--- .part3-slide



## Modeling

---
## Formulae


```r
class(y ~ x1 + x2)
```

```
## [1] "formula"
```


```r
y ~ log(x1)  # transform
y ~ x1 - 1  # no intercept
y ~ x1:x2  # interaction
y ~ x1 * x2  # ~ x1 + x2 + x1:x2
y ~ x + I(x^2)  # raise to the power of something
y ~ poly(x, degree = 3, raw = FALSE)  # x + I(x^2) + I(x^3) 
y ~ polym(x1, x2, degree = 3, raw = FALSE)  # multivariable polynomial
y ~ x1 + x2 | e  # random effects
y ~ .  # all other variables
y ~ . - x42  # all other variables except x42
~x1 + x2  # no response 
y1 + y2 ~ x1  # multiple response 
```


---

## `summary`


```r
lm.fit <- lm(rating ~ Action + Animation + Comedy + Drama + Documentary + Romance, 
    data = mv)
summary(lm.fit)
```

<div style='text-align: left;'>
    <img height='400' src='backgrounds/summary_lm.png' />
</div>

---
## List of modelling packages I use(d) a lot

* `glmnet`
* `LiblineaR`
* `e1071`
* `randomForest`
* `plm`
* `quantreg`
* `nlme`
* `sem`
* `systemfit`
* `forecast`
* `vars`


--- .part2-slide

## Tools

---
  
## Classics
`lapply`, `sapply`, `apply`, `tapply`, `mapply`, `replicate` 

```r
lapply(l, length)[1]
```

```
## [[1]]
## [1] 9
```

```r
sapply(l, length)
```

```
## [1] 9 9 9 2
```

```r
apply(m, 2, function(x) x[1]/max(x))
```

```
## [1] 0.9967 0.9967 0.9967
```


---

## Lets make some "data"

```r
df <- data.frame(city = c("New York", "Vievis"), `2013` = c(1000, 130), `2014` = c(1400, 
    150), check.names = F, stringsAsFactors = F)
library(xtable)
print(xtable(df), type = "html")
```

<!-- html table generated in R 3.0.3 by xtable 1.7-3 package -->
<!-- Sat Apr 12 13:51:48 2014 -->
<TABLE border=1>
<TR> <TH>  </TH> <TH> city </TH> <TH> 2013 </TH> <TH> 2014 </TH>  </TR>
  <TR> <TD align="right"> 1 </TD> <TD> New York </TD> <TD align="right"> 1000.00 </TD> <TD align="right"> 1400.00 </TD> </TR>
  <TR> <TD align="right"> 2 </TD> <TD> Vievis </TD> <TD align="right"> 130.00 </TD> <TD align="right"> 150.00 </TD> </TR>
   </TABLE>

```r
options(xtable.type = "html")
```

---
## Melt & Cast

```r
library(reshape)
mf <- melt(df, id = 1)
xtable(mf)
```

<!-- html table generated in R 3.0.3 by xtable 1.7-3 package -->
<!-- Sat Apr 12 13:51:48 2014 -->
<TABLE border=1>
<TR> <TH>  </TH> <TH> city </TH> <TH> variable </TH> <TH> value </TH>  </TR>
  <TR> <TD align="right"> 1 </TD> <TD> New York </TD> <TD> 2013 </TD> <TD align="right"> 1000.00 </TD> </TR>
  <TR> <TD align="right"> 2 </TD> <TD> Vievis </TD> <TD> 2013 </TD> <TD align="right"> 130.00 </TD> </TR>
  <TR> <TD align="right"> 3 </TD> <TD> New York </TD> <TD> 2014 </TD> <TD align="right"> 1400.00 </TD> </TR>
  <TR> <TD align="right"> 4 </TD> <TD> Vievis </TD> <TD> 2014 </TD> <TD align="right"> 150.00 </TD> </TR>
   </TABLE>

```r
cmf <- cast(mf, city ~ variable)
xtable(cmf)
```

<!-- html table generated in R 3.0.3 by xtable 1.7-3 package -->
<!-- Sat Apr 12 13:51:48 2014 -->
<TABLE border=1>
<TR> <TH>  </TH> <TH> city </TH> <TH> 2013 </TH> <TH> 2014 </TH>  </TR>
  <TR> <TD align="right"> 1 </TD> <TD> New York </TD> <TD align="right"> 1000.00 </TD> <TD align="right"> 1400.00 </TD> </TR>
  <TR> <TD align="right"> 2 </TD> <TD> Vievis </TD> <TD align="right"> 130.00 </TD> <TD align="right"> 150.00 </TD> </TR>
   </TABLE>

---
## `plyr`

#### split-apply-combine      
<p>  </br> 
</p>

```r
library(plyr)
smf <- ddply(mf, "city", summarize, maxval = max(value))
xtable(smf)
```

<!-- html table generated in R 3.0.3 by xtable 1.7-3 package -->
<!-- Sat Apr 12 13:51:48 2014 -->
<TABLE border=1>
<TR> <TH>  </TH> <TH> city </TH> <TH> maxval </TH>  </TR>
  <TR> <TD align="right"> 1 </TD> <TD> New York </TD> <TD align="right"> 1400.00 </TD> </TR>
  <TR> <TD align="right"> 2 </TD> <TD> Vievis </TD> <TD align="right"> 150.00 </TD> </TR>
   </TABLE>

<p>  </br> 
</p>
`ddply, llply, dlply, ldply, daply, adply, mdply, d_ply, ...`

tutorials -> http://plyr.had.co.nz/09-u,    
check out http://blog.rstudio.org/2014/01/17/introducing-dplyr/

---
## %Parallel% `foreach`

```r

library(doParallel)
cl = makeCluster(4)
registerDoParallel(cl)
library(foreach)
out <- foreach(file = list.files(), .combine = rbind) %dopar% {
    # %do%
    d <- fread(file)
    subset(d, year > 2010)
}
stopCluster(cl)
```


* find out more in this [blog post](http://www.r-bloggers.com/the-wonders-of-foreach/)
* or [vignette](http://cran.r-project.org/web/packages/foreach/vignettes/foreach.pdf)
* or `vignette("foreach")`

---
## Using C functions

```r
library(devtools)  #install Rtools for this to work (on Windows)
library(Rcpp)
```

```
cppFunction(
  'int fibonacci(const int x) {
     if (x == 0) return(0); 
     if (x == 1) return(1);
     return (fibonacci(x - 1)) + fibonacci(x - 2);
}')
```

```r
sapply(1:10, fibonacci)
```


```
##  [1]  1  1  2  3  5  8 13 21 34 55
```



---

## `data.table`

inherits from `data.frame`. It offers fast subset, fast grouping, fast update, fast ordered joins and list columns in a short and flexible syntax, for faster development.

```r
library(data.table)
fread(input, sep="auto", sep2="auto", nrows=-1L, header="auto", na.strings="NA",
stringsAsFactors=FALSE, verbose=FALSE, autostart=30L, skip=-1L, select=NULL,
drop=NULL, colClasses=NULL,
integer64=getOption("datatable.integer64"),         # default: "integer64"
showProgress=getOption("datatable.showProgress")    # default: TRUE
)
```

###  More info

*  [introduction](http://127.0.0.1:47900/help/library/data.table/doc/datatable-intro.pdf)
* [faq](http://127.0.0.1:47900/help/library/data.table/doc/datatable-faq.pdf)
* [performance](http://127.0.0.1:47900/help/library/data.table/doc/datatable-timings.pdf)

---

## Example: AWS EC2 instance spot prices

```r
library(jsonlite)
library(stringr)
library(lubridate)
cc2json <- system("aws ec2 describe-spot-price-history --instance-types m1.medium", 
    intern = T)
d <- fromJSON(cc2json)[[1]]  #usually d <- read.csv(filename)
d$Timestamp <- gsub("T|\\.000Z", " ", d$Timestamp)
d$Timestamp <- str_trim(d$Timestamp)
d$Timestamp <- parse_date_time(d$Timestamp, "%Y-%m-%d %H:%M:%S")
d$SpotPrice <- as.numeric(d$SpotPrice)
d <- subset(d, !grepl("Windows", ProductDescription))
```


* `jsonlite` - parse jsons - `fromJSON`
* `stringr` - string munging functions - `str_trim`
* `lubridate` - parse date and time - `parse_date_time`

--- 

## And result


```r
qplot(x = Timestamp, y = SpotPrice, data = d, geom = "step") + facet_grid(ProductDescription ~ 
    AvailabilityZone, scales = "free")
```

![plot of chunk unnamed-chunk-21](assets/fig/unnamed-chunk-21.png) 


---


```r
heart <- function(t) {
    data.frame(x = 16 * sin(t)^3, y = 13 * cos(t) - 5 * cos(2 * t) - 2 * cos(3 * 
        t) - cos(4 * t))
}
qplot(data = heart(seq(0, 2 * pi, length = 100)), x = x, y = y, geom = "polygon")
```

![plot of chunk unnamed-chunk-22](assets/fig/unnamed-chunk-22.png) 



## Thank you!
