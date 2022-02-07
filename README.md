# Ticket to Ride: Europe Analysis
Analyzing the liklihood of individual routes being occupied by the end of the game (so you know which areas you will want to avoid, block or grab first.) 
 
### What is Ticket to Ride? 

The objective of Ticket to Ride is to earn the most points via building on pre-determined railroad tracks all over Europe. More specifically, players earn points by: 

* Completing “ticket” cards - each player aims to fulfill at least three “tickets,” which direct you from starting points on the board to different endpoints. 
* Building “routes” - each pathway between adjacent cities is called a “route.” Routes are of predetermined length on the board. The longer the route, the more points you earn. 
* Building the longest unbroken train route - players can earn 10 extra points at the end of the game for building the longest unbroken route. This is important because players are not only incentivized to get from point A to point B as efficiently as possible. Taking a more circuitous route could end up earning you more points, but at the risk of failing to fulfill you “ticket” cards. 
* A few other ways that are not as relevant to the analysis below.

## Background 

The original [Github Repo](https://github.com/Rob217/TicketToRideAnalysis.git) (by Rob Bettles) that inspired this project analyzes the United States version of the board. The central goal of Bettles' analysis is the same as the one found in this repo: to determine whether or not particular train routes have a higher probability of being occupied by the end of the game. If certain routes are more likely to be occupied, players might use this information to inform their game strategy.

After finding Bettles’ project, I aimed to build off of his Ticket to Ride: United States analysis by adding Europe to the mix, which is the board my family uses to play (shout out to my older brother Jacob for forcing us to play it until we liked it too). 

The "Europe" version of the game has a few new rules added to the mix, and I’m interested in seeing if they have any impact on the probability of certain routes being taken over the course of the game. Below, you’ll see a preliminary analysis of the game’s routes, tickets and challenges.

### The Data 

I began my analysis by creating three datasets: 
* tickets.csv - Contains the start and end destination for each ticket, along with the points won by completing the ticket, and whether the ticket is labeled as a "long" or "regular" route ("long" routes are worth more points than regular).
* routes.csv - Contains information about every single route on the board, including start and end destination, length of train route (measured in train cars), route color, and whether or not the route has additional challenges such as tunnels or ferries. 
* cities_xy.csv - Contains every city on the board, along with their position on an image of the board itself.

## Analysis

### Step 1: Ploting points on the game board
I took a different route here than Bettles. I uploaded an image of the board to [ImageMapGenerator](https://www.image-map.net/), clicked on each city and added their coordinates to cities_xy.csv. 

### Step 2: Find the shortest distance between cities
Here is where Bettles' code starts to come in big time. I followed his code almost exactly to apply Dijkstra's algorithm to my data. While following his code step by step, I learned: 
* Panda's defaultdict

### Step 3: Building the heat map 
Again, I followed Bettles' code almost exactly. A few changes were necessary based on how I structured my data (for example, mapping points on this version were a tad easier because of how I plotted the cites o the game board), but ultimately, my end result is very similar to Bettles', which was the goal.

During this process, I became more familiar with: 
* Uploading images via Pandas
* Creating colormaps via Matplotlib
* Debugging to identify issues with rendering and cleaning data. 

## Attempted Analysis 

### APIs 
At first, I wanted to solve for the same outcome as Bettles but with a different strategy. I wasn't familiar with Dijkstra's algorithm prior to this analysis, nor with applying it in Python. Instead, I hoped to run a similar test using primarily APIs. 

To do so, I used: 
* Google Cloud Geocode API - After updating each city's name to its English and modern spelling (since the game focuses on train travel, it is meant to be a tad antiquated. Therefore some cities now go by different names. St. Petersburg, Russia, for example, is "Petrograd" on the board), I ran this API to give me the longitute and latitude for each city on the board. 
* Here Routes API - Once I had the longitute and latitude, I could use the Here Routes API to get the shortest distance between each point on the board. This API does not have an option for trains, so this was run as if a player were moving along from their start and stop-point in each ticket via car. I captured a return of length of trip (seconds), distance of trip (meters) and a polyline marking the route, which I hoped to plot in QGIS. 
* Here Waypoints API - The Waypoints API allows you to build routes with stop-points in between your Beginning and end points. I hoped to incorporate this API by iterating through both tickets.csv and routes.csv. Cities A and B in tickets.csv would serve as my start and end points. I wanted to find a way to try each route or series of routes in routes.csv to get me the shortest path to each ticket's start and end point. I would append each series of routes per ticket to a list and record the length of each list.

My experimentation with APIs, while helpful in mapping actual points and demonstrating pathways to fulfill each ticket card, could not lead me to analyze the game beyond what we already know: that some ticket cards, even in a best-case/shortest-route scenario, would require more routes to fulfill than others. Regardless, experimenting with APIs taught me: 
* How to read API documentation when completely unfamiliar with the API itself, and especially how to compare APIs that claim to do the same thing (such as geocoding)
* How to geocode with little to no city information, or outdated city information
* Using map-based APIs while optimizing for time or distance 
* Iterating/looping your data through an API so as to capture multiple results at once 
* Appending those results to a dictionary or list, ulitmately creating a new dataframe and CSV from the information  

### Network Analysis

Another possible path for this analysis was through [NetworkX](https://networkx.org/), which allows users to build network graphs. In [Nodes and Edges](https://github.com/jessieblaeser/Federal-Court-Nominees-Scrape-and-Analysis/blob/233e10c37e2430277d0500f6941cc6e452ab3a04/Creating%20pro%20bono%20categories%20and%20mapping.ipynb), you will see a completed Network analysis of the game board, including the most efficient routes for each ticket. 

Much more can be done with this type of analysis (for game players, simplying knowing the quickest route for every single ticket card), and I hope to apply it to Bettles' strategy to streamline the same conclusions. 

While I did not ultimately include the NetworkX analysis in my conclusions, learning this python package can help in a number of journalistic scenarios, including: 
* Visualizing networks, particularly networks of power, flow of money or connections between people
* Visualizing who or what has the most connections in a given network or who acts as the most crucial bridge in a network 
* Enabling readers to explore networks themselves

## Conclusions 

Bettles' initial conclusions for the the USA board hold true for the Europe board: Routes on the perifery are less likely to be occupied by the end of the game simply because they are less often necessary for any of the 46 ticket cards. 

However, this conclusion can only be drawn with the understanding that every player is aiming for efficiency. There are two key rule-changes in Ticke to Ride: Europe that could change this mindset: 
* Ticket to Ride: Europe introduced "long" ticket cards, which are worth more (sometimes double) points than their "regular" counterparts 
* The player with the longest unbroken train route at the end of the game earns an extra 10 points. 

Therefore, players are incentivezed not only to fulfill their ticket cards, but — potentially — to do so in the most *inefficient* way possible. With this in mind, the lightest routes rendered in the following maps become much more desireble. 