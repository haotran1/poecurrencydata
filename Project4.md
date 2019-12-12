```python
## Project 4 - Interactive Crime Map of Baltimore
## ------------------------------------------------------------------------------------------------------------
## An interactive map of crime in Baltimore displaying all felonies commited in 2012. 
## Each point on the map shall be an individual felony in which the suspect was arrested. Each felony shall
## be assigned a different color in order to distinguish different types of felony. Clicking on any individual
## point shall show the arrest location, time, date, age, and sex of the suspect

```


```python
import folium
import requests
import pandas
import numpy as np
import math

arrest_table = pandas.read_csv("http://www.hcbravo.org/IntroDataSci/misc/BPD_Arrests.csv")

arrest_table = arrest_table[pandas.notnull(arrest_table["Location 1"])]

arrest_table["lat"], arrest_table["long"] = arrest_table["Location 1"].str.split(",").str
arrest_table["lat"] = arrest_table["lat"].str.replace("(", "").astype(float)
arrest_table["long"] = arrest_table["long"].str.replace(")", "").astype(float)
arrest_table
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
      <th>arrest</th>
      <th>age</th>
      <th>race</th>
      <th>sex</th>
      <th>arrestDate</th>
      <th>arrestTime</th>
      <th>arrestLocation</th>
      <th>incidentOffense</th>
      <th>incidentLocation</th>
      <th>charge</th>
      <th>chargeDescription</th>
      <th>district</th>
      <th>post</th>
      <th>neighborhood</th>
      <th>Location 1</th>
      <th>lat</th>
      <th>long</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>1</td>
      <td>11127013.0</td>
      <td>37</td>
      <td>B</td>
      <td>M</td>
      <td>01/01/2011</td>
      <td>00:01:00</td>
      <td>2000 Wilkens Ave</td>
      <td>79-Other</td>
      <td>Wilkens Av &amp; S Payson St</td>
      <td>1 1425</td>
      <td>Reckless Endangerment || Hand Gun Violation</td>
      <td>SOUTHERN</td>
      <td>934.0</td>
      <td>Carrollton Ridge</td>
      <td>(39.2814026274, -76.6483635135)</td>
      <td>39.281403</td>
      <td>-76.648364</td>
    </tr>
    <tr>
      <td>2</td>
      <td>11126887.0</td>
      <td>46</td>
      <td>B</td>
      <td>M</td>
      <td>01/01/2011</td>
      <td>00:01:00</td>
      <td>2800 Mayfield Ave</td>
      <td>Unknown Offense</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>Unknown Charge</td>
      <td>NORTHEASTERN</td>
      <td>415.0</td>
      <td>Belair-Edison</td>
      <td>(39.3227699160, -76.5735750473)</td>
      <td>39.322770</td>
      <td>-76.573575</td>
    </tr>
    <tr>
      <td>3</td>
      <td>11126873.0</td>
      <td>50</td>
      <td>B</td>
      <td>M</td>
      <td>01/01/2011</td>
      <td>00:04:00</td>
      <td>2100 Ashburton St</td>
      <td>79-Other</td>
      <td>2100 Ashburton St</td>
      <td>1 1106</td>
      <td>Reg Firearm:Illegal Possession || Hgv</td>
      <td>WESTERN</td>
      <td>735.0</td>
      <td>Panway/Braddish Avenue</td>
      <td>(39.3117196723, -76.6623546313)</td>
      <td>39.311720</td>
      <td>-76.662355</td>
    </tr>
    <tr>
      <td>4</td>
      <td>11126968.0</td>
      <td>33</td>
      <td>B</td>
      <td>M</td>
      <td>01/01/2011</td>
      <td>00:05:00</td>
      <td>4000 Wilsby Ave</td>
      <td>Unknown Offense</td>
      <td>1700 Aliceanna St</td>
      <td>NaN</td>
      <td>Unknown Charge</td>
      <td>NORTHERN</td>
      <td>525.0</td>
      <td>Pen Lucy</td>
      <td>(39.3382885254, -76.6045667070)</td>
      <td>39.338289</td>
      <td>-76.604567</td>
    </tr>
    <tr>
      <td>5</td>
      <td>11127041.0</td>
      <td>41</td>
      <td>B</td>
      <td>M</td>
      <td>01/01/2011</td>
      <td>00:05:00</td>
      <td>2900 Spellman Rd</td>
      <td>81-Recovered Property</td>
      <td>2900 Spelman Rd</td>
      <td>1 1425</td>
      <td>Reckless Endangerment || Handgun Violation</td>
      <td>SOUTHERN</td>
      <td>924.0</td>
      <td>Cherry Hill</td>
      <td>(39.2449886230, -76.6273582432)</td>
      <td>39.244989</td>
      <td>-76.627358</td>
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
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <td>104522</td>
      <td>13610388.0</td>
      <td>32</td>
      <td>B</td>
      <td>M</td>
      <td>12/31/2012</td>
      <td>23:00:00</td>
      <td>3300 Pulaski St</td>
      <td>79-Other</td>
      <td>3300 Pulaski Hw</td>
      <td>1 5212</td>
      <td>Handgun On Person || Handgun Violation</td>
      <td>SOUTHEASTERN</td>
      <td>223.0</td>
      <td>Ellwood Park/Monument</td>
      <td>(39.2958396988, -76.5712467336)</td>
      <td>39.295840</td>
      <td>-76.571247</td>
    </tr>
    <tr>
      <td>104523</td>
      <td>13610389.0</td>
      <td>27</td>
      <td>B</td>
      <td>M</td>
      <td>12/31/2012</td>
      <td>23:10:00</td>
      <td>400 W Baltimore St</td>
      <td>Unknown Offense</td>
      <td>NaN</td>
      <td>2 0055</td>
      <td>Fail Obey Renble/Lawfl || Disorderly/Fto</td>
      <td>CENTRAL</td>
      <td>113.0</td>
      <td>Downtown</td>
      <td>(39.2893323126, -76.6210021717)</td>
      <td>39.289332</td>
      <td>-76.621002</td>
    </tr>
    <tr>
      <td>104524</td>
      <td>13610377.0</td>
      <td>34</td>
      <td>B</td>
      <td>M</td>
      <td>12/31/2012</td>
      <td>23:30:00</td>
      <td>3800 Belair Rd</td>
      <td>4E-Common Assault</td>
      <td>3600 Belair Rd</td>
      <td>1 1415</td>
      <td>Asslt-Sec Degree || Assault</td>
      <td>NORTHEASTERN</td>
      <td>415.0</td>
      <td>Belair-Edison</td>
      <td>(39.3252613570, -76.5689030474)</td>
      <td>39.325261</td>
      <td>-76.568903</td>
    </tr>
    <tr>
      <td>104525</td>
      <td>13610383.0</td>
      <td>38</td>
      <td>B</td>
      <td>M</td>
      <td>12/31/2012</td>
      <td>23:40:00</td>
      <td>2000 Mckean Ave</td>
      <td>87-Narcotics</td>
      <td>1700 Mckean Av</td>
      <td>2A0696</td>
      <td>Att-Cds Manuf/Dist-Narc || Distribution Cds</td>
      <td>WESTERN</td>
      <td>733.0</td>
      <td>Mondawmin</td>
      <td>(39.3116837460, -76.6475011849)</td>
      <td>39.311684</td>
      <td>-76.647501</td>
    </tr>
    <tr>
      <td>104527</td>
      <td>13610460.0</td>
      <td>43</td>
      <td>B</td>
      <td>F</td>
      <td>12/31/2012</td>
      <td>23:55:00</td>
      <td>4200 Towanda Ave</td>
      <td>4C-Agg. Asslt.- Oth.</td>
      <td>4200 Towanda Av</td>
      <td>1 1415</td>
      <td>Asslt-Sec Degree || 1St Degree Assault</td>
      <td>NORTHWESTERN</td>
      <td>613.0</td>
      <td>Towanda-Grantley</td>
      <td>(39.3361054128, -76.6671512244)</td>
      <td>39.336105</td>
      <td>-76.667151</td>
    </tr>
  </tbody>
</table>
<p>63892 rows × 17 columns</p>
</div>




```python
## Adding a new row for the type of charge based off of only felonies.
## Felonies include: 1st Degree Assault, Sexual Assault, Fraud, Murder, Robbery, Burglary, and Arson

