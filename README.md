# wang-ml-sample

## Structure of repository:
At the top level, this repo contains two folders:

1. scripts: this sub-folder contains all the python code (in jupyter notebooks) written to accomplish tasks 1-6. [Check here](scripts) 
2. data: this sub-folder contains csv files that were either sourced from the web, or were created as intermediate dataframes for analysis. [Check here](data)

For every task in the assingment, I have mentioned (at least) the following five things: 
1. High-level approach to the problem 
2. Key Tradeoffs & Technical solutions considered in implementation
3. Sequence of steps to implement solution
4. Scripts written that can be found in the scripts folder 
5. Data collected that can be found in the data folder

Limitations have been mentioned wherever necessary.

## Task 1:
Compile a sample of 400 papers from MIT’s Computer Science and Artificial Intelligence Lab.
* URL: https://dspace.mit.edu/handle/1721.1/5458/browse?type=dateissued
* Timeframe: focus on year 2000 to today.

**Approach to the problem**:
To compile 400 papers from the web, I broke down the process into the following 3 steps:
1. Starting 2000, get research paper URLs and metadata URLs for every paper. Save this data for every page in a csv file
2. Using the metadata links, fetch all the metadata provided
3. Sample 400 research papers based on different criteria (pick _n_ from certain years or select based on _subject_ availability)

**Key Tradeoffs & Technical Solutions Considered:** 
1. Sampling design 
2. Ability to iterate / experiment with more data
3. Server limits

Based on these factors, I tried the following three technical implementations:
1. **Scraping everything using _requests_:** This failed because _requests_ does not handle the issue of _timeouts_ well. 

2. **Limiting scraping data; collection using AWS lambda:** To deal with failed server requests, I considered using _pagination-info_, i.e. the fact that results for 2000 onwards exist from _2708_ to _3804_ along with lambda to send requests in parallel. In this design, I would have chosen equally distributed intervals of say, 20 or 40 between _2708_ and _3804_ and used those to send requests through AWS. I didn't end up doing this because metadata across years didn't seem consistent, so I did not want to lose important data, and setting up AWS was time-consuming.

3. **Isolating link collection and metadata scraping using Selenium:** Selenium resolved the problem of _server-timeouts_. Once I tested that out, I broke down functions such that we would scrape all links first, save them page by page, and then go through each to collect metadata. By isolating processes this way, I was able to debug better, and explore data much better. The only drawback of this solution was collecting so much data took time. 

**Folder Navigation:** 
1. scripts:
    * [getting research paper links](wang-ml-sample/scripts/compiling_paper_links.ipynb)
    * [fetching metadata](wang-ml-sample/scripts/fetch_metadata.ipynb)
    * [sampling 400 papers](wang-ml-sample/scripts/metadata_sampling.ipynb)
2. data:
    * [paper_links](wang-ml-sample/data/paper_links) 
    * [metadata](wang-ml-sample/data/metadata) 

**Sequence of Steps**
- Downloaded research paper links and metadata links, page by page. Script [here](wang-ml-sample/scripts/compiling_paper_links.ipynb); data [here](wang-ml-sample/data/paper_links) 
- Aggregated research paper links in one csv for exploration. Data [here](wang-ml-sample/data/paper_links/all_research_paper_links.csv)
- Downloaded metadata for research papers, page by page. Script [here](wang-ml-sample/scripts/fetch_metadata.ipynb); data [here](wang-ml-sample/data/metadata)
_ Sampled 400 papers using 2 methods: picking 25 random papers from 16 years and picking the ones with non-null values of _subject_ first. Script [here](wang-ml-sample/scripts/metadata_sampling.ipynb)

## Task 2:
Categorize each author’s nationality or ethnicity.

**Approach to the problem:**
For this problem, the approach was simple: look at the column _dc.contributing.author_, split the names and find a way to leverage the _last-name_. 

