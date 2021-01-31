---
editor_options:
  chunk_output_type: inline
output:
  html_document: default
  pdf_document: default
---

```{r setwd("/Users/weizhou/Documents/R_Learning")}
bballbets <- read.csv("/Users/weizhou/Documents/R_Learning/bballbets.csv")
plot(jitter(homewin,0.2) ~ spread, data=bballbets,
     xlab='Home team victory?', ylab='Point spread',
     pch=19, col=rgb(0,0,0,0.2), las=1)
```


# Look at the empirical win frequency within "buckets"

```spread.discrete = cut(bballbets$spread, breaks=seq(-35,45,by=10))
lm0 = lm(homewin~spread.discrete, data=bballbets)
points(fitted(lm0) ~ spread, data=bballbets, col='blue', pch=19)
```
