# Supplementary material

All codes in this Markdown were made using JupyterLab, and executed cell by cell.

To make the scripts we use the programming language Python. The follow librarys helped us to get the job done:

* requests
* BeautifulSoup
* pandas

For each competition (Adidas Next Generation, Euroleague and Eurocup), data from different pages in the respective website are scraped, considering the year of the competition and the information we want. To do that we need to search in two different pages for every year, for each competition, because the 'minutes_played' and 'index', were in different pages. Before gathering all the data, we made a third script, to delete duplicated information. 

## 1. Adidas Next Generation

All data came from: (https://www.adidasngt.com/). 

1.1. [Index](https://github.com/crizmorais/paper_euroleague_players/blob/main/index_u18.ipynb)

1.2. [Minutes played](https://github.com/crizmorais/paper_euroleague_players/blob/main/minutes_u18.ipynb)

1.3. [Delete duplicates](https://github.com/crizmorais/paper_euroleague_players/blob/main/dupU18.ipynb)

## 2. Euroleague

All data came from: (https://www.euroleague.net/).

2.1. [Index](https://github.com/crizmorais/paper_euroleague_players/blob/main/index_euroleague.ipynb)

2.2. [Minutes Played](https://github.com/crizmorais/paper_euroleague_players/blob/main/minutes_euroleague.ipynb)

2.3. [Delete duplicates](https://github.com/crizmorais/paper_euroleague_players/blob/main/dupEuroleague.ipynb)

## 3. Eurocup

All data came from: (https://www.eurocupbasketball.com/)

3.1. [Index](https://github.com/crizmorais/paper_euroleague_players/blob/main/index_eurocup.ipynb)

3.2. [Minutes Played](https://github.com/crizmorais/paper_euroleague_players/blob/main/minutes_eurocup.ipynb)

3.3. [Delete duplicates](https://github.com/crizmorais/paper_euroleague_players/blob/main/dupEurocup.ipynb)


## 4. Gathering data

To gather all data in a single CSV file, we needed to made a fourth script. But, unlike the others, in this case we had to explore the data to fill all the gaps. To a better undestand of the process, you can access the Jupyter Notebook available in the link below.

[Gathering Data](https://github.com/crizmorais/paper_euroleague_players/blob/main/merge_bases.ipynb)

## 5. Disclaimer

There are probably several ways to do the same process proposed above, faster and even more efficiently. Even so, we believe that sharing the work done can serve as a starting point for those who want to do something similar.
