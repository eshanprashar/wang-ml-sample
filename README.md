# wang-ml-sample

## Structure of repository:
At the top level, this repo contains two folders:

1. scripts: this sub-folder contains all the python code (in jupyter notebooks) written to accomplish tasks 1-6. [Check here](scripts) 
2. data: this sub-folder contains csv files that were either sourced from the web, or were created as intermediate dataframes for analysis. [Check here](data)

For every task in the assingment, I have mentioned the following five things: 
1. High-level approach to the problem 
2. Key Tradeoffs & Technical solutions considered in implementation
3. Sequence of steps to implement solution
4. Scripts written that can be found in the scripts folder 
5. Data collected that can be found in the data folder

## Tasks:
1. Compile a sample of 400 papers from MIT’s Computer Science and Artificial Intelligence Lab.
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
1. Scraping everything using _requests_: This failed because _requests_ does not handle the issue of _timeouts_ well. 
2. Limiting scraping data; collection using AWS lambda: To deal with failed server requests, I considered using _pagination-info_, i.e. the fact that results for 2000 onwards exist from _2708_ to _3804_ along with lambda to send requests in parallel. In this design, I would have chosen equally distributed intervals of say, 20 or 40 between _2708_ and _3804_ and used those to send requests through AWS. I didn't end up doing this because metadata across years didn't seem consistent, so I did not want to lose important data, and setting up AWS was time-consuming.
3. Isolating link collection and metadata scraping using Selenium: Selenium resolved the problem of _server-timeouts_. Once I tested that out, I broke down functions such that we would scrape all links first, save them page by page, and then go through each to collect metadata. By isolating processes this way, I was able to debug better, and explore data much better. The only drawback of this solution was collecting so much data took time. 

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