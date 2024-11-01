Web url: momentous-secretary.surge.sh
This webpage visualizes the 2015 New York City Tree Census. (https://data.cityofnewyork.us/Environment/2015-Street-Tree-Census-Tree-Data/uvpi-gqnh/about_data). The dataset records every individual tree, so in order to put the data into a choropleth map, the data are aggregated over census tracts.

Data Explanation:
1. The choropleth map contains information about the number of alive trees in a tract area
2. After clicking a specific tract on the map, the table underneath the map will display the detailed data for that tract.
3. There are four histograms, displaying the number of dead trees, the average diameter of the tree, the health status and the presenece of noticeable problems (paving stones in tree bed, lighting installed on the tree, etc...). The user can use d3 brush functions to filter the data on the histograms.
4. Some tracts are not displayed on the map due to missing data from the dataset.