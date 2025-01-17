# Welcome To The Notebook
In this project, we are going to analyze Geo-spatial data using python and folium.



```python
%pip install matplotlib
```

## TASK 1
Importing modules


```python
import pandas as pd 
import matplotlib as plot
import datetime , calendar
import folium
```

Importing our data


```python
data = pd.read_csv('dataset.csv')
data.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>TRIP_ID</th>
      <th>CALL_TYPE</th>
      <th>TIMESTAMP</th>
      <th>TRIP_PATH</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1372636858620000589</td>
      <td>C</td>
      <td>1372636858</td>
      <td>[(41.141412, -8.618643), (41.141376, -8.618499...</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1372637303620000596</td>
      <td>B</td>
      <td>1372637303</td>
      <td>[(41.159826, -8.639847), (41.159871, -8.640351...</td>
    </tr>
    <tr>
      <th>2</th>
      <td>1372636951620000320</td>
      <td>C</td>
      <td>1372636951</td>
      <td>[(41.140359, -8.612964), (41.14035, -8.613378)...</td>
    </tr>
    <tr>
      <th>3</th>
      <td>1372636854620000520</td>
      <td>C</td>
      <td>1372636854</td>
      <td>[(41.151951, -8.574678), (41.151942, -8.574705...</td>
    </tr>
    <tr>
      <th>4</th>
      <td>1372637091620000337</td>
      <td>C</td>
      <td>1372637091</td>
      <td>[(41.18049, -8.645994), (41.180517, -8.645949)...</td>
    </tr>
  </tbody>
</table>
</div>



### Let's talk about the different columns
---
- CALL_TYPE: The way used to demand taxi service
    - 'A' : if this trip was dispatched from the central
    - 'B' : if this trip was demanded directly to a taxi driver on a specific stand 
    - 'C' : other (i.e. a trip demanded on a random street)

- TIMESTAMP: When the trip starts 

- TRIP_PATH: Contains a list of coordinates
    - The first element is the coordinates of the trip's starting point
    - The last element is the coordinates of the trip's end point

## TASK 2
### Data Preprocessing 
Let's extract starting and ending points of each trip <br>

    - Creating two columns START_LOC and END_LOC


```python
data.TRIP_PATH = data.TRIP_PATH.apply(eval)
data.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>TRIP_ID</th>
      <th>CALL_TYPE</th>
      <th>TIMESTAMP</th>
      <th>TRIP_PATH</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1372636858620000589</td>
      <td>C</td>
      <td>1372636858</td>
      <td>[(41.141412, -8.618643), (41.141376, -8.618499...</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1372637303620000596</td>
      <td>B</td>
      <td>1372637303</td>
      <td>[(41.159826, -8.639847), (41.159871, -8.640351...</td>
    </tr>
    <tr>
      <th>2</th>
      <td>1372636951620000320</td>
      <td>C</td>
      <td>1372636951</td>
      <td>[(41.140359, -8.612964), (41.14035, -8.613378)...</td>
    </tr>
    <tr>
      <th>3</th>
      <td>1372636854620000520</td>
      <td>C</td>
      <td>1372636854</td>
      <td>[(41.151951, -8.574678), (41.151942, -8.574705...</td>
    </tr>
    <tr>
      <th>4</th>
      <td>1372637091620000337</td>
      <td>C</td>
      <td>1372637091</td>
      <td>[(41.18049, -8.645994), (41.180517, -8.645949)...</td>
    </tr>
  </tbody>
</table>
</div>




```python
extraxt_starting_point = lambda list_ : list_[0]
extract_ending_point = lambda list_ : list_[-1]

data['START_LOC'] = data.TRIP_PATH.apply(extraxt_starting_point)
data['END_LOC'] = data.TRIP_PATH.apply(extract_ending_point)

data.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>TRIP_ID</th>
      <th>CALL_TYPE</th>
      <th>TIMESTAMP</th>
      <th>TRIP_PATH</th>
      <th>START_LOC</th>
      <th>END_LOC</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1372636858620000589</td>
      <td>C</td>
      <td>1372636858</td>
      <td>[(41.141412, -8.618643), (41.141376, -8.618499...</td>
      <td>(41.141412, -8.618643)</td>
      <td>(41.154489, -8.630838)</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1372637303620000596</td>
      <td>B</td>
      <td>1372637303</td>
      <td>[(41.159826, -8.639847), (41.159871, -8.640351...</td>
      <td>(41.159826, -8.639847)</td>
      <td>(41.170671, -8.66574)</td>
    </tr>
    <tr>
      <th>2</th>
      <td>1372636951620000320</td>
      <td>C</td>
      <td>1372636951</td>
      <td>[(41.140359, -8.612964), (41.14035, -8.613378)...</td>
      <td>(41.140359, -8.612964)</td>
      <td>(41.14053, -8.61597)</td>
    </tr>
    <tr>
      <th>3</th>
      <td>1372636854620000520</td>
      <td>C</td>
      <td>1372636854</td>
      <td>[(41.151951, -8.574678), (41.151942, -8.574705...</td>
      <td>(41.151951, -8.574678)</td>
      <td>(41.142915, -8.607996)</td>
    </tr>
    <tr>
      <th>4</th>
      <td>1372637091620000337</td>
      <td>C</td>
      <td>1372637091</td>
      <td>[(41.18049, -8.645994), (41.180517, -8.645949)...</td>
      <td>(41.18049, -8.645994)</td>
      <td>(41.178087, -8.687268)</td>
    </tr>
  </tbody>
</table>
</div>



Remapping CALL_TYPE column values to the proper values


```python
CALL_TYPES = {
              "A":"CENTRAL_BASED",
              "B":"STAND_BASED",
              "C":"OTHER"
             }

data.CALL_TYPE = data.CALL_TYPE.map(CALL_TYPES)
data.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>TRIP_ID</th>
      <th>CALL_TYPE</th>
      <th>TIMESTAMP</th>
      <th>TRIP_PATH</th>
      <th>START_LOC</th>
      <th>END_LOC</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1372636858620000589</td>
      <td>OTHER</td>
      <td>1372636858</td>
      <td>[(41.141412, -8.618643), (41.141376, -8.618499...</td>
      <td>(41.141412, -8.618643)</td>
      <td>(41.154489, -8.630838)</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1372637303620000596</td>
      <td>STAND_BASED</td>
      <td>1372637303</td>
      <td>[(41.159826, -8.639847), (41.159871, -8.640351...</td>
      <td>(41.159826, -8.639847)</td>
      <td>(41.170671, -8.66574)</td>
    </tr>
    <tr>
      <th>2</th>
      <td>1372636951620000320</td>
      <td>OTHER</td>
      <td>1372636951</td>
      <td>[(41.140359, -8.612964), (41.14035, -8.613378)...</td>
      <td>(41.140359, -8.612964)</td>
      <td>(41.14053, -8.61597)</td>
    </tr>
    <tr>
      <th>3</th>
      <td>1372636854620000520</td>
      <td>OTHER</td>
      <td>1372636854</td>
      <td>[(41.151951, -8.574678), (41.151942, -8.574705...</td>
      <td>(41.151951, -8.574678)</td>
      <td>(41.142915, -8.607996)</td>
    </tr>
    <tr>
      <th>4</th>
      <td>1372637091620000337</td>
      <td>OTHER</td>
      <td>1372637091</td>
      <td>[(41.18049, -8.645994), (41.180517, -8.645949)...</td>
      <td>(41.18049, -8.645994)</td>
      <td>(41.178087, -8.687268)</td>
    </tr>
  </tbody>
</table>
</div>



 

### Data Analysis 
Let's answer to some analytical questions using our data 
 
 

### Question 1 - What are the most common ways to get a taxi in Porto?


```python
q1_data = data.CALL_TYPE.value_counts(sort=False)
q1_data.plot.bar(grid=True)
```




    <Axes: xlabel='CALL_TYPE'>




    
![png](output_15_1.png)
    


# TASK 3

### Question 2 - Which regions of the city are the best pick up points?
    To answer to this question we can use a dotmap.
    A dotmap is a scatter plot which is put on a map to represent a geographic data distribution.
    And then by looking at the different clusters of the points on the map we can get the answer.

Let's load our map 


```python
data.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>TRIP_ID</th>
      <th>CALL_TYPE</th>
      <th>TIMESTAMP</th>
      <th>TRIP_PATH</th>
      <th>START_LOC</th>
      <th>END_LOC</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1372636858620000589</td>
      <td>OTHER</td>
      <td>1372636858</td>
      <td>[(41.141412, -8.618643), (41.141376, -8.618499...</td>
      <td>(41.141412, -8.618643)</td>
      <td>(41.154489, -8.630838)</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1372637303620000596</td>
      <td>STAND_BASED</td>
      <td>1372637303</td>
      <td>[(41.159826, -8.639847), (41.159871, -8.640351...</td>
      <td>(41.159826, -8.639847)</td>
      <td>(41.170671, -8.66574)</td>
    </tr>
    <tr>
      <th>2</th>
      <td>1372636951620000320</td>
      <td>OTHER</td>
      <td>1372636951</td>
      <td>[(41.140359, -8.612964), (41.14035, -8.613378)...</td>
      <td>(41.140359, -8.612964)</td>
      <td>(41.14053, -8.61597)</td>
    </tr>
    <tr>
      <th>3</th>
      <td>1372636854620000520</td>
      <td>OTHER</td>
      <td>1372636854</td>
      <td>[(41.151951, -8.574678), (41.151942, -8.574705...</td>
      <td>(41.151951, -8.574678)</td>
      <td>(41.142915, -8.607996)</td>
    </tr>
    <tr>
      <th>4</th>
      <td>1372637091620000337</td>
      <td>OTHER</td>
      <td>1372637091</td>
      <td>[(41.18049, -8.645994), (41.180517, -8.645949)...</td>
      <td>(41.18049, -8.645994)</td>
      <td>(41.178087, -8.687268)</td>
    </tr>
  </tbody>
</table>
</div>




```python
Map = folium.Map(location=[41.15,-8.62], zoom_start=14)

for point in data.START_LOC:
    folium.CircleMarker(location = point, color = 'red', radius = 1, weight = 2).add_to(Map)

Map
```

![png](map01.png)



### Question 3 - Which regions of the city are the best pick up points for stand-based trips?
    To answer to this question we create a dotmap with different Marker clusters, each cluster corresponds to a specific
    CALL_TYPE.

Let's check our data again 


```python
data.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>TRIP_ID</th>
      <th>CALL_TYPE</th>
      <th>TIMESTAMP</th>
      <th>TRIP_PATH</th>
      <th>START_LOC</th>
      <th>END_LOC</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1372636858620000589</td>
      <td>OTHER</td>
      <td>1372636858</td>
      <td>[(41.141412, -8.618643), (41.141376, -8.618499...</td>
      <td>(41.141412, -8.618643)</td>
      <td>(41.154489, -8.630838)</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1372637303620000596</td>
      <td>STAND_BASED</td>
      <td>1372637303</td>
      <td>[(41.159826, -8.639847), (41.159871, -8.640351...</td>
      <td>(41.159826, -8.639847)</td>
      <td>(41.170671, -8.66574)</td>
    </tr>
    <tr>
      <th>2</th>
      <td>1372636951620000320</td>
      <td>OTHER</td>
      <td>1372636951</td>
      <td>[(41.140359, -8.612964), (41.14035, -8.613378)...</td>
      <td>(41.140359, -8.612964)</td>
      <td>(41.14053, -8.61597)</td>
    </tr>
    <tr>
      <th>3</th>
      <td>1372636854620000520</td>
      <td>OTHER</td>
      <td>1372636854</td>
      <td>[(41.151951, -8.574678), (41.151942, -8.574705...</td>
      <td>(41.151951, -8.574678)</td>
      <td>(41.142915, -8.607996)</td>
    </tr>
    <tr>
      <th>4</th>
      <td>1372637091620000337</td>
      <td>OTHER</td>
      <td>1372637091</td>
      <td>[(41.18049, -8.645994), (41.180517, -8.645949)...</td>
      <td>(41.18049, -8.645994)</td>
      <td>(41.178087, -8.687268)</td>
    </tr>
  </tbody>
</table>
</div>



Let's create our map


```python
Q3_Map = folium.Map(location = [41.15,-8.62], zoom_start = 14)

colors = {
    "OTHER" : "purple",
    "STAND_BASED" : "red",
    "CENTRAL_BASED" : "blue"
}

Q3_data = data[['CALL_TYPE', 'START_LOC']]
Q3_data

for index, row in Q3_data.iterrows() :
    color = colors[row['CALL_TYPE']]
    location = row['START_LOC']
    folium.CircleMarker(location, color=color, radius=1, weight=2).add_to(Q3_Map)

Q3_Map
```

![png](map02.png)



## TASK 4
### Question 4 - Which regions of the city are the most common destinations on Mondays? 
    To answer to this question we need to use the TIMESTAMP column to extract the day each trip took place. 


```python
day_names = list(calendar.day_name)
```


```python
get_day = lambda timestamp : day_names[datetime.datetime.fromtimestamp(timestamp).weekday()]

data['week_day'] = data.TIMESTAMP.apply(get_day)
data.head()

```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>TRIP_ID</th>
      <th>CALL_TYPE</th>
      <th>TIMESTAMP</th>
      <th>TRIP_PATH</th>
      <th>START_LOC</th>
      <th>END_LOC</th>
      <th>week_day</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1372636858620000589</td>
      <td>OTHER</td>
      <td>1372636858</td>
      <td>[(41.141412, -8.618643), (41.141376, -8.618499...</td>
      <td>(41.141412, -8.618643)</td>
      <td>(41.154489, -8.630838)</td>
      <td>Sunday</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1372637303620000596</td>
      <td>STAND_BASED</td>
      <td>1372637303</td>
      <td>[(41.159826, -8.639847), (41.159871, -8.640351...</td>
      <td>(41.159826, -8.639847)</td>
      <td>(41.170671, -8.66574)</td>
      <td>Sunday</td>
    </tr>
    <tr>
      <th>2</th>
      <td>1372636951620000320</td>
      <td>OTHER</td>
      <td>1372636951</td>
      <td>[(41.140359, -8.612964), (41.14035, -8.613378)...</td>
      <td>(41.140359, -8.612964)</td>
      <td>(41.14053, -8.61597)</td>
      <td>Sunday</td>
    </tr>
    <tr>
      <th>3</th>
      <td>1372636854620000520</td>
      <td>OTHER</td>
      <td>1372636854</td>
      <td>[(41.151951, -8.574678), (41.151942, -8.574705...</td>
      <td>(41.151951, -8.574678)</td>
      <td>(41.142915, -8.607996)</td>
      <td>Sunday</td>
    </tr>
    <tr>
      <th>4</th>
      <td>1372637091620000337</td>
      <td>OTHER</td>
      <td>1372637091</td>
      <td>[(41.18049, -8.645994), (41.180517, -8.645949)...</td>
      <td>(41.18049, -8.645994)</td>
      <td>(41.178087, -8.687268)</td>
      <td>Sunday</td>
    </tr>
  </tbody>
</table>
</div>



Let's get all the trips on Mondays 


```python
Q4_data = data[data.week_day == 'Monday']
Q4_data.head()

```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>TRIP_ID</th>
      <th>CALL_TYPE</th>
      <th>TIMESTAMP</th>
      <th>TRIP_PATH</th>
      <th>START_LOC</th>
      <th>END_LOC</th>
      <th>week_day</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>220</th>
      <td>1372651639620000517</td>
      <td>CENTRAL_BASED</td>
      <td>1372651639</td>
      <td>[(41.164632, -8.583048), (41.164173, -8.582679...</td>
      <td>(41.164632, -8.583048)</td>
      <td>(41.148855, -8.585604)</td>
      <td>Monday</td>
    </tr>
    <tr>
      <th>236</th>
      <td>1372651473620000454</td>
      <td>CENTRAL_BASED</td>
      <td>1372651473</td>
      <td>[(41.158269, -8.638272), (41.158278, -8.638263...</td>
      <td>(41.158269, -8.638272)</td>
      <td>(41.148864, -8.585631)</td>
      <td>Monday</td>
    </tr>
    <tr>
      <th>240</th>
      <td>1372653466620000403</td>
      <td>STAND_BASED</td>
      <td>1372653466</td>
      <td>[(41.15187, -8.62704)]</td>
      <td>(41.15187, -8.62704)</td>
      <td>(41.15187, -8.62704)</td>
      <td>Monday</td>
    </tr>
    <tr>
      <th>241</th>
      <td>1372652617620000477</td>
      <td>OTHER</td>
      <td>1372652617</td>
      <td>[(41.15115, -8.6148), (41.151348, -8.614728), ...</td>
      <td>(41.15115, -8.6148)</td>
      <td>(41.237523, -8.67024)</td>
      <td>Monday</td>
    </tr>
    <tr>
      <th>242</th>
      <td>1372652978620000167</td>
      <td>CENTRAL_BASED</td>
      <td>1372652978</td>
      <td>[(41.155389, -8.677953), (41.155389, -8.677962...</td>
      <td>(41.155389, -8.677953)</td>
      <td>(41.1462, -8.617698)</td>
      <td>Monday</td>
    </tr>
  </tbody>
</table>
</div>



Now Let's visualize the data on the map


```python
Q4_map = folium.Map(location=[41.15, -8.62], zoom_start=14)

for loc in Q4_data.END_LOC :
    folium.CircleMarker(loc, color='red', radius=1, weight=2).add_to(Q4_map)

Q4_map
```

![png](map03.png)



### Question 5 - What are the most common start and end points on Monday Mornings (from 6 am to 9 am)?
    To answer to this question we need to extract the hour information for each of the trips. 

Let's extract the hour column from TIMESTAMP column 


```python
extrach_hour = lambda timestamp : datetime.datetime.fromtimestamp(timestamp).hour

data['hour'] = data.TIMESTAMP.apply(extrach_hour)
data.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>TRIP_ID</th>
      <th>CALL_TYPE</th>
      <th>TIMESTAMP</th>
      <th>TRIP_PATH</th>
      <th>START_LOC</th>
      <th>END_LOC</th>
      <th>week_day</th>
      <th>Hour</th>
      <th>hour</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1372636858620000589</td>
      <td>OTHER</td>
      <td>1372636858</td>
      <td>[(41.141412, -8.618643), (41.141376, -8.618499...</td>
      <td>(41.141412, -8.618643)</td>
      <td>(41.154489, -8.630838)</td>
      <td>Sunday</td>
      <td>20</td>
      <td>20</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1372637303620000596</td>
      <td>STAND_BASED</td>
      <td>1372637303</td>
      <td>[(41.159826, -8.639847), (41.159871, -8.640351...</td>
      <td>(41.159826, -8.639847)</td>
      <td>(41.170671, -8.66574)</td>
      <td>Sunday</td>
      <td>20</td>
      <td>20</td>
    </tr>
    <tr>
      <th>2</th>
      <td>1372636951620000320</td>
      <td>OTHER</td>
      <td>1372636951</td>
      <td>[(41.140359, -8.612964), (41.14035, -8.613378)...</td>
      <td>(41.140359, -8.612964)</td>
      <td>(41.14053, -8.61597)</td>
      <td>Sunday</td>
      <td>20</td>
      <td>20</td>
    </tr>
    <tr>
      <th>3</th>
      <td>1372636854620000520</td>
      <td>OTHER</td>
      <td>1372636854</td>
      <td>[(41.151951, -8.574678), (41.151942, -8.574705...</td>
      <td>(41.151951, -8.574678)</td>
      <td>(41.142915, -8.607996)</td>
      <td>Sunday</td>
      <td>20</td>
      <td>20</td>
    </tr>
    <tr>
      <th>4</th>
      <td>1372637091620000337</td>
      <td>OTHER</td>
      <td>1372637091</td>
      <td>[(41.18049, -8.645994), (41.180517, -8.645949)...</td>
      <td>(41.18049, -8.645994)</td>
      <td>(41.178087, -8.687268)</td>
      <td>Sunday</td>
      <td>20</td>
      <td>20</td>
    </tr>
  </tbody>
</table>
</div>



Let's get the trips which took place between 6 am to 9 am on Mondays


```python
Q5_data = data[(data.week_day == 'Monday') & (data.hour > 6) & (data.hour < 9)]
Q5_data.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>TRIP_ID</th>
      <th>CALL_TYPE</th>
      <th>TIMESTAMP</th>
      <th>TRIP_PATH</th>
      <th>START_LOC</th>
      <th>END_LOC</th>
      <th>week_day</th>
      <th>Hour</th>
      <th>hour</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>1633</th>
      <td>1372676644620000465</td>
      <td>CENTRAL_BASED</td>
      <td>1372676644</td>
      <td>[(41.182569, -8.604081), (41.182434, -8.604099...</td>
      <td>(41.182569, -8.604081)</td>
      <td>(41.148765, -8.584236)</td>
      <td>Monday</td>
      <td>7</td>
      <td>7</td>
    </tr>
    <tr>
      <th>1637</th>
      <td>1372676476620000189</td>
      <td>CENTRAL_BASED</td>
      <td>1372676476</td>
      <td>[(41.161671, -8.647911), (41.161797, -8.64783)...</td>
      <td>(41.161671, -8.647911)</td>
      <td>(41.161653, -8.647848)</td>
      <td>Monday</td>
      <td>7</td>
      <td>7</td>
    </tr>
    <tr>
      <th>1661</th>
      <td>1372677215620000431</td>
      <td>CENTRAL_BASED</td>
      <td>1372677215</td>
      <td>[(41.157252, -8.610624), (41.157198, -8.610219...</td>
      <td>(41.157252, -8.610624)</td>
      <td>(41.180886, -8.664048)</td>
      <td>Monday</td>
      <td>7</td>
      <td>7</td>
    </tr>
    <tr>
      <th>1671</th>
      <td>1372677241620000295</td>
      <td>STAND_BASED</td>
      <td>1372677241</td>
      <td>[(41.162931, -8.584389), (41.16285, -8.584497)...</td>
      <td>(41.162931, -8.584389)</td>
      <td>(41.175225, -8.586126)</td>
      <td>Monday</td>
      <td>7</td>
      <td>7</td>
    </tr>
    <tr>
      <th>1673</th>
      <td>1372677899620000230</td>
      <td>STAND_BASED</td>
      <td>1372677899</td>
      <td>[(41.147991, -8.619867), (41.148, -8.619867), ...</td>
      <td>(41.147991, -8.619867)</td>
      <td>(41.14431, -8.622288)</td>
      <td>Monday</td>
      <td>7</td>
      <td>7</td>
    </tr>
  </tbody>
</table>
</div>



Now let's visualize this data


```python
Q5_map = folium.Map(location= [41.15, -8.62], zoom_start = 14)

put_start_loc = lambda loc : folium.CircleMarker(loc, color='red', radius=1, weight=2).add_to(Q5_map)
put_end_loc = lambda loc : folium.CircleMarker(loc, color='blue', radius=1, weight=2).add_to(Q5_map)

Q5_data.START_LOC.apply(put_start_loc)
Q5_data.END_LOC.apply(put_end_loc)


Q5_map
```

![png](map04.png)




## TASK 5
### Question 6 - Find out the rush hour in Porto


```python
data.hour.value_counts(sort=True).sort_index().plot.bar()
```




    <Axes: xlabel='hour'>




    
![png](output_40_1.png)
    


### Question 7 - Which streets has the more traffic during the rush hour
    To answer to this question we need to use TRIP_PATH column to visualize each path as a line on the map.
    Firstly, let's filter out the needed data. 


```python
Q7_data = data[data.hour == 9]

Q7_data.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>TRIP_ID</th>
      <th>CALL_TYPE</th>
      <th>TIMESTAMP</th>
      <th>TRIP_PATH</th>
      <th>START_LOC</th>
      <th>END_LOC</th>
      <th>week_day</th>
      <th>Hour</th>
      <th>hour</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>2106</th>
      <td>1372683913620000163</td>
      <td>OTHER</td>
      <td>1372683913</td>
      <td>[(41.154885, -8.630424), (41.154705, -8.63064)...</td>
      <td>(41.154885, -8.630424)</td>
      <td>(41.163669, -8.646849)</td>
      <td>Monday</td>
      <td>9</td>
      <td>9</td>
    </tr>
    <tr>
      <th>2107</th>
      <td>1372684251620000403</td>
      <td>OTHER</td>
      <td>1372684251</td>
      <td>[(41.064822, -8.570754), (41.064795, -8.570808...</td>
      <td>(41.064822, -8.570754)</td>
      <td>(41.056695, -8.566803)</td>
      <td>Monday</td>
      <td>9</td>
      <td>9</td>
    </tr>
    <tr>
      <th>2110</th>
      <td>1372684235620000251</td>
      <td>CENTRAL_BASED</td>
      <td>1372684235</td>
      <td>[(41.148513, -8.616141), (41.14845, -8.616096)...</td>
      <td>(41.148513, -8.616141)</td>
      <td>(41.147055, -8.608887)</td>
      <td>Monday</td>
      <td>9</td>
      <td>9</td>
    </tr>
    <tr>
      <th>2123</th>
      <td>1372684639620000680</td>
      <td>STAND_BASED</td>
      <td>1372684639</td>
      <td>[(41.145588, -8.611011)]</td>
      <td>(41.145588, -8.611011)</td>
      <td>(41.145588, -8.611011)</td>
      <td>Monday</td>
      <td>9</td>
      <td>9</td>
    </tr>
    <tr>
      <th>2141</th>
      <td>1372684824620000611</td>
      <td>CENTRAL_BASED</td>
      <td>1372684824</td>
      <td>[(41.15763, -8.627562)]</td>
      <td>(41.15763, -8.627562)</td>
      <td>(41.15763, -8.627562)</td>
      <td>Monday</td>
      <td>9</td>
      <td>9</td>
    </tr>
  </tbody>
</table>
</div>




```python
coords = Q7_data.TRIP_PATH.iloc[0]

Q7_map = folium.Map(location=[41.15, -8.62], zoom_start=14)
for coords in Q7_data.TRIP_PATH : 
    folium.PolyLine(coords, color='red', opacity=0.1, weight=2).add_to(Q7_map)

Q7_map
```

![png](map05.png)



```python

```
