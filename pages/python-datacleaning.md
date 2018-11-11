## Data Cleaning

##### In:

```python
import os
import pandas as pd
import numpy as np 
import matplotlib.pyplot as plt
import datetime as dt
import getpass
import math

if getpass.getuser()=="nadialucas":
    dbPath = r"/Users/nadialucas/Dropbox/epic_predoc_resources/epic_orientation"
else:
  print("please add your machine's directory")


# Set directory paths
dataPath = r"{}/data/Python_R_Puerto_Rico".format(dbPath)

# read in 
media_trump_filename = r"{}/mediacloud_trump.csv".format(dataPath)
google_trends_filename = r"{}/google_trends.csv".format(dataPath)
media_states_filename = r"{}/mediacloud_states.csv".format(dataPath)
media_hurricanes_filename = r"{}/mediacloud_hurricanes.csv".format(dataPath)
tv_hurricanes_filename = r"{}/tv_hurricanes.csv".format(dataPath)
tv_hurricanes_by_network_filename = r"{}/tv_hurricanes_by_network.csv".format(dataPath)
```

### Pandas

Pandas is an open-source package that has been written for Python that builds off of existing data structures. Some basic data structures underlying pandas dataframes are arrays (specifically ndarrays) and dictionaries.

#### Arrays

Arrays are lists of objects. Arrays are quite flexible and can contain any different data types. Functions can be powerfully mapped onto arrays. Some of you may find this similar to Stata where manipulating data is done on a 'per variable' basis.

#### Dictionaries

Dictionaries, otherwise known as hash tables, are mappings of key, value pairs. They provide very fast lookup time and can store a lot of data.

#### Pandas Dataframes

Pandas Dataframes are [data structures](https://pandas.pydata.org/pandas-docs/stable/dsintro.html) that are built off of arrays (specifically ndarrays) and dictionaries. They are very flexible and can be created either by using dictionaries of arrays or by arrays of dictionaries. Python is pretty smart and can usually figure out what you are trying to pass into it.

##### In:

```python
dict_of_arrays = {'id': [1,2,3,4,5], 
                'Name': ['Trin', 'Henry', 'Andrew', 'Sunny','Kotia'], 
                'Hometown': ['SINGAPORE', 'DENVER', 'HOUSTON', 'PROVIDENCE', 'NEW DELHI']}
pd_doa = pd.DataFrame(dict_of_arrays)
print(pd_doa.head())
```
##### Out:
|    Hometown  |  Name  |id
---|---|---|---
0  | SINGAPORE  |  Trin  | 1
1  |    DENVER |  Henry |  2
2   |  HOUSTON | Andrew  | 3
3 | PROVIDENCE |  Sunny  | 4
4  | NEW DELHI  | Kotia  | 5

##### In:

```python
array_of_dicts = [{'id': 1, 'Name': 'Trin', 'Hometown': 'SINGAPORE'},
                  {'id': 2, 'Name': 'Henry', 'Hometown': 'DENVER'},
                  {'id': 3, 'Name': 'Andrew', 'Hometown': 'HOUSTON'},
                  {'id': 4, 'Name': 'Sunny', 'Hometown': 'PROVIDENCE'},
                  {'id': 5, 'Name': 'Kotia', 'Hometown': 'NEW DELHI'}]
pd_aod = pd.DataFrame(array_of_dicts)
print(pd_aod.head())
```
##### Out:

|    Hometown  |  Name  |id
---|---|---|---
0  | SINGAPORE  |  Trin  | 1
1  |    DENVER |  Henry |  2
2   |  HOUSTON | Andrew  | 3
3 | PROVIDENCE |  Sunny  | 4
4  | NEW DELHI  | Kotia  | 5

### Reading in Data

Of course, there are built-in funtions that read data from outside files as pandas objects, which is what you'll end up needing to do but understanding the basic data structures pandas dataframes are built off of will help you understand the syntax of using and manipulating these data structures.

##### In:

```python
# we create the object media_trump which is an instance of a pandas dataframe
media_trump = pd.read_csv(media_trump_filename)
google_trends = pd.read_csv(google_trends_filename)
media_states = pd.read_csv(media_states_filename)
media_hurricanes = pd.read_csv(media_hurricanes_filename)
tv_hurricanes = pd.read_csv(tv_hurricanes_filename)
tv_hurricanes_by_network = pd.read_csv(tv_hurricanes_by_network_filename)

print(google_trends.head())
```

##### Out:

|   Day | "Hurricane Harvey": (United States)  | "Hurricane Irma": (United States) | "Hurricane Maria": (United States) | "Hurricane Jose": (United States)
---|---|---|---|---|---
0 | 8/20/17 | 0   |0|0|0
1 | 8/21/17  |  0   |0|0|0
2 | 8/22/17 | 0  |0|0|0 
3 | 8/23/17 |  1  |0|0|0 
4 | 8/24/17 | 9|0|0|0

## Cleaning Data
# For some reason the csvs we read in sometimes has wonky or long variable titles so let's clean those up
# and make sure to get rid of random quotation marks we find.

##### In:

```python
media_trump = media_trump.rename(columns = {'title:"Puerto Rico"': 'PuertoRico',
                                 'title:"Puerto Rico" AND (title:Trump OR title:President)': 'PuertoRico_Trump',
                                 'title:Florida': 'Florida',
                                 'title:Florida AND (title:Trump OR title:President)': 'Florida_Trump', 
                                 'title:Texas': 'Texas',
                                 'title:Texas AND (title:Trump OR title:President)': 'Texas_Trump'})

google_trends = google_trends.rename({'"Hurricane Harvey": (United States)': 'Harvey_US',
                                    '"Hurricane Irma": (United States)': 'Irma_US',
                                    '"Hurricane Maria": (United States)': 'Maria_US',
                                    '"Hurricane Jose": (United States)': 'Jose_US',
                                    '"Puerto Rico"': 'Puerto Rico'}, axis = 'columns')
print(google_trends.head())
```
##### Out:

| Day | Harvey_US | Irma_US | Maria_US | Jose_US
---|---|---|---|---|---
0 | 8/20/17      |    0    |    0    |     0    |    0
1 | 8/21/17      |    0    |    0    |     0    |    0
2 | 8/22/17      |    0    |    0    |     0    |    0
3 | 8/23/17      |    1    |    0    |     0    |    0
4 | 8/24/17      |    9    |    0    |     0    |    0




