# An Analysis of the Path of Exile League Economy

### Introduction

<p> Path of Exile (PoE) is a MMOARPG (Massively Multiplayer Online Action Role Playing Game) developed by Grinding Gear Games. Released in 2013, with the support of crowdfunding, the game has since grown greatly, having hundreds of thousands of players playing with the release of each patch. One of the biggest differences from other games is that in most MMOs one would kill monsters and expect some sort of monetary reward like gold. However, in PoE killing monsters rewards players with a variety of orbs which serves as the currency. These currency orbs are not only currency, but double as a usable item which can be used to affect items, for example: Chaos orbs which removes all the stats on an item and rerolls completely new stat values and properties. The inherent value in these currencies comes from how they are essentially used to gamble on items, with players trying to use them in order to try and upgrade their items to be stronger. However conversely, players can also just save up currency and buy a better item from another player.
</p>

<div> Path of Exile operates on a system that is known as Leagues. Roughly every 3 months the game receives a major patch which introduces new mechanics, items, and content but also wipes out all existing characters and items from the previous league. This gives the economy of the game a fresh start every 3 months, which allows us to track trends in the valuation of currency across leagues. While the prices of relative price of currency items tends to stabilize as each league progresses, the first month’s economy is incredibly volatile, and a lot of money can be made by understanding the demand for currency items and correctly playing the market. 
</div>

<p> Another thing to note is that in PoE, people tend to pay for convenience. Unlike most	games, there is not actual in-game designated marketplace. People have to search up on a 3rd party website, for what they want, then message the player for it, and then go to their “hideout” to trade with them. This makes it so that people who want to buy bulk amounts of items, tend to pay a premium to spare them the inconvenience of having to go out of their way to complete multiple trades with different people. 
</p>
> An example of this would be that if you wanted to buy 1000 pencils online and there were only 2 listings in existence. The first is that you could buy all 1000 pencils in 1 transaction for $1000, or you could buy 1 pencil at a time for $0.95. While some people would be ok with wasting time clicking 1000 times, a lot of people would just pay the extra $50 to spare them the pain. 

### Getting Started

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
