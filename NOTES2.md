# Week 1

Prior to today, I had a meeting with the previous RA on the background of the research, its current state, and the tasks moving forward.  

The main goal right now is to obtain the address and phone number for the schools for Simbli. 

Today I set up the project environment, code environment, analyzed the .csv files, reviewed the notes to see how the previous RA merged the BoardDocs and NCES data, and studied the .ipynb codes from deliverable #2 to brainstorm ideas on how to go about the merging for Simbli.

The previous RA attempted to scrap the address and phone number data from the Index.aspx page but failed because of the scrapper protection system, I will have to find a workaround for it.

I attempted to run `scrape_simbli.ipnyb` and `get_simbli_urls.ipnyb` to better understand the problem with the scrapper protection system. 

I got some help from StackOverflow and other online forums for Incapsula but none worked, so I will be attempting to use scrapper services and see which one works.

I'm trying a scrapper tool called Zenrows to see if it works. I tested it and Zenrows worked for unprotected sites and other site protected by Incapsula but did not work for Simbli. Might have to try out different parameters. 

Seems like it's a dynamic JS rendering problem since it does return an html code, but when I included JS rendering and increased the wait time, I get a `422` error "RESP001 Could Not Get Content". Tried using the premium proxy too but got the same results. 

Maybe I'll try to wait for a specific element instead of using a fixed wait time. 

Good news! After looking through Inspect to find a specific element, I instead stumbled upon the URLs that responds with the exact data we need in JSON format. Seems like we don't need to scrape the webpage afterall––I might be able to use API calls instead.  

It looks something like this: `https://simbli.eboardsolutions.com/CoreServices/api/HomePage/?sid=zxfvZYmcKkpluslhKHzD4ftFA==&uid=`

Tried to obtain the needed information with a python script and it worked. I was able to obtain the name, address, city, state, zip code, phone, fax, and website. I can match it to the `simbli_urls` using the name since it should be the same. 

But before that, I need to find out how I can get the SID for the URL for each school. I'll try analyzing the network and looking for a pattern. 

The SID is in Base-64 string so it should be nearly impossible to do trial-and-error, but maybe I can decode it? I tried it and it outputted a string which seems to be made of hexadecimal representation. I'll try to decode it too. I'll do this to several samples and try to find a pattern if I could. 


# Week 2

Looking at the decoded SIDs it all seems like nonsense. It's probably impossible to find a pattern since they might be randomly generated.

I'm trying to see if I can use Python or JS to extract the URLs called after a URL instead. SUCESS!!! I was able to get all the API calls with Playright in Python and ChatGPT's help in debugging. 

Unfortunately, because I called the URL from code too many times, Simbli temporarily blocked me (`Error 15 Access Denied`). I'll have to find a fix to this.

I tried using rotating proxies for this by got `Error 16` instead. Revision: I think it only *sometimes* gives off `Error 16`, so I'll just run the script several times to make sure we get all of them.

Also, I'll run it in smaller batches to not overload my computer. 

I've realized that not all schools have an `Index` page with their information. However, they still have a link to their website if you click the logo, which is also revealed in a different URL, so instead of saving the URL, I'll save the SIDs from the endpoints so I can use it on a different endpoint to at least get the website for those schools.

By scrapping the Index page and website link from the top left corner logo of the school for the schools with no Index page, I have succesfully written extracted the addresses, zip codes, phone numbers, fax numbers, and school websites from Simbli from the Index page. This can be found in `simbli_school_data.json`. Code can be found in `new_scrape_simbli.ipynb`.

I'll start export this into a .csv file and start writing code to merge it with the NCES data. 


# Week 3

I cleaned the simbli school data into standardized formats for matching code can be found in `merge_nces_simbli.ipynb`. The .csv file of the simbli school data can be found in `simbli_school_data.csv`. Started with 18% so I tried matching with 2 variable combinations. Ended up with 92.51%.


# Week 4

As per the meeting with Professor Kane, I'll head straight on to working on deliverable 4. 

I'll start by studying the BoardDocs website for a school to see where the documents that need to be extracted are and look for patterns that might be useful.

So apparently there is a search bar in each of the school's BoardDoc website. This search bar can look for words or phrases that appear in the meeting's agenda or in the files (like pdf files) attached in the meeing. If the word appears in a file, the search bar would return the meeting's agenda in which the file is found. However, there can be multiple files attached in a meeting agenda (and in different sections, at that) and there seems to be no way of telling which file contains the keywords we searched for.

The idea right now is to leverage this search bar to obtain all the pdf files (will look if there are other types of files necessary) to minimize the number of files to be searched through and then implement an additional filter to only extract the relevant files.

Upon further inspection, all details of a meeting are summarized through a "View Minutes" button. However, to find this button I had to press the back button after pressing a meeting because it redirected me to the Agenda page instead. I might just use this instead for conciseness and standardization since it would make validation easier as well.  

