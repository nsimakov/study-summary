# SAS

## Reading Data

```sas
data table;
  infile 'table.dat';
  input string $ x y;
run;

data table;
  input string $ x y;
  datalines;
cl1 1.0 2.0
cl2 . 2.0
;run;
proc print data=table; run;

data insomnia_stacked;
set insomnia;
time=0; cortisol=t0; output;
...
keep id time cortisol group;
run;
```


## Simple Calculations

```sas
data problem1;
  n=12; Ybar=157.6; muH0=165; S=24.4;
  TestStat=(Ybar-muH0)/(S/sqrt(n));
  t_0975=quantile("t", 0.975, n-1);
  p_value=2*cdf("t", -abs(TestStat), n-1);
run;
data problem2;
  n=25; Ybar=4.3; S=0.6; alpha=0.1; S2=S*S;
  chi2left=quantile("chisq", alpha/2, n-1);
  chi2right=quantile("chisq", 1-alpha/2, n-1);
  ci_left=sqrt((n-1)*S2/chi2right);
  ci_right=sqrt((n-1)*S2/chi2left);
run; proc print data=problem2; run;

proc means data=insomnia_stacked mean std maxdec=2;
  class group time;
  var cortisol;
run;

proc corr data=table2;
  var y x2 x2'
run;
```

## Plotting

```sas
proc univariate data=insomnia_stacked noprint; /*histogram*/
  class time;
  var cortisol;
  histogram / nrows=6 ncols=1;
run;
proc gplot data = insomnia_stacked;
  plot y*(x1 x2);
run;
proc sgplot data = insomnia_stacked; /*scatter*/
  series x=time y=cortisol / group=id;
  /*refline -1 1;*/
  /*xaxis values=(1 to 20 by 1)'*/
run;
proc sgpanel data=insomnia_stacked; /*scatter in panels*/
  panelby id / columns=6 rows=4;
  series x=time y=cortisol;
  scatter x=time y=cortisol;
run;
proc sgplot data = bacteria; /*boxplot*/
  vbox log2_size / category=t group=group connect=mean;
run;
proc corr data=insomnia noprob nosimple
plots=matrix(histogram nvar=all);
 var t0 t15 t30 t45 t60;
 by group;
run;
```

## Linear Regression

```sas
proc reg data=table3
  model y = x1 x2 x3/clb covb /*ss1 ss2 pcorr1 pcorr2 influence*/;
  output= outdata p=yhat lclm=lclm uclm=uclm lcl=lcl ucl=ucl cookd=cookd;
  /*test1: test x1=0,x2=0*/
run; quit;
```

## One Way Anova

```sas
proc reg data=table3
  model y = x1 x2 x3/clb covb /*ss1 ss2 pcorr1 pcorr2 influence*/;
  output= outdata p=yhat lclm=lclm uclm=uclm lcl=lcl ucl=ucl cookd=cookd;
  /*test1: test x1=0,x2=0*/
run; quit;
```
