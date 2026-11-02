*******************************************************************************
/*
File name: Assignment1.do 

Purpose: Clean and merge 2020 ANES survery data, describe Trump and Biden voters, examine relationships between education, political attention and voting.

Required packages:
    estout     (ssc install estout)
    binscatter (ssc install binscatter) 
	
Created on: August 2025
Last updated on: August 2025

*/	
********************************************************************************

*------------------------------------------------------------------------------*
* 1. Cleaning data
*------------------------------------------------------------------------------*
clear all

*set path to project folder containing data 
global root "/Users/edhareddy/Desktop/SSE/First Semester/5304 - Econometrics/PS1"

*create global macro for the data directory
global data "$root/Data"

*create global macro for output directory
global output "$root/Output"
capture mkdir "$output"


*import data
use "$data/PS1_ANES2020.dta", clear
describe 

* rename variables and assign labels
rename V200001 respondent_id
rename V200007 case_validation
rename V201004 gender
rename V201005 attention
rename V201102 voted_2016
rename V201103 president_2016
rename V201120 worried

label variable respondent_id "2020 CASE ID"
label variable case_validation "PRE- AND POST-ELECTION SELECTION STATUS"
label variable gender "gender"
label variable attention "Attention to Politics"
label variable voted_2016 "PRE: DID R VOTE FOR PRESIDENT IN 2016"
label variable president_2016 "PRE: WHO DID R VOTE FOR PRESIDENT IN 2016"
label variable worried "Worried about the country"

*verify that the main dataset contains one record per respondent
isid respondent_id

*store the main dataset temporarily
tempfile main
save `main'

*remove exact duplicate records from the supplementary dataset
use "$data/PS1_ANES2020_to_merge.dta", clear
duplicates report respondent_id
duplicates drop

*stop if respondent IDs remain duplicated or are missing
isid respondent_id

*attach supplementary variables to the main dataset
*keep all main-dataset respondents and exclude supplementary-only records
merge 1:1 respondent_id using `main', keep(using match)

*inspect matching outcomes before removing the merge indicator
tabulate _merge
drop _merge

*recode negative nonresponse codes as missing for analysis variables.
ds
foreach var of varlist `r(varlist)' {
    replace `var' = . if `var' < 0
}

*recode the education nonresponse category as missing
replace education = . if education == 95

*restrict the analysis sample to respondents with observed age and education
drop if missing(age) | missing(education)

* save data for the analysis
save "$data/PS1_ANES2020_clean", replace


*------------------------------------------------------------------------------*
* 2. Explorative analysis
*------------------------------------------------------------------------------*

use "$data/PS1_ANES2020_clean", clear
  
*plot mean political attention by education level
preserve

collapse (mean) attention, by(education)

scatter attention education, ytitle("Mean attention") xtitle("Education level") title("Mean attention by education level") msymbol(Dh) msize(small)

graph export "$output/Fig1.png", width(4000) replace

restore

generate byte bachelor_plus = (education >= 5) if !missing(education)

label variable bachelor_plus "Has at least a Bachelor's degree"

generate byte white = (race == 1) if !missing(race)
label variable white "Respondent is White"

generate byte not_worked = (working <= 1) if !missing(working)
label variable not_worked "Did not work for pay last week"

*retain respondents reporting a Biden or Trump vote
*assumes 1 = Biden and 2 = Trump
keep if inlist(president_vote, 1, 2)

generate byte trump_vote = (president_vote == 2)
label variable trump_vote "Voted for Trump"

preserve

estpost summarize bachelor_plus white not_worked age conservative attention worried
est store pooled

estpost summarize bachelor_plus white not_worked age conservative attention worried if trump_vote == 1
est store trump

estpost summarize bachelor_plus white not_worked age conservative attention worried if trump_vote == 0
est store biden

esttab pooled trump biden using descriptives.rtf, cells("mean(fmt(2)) sd(fmt(2))") label title("Descriptive Statistics: Trump vs Biden Voters") replace

histogram age, width(1) frequency

*only for trump voters 
histogram age if trump_vote==1, width(1) frequency 

*only for trump and age less than 80
histogram age if trump_vote==1 & age <80, width(1) frequency

*using binscatter
local nbins = 20

binscatter trump_vote age if age < 80, linetype(lfit) ytitle("") xtitle("") nq(`nbins') title("Linear fit") note("Number of bins: `nbins'") name(lfit, replace) graphregion(color(white))

binscatter trump_vote age if age < 80, linetype(qfit) ytitle("") xtitle("") nq(`nbins') title("Quadratic fit") note("Number of bins: `nbins'") name(qfit, replace) graphregion(color(white))

* combine the plots
graph combine lfit qfit, graphregion(color(white)) b2title("Age") l2title("Fraction voting Trump")
graph export "$output/Fig3.png", width(4000) replace

restore
