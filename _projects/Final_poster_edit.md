---
title: "Visualizing Lansing Traffic Stop Data"
image: 
  path: /assets/images/chocolate-chip-cookies-lg.jpg
  thumbnail: /assets/images/chocolate-chip-cookies-400x200.jpg
  caption: "Photo from [Pexels](https://www.pexels.com)"
---

### <p style="text-align: right;"> Brandon McIntyre </p>
#### <p style="text-align: right;"> Analysis of Lansing Traffic Stops <br /> <br /> CMSE 402 SS20 </p>

# Final Writeup

**Data Used**

Traffic Stop Data of Lansing Police Department (02/01/2016 - 02/13/2019):
http://data-lansing.opendata.arcgis.com/datasets/32b48df10c674a7aace77071046ba272_0?selectedAttribute=Date

Census Data/ ACS Data:
https://data.census.gov/cedsci/table?q=Lansing%20MI&g=1600000US2646000&hidePreview=false&tid=ACSDP5Y2018.DP05&vintage=2010&layer=VT_2018_160_00_PY_D1&cid=DP05_0001E

**Note**

Looking at Lansing Police Department MATS Data Analysis for Year 17 
(https://lansingmi.gov/DocumentCenter/View/6979/MATS-Months193-204-May-2018) 
it is mentioned that using Census Data is not 100% accurate as driver demographics do not necessarily match census demographics. This makes using Census data troublesome. This site 
(https://nij.ojp.gov/topics/articles/racial-profiling-and-traffic-stops)
goes into more detail on the subject. Thus it appears that to get proper comparsion, one must look at the demographics of all licensed holding drivers. This information is only avalible on a state level and only on Sex. 
https://www.fhwa.dot.gov/policyinformation/statistics/2016/dl1c.cfm . 
(Atleast offically recognized datasets that I could find). So with this, I will continue to the use ACS data. However, it may not produce 100% accurate results. I also tried looking for more MATS traffic stop data from lansing department, however I was unable to.  
(https://data.census.gov/cedsci/table?q=Lansing%20MI&g=1600000US2646000&hidePreview=false&tid=ACSDP5Y2018.DP05&vintage=2010&layer=VT_2018_160_00_PY_D1&cid=DP05_0001E)  

Upon more research I have also found since the writeup a traffic stop analysis for Year 18
(https://lansingmi.gov/ArchiveCenter/ViewFile/Item/655)  
and Year 16
https://www.lansingmi.gov/Archive/ViewFile/Item/603

**Interesting News since April 1st**

Here are some interesting articles just posted on 4/1/20 about this particular issue in the East lansing police department.
(They all say the same excat thing, some with visualizations (That are not particularly good))
(https://www.lansingcitypulse.com/stories/mayor-east-lansing-police-over-stop-black-drivers-which-is-not-acceptable,14053)
(https://www.lansingstatejournal.com/story/news/2020/04/01/east-lansing-wants-changes-after-study-shows-race-bias-police-stops/5102549002/)
(https://eastlansinginfo.org/content/newly-released-data-show-racial-bias-east-lansing-policing)
(https://statenews.com/article/2020/04/elpd-race-data-report-shows-african-americans-over-stopped-in-east-lansing?ct=content_open&cv=cbox_featured)
(https://www.wilx.com/content/news/City-of-East-Lansing-releases-race-data-for-ELPD-officer-initiated-contacts-569281531.html)

The articles state that in the past 2 months there have been an "over-stop of black drivers". This came from "The East Lansing Police Department began gathering race data on officer-initiated contacts, like traffic stops, in February after a black 19-year-old accused an officer of using excessive force during an arrest. The study was requested by the City Council. "
The study states "Though black residents comprise 8% of East Lansing's population, they accounted for 22% of police officer-initiated contact in February and March, according to a department study".

Now this is particualry interesting based upon the fact that the EL study is based upon census data. The Lansing Police department made it specific that Census data is not conclusive based upon the difference between census and actual licensed drivers. The city pulse article actaully references the Lansing Data that I am looking at now and say that similar trends are in the Lansing Traffic stop data as well.

This information is of particular interest because honeslty I don't know which side to belive. I feel like the issue in this situation stems from the lack of information when it comes to demographics on lisenced drivers. Not having this causes Lansing police to say that there is no racial biasness, yet EL police department to say that there is a bias and they want to correct it. Who is in the wrong and who is in the right?

I have tried to find the data from EL that the study is on, but all I can seem to find are there weekly reports that do not include the race (https://www.cityofeastlansing.com/Archive.aspx?AMID=47). Further more, another point of interest on this topic, the annual report that EL police deparment puts out does not include race in any manner (https://www.cityofeastlansing.com/DocumentCenter/View/910/ELPD-Annual-Report-2019-PDF?bidId=). Leading me to think that EL is not as experinced when it comes to the lansing department when it comes to answering the question of racial biasing.

### Links


**Variable: Exploration and Plots**

* [Date: Year,Month,Day,Hour,Min](#Date)   
* [Reason for Stop](#Reason_for_Stop)  
* [Race/Ethnicity](#Race_Ethnicity)  
* [Gender](#Gender)  
* [Analysis of Age](#Age)  
* [Search Performed](#Search_Performed)  
* [Search Authority](#Search_Authority)  
* [Discovery If_Searched](#Discovery_If_Searched)  
* [Result of Stop](#Result_of_Stop)  
* [Officer Badge](#Officer_Badge)

**Questions**  

* [Individuals who were searched vs not searched is there a link to demographics?](#Search)
* [Is there any demographic subjected to harsher punishment across traffic stop results by Reason for Stop,
Reason for Search, Search Preformed, Search Discovery and Search Authority, and Result?](#Harsh)  
* [Is there a trend by Age of which certain demographics are pulled over?](#Street)  
* [Is there any officer that is pulling over a disproportionate percentage of individuals from
a given demographic?](#Officer)  
* [Are certain demographics more targeted per time of day of the traffic stop?](#Time)

**Libraries**


```python
import pandas as pd
import numpy as np
import pytz
import seaborn as sns
import matplotlib
import matplotlib.ticker as mtick
import matplotlib.pyplot as plt
```

### Load in Data

**Traffic Stop Data**

Data came not sorted. Data was sorted by time. Time was also off by 5 hours as it was given in GMT. The data was corrected to be moved to EST


```python
traffic = pd.read_csv("./Traffic_Stops.csv", parse_dates=["Date"])
traffic.sort_values("Date", axis=0, ascending=True, inplace=True)
traffic.reset_index(inplace=True,drop=True)
traffic["Date"] = traffic["Date"].dt.tz_convert(pytz.timezone('US/Eastern'))
traffic
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
      <th>Date</th>
      <th>Team_Area</th>
      <th>Street_Area</th>
      <th>Reason_for_Stop</th>
      <th>Race_Ethnicity</th>
      <th>Gender</th>
      <th>Age</th>
      <th>Search_Performed</th>
      <th>Search_Authority</th>
      <th>Discovery_If_Searched</th>
      <th>Result_of_Stop</th>
      <th>Officer_Badge</th>
      <th>Traffic_Crash</th>
      <th>Serial_Number</th>
      <th>ObjectId</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>0</td>
      <td>2016-02-01 00:03:00-05:00</td>
      <td>4</td>
      <td>Jolly</td>
      <td>Equipment Violation</td>
      <td>White</td>
      <td>Male</td>
      <td>23</td>
      <td>None</td>
      <td></td>
      <td></td>
      <td>Citation</td>
      <td>158</td>
      <td>No</td>
      <td>48071</td>
      <td>1</td>
    </tr>
    <tr>
      <td>1</td>
      <td>2016-02-01 00:04:00-05:00</td>
      <td>1</td>
      <td>Oakland</td>
      <td>Equipment Violation</td>
      <td>Unknown</td>
      <td>Male</td>
      <td>42</td>
      <td>None</td>
      <td></td>
      <td></td>
      <td>Citation</td>
      <td>225</td>
      <td>No</td>
      <td>48072</td>
      <td>3</td>
    </tr>
    <tr>
      <td>2</td>
      <td>2016-02-01 00:12:00-05:00</td>
      <td>3</td>
      <td>Other</td>
      <td>Equipment Violation</td>
      <td>African-American/Black</td>
      <td>Male</td>
      <td>31</td>
      <td>None</td>
      <td></td>
      <td></td>
      <td>Citation</td>
      <td>62</td>
      <td>No</td>
      <td>48073</td>
      <td>4</td>
    </tr>
    <tr>
      <td>3</td>
      <td>2016-02-01 00:37:00-05:00</td>
      <td>4</td>
      <td>Oakland</td>
      <td>Moving Violation</td>
      <td>African-American/Black</td>
      <td>Female</td>
      <td>21</td>
      <td>None</td>
      <td></td>
      <td></td>
      <td>Citation</td>
      <td>158</td>
      <td>No</td>
      <td>48074</td>
      <td>7</td>
    </tr>
    <tr>
      <td>4</td>
      <td>2016-02-01 00:40:00-05:00</td>
      <td>4</td>
      <td>Cedar</td>
      <td>Moving Violation</td>
      <td>White</td>
      <td>Female</td>
      <td>33</td>
      <td>None</td>
      <td></td>
      <td></td>
      <td>Warning</td>
      <td>124</td>
      <td>No</td>
      <td>48075</td>
      <td>8</td>
    </tr>
    <tr>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <td>20644</td>
      <td>2019-02-12 14:00:00-05:00</td>
      <td>1</td>
      <td>M.L. King Jr.</td>
      <td>Other</td>
      <td>African-American/Black</td>
      <td>Male</td>
      <td>42</td>
      <td>Driver</td>
      <td>Incident to Arrest</td>
      <td>Nothing</td>
      <td>Arrest</td>
      <td>89</td>
      <td>No</td>
      <td>73879</td>
      <td>20645</td>
    </tr>
    <tr>
      <td>20645</td>
      <td>2019-02-12 14:06:00-05:00</td>
      <td>1</td>
      <td>Saginaw</td>
      <td>Moving Violation</td>
      <td>White</td>
      <td>Male</td>
      <td>33</td>
      <td>None</td>
      <td></td>
      <td></td>
      <td>Warning</td>
      <td>202</td>
      <td>No</td>
      <td>73875</td>
      <td>20646</td>
    </tr>
    <tr>
      <td>20646</td>
      <td>2019-02-12 14:39:00-05:00</td>
      <td>1</td>
      <td>Other</td>
      <td>Registration</td>
      <td>Unknown</td>
      <td>Male</td>
      <td>28</td>
      <td>None</td>
      <td></td>
      <td></td>
      <td>Citation</td>
      <td>202</td>
      <td>No</td>
      <td>73877</td>
      <td>20647</td>
    </tr>
    <tr>
      <td>20647</td>
      <td>2019-02-12 16:02:00-05:00</td>
      <td>2</td>
      <td>Michigan</td>
      <td>Registration</td>
      <td>White</td>
      <td>Female</td>
      <td>26</td>
      <td>None</td>
      <td></td>
      <td></td>
      <td>Citation</td>
      <td>245</td>
      <td>No</td>
      <td>73881</td>
      <td>20648</td>
    </tr>
    <tr>
      <td>20648</td>
      <td>2019-02-12 19:36:00-05:00</td>
      <td>2</td>
      <td>Michigan</td>
      <td>Equipment Violation</td>
      <td>African-American/Black</td>
      <td>Female</td>
      <td>23</td>
      <td>None</td>
      <td></td>
      <td>Nothing</td>
      <td>Warning</td>
      <td>149</td>
      <td>No</td>
      <td>73883</td>
      <td>20649</td>
    </tr>
  </tbody>
</table>
<p>20649 rows × 15 columns</p>
</div>



Add an "Hour" Column for later use


```python
traffic["Hour"] = traffic["Date"].dt.hour
```


```python
traffic.columns
```




    Index(['Date', 'Team_Area', 'Street_Area', 'Reason_for_Stop', 'Race_Ethnicity',
           'Gender', 'Age', 'Search_Performed', 'Search_Authority',
           'Discovery_If_Searched', 'Result_of_Stop', 'Officer_Badge',
           'Traffic_Crash', 'Serial_Number', 'ObjectId', 'Hour'],
          dtype='object')



*Subsets of MATS report's Data*


```python
year16 = traffic[(traffic["Date"] >= '2016-02-12 00:00:00-05:00') & (traffic["Date"] <=
'2017-02-11 23:59:59-05:00')]
year17 = traffic[(traffic["Date"] >= '2017-02-12 00:00:00-05:00') & (traffic["Date"] <=
'2018-02-11 23:59:59-05:00')]
#Not sure why they skipped 02-12-2018?
year18 = traffic[(traffic["Date"] >= '2018-02-13 00:00:00-05:00') & (traffic["Date"] <=
'2019-02-12 23:59:59-05:00')]
```

**Census Data**

Another issue with the census Data I have found is th the races are not clearly seperated. I cannot make 100% devided between the values of the traffic stop races and the races reported on the census. This is due to the fact that hispanic and latino is a seperate section in the census. There is a section called "Two or more races" that makes this hard to calculate. I will therefore only add together Under the Latino Section the single races plus latino and leave out the double races. This will hopefully evenly distribute the 6% that is more than one race.

The excel is included (census_calc.xlsx) that has all the numbers and calculations I used to get the percentages of each race and gender. What I am loading in is the csv that uses the same values from the calcualtion excel file, but minus all of the formulas for easy load in to pandas


```python
census = pd.read_csv("./ACS_data/census.csv")
census_2016 = census.iloc[0]
census_2017 = census.iloc[1]
census_2018 = census.iloc[2]
census_ALL = census.iloc[3]
census
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
      <th>Year</th>
      <th>Male</th>
      <th>Female</th>
      <th>White</th>
      <th>Black</th>
      <th>Native</th>
      <th>Asian_Pacific</th>
      <th>Hispanic</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>0</td>
      <td>2016</td>
      <td>0.479</td>
      <td>0.521</td>
      <td>0.593992</td>
      <td>0.228385</td>
      <td>0.004449</td>
      <td>0.041471</td>
      <td>0.131703</td>
    </tr>
    <tr>
      <td>1</td>
      <td>2017</td>
      <td>0.475</td>
      <td>0.525</td>
      <td>0.562655</td>
      <td>0.234408</td>
      <td>0.003599</td>
      <td>0.063806</td>
      <td>0.135532</td>
    </tr>
    <tr>
      <td>2</td>
      <td>2018</td>
      <td>0.462</td>
      <td>0.538</td>
      <td>0.576798</td>
      <td>0.234277</td>
      <td>0.003184</td>
      <td>0.037995</td>
      <td>0.147745</td>
    </tr>
    <tr>
      <td>3</td>
      <td>ALL</td>
      <td>0.480</td>
      <td>0.520</td>
      <td>0.590836</td>
      <td>0.230128</td>
      <td>0.004314</td>
      <td>0.039975</td>
      <td>0.134747</td>
    </tr>
  </tbody>
</table>
</div>



### Visualization Exploration of Data

**Analysis of Traffic Stops Overtime**
<a id='Date'></a>


```python
traffic.groupby("Hour").count()["Date"].plot(kind="bar")
```




    <matplotlib.axes._subplots.AxesSubplot at 0x2907411c9c8>




![png](/assets/images/traffic_stop/output_22_1.png)


Now looking at the MATS report for year 18 (February 13, 2018, through February 12, 2019).


```python
year18.groupby(traffic["Date"].dt.hour).count()["Date"].plot(kind="bar")
```




    <matplotlib.axes._subplots.AxesSubplot at 0x2907407e248>




![png](/assets/images/traffic_stop/output_24_1.png)


MATS report plot for comparison from Year 18

![](MATS_hour.PNG)

**Reason for Stop**
<a id='Reason_for_Stop'></a>


```python
traffic["Reason_for_Stop"].unique()
```




    array(['Equipment Violation', 'Moving Violation', 'Other', 'Registration',
           'Investigative Stop'], dtype=object)




```python
values, counts = np.unique(traffic["Reason_for_Stop"], return_counts=True)
```


```python
fig, ax = plt.subplots()
ax.barh(values,counts)
```




    <BarContainer object of 5 artists>




![png](/assets/images/traffic_stop/output_30_1.png)



```python
by_reason = traffic.groupby(by=["Hour","Reason_for_Stop"],as_index=False).count()
by_reason = by_reason.pivot(index="Hour",columns="Reason_for_Stop", values= "Date")
by_reason.plot(kind="bar",subplots=True,layout=(3,2),sharex=True, sharey=True,figsize=(10,7))
[traffic.groupby(by=["Hour"]).count()["Date"].plot(kind="bar", ax=ax,color="gray",alpha=0.3) for ax in plt.gcf().axes]
plt.tight_layout()
#plt.title("Reason for Traffic Stop by Hour")
```


![png](/assets/images/traffic_stop/output_31_0.png)


**Race/Ethnicity**
<a id='Race_Ethnicity'></a>


```python
traffic["Race_Ethnicity"].unique()
```




    array(['White', 'Unknown', 'African-American/Black',
           'Asian-Pacific Islander', 'Hispanic', 'Native American'],
          dtype=object)




```python
values, counts = np.unique(traffic["Race_Ethnicity"], return_counts=True)
fig, ax = plt.subplots()
ax.barh(values,counts)
```




    <BarContainer object of 6 artists>




![png](/assets/images/traffic_stop/output_34_1.png)



```python
counts_race = "{}:{} , {}:{} , {}:{} , {}:{} , {}:{} , {}:{}".format(values[0],counts[0],values[1],counts[1],values[2],counts[2],values[3],counts[3],values[4],counts[4],values[5],counts[5])
print(counts_race)
```

    African-American/Black:6728 , Asian-Pacific Islander:319 , Hispanic:1334 , Native American:11 , Unknown:2109 , White:10148
    


```python
races_total = {}
j=0
for i in values:
    races_total[i] = counts[j]
    j = j+1
races_total
```




    {'African-American/Black': 6728,
     'Asian-Pacific Islander': 319,
     'Hispanic': 1334,
     'Native American': 11,
     'Unknown': 2109,
     'White': 10148}




```python
race_percentage = {}
j=0
for i in values:
    if(i == "Unknown"):
        continue
    race_percentage[i] = counts[j]/len(traffic[traffic["Race_Ethnicity"] != "Unknown"])
    j = j+1
race_percentage
```




    {'African-American/Black': 0.362891046386192,
     'Asian-Pacific Islander': 0.01720604099244876,
     'Hispanic': 0.07195253505933118,
     'Native American': 0.0005933117583603021,
     'White': 0.11375404530744336}




```python
by_race = traffic.groupby(by=["Hour","Race_Ethnicity"],as_index=False).count()
by_race = by_race.pivot(index="Hour",columns="Race_Ethnicity", values= "Date")
by_race.plot(kind="bar",subplots=True,layout=(3,2),sharex=True, sharey=True,figsize=(10,7))
[traffic.groupby(by=["Hour"]).count()["Date"].plot(kind="bar", ax=ax,color="gray",alpha=0.3) for ax in plt.gcf().axes]
plt.tight_layout()
#plt.title("Race by Traffic Stop by Hour")
```


![png](/assets/images/traffic_stop/output_38_0.png)


**Gender**
<a id='Gender'></a>


```python
traffic["Gender"].unique()
```




    array(['Male', 'Female'], dtype=object)




```python
values, counts = np.unique(traffic["Gender"], return_counts=True)
fig, ax = plt.subplots()
ax.barh(values,counts)
```




    <BarContainer object of 2 artists>




![png](/assets/images/traffic_stop/output_41_1.png)



```python
by_gender = traffic.groupby(by=["Hour","Gender"],as_index=False).count()
by_gender = by_gender.pivot(index="Hour",columns="Gender", values= "Date")
by_gender.plot(kind="bar",subplots=True,layout=(1,2),sharex=True, sharey=True,figsize=(10,3))
[traffic.groupby(by=["Hour"]).count()["Date"].plot(kind="bar", ax=ax,color="gray",alpha=0.3) for ax in plt.gcf().axes]
plt.tight_layout()
```


![png](/assets/images/traffic_stop/output_42_0.png)


**Analysis of Age**
<a id='Age'></a>


```python
Sorted_age_indx = np.argsort(traffic["Age"].values)
```


```python
traffic["Age"][Sorted_age_indx].unique()
```




    array([-7167, -7154, -6181,  -977,  -955,     0,     1,     2,    11,
              12,    13,    14,    15,    16,    17,    18,    19,    20,
              21,    22,    23,    24,    25,    26,    27,    28,    29,
              30,    31,    32,    33,    34,    35,    36,    37,    38,
              39,    40,    41,    42,    43,    44,    45,    46,    47,
              48,    49,    50,    51,    52,    53,    54,    55,    56,
              57,    58,    59,    60,    61,    62,    63,    64,    65,
              66,    67,    68,    69,    70,    71,    72,    73,    74,
              75,    76,    77,    78,    79,    80,    81,    82,    83,
              84,    85,    86,    87,    88,    89,    90,    91,    92,
              93,   101,   120,   121], dtype=int64)



Ok Lets assume anyone lower than 14 is not correct, however, it could be true. I will just take 14 and up since typically you can start drivers training at 14 and 9 months in michigan. Also there is a jump after 93 to 101. It may be true that are drivers older than 93 however, I will exclude the data because it could be an error.


```python
Age_corrected_filter = ((traffic["Age"] > 13) & (traffic["Age"] < 94))
traffic["Age"][Sorted_age_indx][Age_corrected_filter].unique()
```




    array([14, 15, 16, 17, 18, 19, 20, 21, 22, 23, 24, 25, 26, 27, 28, 29, 30,
           31, 32, 33, 34, 35, 36, 37, 38, 39, 40, 41, 42, 43, 44, 45, 46, 47,
           48, 49, 50, 51, 52, 53, 54, 55, 56, 57, 58, 59, 60, 61, 62, 63, 64,
           65, 66, 67, 68, 69, 70, 71, 72, 73, 74, 75, 76, 77, 78, 79, 80, 81,
           82, 83, 84, 85, 86, 87, 88, 89, 90, 91, 92, 93], dtype=int64)




```python
traffic["Age"][Age_corrected_filter].hist(bins=80,width=1)
```




    <matplotlib.axes._subplots.AxesSubplot at 0x29074c7bd48>




![png](/assets/images/traffic_stop/output_48_1.png)



```python

```

**Search Performed**
<a id='Search_Performed'></a>


```python
traffic['Search_Performed'].unique()
```




    array(['None', 'Driver', 'Vehicle', nan, 'Passenger'], dtype=object)




```python
search_p_clean = traffic[~traffic["Search_Performed"].isnull()]
```


```python
# Will need to look into values, probably something to do with the nan value
values, counts = np.unique(search_p_clean["Search_Performed"], return_counts=True)
fig, ax = plt.subplots()
ax.barh(values,counts)
```




    <BarContainer object of 4 artists>




![png](/assets/images/traffic_stop/output_53_1.png)



```python
by_door = search_p_clean.groupby(by=["Hour","Search_Performed"],as_index=False).count()
by_door = by_door.pivot(index="Hour",columns="Search_Performed", values= "Date")
by_door.plot(kind="bar",subplots=True,layout=(2,2),sharex=True, sharey=True,figsize=(10,5))
[search_p_clean.groupby(by=["Hour"]).count()["Date"].plot(kind="bar", ax=ax,color="gray",alpha=0.3) for ax in plt.gcf().axes]
plt.tight_layout()
```


![png](/assets/images/traffic_stop/output_54_0.png)



```python
search_p_positive_clean = search_p_clean[search_p_clean["Search_Performed"] != "None"]
```


```python
by_door = search_p_positive_clean.groupby(by=["Hour","Search_Performed"],as_index=False).count()
by_door = by_door.pivot(index="Hour",columns="Search_Performed", values= "Date")
by_door.plot(kind="bar",subplots=True,layout=(2,2),sharex=True, sharey=True,figsize=(10,5))
[search_p_positive_clean.groupby(by=["Hour"]).count()["Date"].plot(kind="bar", ax=ax,color="gray",alpha=0.3) for ax in plt.gcf().axes]
plt.tight_layout()
```


![png](/assets/images/traffic_stop/output_56_0.png)


**Search Authority**
<a id='Search_Authority'></a>


```python
traffic['Search_Authority'].unique()
```




    array(['      ', 'Incident to Arrest', 'Terry Cursory', 'Consent',
           'Plain View', 'Tow Inventory', 'Parole/Probation'], dtype=object)




```python
print("Total number not searched",len(traffic[traffic["Search_Authority"] == '      ']))
print("Total number searched",len(traffic[traffic["Search_Authority"] != '      ']))
```

    Total number not searched 18748
    Total number searched 1901
    


```python
search_clean = traffic[traffic["Search_Authority"] != '      ']
```


```python
values, counts = np.unique(search_clean["Search_Authority"], return_counts=True)
fig, ax = plt.subplots()
ax.barh(values,counts)
```




    <BarContainer object of 6 artists>




![png](/assets/images/traffic_stop/output_61_1.png)



```python
by_search = search_clean.groupby(by=["Hour","Search_Authority"],as_index=False).count()
by_search = by_search.pivot(index="Hour",columns="Search_Authority", values= "Date")
by_search.plot(kind="bar",subplots=True,layout=(3,2),sharex=True, sharey=True,figsize=(10,7))
[search_clean.groupby(by=["Hour"]).count()["Date"].plot(kind="bar", ax=ax,color="gray",alpha=0.3) for ax in plt.gcf().axes]
plt.tight_layout()
```


![png](/assets/images/traffic_stop/output_62_0.png)


**Discovery If Searched**
<a id='Discovery_If_Searched'></a>


```python
traffic['Discovery_If_Searched'].unique()
```




    array(['       ', 'Nothing', 'Alcohol', 'Drugs', 'Drugs/ Alcohol', 'Cash',
           'Other Property', 'Drugs/ Cash', 'Weapon/ Drugs', 'Weapon',
           'Vehicle/ Drugs', 'Drugs/ Other Property', 'Cash/ Other Property',
           'Vehicle/ Alcohol/ Cash', 'Drugs/ Alcohol/ Cash/ Other Property',
           'Vehicle/ Alcohol', 'Vehicle', 'Weapon/ Drugs/ Alcohol',
           'Weapon/ Drugs/ Cash', 'Weapon/ Vehicle', 'Weapon/ Vehicle/ Cash',
           'Weapons/ Drugs/ Other Property', 'Weapon/ Drugs/ Other Property',
           'Vehicle/ Other Property', 'Vehicle/ Drugs/ Cash',
           'Vehicle/ Drugs/ Cash/ Other Property',
           'Weapon/ Vehicle/ Drugs/ Cash', 'Vehicle/ Cash/ Other Property',
           'Drugs/ Alcohol/ Cash', 'Weapon/ Other Property',
           'Alcohol/ Other Property', 'Alcohol/ Cash'], dtype=object)




```python
print("Total number not searched",len(traffic[traffic["Discovery_If_Searched"] == '       ']))
print("Total number searched",len(traffic[traffic["Discovery_If_Searched"] != '       ']))
print("Total number searched, but nothing found",len(traffic[(traffic["Discovery_If_Searched"] != '       ') & (traffic["Discovery_If_Searched"] == 'Nothing')]))
print("Total number searched and discovery made",len(traffic[(traffic["Discovery_If_Searched"] != '       ') & (traffic["Discovery_If_Searched"] != 'Nothing')]))
```

    Total number not searched 18512
    Total number searched 2137
    Total number searched, but nothing found 1547
    Total number searched and discovery made 590
    


```python
discovery_clean = traffic[traffic["Discovery_If_Searched"] != '       ']
```


```python
values, counts = np.unique(discovery_clean['Discovery_If_Searched'], return_counts=True)
fig, ax = plt.subplots(figsize=(5,10))
ax.barh(values,counts)
```




    <BarContainer object of 31 artists>




![png](/assets/images/traffic_stop/output_67_1.png)



```python
counts
```




    array([  95,    1,    1,   20,   10,  216,   17,    1,    1,   12,   10,
           1547,   66,   41,    4,    1,    3,   14,    6,    1,    2,   36,
             19,    5,    2,    1,    1,    1,    1,    1,    1], dtype=int64)




```python
by_discovery = discovery_clean.groupby(by=["Hour","Discovery_If_Searched"],as_index=False).count()
by_discovery = by_discovery.pivot(index="Hour",columns="Discovery_If_Searched", values= "Date")
by_discovery.plot(kind="bar",subplots=True,layout=(16,2),sharex=True, sharey=True,figsize=(10,40))
[discovery_clean.groupby(by=["Hour"]).count()["Date"].plot(kind="bar", ax=ax,color="gray",alpha=0.3) for ax in plt.gcf().axes]
plt.tight_layout()
```


![png](/assets/images/traffic_stop/output_69_0.png)



```python
discovery_clean_positive = traffic[(traffic["Discovery_If_Searched"] != '       ') & (traffic["Discovery_If_Searched"] != 'Nothing')]
values, counts = np.unique(discovery_clean_positive['Discovery_If_Searched'], return_counts=True)
fig, ax = plt.subplots(figsize=(5,10))
ax.barh(values,counts)
```




    <BarContainer object of 30 artists>




![png](/assets/images/traffic_stop/output_70_1.png)



```python
by_discovery_positive = discovery_clean_positive.groupby(by=["Hour","Discovery_If_Searched"],as_index=False).count()
by_discovery_positive = by_discovery_positive.pivot(index="Hour",columns="Discovery_If_Searched", values= "Date")
by_discovery_positive.plot(kind="bar",subplots=True,layout=(16,2),sharex=True, sharey=True,figsize=(10,40))
[discovery_clean_positive.groupby(by=["Hour"]).count()["Date"].plot(kind="bar", ax=ax,color="gray",alpha=0.3) for ax in plt.gcf().axes]
plt.tight_layout()
```


![png](/assets/images/traffic_stop/output_71_0.png)


**Result of Stop**
<a id='Result_of_Stop'></a>


```python
traffic['Result_of_Stop'].unique()
```




    array(['Citation', 'Warning', 'Arrest', '    ', 'Report'], dtype=object)




```python
values, counts = np.unique(traffic["Result_of_Stop"], return_counts=True)
fig, ax = plt.subplots()
ax.barh(values,counts)
```




    <BarContainer object of 5 artists>




![png](/assets/images/traffic_stop/output_74_1.png)



```python
by_result = traffic.groupby(by=["Hour","Result_of_Stop"],as_index=False).count()
by_result = by_result.pivot(index="Hour",columns="Result_of_Stop", values= "Date")
by_result.plot(kind="bar",subplots=True,layout=(3,2),sharex=True, sharey=True,figsize=(10,7.5))
[traffic.groupby(by=["Hour"]).count()["Date"].plot(kind="bar", ax=ax,color="gray",alpha=0.3) for ax in plt.gcf().axes]
plt.tight_layout()
```


![png](/assets/images/traffic_stop/output_75_0.png)


**Officer Badge**
<a id='Officer_Badge'></a>


```python
traffic['Officer_Badge'].unique()
```




    array([158, 225,  62, 124, 152,  71,  50, 112, 337, 334, 132, 169, 390,
            35,  87,  44,  96, 220, 165,  74,  49,  97, 154,  82,  86, 143,
           176,   2,  89, 391, 215, 160,  37,  85, 197, 185,  52,  59,  28,
           222, 305,  40, 212, 211, 110, 442, 153,  64,  22,  84, 376,   0,
           118,   8, 312,  36, 345, 378, 140,  63,  79, 100,  54, 177,   3,
           324, 339, 398,   7,  23, 319, 142, 344,  46, 128,  81, 317, 168,
           175,  21,  27, 201, 307,  67, 226, 164, 358, 146, 127, 351, 353,
           101, 347,  38, 355, 134, 230, 129, 309, 457,  47,  48, 326,  29,
            14,  66,  83, 188, 103,  94,  11, 136,  80,  34, 335, 122, 414,
           408, 181,  69,  75, 308,  12,  43,  57,  45, 108, 125, 145,  51,
            72, 117, 120, 182, 428, 354, 472, 245, 111, 123,  70, 130, 387,
            31,  53, 135,   6, 203, 106,  91, 139, 167,  90,  41,  99, 191,
           121, 196, 202, 131, 200,  76,  92,   4, 228,  16, 186, 126, 115,
           119, 151, 104, 150, 190, 149, 193, 137, 170, 148, 113], dtype=int64)




```python
Sorted_badge_indx = np.argsort(traffic['Officer_Badge'].values)
traffic['Officer_Badge'][Sorted_badge_indx].unique()
```




    array([  0,   2,   3,   4,   6,   7,   8,  11,  12,  14,  16,  21,  22,
            23,  27,  28,  29,  31,  34,  35,  36,  37,  38,  40,  41,  43,
            44,  45,  46,  47,  48,  49,  50,  51,  52,  53,  54,  57,  59,
            62,  63,  64,  66,  67,  69,  70,  71,  72,  74,  75,  76,  79,
            80,  81,  82,  83,  84,  85,  86,  87,  89,  90,  91,  92,  94,
            96,  97,  99, 100, 101, 103, 104, 106, 108, 110, 111, 112, 113,
           115, 117, 118, 119, 120, 121, 122, 123, 124, 125, 126, 127, 128,
           129, 130, 131, 132, 134, 135, 136, 137, 139, 140, 142, 143, 145,
           146, 148, 149, 150, 151, 152, 153, 154, 158, 160, 164, 165, 167,
           168, 169, 170, 175, 176, 177, 181, 182, 185, 186, 188, 190, 191,
           193, 196, 197, 200, 201, 202, 203, 211, 212, 215, 220, 222, 225,
           226, 228, 230, 245, 305, 307, 308, 309, 312, 317, 319, 324, 326,
           334, 335, 337, 339, 344, 345, 347, 351, 353, 354, 355, 358, 376,
           378, 387, 390, 391, 398, 408, 414, 428, 442, 457, 472], dtype=int64)




```python
# Why is there some officers with a bunch?
values, counts = np.unique(traffic["Officer_Badge"], return_counts=True)
fig, ax = plt.subplots()
ax.barh(values,counts)
```




    <BarContainer object of 180 artists>




![png](/assets/images/traffic_stop/output_79_1.png)



```python
#Officers that have made over 100 stops
officer_100 = values[counts > 100]
```


```python
mask =[]
for row in traffic["Officer_Badge"]:
    if row in officer_100:
        mask.append(True)
    else:
        mask.append(False)
officer_top = traffic[mask]
```

### Questions

**Individuals who were searched vs not searched is there a link to demographics?** **/** 
<a id='Search'></a>
**Is there any demographic subjected to harsher punishment across traffic stop results?**
<a id='Harsh'></a>

*Disclaimer*

Due to the nature of the stops and the fact that there can be multiple autorites to arrest and search. The values are hard to correct if they are errored or not. For example there may be situations where there exist multiple columns that logically do not flow together (No search, but incident to arrest, and finding something, and no arrest). Thus no values will be purged as purging will result in data loss that may be not deserving of purging.

However, I will only be going off the accuracy if the table says the search was preformed. I will not edit the table if other rows seem like this answer should be yes.

Also this would be best served as a combined visualization. I do not have it made yet, however, the idea is that I will create a count for each column under the "Search" and make a giant heat map with a diverging colormap from the census data.


```python
traffic.columns
```




    Index(['Date', 'Team_Area', 'Street_Area', 'Reason_for_Stop', 'Race_Ethnicity',
           'Gender', 'Age', 'Search_Performed', 'Search_Authority',
           'Discovery_If_Searched', 'Result_of_Stop', 'Officer_Badge',
           'Traffic_Crash', 'Serial_Number', 'ObjectId', 'Hour'],
          dtype='object')




```python
print(counts_race)
```

    African-American/Black:6728 , Asian-Pacific Islander:319 , Hispanic:1334 , Native American:11 , Unknown:2109 , White:10148
    


```python

```


```python
census_race = [census_ALL["White"],census_ALL["Black"],census_ALL["Hispanic"],census_ALL["Asian_Pacific"],census_ALL["Native"]]
census_race_mask = [census_race for i in range(5)]
census_race_mask
```




    [[0.5908361910000001,
      0.230127567,
      0.13474726199999998,
      0.039975298,
      0.004313682],
     [0.5908361910000001,
      0.230127567,
      0.13474726199999998,
      0.039975298,
      0.004313682],
     [0.5908361910000001,
      0.230127567,
      0.13474726199999998,
      0.039975298,
      0.004313682],
     [0.5908361910000001,
      0.230127567,
      0.13474726199999998,
      0.039975298,
      0.004313682],
     [0.5908361910000001,
      0.230127567,
      0.13474726199999998,
      0.039975298,
      0.004313682]]




```python
census_race_mask = [census_race for i in range(5)]
by_result = traffic.groupby(by=["Race_Ethnicity","Result_of_Stop"],as_index=False).count()
by_result = by_result.pivot(index="Race_Ethnicity",columns="Result_of_Stop", values= "Date")
by_result = by_result.drop(index="Unknown")
total_mask=by_result.sum().values
by_result = by_result.reindex(["White", "African-American/Black", "Hispanic","Asian-Pacific Islander","Native American"])
by_result = by_result.rename(columns={'    ':"None"})
by_result_comp = (by_result/total_mask)#-census_race_mask
by_result_comp
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
      <th>Result_of_Stop</th>
      <th>None</th>
      <th>Arrest</th>
      <th>Citation</th>
      <th>Report</th>
      <th>Warning</th>
    </tr>
    <tr>
      <th>Race_Ethnicity</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>White</td>
      <td>0.491525</td>
      <td>0.340153</td>
      <td>0.561575</td>
      <td>0.411765</td>
      <td>0.517007</td>
    </tr>
    <tr>
      <td>African-American/Black</td>
      <td>0.355932</td>
      <td>0.570332</td>
      <td>0.352537</td>
      <td>0.470588</td>
      <td>0.379906</td>
    </tr>
    <tr>
      <td>Hispanic</td>
      <td>0.135593</td>
      <td>0.084399</td>
      <td>0.068627</td>
      <td>0.058824</td>
      <td>0.082156</td>
    </tr>
    <tr>
      <td>Asian-Pacific Islander</td>
      <td>NaN</td>
      <td>0.005115</td>
      <td>0.016911</td>
      <td>0.058824</td>
      <td>0.019623</td>
    </tr>
    <tr>
      <td>Native American</td>
      <td>0.016949</td>
      <td>NaN</td>
      <td>0.000351</td>
      <td>NaN</td>
      <td>0.001308</td>
    </tr>
  </tbody>
</table>
</div>




```python
census_race_mask = [census_race for i in range(5)]
by_reason = traffic.groupby(by=["Race_Ethnicity","Reason_for_Stop"],as_index=False).count()
by_reason = by_reason.pivot(index="Race_Ethnicity",columns="Reason_for_Stop", values= "Date")
by_reason = by_reason.drop(index="Unknown")
total_mask=by_reason.sum().values
by_reason = by_reason.reindex(["White", "African-American/Black", "Hispanic","Asian-Pacific Islander","Native American"])
by_reason_comp = (by_reason/total_mask)#-census_race_mask
by_reason_comp#.plot()
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
      <th>Reason_for_Stop</th>
      <th>Equipment Violation</th>
      <th>Investigative Stop</th>
      <th>Moving Violation</th>
      <th>Other</th>
      <th>Registration</th>
    </tr>
    <tr>
      <th>Race_Ethnicity</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>White</td>
      <td>0.482554</td>
      <td>0.520436</td>
      <td>0.567977</td>
      <td>0.372263</td>
      <td>0.556291</td>
    </tr>
    <tr>
      <td>African-American/Black</td>
      <td>0.423077</td>
      <td>0.408719</td>
      <td>0.338536</td>
      <td>0.558394</td>
      <td>0.369915</td>
    </tr>
    <tr>
      <td>Hispanic</td>
      <td>0.079699</td>
      <td>0.064033</td>
      <td>0.072651</td>
      <td>0.060219</td>
      <td>0.064333</td>
    </tr>
    <tr>
      <td>Asian-Pacific Islander</td>
      <td>0.013878</td>
      <td>0.006812</td>
      <td>0.020282</td>
      <td>0.005474</td>
      <td>0.009461</td>
    </tr>
    <tr>
      <td>Native American</td>
      <td>0.000793</td>
      <td>NaN</td>
      <td>0.000555</td>
      <td>0.003650</td>
      <td>NaN</td>
    </tr>
  </tbody>
</table>
</div>



I don't think how they were searched as much as the fact they were searched is important


```python
census_race_mask = [census_race for i in range(7)]
by_authority = traffic.groupby(by=["Race_Ethnicity","Search_Authority"],as_index=False).count()
by_authority = by_authority.pivot(index="Race_Ethnicity",columns="Search_Authority", values= "Date")
by_authority = by_authority.drop(index="Unknown")
total_mask=by_authority.sum().values
by_authority = by_authority.reindex(["White", "African-American/Black", "Hispanic","Asian-Pacific Islander","Native American"])
by_authority_comp = (by_authority/total_mask)-census_race_mask
by_authority_comp#.plot()
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
      <th>Search_Authority</th>
      <th></th>
      <th>Consent</th>
      <th>Incident to Arrest</th>
      <th>Parole/Probation</th>
      <th>Plain View</th>
      <th>Terry Cursory</th>
      <th>Tow Inventory</th>
    </tr>
    <tr>
      <th>Race_Ethnicity</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>White</td>
      <td>-0.020120</td>
      <td>-0.262760</td>
      <td>-0.266736</td>
      <td>-0.340836</td>
      <td>-0.322086</td>
      <td>-0.344459</td>
      <td>-0.313058</td>
    </tr>
    <tr>
      <td>African-American/Black</td>
      <td>0.108231</td>
      <td>0.356623</td>
      <td>0.372827</td>
      <td>0.269872</td>
      <td>0.419872</td>
      <td>0.451032</td>
      <td>0.380984</td>
    </tr>
    <tr>
      <td>Hispanic</td>
      <td>-0.062874</td>
      <td>-0.049574</td>
      <td>-0.066419</td>
      <td>0.115253</td>
      <td>-0.065997</td>
      <td>-0.062283</td>
      <td>-0.060673</td>
    </tr>
    <tr>
      <td>Asian-Pacific Islander</td>
      <td>-0.021517</td>
      <td>NaN</td>
      <td>-0.036282</td>
      <td>NaN</td>
      <td>-0.027475</td>
      <td>NaN</td>
      <td>-0.002938</td>
    </tr>
    <tr>
      <td>Native American</td>
      <td>-0.003720</td>
      <td>NaN</td>
      <td>-0.003390</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
  </tbody>
</table>
</div>




```python
search = by_authority[["Consent","Incident to Arrest","Parole/Probation","Plain View","Terry Cursory","Tow Inventory"]].sum(axis=1)
no_search = by_authority['      ']
A_search_nosearch = pd.DataFrame([no_search,search])
A_search_nosearch = A_search_nosearch.T.rename(columns={'      ': "Not_Searched","Unnamed 0": "Searched"})

total_mask=A_search_nosearch.sum().values
census_race_mask = [census_race for i in range(2)]
A_search_nosearch_comp = (A_search_nosearch/total_mask)#-census_race_mask
A_search_nosearch
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
      <th>Not_Searched</th>
      <th>Searched</th>
    </tr>
    <tr>
      <th>Race_Ethnicity</th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>White</td>
      <td>9616.0</td>
      <td>532.0</td>
    </tr>
    <tr>
      <td>African-American/Black</td>
      <td>5701.0</td>
      <td>1027.0</td>
    </tr>
    <tr>
      <td>Hispanic</td>
      <td>1211.0</td>
      <td>123.0</td>
    </tr>
    <tr>
      <td>Asian-Pacific Islander</td>
      <td>311.0</td>
      <td>8.0</td>
    </tr>
    <tr>
      <td>Native American</td>
      <td>10.0</td>
      <td>1.0</td>
    </tr>
  </tbody>
</table>
</div>



I don't think what was discoverd is as important as if they discovered anything


```python
traffic["Discovery_If_Searched"].unique()
```




    array(['       ', 'Nothing', 'Alcohol', 'Drugs', 'Drugs/ Alcohol', 'Cash',
           'Other Property', 'Drugs/ Cash', 'Weapon/ Drugs', 'Weapon',
           'Vehicle/ Drugs', 'Drugs/ Other Property', 'Cash/ Other Property',
           'Vehicle/ Alcohol/ Cash', 'Drugs/ Alcohol/ Cash/ Other Property',
           'Vehicle/ Alcohol', 'Vehicle', 'Weapon/ Drugs/ Alcohol',
           'Weapon/ Drugs/ Cash', 'Weapon/ Vehicle', 'Weapon/ Vehicle/ Cash',
           'Weapons/ Drugs/ Other Property', 'Weapon/ Drugs/ Other Property',
           'Vehicle/ Other Property', 'Vehicle/ Drugs/ Cash',
           'Vehicle/ Drugs/ Cash/ Other Property',
           'Weapon/ Vehicle/ Drugs/ Cash', 'Vehicle/ Cash/ Other Property',
           'Drugs/ Alcohol/ Cash', 'Weapon/ Other Property',
           'Alcohol/ Other Property', 'Alcohol/ Cash'], dtype=object)




```python
census_race_mask = [census_race for i in range(32)]
by_discovery = traffic.groupby(by=["Race_Ethnicity",'Discovery_If_Searched'],as_index=False).count()
by_discovery = by_discovery.pivot(index="Race_Ethnicity",columns='Discovery_If_Searched', values= "Date")
by_discovery = by_discovery.drop(index="Unknown")
total_mask=by_discovery.sum().values
by_discovery = by_discovery.reindex(["White", "African-American/Black", "Hispanic","Asian-Pacific Islander","Native American"])
by_discovery_comp = (by_discovery/total_mask)-census_race_mask
by_discovery_comp
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
      <th>Discovery_If_Searched</th>
      <th></th>
      <th>Alcohol</th>
      <th>Alcohol/ Cash</th>
      <th>Alcohol/ Other Property</th>
      <th>Cash</th>
      <th>Cash/ Other Property</th>
      <th>Drugs</th>
      <th>Drugs/ Alcohol</th>
      <th>Drugs/ Alcohol/ Cash</th>
      <th>Drugs/ Alcohol/ Cash/ Other Property</th>
      <th>...</th>
      <th>Weapon</th>
      <th>Weapon/ Drugs</th>
      <th>Weapon/ Drugs/ Alcohol</th>
      <th>Weapon/ Drugs/ Cash</th>
      <th>Weapon/ Drugs/ Other Property</th>
      <th>Weapon/ Other Property</th>
      <th>Weapon/ Vehicle</th>
      <th>Weapon/ Vehicle/ Cash</th>
      <th>Weapon/ Vehicle/ Drugs/ Cash</th>
      <th>Weapons/ Drugs/ Other Property</th>
    </tr>
    <tr>
      <th>Race_Ethnicity</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>White</td>
      <td>-0.025718</td>
      <td>-0.268997</td>
      <td>0.409164</td>
      <td>NaN</td>
      <td>-0.473189</td>
      <td>-0.162265</td>
      <td>-0.277705</td>
      <td>-0.296719</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>...</td>
      <td>-0.215836</td>
      <td>-0.355542</td>
      <td>-0.390836</td>
      <td>0.409164</td>
      <td>0.409164</td>
      <td>0.409164</td>
      <td>NaN</td>
      <td>0.409164</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <td>African-American/Black</td>
      <td>0.113823</td>
      <td>0.379068</td>
      <td>NaN</td>
      <td>0.769872</td>
      <td>0.652225</td>
      <td>0.341301</td>
      <td>0.386034</td>
      <td>0.358108</td>
      <td>0.769872</td>
      <td>0.769872</td>
      <td>...</td>
      <td>0.238622</td>
      <td>0.475755</td>
      <td>0.369872</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>0.769872</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>0.769872</td>
    </tr>
    <tr>
      <td>Hispanic</td>
      <td>-0.062748</td>
      <td>-0.077276</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>-0.064040</td>
      <td>-0.017100</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>...</td>
      <td>0.021503</td>
      <td>-0.075924</td>
      <td>0.065253</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <td>Asian-Pacific Islander</td>
      <td>-0.021645</td>
      <td>-0.028481</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>0.960025</td>
      <td>NaN</td>
    </tr>
    <tr>
      <td>Native American</td>
      <td>-0.003713</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
  </tbody>
</table>
<p>5 rows × 32 columns</p>
</div>




```python
search = by_authority[["Consent","Incident to Arrest","Parole/Probation","Plain View","Terry Cursory","Tow Inventory"]].sum(axis=1)
discovery = by_discovery.drop(columns=['       ','Nothing']).sum(axis=1)
no_discovery = search - discovery
discovery_nodiscovery = pd.DataFrame([no_discovery,discovery])
discovery_nodiscovery = discovery_nodiscovery.T.rename(columns={0:"No_Discovery", 1:"Discovery"})

total_mask=discovery_nodiscovery.sum().values
census_race_mask = [census_race for i in range(2)]
discovery_nodiscovery_comp = (discovery_nodiscovery/total_mask)#-census_race_mask
discovery_nodiscovery
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
      <th>No_Discovery</th>
      <th>Discovery</th>
    </tr>
    <tr>
      <th>Race_Ethnicity</th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>White</td>
      <td>366.0</td>
      <td>166.0</td>
    </tr>
    <tr>
      <td>African-American/Black</td>
      <td>703.0</td>
      <td>324.0</td>
    </tr>
    <tr>
      <td>Hispanic</td>
      <td>87.0</td>
      <td>36.0</td>
    </tr>
    <tr>
      <td>Asian-Pacific Islander</td>
      <td>5.0</td>
      <td>3.0</td>
    </tr>
    <tr>
      <td>Native American</td>
      <td>1.0</td>
      <td>0.0</td>
    </tr>
  </tbody>
</table>
</div>




```python
by_result_comp.T
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
      <th>Race_Ethnicity</th>
      <th>White</th>
      <th>African-American/Black</th>
      <th>Hispanic</th>
      <th>Asian-Pacific Islander</th>
      <th>Native American</th>
    </tr>
    <tr>
      <th>Result_of_Stop</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>None</td>
      <td>0.491525</td>
      <td>0.355932</td>
      <td>0.135593</td>
      <td>NaN</td>
      <td>0.016949</td>
    </tr>
    <tr>
      <td>Arrest</td>
      <td>0.340153</td>
      <td>0.570332</td>
      <td>0.084399</td>
      <td>0.005115</td>
      <td>NaN</td>
    </tr>
    <tr>
      <td>Citation</td>
      <td>0.561575</td>
      <td>0.352537</td>
      <td>0.068627</td>
      <td>0.016911</td>
      <td>0.000351</td>
    </tr>
    <tr>
      <td>Report</td>
      <td>0.411765</td>
      <td>0.470588</td>
      <td>0.058824</td>
      <td>0.058824</td>
      <td>NaN</td>
    </tr>
    <tr>
      <td>Warning</td>
      <td>0.517007</td>
      <td>0.379906</td>
      <td>0.082156</td>
      <td>0.019623</td>
      <td>0.001308</td>
    </tr>
  </tbody>
</table>
</div>




```python
by_reason_comp.T
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
      <th>Race_Ethnicity</th>
      <th>White</th>
      <th>African-American/Black</th>
      <th>Hispanic</th>
      <th>Asian-Pacific Islander</th>
      <th>Native American</th>
    </tr>
    <tr>
      <th>Reason_for_Stop</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>Equipment Violation</td>
      <td>0.482554</td>
      <td>0.423077</td>
      <td>0.079699</td>
      <td>0.013878</td>
      <td>0.000793</td>
    </tr>
    <tr>
      <td>Investigative Stop</td>
      <td>0.520436</td>
      <td>0.408719</td>
      <td>0.064033</td>
      <td>0.006812</td>
      <td>NaN</td>
    </tr>
    <tr>
      <td>Moving Violation</td>
      <td>0.567977</td>
      <td>0.338536</td>
      <td>0.072651</td>
      <td>0.020282</td>
      <td>0.000555</td>
    </tr>
    <tr>
      <td>Other</td>
      <td>0.372263</td>
      <td>0.558394</td>
      <td>0.060219</td>
      <td>0.005474</td>
      <td>0.003650</td>
    </tr>
    <tr>
      <td>Registration</td>
      <td>0.556291</td>
      <td>0.369915</td>
      <td>0.064333</td>
      <td>0.009461</td>
      <td>NaN</td>
    </tr>
  </tbody>
</table>
</div>




```python
binary_comp = pd.concat([A_search_nosearch_comp, discovery_nodiscovery_comp], axis=1)
binary_comp
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
      <th>Not_Searched</th>
      <th>Searched</th>
      <th>No_Discovery</th>
      <th>Discovery</th>
    </tr>
    <tr>
      <th>Race_Ethnicity</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>White</td>
      <td>0.570716</td>
      <td>0.314607</td>
      <td>0.314974</td>
      <td>0.313800</td>
    </tr>
    <tr>
      <td>African-American/Black</td>
      <td>0.338358</td>
      <td>0.607333</td>
      <td>0.604991</td>
      <td>0.612476</td>
    </tr>
    <tr>
      <td>Hispanic</td>
      <td>0.071874</td>
      <td>0.072738</td>
      <td>0.074871</td>
      <td>0.068053</td>
    </tr>
    <tr>
      <td>Asian-Pacific Islander</td>
      <td>0.018458</td>
      <td>0.004731</td>
      <td>0.004303</td>
      <td>0.005671</td>
    </tr>
    <tr>
      <td>Native American</td>
      <td>0.000594</td>
      <td>0.000591</td>
      <td>0.000861</td>
      <td>0.000000</td>
    </tr>
  </tbody>
</table>
</div>




```python
# split into 4 different arrays for spacing
race = ["White", "African-American/Black","Hispanic","Asian-Pacific Islander","Native American"]

results_name = by_result_comp.T.index.values
results_values = by_result_comp.T.values

reasons_name = by_reason_comp.T.index.values
reasons_values = by_reason_comp.T.values

binary_name = binary_comp.T.index.values
binary_value = binary_comp.T.values

```


```python
#Got this code from https://matplotlib.org/3.1.0/gallery/images_contours_and_fields/image_annotated_heatmap.html#sphx-glr-gallery-images-contours-and-fields-image-annotated-heatmap-py
def heatmap(data, row_labels, col_labels, ax=None,colorbar=True, title= " ",
            cbar_kw={}, cbarlabel="", **kwargs):
    """
    Create a heatmap from a numpy array and two lists of labels.

    Parameters
    ----------
    data
        A 2D numpy array of shape (N, M).
    row_labels
        A list or array of length N with the labels for the rows.
    col_labels
        A list or array of length M with the labels for the columns.
    ax
        A `matplotlib.axes.Axes` instance to which the heatmap is plotted.  If
        not provided, use current axes or create a new one.  Optional.
    cbar_kw
        A dictionary with arguments to `matplotlib.Figure.colorbar`.  Optional.
    cbarlabel
        The label for the colorbar.  Optional.
    **kwargs
        All other arguments are forwarded to `imshow`.
    """

    if not ax:
        ax = plt.gca()

    # Plot the heatmap
    im = ax.imshow(data, **kwargs)
    
    
    # Create colorbar
    if(colorbar == True):
        cbar = ax.figure.colorbar(im, ax=ax, **cbar_kw)
        cbar.ax.set_ylabel(cbarlabel, rotation=-90, va="bottom")
    ax.set_xlabel(title,labelpad =10, fontsize=25)
    # We want to show all ticks...
    ax.set_xticks(np.arange(data.shape[1]))
    ax.set_yticks(np.arange(data.shape[0]))
    # ... and label them with the respective list entries.
    ax.set_xticklabels(col_labels, fontsize=22)
    ax.set_yticklabels(row_labels, fontsize=22)

    # Let the horizontal axes labeling appear on top.
    ax.tick_params(top=True, bottom=False,
                   labeltop=True, labelbottom=False)

    # Rotate the tick labels and set their alignment.
    plt.setp(ax.get_xticklabels(), rotation=-30, ha="right",
             rotation_mode="anchor")

    # Turn spines off and create white grid.
    for edge, spine in ax.spines.items():
        spine.set_visible(False)

    ax.set_xticks(np.arange(data.shape[1]+1)-.5, minor=True)
    ax.set_yticks(np.arange(data.shape[0]+1)-.5, minor=True)
    ax.grid(which="minor", color="w", linestyle='-', linewidth=3)
    ax.tick_params(which="minor", bottom=False, left=False)
    
    norm = matplotlib.colors.Normalize(vmin=0,vmax=0.61)
    norm(0.)
    
    if(colorbar == True):
        return im, cbar
    if(colorbar == False):
        return im


def annotate_heatmap(im, data=None, valfmt="{x:.2f}",
                     textcolors=["black", "white"],
                     threshold=None, **textkw):
    """
    A function to annotate a heatmap.

    Parameters
    ----------
    im
        The AxesImage to be labeled.
    data
        Data used to annotate.  If None, the image's data is used.  Optional.
    valfmt
        The format of the annotations inside the heatmap.  This should either
        use the string format method, e.g. "$ {x:.2f}", or be a
        `matplotlib.ticker.Formatter`.  Optional.
    textcolors
        A list or array of two color specifications.  The first is used for
        values below a threshold, the second for those above.  Optional.
    threshold
        Value in data units according to which the colors from textcolors are
        applied.  If None (the default) uses the middle of the colormap as
        separation.  Optional.
    **kwargs
        All other arguments are forwarded to each call to `text` used to create
        the text labels.
    """

    if not isinstance(data, (list, np.ndarray)):
        data = im.get_array()

    # Normalize the threshold to the images color range.
    if threshold is not None:
        threshold = im.norm(threshold)
    else:
        threshold = im.norm(data.max())/2.

    # Set default alignment to center, but allow it to be
    # overwritten by textkw.
    kw = dict(horizontalalignment="center",
              verticalalignment="center")
    kw.update(textkw)

    # Get the formatter in case a string is supplied
    if isinstance(valfmt, str):
        valfmt = matplotlib.ticker.StrMethodFormatter(valfmt)

    # Loop over the data and create a `Text` for each "pixel".
    # Change the text's color depending on the data.
    texts = []
    for i in range(data.shape[0]):
        for j in range(data.shape[1]):
            kw.update(color=textcolors[int(im.norm(data[i, j]) > threshold)])
            text = im.axes.text(j, i, valfmt(data[i, j], None), **kw)
            texts.append(text)

    return texts

fig, ((ax1,ax2,ax3)) = plt.subplots(1, 3, figsize=(22, 16))

im = heatmap(results_values, results_name, race,  ax=ax1,colorbar=False, title= "Result of Stop",
                   cmap='Greens', cbarlabel="Percentage per individual weapon")
annotate_heatmap(im,fontsize=17)

im = heatmap(reasons_values, reasons_name, race,  ax=ax2, colorbar=False, title= "Reason for Stop",
                   cmap='Greens', cbarlabel="Percentage per individual weapon")
annotate_heatmap(im,fontsize=17)

im = heatmap(binary_value, binary_name, race,  ax=ax3, colorbar=False, title= "Search Results",
                   cmap='Greens', cbarlabel="Percentage per individual weapon")
annotate_heatmap(im,fontsize=17)


fig.subplots_adjust(right=0.8)
cbar_ax = fig.add_axes([0.08, .2, 0.9, 0.04])
cbar = fig.colorbar(im, cax=cbar_ax,orientation="horizontal")
cbar.set_label("Percent of Traffic Stops", rotation=0,size=25, labelpad = 50)
cbar.ax.tick_params(labelsize=18)

fig.tight_layout()
plt.savefig("./outcomes.png", dpi=400,bbox_inches="tight", pad_inches=0,transparent=True)
plt.show()
```

    C:\ProgramData\Anaconda3\lib\site-packages\matplotlib\colors.py:933: UserWarning: Warning: converting a masked element to nan.
      dtype = np.min_scalar_type(value)
    C:\ProgramData\Anaconda3\lib\site-packages\numpy\ma\core.py:713: UserWarning: Warning: converting a masked element to nan.
      data = np.array(a, copy=False, subok=subok)
    C:\ProgramData\Anaconda3\lib\site-packages\ipykernel_launcher.py:150: UserWarning: This figure includes Axes that are not compatible with tight_layout, so results might be incorrect.
    


![png](/assets/images/traffic_stop/output_102_1.png)


**Is there a trend by Age of which certain demographics are pulled over?**
<a id='Street'></a>


```python
traffic["Age"][Age_corrected_filter].hist(bins=80,width=1)
```




    <matplotlib.axes._subplots.AxesSubplot at 0x29074ee3ec8>




![png](/assets/images/traffic_stop/output_104_1.png)



```python
traffic["Race_Ethnicity"].unique()
```




    array(['White', 'Unknown', 'African-American/Black',
           'Asian-Pacific Islander', 'Hispanic', 'Native American'],
          dtype=object)




```python
races = ['Native American','African-American/Black','Asian-Pacific Islander','Hispanic','White']

fig, ax = plt.subplots()

cmap = matplotlib.cm.get_cmap('Greens')
#styles = [(0, (3, 5, 1, 5, 1, 5)),'dashed', 'solid','dashdot',(0, (5, 5)),(0, (3, 5, 1, 5)),(0, (1, 1))]
colors = [cmap(2/7),cmap(3/7),cmap(4/7),cmap(5/7),cmap(6/7)]


# Iterate through the races
i=0
for race in races:
    # Subset to the race
    
    subset = traffic[traffic['Race_Ethnicity'] == race][Age_corrected_filter]
    
    
    # Draw the density plot
    sns.distplot(subset['Age'].dropna(), hist = False, kde = True, color = colors[i],
                 label = race,kde_kws={'linewidth':5})
    i = i +1

sns.distplot(traffic['Age'][Age_corrected_filter].dropna(), hist = False, kde = True, color = "black",
                 label = "All Races Together",kde_kws={'linewidth':3,'linestyle':'--','alpha':.5})

# Plot formatting
fig.set_size_inches(22.5, 14.5)
ax.spines['top'].set_visible(False)
ax.spines['right'].set_visible(False)
ax.spines['left'].set_visible(False)
plt.yticks([0.01,0.04], fontsize =35)
plt.xticks(np.arange(0,100,5),fontsize=35)
legend = plt.legend(title = 'Race',prop={'size': 35})
legend.get_title().set_fontsize('30')
plt.title('Age distribution of Traffic Stops by race \n (All races same sample size)', pad = 50,fontdict = 
          {'fontsize': 45,
            'fontweight' : 3,
             'verticalalignment': 'top',
             'horizontalalignment': "center"})
plt.xlabel('Age (years)', fontsize = 40, labelpad= 30)
plt.grid(axis='x',linestyle='--',alpha = 0.5)
plt.ylabel("Density of Traffic Stops", rotation=90,fontsize = 40,labelpad=30)
plt.savefig("./Age_by_race.png", dpi=400,bbox_inches="tight", pad_inches=0,transparent=True)
```

    C:\ProgramData\Anaconda3\lib\site-packages\ipykernel_launcher.py:15: UserWarning: Boolean Series key will be reindexed to match DataFrame index.
      from ipykernel import kernelapp as app
    C:\ProgramData\Anaconda3\lib\site-packages\ipykernel_launcher.py:15: UserWarning: Boolean Series key will be reindexed to match DataFrame index.
      from ipykernel import kernelapp as app
    C:\ProgramData\Anaconda3\lib\site-packages\ipykernel_launcher.py:15: UserWarning: Boolean Series key will be reindexed to match DataFrame index.
      from ipykernel import kernelapp as app
    C:\ProgramData\Anaconda3\lib\site-packages\ipykernel_launcher.py:15: UserWarning: Boolean Series key will be reindexed to match DataFrame index.
      from ipykernel import kernelapp as app
    C:\ProgramData\Anaconda3\lib\site-packages\ipykernel_launcher.py:15: UserWarning: Boolean Series key will be reindexed to match DataFrame index.
      from ipykernel import kernelapp as app
    


![png](/assets/images/traffic_stop/output_106_1.png)



```python

```

**Is there any officer that is pulling over a disproportionate percentage of individuals from
a given demographic?**
<a id='Officer'></a>


```python
percents = [census_ALL["Black"],census_ALL["White"],census_ALL["Asian_Pacific"],census_ALL["Hispanic"],census_ALL["Native"]]
percents = np.array(percents)*100
```


```python
by_officer = officer_top.groupby(by=["Race_Ethnicity","Officer_Badge"],as_index=False).count()
by_officer = by_officer.pivot(index="Race_Ethnicity",columns="Officer_Badge", values= "Date")
by_officer = by_officer.drop("Unknown", axis=0)
by_officer.reset_index()
for i in by_officer.columns:
    sum_c = by_officer[i].sum()
    for j in range(len(by_officer[i])):
        by_officer[i].iloc[j] = (by_officer[i].iloc[j]/sum_c)*100
officer_black = by_officer.T["African-American/Black"]
officer_asain = by_officer.T["Asian-Pacific Islander"]
officer_hispanic = by_officer.T["Hispanic"]
officer_native = by_officer.T["Native American"]
officer_white = by_officer.T["White"]
```


```python
print("Number of officers surveryed {}".format(len(by_officer.T)))
```

    Number of officers surveryed 43
    


```python
fig, (axes1,axes2) = plt.subplots(2,1,figsize=(13,17))

axes1.violinplot(dataset = [officer_black.dropna().values,
                           officer_white.dropna().values] )

axes1.set_title("Percentage of Race Stopped by Individual Officers",fontsize = 30)
axes1.spines['top'].set_visible(False)
axes1.spines['right'].set_visible(False)
axes1.spines['left'].set_visible(False)
axes1.spines['bottom'].set_visible(False)
axes1.tick_params(axis='both', which='major', labelsize=25)
axes1.yaxis.set_major_formatter(mtick.PercentFormatter())
#axes1.set_ylabel('Percentage of Population Stopped',labelpad =10,fontsize=25)
axes1.set_xticks(np.arange(1,3,1))
axes1.set_xticklabels(["African America/Black","White"],rotation=45)
axes1.scatter([1,2],percents[0:2],marker="D",s=100,color="Black",label = "Population Percentage \n from Census",alpha=0.5)
boxprops = dict(linestyle=' ', linewidth=0.1)
capprops=dict(linestyle=' ', linewidth=0.1)
medianprops=dict(linestyle='-', linewidth=2)
bp = by_officer.T[["African-American/Black","White"]].boxplot(figsize=(15,10),boxprops=boxprops,capprops=capprops,medianprops=medianprops,ax=axes1)
#axes1.legend(loc="upper left",fontsize=17)
axes1.grid(True,axis='y')
axes1.grid(False,axis='x')


axes2.violinplot(dataset = [officer_asain.dropna().values,
                           officer_hispanic.dropna().values,
                           officer_native.dropna().values] )

#axes2.set_title("Percentage of Race Stopped by Individual Officers",fontsize = 30)
axes2.spines['top'].set_visible(False)
axes2.spines['right'].set_visible(False)
axes2.spines['left'].set_visible(False)
axes2.spines['bottom'].set_visible(False)
axes2.tick_params(axis='both', which='major', labelsize=25)
axes2.yaxis.set_major_formatter(mtick.FormatStrFormatter('%.0f%%'))
#axes2.set_ylabel('Percentage of Population Stopped by individual officer (N = 43)',labelpad =10, fontsize=25)
fig.text(0, 0.5, "Percentage of Race Stopped in Individual Officer's Total Traffic Stops (N = 43)", va='center', rotation='vertical', fontsize=25)
axes2.set_xticks(np.arange(1,4,1))
axes2.set_xticklabels(["Asian-Pacific Islander","Hispanic","Native American"],rotation=45)
axes2.scatter([1,2,3],percents[2::],marker="D",s=100,color="Black",label = "Population Percentage \n from Census",alpha=0.5)
boxprops = dict(linestyle=' ', linewidth=0.1)
capprops=dict(linestyle=' ', linewidth=0.1)
medianprops=dict(linestyle='-', linewidth=2)
by_officer.T[["Asian-Pacific Islander","Hispanic","Native American"]].boxplot(figsize=(15,10),boxprops=boxprops,capprops=capprops,medianprops=medianprops,ax=axes2)
#xes2.grid(True,axis='y')
axes2.legend(loc="upper left",fontsize=17)
axes2.grid(False,axis='x')
plt.savefig("./Officer_Traffic_Race.png", dpi=400,bbox_inches="tight", pad_inches=0,transparent=True)
plt.show()
```


![png](/assets/images/traffic_stop/output_112_0.png)


**Are certain demographics more targeted per time of day of the traffic stop?**
<a id='Time'></a>

The last plot is the one I ended up using. This plot can be represented in many different ways, but I feel comparing it to the census gives the most imformation and observations of pattern


```python
traffic_corrected = traffic[traffic['Race_Ethnicity'] != "Unknown"]
```


```python
race_percentage
```




    {'African-American/Black': 0.362891046386192,
     'Asian-Pacific Islander': 0.01720604099244876,
     'Hispanic': 0.07195253505933118,
     'Native American': 0.0005933117583603021,
     'White': 0.11375404530744336}




```python
max_hour = traffic_corrected.groupby(by=["Hour"]).count()["Date"].values
white = abs(by_race["White"].values/max_hour)#-census_ALL["White"])
black = abs(by_race["African-American/Black"].values/max_hour)#-census_ALL["Black"])
asian = abs(by_race["Asian-Pacific Islander"].values/max_hour)#-census_ALL["Asian_Pacific"])
hispanic = abs(by_race["Hispanic"].values/max_hour)#-census_ALL["Hispanic"])
native = abs(by_race["Native American"].values/max_hour)#-census_ALL["Native"])

white_m = abs(white - race_percentage['White'])
black_m = abs(black - race_percentage['African-American/Black'])
asian_m = abs(asian - race_percentage['Asian-Pacific Islander'])
hispanic_m = abs(hispanic - race_percentage['Hispanic'])
native_m = abs(native - race_percentage['Native American'])


native_estimate_m=pd.DataFrame(native_m).fillna(method="bfill").values
asian_estimate_m=pd.DataFrame(asian_m).fillna(method="bfill").values
```


```python
by_race["White"].values/max_hour
```




    array([0.47021277, 0.4054878 , 0.46610169, 0.38461538, 0.41836735,
           0.31707317, 0.58706468, 0.61942959, 0.63086172, 0.60101243,
           0.56942102, 0.56384505, 0.5725938 , 0.55119215, 0.48765432,
           0.46764706, 0.48816029, 0.50173611, 0.50630631, 0.54291845,
           0.42950108, 0.44904815, 0.462     , 0.47295597])




```python
races = [black_m,native_m,asian_m,hispanic_m,white_m]

names=["African America/Black","Native American","Asian-Pacific Islander","Hispanic","White","Unkown"]

fig, ax = plt.subplots()
cmap = matplotlib.cm.get_cmap('Greens')
colors = [cmap(6/7),cmap(5/7),cmap(4/7),cmap(3/7),cmap(2/7)]

#plt.plot(np.arange(0,24,1),np.zeros(24),label="Population Percentage \n from Census",linestyle="--",color="Black",alpha=0.8)
plt.plot(np.arange(0,24,1),native_estimate_m*100,color=cmap(4/6),linestyle='--',linewidth=3)
#Plotting the estimated time of asain-pacific
plt.plot(np.arange(0,24,1),asian_estimate_m*100,color=cmap(3/6), linestyle = "--", linewidth=3)


#styles = [(0, (3, 5, 1, 5, 1, 5)),'dashed', 'solid','dashdot',(0, (5, 5)),(0, (3, 5, 1, 5)),(0, (1, 1))]


# Iterate through the races
i=0
for race in races:
    # Subset to the rac
    
    # Draw the density plot
    plt.plot(race*100,label = names[i], linewidth = 3,color = colors[i])
    i = i +1
    

# Plot formatting
fig.set_size_inches(18.5, 10.5)
ax.spines['top'].set_visible(False)
ax.spines['right'].set_visible(False)
ax.spines['left'].set_visible(False)
ax.tick_params(axis='both', which='major', labelsize=30)
ax.yaxis.set_major_formatter(mtick.PercentFormatter())
plt.xticks(np.arange(0,24,1))
plt.legend(bbox_to_anchor=(0.5, -0.05),prop={'size': 16}, title = 'Race')
plt.title('Traffic Stops by Hour compared to race', pad = 50,fontdict = 
          {'fontsize': 35,
            'fontweight' : 3,
             'verticalalignment': 'top',
             'horizontalalignment': "right"})
plt.xlabel('Hours (24hr Time)', fontsize = 30,labelpad=50)
plt.grid(axis='x',linestyle='--',alpha = 0.5)
plt.ylabel("Difference from Mean Percentage", rotation=90,fontsize = 30,labelpad=50)



#Plotting the esitamted time of Native American


plt.savefig("./Race_per_hour_abs.png", dpi=400,bbox_inches="tight", pad_inches=0)

```


![png](/assets/images/traffic_stop/output_119_0.png)



```python
max_hour = traffic_corrected.groupby(by=["Hour"]).count()["Date"].values
white = abs(by_race["White"].values/max_hour)#-census_ALL["White"])
black = abs(by_race["African-American/Black"].values/max_hour)#-census_ALL["Black"])
asian = abs(by_race["Asian-Pacific Islander"].values/max_hour)#-census_ALL["Asian_Pacific"])
hispanic = abs(by_race["Hispanic"].values/max_hour)#-census_ALL["Hispanic"])
native = abs(by_race["Native American"].values/max_hour)#-census_ALL["Native"])

white_m = white - race_percentage['White']
black_m = black - race_percentage['African-American/Black']
asian_m = asian - race_percentage['Asian-Pacific Islander']
hispanic_m = hispanic - race_percentage['Hispanic']
native_m = native - race_percentage['Native American']


native_estimate_m=pd.DataFrame(native_m.copy()).fillna(method="bfill").values.flatten()
asian_estimate_m=pd.DataFrame(asian_m.copy()).fillna(method="bfill").values.flatten()
```


```python
races = [black_m,native_m,asian_m,hispanic_m,white_m]

names=["African America/Black","Native American","Asian-Pacific Islander","Hispanic","White"]

fig, ax = plt.subplots()
cmap = matplotlib.cm.get_cmap('Greens')
colors = [cmap(6/7),cmap(5/7),cmap(4/7),cmap(3/7),cmap(2/7)]

#plt.plot(np.arange(0,24,1),np.zeros(24),label="Population Percentage \n from Census",linestyle="--",color="Black",alpha=0.8)
plt.plot(np.arange(0,24,1),native_estimate_m*100,color=cmap(4/6),linestyle='--',linewidth=3)
#Plotting the estimated time of asain-pacific
plt.plot(np.arange(0,24,1),asian_estimate_m*100,color=cmap(3/6), linestyle = "--", linewidth=3)


#styles = [(0, (3, 5, 1, 5, 1, 5)),'dashed', 'solid','dashdot',(0, (5, 5)),(0, (3, 5, 1, 5)),(0, (1, 1))]


# Iterate through the races
i=0
for race in races:
    # Subset to the rac
    
    # Draw the density plot
    plt.plot(race*100,label = names[i], linewidth = 3,color = colors[i])
    i = i +1
    

# Plot formatting
fig.set_size_inches(18.5, 10.5)
ax.spines['top'].set_visible(False)
ax.spines['right'].set_visible(False)
ax.spines['left'].set_visible(False)
ax.tick_params(axis='both', which='major', labelsize=20)
ax.yaxis.set_major_formatter(mtick.PercentFormatter())
plt.xticks(np.arange(0,24,1))
plt.legend(bbox_to_anchor=(1.00, 1.0),prop={'size': 16}, title = 'Race')
plt.title('Traffic Stops by Hour compared to race', pad = 50,fontdict = 
          {'fontsize': 30,
            'fontweight' : 3,
             'verticalalignment': 'top',
             'horizontalalignment': "right"})
plt.xlabel('Hours (24hr Time)', fontsize = 25,labelpad=50)
plt.grid(axis='x',linestyle='--',alpha = 0.5)
plt.ylabel("Difference from Mean Percentage", rotation=90,fontsize = 25,labelpad=50)



#Plotting the esitamted time of Native American


plt.savefig("./Race_per_hour.png", dpi=400,bbox_inches="tight", pad_inches=0)

```


![png](/assets/images/traffic_stop/output_121_0.png)



```python
asian = by_race["Asian-Pacific Islander"]/max_hour-census_ALL["Asian_Pacific"]
native = by_race["Native American"]/max_hour-census_ALL["Native"]
native_estimate=abs(native.fillna(method="bfill").values)
asian_estimate=abs(asian.fillna(method="bfill").values)

max_hour = traffic_corrected.groupby(by=["Hour"]).count()["Date"].values
white = abs(by_race["White"].values/max_hour-census_ALL["White"])
black = abs(by_race["African-American/Black"].values/max_hour-census_ALL["Black"])
asian = abs(by_race["Asian-Pacific Islander"].values/max_hour-census_ALL["Asian_Pacific"])
hispanic = abs(by_race["Hispanic"].values/max_hour-census_ALL["Hispanic"])
native = abs(by_race["Native American"].values/max_hour-census_ALL["Native"])

#white_m = abs(white - white.mean())
#black_m = abs(black - black.mean())
#asain_m = abs(asain - np.nanmean(asain))
#hispanic_m = abs(hispanic - hispanic.mean())
#native_m = abs(native - np.nanmean(native))


#native_estimate_m = abs(native_estimate - np.nanmean(native))
#asian_estimate_m = abs(asain_estimate - np.nanmean(asain))
```


```python
races = [black,native,asian,hispanic,white]

names=["African America/Black","Native American","Asian-Pacific Islander","Hispanic","White","Unkown"]

fig, ax = plt.subplots()
cmap = matplotlib.cm.get_cmap('Greens')
colors = [cmap(6/7),cmap(5/7),cmap(4/7),cmap(3/7),cmap(2/7)]

#plt.plot(np.arange(0,24,1),np.zeros(24),label="Population Percentage \n from Census",linestyle="--",color="Black",alpha=0.8)
plt.plot(np.arange(0,24,1),native_estimate*100,color=cmap(4/6),linestyle='--',linewidth=3)
#Plotting the estimated time of asain-pacific
plt.plot(np.arange(0,24,1),asian_estimate*100,color=cmap(3/6), linestyle = "--", linewidth=3)


#styles = [(0, (3, 5, 1, 5, 1, 5)),'dashed', 'solid','dashdot',(0, (5, 5)),(0, (3, 5, 1, 5)),(0, (1, 1))]


# Iterate through the races
i=0
for race in races:
    # Subset to the rac
    
    # Draw the density plot
    plt.plot(race*100,label = names[i], linewidth = 3,color = colors[i])
    i = i +1
    

# Plot formatting
fig.set_size_inches(18.5, 10.5)
ax.spines['top'].set_visible(False)
ax.spines['right'].set_visible(False)
ax.spines['left'].set_visible(False)
ax.tick_params(axis='both', which='major', labelsize=20)
ax.yaxis.set_major_formatter(mtick.PercentFormatter())
plt.xticks(np.arange(0,24,1))
plt.legend(bbox_to_anchor=(1.00, 1.0),prop={'size': 16}, title = 'Race')
plt.title('Traffic Stops by Hour compared to race', pad = 50,fontdict = 
          {'fontsize': 30,
            'fontweight' : 3,
             'verticalalignment': 'top',
             'horizontalalignment': "right"})
plt.xlabel('Hours (24hr Time)', fontsize = 25,labelpad=50)
plt.grid(axis='x',linestyle='--',alpha = 0.5)
plt.ylabel("Difference from Population Percentage", rotation=90,fontsize = 25,labelpad=50)



#Plotting the esitamted time of Native American


plt.savefig("./Race_per_hour_census_abs.png", dpi=400,bbox_inches="tight", pad_inches=0)

```


![png](/assets/images/traffic_stop/output_123_0.png)



```python
asian = by_race["Asian-Pacific Islander"]/max_hour-census_ALL["Asian_Pacific"]
native = by_race["Native American"]/max_hour-census_ALL["Native"]
native_estimate=native.fillna(method="bfill").values
asian_estimate=asian.fillna(method="bfill").values

max_hour = traffic_corrected.groupby(by=["Hour"]).count()["Date"].values
white = by_race["White"].values/max_hour-census_ALL["White"]
black = by_race["African-American/Black"].values/max_hour-census_ALL["Black"]
asian = by_race["Asian-Pacific Islander"].values/max_hour-census_ALL["Asian_Pacific"]
hispanic = by_race["Hispanic"].values/max_hour-census_ALL["Hispanic"]
native = by_race["Native American"].values/max_hour-census_ALL["Native"]

#white_m = abs(white - white.mean())
#black_m = abs(black - black.mean())
#asain_m = abs(asain - np.nanmean(asain))
#hispanic_m = abs(hispanic - hispanic.mean())
#native_m = abs(native - np.nanmean(native))


#native_estimate_m = abs(native_estimate - np.nanmean(native))
#asian_estimate_m = abs(asain_estimate - np.nanmean(asain))
```


```python
races = [black,native,asian,hispanic,white]

names=["African America/Black","Native American","Asian-Pacific Islander","Hispanic","White","Unkown"]

fig, ax = plt.subplots()
cmap = matplotlib.cm.get_cmap('Greens')
colors = [cmap(6/7),cmap(5/7),cmap(4/7),cmap(3/7),cmap(2/7)]

plt.plot(np.arange(0,24,1),np.zeros(24),label="Population Percentage \n from Census",linestyle="--",color="Black",alpha=0.8)
plt.plot(np.arange(0,24,1),native_estimate*100,color=cmap(4/6),linestyle='--',linewidth=3)
#Plotting the estimated time of asain-pacific
plt.plot(np.arange(0,24,1),asian_estimate*100,color=cmap(3/6), linestyle = "--", linewidth=3)


#styles = [(0, (3, 5, 1, 5, 1, 5)),'dashed', 'solid','dashdot',(0, (5, 5)),(0, (3, 5, 1, 5)),(0, (1, 1))]


# Iterate through the races
i=0
for race in races:
    # Subset to the rac
    
    # Draw the density plot
    plt.plot(race*100,label = names[i], linewidth = 6,color = colors[i])
    i = i +1
    

# Plot formatting
fig.set_size_inches(24.5, 10.5)
ax.spines['top'].set_visible(False)
ax.spines['right'].set_visible(False)
ax.spines['left'].set_visible(False)
ax.tick_params(axis='both', which='major', labelsize=25)
ax.yaxis.set_major_formatter(mtick.PercentFormatter())
plt.xticks(np.arange(0,24,1))
legend = plt.legend(loc='upper center',bbox_to_anchor=(0.5, -0.25),prop={'size': 25}, title = 'Race',ncol=3)
legend.get_title().set_fontsize('25')
plt.title('Traffic Stops by Hour compared to race', pad = 50,fontdict = 
          {'fontsize': 40,
            'fontweight' : 3,
             'verticalalignment': 'top'})
plt.xlabel('Hours (24hr Time)', fontsize = 35,labelpad=50)
plt.grid(axis='x',linestyle='--',alpha = 0.5)
plt.ylabel("Difference from Population Percentage", rotation=90,fontsize = 35,labelpad=50)



#Plotting the esitamted time of Native American


plt.savefig("./Race_per_hour_census.png", dpi=400,bbox_inches="tight", pad_inches=0,transparent=True)

```


![png](/assets/images/traffic_stop/output_125_0.png)