charges = arrest_table['chargeDescription']
arrest_table['type'] = np.zeros(len(arrest_table))
ind = arrest_table.index

assault = []
sex = []
fraud = []
murder = []
robbery = []
burglary = []
arson = []
for i in ind:
    temp = str(charges[i])

    ## First degree Assault
    if "Asslt-First Degree" in temp:
        assault.append(i)
    elif "1St Degree Assault" in temp:
        assault.append(i)
    elif "Assault 1St Degree" in temp:
        assault.append(i)
        
    ## Sexual Assault/ Sexual offense/ Child Sexual Abuse
    elif "Sex" in temp:
        sex.append(i)
    elif "Fraud" in temp:
        fraud.append(i)
    elif "Murder" in temp:
        murder.append(i)
    elif "Robbery" in temp:
        robbery.append(i)
    elif "Burglary" in temp:
        burglary.append(i)
    elif "Arson" in temp:
        arson.append(i)
        
```


```python
## Assign a number to the type based off of the type of felony

arrest_table['type'] = np.zeros(len(arrest_table))

for i in assault:
    arrest_table.at[i,'type'] = 1
for i in sex:
    arrest_table.at[i,'type'] = 2
for i in fraud:
    arrest_table.at[i,'type'] = 3
for i in murder:
    arrest_table.at[i,'type'] = 4
