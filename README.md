# Predicting Pet Insurance Claims in the Second Policy Year

[![Python 3.8](https://img.shields.io/badge/python-3.8-blue.svg)](https://www.python.org/downloads/release/python-380/)
[![Maintenance](https://img.shields.io/badge/Maintained%3F-no-red.svg)](https://github.com/stevenrhart/predicting-claims/graphs/commit-activity)
[![License: GPL v3](https://img.shields.io/badge/License-GPLv3-blue.svg)](https://www.gnu.org/licenses/gpl-3.0)

**[Executive Summary](#exec-summary)** | **[Data](#data)** | **[Wrangling](#wrangling)** | **[EDA](#eda)** | **[Modeling](#model)** | **[Results](#results)** | **[Future](#future)**

See the [presentation](#insert link to presentation)

See the [complete report](#insert link to report)


## EXECUTIVE SUMMARY <a id='overview'></a>

<p align="justify">Insert background paragraph on pet insurance industry </p>

<p align="justify">When a pet insurance policy holder incurs veterinary expenses related to their enrolled pet, they can submit claims for reimbursement, and the insurance company reimburses eligible expenses. To price insurance products correctly, the insurance company needs to have a good idea of the amount their policy holders are likely to claim in future policy years. The goal of this project is to create a machine learning model to predict how much (in dollars) a given policy holder will claim for during their second policy year based on pet information and claims data from their first policy year. </p>

<p align="justify">Insert paragraph on summary results </p>


## DATA <a id ='data'></a>

<p align = 'justify'>The underlying source data for the project consists of two files - <code>PetData.csv</code> and <code>ClaimData.csv</code>. The former contains enrollment and premium data for 50000 pets that enrolled for insurance during the 2018 calendar year, and the latter contains the dates and dollar amounts of the associated claims for these pets between 2018 and 2021. </p>

<p align = 'justify'><strong>PetData.csv columns:</strong>
<ul>
    <li><code>PetId</code> - a unique number that identifies enrolled pets </li>
    <li><code>EnrollDate</code> - the date of customer enrollment </li>
    <li><code>Species</code> - species of pet, dog or cat </li>
    <li><code>Breed</code> - breed of pet </li>
    <li><code>PetAge</code> - the age of the pet at enrollment (not necessarily the current age of the pet) </li>
    <li><code>Premium</code> - the monthly premium (in USD) of the pet’s insurance policy </li>
    <li><code>Deductible</code> - the deductible (in USD) of the pet’s insurance policy </li>
    <li><code>EnrollPath</code> - indicates whether the member enrolled via company website or over the phone </li> 
</ul>
</p>
The underlying source data for the project consists of two files - `PetData.csv` and `ClaimData.csv`. The former contains enrollment and premium data for 50000 pets that enrolled for insurance during the 2018 calendar year, and the latter contains the dates and dollar amounts of the associated claims for these pets between 2018 and 2021.

**PetData.csv columns:**
* `PetId` - a unique number that identifies enrolled pets 
* `EnrollDate` - the date of customer enrollment 
* `Species` - species of pet, dog or cat
* `Breed` - breed of pet
* `PetAge` - the age of the pet at enrollment (not necessarily the current age of the pet)
* `Premium` - the monthly premium (in USD) of the pet’s insurance policy 
* `Deductible` - the deductible (in USD) of the pet’s insurance policy 
* `EnrollPath` - indicates whether the member enrolled via company website or over the phone  



<p align = 'justify'><strong>ClaimsData.csv columns:</strong>
<ul>
    <li><code>PetId</code> - a unique number that identifies enrolled pets </li>
    <li><code>ClaimId</code> - a unique number that identifies individual claims made by our members </li>
    <li><code>ClaimDate</code> - date of claim </li>
    <li><code>AmountClaimed</code> - amount of claim </li>
</ul></p>


## DATA WRANGLING <a id ='wrangling'></a>

<p align = 'justify'>Overall, the two datasets were relatively clean and the bulk of the data wrangling process consisted of data verification and determining how best to combine the pets data with the associated claims data. A few columns required some additional manipulation in preparation for exploratory data analysis.</p>
    
<p align = 'justify'><strong>Key Observations:</strong>
<ul>
    <li>Pet Count - Verified 50,000 unique pets (based on PetIds)</li>
    <li>Species - Data consists of two species of pets, cats and dogs (with dogs outnumbering cats 5 to 1)</li>
    <li>Breed - Observed 373 unique breeds in total (55 cat and 318 dog) </li>
    <li>Age - Pet ages range between 0 (i.e., &lt; 1 year) and 13 </li>
    <li>Premium - Premiums fall into a wide range with a few outlier values close to $1000 </li>
    <li>Deductible - Deductibles are fairly well distributed and appear to be stratified across a range of common values </li>
    <li>Median Claims - For cats and dogs, the median value for total number and total amount of claims is 0 </li>
    <li>Outlier Claims - On the flip side, both species have some significant outliers in both categories (number and amount of claims) </li>
</ul></p>   

<p align = 'justify'>While overall, the data was relatively clean, some columns needed extra attention. The bulk of the cleaning work was required in the Breed column of the pets data and to clean up a few issues related to claims. In addition, the datasets needed to be merged together to prepare for analysis on claims amounts. Below is a list of some of the key steps performed as part of the wrangling step.</p>
    
<p align = 'justify'><strong>Key Data Wrangling Steps:</strong>
<ul>
    <li>Removed special characters and extra whitespace from all other breed names </li> 
    <li>Simplified 'Mixed Breed' entries and added a new feature to designate whether a pet is 'mixed' or not</li>
    <li>Converted PetAge from a categorical column to a numeric column capturing a pet's age in years</li> 
    <li>Removed claims for pets and dates outside of our analysis window and removed zero and negative valued claims </li> 
    <li>Merged the two datasets into a single dataframe for further analysis</li>
</ul></p>


## EDA <a id ='eda'></a>

<p align = 'justify'> </p>

**Species**
* Dogs and cats display similar patterns for claims, but on average, dogs tend to have more claims and higher claims totals
* Dogs and cats both have a broad range of premiums and deductibles with similar distributions for each, but *on average*, dogs have higher premiums and higher deductibles than cats
* Around one half of pets in both species have no claims at all, but cats are more likely to have claims in one or both years

**Pet Age**
* Average total claims amounts and the variability of total claims amounts tend to rise with age 
* Age matters when it comes to claims amounts, but it doesn't follow a straighforward linear relationship

**Breed**
* As the average number of claims for a breed goes up, the average total claims amount goes up in a linear fashion
* As the number of pets in a breed increases, the variability in claims (number and amount) goes down, moving the breed closer to the linear trend line
* Breed matters when it comes to total claims amounts and we'll need to determine how to handle this categorical feature given the large number of unique values

**Correlation**
* Generally, we have weak to no correlation between our features and our target variable (AmtClaimsYr2)

**Dropped Columns**
* Based on our analysis above, we decided to drop 'EnrollPath' and 'MixedBreed' from our dataframe


## PREDICTIVE MODELING <a id ='model'></a>

<p align = 'justify'>TBD </p>


## RESULTS <a id='results'></a>

<p align = 'justify'> TBD </p>


## FUTURE RESEARCH <a id = 'future'></a>

- <p align = 'justify'>TBD</p>