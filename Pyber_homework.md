
# Pyber Analydsis

### 1. From the data , we can clearly see that URBAN areas are the leading in terms of rides and population.
### 2. From the data it is clear that there is a much larger number of drivers in urban areas than in suburban or rural, the average fare prices are still lower.
### 3. Rides prices are more competitive in Urban areas than other areas. In fact, we can colclude that lower the rides (drivers) in areas, higher is the ride cost.


```python
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt

rides = "ride_data.csv"
city = "city_data.csv"
```


```python
df_city = pd.read_csv(city)
df_ride = pd.read_csv(rides)
```


```python
# df_city = df_city.drop_duplicates('city')
# cities = df_city['city']
# cities

# Mergign city and rides data
df_Pyber = pd.merge(df_city,df_ride,on = 'city')
df_merged.head()
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>city</th>
      <th>driver_count</th>
      <th>type</th>
      <th>date</th>
      <th>fare</th>
      <th>ride_id</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Kelseyland</td>
      <td>63</td>
      <td>Urban</td>
      <td>2016-08-19 04:27:52</td>
      <td>5.51</td>
      <td>6246006544795</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Kelseyland</td>
      <td>63</td>
      <td>Urban</td>
      <td>2016-04-17 06:59:50</td>
      <td>5.54</td>
      <td>7466473222333</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Kelseyland</td>
      <td>63</td>
      <td>Urban</td>
      <td>2016-05-04 15:06:07</td>
      <td>30.54</td>
      <td>2140501382736</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Kelseyland</td>
      <td>63</td>
      <td>Urban</td>
      <td>2016-01-25 20:44:56</td>
      <td>12.08</td>
      <td>1896987891309</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Kelseyland</td>
      <td>63</td>
      <td>Urban</td>
      <td>2016-08-09 18:19:47</td>
      <td>17.91</td>
      <td>8784212854829</td>
    </tr>
  </tbody>
</table>
</div>




```python
urban = df_Pyber.loc[df_Pyber['type']=='Urban']
rural = df_Pyber.loc[df_Pyber['type']=='Rural']
suburban = df_Pyber.loc[df_Pyber['type']=='Suburban']

# # Total Revenue Per City
urban_total = urban.groupby("city").sum()['fare']
suburban_total = suburban.groupby("city").sum()['fare']
rural_total = rural.groupby("city").sum()['fare']

# # Total Number of Rides Per City
urban_rides = urban.groupby("city").count()['ride_id']
suburban_rides = suburban.groupby("city").count()['ride_id']
rural_rides = rural.groupby("city").count()['ride_id']

# # Average Fare ($) Per City
urban_avg = round(urban_total/urban_rides,2)
suburban_avg = round(suburban_total/suburban_rides,2)
rural_avg = round(rural_total/rural_rides,2)

# # Total Number of Drivers Per City
urban_drivers = urban.drop_duplicates('city').set_index('city')['driver_count']
suburban_drivers = suburban.drop_duplicates('city').set_index('city')['driver_count']
rural_drivers = rural.drop_duplicates('city').set_index('city')['driver_count']

plt.scatter(urban_rides, urban_avg, marker='o', facecolors='lightcoral', edgecolors='black', s=urban_drivers*10, alpha=0.75, label='Urban')
plt.scatter(suburban_rides, suburban_avg, marker='o', facecolors='lightblue', edgecolors='black', s=suburban_drivers*10, alpha=0.75, label='Suburban')
plt.scatter(rural_rides, rural_avg, marker='o', facecolors='yellow', edgecolors='black', s=rural_drivers*10, alpha=0.75, label='Rural')

plt.title("Pyber Ride Sharing Data (2016)")
plt.xlabel("Total Number of Rides (Per City)")
plt.xlim(0,36)
plt.ylabel("Average FAre ($)")
plt.ylim(15,51)
lgnd = plt.legend(scatterpoints=1)
lgnd.legendHandles[0]._sizes = [50]
lgnd.legendHandles[1]._sizes = [50]
lgnd.legendHandles[2]._sizes = [50]

plt.annotate(s='Note:\nCircle size correlates with driver count per city', xy=(0,15), xytext=(37,45))

plt.show()
```


![png](output_4_0.png)



```python
Pyber_total_fares = (df_Pyber.groupby("type")["fare"].sum() / df_Pyber["fare"].sum()*100).round(2)
df_Pyber_total_fares = pd.DataFrame(Pyber_total_fares).reset_index()
df_Pyber_total_fares
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>type</th>
      <th>fare</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Rural</td>
      <td>6.58</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Suburban</td>
      <td>31.45</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Urban</td>
      <td>61.97</td>
    </tr>
  </tbody>
</table>
</div>




```python
members = df_Pyber_total_fares['fare'].tolist()
types = df_Pyber_total_fares['type'].tolist()

colors = ["yellow", "lightskyblue","lightcoral"]
plt.title("% of Total Fares by City Type")
explode = (0,0,0.075)
plt.pie(members, labels=types, explode=explode, colors=colors,
        autopct="%1.1f%%", shadow=True, startangle=120)
plt.axis("equal")
plt.show()
```


![png](output_6_0.png)



```python
Pyber_total_rides = (df_Pyber.groupby("type")["ride_id"].count() / df_Pyber["ride_id"].count()*100).round(2)
df_Pyber_total_rides = pd.DataFrame(Pyber_total_rides).reset_index()
df_Pyber_total_rides
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>type</th>
      <th>ride_id</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Rural</td>
      <td>5.19</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Suburban</td>
      <td>27.30</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Urban</td>
      <td>67.51</td>
    </tr>
  </tbody>
</table>
</div>




```python
members = df_Pyber_total_rides['ride_id'].tolist()
types = df_Pyber_total_fares['type'].tolist()

colors = ["yellow", "lightskyblue","lightcoral"]
plt.title("% of Total Rides by City Type")
explode = (0,0,0.075)
plt.pie(members, labels=types, explode=explode, colors=colors,
        autopct="%1.1f%%", shadow=True, startangle=120)
plt.axis("equal")
plt.show()
```


![png](output_8_0.png)



```python
Pyber_total_drivers = (df_city.groupby("type")["driver_count"].sum() / df_city["driver_count"].sum()*100).round(2)
df_Pyber_total_drivers = pd.DataFrame(Pyber_total_drivers).reset_index()
df_Pyber_total_drivers

```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>type</th>
      <th>driver_count</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Rural</td>
      <td>3.11</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Suburban</td>
      <td>19.05</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Urban</td>
      <td>77.84</td>
    </tr>
  </tbody>
</table>
</div>




```python
members = df_Pyber_total_drivers['driver_count'].tolist()
types = df_Pyber_total_fares['type'].tolist()

colors = ["yellow", "lightskyblue","lightcoral"]
plt.title("% of Total Drivers by City Type")
explode = (0,0,0.075)
plt.pie(members, labels=types, explode=explode, colors=colors,
        autopct="%1.1f%%", shadow=True, startangle=120)
plt.axis("equal")
plt.show()
```


![png](output_10_0.png)

