Toronto Condo Rental Analysis



An end-to-end data project that collects Toronto condo rental listings, convertssemi-structured web content into analysis-ready records, and examines how askingrent varies by location, unit configuration, floor area, parking, and propertycharacteristics.

The project combines a historical Jupyter Notebook analysis with a modern,Chromium-based collection pipeline designed for rendered search-result pages.

Project Status

Component

Status

Historical exploratory analysis

Complete

Chromium-based scraper

Implemented and validated

One-page validation

54 records, 0 parse errors

Multi-page production collection

Ready for controlled testing

Automated tests and dashboard

Planned

The historical findings below describe the saved Notebook dataset, not thecurrent Toronto rental market. Live listings and prices change continuously.

Project Objectives

Build a reproducible workflow for collecting Toronto condo rental listings.

Parse and standardize price, room, bathroom, parking, and floor-area fields.

Preserve raw collection snapshots for traceability and future comparison.

Compare rental supply and asking prices across Toronto market areas.

Identify relationships between asking rent and unit characteristics.

Present the work as a maintainable, portfolio-ready analytics project.

Data Acquisition Workflow

Current workflow

The current scraper uses DrissionPage to open public search-result pages in anormal Chromium browser. This replaces the original static HTTP approach, whichnow receives a Cloudflare 403 response.

flowchart TD
    A[Rendered search page] --> B[Locate listing cards]
    B --> C[Parse listing fields]
    C --> D[Validate and deduplicate]
    D --> E[Save page checkpoint]
    E --> F[Timestamped raw CSV]

For each requested page, the pipeline:

Opens the Toronto rental search page in Chromium.

Waits for rendered listing content.

Identifies listing cards from their address elements.

Extracts structured values from each card.

Records parsing errors separately instead of silently discarding them.

Deduplicates listings by source URL, with a field-based fallback.

Replaces the checkpoint CSV after every successfully processed page.

Saves a separate timestamped CSV when the run finishes.

The scraper does not attempt to bypass verification challenges. If the websitepresents one, collection should stop for review.

Historical Notebook workflow

The original Notebook used Requests and Beautiful Soup to:

scan search pages 1–76;

discover 3,280 property-detail links;

visit individual property pages with a request interval;

clean incomplete and duplicate observations; and

produce an exploratory dataset containing 2,782 listings.

The saved Notebook outputs preserve this earlier market snapshot even though thelegacy scraper is no longer reliable.

Current Data Schema

Field

Description

ScrapedAtUTC

UTC collection timestamp

SourcePage

Search-result page number

SourceURL

Individual listing URL

Address

Unit number and street address

PriceCAD

Monthly asking rent in Canadian dollars

Room

Bedroom/den configuration, including Studio

Bath

Number of bathrooms

Parking

Number of parking spaces

SizeMinSqft

Lower bound of the reported floor-area range

SizeMaxSqft

Upper bound of the reported floor-area range

SizeMidSqft

Midpoint of the reported floor-area range

SizeSqm

Midpoint converted to square metres

Neighbourhood

Local neighbourhood, when available

Area

Broader Toronto market area, when available

PropertyType

Reported property category, when available

Furnished

Furnishing status, when available

OutdoorSpace

Reported outdoor-space category, when available

AgeOfBuild

Reported building age, when available

RawText

Original normalized card text for audit and reprocessing

Analysis Results

Current scraper validation

The updated parser was tested against the first rendered search page:

Validation metric

Result

Pages collected

1

Records collected

54

Parse errors

0

Duplicate records after processing

0

The validation also confirmed corrections for:

Studio room labels;

parking values that were previously confused with street numbers; and

comma-formatted size ranges such as 1,000–1,199 sqft.

Historical market snapshot

After cleaning, the original Notebook analysis contains 2,782 rental listingsacross nine Toronto market areas.

Metric

Historical result

Cleaned listings

2,782

Mean asking rent

$3,124.51/month

Median asking rent

$2,800/month

Mean unit size

79.86 m²

Downtown listings

1,506

Downtown share

54.1%

North York listings

474

Rent and listing volume by area

Area

Listings

Mean Rent

Median Rent

Downtown

1,506

$3,230.87

$2,800

North York

474

$2,986.58

$2,900

Midtown / Central

202

$3,384.25

$2,850

Etobicoke

201

$2,927.35

$2,750

West End

197

$3,009.03

$2,800

Scarborough

127

$2,653.65

$2,700

East End

50

$2,863.90

$2,675

East York

14

$2,730.00