**Key Tradeoffs & Technical Solutions Considered:** 
1. Isolating intermediate dataframe
2. Modelling / Predicting ethnicity

Based on these two factors, I tried the following technical implementations:
1. **Isolating intermediate dataframe:** I created a separate dataframe for authors, where one row is an author. The key design principles here are isolation and efficiency - once we have relevant columns just for authors, we can conduct author specific analysis more easily

2. **Modelling / Predicting ethnicity:** For this, I tried working with the python package _ethnicolr_. However, I faced errors in package management, so I used the US Census data, tried to find closest matches of author names to Census names and assign an ethnicity accordingly. Specific steps are mentioned below.

**Folder Navigation:** 
1. scripts:
    * [predicting ethnicity for authors using Census](wang-ml-sample/scripts/metadata_analysis_authors.ipynb)

2. data:
    * [aggregated author dataframe](wang-ml-sample/data/authors/authors.csv)
    * [aggregated author dataframe with ethnicity](wang-ml-sample/data/authors/authors_ethnicity.csv)
    * [2010 census names](wang-ml-sample/data/census_names)

**Sequence of Steps**
- Downloaded names from a 2010 US Census dataset that covers _Frequently Occurring Surnames in the 2010 Census: Surnames Occurring at Least 100 Times Nationally._ This data has a list of ~160K names. For each _lastname_, they provide a breakdown of lastnames observed in given ethnicities:

| Column Name | Description                                                                                   |
|-------------|-----------------------------------------------------------------------------------------------|
| pctwhite    | Percent Non-Hispanic White Alone                                                              |
| pctblack    | Percent Non-Hispanic Black or African American Alone                                          |
| pctapi      | Percent Non-Hispanic Asian and Native Hawaiian and Other Pacific Islander Alone               |
| pctaian     | Percent Non-Hispanic American Indian and Alaska Native Alone                                  |
| pct2prace   | Percent Non-Hispanic Two or More Races                                                        |
| pcthispanic | Percent Hispanic or Latino origin                                                             |

All of these values add up to 100%. 
- For each _lastname_, I assigned an ethnicity based on the highest value observed in columns above. So, for example, for the _lastname_ **Smith**, the _pctwhite_ value is **70.9**, so Smith is assigned the ethnicity White. 

- Then, I found _most-similar_ matches to author names in Census names using _cosine similarity_. 
- Finally, I assigned the ethnicity of the _most similar_ last name to the author. All these steps are [here](wang-ml-sample/scripts/metadata_analysis_authors.ipynb)

**Limitations:**
1. The US Census database used for this analysis only has 6 meaningful ethnic divisions as shown above. _Ethnicolr_ has a lot more divisions, which would provide more insight, especially because many White names have European origins. 

## Task 3:
Categorize research topics: sub-categories within AI (e.g. facial recognition, speech recognition)

**Approach to the problem:**
I looked at the following 3 columns to get some idea of relevant AI topics: _dc.subject_, _dc.title_ and _dc.dc.description.abstract_. Using these columns, I examined: 
* frequently occuring _subject names_, 
* n-grams in papers abstract, 
* topics in papers abstract

and finally used a pre-trained bart model with zero-shot classification to assign topic labels to each research paper.

**Key Tradeoffs & Technical Solutions Considered:** 
1. Isolating intermediate dataframe 
2. Iterative approach to labelling / aggregating sub-topics

Based on these two factors, I tried the following technical implementations:
1. **Isolating intermediate dataframe:** Similar to the approach taken for authors, I created a separate dataframe for subjects, where one row is a subject. Again, the key design principles here are isolation and efficiency. 

2. **Labelling/Aggregating sub-topics:** My sampling design used to select 400 research papers gives priority to papers that have non-null values for _subject_, however, I designed all functions such that this dataframe can be replaced by any other selection of 400 papers and the code would still work. For this specific sample though, 

**Folder Navigation:** 
1. scripts:
    * 

2. data:
    * 
    * 
    * 