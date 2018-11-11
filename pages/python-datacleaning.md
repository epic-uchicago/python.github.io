{
 "cells": [
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "## Python for Data Science\n",
    "\n",
    "If you were in the python session about functions and classes, the following code sample is super awesome for motivating [numerical methods](https://nbviewer.jupyter.org/github/davidrpugh/numerical-methods/blob/master/labs/lab-1/lab-1.ipynb) in Python.\n",
    "\n",
    "We will be using the FiveThirtyEight [article](https://github.com/fivethirtyeight/data/tree/master/puerto-rico-media) about Puerto Rico and media attention\n",
    "\n",
    "This orientation session will be tailored to doing some data analysis in Python using the [pandas](https://pandas.pydata.org/pandas-docs/stable/) data science package. A really good blog post to step you through some more in-depth pandas learning is linked [here](https://medium.com/dunder-data/how-to-learn-pandas-108905ab4955). This tutorial is very distilled down.\n",
    "\n",
    "Note, for analysis we will be using these date cutoffs:\n",
    "* Hurricane Harvey makes landfall 8/25/17 \n",
    "* Hurricane Irma makes landfall 9/10/17\n",
    "* Hurricane Maria makes landfall 9/20/17"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 100,
   "metadata": {
    "scrolled": true
   },
   "outputs": [],
   "source": [
    "\n",
    "import os\n",
    "import pandas as pd\n",
    "import numpy as np \n",
    "import matplotlib.pyplot as plt\n",
    "import datetime as dt\n",
    "import getpass\n",
    "import math\n",
    "\n",
    "if getpass.getuser()==\"nadialucas\":\n",
    "    dbPath = r\"/Users/nadialucas/Dropbox/epic_predoc_resources/epic_orientation\"\n",
    "else:\n",
    "\tprint(\"please add your machine's directory\")\n",
    "\n",
    "\n",
    "# Set directory paths\n",
    "dataPath = r\"{}/data/Python_R_Puerto_Rico\".format(dbPath)\n",
    "\n",
    "# read in \n",
    "media_trump_filename = r\"{}/mediacloud_trump.csv\".format(dataPath)\n",
    "google_trends_filename = r\"{}/google_trends.csv\".format(dataPath)\n",
    "media_states_filename = r\"{}/mediacloud_states.csv\".format(dataPath)\n",
    "media_hurricanes_filename = r\"{}/mediacloud_hurricanes.csv\".format(dataPath)\n",
    "tv_hurricanes_filename = r\"{}/tv_hurricanes.csv\".format(dataPath)\n",
    "tv_hurricanes_by_network_filename = r\"{}/tv_hurricanes_by_network.csv\".format(dataPath)"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "### Pandas\n",
    "\n",
    "Pandas is an open-source package that has been written for Python that builds off of existing data structures. Some basic data structures underlying pandas dataframes are arrays (specifically ndarrays) and dictionaries.\n",
    "\n",
    "#### Arrays\n",
    "\n",
    "Arrays are lists of objects. Arrays are quite flexible and can contain any different data types. Functions can be powerfully mapped onto arrays. Some of you may find this similar to Stata where manipulating data is done on a 'per variable' basis.\n",
    "\n",
    "#### Dictionaries\n",
    "\n",
    "Dictionaries, otherwise known as hash tables, are mappings of key, value pairs. They provide very fast lookup time and can store a lot of data.\n",
    "\n",
    "#### Pandas Dataframes\n",
    "\n",
    "Pandas Dataframes are [data structures](https://pandas.pydata.org/pandas-docs/stable/dsintro.html) that are built off of arrays (specifically ndarrays) and dictionaries. They are very flexible and can be created either by using dictionaries of arrays or by arrays of dictionaries. Python is pretty smart and can usually figure out what you are trying to pass into it."
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 101,
   "metadata": {},
   "outputs": [
    {
     "name": "stdout",
     "output_type": "stream",
     "text": [
      "     Hometown    Name  id\n",
      "0   SINGAPORE    Trin   1\n",
      "1      DENVER   Henry   2\n",
      "2     HOUSTON  Andrew   3\n",
      "3  PROVIDENCE   Sunny   4\n",
      "4   NEW DELHI   Kotia   5\n"
     ]
    }
   ],
   "source": [
    "dict_of_arrays = {'id': [1,2,3,4,5], \n",
    "                'Name': ['Trin', 'Henry', 'Andrew', 'Sunny','Kotia'], \n",
    "                'Hometown': ['SINGAPORE', 'DENVER', 'HOUSTON', 'PROVIDENCE', 'NEW DELHI']}\n",
    "pd_doa = pd.DataFrame(dict_of_arrays)\n",
    "print(pd_doa.head())"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 102,
   "metadata": {},
   "outputs": [
    {
     "name": "stdout",
     "output_type": "stream",
     "text": [
      "     Hometown    Name  id\n",
      "0   SINGAPORE    Trin   1\n",
      "1      DENVER   Henry   2\n",
      "2     HOUSTON  Andrew   3\n",
      "3  PROVIDENCE   Sunny   4\n",
      "4   NEW DELHI   Kotia   5\n"
     ]
    }
   ],
   "source": [
    "array_of_dicts = [{'id': 1, 'Name': 'Trin', 'Hometown': 'SINGAPORE'},\n",
    "                  {'id': 2, 'Name': 'Henry', 'Hometown': 'DENVER'},\n",
    "                  {'id': 3, 'Name': 'Andrew', 'Hometown': 'HOUSTON'},\n",
    "                  {'id': 4, 'Name': 'Sunny', 'Hometown': 'PROVIDENCE'},\n",
    "                  {'id': 5, 'Name': 'Kotia', 'Hometown': 'NEW DELHI'}]\n",
    "pd_aod = pd.DataFrame(array_of_dicts)\n",
    "print(pd_aod.head())"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "### Reading in Data\n",
    "\n",
    "Of course, there are built-in funtions that read data from outside files as pandas objects, which is what you'll end up needing to do but understanding the basic data structures pandas dataframes are built off of will help you understand the syntax of using and manipulating these data structures"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 103,
   "metadata": {},
   "outputs": [
    {
     "name": "stdout",
     "output_type": "stream",
     "text": [
      "       Day  \"Hurricane Harvey\": (United States)  \\\n",
      "0  8/20/17                                    0   \n",
      "1  8/21/17                                    0   \n",
      "2  8/22/17                                    0   \n",
      "3  8/23/17                                    1   \n",
      "4  8/24/17                                    9   \n",
      "\n",
      "   \"Hurricane Irma\": (United States)  \"Hurricane Maria\": (United States)  \\\n",
      "0                                  0                                   0   \n",
      "1                                  0                                   0   \n",
      "2                                  0                                   0   \n",
      "3                                  0                                   0   \n",
      "4                                  0                                   0   \n",
      "\n",
      "   \"Hurricane Jose\": (United States)  \n",
      "0                                  0  \n",
      "1                                  0  \n",
      "2                                  0  \n",
      "3                                  0  \n",
      "4                                  0  \n"
     ]
    }
   ],
   "source": [
    "# we create the object media_trump which is an instance of a pandas dataframe\n",
    "media_trump = pd.read_csv(media_trump_filename)\n",
    "google_trends = pd.read_csv(google_trends_filename)\n",
    "media_states = pd.read_csv(media_states_filename)\n",
    "media_hurricanes = pd.read_csv(media_hurricanes_filename)\n",
    "tv_hurricanes = pd.read_csv(tv_hurricanes_filename)\n",
    "tv_hurricanes_by_network = pd.read_csv(tv_hurricanes_by_network_filename)\n",
    "\n",
    "print(google_trends.head())"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "## Manipulating Data"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 104,
   "metadata": {},
   "outputs": [
    {
     "name": "stdout",
     "output_type": "stream",
     "text": [
      "       Day  Harvey_US  Irma_US  Maria_US  Jose_US\n",
      "0  8/20/17          0        0         0        0\n",
      "1  8/21/17          0        0         0        0\n",
      "2  8/22/17          0        0         0        0\n",
      "3  8/23/17          1        0         0        0\n",
      "4  8/24/17          9        0         0        0\n"
     ]
    }
   ],
   "source": [
    "# For some reason the csvs we read in sometimes has wonky or long variable titles so let's clean those up\n",
    "# and make sure to get rid of random quotation marks we find.\n",
    "media_trump = media_trump.rename(columns = {'title:\"Puerto Rico\"': 'PuertoRico',\n",
    "                                 'title:\"Puerto Rico\" AND (title:Trump OR title:President)': 'PuertoRico_Trump',\n",
    "                                 'title:Florida': 'Florida',\n",
    "                                 'title:Florida AND (title:Trump OR title:President)': 'Florida_Trump', \n",
    "                                 'title:Texas': 'Texas',\n",
    "                                 'title:Texas AND (title:Trump OR title:President)': 'Texas_Trump'})\n",
    "\n",
    "google_trends = google_trends.rename({'\"Hurricane Harvey\": (United States)': 'Harvey_US',\n",
    "                                    '\"Hurricane Irma\": (United States)': 'Irma_US',\n",
    "                                    '\"Hurricane Maria\": (United States)': 'Maria_US',\n",
    "                                    '\"Hurricane Jose\": (United States)': 'Jose_US',\n",
    "                                    '\"Puerto Rico\"': 'Puerto Rico'}, axis = 'columns')\n",
    "print(google_trends.head())"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "## Indexing Data\n",
    "\n",
    "You can use .loc to index both rows and columns by name. Syntax is \n",
    "```\n",
    "df.loc[row_begin : row_end, column_begin : column_end]\n",
    "df.loc[[row_1, row_2, ...], [column_1, column_2, ...]]\n",
    "```\n",
    "\n",
    "You can use .iloc to index both rows and columns by index instead of name with the same syntax."
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 105,
   "metadata": {},
   "outputs": [
    {
     "name": "stdout",
     "output_type": "stream",
     "text": [
      "       Day  Harvey_US\n",
      "0  8/20/17          0\n",
      "1  8/21/17          0\n",
      "2  8/22/17          0\n",
      "3  8/23/17          1\n",
      "4  8/24/17          9\n"
     ]
    }
   ],
   "source": [
    "loc_slice = google_trends.loc[:, ['Day', 'Harvey_US']]\n",
    "\n",
    "print(loc_slice[:5])"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 106,
   "metadata": {},
   "outputs": [
    {
     "name": "stdout",
     "output_type": "stream",
     "text": [
      "       Day  Harvey_US  Irma_US  Maria_US\n",
      "0  8/20/17          0        0         0\n",
      "1  8/21/17          0        0         0\n",
      "2  8/22/17          0        0         0\n",
      "3  8/23/17          1        0         0\n",
      "4  8/24/17          9        0         0\n",
      "5  8/25/17         29        0         0\n"
     ]
    }
   ],
   "source": [
    "iloc_slice = google_trends.iloc[:6, 0:4]\n",
    "\n",
    "print(iloc_slice)"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "## Masking\n",
    "\n",
    "A function to find and replace all observations that meet a certain criterion using boolean logic. The following code shows how you slice the variable in question and then apply a mask to just that variable and add it back into the dataframe. You can also apply a mask to an entire dataframe which would find and then replace all rows instead of singular observations.\n",
    "\n",
    "Note: We first create a copy of the dataframe we can manipulate so we don't affect later code that uses the original. We have to use the copy() function to replicate the dataframe entirely, otherwise we would just be setting the two variable name pointers equal to each other and manipulating the original dataframe anyways."
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 119,
   "metadata": {},
   "outputs": [
    {
     "name": "stdout",
     "output_type": "stream",
     "text": [
      "       Day  Harvey_US  Irma_US  Maria_US  Jose_US\n",
      "12  9/1/17          7       14         0        0\n",
      "13  9/2/17          5       13         0        0\n",
      "14  9/3/17          4     1000         0        0\n",
      "15  9/4/17          3     1000         0        0\n",
      "16  9/5/17          4     1000         0        1\n",
      "17  9/6/17          4     1000         0        3\n"
     ]
    }
   ],
   "source": [
    "google_trends_copy = google_trends.copy()\n",
    "mask = google_trends_copy.Irma_US.mask(google_trends_copy.Irma_US>20, 1000)\n",
    "google_trends_copy[\"Irma_US\"] = mask\n",
    "print(google_trends_copy[12:18])"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "## Functionals\n"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 120,
   "metadata": {},
   "outputs": [
    {
     "name": "stdout",
     "output_type": "stream",
     "text": [
      "       Day  Harvey_US  Irma_US  Maria_US  Jose_US\n",
      "0  8/20/17          0        0         0        0\n",
      "1  8/21/17          0        0         0        0\n",
      "2  8/22/17          0        0         0        0\n",
      "3  8/23/17          1        0         0        0\n",
      "4  8/24/17          9        0         0        0\n"
     ]
    }
   ],
   "source": [
    "# the dataframe is generally very flexible so in the case we want\n",
    "# to plot date objects on the x-axis we can remove dates as a vector\n",
    "# and do a list functional\n",
    "# pandas is known for its time series functionality\n",
    "google_trends_dates = google_trends['Day']\n",
    "# the following is a list functional\n",
    "google_trends_date_objects = [dt.datetime.strptime(d,'%m/%d/%y').date() for d in google_trends_dates]\n",
    "print(google_trends.head())"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "## Visualizing Data"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 14,
   "metadata": {},
   "outputs": [
    {
     "data": {
      "image/png": "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\n",
      "text/plain": [
       "<Figure size 432x288 with 1 Axes>"
      ]
     },
     "metadata": {},
     "output_type": "display_data"
    }
   ],
   "source": [
    "# then we can plot the x-axis as a date object instead of dealing with a \n",
    "# clutter of dates as strings\n",
    "plt.plot(google_trends_date_objects, google_trends['Harvey_US'], label = \"Harvey\")\n",
    "plt.plot(google_trends_date_objects, google_trends['Irma_US'], label = \"Irma\")\n",
    "plt.plot(google_trends_date_objects, google_trends['Maria_US'], label = \"Maria\")\n",
    "plt.axvline(x=dt.datetime.strptime(\"9/20/17\", '%m/%d/%y').date(), linestyle = 'dotted')\n",
    "plt.axvline(x=dt.datetime.strptime(\"8/25/17\", '%m/%d/%y').date(), linestyle = 'dotted')\n",
    "plt.axvline(x=dt.datetime.strptime(\"9/10/17\", '%m/%d/%y').date(), linestyle = 'dotted')\n",
    "plt.legend(loc='best')\n",
    "\n",
    "plt.show()\n"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "metadata": {
    "collapsed": true
   },
   "outputs": [],
   "source": []
  }
 ],
 "metadata": {
  "kernelspec": {
   "display_name": "Python 2",
   "language": "python",
   "name": "python2"
  },
  "language_info": {
   "codemirror_mode": {
    "name": "ipython",
    "version": 2
   },
   "file_extension": ".py",
   "mimetype": "text/x-python",
   "name": "python",
   "nbconvert_exporter": "python",
   "pygments_lexer": "ipython2",
   "version": "2.7.15"
  }
 },
 "nbformat": 4,
 "nbformat_minor": 2
}
