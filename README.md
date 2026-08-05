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
