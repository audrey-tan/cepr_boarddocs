Here are my working notes, including ideas and approaches.

# Dec 18
I begin with the first deliverable of trying to get all the websites.

Idea: is there a sitemap?
Idea: is there a pattern to the url, in a way such that we can map actual school district names

Observation: The individual link (e.g. https://go.boarddocs.com/oh/jtmo/Board.nsf) is searchable on Google.

Approach: I downloaded a HTML `div` of school districts from Ballotpedia [here](https://ballotpedia.org/List_of_school_districts_in_the_United_States) into `school_district_list.html` and parsed it using `get_school_districts.ipynb` into `school_districts.csv`.

CANCELREPORT: District of Columbia is not included.

Approach: I then use this list of school districts and search Google with the appended search term "BoardDocs". Most of the time this returns the intended web URL.

Realization: According to Google, there are ~13k school districts instead. I found a dataset on Kaggle with ~13k school districts [here](https://www.kaggle.com/datasets/shivd24coder/us-school-districts-census-data). I will use this instead of the `school_district_list.csv` scrapped from Ballotpedia.

I have downloaded the dataset as `kaggle-school-districts.csv`, and renamed the columns "State Postal Code" to "state" and "Name" to "school_district".

Currently scraping with 100 links per minute, so 13k links will require 130 minutes or a couple of hours.

# Dec 19
Reading the second deliverable, I realize that we have to merge to another dataset that has a list of schools. I will rerun the scraper by feeding in that list of schools instead. I named the csv `nces2324.csv` and it is downloaded from this [link](https://nces.ed.gov/ccd/files.asp#Fiscal:2,LevelId:5,SchoolYearId:38,Page:1). It is the Directory, Data file.

I have asserted that the NCES Agency Identification Number (LEAID) is indeed unique.

I will create a file to create the queries for each LEA (Local Education Agency). This is `create_query_lea.ipynb`.

# Dec 20
Percentage of total school boards scrapped: 75.69%

Since we hit the 10k limit, we will have to wait for a day. In the meantime, I will prepare a small sample of 1k records to send over to Tom and see if it is ok.

For deliverable 1, I will take a set of all scrapped links. Then I will filter out the valid ones. Then I will use `requests` to scrape the two fields of name and address.