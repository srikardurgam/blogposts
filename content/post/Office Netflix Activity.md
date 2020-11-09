```python
import pandas as pd
df = pd.read_csv (r'C:\Users\srika\Documents\ViewingActivity.csv')
```

# Analyzing my Netflix Data

I just decided to become more vulnerable with my data I guess. This week I will be exploring my netflix data and see how many hours and days I have spent watching my two favorite shows at the moment. The Office (all time favorite) and my new favorite (Schitts Creek)


```python
df.shape
```




    (10783, 10)




```python
df.head(1)
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
      <th>Profile Name</th>
      <th>Start Time</th>
      <th>Duration</th>
      <th>Attributes</th>
      <th>Title</th>
      <th>Supplemental Video Type</th>
      <th>Device Type</th>
      <th>Bookmark</th>
      <th>Latest Bookmark</th>
      <th>Country</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Venkata Srikar</td>
      <td>2020-11-02 20:10:14</td>
      <td>00:12:05</td>
      <td>NaN</td>
      <td>Schitt's Creek: Season 3: Driving Test (Episod...</td>
      <td>NaN</td>
      <td>Netflix Chrome MAC (Cadmium) HTML 5</td>
      <td>00:18:32</td>
      <td>00:18:32</td>
      <td>US (United States)</td>
    </tr>
  </tbody>
</table>
</div>




```python
df = df.drop(['Profile Name', 'Attributes', 'Supplemental Video Type', 'Device Type', 'Bookmark', 'Latest Bookmark', 'Country'], axis=1)
df.head(1)
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
      <th>Start Time</th>
      <th>Duration</th>
      <th>Title</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>2020-11-02 20:10:14</td>
      <td>00:12:05</td>
      <td>Schitt's Creek: Season 3: Driving Test (Episod...</td>
    </tr>
  </tbody>
</table>
</div>




```python
df.dtypes

```




    Start Time    object
    Duration      object
    Title         object
    dtype: object




```python
df['Start Time'] = pd.to_datetime(df['Start Time'], utc=True)
df.dtypes
```




    Start Time    datetime64[ns, UTC]
    Duration                   object
    Title                      object
    dtype: object




```python
# change the Start Time column into the dataframe's index
df = df.set_index('Start Time')

# convert from UTC timezone to eastern time
df.index = df.index.tz_convert('US/Eastern')

# reset the index so that Start Time becomes a column again
df = df.reset_index()

#double-check that it worked
df.head(1)
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
      <th>Start Time</th>
      <th>Duration</th>
      <th>Title</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>2020-11-02 15:10:14-05:00</td>
      <td>00:12:05</td>
      <td>Schitt's Creek: Season 3: Driving Test (Episod...</td>
    </tr>
  </tbody>
</table>
</div>




```python
df['Duration'] = pd.to_timedelta(df['Duration'])
df.dtypes
```




    Start Time    datetime64[ns, US/Eastern]
    Duration                 timedelta64[ns]
    Title                             object
    dtype: object




```python
# create a new dataframe called office that that takes from df
# only the rows in which the Title column contains 'The Office (U.S.)'
office = df[df['Title'].str.contains('The Office (U.S.)', regex=False)]
```


```python
Schitts_Creek = df[df['Title'].str.contains("Schitt's Creek", regex=False)]
```


```python
Schitts_Creek.shape
```




    (62, 3)




```python
Schitts_Creek = Schitts_Creek[(Schitts_Creek['Duration'] > '0 days 00:01:00')]
Schitts_Creek.shape
```




    (54, 3)




```python
Schitts_Creek['weekday'] = Schitts_Creek['Start Time'].dt.weekday
Schitts_Creek['hour'] = Schitts_Creek['Start Time'].dt.hour

# check to make sure the columns were added correctly
Schitts_Creek.head(10)
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
      <th>Start Time</th>
      <th>Duration</th>
      <th>Title</th>
      <th>weekday</th>
      <th>hour</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>2020-11-02 15:10:14-05:00</td>
      <td>00:12:05</td>
      <td>Schitt's Creek: Season 3: Driving Test (Episod...</td>
      <td>0</td>
      <td>15</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2020-11-02 13:46:49-05:00</td>
      <td>00:06:27</td>
      <td>Schitt's Creek: Season 3: Driving Test (Episod...</td>
      <td>0</td>
      <td>13</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2020-11-02 13:25:10-05:00</td>
      <td>00:21:31</td>
      <td>Schitt's Creek: Season 3: New Car (Episode 3)</td>
      <td>0</td>
      <td>13</td>
    </tr>
    <tr>
      <th>3</th>
      <td>2020-11-02 13:03:38-05:00</td>
      <td>00:21:31</td>
      <td>Schitt's Creek: Season 3: The Throuple (Episod...</td>
      <td>0</td>
      <td>13</td>
    </tr>
    <tr>
      <th>4</th>
      <td>2020-11-02 12:54:29-05:00</td>
      <td>00:09:08</td>
      <td>Schitt's Creek: Season 3: Opening Night (Episo...</td>
      <td>0</td>
      <td>12</td>
    </tr>
    <tr>
      <th>5</th>
      <td>2020-11-01 17:10:19-05:00</td>
      <td>00:12:19</td>
      <td>Schitt's Creek: Season 3: Opening Night (Episo...</td>
      <td>6</td>
      <td>17</td>
    </tr>
    <tr>
      <th>6</th>
      <td>2020-11-01 16:55:11-05:00</td>
      <td>00:15:07</td>
      <td>Schitt's Creek: Season 2: Happy Anniversary (E...</td>
      <td>6</td>
      <td>16</td>
    </tr>
    <tr>
      <th>9</th>
      <td>2020-11-01 00:04:10-04:00</td>
      <td>00:05:50</td>
      <td>Schitt's Creek: Season 2: Happy Anniversary (E...</td>
      <td>6</td>
      <td>0</td>
    </tr>
    <tr>
      <th>11</th>
      <td>2020-10-31 23:52:48-04:00</td>
      <td>00:11:21</td>
      <td>Schitt's Creek: Season 2: Lawn Signs (Episode 12)</td>
      <td>5</td>
      <td>23</td>
    </tr>
    <tr>
      <th>12</th>
      <td>2020-10-31 23:01:39-04:00</td>
      <td>00:10:02</td>
      <td>Schitt's Creek: Season 2: Lawn Signs (Episode 12)</td>
      <td>5</td>
      <td>23</td>
    </tr>
  </tbody>
