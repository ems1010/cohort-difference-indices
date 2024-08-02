# Cohort and Difference Indices
## Code to assist in applying Cohort and Difference Indices to linguistic panel data

This repository accompanies my Linguistic Vanguard manuscript "Situating speakers in community change: a methodology for quantifying degree and direction of change over the lifespan." The article discusses an approach to quantifying individuals' direction and degree of change over the lifespan. Specifically, how to use speaker random intercepts from mixed effects models to represent how far ahead/behind an individual is in a change for their cohort (a 'Cohort Index'), and how to use the difference between  random intercepts from different time points to quantify how much an individual has moved over time (a 'Difference Index'). This repository contains useful code chunks for those interested in applying the methodology themselves. 

## Using the repository
The structure of the files is very simple. There is a Quarto script with the following:
 - R libraries used
 - An example linear mixed effects model (the same structure used in the manuscript)
 - Code to extract random intercept values for individual speakers from linear mixed effects models
 - Code to change the sign (+/-) of the extracted random intercept values, where relevant
 - A description of how to calculate Difference Indices
 - Code to test a correlation between relative position to change (represented by Cohort Indices) and degree and direction of change over time (represented by Difference Indices)
 
The Quarto script can be rendered as a stand-alone .html file. Alternatively, the rendered html file in the repository can be independently downloaded and code chunks copied from there. 

## Other comments
I would love for this methodology to be applied further in lifespan research (and for other applications I have not thought of). Please let me know if you have any questions or suggestions for improving this documentation. 