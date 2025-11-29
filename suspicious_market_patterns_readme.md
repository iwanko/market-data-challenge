# 1. Data source considerations  

## First and foremost observation  

- 1) Sparse data with **unsignificant amount of ticks**. This complicates applying common statistical tools and assumptions.  
The minimal time gap between OB updates is around 1091 seconds, which is a lot.

```
Orderbook ts count by days:	64, 68, 56, 
Trades ts count by days:	273, 274, 298,
```


Historical trades from Binance can be downloaded at:
https://data.binance.vision/?prefix=data/spot/daily/aggTrades/ETHBTC/  
Number of daily entries there ranges from 43k to 79k, which is several orders of magnitude larger than all data points in the task dataset (<1k).  
Which makes it doubtful to assume that such data is a *real data feed from an actual exchange*, because such a low activity would be not sustainable for the exchange to maintain operation on this pair.  
Also it appears that ETHBTC pair isn't present even on some large CEXs.  


## Observations on data which look unnatural:

- 2) Almost all trades occur above the midprice but below best ask. (Also this majority is BUY trades). *See 2.3 in the notebook.*  
Normally, trades should probably occur closer to TOB than it appears here.  

![Prices vs Time: Trade prices, Best Bid/Ask, and Mid](./img/2_3_prices_vs_time.png)


> [!IMPORTANT]  
> Adding this to the above, one can suspect **possible data manipulation** (possibly undersampling in time dimension).  

- 3) There are several areas where the shape of the TOB over time looks unnaturally flat. Even when the other side might move in a more or less natural manner. *(plot above)*  
  
- 4) The orderbook is heavily imbalanced toward the BUY side, which contradicts the price mainly moving down over the period presented. *(2.4 plot below)*  

![](./img/2_4.png)




## Observation from comparing the price movements to ETHBTC plot from Binance:  

- The spread is large when compared even to candlestick plot from Binance for that time period. (This is apparent even by glancing at the plots, without computing the bps values. The candlestick provides resolution up to 15 min, and for each candle one can expect that the spread at any time moment within is bound by the candle swing, but typically should be much smaller by some orders of magnitude.)  

> [!IMPORTANT]  
> This (combined with point 3 from above) is another signal to suspect that the **data have been manipulated**: maybe some adaptive number of top levels where removed for several timeranges to produce such a flat profile.  

A proper comparison would require having similar data piece from a large exchange, but it appears impossible to find free full-orderbook historical updates. (Or even TOB-only).


## Some other small observations:

- 1.1 Significant portion of bids orderbooks (~69%) doesn't introduce any updates to the previous tick  *(2.6*)
![](./img/2_6.png)
- 1.2 Almost a half of ticks (~44%) update only the best level of asks  

> [!IMPORTANT]  
> This also hints at probability of an **incoherent data nature**.  

- 2) An abnormal volume value is present at one tick in top ask level, which is >2 orders of magnitude higher than typical. The nature of this outlier is unclear, but a hypothesis is presented at part 3.  

<br>
<br>


# 2. Taking the data at face value

After all of the above considerations, leaving the inconsistent characteristics of the data aside, some "as-is" research showed the following.

- There's a chunk of 'BUY' trades which have volumes several orders of magnitude larger that the rest.  *(Plots below or 2.8)*

![](./img/2_8_tr.png)
![](./img/2_8_ask.png)
![](./img/2_8_bid.png)

- - Applying such large trades to present orderbook **would sweep off all 50 levels most of the time with overshoot** of 1-2 orders of magnitude *(2.9)*  

![](./img/2_9.png)

- - Level 50 is several times more distant from the TOB than the opposite TOB, so large trades should've been resulting in an extreme price swing, but this apparently does not happen in this dataset. And by taking a look at csv data, one can see that these large trades are not temporally clustered.  
- - - The plot below is included to show the potential magnitude of the price swing after a 50-level trade in relation to spread:

![](./img/2_9_1.png)

- - It seems highly unlikely that supposed removed levels would contain cumulative volume corresponding to these large trades if that was a 'natural' market.  

- - - This group of trades (>10<sup>8</sup> in size) looks like it's unrelated to orderbooks in majority of cases.  


- Even using Benford's law isn't necessary to see the unfitting nature of the bloated trades. And the number of points is rather insufficient to use this statistic anyways.  
(https://dn.institute/research/market-health/docs/benford/)


> [!IMPORTANT]  
> All of the above suggests that part of the trades data is either **constructed artificially or produced by manipulation**  
> Also **OB may have been tampered**


# 3. Hypothetic conclusion

It's unlikely to see some price manipulation technics (like pump-and-dump) on a large cap token-to-token pair. BTC and ETH are two largest cryptocurrencies, their prices have a lot of correlation (driven by general crypto market fear-greed mood).  

The pattern of the large trades does resemble **wash trading**, but to settle on this conclusion, the data are lacking corresponding opposite trades.  

There are still some hypotheses that can explain the pattern of the data without discarding its credibility.  
A small number of points can be explained by either:  
1) Tight rate-limited connection (if the data were collected in real-time) or postprocessing on the exchange side.  
2) Actual sparsity of the updates sent by some scam exchange.  

Both cases hint on the side of some scam activity - most probably faking the volumes traded so that the exchange looks more established than it actually is.  

Large trades then point to 'invisible' levels which happened to be posted and traded inbetween the orderbook ticks that are present. Also, a large ASK top-level outlier is explainable in this case - it has been taken almost immediately. (And somehow made it into the final dataset, which probably shouldn't have happened, if we suppose intentional manipulation).  

The lack of large SELL trades may be explained by a scheme where insiders made the reverse trade either on some other venue, or via an 'arbitrage triangle' - by selling the base asset for USDT (e.g.) and then buying the quote asset with those USDT. In the latter case, having data from the same venue for ETHUSDT and BTCUSDT would clarify the issue.  

<br>
<br>

- - -

Some further research may include comparing the data to historical klines for this instrument (from another venue), and also taking a look at prices of ETH and BTC in USDT. (Even though such comparison is only relevant for non-tampered data)