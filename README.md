
# Parsing dates from Pubmed Abstracts

Erick Lu

April 3, 2020

This is a neat coding exercise to learn how to wrangle and extract data using `pandas`. This code will take a csv file of abstracts, extract the publication date of each abstract, and count the number of abstracts published each year, which we can then perform some time series analysis on.

The csv file of abstracts is downloaded from PubMed using a Python script I wrote that automates NCBI E-utilities, which is available at my GitHub repo [pubmed-abstract-compiler](https://github.com/erilu/pubmed-abstract-compiler). Below is an example using the `abstracts.csv` file from that repo.


```python
import pandas as pd
import re
import calendar
```


```python
abstract_df = pd.read_csv("abstracts.csv", usecols=[0])
```

After importing the data, we will use regexes to search for the year and the month the article was published:


```python
abstract_df['year'] = [int(re.search(".\s(\d{4})\s(\w{3})",row).group(1)) for row in abstract_df['Journal']]
abstract_df['month'] = [re.search(".\s(\d{4})\s(\w{3})",row).group(2) for row in abstract_df['Journal']]
abstract_df.head(5)
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Journal</th>
      <th>year</th>
      <th>month</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>\n1. Cancer Manag Res. 2019 Aug 6;11:7455-7472...</td>
      <td>2019</td>
      <td>Aug</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2. Immunol Rev. 2019 May;289(1):158-172. doi: ...</td>
      <td>2019</td>
      <td>May</td>
    </tr>
    <tr>
      <th>2</th>
      <td>3. Genes Chromosomes Cancer. 2019 Sep;58(9):61...</td>
      <td>2019</td>
      <td>Sep</td>
    </tr>
    <tr>
      <th>3</th>
      <td>4. Nature. 2019 Mar;567(7747):244-248. doi: 10...</td>
      <td>2019</td>
      <td>Mar</td>
    </tr>
    <tr>
      <th>4</th>
      <td>5. Leukemia. 2019 Apr;33(4):893-904. doi: 10.1...</td>
      <td>2019</td>
      <td>Apr</td>
    </tr>
  </tbody>
</table>
</div>



We can convert the month from its 3 letter abbreviation to its number, so that we can sort the data more easily:


```python
abstract_df['month_num'] = [list(calendar.month_abbr).index(x) for x in abstract_df['month']]
abstract_df.head(5)
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Journal</th>
      <th>year</th>
      <th>month</th>
      <th>month_num</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>\n1. Cancer Manag Res. 2019 Aug 6;11:7455-7472...</td>
      <td>2019</td>
      <td>Aug</td>
      <td>8</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2. Immunol Rev. 2019 May;289(1):158-172. doi: ...</td>
      <td>2019</td>
      <td>May</td>
      <td>5</td>
    </tr>
    <tr>
      <th>2</th>
      <td>3. Genes Chromosomes Cancer. 2019 Sep;58(9):61...</td>
      <td>2019</td>
      <td>Sep</td>
      <td>9</td>
    </tr>
    <tr>
      <th>3</th>
      <td>4. Nature. 2019 Mar;567(7747):244-248. doi: 10...</td>
      <td>2019</td>
      <td>Mar</td>
      <td>3</td>
    </tr>
    <tr>
      <th>4</th>
      <td>5. Leukemia. 2019 Apr;33(4):893-904. doi: 10.1...</td>
      <td>2019</td>
      <td>Apr</td>
      <td>4</td>
    </tr>
  </tbody>
</table>
</div>



We want to count the number of papers published each month. A way to do that is to use `groupby` and `count()` in pandas:


```python
abstract_df.groupby(['year','month']).count()
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th></th>
      <th>Journal</th>
      <th>month_num</th>
    </tr>
    <tr>
      <th>year</th>
      <th>month</th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>1997</th>
      <th>May</th>
      <td>1</td>
      <td>1</td>
    </tr>
    <tr>
      <th>2004</th>
      <th>Oct</th>
      <td>1</td>
      <td>1</td>
    </tr>
    <tr>
      <th>2007</th>
      <th>May</th>
      <td>1</td>
      <td>1</td>
    </tr>
    <tr>
      <th rowspan="2" valign="top">2010</th>
      <th>Feb</th>
      <td>1</td>
      <td>1</td>
    </tr>
    <tr>
      <th>Jul</th>
      <td>2</td>
      <td>2</td>
    </tr>
    <tr>
      <th rowspan="2" valign="top">2011</th>
      <th>Feb</th>
      <td>1</td>
      <td>1</td>
    </tr>
    <tr>
      <th>Jun</th>
      <td>1</td>
      <td>1</td>
    </tr>
    <tr>
      <th rowspan="6" valign="top">2012</th>
      <th>Apr</th>
      <td>1</td>
      <td>1</td>
    </tr>
    <tr>
      <th>Dec</th>
      <td>1</td>
      <td>1</td>
    </tr>
    <tr>
      <th>Feb</th>
      <td>1</td>
      <td>1</td>
    </tr>
    <tr>
      <th>Mar</th>
      <td>1</td>
      <td>1</td>
    </tr>
    <tr>
      <th>Oct</th>
      <td>1</td>
      <td>1</td>
    </tr>
    <tr>
      <th>Sep</th>
      <td>2</td>
      <td>2</td>
    </tr>
    <tr>
      <th rowspan="4" valign="top">2013</th>
      <th>Jan</th>
      <td>1</td>
      <td>1</td>
    </tr>
    <tr>
      <th>Jun</th>
      <td>1</td>
      <td>1</td>
    </tr>
    <tr>
      <th>Mar</th>
      <td>1</td>
      <td>1</td>
    </tr>
    <tr>
      <th>Nov</th>
      <td>1</td>
      <td>1</td>
    </tr>
    <tr>
      <th rowspan="5" valign="top">2014</th>
      <th>Aug</th>
      <td>1</td>
      <td>1</td>
    </tr>
    <tr>
      <th>Dec</th>
      <td>1</td>
      <td>1</td>
    </tr>
    <tr>
      <th>Jan</th>
      <td>1</td>
      <td>1</td>
    </tr>
    <tr>
      <th>Nov</th>
      <td>1</td>
      <td>1</td>
    </tr>
    <tr>
      <th>Oct</th>
      <td>1</td>
      <td>1</td>
    </tr>
    <tr>
      <th rowspan="3" valign="top">2015</th>
      <th>Apr</th>
      <td>1</td>
      <td>1</td>
    </tr>
    <tr>
      <th>Dec</th>
      <td>1</td>
      <td>1</td>
    </tr>
    <tr>
      <th>Sep</th>
      <td>1</td>
      <td>1</td>
    </tr>
    <tr>
      <th rowspan="3" valign="top">2016</th>
      <th>Dec</th>
      <td>2</td>
      <td>2</td>
    </tr>
    <tr>
      <th>Feb</th>
      <td>1</td>
      <td>1</td>
    </tr>
    <tr>
      <th>Jul</th>
      <td>1</td>
      <td>1</td>
    </tr>
    <tr>
      <th rowspan="8" valign="top">2017</th>
      <th>Apr</th>
      <td>1</td>
      <td>1</td>
    </tr>
    <tr>
      <th>Aug</th>
      <td>1</td>
      <td>1</td>
    </tr>
    <tr>
      <th>Feb</th>
      <td>1</td>
      <td>1</td>
    </tr>
    <tr>
      <th>Jul</th>
      <td>1</td>
      <td>1</td>
    </tr>
    <tr>
      <th>Jun</th>
      <td>1</td>
      <td>1</td>
    </tr>
    <tr>
      <th>May</th>
      <td>1</td>
      <td>1</td>
    </tr>
    <tr>
      <th>Nov</th>
      <td>2</td>
      <td>2</td>
    </tr>
    <tr>
      <th>Oct</th>
      <td>2</td>
      <td>2</td>
    </tr>
    <tr>
      <th rowspan="4" valign="top">2018</th>
      <th>Feb</th>
      <td>1</td>
      <td>1</td>
    </tr>
    <tr>
      <th>Jul</th>
      <td>1</td>
      <td>1</td>
    </tr>
    <tr>
      <th>Mar</th>
      <td>2</td>
      <td>2</td>
    </tr>
    <tr>
      <th>Sep</th>
      <td>1</td>
      <td>1</td>
    </tr>
    <tr>
      <th rowspan="5" valign="top">2019</th>
      <th>Apr</th>
      <td>1</td>
      <td>1</td>
    </tr>
    <tr>
      <th>Aug</th>
      <td>1</td>
      <td>1</td>
    </tr>
    <tr>
      <th>Mar</th>
      <td>1</td>
      <td>1</td>
    </tr>
    <tr>
      <th>May</th>
      <td>1</td>
      <td>1</td>
    </tr>
    <tr>
      <th>Sep</th>
      <td>1</td>
      <td>1</td>
    </tr>
  </tbody>
</table>
</div>



However, we also want to include entries for each month of each year that do not have any papers published. Every year should have 12 rows, each corresponding to a month, and zeros should be displayed if there were no papers published that month. This way, we can create a monthly timeseries without missing data points.

In order to do this, we can count the number of entries that correspond to each month of the year, for each year, and sequentially append these results to a new table.


```python
papers_by_month_df = pd.DataFrame(columns=['year','month','count'])

for year in range(min(abstract_df['year']),max(abstract_df['year'])+1):
    entries = abstract_df.loc[abstract_df['year']==year,]
    # for months 1-12, count the number of entries and append the new row to papers_by_month_df
    for month in range(1,13):
        count = len(entries.loc[entries['month_num']==month,])
        papers_by_month_df = papers_by_month_df.append(pd.Series([year,month,count], index = papers_by_month_df.columns), ignore_index=True)

papers_by_month_df.tail(24)
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>year</th>
      <th>month</th>
      <th>count</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>252</th>
      <td>2018</td>
      <td>1</td>
      <td>0</td>
    </tr>
    <tr>
      <th>253</th>
      <td>2018</td>
      <td>2</td>
      <td>1</td>
    </tr>
    <tr>
      <th>254</th>
      <td>2018</td>
      <td>3</td>
      <td>2</td>
    </tr>
    <tr>
      <th>255</th>
      <td>2018</td>
      <td>4</td>
      <td>0</td>
    </tr>
    <tr>
      <th>256</th>
      <td>2018</td>
      <td>5</td>
      <td>0</td>
    </tr>
    <tr>
      <th>257</th>
      <td>2018</td>
      <td>6</td>
      <td>0</td>
    </tr>
    <tr>
      <th>258</th>
      <td>2018</td>
      <td>7</td>
      <td>1</td>
    </tr>
    <tr>
      <th>259</th>
      <td>2018</td>
      <td>8</td>
      <td>0</td>
    </tr>
    <tr>
      <th>260</th>
      <td>2018</td>
      <td>9</td>
      <td>1</td>
    </tr>
    <tr>
      <th>261</th>
      <td>2018</td>
      <td>10</td>
      <td>0</td>
    </tr>
    <tr>
      <th>262</th>
      <td>2018</td>
      <td>11</td>
      <td>0</td>
    </tr>
    <tr>
      <th>263</th>
      <td>2018</td>
      <td>12</td>
      <td>0</td>
    </tr>
    <tr>
      <th>264</th>
      <td>2019</td>
      <td>1</td>
      <td>0</td>
    </tr>
    <tr>
      <th>265</th>
      <td>2019</td>
      <td>2</td>
      <td>0</td>
    </tr>
    <tr>
      <th>266</th>
      <td>2019</td>
      <td>3</td>
      <td>1</td>
    </tr>
    <tr>
      <th>267</th>
      <td>2019</td>
      <td>4</td>
      <td>1</td>
    </tr>
    <tr>
      <th>268</th>
      <td>2019</td>
      <td>5</td>
      <td>1</td>
    </tr>
    <tr>
      <th>269</th>
      <td>2019</td>
      <td>6</td>
      <td>0</td>
    </tr>
    <tr>
      <th>270</th>
      <td>2019</td>
      <td>7</td>
      <td>0</td>
    </tr>
    <tr>
      <th>271</th>
      <td>2019</td>
      <td>8</td>
      <td>1</td>
    </tr>
    <tr>
      <th>272</th>
      <td>2019</td>
      <td>9</td>
      <td>1</td>
    </tr>
    <tr>
      <th>273</th>
      <td>2019</td>
      <td>10</td>
      <td>0</td>
    </tr>
    <tr>
      <th>274</th>
      <td>2019</td>
      <td>11</td>
      <td>0</td>
    </tr>
    <tr>
      <th>275</th>
      <td>2019</td>
      <td>12</td>
      <td>0</td>
    </tr>
  </tbody>
</table>
</div>



We're done! Now we have a time series with equidistant data points. We can export the file now, using the simple command below.


```python
papers_by_month_df.to_csv("papers_published_per_month.csv")
```
