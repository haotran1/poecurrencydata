# An Analysis of the Path of Exile League Economy
![Title]($$$$$.png)

## Introduction

<p> Path of Exile (PoE) is a MMOARPG (Massively Multiplayer Online Action Role Playing Game) developed by Grinding Gear Games. Released in 2013, with the support of crowdfunding, the game has since grown greatly, having hundreds of thousands of players playing with the release of each patch. The premise of the game is that the player is an exile who has been banished to the land of Wraeclast finding nothing but writhing horrors, murderous beasts, and ravenous undead. Throughout this game the player is gathering wepons, gems, and power. The exile travels the forsaken lands to find answers as to what has corrupted the land, killing monsters and even gods; to the point that who the world can no longer distinguish who the real monster is. </p>

<p> Our tutorial is going to be analysing the economy of PoE and finding optimal ways of making in game currency by looking at trends in the pricing of in-game currency items. We are going to be observing the trends in the value of currency items relative to the item Chaos Orb, as well as a the efficiency of a common money making method known as flipping.</p>

### Better understanding PoE's economy
<p> One of the biggest differences from other games is that in most MMOs one would kill monsters and expect some sort of monetary reward like gold. However, in PoE killing monsters rewards players with a variety of orbs which serves as the currency. These currency orbs are not only currency, but double as a usable item which can be used to affect items, for example: Chaos orbs which removes all the stats on an item and rerolls completely new stat values and properties. The inherent value in these currencies comes from how they are essentially used to gamble on items, with players trying to use them in order to try and upgrade their items to be stronger. However conversely, players can also just save up currency and buy a better item from another player. </p>

<p> The game economy is also completely set by the players. The developers play no hand in determining the price of an item so it is only market supply and demand that can determine the prices of items. An item can have an incredibly rare drop rate, however, if there is no demand for it then is sold for pocket change. The economy shares many properties that one would expect with a normal market, however it has some quirks that makes an analysis of the PoE economy incredibly interesting.</p>

<p> Below are some of the key points that one would need to know to help better understand the PoE economy: </p>

- Path of Exile operates on a system that is known as Leagues. Roughly every 3 months the game receives a major patch which introduces new mechanics, items, and content but also wipes out all existing characters and items from the previous league. This gives the economy of the game a fresh start every 3 months, which allows us to track trends in the valuation of currency across leagues. While the prices of relative price of currency items tends to stabilize as each league progresses, the first month’s economy is incredibly volatile, and a lot of money can be made by understanding the demand for currency items and correctly playing the market. 

- In PoE, people tend to pay for convenience. Unlike most games, there is not actual in-game designated marketplace. People have to search up on a 3rd party website, for what they want, then message the player for it, and then go to their “hideout” to trade with them. This makes it so that people who want to buy bulk amounts of items, tend to pay a premium to spare them the inconvenience of having to go out of their way to complete multiple trades with different people. This allows for a certain margin of upricing that can be deemed acceptable by the buyer, thus allowing the seller to generate higher profit margins. Because of this bulk upricing, the seller holds an upper hand in trades, and has far more power in dictating price. In most cases buyers are willing to pay premiums of up to even 25% in order to save themselves time.

### Data Source
<p> Poeninja is a webstie which has been logging trade data for each league since 2017. Using their datasets found at: https://poe.ninja/data, we are going to be taking data from the previous 5 leagues: Blight, Legion, Synthesis, Betrayal, and Delve. Our tutorial is going to be looking at the ratios between currency items on the market, and observing what trades can be deemed as profitable. </p>

### Getting Started

<p> We are going to be utilizing the following imported libraries </p>
```python
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
```

## Preprocessing the Data

```python
legion = pd.read_csv("Legion.2019-06-07.2019-09-02.currency.csv", sep=',')
legion.head()
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
      <th>League</th>
      <th>Date</th>
      <th>Get</th>
      <th>Pay</th>
      <th>Value</th>
      <th>Confidence</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>0</td>
      <td>Legion</td>
      <td>6/7/2019</td>
      <td>Orb of Alchemy</td>
      <td>Chaos Orb</td>
      <td>0.34000</td>
      <td>High</td>
    </tr>
    <tr>
      <td>1</td>
      <td>Legion</td>
      <td>6/8/2019</td>
      <td>Orb of Alchemy</td>
      <td>Chaos Orb</td>
      <td>0.27347</td>
      <td>High</td>
    </tr>
    <tr>
      <td>2</td>
      <td>Legion</td>
      <td>6/9/2019</td>
      <td>Orb of Alchemy</td>
      <td>Chaos Orb</td>
      <td>0.27028</td>
      <td>High</td>
    </tr>
    <tr>
      <td>3</td>
      <td>Legion</td>
      <td>6/10/2019</td>
      <td>Orb of Alchemy</td>
      <td>Chaos Orb</td>
      <td>0.32258</td>
      <td>High</td>
    </tr>
    <tr>
      <td>4</td>
      <td>Legion</td>
      <td>6/11/2019</td>
      <td>Orb of Alchemy</td>
      <td>Chaos Orb</td>
      <td>0.32196</td>
      <td>High</td>
    </tr>
  </tbody>
</table>
</div>

<p>
The above table is the dataset for currency items in Legion League. The columns are:
</p>
 - League
 - Date
 - Get: What the buyer is trying to buy
 - Pay: The currency with which the buyer is paying in
 - Value: How many of the currency the buyer must pay for 1 of the "Get" item
 - Confidence: Poeninja's confidence in the value
