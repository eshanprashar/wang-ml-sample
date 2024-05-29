# wang-ml-sample

## Structure of repository:
At the top level, this repo contains two folders:

1. scripts: this sub-folder contains all the python code (in jupyter notebooks) written to accomplish tasks 1-6. [Check here](scripts) 
2. data: this sub-folder contains csv files that were either sourced from the web, or were created as intermediate dataframes for analysis. [Check here](data)

For every task in the assingment, I have mentioned the following five things: 
1. High-level approach to the problem 
2. Key Tradeoffs & Technical solutions considered in implementation
3. Tasks accomplished to implement solution
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
