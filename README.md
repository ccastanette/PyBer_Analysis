# PyBer_Analysis

### Resources
  -Data Source: [city_data.csv](https://github.com/ccastanette/PyBer_Analysis/blob/main/Resources/city_data.csv) and [ride_data.csv](https://github.com/ccastanette/PyBer_Analysis/blob/main/Resources/ride_data.csv)
  
  -Software: Jupyter Notebook
  -Dependancies: pandas, matplotlib.pyplot, & matplotlib.dates

### Usage
  - add both of the data sources to a folder called resources within our project location, then use use the following to read them into jupyter notebook
  ```
  city_data_to_load = "Resources/city_data.csv"
  ride_data_to_load = "Resources/ride_data.csv"
  ```
## Project Overview

The purpose of this project is to create a summary data frame that would allow us to quickly see the total and averages based on city type as well as create line chart of the total fares by city type per week from the dates Jan-01-2019 to Apr-29-2019.

The first step was to combine both of the resource files into a single data frame using `pyber_data_df = pd.merge(ride_data_df, city_data_df, how="left", on=["city", "city"])`. Then we had to pull out assorted values that would be used to create the graph. First we pulled out the total rides for each city type using this code:  
`total_rides_by_type = pyber_data_df.groupby(["type"]).count()["fare"]` 
Then we modified this code to pull out the rest of the data we needed. This included:
  - total drivers by city type 
  - sum of fares by city type
  - average fares by city type
  - average fare per driver by city type
  
  This information was then added to the dataframe `pyber_summary_df` and formatted.

The next step was to gather and format the info that we would need to create our line chart. First we used the groupby function to group the rides by city type and date:
```
date_fare_sum_df = pd.DataFrame(pyber_data_df.groupby(["type", "date"]).sum()["fare"])
date_fare_sum_df = date_fare_sum_df.reset_index()
```
Adding the index back in will allow us to use the pivot in the next step that is using the date as an index and making the columns fares based on the city types. 
```
pivot_df = date_fare_sum_df.pivot(index='date', columns='type', values='fare')
```
Next we used `.index ` to pull out only the values from Jan-01-2019 to Apr-29-2019.
After that we were able to use `.resample` to separate them by the week and get the sum of all fares for that week.

The final step was to create the line plot to diplay our results using the object oriented interface method:
```
# Import the style from Matplotlib.
from matplotlib import style
# Use the graph style fivethirtyeight.
style.use('fivethirtyeight')

x_labels = ["Jan 2019", "Feb", "Mar", "Apr"]
fig, ax = plt.subplots(figsize = (25, 6))
ax.set_title('Total Fare by City Type')
ax.set_ylabel('Fare($USD)')
ax.plot(week_sum_fares_df)
months = mdates.MonthLocator()  
years = mdates.YearLocator()
monthsFmt = mdates.DateFormatter('%b')
yearsFmt = mdates.DateFormatter('%Y')
ax.xaxis.set_major_locator(years)
ax.xaxis.set_major_formatter(yearsFmt)
ax.xaxis.set_minor_locator(months)
ax.xaxis.set_minor_formatter(monthsFmt)
ax.legend(['Rural', 'Suburban', 'Urban'])
ax.grid()

plt.savefig("analysis/Fig8.png")
plt.show()
```


## Results

The results of our work were 2 visualizations that will allow us to make important business decisions. Here they are:
![Summary Data Frame]()

![Line Chare Fare by City](https://github.com/ccastanette/PyBer_Analysis/blob/main/analysis/Fig8.png)

## Summary
Looking at our visualizations we can see that the total rides decrease significantly from urban to rural cities which is to be expected, but since the total number of drivers in the urban cities is so much larger than in rural cities the average fare per driver is larger in rural areas. The average fare per ride is also larger in the rural cities. The total fares is still larger in the urban cities.

Due to the low fare per driver average I would recommend decreasing the number of drivers in the urban cities to drive that up. I would also recommend doing something to increase the average fare per ride since it is decreasing in urban cities, possibly adding a per ride fee to give it a base fare amount. If you look at the total fare by city typer per week there seems to be a spike in February possibly due to Valentine's day, so I would recommend doing more marketing around holidays to increase that spike.
