Here are my working notes, including ideas and approaches.

# Dec 18
I begin with the first deliverable of trying to get all the websites.

Idea: is there a sitemap?
Idea: is there a pattern to the url, in a way such that we can map actual school district names

Observation: The individual link (e.g. https://go.boarddocs.com/oh/jtmo/Board.nsf) is searchable on Google.

Approach: I downloaded a HTML `div` of school districts from Ballotpedia [here](https://ballotpedia.org/List_of_school_districts_in_the_United_States) into `school_district_list.html` and parsed it using `get_school_districts.ipynb` into `school_districts.csv`.

TOREPORT: District of Columbia is not included.

Approach: I then use this list of school districts and search Google with the appended search term "BoardDocs". Most of the time this returns the intended web URL.

Realization: According to Google, there are ~13k school districts instead. I found a dataset on Kaggle with ~13k school districts [here](https://www.kaggle.com/datasets/shivd24coder/us-school-districts-census-data). I will use this instead of the `school_district_list.csv` scrapped from Ballotpedia.

I have downloaded the dataset as `kaggle-school-districts.csv`, and renamed the columns "State Postal Code" to "state" and "Name" to "school_district".

Currently scraping with 100 links per minute, so 13k links will require 130 minutes or a couple of hours.

# Dec 19
I will look into the link and try to scrape 