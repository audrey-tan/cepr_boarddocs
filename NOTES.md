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

For deliverable 1, I will take a set of all scrapped links. Then I will filter out the valid ones. This is at `clean_boarddocs_url.ipynb`. Then I will use `requests` to scrape the two fields of name and address. This is at `visit_boarddocs.ipynb`.

# Jan 2
I received a reply from Tom on Dec 30 in response to the sample. Here are his suggestions

1. There are some organizations included which are not school boards.  For instance, line 35 is the City of Hamtramck, line 76 is “Midlothian Town Hall”, line 236 is City of Marysville CA.  I would propose that you proceed and capture as many as possible.  However, please add a column to the spreadsheet noting whether the words “school”, “academy”, “district” or “education” appear in the “address” or “school_district” columns.   That way, we can go through later and weed out the other organizations.
2. In some observations, there is no text in the “school_district” column.   For example, see rows 73, 74, 76. 
3. In some observations, the text in the “school_district” column is a phone number.   For example, see rows 98 and 100.   98 is the Seminole County Public Schools and rows 100 is Spring Hill Public Schools—so these are public schools, but it will be hard to match them by later with the text in
4. The directory file that you found has a variable “LEAID” that would be very helpful for merging in other information later.  If you are using the directory information as a source of key words to find BoardDocs sites, could you add it at this point?   If so, that will simplify the merging process later.

The suggestions can be implemented after the runs on the Programmable Search Engine.

I continue to run `google_search_boarddocs.ipynb` to get the remaining links.

In the meantime, I will work on suggestion #1. The spirit of suggestion 1 is to tell apart LEAs from non-LEAs. It is tempting to classify a row as LEA based on whether it has the word "school" in the `school_district` column, but some rows don't have values for the `school_district` column and [Albany-Schoharie-Schenectady-Saratoga BOCES](https://go.boarddocs.com/ny/crboces/Board.nsf/Public) is a LEA but its `school_district` column doesn't contain the word "school".

One idea I am having now, from my work with Dell group, is that we can use the link transformer, and train it on sample that we know are correct (e.g. by phone number matching).

Ok. Taking a step back, notice that some Boarddocs put their address in the first block, some in the second. I will try to standardize this first. This work is at `clean_boarddocs_websites.ipynb`.

I noticed that on the BoardDocs website, there is a `a` tag with `id="btn-home-nav"` that links to the official school website. This can be an anchor point. I now edit `visit_boarddocs.ipynb` to include this functionality.

It seems that most BoardDocs websites have this home website link. We can use this to match with the NCES, which do have school website URLs.

I will keep examing a BoardDocs website (a least maintained one like [this one](https://go.boarddocs.com/pa/foxc/Board.nsf/Public)) and see what I find.

There's also the `h1` title tag. I will add that to the scrapper. Hmm, it turns out that it is dynamically loaded later. I also noticed some websites (like [this](https://go.boarddocs.com/ia/cbcsd/Board.nsf/Public)) just put "Board of Education" as the `h1`. We will ignore this then.

Prelim results show that 2% of the BoardDocs do not link back. For these 2%, it seems like `h1` tag can offer a way to match.

In the spirit of completing deliverable 1, I will continue to clean the cols first.

Also, we completed the Google Search! We scrapped 93.78% of all given school districts. We managed to get 11,186 unique URLs.