---
title: "My first post"
author: "HQ"
date: 2023-03-06
categories: ["Coding"]
tags: ["R Markdown", "plot", "KM Plot"]
---



Try to write a post about KM plots!

```{r}
# risk.table.title="My title"

survPlot <- function(dat_surv_s, outcomeTime,outcome,xlab, ylab,VarName,ExpName, ExpLabels = NULL, HRText = F, MedianText=T, labelX = 0, BreakBy=12, title=NULL){
  dat_surv_s$Group = dat_surv_s[[VarName]]
  if (is.null(outcomeTime)) {
    survF = as.formula(paste0("Surv(begin_a,end_a,",outcome,") ~ Group"))
    
  } else {
    survF = as.formula(paste0("Surv(",outcomeTime,",",outcome,") ~ Group"))
    
  }
  Sfit <- surv_fit(survF, data = dat_surv_s)
  g = ggsurvplot(Sfit, data = dat_surv_s, 
                 legend.title = ExpName, legend = c(0.9, 0.9), 
                 legend.labs = ExpLabels,
                 xlab = xlab, ylab = ylab,
                 break.time.by = BreakBy,
                 tables.height = 0.2,
                 surv.plot.height = 0.8,
                 tables.theme = custom_theme,
                 title=title,
                 risk.table = TRUE)
  if (HRText){
        sdf.cox = coxph(survF, data = dat_surv_s, ties="breslow")
        HR = round(exp(sdf.cox$coefficients),2)
        up95 = round(exp(confint(sdf.cox))[2],2)
        low95 = round(exp(confint(sdf.cox))[1],2)
        p.val = round(summary(sdf.cox)$waldtest[3],4)
    annoLine1 = paste0("J15237 vs. Control")
    annoLine2 = paste0("Cox Model HR (95% CI)") 
    annoLine3 = paste0(HR," [",low95,", ",up95, "]")
    annoLine4 = paste0("p = ",p.val)
    g$plot = g$plot + 
      annotate("text", x = labelX, y = 0.85, label = annoLine1, hjust = 0,size=4) +
      annotate("text", x = labelX, y = 0.80, label = annoLine2, hjust = 0,size=4) +
      annotate("text", x = labelX, y = 0.75, label = annoLine3, hjust = 0,size=4) +
      annotate("text", x = labelX, y = 0.70, label = annoLine4, hjust = 0,size=4)+
      theme(legend.text = element_text(size = 8))
  }
  if (MedianText) {
    logrank = survdiff(survF, data = dat_surv_s)
    median0 = round(surv_median(Sfit)$median[1],1)
    median1 = round(surv_median(Sfit)$median[2],1)
    median0low = round(surv_median(Sfit)$lower[1],1)
    median0up = round(surv_median(Sfit)$upper[1],1)
    median1low = round(surv_median(Sfit)$lower[2],1)
    median1up = round(surv_median(Sfit)$upper[2],1)
    p.val = round(logrank$pvalue,3)
    
    annoLine1 = paste0("Median Survival (95% CI)")
    annoLine2 = paste0("J15237: ",median1, " [", median1low,", ",median1up,"] Months") 
    annoLine3 = paste0("Control: ",median0, " [", median0low,", ",median0up,"] Months")
    annoLine4 = paste0("Log-rank P-Value = ",p.val)
    g$plot = g$plot + 
      annotate("text", x = labelX, y = 0.85, label = annoLine1, hjust = 0,size=4) +
      annotate("text", x = labelX, y = 0.80, label = annoLine2, hjust = 0,size=4) +
      annotate("text", x = labelX, y = 0.75, label = annoLine3, hjust = 0,size=4) +
      annotate("text", x = labelX, y = 0.70, label = annoLine4, hjust = 0,size=4)+
      theme(legend.text = element_text(size = 8))
  }

  
  return(g)
  
}
```


