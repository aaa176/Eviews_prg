Eviews_prg
==========
Eviews programme 1219676 / Introduction to Econometric Software Assignment
==========
'Ar(1) process on original series
'First we create a workfile for the entire range of the sample
wfcreate (wf=houst_AR) m 1959m1 2014m9
‘To find and download the series houst from FRED
fetch fred::houst
'Set the sample for the initial estimation window
smpl 1959m1 1995m12
'Run the equation for the Autoregressive process, using the Ar(1) term which suggest that the error term follow an AR process
equation eq_arhoust.ls houst c AR(1)
'Freeze the equation into a table for the initial window and name it initial_smplhoust
freeze(initial_houst) eq_arhoust.ls houst c AR(1)
'Create a vector to store Ar(1) coefficient
vector(224) autoreg_houst
'Count the number of equations in the loop
!rowcounter=1
'This part is to create a recursive estimation object from 1996M1 and store Ar(1) coefficient in the vector autoreg_houst
for !i=1 to 224
'The sample will be added gradually and cumulatively one period till the end of the sample 2014M9
smpl 1959m1 1996m1+!i
'The FOR loop will run the following estimation equation
equation eq_arhoust{!i}.ls houst c AR(1)
'Autoregressive coefficient (coef(2)) from each equation will be recorded in the vector autoreg_houst
Autoreg_houst(!rowcounter)= eq_arhoust{!i}.@coef(2)
!rowcounter = !rowcounter+1
next
'To plot the autoregressive against time, first I set the sample to match the calculated equations
smpl 1996m1 2014m10
'Then convert the vector to a series in order to ensure the values are against time rather than row numbers
mtos(autoreg_houst, autohoust_series)
'Plot and freeze the autoregressive coefficient against time
freeze(autohoust_graph) autohoust_series.line
'----------------------------------------------------------------------------
'Ar(1) with logarithms:
'Create a series with data in logarithms
smpl 1959m1 2014m9
genr loghoust = log(houst)
'We set the sample for the initial estimation window
smpl 1959m1 1995m12
'Run the equation for the Autoregressive process, using the Ar(1) term
equation eq_arlog.ls loghoust c AR(1)
'Freeze to a table and name it
freeze(initial_log) eq_arlog.ls loghoust c AR(1)
'Create a vector to store Ar(1) coefficient
vector(224) autoreg_log
'Count the number of equations in the loop
!rowcounter=1
'Recursive estimation
for !i=1 to 224
smpl 1959m1 1996m1+!i
equation eq_arlog{!i}.ls loghoust c AR(1)
autoreg_log(!rowcounter)= eq_arlog{!i}.@coef(2)
!rowcounter = !rowcounter+1
next

'Plot the autoregressive against time
smpl 1996m1 2014m10
mtos(autoreg_log,autolog_series)

'Plot and freeze line graph
freeze(autolog_graph) autolog_series.line
'----------------------------------------------------------------------------
'Because the data is not stationary
'The data is not stationary and hence I generated a new series for first-differences
smpl 1959m1 2014m9
genr dhoust = d(houst)
‘Set the sample for the initial estimation window
smpl 1959m1 1995m12
‘Run Ar(1) estimation equation
equation eq_arterm.ls dhoust c AR(1)
'Freeze outputs to table and name it
freeze(initial_dhoust) eq_arterm.ls dhoust c AR(1)
'Create a vector to store Ar(1) coefficient
vector(224) autoreg_dhoust
'Count equations in the loop
!rowcounter=1
'Recursive estimation
for !i=1 to 224
smpl 1959m1 1996m1+!i
equation eq_arterm{!i}.ls dhoust c AR(1)
autoreg_dhoust(!rowcounter)= eq_arterm{!i}.@coef(2)
!rowcounter = !rowcounter+1
next
'Plot the autoregressive coef
smpl 1996m1 2014m10
mtos(autoreg_dhoust,autodhoust_series)
freeze(auto_graph) autodhoust_series.line

'----------------------------------------------------------------------------
'EXTRA
'ARMA(1,1) process

'set the initial sample
smpl 1959m1 1995m12
'run Ar(1) and MA(1)
equation eq_arma1.ls dhoust c ar(1) ma(1)
'freez the output results for the estimated equation
freeze(initial_smplarma1) eq_arma1.ls dhoust c ar(1) ma(1)

'create a matrix of 2 columns for the Ar coefficients and 224 rows for the number of equations that will run
matrix(225,2) arma1coefs
!rowcounter=1

'loop to run the model on the rest of the sample
for !i=1 to 225
smpl 1959m1 1996m1+!i

equation eq_arma1{!i}.ls dhoust c ar(1) ma(1)

'to store Ar coeffecients in the matrix
'based on the number of equation as counted by rowcounter the first column will store ar(1) coef
arma1coefs(!rowcounter,1) = c(2) 
'the second column will store ma(1) coef
arma1coefs(!rowcounter,2) =c(3)

!rowcounter = !rowcounter+1
next 

'plot the ar(1) and ma(1) against time
'set the sample
smpl 1996m1  2014m9
matrix(225,2) arma1coefs
'conert matrix to group
mtos(arma1coefs,arma1_series)
'line plot the results
freeze(arma1_graph) arma1_series.line

'------------------------------------------------------------------------------
'EXTRA
'ARMA (2,2) process
'initial sample
smpl 1959m1 1995m12
'run Ar(1), Ar(2), MA(1) and MA(2)
equation eq_arma2.ls dhoust c ar(1) ar(2) ma(1) ma(2)
'freez the output results for the estimated equation
freeze(initial_smplarma2) eq_arma2.ls dhoust c ar(1) ar(2) ma(1) ma(2)

'create a matrix of 2 columns for the Ar coefficients and 224 rows for the number of equations that will run
matrix(225,4) arma2coefs
!rowcounter=1

'loop to run the model on the rest of the sample
for !i=1 to 225
smpl 1959m1 1996m1+!i

equation eq_arma2{!i}.ls dhoust c ar(1) ar(2) ma(1) ma(2)

'to store Ar coeffecients in the matrix
'based on the number of equation as counted by rowcounter the first column will store ar(1) coef
arma2coefs(!rowcounter,1) = c(2) 
'the second column will store ar(2) coef
arma2coefs(!rowcounter,2) =c(3)
'the third column will store ma(1) coef
arma2coefs(!rowcounter,3) = c(4) 
'the fourth column will store ma(2) coef
arma2coefs(!rowcounter,4) =c(5)



!rowcounter = !rowcounter+1
next 

'plot the arma against time
'set the sample
smpl 1996m1  2014m9
matrix(225,4) arma1coefs
'conert matrix to group
mtos(arma2coefs,arma2_series)
'line plot the results
freeze(arma2_graph) arma2_series.line

'------------------------------------------------------------------------------
