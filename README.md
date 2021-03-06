# Is My District Gerrymandered?

[![Donate](https://img.shields.io/badge/Donate-PayPal-green.svg)](https://www.paypal.com/cgi-bin/webscr?cmd=_s-xclick&hosted_button_id=TYZQFQ46HNQE6)

This is a project that estimates how likely a congressional district is gerrymandered based on its geographical compactness, voter wastage, and redistricting control. It was last updated for the 115th US Congressional districts (2016 election).

Check it out at [ismydistrictgerrymandered.us](http://ismydistrictgerrymandered.us).

![Screenshot of the app](misc/demo.png)

### How does it work?

This project determines whether a district is gerrymandered based on 3 factors:

1. **Geographical Compactness**: Basically, how compact is the district? It is more likely that a district is gerrymandered if it has an odd shape and a lot of small tendrils than if it is more regular. While this isn't necessarily a perfect gauge of gerrymandering, it can be a decent proxy. Geographical compactness is calculated using the *Polsby-Popper method*: dividing the area of the district by the area of a circle with the same perimeter. I used QGIS to calculate compactness.

    * What is an "acceptable" level of compactness? This project determines that a district is compact if its Polsby-Popper score is higher than 0.287. This is the 50th percentile of compactness for a sample of present and past congressional districts.


2. **Voter Wastage**: Gerrymandering can essentially be done in two ways: "Packing", which crams all the voters of one party into a few districts to eliminate a majority, or "Cracking", which spreads the voters of one party into a lot of districts so they don't have a majority in any district. By calculating the number of wasted votes (the number of votes above the threshold to win, and all the votes of the losing party) for each party, we can determine if there is are more votes wasted for a specific party, a good indicator of partisan gerrymandering. Specifically, we measure voter wastage using the "efficiency gap", which you can read more about [here](https://www.brennancenter.org/sites/default/files/legal-work/How_the_Efficiency_Gap_Standard_Works.pdf).

    * An acceptable amount of voter wastage is less than a 2 seat efficiency gap
    * The efficiency gap is calculated with only Republican and Democrat candidates, and it is assumed the opponent would have won 25% of the vote in uncontested districts.


3. **Redistricting Control**: How are district lines drawn by state? In most states, the state legislatures, or other elected officials primarily draw district lines. This can potentially be biased, as members of one party will be drawing the district lines which can influence the election of members of their own party. Some state legislatures split redistricting control between both parties, mitigating some amount of partisan bias. Even better, a few states use independent, bipartisan commissions to draw district lines. Thus, how likely a redistricting process is to be biased is factored into our assesment.  

To specifically determine whether a district is gerrymandered, a "gerrymander_score" is calculated internally in `data/generate_csv.js`. If this score is greater than 50, the district is gerrymandered. If a state has less than or equal to 6 districts, it is unlikely to be gerrymandered, and those districts' gerrymander scores are set to 0. Otherwise, 75% of this score is from the geographical compactness of the district and 25% of the score is from the efficiency gap of the state the district is in. Though both equally important, the efficiency gap score is slightly de-emphasized because it is a state-level calculation, whereas compactness applies to individual districts. Additionally, redistricting control can only help the score, meaning that the gerrymander score is reduced if the state redistricts using a bipartisan independent commission. Having a biased redistricting process does not necessarily mean a gerrymandered district. However, having a bipartisan, independent redistricting process significantly reduces the chance of a gerrymandered district.

### Table of Contents

*   index.js: the main file which hosts the webserver.
*   views/: all of the webpages stored here.
*   public/: all of the frontend code is stored here.
*   data/:
  * generate_csv.js: This file generates the master.csv, which contains all the data, for all the districts.
  * generate_efficiency.js: This file generates efficiency.csv, which contains information on voter wastage.
  * generate_geographical_compactness.js: This file generates compactness.csv, which contains the Polsby-Popper score for each district.

### Disclaimer

This project was created solely out of curiosity: I had heard a lot about partisan gerrymandering, and wanted to know if my district could be gerrymandered. It was not created out of political reasons: The project does not support any one party, but just displays the data collected. In the spirit of transparency, all of the source code used for this project is in this repository, and all of the datasets used are listed below.

### Issues?

Please submit an issue, or contact me at jshen@andover.edu if you believe you have found an error with this tool.

## Credits

This project was written by Jeffrey Shen, a current student at Phillips Academy. I received some assistance from Varun Roy and Bill Qin.

### Datasets used

*   **[github.com/unitedstates/districts](https://github.com/unitedstates/districts)**: Used to get the geojson files of each district.
* **[census.gov/geo/maps-data/data/cbf/cbf_cds.html](https://www.census.gov/geo/maps-data/data/cbf/cbf_cds.html)**: Used to get shapefiles of congressional districts, which were used to calculate geographical compactness.
*   **[history.house.gov](http://history.house.gov/Institution/Election-Statistics/Election-Statistics/)**: Used for house election data.
*   **[redistricting.lls.edu/who.php](http://redistricting.lls.edu/who.php)**: Used for information on redistricting by state.

### Built With

*   **QGIS**: Used to calculate geographical compactness.
*   **Leaflet** and **Mapbox**: Used to display a map of each congressional district.
*   **Geocoder**: Used for converting an address into longitude and latitude, so it can later be converted into a congressional district.

### General Sources
These were all the articles, books, etc. that I read to better help me understand gerrymandering:

*   [“How the New Math of Gerrymandering Works.”](https://www.nytimes.com/interactive/2017/10/03/upshot/how-the-new-math-of-gerrymandering-works-supreme-court.html)
*   [“Compactness in Redistricting.”](http://www.theseventhstate.com/?tag=polsby-popper)
*   [“We have a standard for judging partisan gerrymandering. The Supreme Court should use it.”](https://www.washingtonpost.com/news/monkey-cage/wp/2017/02/02/we-have-a-standard-for-judging-partisan-gerrymandering-the-supreme-court-should-use-it/?utm_term=.6d7324399dee)
*   [“How the Efficiency Gap Works.”](https://www.brennancenter.org/sites/default/files/legal-work/How_the_Efficiency_Gap_Standard_Works.pdf)
*   [Partisan Gerrymandering and the Efficiency Gap.”](https://papers.ssrn.com/sol3/papers.cfm?abstract_id=2457468)
*   [A Two Hundred-Year Statistical History of the Gerrymander](https://pdfs.semanticscholar.org/e6b9/c6767f87de38e549242e84b60db685118f8c.pdf)
*   [Partisan Gerrymandering and the Efficiency Gap](https://chicagounbound.uchicago.edu/cgi/viewcontent.cgi?referer=https://www.google.com/&httpsredir=1&article=1946&context=public_law_and_legal_theory)

### License

This project is licensed under the CC0 License -- See the LICENSE file for more info.