for i in robbery:
    arrest_table.at[i,'type'] = 5
for i in burglary:
    arrest_table.at[i,'type'] = 6
for i in arson:
    arrest_table.at[i,'type'] = 7
    
## Remove from the table all non-felony offences
arrest_table = arrest_table[arrest_table.type != 0]

## Remove from the table all felonies not done in 2012
date = arrest_table['arrestDate']
ind = arrest_table.index
arrest_table['year'] = np.zeros(len(arrest_table))
for i in ind:
    asfloat = float(date[i][8:])
    if (asfloat == 11):
        arrest_table.at[i,'year'] = 11
    elif (asfloat == 12):
        arrest_table.at[i,'year'] = 12
arrest_table = arrest_table[arrest_table.year != 11]
```

    /opt/conda/lib/python3.7/site-packages/ipykernel_launcher.py:22: SettingWithCopyWarning: 
    A value is trying to be set on a copy of a slice from a DataFrame.
    Try using .loc[row_indexer,col_indexer] = value instead
    
    See the caveats in the documentation: http://pandas.pydata.org/pandas-docs/stable/user_guide/indexing.html#returning-a-view-versus-a-copy



```python
## Assign each felony a color in order to destinguish them from one another.
# assault = 1 = red
# sex = 2 = pink
# fraud = 3 = green
# murder = 4 = black
# robbery = 5 = lightgray
# burglary = 6 = gray
# arson = 7 = darkred
# location, time, date, age, and sex

map_osm = folium.Map(location=[39.29, -76.61], zoom_start=11)

for i in range(1,len(arrest_table)):
    loc = arrest_table.iloc[i]
    pop = "Location: " + str(loc['incidentLocation']) + "\nDate: " + str(loc['arrestDate']) + "\nTime: " + str(loc["arrestTime"]) + "\nAge: " + str(loc['age']) + "\nGender: " + str(loc['sex'])
    color_ind = 'black'
    if (arrest_table.iloc[i]['type'] == 1):
        color_ind = 'red'
    elif (arrest_table.iloc[i]['type'] == 2):
        color_ind = 'pink'
    elif (arrest_table.iloc[i]['type'] == 3):
        color_ind = 'green'
    elif (arrest_table.iloc[i]['type'] == 4):
        color_ind = 'black'
    elif (arrest_table.iloc[i]['type'] == 5):
        color_ind = 'lightgray'
    elif (arrest_table.iloc[i]['type'] == 6):
        color_ind = 'gray'
    elif (arrest_table.iloc[i]['type'] == 7):
        color_ind = 'darkred'
    folium.Marker([arrest_table.iloc[i]['lat'], arrest_table.iloc[i]['long']],
                    popup=pop,
                    icon=folium.Icon(color=color_ind, icon = 'circle', prefix = 'fa')).add_to(map_osm)