$2,597.50

York Crosstown

11

$2,531.45

$2,600

Key observations from the historical analysis

Downtown accounts for slightly more than half of the cleaned listings, socity-wide statistics are strongly influenced by the downtown market.

Midtown / Central has the highest mean rent among areas with at least 100observations, followed by Downtown and West End.

Listings with parking show higher average asking rents in the sample, althoughthe relationship may also reflect unit size, location, and property type.

Recorded asking rents contain substantial outliers, including values from$149 to $25,000 per month. These require validation before predictive work.

The original Notebook represented each size range using its upper bound. Thenew scraper preserves both bounds and also calculates a midpoint.

The Notebook includes rent distributions, area-level comparisons, room andparking comparisons, property-type summaries, price-versus-size plots, andpairwise numeric exploration.

Technology Stack

Category

Tools

Language

Python 3.11

Current collection

DrissionPage, Chromium

Historical collection

Requests, Beautiful Soup

Data processing

Pandas

Visualization

Matplotlib, Seaborn

Analysis environment

Jupyter Notebook

Version control

Git, GitHub

Repository Structure

Projects/
├── .gitignore
├── README.md
├── requirements.txt
├── src/
│   └── scraper.py
├── data/
│   └── raw/                         # generated locally; ignored by Git
├── WebScraping_Project_Condos_Final_ (2).ipynb
└── Corporate_Credit_Rating_Forecast.ipynb

The repository currently contains more than one portfolio project. Moving thecondo analysis into a dedicated repository is included in the roadmap.

Running the Project

1. Clone the repository

git clone https://github.com/BlazingTheTrail/Projects.git
cd Projects

2. Create and activate a virtual environment

macOS or Linux:

python3 -m venv .venv
source .venv/bin/activate

Windows PowerShell:

python -m venv .venv
.venv\Scripts\Activate.ps1

3. Install the scraper dependencies

python -m pip install --upgrade pip
python -m pip install -r requirements.txt

4. Run a one-page validation

python src/scraper.py --start-page 1 --end-page 1

5. Run a controlled multi-page collection

python src/scraper.py --start-page 1 --end-page 10

Enable detailed logging when diagnosing a run:

python src/scraper.py --start-page 1 --end-page 10 --verbose

Generated files are written to data/raw/:

Output

Purpose

toronto_condos_checkpoint.csv

Latest accumulated checkpoint

toronto_condos_parse_errors.csv

Records that could not be parsed

toronto_condos_YYYYMMDD_HHMMSS.csv

Final timestamped snapshot

Command-line options

Option

Default

Description

--start-page

1

First page to collect

--end-page

1

Last page to collect, inclusive

--output-dir

data/raw

CSV output directory

--timeout

20

Seconds to wait for the first listing

--verbose

off

Enable debug logging

Open the historical Notebook

Install the optional analysis packages:

python -m pip install jupyter matplotlib seaborn
jupyter notebook "WebScraping_Project_Condos_Final_ (2).ipynb"

The Notebook contains saved outputs and can be reviewed without rerunning itslegacy web-scraping cells.

Data Quality and Limitations

Search listings are dynamic and may change between collection runs.

Search results may not represent the entire Toronto rental market.

Some descriptive fields are not displayed on every result card.

Asking rent does not necessarily equal the final contracted rent.

Listing text and page structure can change without notice.

Historical results are descriptive and do not establish causality.

Outliers and implausible values require business-rule validation.

A single-page scraper test confirms parsing behavior but not long-runstability across all available pages.

Roadmap

Preserve the original Notebook and historical outputs.

Replace the blocked static scraper with a Chromium-based pipeline.

Add logging, deduplication, checkpoints, and parsing-error output.

Add .gitignore and a reproducible requirements.txt.

Validate price, room, bathroom, parking, and size parsing on page 1.

Run and audit a controlled 10-page collection.

Add automated parser tests and sample HTML fixtures.

Build a cleaning and validation module for the current schema.

Export publication-ready charts to outputs/charts/.

Add rent-per-square-foot and neighbourhood-level analysis.

Compare timestamped snapshots to measure rental-market changes.

Build an interactive dashboard in Power BI, Tableau, or Streamlit.

Move the project into a dedicated repository.

Responsible Use

This project is intended for education and portfolio demonstration. Anyonerunning the collector should review the source website's current terms, robotsguidance, privacy expectations, and request limits, and should use conservativecollection settings.

Author

Xiang DingData Analytics and Risk Analytics PortfolioGitHub profile