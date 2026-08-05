# web_scrape

Home Depot publishes a set of files listing every product page on their site. These exist so Google can index them, so they are open to anyone — unlike the product pages themselves, which blocked every direct approach we tried.

The script does four things:

Reads your Excel file and collects the OMSIDs.
Downloads Home Depot's product listing files — about 169 of them, covering 4.19 million products.
Every URL in those files ends in an OMSID, so it matches your numbers against that list.
Writes a new Excel file: match found means the item is live and you get the URL; no match means N/A.

Why this approach instead of the obvious one

The obvious approach is to visit each product page and see whether it loads. We tried that four different ways and Home Depot blocked all of them. The sitemap route sidesteps that entirely — it reads a list Home Depot publishes on purpose rather than knocking on a door that is guarded.

It is also faster. One bulk download answers your whole list at once, instead of hundreds of individual requests.

What to remember when you use it

A URL in the Results tab is reliable.
N/A means "not in Home Depot's published list," which is almost always the same as not live — except for items that went live very recently.
The Needs Review tab is where those N/A rows land, with a search link so you can check them yourself.
SITEMAP_REBUILD = True forces a fresh download. Set it to False and repeat runs use the cached copy and finish instantly.

Let me walk through one OMSID from start to finish: 206856244, your Hollywood bed frame.

Step 1 — Read your Excel file

The script opens Book11.xlsx, finds the OMSID column, and reads the value.

Looking for: 206856244

Step 2 — Ask Home Depot where its sitemaps are

Downloads https://www.homedepot.com/robots.txt. Every website has this file. It contains a line reading:

Sitemap: https://www.homedepot.com/sitemap/main.xml

Now the script knows where the catalog list begins.

Step 3 — Follow the trail down to the product files

main.xml is a table of contents, not the data itself. It points to other files, one of which is P/PIPs.xml. That file is another table of contents, pointing to 94 files named PIP-0.xml through PIP-93.xml.

Those 94 are the real product lists.

main.xml → P/PIPs.xml → PIP-0.xml, PIP-1.xml, ... PIP-93.xml

Step 4 — Download a product file and look inside

Downloading PIP-0.xml gives roughly 45,000 lines that look like this:

xml
<url><loc>https://www.homedepot.com/p/Hollywood-Bed-Frame-Atlas-Lock-3150BSG-I/206856244</loc></url>
<url><loc>https://www.homedepot.com/p/GE-21-cu-ft-Bottom-Freezer/319240113</loc></url>
<url><loc>https://www.homedepot.com/p/AWNTECH-Chicago-Fixed-Awning/301100581</loc></url>

Every line is one product Home Depot has published.

Step 5 — Split each URL into name and number

Home Depot's URL format is always /p/{product-name}/{OMSID}. So the script chops the number off the end of each line:

URL	Number at the end
.../p/Hollywood-Bed-Frame-Atlas-Lock-3150BSG-I/206856244	206856244
.../p/GE-21-cu-ft-Bottom-Freezer/319240113	319240113
.../p/AWNTECH-Chicago-Fixed-Awning/301100581	301100581

That number is the OMSID.

Step 6 — Check for a match

The script compares each number against the one you asked about.

Is 206856244 == 206856244?   YES — match found

It saves the full URL from that line.

Step 7 — Write the answer

results.xlsx gets:

OMSID	Status	URL
206856244	LIVE	https://www.homedepot.com/p/Hollywood-Bed-Frame-Atlas-Lock-3150BSG-I/206856244

Done.

What happens when there is no match

Take 999991243, your made-up number. The script reads all 94 files, checks all 4.19 million numbers, and never finds it. So:

OMSID	Status	URL
999991243	NOT_FOUND	N/A

And that row also appears on the Needs Review tab, because "not in the list" is not quite the same as "not on the site" — as 343542969 proved.

The core idea in one sentence

Home Depot publishes a list of every product URL, each URL ends in the OMSID, so the script downloads the list and looks your numbers up in it — a very large VLOOKUP, where the lookup table is downloaded from Home Depot rather than pasted into a sheet.