Idea workflow:
1. Get all meetings of school (eg. `https://go.boarddocs.com/de/sussexvt/Board.nsf/BD-GETMeetingsListForSEO?open`)
2. Get filtered meetings with keyword (eg. `https://go.boarddocs.com/de/sussexvt/Board.nsf/BD-SearchInContext?open`)
3. Match ID from filtered meetings to ID in all meetings ID
4. Obtain meeting minutes through "View Minutes" (eg. `https://go.boarddocs.com/de/sussexvt/Board.nsf/BD-GetMinutes?open`)
5. Check if the keyword actually exists in the meeting minutes
6. Download meeting minutes if it does

I'll test it out with 1 school first.

Step 1 was succesfully coded. 

There was an issue with Step 2 where the request returned no IDs. Then I realized that it returned the response in XML format instead of JSON. Still no IDs. I looked into the Payload of the Network and included the data (all meeting IDs of that school from Step 1) and the search keyword and it worked. There was also a weird issue with Step 2 where it returns twice as many IDs. Then I noticed that it outputs a pattern of exisiting ID and non-existing ID with the non-exisiting ID sharing the same beginning characters, so I just removed the non-exisitng ones by matching it to the list of all meeting IDs for that school, which was basically Step 3 anyway.

Now onto Step 4. This part is a bit tricky. Even as I paste the URL url in the browser nothing appears. Adding data from the payload also not working. It's not returning JSON or XML, but HTML instead. Might need to convert this HTML file to PDF later for download but that's a problem for later. 

Fixed a bug in the code--now it works.

Step 5 worked successully.

Now onto Step 6. I'll need to research on how to do this. There's a tool called `wkhtmltopdf` but has already been abandoned so might not use it. After a bunch of trial-and-errors, I was successful using `xhtml2pdf` open-source library.

Code for Step 1-6 can be found in `steps.ipynb`.

Next I will put together all the steps to automate it for one school and then all schools. 

I realized that some keywords are located not in the meeting minutes but agenda details and files within the agenda instead. I'll write some code to scrape those as well. The problem is that I searched online and found that schools can attach any type of document into the meeting agenda. This would require writing individual code for EACH individual file types (eg. pdf, docx, pptx, txt, doc, xlsx) to search through the document to see if the keyword exists since each file type have different formats. Going through BoardDocs, I'm only seeing pdfs, so I will do just pdf for now and will confirm with Professor Kane if I should work on the other file types now or move on to the next step first. 

Another problem: some pdfs attached in the meeting agenda are encrypted. 

Completed. Results can be found in `scrape_schools.ipynb`.


# Week 5

I noticed a bug in HTML to PDF conversion because some colors in the HTML are undefined. Solved by sanitizing the HTML by replacing the undefined colors before conversion. I also expanded the BoardDocs document extraction tool to search and scrape .docx, .doc, and .pptx files attached to the meeting. 


# Week 6

I will start working on BoardBook. I'll put this in the folder `deliverable5 - boardbook tool`.

First, I need to find the urls in which the meeting minutes are stored. 

I found this: `https://manuals.boardbook.org/Public/`, which conveniently has the list of all the public schools that uses BoardBook. I'll extract this and their BoardBook indices from the URL linked to each school name. 

Sucssefully extracted the names and indices of each school. Total: 2167 rows.
Code is in `boardbook_get_schools.ipynb` and school list is in `boardbook_schools.csv`.

Next, I'll find where the information (address, phone number, etc.) of each school is stored. 

Ok while looking, I found something weird. There is a "BoardBook Manual" which I extracted the schools from above and a seperate "BoardBook Premier" `https://meetings.boardbook.org/Public/`. I don't know the difference so let me also extract the school names and indices from this one and see if there is any difference. 

So there is 4 index mismatch between `boardbook_schools.csv` and `premier_boardbook_schools.csv` and 439 schools missing in `premier_boardbook_schools.csv`, so I will be using `boardbook_schools.csv` and manually check the 4 mismatched schools. 

These 4 schools are:
-`Middletown Public Schools`
-`Tecumseh Public Schools`
-`Clinton Public Schools`
-`Lakeview Community Schools`
Their manuals and premier versions have different outputs.

I'll just keep both for now and decide how to handle them later.


After spending more time going through BoardBooks and external searches, I don't think BoardBook has the additional information for the schools (address, phone number, etc.). The closest it has is the meeting locations, but I checked and they're usually not the same as the school address. Also, BoardBook does not have a search feature like BoardDocs. 

Difference breakdown:
- Simbli has a feature that can search keywords accross all meetings from all schools.
- BoardDocs has a feature that can search keywords accross all meetings for ONE school at a time.
- BoardBooks has no search feature we can use.

Thus, I would have to make it from scratch.

Good news! I accidentally found the search feature for BoardBooks when trying to when analyzing the results from code to download all files in a page. 

`https://meetings.boardbook.org/Search/Index/3184`

This means that BoardBooks actually HAS a search feature and it's similar to BoardDocs', except that it's more efficient in the sense that it can search by Agenda Item, File, and Meeting. Also, it returns the actual document that contains the keyword, unlike BoardDocs' search feature that .

