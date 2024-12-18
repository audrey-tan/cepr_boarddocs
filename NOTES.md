# Dec 18
I begin with the first deliverable of trying to get all the websites.

Idea: is there a sitemap?
Idea: is there a pattern to the url, in a way such that we can map actual school district names

Observation: The individual link (e.g. https://go.boarddocs.com/oh/jtmo/Board.nsf) is searchable on Google.

Approach: I downloaded a HTML `div` of school districts from Ballotpedia [here](https://ballotpedia.org/List_of_school_districts_in_the_United_States) into `school_district_list.html` and parsed it using `get_school_districts.ipynb` into `school_districts.csv`.

TOREPORT: District of Columbia is not included.