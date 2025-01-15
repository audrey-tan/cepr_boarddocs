# Jan 5

Recap: I downloaded the NCES data from this [link](https://nces.ed.gov/ccd/files.asp#Fiscal:2,LevelId:5,SchoolYearId:38,Page:1), which gave me `nces2324.csv` and `nces_directory_companion.csv`.

The merge work is at `merge_nces.ipynb`.

First merge by website, since most coverage.

Then, I now merge by phone. I realized that some websites did not merge because of the various `http` and `www.` forms. I will remove them and do a new merge at the website merge place.

By cleaning the `https` and `www`, I managed to increase the proportion of matches from 37% to 60%.

Merging by phone matches 71% of the remaining ones.

I also realize that some websites are thrown off by the trailing `/`. Putting this at the start.

This further increases the matches from 60% to 78%.

Total matches: 92.8%.

# Jan 15

Match by unique zipcode.

Zipcode matches 78. But some matches are bad. Discard all matches.

Decide to match by name directly. Lower case them. Drop the words "school district" and "SD". Then remove all spaces.

This increases matched to 97.1%.