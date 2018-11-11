## Indexing Data

You can use .loc to index both rows and columns by name. Syntax is 
```
df.loc[row_begin : row_end, column_begin : column_end]
df.loc[[row_1, row_2, ...], [column_1, column_2, ...]]
```

You can use .iloc to index both rows and columns by index instead of name with the same syntax.

##### In:

```python
loc_slice = google_trends.loc[:, ['Day', 'Harvey_US']]

print(loc_slice[:5])
```

##### Out:
  |     Day | Harvey_US
 ---|---|--- 
0 | 8/20/17    |      0
1 | 8/21/17    |      0
2 | 8/22/17    |      0
3 | 8/23/17    |      1
4 | 8/24/17    |      9

##### In:

```python
iloc_slice = google_trends.iloc[:6, 0:4]

print(iloc_slice)
```

##### Out:
|    Day | Harvey_US | Irma_US | Maria_US
---|---|---|---|---
0 | 8/20/17   |       0   |     0   |      0
1 | 8/21/17   |       0   |     0   |      0
2 | 8/22/17   |       0   |     0   |      0
3 | 8/23/17   |       1   |     0   |      0
4 | 8/24/17   |       9   |     0   |      0
5 | 8/25/17   |      29   |     0   |      0

## Masking

A function to find and replace all observations that meet a certain criterion using boolean logic. The following code shows how you slice the variable in question and then apply a mask to just that variable and add it back into the dataframe. You can also apply a mask to an entire dataframe which would find and then replace all rows instead of singular observations.

Note: We first create a copy of the dataframe we can manipulate so we don't affect later code that uses the original. We have to use the copy() function to replicate the dataframe entirely, otherwise we would just be setting the two variable name pointers equal to each other and manipulating the original dataframe anyways.

##### In:

```python
google_trends_copy = google_trends.copy()
mask = google_trends_copy.Irma_US.mask(google_trends_copy.Irma_US>20, 1000)
google_trends_copy["Irma_US"] = mask
print(google_trends_copy[12:18])
```

##### Out:
| Day | Harvey_US  | Irma_US | Maria_US | Jose_US
---|---|---|---|---|---
12 | 9/1/17     |     7  |     14   |      0    |    0
13 | 9/2/17     |     5  |     13   |      0    |    0
14 | 9/3/17     |     4  |   1000   |      0    |    0
15 | 9/4/17     |     3  |   1000   |      0    |    0
16 | 9/5/17     |     4  |   1000   |      0    |    1
17 | 9/6/17     |     4  |   1000   |      0    |    3

## Functionals

##### In:

```python
# the dataframe is generally very flexible so in the case we want
# to plot date objects on the x-axis we can remove dates as a vector
# and do a list functional
# pandas is known for its time series functionality
google_trends_dates = google_trends['Day']
# the following is a list functional
google_trends_date_objects = [dt.datetime.strptime(d,'%m/%d/%y').date() for d in google_trends_dates]
print(google_trends.head())
```

##### Out:
|  Day |  Harvey_US | Irma_US | Maria_US | Jose_US
---|---|---|---|---|---
0 | 8/20/17    |      0   |     0    |     0   |     0
1 | 8/21/17    |      0   |     0    |     0   |     0
2 | 8/22/17    |      0   |     0    |     0   |     0
3 | 8/23/17    |      1   |     0    |     0   |     0
4 | 8/24/17    |      9   |     0    |     0   |     0



