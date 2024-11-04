# Quant Challenge
XXX
* [Background](#Background)
* [Energy Trading in a Nutshell](#Energy-Trading-in-a-Nutshell)
* [The Challenge](#The-Challenge)
  * [Data Model](#Data-Model)
  * [Assignments](#Assignments)
* [Setup](#Setup)
* [Submission Instructions](#Submission-Instructions)
* [Notes](#Notes)


## Background
This challenge is meant to give you a taste of the type of problems our quants and developers have to solve on a daily basis. It helps you decide if you might have fun working with us. It is also an opportunity to demonstrate your technical/statistical/data skills and the ability to understand and work with our domain. It includes two tasks … XXX


## Task 1: Minimal Reporting tool

### Overview
Energy trading happens in an **exchange**, a market where traders working for energy producers 
(solar plants, nuclear power plants, ...) and consumers (B2C energy providers, big 
energy consuming industries like steel and trains...) submit orders to buy and sell energy.
One of the major exchanges in Europe is called [**EPEX**](https://en.wikipedia.org/wiki/European_Power_Exchange).

These orders consist of a quantity (in Megawatt) over a predefined period of
time, called **delivery period** (for example between 12:00, the **delivery start** and 13:00, 
the **delivery end**, on a given day) and for a given price per megawatt hour (referred to as mwh).

The orders are structured within orderbooks divided in bid (buy orders) and ask (sell orders).
![img.png](comtrader_snip.png)


If the prices of two orders with opposite sides match, i.e the buy price is higher than the sell price, 
then, a trade is generated. 
For example if the orderbook contains an order to sell 10 mw for 10 euros/mwh and another trader 
submits an order to buy 5 mw for 11 euros/mwh, the orders are matched by the exchange and a trade is 
generated for 5mw at 10 euros/mwh.

Those trades are saved by flex power and used to compute various indicators on the performance of our trading strategies. 
The first task bases on a collection of such trades and tries to implement a minimal reporting tool for different trading strategies.

The task models a trade as follows:
•	Trade:
o	id: integer
o	price: float
o	quantity: integer
o	side: string, buy or sell
o	strategy_id: string
![image](https://github.com/user-attachments/assets/e2ec9a1e-bb97-4394-8c85-df701d3dcd68)


### Task 1.1: 
Write a function that computes the total buy volume for flex power, another that computes the total sell volume.
```python

def compute_total_buy_volume(*args, **kwargs) -> float:
    pass

def compute_total_sell_volume(*args, **kwargs) -> float:
    pass
```

#### Task 1.2: 
Write a function that computes the PnL (profit and loss) of each strategy in euros. It's defined as the sum of the incomes 
realized with each trade.

If we sell energy, our income is `quantity * price` since we got money for our electricity. If we buy energy, our income is `-quantity * price`.
```python
def compute_pnl(strategy_id: str, *args, **kwargs) -> float:
    pass
```
This function should return 0 it there are no trades that correspond to the strategy id.

#### Task 1.3: 
Expose the function defined in the second task as an entrypoint of a web application. 

Here is the corresponding API definition.
```yaml
swagger: "2.0"
info:
  title: Energy Trading API
  version: 1.0.0
host: api.example.com
basePath: /v1
schemes:
  - https
paths:
  /pnl/{strategy_id}:
    get:
      summary: Returns the pnl of the corresponding strategy.
      parameters:
        - in: path
          name: user_id
          required: true
          type: string
          description: string identifier of a strategy.
      produces:
        - application/json
      responses:
        200:
          description: A PnL data object.
          schema:
            type: object
            properties:
              strategy:
                type: string
                example: my_strategy
              value:
                type: float
                example: 100.0
              unit:
                type: string
                example: euro
              capture_time:
                type: string
                example: "2023-01-16T08:15:46Z"
```

## Setup
The repository contains a sqlite file `trades.sqlite`, containing a database named `trades`.

The database contains a table called `epex_2022_12_20_12-13`, with all trades made by flex power traders 
 on the EPEX exchange for the delivery period 12:00 to 13:00 on 2022-12-20.

The schema is the following
```sqlite
id TEXT PRIMARY KEY,
quantity INTEGER NOT NULL,
price REAL NOT NULL,
side TEXT NOT NULL CHECK (side IN ('buy', 'sell')),
strategy TEXT NOT NULL
```


## Task 2: Data analysis and building a trading strategy

### Overview

Power prices depend on external factors, most importantly the (forecasted) infeed from variable renewable energy sources PV and wind. In this task you are supposed to do some data analysis, get to know this correlation and finally build your own trading strategy based on wind and pv forecasts. The trading strategy is task 2.7, try to spend most of your time on that one. It is a very open task and there is no one correct solution so get creative here. We encourage you though to present clean results, nice graphs and generally good work. ![image](https://github.com/user-attachments/assets/288569cd-5271-49c3-97be-62cc28b55e4b)

### Setup


### Task 2.1:
How much Wind/PV Power was forecasted to produced in German in 2021 [in MWh] on Day Ahead (da) and on Intraday (id). Hint: Be careful: you have values in MW on a quarter hourly basis, think how this transaltes into hourly values.

### Task 2.2:
Show the average Wind/Solar production for 2021 over a 24h period for Intraday id and Day Ahead da (4 lines in one graph)

### Task 2.3:
What was the average value [in EUR/MWh] for Wind/Solar Power in 2021 using the da forecast and using da h prices. The average value is defined as the average hourly value that a Wind/PV farm owner would have received for their product. Is the average value of Wind and PV higher or lower than the average da price? Why could it be higher/lower?

### Task 2.4:
Find the Day with the highest renewable energy production and with the lowest renewable energy production in 2021. What was the avaerage Day Ahead Price levels on these days? How do you explain the difference in prices?

### Task 2.5:
What is the average hourly da price during week days vs during weekends. Why do you think average prices may differ?

### Task 2.6:
How much revenue would you generate with a battery with a capacity of 1 MWh which you can fully charge and fully discharge (1 Cycle) every day in 2021? Think about when you would charge and when you would discharge and apply this rule for each day of the year.

### Task 2.7:
Come up with a trading strategy that makes money between the day ahead hourly prices and the intraday hourly prices. A strategy could be something like, always buy hour 19-20 on day ahead and sell it on intraday. You can look at certain times, weekdays, seasons, production levels of wind and solar. Your strategy can have a few input paramters such as time, renewable produciton etc, and then a decision output between two prices. I.e. when do you want to go long and short. Show the cumulative performance of this strategy with a 100 MW position. Show your results and quickly explain your reasoning of why you think this strategy might be a good idea and why it does or does not work.



## Submission Instructions
Create a Github repository containing your solution and provide us with access so that we can review your 
code.

Feel free to add notes about technology choices and design decisions, as well as anything that we should
keep in mind when reviewing your code.

Ideally, the deliverable should be in python 3.*.

## Notes
* Spend as much time as you want on the challenge to produce something you are proud of. The intended time is a couple 
of hours.
* If you can think of any other cool features that you could implement, go for it! Just be sure to describe them in the readme.
* If you already have similar personal projects that you would like to submit instead of this one, it's also possible.
Just make sure that you include an exhaustive documentation on what it's about and how we can start it.
