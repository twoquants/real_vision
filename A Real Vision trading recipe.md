# With a pinch of stop loss: A Real Vision trading recipe

### Research note by M. Heiden, D. Schneller, M. Seibert

This notebook higlights some graphics from our research note 'With a pinch of stop loss: A Real Vision trading recipe' as mentioned on our blog post on the [TwoQuants](https://www.twoquants.com) blog. The research note itself can be found on my [ResearchGate page](https://www.researchgate.net/publication/342765150_With_a_pinch_of_stop_loss_A_Real_Vision_trading_recipe). Note that this is an ongoing project, so changes are very likely. This report simply summarizes our first insights and allows for feedback and additional research questions.


The research note is an excerpt of our a paper with the same name. The paper analyses the sentiment and compares the performance of trade ideas extracted from $311$ interviews of professional investors and money managers, which where streamed on the ``Netflix of Finance'' - Real Vision between January $2018$ and March $2020$.
This short note gives unique insights into the performance of the trading strategies and analyzes the usefulness of using defined targets and risk limits in trade 

Our thanks go to Real Vision for many entertaining hours of content. Keep up the good work guys!

## Introduction

> You have to minimize your losses and try to preserve capital for those very few instances where you can make a lot in a very short period of time. What you can’t afford to do is throw away your capital on suboptimal trades."

Richard Dennis (Market Wizards)

Financial analyst recommendations are part of your daily life as an investment manager. If and under which conditions these recommendations add value to your investment performance is an ongoing debate in academia, see e.g. Jegadeesh et al. (2004) and Su et al. (2019). On the other hand, real live trading examples have shown that actually shorting popular stocks, in this case recommendations by Jim Cramer in the evening TV show Mad Money on CNBC, can exploit attention-driven buying by retail investors, see Niemann (2017).

In this research note, the bar is much lower. Rather than finding the perfect strategy that makes us rich, we simply want to know if an investment in a subscription to the “Netflix of Finance” - Real Vision, can teach viewers (and readers) something useful about the way of approaching trade ideas. RealVision is an on-demand financial TV channel that conducts interviews with experts in the field of finance, business and the global economy. Among them are well know investors, hedge fund managers, and fi- nancial researchers. Content is updated daily, most of it accessible via a subscription based offering. In addition to Real Vision’s own website, content is available via var- ious other platforms, such as Yahoo Finance, Refinitiv, Trading View, and Interactive Brokers.

The variety of topics ranges from daily briefings to regularly recurring interviews and topics, such as investment ideas or macroeconomic analysis. We focus on the format called 'Trade Ideas' which was updated frequently between January 4th, 2018 to March 12th, 2020. In 2020, Real Vision changed some of the formats. Subsequently, at the date of writing this note, 'Trade Ideas' has been featured less frequently on the channel as broader economic topics dominate the discussion. Real Vision states that these interviews should be interpreted as ideas, not investment advice. In our study, we do not want to impose our discretionary views, but  judge ideas that could be fully implemented given the information by the interviewee. Since many of the ideas entail a very detailed specification of the trade  we are able to analyze this 'systematically simplified' version of the trade. We are interested in the ideas that have at least a defined instrument and trade direction (long/short). If available, we include additional information as pointed out in the next section. Note that we do believe that most likely a better implementation of the trade can be achieved if additional information of the trade idea is processed by an informed human trader. For our analysis, we deliberately keep the discretionary choices to a minimum to avoid hindsight bias. 




## Date & Methodology

### Real Vision Data

We download transcripts of the interviews from January 4th, 2018 to March 12th, 2020. This period includes $292$ interviews, from which we extract $311$ trade ideas. Some interviews contain more than one idea while others contain only broad recom- mendations or tactical tilts. The interviews are mostly structured around the topic of the trade idea and in many cases, this trade idea is summarized in the last paragraph of the transcript, including the instrument, possible entry and exit points, stop-losses or performance targets at which the trade should be exited. 

We only extract an idea if an instrument and a trade direction can clearly be identified, first by our text analysis algorithm and in a second cross-check by a human . We automatically separate key
information such as the title, participants, interview date and publication date. The methodology also cleans the text based on common natural language processing techniques, and prepares it for a sentiment analysis which is part of ongoing research. The algorithm also matches the interview against a dictionary of asset classes to obtain keywords and topic classification.

Since not every trade specification is always unambiguous, we stick to simple rules for specifying the trades:

* if no entry price is explicitly mentioned, we assume the trade should be entered directly after the publication date of the interview.
* if a range of entries, exits or stops is provided, we take the mid price of these intervals.
* if multiple time frames for the trade are mentioned, we take the shorter one.
* if no instrument or clear direction of the trade is given, we do not include the trade idea in our analysis.

These rules do not apply if the interviewee clearly states within the interview that the ideas should be treated as separate trades. We currently exclude:

* trades that use trailing entries or stops.
* trades with an end date after the current date of evaluation, 1st of July,
2020.
* trades using options, futures and government bonds.
We mark the trade as having a valid “exit rule”, if it has at least one of the following
* a target price or a percentage target return. • a stop-loss.
* a defined end-date, which we call time stop.


If a trade is not stopped out by one of the exit rules, it is stopped out by the current date of evaluation which we mark as max. date.
Overall, we are left with $282$ valid trade ideas. This is further reduced to $269$ trades, as $13$ trades do not reach their entry level. Of these ideas, $121$ are for Equities (single stocks), $60$ for ETFs, $53$ for Currencies and $35$ for Indices. For simplicity, we assume that the mentioned indices are tradable.
Table 1 shows the $10$ most common topics of the trade ideas, as well as the most common instruments that were mentioned. Note that since we classify the trade according to the general topic of the interview, there can be more uses of the instrument than counts of the topic. For example, for an interview that has an explicit view on the USD and decides to implement this view against the EUR we classify the topic as “USD”. The used instrument will nevertheless be the EUR/USD.

|    | Instrument     |   Count | Topic           |   Count |
|---:|:---------------|--------:|:----------------|--------:|
|  0 | SPX Index      |      19 | technology      |      40 |
|  1 | XAUUSD Curncy  |      15 | large cap       |      39 |
|  2 | EURUSD Curncy  |       9 | precious metals |      29 |
|  3 | EEM US Equity  |       7 | financials      |      10 |
|  4 | SPY US Equity  |       4 | agriculture     |      10 |
|  5 | KRE US Equity  |       4 | entertainment   |       9 |
|  6 | DIS US Equity  |       4 | healthcare      |       8 |
|  7 | GLD US Equity  |       4 | eur             |       8 |
|  8 | AAPL US Equity |       3 | real estate     |       8 |
|  9 | USDJPY Curncy  |       3 | energy          |       8 |

Table 1: 10 most common topics and instruments. 

### Market Data

Market data is obtained via Bloomberg for the period beginning with the
publication of the respective idea until the current date of evaluation,
1st of July, 2020. We use closing prices and currently exclude
dividends in our calculations. For each instrument not denominated
in USD, we check which currency is referenced in the interview and
convert prices and trade specifications into USD using 5pm New York time
Bloomberg fixings. In addition to our topic classification we run on the
pure text, we also obtain security class information from Bloomberg for
each instrument.

### Trading Idea Evaluation


For each trade, we calculate the log-return, volatility, and average annualized Sharpe ratio(rf =0).For a first analysis,we examine Sharpe ratios classified by the existence of entry and exit rules, see Table 2. Of the $269$ trades that are entered, we cannot compute valid volatility estimates for $15$ trades since their lifespan is smaller than two days.

|    Entry/Exit            |   ann_sharpe_ratio |   ann_sharpe_ratio |
|:---------------|-------------------:|-------------------:|
| (False, False) |             -0.127 |                 34 |
| (False, True)  |              0.04  |                139 |
| (True, False)  |              0.307 |                  7 |
| (True, True)   |              0.332 |                 74 |

Table 2: Average annualized Sharpe ratio grouped by entry/exit combination. Entry means a a precise trade entry is specified. Exit means a precise exit condition is specified.


We observe that while the majority of trades has no defined entry rule, an exit rule is present for $212$ of the trades. Based on the Sharpe ratio, trades without an entry and exit perform worst ($−0.127$), while trades with entry and exit perform best ($0.332$). The number of trades that have an entry, but no exit is small, but their Sharpe ratio is surprisingly high. Without an exit rule, these $7$ trades are entered on publication date and stopped out at the evaluation date beginning of July 2020. It’s worth to have a separate look at these trades, as three of them are coming from the same interview. These three trades alone have an average return of 42% and are all technology related in the single stock space. The second best trade in this group is a long Gold position with a return of $28%$.


Figure 1 shows the relative frequency of the three most frequent topics according to each of the four groups. Trades from the technology sector are relatively less prevalent in the other groups, where they on average make up $15%$ of trades vs. $57%$ in the defined entry but undefined exit group. Hence, the results in this group might be skewed due to a sector focus.


```python
from IPython.display import IFrame
```


```python
IFrame(src='hist.html', width=900, height=600)
```





<iframe
    width="900"
    height="600"
    src="hist.html"
    frameborder="0"
    allowfullscreen
></iframe>




Figure 1: Relative frequency of the three most frequent topics by entry/exit combination.

Instead of the average of the individual trade Sharpe ratios, a clearer picture of the trade characteristics can be provided based on the distribution of trade returns in Figure 2 and the descriptive statistics in Table 3. Note that this allows us to include the additional $15$ trades with very short holding periods, where no volatility estimate could be calculated. Hence, the number of observations does change slightly. We can see the outliers of the group with defined entry but no exit. Both groups of trades with entries exhibit positive skewness, while the groups without entry rule show negative skewness, pointing towards the existence of outliers. Trades with defined entry and defined exit have the lowest volatility and highest kurtosis among the four groups, which means these trades have a higher probability of an upside gain.

|          | def. entry, def. exit   | def. entry, undef. exit   | undef. entry, def. exit   | undef. entry, undef. exit   |
|:---------|:------------------------|:--------------------------|:--------------------------|:----------------------------|
| nobs     | 79                      | 7                         | 149                       | 34                          |
| minmax   | (-0.35, 1.008)          | (-0.198, 0.862)           | (-1.74, 1.021)            | (-0.806, 0.529)             |
| mean     | 0.018                   | 0.211                     | -0.025                    | -0.073                      |
| variance | 0.032                   | 0.156                     | 0.081                     | 0.066                       |
| skewness | 2.225                   | 0.566                     | -1.444                    | -0.264                      |
| kurtosis | 10.49                   | -0.988                    | 9.401                     | 1.105                       |

Table 3: Statistics of distribution of trade returns grouped by entry/exit combination. Def. entry means the trade has a defined entry price, defined exit means the trade has one or several of the exit rules: price target or stop-loss.


```python
IFrame(src='density.html', width=900, height=600)
```





<iframe
    width="900"
    height="600"
    src="density.html"
    frameborder="0"
    allowfullscreen
></iframe>




Table 4 highlights the average trade return based existence of entry and exit, split up into the three different combinations of price based exits that the trade can have: a price target, a price target combined with a stop-loss rule and a stop-loss rule only.
We observe that having only a stop-loss rule yields a negative average return. However, these groups in total have only $3$ observations, which makes the significance of the results questionable. 



| Entry/Exit type       |   trade_return |   trade_return |
|:----------------------|---------------:|---------------:|
| (False, 'none')       |         -0.073 |             34 |
| (False, 'price')      |         -0.056 |             89 |
| (False, 'price_stop') |          0.024 |             58 |
| (False, 'stop')       |         -0.081 |              2 |
| (True, 'none')        |          0.21  |              7 |
| (True, 'price')       |          0.095 |             15 |
| (True, 'price_stop')  |          0.002 |             63 |
| (True, 'stop')        |         -0.177 |              1 |

Table 4: Average trade return conditional on entry and specific exit rule. Price means a valid exit price is specified, price_stop: the trade has a valid exit price and a stop-loss, stop: only a stop-loss is specified. None means that no exit rule is present for the trade

Having a price target and a stop-loss on the other hand yields a positive average return, independent of the existence of an entry.
The average price target is $1.7\%$ higher than the average stop-loss in the groups where both exit rules exist. We find that $55\%$ of the trades with a stop-loss and price target hit the stop-loss. Overall, the probability of hitting a stop-loss is nearly three times higher than the probability of hitting the price target. Due to their asymmetric specification, these trades do effectively cut losses while maintaining upside potential. 

For trades that only have a price target, the specification of an entry seems to be a decisive factor. Trades with defined entry and a price target yield an average return of $9.5\%$ per trade, while trades without a defined entry and a price target results in an average return of $-5.6\%$, see Table 3. Intuitively, this makes sense: even though the lag between filming and publishing the interviews is $3-4$ days, entry prices and conditions may have changed in the meantime. While $75\%$ of trades with an entry hit their price target, only $29\%$ of trades without an entry hit their price target. More than $50\%$ of the trades without an entry are stopped out by reaching our evaluation date. $3$ of these trades have a time limit beyond the evaluation date and are forced to stop before reaching a designated target date. Hence, for the rest of the trades within the group, performance is heavily dependent on the evaluation date. This is less prevalent for trades with an entry and a price target, where we find that only $7\%$ of trades are stopped out by the evaluation date.

Trades with undefined entry and undefined exit show an average return of $-7.3\%$ per trade for a total of $34$ observations and are stopped by the evaluation date  in $63\%$ of the cases.


### Conclusion

In this research note, we analyzed the performance of $269$ trade ideas aired on the on-demand financial TV channel Real Vision between January $2018$ and March $2020$. Our first results indicate, that the return distribution of trade ideas with a systematic set of trading rules such as entry, exit and stop-loss exhibits higher positive skew, less volatility and fewer outliers compared to trade ideas that are less specific. Especially for trades with a price target, the specification of an entry rule seems to be a decisive factor for trade performance. Overall, our analysis shows that even for discretionary ideas, systematic rules play an important role. Besides making a trade idea testable, they reduce downside risk while allowing for upside potential.
 

### References

* Jegadeesh N, Kim J, Krische SD, Lee CMC. Analyzing the analysts: When do recom- mendations add value? The Journal of Finance 2004;59(3):1083–124.
* Niemann M. Essays on Prediction and Behavior of Financial Markets. Ph.D. thesis; University of Augsburg; 2017.
* Su C, Zhang H, Bangassa K, Joseph NL. On the investment value of sell-side analyst recommendation revisions in the UK. Review of Quantitative Finance and Account- ing 2019;53(1):257–93. doi:10.1007/s11156-018-0749-y.

## Additional figures for illustratioan

### Long trade in AAPL US Equity (Apple stock)

The trade was specified with:
* no entry date, hence it was entered on publication date
* no exit rules, but a target date (time-stop)


```python
IFrame(src='trade_illustration_apple.html', width=900, height=600)
```





<iframe
    width="900"
    height="600"
    src="trade_illustration_apple.html"
    frameborder="0"
    allowfullscreen
></iframe>




### Long trade in SPY US Equity (S&P 500 ETF)


The trade was specified with:
* no entry date, hence it was entered on publication date
* a stop-loss rule. The stop-loss was very close to the entry, not giving the trade room to breathe


```python
IFrame(src='trade_illustration_spy.html', width=900, height=600)
```





<iframe
    width="900"
    height="600"
    src="trade_illustration_spy.html"
    frameborder="0"
    allowfullscreen
></iframe>




### Long trade in V US Equity (Visa stock)


The trade was specified with:
* a valid entry price, upon which the trade was entered
* a valid exit rule (price target), upon which the trade was closed


```python
IFrame(src='trade_illustration_v_us_exit.html', width=900, height=600)
```





<iframe
    width="900"
    height="600"
    src="trade_illustration_v_us_exit.html"
    frameborder="0"
    allowfullscreen
></iframe>



