Note: This repo is largely a snapshop record of bring Wikidata
information in line with Wikipedia, rather than code specifically
deisgned to be reused.

The code and queries etc here are unlikely to be updated as my process
evolves. Later repos will likely have progressively different approaches
and more elaborate tooling, as my habit is to try to improve at least
one part of the process each time around.

---------

Step 1: Tracking page
=====================

I set up a Listeria page for the term. Initial version is at:
https://www.wikidata.org/w/index.php?title=Wikidata:WikiProject_every_politician/Norway/data/Storting/2005-2009&oldid=1240842662

Step 2: Set up the metadata
===========================

Edited [add_P39.js script](add_P39.js) to configure the P39 data, and
source URL.

Step 3: Get local copy of Wikidata information
==============================================

    wd ee --dry add_P39.js | jq -r '"\(.claims.P39.value) \(.claims.P39.qualifiers.P2937)"' | 
      xargs wd sparql members.js | tee wikidata.json

Step 4: Scrape
==============

Comparison/source = https://no.wikipedia.org/wiki/Liste_over_stortingsrepresentanter_2005%E2%80%932009

    wb ee --dry add_P39.js  | jq -r '.claims.P39.references.P4656' |
      xargs bundle exec ruby scraper.rb | tee wikipedia.csv

The constituency info here is all wrong, as Wikidata has distinct items
for the electoral district vs the administrative district, but we can
just ignore those.

Step 6: Add missing 'elected in' qualifiers
===========================================

    bundle exec ruby new-qualifiers.rb wikipedia.csv wikidata.json 2>/dev/null | fgrep P2715 |
      wd aq --batch --summary "Add elected in qualifiers, from $(wb ee --dry add_P39.js | jq -r '.claims.P39.references.P4656')"

168 additions made as https://tools.wmflabs.org/editgroups/b/wikibase-cli/f48bec5092243/

Step 7: Refresh the Tracking Page
==================================

New version at 
https://www.wikidata.org/w/index.php?title=Wikidata:WikiProject_every_politician/Norway/data/Storting/2005-2009&oldid=1240887117