It's still different from BoardDocs so I can't just use the BoardDocs code, but at least it shouldn't be too far off. 


# Week 7

I am working on extracting the documents from BoardBook. Analyzing the network, I found the URL for the search that returns an HTML page with the urls that download files attached and links to the meetings with the meeting ID. Then, I can use the meeting ID on this url for example, `https://meetings.boardbook.org/Public/DownloadAgenda/3184?meeting=681763` to download the meeting agenda. 

Ok I realized something, it turns out that if I download the meeting agenda from that link I just showed, it also downloads ALL files attached to that meeting into the same pdf. That means I would only need to search for 'meeting agenda' and download from that link. It's also cleaner since each meeting is compiled into 1 pdf. 

Actually, scratch that. Turns out when searching for 'meeting agenda', the search function by BoardBook searches for the keyword just in the meeting agenda and not the files in it. The files in it would be returned by the search in a seperate format if we specifically search for 'file attachments', so we'd still need to search for both. 

I wrote the code to iterate it for all schools. When running it I found out- not all schools have the search feature in BoardBooks. I don't know why it was made like this. Is there a different tier each school can purchase for BoardBooks so that only some includes the search feature? I don't know.

Anyway, I'll loop through the schools to see which one has the search feature both on the `https://manuals.boardbook.org/` and `https://meetings.boardbook.org/`. From my observations on some schools, it seems that these schools with no search feature also has no download agenda feature.

So 788 schools out of the 2167 has a search feature on `https://manuals.boardbook.org/` and also 788 on `https://manuals.boardbook.org/`. I wrote code to check if they overlap and found that 783 schools overlap. I'll leave it at that for now.

I'll consult with Professor Kane on how we should deal with this.


# Week 8

I'm going to match the NCES data for BoardBooks by name. Since there is no other data I can base it on, I removed the duplicates in the NCES data to merge it. Results can be found in `merged_boardbook_with_leaid.csv.csv` with a 55.33% success rate.

I will now move on to making the front-end for the other researchers to use.


# Week 9

I considered several options in making the front-end like using React or Django and then publishing it on platforms like Vercel, but for the sake of handover I think it might be better to use Flask for its simplicity in running the program and also so that if future changes were to be made to the code, there would be no need to deal with deployment or additional platforms. 

I made the frontend in another folder and can be accessed in the seperate github link later.


# Week 10

I will now work on making the extractor for Simbli. This should be similar to BoardBook as it has a search feature, except that its search can search through all schools at once. I'll base the search off of the `Meetings` tab same as the deliverable for BoardDocs written on the blurb. 

I'm looking through Networks and realizing how far more complicated Simbli is compared to BoardBook, so I decided to look through the search feature for individual schools first. Firstly, I found `https://simbli.eboardsolutions.com/CoreServices/api/Search/SearchMeetingModule` that returns a list of the meetings that contains the keyword whether in the minutes or documents attached to the meeting. However, this URL only returns 25 meetings at a time and returns more in subsequent calls of the URL. I will have to figure out how they determine the subsequent calls (maybe through payload?). --this problem was solved by using the "next" button that appears in the popup of the first item instead.

Also, to differentiate minutes from attached documents, I found that we can use these two keys:
`"ExistInAgenda": true,`
`"ExistInMinutes": false,`
If ExistInAgenda is true, then the keywords appear in attached document(s). If ExistInMinutes is true, then it appears in the meeting minutes. 

I tried plugging in the `MeetingId` from the search results to a URL to get the meeting page but it prompted me to log in using username and password. I have to find a way to access the page in public domain.

I found a different URL (kinda hidden) that returns the encrypted IDs needed to access the meetings page on public domain. Example of URL: `https://simbli.eboardsolutions.com/CoreServices/api/Search/GetSearchedMeetingData/?sid=zxfvZYmcKkpluslhKHzD4ftFA==&cacheKey=rkyookptilw45yfbruboymz4_MM&iid=d60e0tRXNsslshco4wLRTnxiA==`. Scratch that- it also triggers Incapsula. Also, I looked through a bunch of other URLs I might potentially use, but a lot of them requires a lot of randomly generated IDs (like 'zxfvZYmcKkpluslhKHzD4ftFA') that are not all obtainable from the Network. 

I tried using Selenium instead but again encountered Incapsula. I keep getting Incapsula with Playwright as well, but I have an idea: I'll try writing code for the console instead. If it works, it might not make it to the frontend tool with Boarddocs and Boardbook but at least it should automatically download all the files and I'll write a documentation on how to use it.


# Week 11

THe console code works. However, it is only able to download the documents attached to the meeting that contains the keyword and not the meeting minutes. I found that it's because the URL to print the meeting minute is on another page AND it's generated dynamically for temporary use. 

I'll write up a documentation for Simbli since it's different from BoardDocs and BoardBook.


# Week 12

Finishing up Simbli code, cleaning up notes, code, and writing the documentation.