</table>
</div>




```python
office.shape
```




    (3294, 3)




```python
office = office[(office['Duration'] > '0 days 00:01:00')]
office.shape
```




    (3035, 3)




```python
office['Duration'].sum()
```




    Timedelta('29 days 02:15:49')




```python
office['weekday'] = office['Start Time'].dt.weekday
office['hour'] = office['Start Time'].dt.hour

# check to make sure the columns were added correctly
office.head(1)
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
      <th>Start Time</th>
      <th>Duration</th>
      <th>Title</th>
      <th>weekday</th>
      <th>hour</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>100</th>
      <td>2020-10-16 20:44:59-04:00</td>
      <td>00:08:15</td>
      <td>The Office (U.S.): Season 7: Todd Packer (Epis...</td>
      <td>4</td>
      <td>20</td>
    </tr>
  </tbody>
</table>
</div>




```python
%matplotlib inline
import matplotlib
```


```python
# set our categorical and define the order so the days are plotted Monday-Sunday
office['weekday'] = pd.Categorical(office['weekday'], categories=
    [0,1,2,3,4,5,6],
    ordered=True)

# create office_by_day and count the rows for each weekday, assigning the result to that variable
office_by_day = office['weekday'].value_counts()

# sort the index using our categorical, so that Monday (0) is first, Tuesday (1) is second, etc.
office_by_day = office_by_day.sort_index()

# optional: update the font size to make it a bit larger and easier to read
matplotlib.rcParams.update({'font.size': 22})

# plot office_by_day as a bar chart with the listed size and title
office_by_day.plot(kind='bar', figsize=(20,10), title='Office Episodes Watched by Day')
```




    <matplotlib.axes._subplots.AxesSubplot at 0x1b958540ca0>




![png](output_20_1.png)



```python
# set our categorical and define the order so the days are plotted Monday-Sunday
Schitts_Creek['weekday'] = pd.Categorical(Schitts_Creek['weekday'], categories=
    [0,1,2,3,4,5,6],
    ordered=True)
# create office_by_day and count the rows for each weekday, assigning the result to that variable
Schitts_Creek_by_day = Schitts_Creek['weekday'].value_counts()

# sort the index using our categorical, so that Monday (0) is first, Tuesday (1) is second, etc.
Schitts_Creek_by_day = Schitts_Creek_by_day.sort_index()

# optional: update the font size to make it a bit larger and easier to read
matplotlib.rcParams.update({'font.size': 22})

# plot office_by_day as a bar chart with the listed size and title
Schitts_Creek_by_day.plot(kind='bar', figsize=(20,10), title='Schitts Creek Episodes Watched by Day')
```




    <matplotlib.axes._subplots.AxesSubplot at 0x1b95912b790>




![png](output_21_1.png)



```python
# set our categorical and define the order so the hours are plotted 0-23
office['hour'] = pd.Categorical(office['hour'], categories=
    [0,1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22,23],
    ordered=True)

# create office_by_hour and count the rows for each hour, assigning the result to that variable
office_by_hour = office['hour'].value_counts()

# sort the index using our categorical, so that midnight (0) is first, 1 a.m. (1) is second, etc.
office_by_hour = office_by_hour.sort_index()

# plot office_by_hour as a bar chart with the listed size and title
office_by_hour.plot(kind='bar', figsize=(20,10), title='Office Episodes Watched by Hour')
```




    <matplotlib.axes._subplots.AxesSubplot at 0x1b9585515b0>




![png](output_22_1.png)



```python
# set our categorical and define the order so the hours are plotted 0-23
Schitts_Creek['hour'] = pd.Categorical(Schitts_Creek['hour'], categories=
    [0,1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22,23],
    ordered=True)

# create office_by_hour and count the rows for each hour, assigning the result to that variable
Schitts_Creek_by_hour = Schitts_Creek['hour'].value_counts()

# sort the index using our categorical, so that midnight (0) is first, 1 a.m. (1) is second, etc.
Schitts_Creek_by_hour = Schitts_Creek_by_hour.sort_index()

# plot office_by_hour as a bar chart with the listed size and title
Schitts_Creek_by_hour.plot(kind='bar', figsize=(20,10), title='Schitts Creek Episodes Watched by Hour')
```




    <matplotlib.axes._subplots.AxesSubplot at 0x1b959a7fee0>




![png](output_23_1.png)

