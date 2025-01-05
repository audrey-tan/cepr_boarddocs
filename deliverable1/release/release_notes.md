# Release notes for deliverable 1

The data file can be found at [deliverable_1.csv](deliverable_1.csv).

## Original Specification

> Deliverable #1: Find all the websites using the https://go.boarddocs.com/ stem (even if they are
not school boards), capture the name and address of the organization (typically in the top left
corner of the webpage as in the above).

## Result specification

The final csv at [/deliverable_1.csv] has 3,905 rows and 6 columns. The columns are
- `URL` which is the BoardDocs URL
- `school_district` which is the school district name scrapped from BoardDocs
- `address` which is the school district name address from BoardDocs
- `home_website` which is the main school website scrapped from BoardDocs (not the BoardDocs URL, but a public-facing website)
- `phone` which is the phone number extracted from `school_district` or `address`.

The `URL` and `home_website` are guaranteed to be valid URL strings.

The `school_district` and `address` columns are guaranteed to be strings or empty.

The `phone` column is guaranteed to be 10 digit phone number in the format `(???) ???-????`.

## Statistics

We collected 3,905 unique BoardDocs URL.

Of these, I managed to find `school_district` values for 93.2% of them. The remaining 6.8% rows have empty values in that column.

`address` values are found for 74.8% of them. The remaining 25.2% rows have empty values.

87.2% or 3,407 of them have "school", "academy", "district", or "education" in their `school_district` or `address` columns. The remaining 12.8% don't.

Additionally, I identified the home website for 98.6% of the rows (3,852), which can be used to match with the NCES database. This is not the BoardDocs website, but the school district's public facing website.

I also extracted the phone numbers for 56.9% of the rows.

Collectively, 98.8% of the rows have either a home website or a phone number, which we can use to match with NCES.

## Working notes

A copy of the working notes is attached as [working_notes.md](working_notes.md).