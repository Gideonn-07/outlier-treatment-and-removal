data salaries;
    input id salary;
    datalines;
    1 3000
    2 3200
    3 3500
    4 4000
    5 4200
    6 10000  /* Outlier */
    7 4100
    8 3800
    9 15000  /* Outlier */
    10 3900
    ;
run;
 proc univariate data=salaries noprint;
    var salary;
    output out=stats pctlpts=25 75 pctlpre=Q;
run;

/* Get the IQR and calculate the boundaries for outliers */
data stats;
    set stats;
    IQR = Q75 - Q25;
    lower_limit = Q25 - 1.5 * IQR;
    upper_limit = Q75 + 1.5 * IQR;
run;

/* Remove outliers from the dataset */
data salaries_clean;
    if _n_= 1 then set stats;
    set salaries;
    if salary >= lower_limit and salary <= upper_limit;
run;

/* Display the cleaned dataset */
proc print data=salaries_clean;
run;
