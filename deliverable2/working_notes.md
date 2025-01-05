# Jan 5

Recap: I downloaded the NCES data from this [link](https://nces.ed.gov/ccd/files.asp#Fiscal:2,LevelId:5,SchoolYearId:38,Page:1), which gave me `nces2324.csv` and `nces_directory_companion.csv`.

The merge work is at `merge_nces.ipynb`.

First merge by website, since most coverage.

Then, I now merge by phone. I realized that some websites did not merge because of the various `http` and `www.` forms. I will remove them and do a new merge at the website merge place.

By cleaning the `https` and `www`, I managed to increase the proportion of matches from 37% to 60%.