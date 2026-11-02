# econometrics-code-samples

This repository contains Stata code from assignments completed during my MSc Economics at the Stockholm School of Economics. Each do-file corresponds to a separate econometrics assignment.

## Assignments

| File | Description |
|---|---|
| [Assignment_01.do](Assignment_01.do) | Cleaning and merging ANES 2020 survey data, constructing analysis variables, producing descriptive statistics, and visualizing patterns in political attention and presidential voting. |

## Software requirements

The code is written in Stata. Assignment 1 uses the user-written packages `estout` and `binscatter`, which can be installed by running:

```stata
ssc install estout
ssc install binscatter
```

## Data availability

The course-provided datasets are not included in this repository. The code is shared as a sample of my coursework; running it requires the original input files.

Assignment 1 requires:

- `PS1_ANES2020.dta`
- `PS1_ANES2020_to_merge.dta`

## Running the code

1. Install the required Stata packages.
2. Place the required datasets in a `Data` folder within your project directory.
3. Update the project path defined at the beginning of the do-file.
4. Ensure an `Output` folder exists at the location specified in the script.
5. Run the do-file in Stata.
