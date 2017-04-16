---
layout: post
title: Practical Python Business Cost Model for Traveling
tags: [pythonic_business, python, pandas, cost model]
---

So recently me and my friend are planning to go to a trip to New York. We
recently just bought the airfare ticket and the musical ticket for the show we
want to watch, but we were worrying what the housing should be, and how do we
estimate our budget to rent the AirBnB we're interested. 

I also recently had just read the [Practical Python Business](https://help.ghost.org/hc/en-us/articles/224410728-Markdown-Guide)
article about using python as a [cost model sheet](http://pbpython.com/amortization-model.html)
(it goes in depth on why using python is so much better than using excel), and
wanted to tackle the problem using python. 

The idea starts from excel having a lot of hidden functions, poor sourcing
control environment, and delicate sheet that is easily breakable.

Enjoy my python cost model notebook!


## Cost Model for Trip to New York! The idea is to minimize the amount of cost,
and Maximize the amount of fun!

Goal: Trying to predict/approximate how our spending in NYC would be, so we can
don't go too crazy with our spending, and have a good estimate on how much we
are pouring in!


```python
# import all the needed library.
import pandas as pd
import numpy as np
import arrow
import seaborn as sns
from datetime import datetime

%matplotlib inline
```

Defining on all the input we'd like!


```python
# Global variables. Defining fixed cost/ goal.
# Currently fixated at 1050 per person.
Budget = 1050
# This is how much it costs to fly back and forth from SFO to NYC
Airfare_A = 140 + 268.2
# This is how much it costs to fly back and forth from OC to NYC
# TODO(alex): Ask J what is the actual fare for the flight
Airfare_J = 500
# How much waitress was per ticket.
Waitress_Price = 148.05
# Guestimate the approximate spending for housing.
Housing_Price_Per_Night = 125
Daily_Coffee = 4
Lunch_Cost = 10
Breakfast_Cost = 10 
Dinner_Cost = 20
Daily_Commute_Cost = 10
Nights_In_NYC = 5
```


```python
# Define the constants that are set.
start = datetime(2017, 5, 24)
end = datetime(2017, 5, 29)
date_range = list()
for r in arrow.Arrow.range('day', start, end):
    date_range.append(str(r.format('YYYY-MM-DD')))

print date_range
```

    ['2017-05-24', '2017-05-25', '2017-05-26', '2017-05-27', '2017-05-28', '2017-05-29']



```python
# Define Fixed Cost already:
Remaining_Budget = (Budget * 2) - Airfare_A - Airfare_J - (Waitress_Price * 2) - \
(Housing_Price_Per_Night * Nights_In_NYC)
print "Remaining budget is ${}".format(round(Remaining_Budget,2))
```

    Remaining budget is $270.7



```python
df = pd.DataFrame(index=date_range,
                 columns=['BreakFast',
                          'Lunch',
                          'Dinner',
                          'Coffee',
                          'Commute'])
df.index.name = 'Date'
df = df.reset_index()
```


```python
df['Coffee'] = Daily_Coffee
df['BreakFast'] = Breakfast_Cost
df['Lunch'] = Lunch_Cost
df['Dinner'] = Dinner_Cost
df['Commute'] = Daily_Commute_Cost
```


```python
# Clean up the Data a bit: No breakfast lunch, dinner, coffee for the 24th, 
# and since we are leaving on the 29th morning, we can take out the lunch, dinner
df.loc[df['Date'].isin(['2017-05-24', '2017-05-29']),
       ['Coffee', 'Lunch', 'Dinner']] = 0
df.loc[df['Date'].isin(['2017-05-24']),  'BreakFast'] = 0
```


```python
df
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Date</th>
      <th>BreakFast</th>
      <th>Lunch</th>
      <th>Dinner</th>
      <th>Coffee</th>
      <th>Commute</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>2017-05-24</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>10</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2017-05-25</td>
      <td>10</td>
      <td>10</td>
      <td>20</td>
      <td>4</td>
      <td>10</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2017-05-26</td>
      <td>10</td>
      <td>10</td>
      <td>20</td>
      <td>4</td>
      <td>10</td>
    </tr>
    <tr>
      <th>3</th>
      <td>2017-05-27</td>
      <td>10</td>
      <td>10</td>
      <td>20</td>
      <td>4</td>
      <td>10</td>
    </tr>
    <tr>
      <th>4</th>
      <td>2017-05-28</td>
      <td>10</td>
      <td>10</td>
      <td>20</td>
      <td>4</td>
      <td>10</td>
    </tr>
    <tr>
      <th>5</th>
      <td>2017-05-29</td>
      <td>10</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>10</td>
    </tr>
  </tbody>
</table>
</div>



Calculate how much we would spend on daily!


```python
df['daily_spend'] = df.apply(lambda x: x['BreakFast'] + x['Lunch'] + \
                             x['Dinner'] + x['Coffee'] + x['Commute'], axis = 1)
```


```python
df
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Date</th>
      <th>BreakFast</th>
      <th>Lunch</th>
      <th>Dinner</th>
      <th>Coffee</th>
      <th>Commute</th>
      <th>daily_spend</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>2017-05-24</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>10</td>
      <td>10</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2017-05-25</td>
      <td>10</td>
      <td>10</td>
      <td>20</td>
      <td>4</td>
      <td>10</td>
      <td>54</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2017-05-26</td>
      <td>10</td>
      <td>10</td>
      <td>20</td>
      <td>4</td>
      <td>10</td>
      <td>54</td>
    </tr>
    <tr>
      <th>3</th>
      <td>2017-05-27</td>
      <td>10</td>
      <td>10</td>
      <td>20</td>
      <td>4</td>
      <td>10</td>
      <td>54</td>
    </tr>
    <tr>
      <th>4</th>
      <td>2017-05-28</td>
      <td>10</td>
      <td>10</td>
      <td>20</td>
      <td>4</td>
      <td>10</td>
      <td>54</td>
    </tr>
    <tr>
      <th>5</th>
      <td>2017-05-29</td>
      <td>10</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>10</td>
      <td>20</td>
    </tr>
  </tbody>
</table>
</div>




```python
 Total_Spend = df['daily_spend'].sum()
print "We would spend in total: ${} living in NYC".format(Total_Spend)
```

    We would spend in total: $246 living in NYC



```python
budget_left_post_trip = Remaining_Budget - Total_Spend
```


```python
print "Leftover budget is ${}".format(round(budget_left_post_trip,2))
```

    Leftover budget is $24.7


## It looks like if we follow this spend behavior(or even less than what is estimated, we can really crank up a whole 5 days under 1k per person!) 

We even would have 24.69 dollars in remain xD



We can even make a linear equation on Budget to Housing Cost!

Technically, it is

Input_Budget = (2 X Budget) - Airfare_A - Airfare_J - (Waitress_Price * 2) - Total_Spend - ( 5 X Housing_Price_Per_Night)


```python
def house_price_to_budget(house_price):
    Total_Budget = Airfare_A + Airfare_J + (Waitress_Price * 2)\
    + Total_Spend + (Nights_In_NYC * house_price)
    
    # Split the budget by 2
    return Total_Budget / 2.0
```


```python
h1 = 80
h2 = 100
h3 = 130

b1 = house_price_to_budget(h1)
b2 = house_price_to_budget(h2)
b3 = house_price_to_budget(h3)
```


```python
# Let's see what it looks like in a line graph!
budget_df = pd.DataFrame({'house_price': [h1,h2,h3],
                          'budget': [b1,b2,b3]})
print budget_df
```

        budget  house_price
    0   925.15           80
    1   975.15          100
    2  1050.15          130



```python
budget_df.plot.line(x='budget',
                   y='house_price',
                   title='Daiy House Rental to Total Budget')
```




    <matplotlib.axes._subplots.AxesSubplot at 0x1114dad90>




![png](/img/ipnb/cost_model.png)


Hope this is helpful to determine how much we'd want to spend for daily house rental!

## Conclusion:
Over all after working on the model on python notebook. The readability
was definitely clearer than excel. Also there is nothing that is hidden
within the code with implicit functions. I do like budgeting this way
a lot, and hopefully I can do more pythonic style models for my day to day
in the future.