map_osm
```








```python
## Reasoning:
## Due to the large nature of the crime database it was impossible to graph all points of data, therefore 
## It seemed necessary to somehow prune the data, so it was decided that felonies are the most serious
## and therefore more important to display. Even so, only felonies resulted in over 2000 entries which 
## still proved to be difficult for the map to display, so it was further pruned to only display crimes
## in 2012. This smaller set of entries made the data much less unwieldy and ultimately resulted in a
## much more manable map.

arrest_table
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
      <th>arrest</th>
      <th>age</th>
      <th>race</th>
      <th>sex</th>
      <th>arrestDate</th>
      <th>arrestTime</th>
      <th>arrestLocation</th>
      <th>incidentOffense</th>
      <th>incidentLocation</th>
      <th>charge</th>
      <th>chargeDescription</th>
      <th>district</th>
      <th>post</th>
      <th>neighborhood</th>
      <th>Location 1</th>
      <th>lat</th>
      <th>long</th>
      <th>type</th>
      <th>year</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>52870</td>
      <td>12395914.0</td>
      <td>23</td>
      <td>B</td>
      <td>M</td>
      <td>01/01/2012</td>
      <td>00:10:00</td>
      <td>5000 Preston St</td>
      <td>3B-Robb Highway (Ua)</td>
      <td>1000-Blk Armistead Wy</td>
      <td>1 0521</td>
      <td>Theft Less Than $100.00 || Unarmed Robbery</td>
      <td>NORTHEASTERN</td>
      <td>431.0</td>
      <td>Armistead Gardens</td>
      <td>(39.3069129142, -76.5552677284)</td>
      <td>39.306913</td>
      <td>-76.555268</td>
      <td>5.0</td>
      <td>12.0</td>
    </tr>
    <tr>
      <td>52892</td>
      <td>12395891.0</td>
      <td>60</td>
      <td>B</td>
      <td>M</td>
      <td>01/01/2012</td>
      <td>02:00:00</td>
      <td>1200 Shellbanks Rd</td>
      <td>4B-Agg. Asslt.- Cut</td>
      <td>1200 Shellbanks Rd</td>
      <td>1 1425</td>
      <td>Reckless Endangerment || 1St Degree Assault</td>
      <td>SOUTHERN</td>
      <td>922.0</td>
      <td>Cherry Hill</td>
      <td>(39.2501297933, -76.6311094410)</td>
      <td>39.250130</td>
      <td>-76.631109</td>
      <td>1.0</td>
      <td>12.0</td>
    </tr>
    <tr>
      <td>52906</td>
      <td>12395959.0</td>
      <td>25</td>
      <td>B</td>
      <td>M</td>
      <td>01/01/2012</td>
      <td>04:09:00</td>
      <td>400 N Clinton St</td>
      <td>5B-Burg. Res. (Att.)</td>
      <td>400 N Clinton St</td>
      <td>2A3030</td>
      <td>Att-Burglary-Fourth Degree || Attempt B And E</td>
      <td>SOUTHEASTERN</td>
      <td>223.0</td>
      <td>Ellwood Park/Monument</td>
      <td>(39.2965661400, -76.5706440900)</td>
      <td>39.296566</td>
      <td>-76.570644</td>
      <td>6.0</td>
      <td>12.0</td>
    </tr>
    <tr>
      <td>52943</td>
      <td>12396078.0</td>
      <td>35</td>
      <td>B</td>
      <td>F</td>
      <td>01/01/2012</td>
      <td>18:00:00</td>
      <td>2800 Carver Rd</td>
      <td>4E-Common Assault</td>
      <td>2800 Carver Rd</td>
      <td>1 6505</td>
      <td>Arson/Threat || Assault</td>
      <td>SOUTHERN</td>
      <td>924.0</td>
      <td>Cherry Hill</td>
      <td>(39.2472799811, -76.6260669179)</td>
      <td>39.247280</td>
      <td>-76.626067</td>
      <td>7.0</td>
      <td>12.0</td>
    </tr>
    <tr>
      <td>52952</td>
      <td>12396121.0</td>
      <td>33</td>
      <td>B</td>
      <td>M</td>
      <td>01/01/2012</td>
      <td>21:30:00</td>
      <td>4400 Frankford Ave</td>
      <td>5C-Burg. Res. (Noforce)</td>
      <td>4400 Frankford Av</td>
      <td>2 3030</td>
      <td>Burglary-Fourth Degree || Burglary - 4Th Degree</td>
      <td>NORTHEASTERN</td>
      <td>435.0</td>
      <td>Frankford</td>
      <td>(39.3360523016, -76.5495518054)</td>
      <td>39.336052</td>
      <td>-76.549552</td>
      <td>6.0</td>
      <td>12.0</td>
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
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <td>104391</td>
      <td>12610025.0</td>
      <td>40</td>
      <td>B</td>
      <td>M</td>
      <td>12/30/2012</td>
      <td>15:00:00</td>
      <td>6700 Reisterstown Rd</td>
      <td>3CO-Robb Comm-Other Wpn</td>
      <td>6700 Reisterstown Rd</td>
      <td>1 0600</td>
      <td>Resist/Interfere With Arrest || Commercial Arm...</td>
      <td>NORTHWESTERN</td>
      <td>631.0</td>
      <td>Reisterstown Station</td>
      <td>(39.3582506244, -76.7052824949)</td>
      <td>39.358251</td>
      <td>-76.705282</td>
      <td>5.0</td>
      <td>12.0</td>
    </tr>
    <tr>
      <td>104401</td>
      <td>12610081.0</td>
      <td>53</td>
      <td>W</td>
      <td>F</td>
      <td>12/30/2012</td>
      <td>17:15:00</td>
      <td>2400 Shirley Ave</td>
      <td>8AO-Arson Sin Res Str-Occ</td>
      <td>2400 Shirley Av</td>
      <td>1 6502</td>
      <td>Malicious Burning/1St Deg || Arson 1St Degree</td>
      <td>NORTHERN</td>
      <td>531.0</td>
      <td>Greenspring</td>
      <td>(39.3359059900, -76.6586466061)</td>
      <td>39.335906</td>
      <td>-76.658647</td>
      <td>7.0</td>
      <td>12.0</td>
    </tr>
    <tr>
      <td>104461</td>
      <td>12610201.0</td>
      <td>22</td>
      <td>B</td>
      <td>M</td>
      <td>12/31/2012</td>
      <td>09:30:00</td>
      <td>3000 Westwood Ave</td>
      <td>87-Narcotics</td>
      <td>1700 Bloomingdale Rd</td>
      <td>2 3030</td>
      <td>Burglary-Fourth Degree || Cds Violation</td>
      <td>SOUTHWESTERN</td>
      <td>811.0</td>
      <td>Northwest Community Action</td>
      <td>(39.3080332653, -76.6682045590)</td>
      <td>39.308033</td>
      <td>-76.668205</td>
      <td>6.0</td>
      <td>12.0</td>
    </tr>
    <tr>
      <td>104466</td>
      <td>12610235.0</td>
      <td>21</td>
      <td>B</td>
      <td>M</td>
      <td>12/31/2012</td>
      <td>10:55:00</td>
      <td>4700 Old York Rd</td>
      <td>5A-Burg. Res. (Force)</td>
      <td>4700 Old York Rd</td>
      <td>2C3010</td>
      <td>Con-Burglary/2Nd Degree/General || Burglary</td>
      <td>NORTHERN</td>
      <td>524.0</td>
      <td>Richnor Springs</td>
      <td>(39.3466972399, -76.6076119157)</td>
      <td>39.346697</td>
      <td>-76.607612</td>
      <td>6.0</td>
      <td>12.0</td>
    </tr>
    <tr>
      <td>104527</td>
      <td>13610460.0</td>
      <td>43</td>
      <td>B</td>
      <td>F</td>
      <td>12/31/2012</td>
      <td>23:55:00</td>
      <td>4200 Towanda Ave</td>
      <td>4C-Agg. Asslt.- Oth.</td>
      <td>4200 Towanda Av</td>
      <td>1 1415</td>
      <td>Asslt-Sec Degree || 1St Degree Assault</td>
      <td>NORTHWESTERN</td>
      <td>613.0</td>
      <td>Towanda-Grantley</td>
      <td>(39.3361054128, -76.6671512244)</td>
      <td>39.336105</td>
      <td>-76.667151</td>
      <td>1.0</td>
      <td>12.0</td>
    </tr>
  </tbody>
</table>
<p>1226 rows × 19 columns</p>
</div>




```python

```


```python

```