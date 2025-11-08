
Liquidity pools at their core can be derived from 2 basic principles:
1) The pool is collected at 50/50 ratio
2) The price follows the current assets ratio, (or more specifically - inverse of the price)

For abstraction, let's assume the naming for ETH/USDT pair as 'asset' and 'currency'
According to primary formulations from part 1 of
https://github.com/runtimeverification/verified-smart-contracts/blob/uniswap/uniswap/x-y-k.pdf
We can derive that the price of the asset changes proporionally currency/asset ratio (see Δx and Δy)
When the amount of x changes, e.g. x<sub>1</sub> = kx<sub>0</sub>, the price changes quadratically w.r.t k (Since y<sub>1</sub> = y<sub>0</sub> / k and price of y is Δx/Δy)
Inversely, the asset/currency ratio is related to price change as a square root
Initial investment value:

V<sub>0</sub> = x<sub>0</sub> + y<sub>0</sub> × p<sub>0</sub> ( = 2 × x<sub>0</sub> );

Where x is amount of USDT, y - ETH, p - price
The amount of x rises with the price, y - inversely. Let's denote dimensionless price change as P (= p' / p<sub>0</sub>):

x<sub>1</sub> = x<sub>0</sub> × √P; y<sub>1</sub> = y<sub>0</sub> / √P

Then the investment value as a function of price:

V = x<sub>0</sub> × √P + y<sub>0</sub> × p<sub>0</sub> × P / √P  = √P × (x<sub>0</sub> + y<sub>0</sub> × p<sub>0</sub>) = 2 × x<sub>0</sub> × √P

Derivative of that w.r.t. dimensionless price:

dV/dP = x<sub>0</sub> / √P

At entry, P = 1, so the derivate is equivalent to the investment of y<sub>0</sub> ETH

This corresponds to the plot at:
https://hacken.io/discover/liquidity-pools/#h-anchor-7

Answer 1:

One should short the same amount of ETH that was put in the pool


Answer 2:

Other costs to consider:

1) Perp funding — funding paid/received to maintain the short (cumulative funding cost can dominate over long horizons).

2) Uniswap fees earned

3) Impermanent loss (IL) — LP suffers IL vs. hold when price moves:
IL = 2 x<sub>0</sub> × √P - (x<sub>0</sub> + x<sub>0</sub> P)

4) Entry costs and slippage — entering LP and executing the perp short; on DEX this includes gas/DEX slippage; on CEXs, taker fees.

5) Price divergence — mark price on the CEX vs the pool price (and possible temporal difference between entries in case of manual hedging)

Answer 3:

In case of v3, we have virtual reserves that 'prop up' our investment.
With symmetrical entry that is stated in the task (+10%; -10%; ln(P<sub>b</sub>) = - ln(P<sub>a</sub>)) we still have 50/50 ratio and the derivative profile remains the same.
As in https://app.uniswap.org/whitepaper-v3.pdf Figure 2, the hyperbolic curve is shifted without changing its shape.
Meaning one should short the same amount of ETH that was put in the pool
(Of course, with the same amount put, we'd have a completely different liquidity profile compared to v2, and about 20 times more fees earned)


Personal note: that was an interesting task as I wasn't familiar with Uniswap and barely knew anything specific about DEXs