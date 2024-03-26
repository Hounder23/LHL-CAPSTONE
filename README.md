# FFXIV Consumable Market Analysis - Final-Project-CAPSTONE

## Project/Goals

In this project I'll be exploring the In-Game market of Final Fantasy XIV. Specifically My plan is to use this collected data to determine what day of the week the consumable market is most stable so that I can list my own items in that price range and maximize my profits on the unstable days.

## Process

### Data Collection / API research

The 1st step in this project was to find some public APIs to get the market board data. To do this I searched through some player/community run websites that have some market tracking capabilities  and found that the Universalis project has an API available for people to send requests to to retrieve live and historical market data for specific items (<https://docs.universalis.app/>). Very conveniently the Universalis API Doc points to some other APIs to map item IDs to their item names. The API I'll be using to map these items will be XIVAPI (<https://xivapi.com/docs>) since this one api has another endpoint I can use to map world IDs from the market API to their appropriate names.

- universalis = market data per itemID per worldID
- XIVAPI = Item and World names by ID

So after using the XIVAPI to get a list of all the in game consumables as a json response with the ItemKind filter, I realized that we never learned about json responses that use pagination to seperate their response into pages like a google search. After learning to scan through the pages i was left with a pandas DF with 7 columns. I ended up using only 2 of the columns, ID and Name. XIVAPI also has some endpoints that return server data, Data Centers and World data, esentially with these 2 endpoints you can decipher which Worlds (playable servers) are connected to which DataCenters(Groups or worlds by region) using the World.id property. And this was all the data i gathered from XIVAPI.

Next was to connect to Universalis. The first endpoint i connected to was the marketable endpoint which returned to me the list of item IDs in game that are allowed to be placed on the marketboard. This made life real easy and i merged the consumabels data frame from XIVAPI with the marketable data frame on their id columns to get a reduced list of only items that can be marketed reducing the total size by ~ 350 items. After some testing with the market history endpoint of the API it was time to itterate over the marketable consumables list requesting the market history from the DataCenter i play on and how much history on each item i wanted. This block of code was behaving exactly as i hoped and went over the whole list of marketable consumables in game and returned a table. But i learned this approach was not correct, I did not take into account that each item would return a nested table in the entries column which i did not normalize for every item i requested since that would mean having thousands of files with thousands of transactions, and that was outside the scope of this project. so instead i created the narrow collection notebook to only gather market data on the 5 items i wanted.


### EDA / Model building

Since i was working in python already, i used python to do my EDA as well. Most of my EDA was done in the ModelBuilding notebook since i didn't want to have to run another instance of python. Here i manufactuered some more columns of data using existing columns in the hopes of improving my models score. I made a list of things i needed to do to make my models. This involved removing any NULL or duplicate entries as well as removing any outliers (zscore > 3). Then i went about creating different kinds of plots for each of the 5 markets to find some interesting features of the data.
Now it was time to actually build some models at first i used only 3 features i thought mattered the most but all of my models had an R^2 less than 0.7 so i was not too confident about the prospects of the results for my project. So in an attempt to improve the R^2 i learned how to build the models in sklearn hoping for a better model but still my scores were awfull. So in an attempt to improve the scores i went back and included a bunch of time associated variables to improve the scores which did end up working but not enough for me to be confident but these are the results i stuck with. I then used the statsmodels predict function to try and predict the price per unit on any given day of the week and the results were less than satisifying.

## Results

Final results showed me that the day by day forecasting of pricePerUnit is largely not informative for maximizing profits since the markets moves so fast constantly. I would be better off choosing any day of the week and posting my own items at around the mean market price of that item.

## Challenges

- json nomralization
- feature creation
- API multithreading requests

## Future Goals

If i had more time i would like to do the entire consumables market but I would have to find a way to improve my understanding of splitting API calls into multiple cpu threaded tasks.


