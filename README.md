How to share data with a statistician
===========

This is a guide for anyone who needs to share data with a statistician. The target audiences I have in mind are:

* Scientific collaborators who need statisticians to analyze data for them
* Students or postdocs in scientific disciplines looking for consulting advice
* Junior statistics students whose job it is to collate/clean data sets

The goals of this guide are to provide some instruction on the best way to share data to avoid the most common pitfalls
and sources of delay in the transition from data collection to data analysis. The Leek group works with a large
number of collaborators and the number one source of variation in the speed to results is the status of the data
when they arrive at the Leek group. Based on my conversations with other statisticians this is true nearly universally.

My strong feeling is that statisticians should be able to handle the data in whatever state they arrive. It is important
to see the raw data, understand the steps in the processing pipeline, and be able to incorporate hidden sources of
variability in one's data analysis. On the other hand, for many data types, the processing steps are well documented
and standardized. So the work of converting the data from raw form to directly analyzable form can be performed 
before calling on a statistician. This can dramatically speed the turnaround time, since the statistician doesn't
have to work through all the pre-processing steps first. 


What you should deliver to the statistician
====================

For maximum speed in the analysis this is the information you should pass to a statistician:

1. The raw data.
2. A [tidy data set](http://vita.had.co.nz/papers/tidy-data.pdf) 
3. A code book describing each variable and its values in the tidy data set.  
4. An explicit and exact recipe you used to go from 1 -> 2,3 

Let's look at each part of the data package you will transfer. 


### The raw data

It is critical that you include the rawest form of the data that you have access to. Here are some examples of the
raw form of data:

* The strange binary file your measurement machine spits out
* The unformated Excel file with 10 workbooks the company you contracted with sent you
* The complicated JSON data you got from scraping the Twitter API
* The hand-entered numbers you collected looking through a microscope

You know the raw data is in the right format if you: 

1. Ran no software on the data
2. Did not manipulate any of the numbers in the data
3. You did not remove any data from the data set
4. You did not summarize the data in any way

If you did any manipulation of the data at all it is not the raw form of the data. Reporting manipulated data
as raw data is a very common way to slow down the analysis process, since the analyst will often have to do a
forensic study of your data to figure out why the raw data looks weird. 

### The tidy data set

The general principles of tidy data are laid out by Hadley Wickham in [this paper](http://vita.had.co.nz/papers/tidy-data.pdf)
and [this video](http://vimeo.com/33727555). The paper and the video are both focused on the R package, which you
may or may not know how to use. Regardless the three general principles you should pay attention to are:

1. Each variable you measure should be in one column
2. Each different observation of that variable should be in a different row
3. There should be one table for each "kind" of variable
4. If you have multiple tables, they should include a column in the table that allows them to be linked

While these are the hard and fast rules, there are a number of other things that will make your data set much easier
to handle. First is to include a row at the top of each data table/spreadsheet that contains full row names. 
So if you measured age at diagnosis for patients, you would head that column with the name AgeAtDiagnosis instead
of something like ADx or another abreviation that may be hard for another person to understand. 


Here is an example of how this would work from genomics. Suppose that for 20 people you have collected gene expression measurements with 
[RNA-sequencing](http://en.wikipedia.org/wiki/RNA-Seq). You have also collected demographic and clinical information
about the patients including their age, treatment, and diagnosis. You would have one table/spreadsheet that contains the clinical/demographic
information. It would have four columns (patient id, age, treatment, diagnosis) and 21 rows (a row with variable names, then one row
for every patient). You would also have one spreadsheet for the summarized genomic data. Usually this type of data
is summarized at the level of the number of counts per exon. Suppose you have 100,000 exons, then you would have a
table/spreadsheet that had 21 rows (a row for gene names, and one row for each patient) and 100,001 columns (one row for patient
ids and one row for each data type). 


### The code book

For almost any data set, the measurements you calculate will need to be described in more detail than you will sneak
into the spreadsheet. The code book contains this information. At minimum it should contain:

1. Information about the variables (including units!) in the data set not contained in the tidy data 
2. Information about the summary choices you made
3. Information about the experimental study design you used

In our genomics example, the analyst would want to know what the unit of measurement for each
clinical/demographic variable is (age in years, treatment by name/dose, level of diagnosis and how heterogeneous). They 
would also want to know how you picked the exons you used for summarizing the genomic data (UCSC/Ensembl, etc.). They
would also want to know any other information about how you did the data collection/study design. For example,
are these the first 20 patients that walked into the clinic? Are they 20 highly selected patients by some characteristic
like age? Are they randomized to treatments? 

A common format for this document is a word file. There should be a section called "Study design" that has a thorugh
description of how you collected the data. There is a section called "Code book" that describes each variable and its
units. 

### How to code variables

When you put variables into a spreadsheet there are several main categories you will run into:

1. Continuous
2. Ordinal
3. Categorical
4. Misssing 
5. Censored

Continuous variables are anything measured on a quantitative scale that could be any fractional number. An example
would be something like weight measured in kg. Ordinal data are data that have a fixed, small (< 100) number of levels but are ordered. 
This could be for example survey responses where the choices are: poor, fair, good. Categorical data are data where there
are multiple categories, but they aren't ordered. One example would be sex: male or female. Missing data are data
that are missing and you don't know the mechanism. You should code missing values as NA. Censored data are data
where you know the missingness mechanism on some level. Common examples are a measurement being below a detection limit
ora patient being lost to follow-up. They should also be coded as NA when you don't have the data. But you should
also add a new column to your tidy data called, "VariableNameCensored" which should have values of TRUE if censored 
and FALSE if not. In the code book you should explain why those values are missing. It is absolutely critical to report
to the analyst if there is a reason you know about that some of the data are missing. You should also not impute/make up/
throw away missing observations.

In general, try to avoid coding categorical or ordinal variables as numbers. When you enter the value for sex in the tidy
data, it should be "male" or "female". The ordinal values in the data set should be "poor", "fair", and "good" not 1, 2 ,3.
This will avoid potential mixups about which direction effects go and will help identify coding errors. 


### The instruction list/script

You may have heard this before, but [reproducibility is kind of a big deal in computational science](http://www.sciencemag.org/content/334/6060/1226).
That means, when you submit your paper, the reviewers and the rest of the world should be able to exactly replicate
the analyses from raw data all the way to final results. If you are trying to be efficient, you will likely perform
some summarization/data analysis steps before the data can be considered tidy. 

The ideal thing for you to do when performing summarization is to create a computer script (in R, Python, or something else) 
that takes the raw data as input and produces the tidy data you are sharing as output. You can try running your script
a couple of times and see if the code produces the same output. 

In many cases, the person who collected the data has incentive to make it tidy for a statistician to speed the process
of collaboration. They may not know how to code in a scripting language. In that case, what you should provide the statistician
is something called psuedocode. It should look something like:

1. Step 1 - take the raw file, run version 3.1.2 of summarize software with parameters a=1, b=2, c=3
2. Step 2 - run the software separatly for each sample
3. Step 3 - take column three of outputfile.out for each sample and that is the corresponding row in the output data set

You should also include information about which system (Mac/Windows/Linux) you used the software on and whether you 
tried it more than once to confirm it gave the same results. Ideally, you will run this by a fellow student/labmate
to confirm that they can obtain the same output file you did. 



What you should expect from a statistician
====================

When you turn over a properly tidied data set it dramatically decreases the workload on the statistician. So hopefully
they will get back to you much sooner. But most careful statisticians will check your recipe, ask questions about
steps you performed, and try to confirm that they can obtain the same tidy data that you did with, at minimum, spot
checks.

You should then expect from the statistician:
1. An analysis script that performs each of the analyses (not just instructions)
2. The exact computer code they used to run the analysis
3. All output files/figures they generated. 

This is the information you will use in the supplement to establish reproducibility and precision of your results. Each
of the steps in the analysis should be clearly explained and you should ask questions when you don't understand
what the analyst did. It is the responsibility of both the statistician and the scientist to understand the statistical
analysis. You may not be able to perform the exact analyses without the statistician's code, but you should be able
to explain why the statistician performed each step to a labmate/your principal investigator. 

