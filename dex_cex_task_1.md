# 1)  
For abstraction, let's assume the naming for ETH/USDT pair as 'asset' and 'currency'  
According to primary formulations from part 1 of  
https://github.com/runtimeverification/verified-smart-contracts/blob/uniswap/uniswap/x-y-k.pdf  
We can derive that the price of the asset changes proporionally to currency/asset ratio (see Δx and Δy)  
When the amount of $x$ changes, e.g. $x_1 = qx_0$, the price changes quadratically w.r.t $q$ (Since $y_1 = y_0 / q$ and price of $y$ is $\Delta x/\Delta y$ which proportional to $x/y$)  
Inversely, the asset/currency ratio is related to price change as a square root  
Initial investment value:

$$V_0 = x_0 + y_0 \times p_0 (= 2 \times x_0)$$

Where $x$ is amount of USDT, $y$ - ETH, $p$ - price  
The amount of $x$ rises with the price, $y$ - inversely. Let's denote **dimensionless price change** as $P$ ($= p' / p_0$; where $p'$ - new price):

$$x_1 = x_0 \times \sqrt{P}; \ \ \ \ \ \ y_1 = y_0 / \sqrt{P}$$

Then the investment value as a function of price:

$$V = x_0 \sqrt{P} + y_0 p_0 \frac{P}{\sqrt{P}} = \sqrt{P} (x_0 + y_0 p_0) = 2 x_0 \sqrt{P}$$

Derivative of that w.r.t. dimensionless price:

$$\frac{dV}{dP} = \frac{x_0}{\sqrt{P}}$$


At entry, $P = 1$, so the derivate is equivalent to the investment of $y_0$ ETH

This corresponds to the plot at:
https://hacken.io/discover/liquidity-pools/#h-anchor-7


**Answer 1:**

One should short **the same amount of ETH** that was put in the pool

<br />

- - -

<br />

# 2)

**Answer 2:**

Other costs to consider:

1) Perp funding — funding paid/received to maintain the short (cumulative funding cost can dominate over long horizons).

2) Uniswap fees earned

3) Impermanent loss (IL) — LP suffers IL vs. hold when price moves, which is the differnce between investment values in case of holding versus providing liquidity:  
$\quad \text{IL} = 2x_0 \sqrt{P} - (x_0 + x_0 P)$; (in this form it's non-positive)

4) Entry costs and slippage — entering LP and executing the perp short; on DEX this includes gas/DEX slippage; on CEXs, taker fees.

5) Price divergence — mark price on the CEX vs the pool price (and possible temporal difference between entries in case of manual hedging)

<br />

- - -

<br />

# 3)

In case of v3, we have virtual reserves that 'prop up' our investment.  
The price bracket for which the liquidity is provided defines the amount put in the pool.  
(The higher the upper bound for price of the asset, the more of it should be put. The lower the bound - the more currency)  

## Answer 3.1 (intuitive, found by Nov 10)  
  
As in https://app.uniswap.org/whitepaper-v3.pdf Figure 2, the hyperbolic curve is shifted without changing its shape.  
But the derivative remains the same. (Increasing P means moving along the curve in direction of x amount rising)  
Meaning one should short **the same amount of ETH** that was put in the pool.  
(Of course, with the same amount put in the defined price range, we'd have a completely different liquidity profile compared to v2, and about 20 times more fees earned. Also the hedging would be exposed to much higher risks.)

## Solution 3.2 (rigorous, Nov 19)

Liquidity profile is defined by at least 4 variables:
- Initial price $p_0$, lowest supported price $p_a$, highest supported price $p_b$
- One of the following amounts: $x_0$, $y_0$, or $V_0$

Let's denote additional 'virtual' reserves as $X$ and $Y$. The v3 equation for amounts is: $(x_0 + X)(y_0 + Y) = k$ (const)  
Parallel to v2, when the x part grows $\sqrt{P}$ times, the other part shrinks reverse proportionally:

$\quad (x' + X) = (x_0 + X)\sqrt{P}; \ \ \ \ \ \  (y' + Y) = \frac{(y_0 + Y)}{\sqrt{P}}$

From this we can derive equations for $X$ and $Y$ depending on actual reserves put - when the price reaches the bound, real reserves become depleted:  

$\quad (x_0 + X)\sqrt{P_a} = X; \ \ \ \ \ \  y_0 + Y = Y\sqrt{P_b}$; (here we use dimensionless price)


From this:  

$\quad X = \frac{x_0}{\frac{1}{\sqrt{P_a}} - 1}; \ \ \ \ \ \  Y = \frac{y_0}{\sqrt{P_b} - 1}$


Let's denote $x_0 + X$ as $\tilde{X_0}$, and $y_0 + Y$ as $\tilde{Y_0}$. Then:  

$\quad x(P) = \tilde{X_0}(\sqrt{P} - \sqrt{P_a}) ;  \ \ \ \ \ \  y(P) = \tilde{Y_0}(\frac{1}{\sqrt{P}} - \frac{1}{\sqrt{P_b}})$

We should also note the symmetry at entry, when taking virtual reserves into account. This provides relation between x and y parts of the overall value:  

$$x_0 + X = \frac{x_0}{1 - \sqrt{P_a}} = \tilde{X_0} = \tilde{Y_0}  p_0 = (y_0 + Y)  p_0 = p_0 y_0  \frac{1}{1 - \frac{1}{\sqrt{P_b}}} $$
<br>

Also, let's tie the initial investment value $V_0$ to $y_0$:

$$V_0 = y_0 p_0 + x_0 = y_0 p_0 (1 + \frac{1 - \sqrt{P_a}}{1 - \frac{1}{\sqrt{P_b}}}) $$


In order to find the hedge amount for delta-neutrality, let's find the dependency of overall value $V$ on dimensionless price $P$:

$$ V(P) = \tilde{X_0}  \sqrt{P} - X + (\frac{\tilde{Y_0}}{\sqrt{P}} - Y) p' = \tilde{X_0} \sqrt{P} + \tilde{Y_0}  p_0  \sqrt{P} - X - Y P p_0 $$

(note: $p' = P \times p_0$ by definition of $P$ )  

A linear negative term appears here, apparently it accounts for the y reserves that we haven't put, basically 'virtual' reserves of y multiplied by the price at entry - when the price goes up, we are not receiving returns from them (unlike v2 case), hence the negative term.

Derivative:

$$ \frac{dV}{dP} = \frac{x_0 + X}{2 \sqrt{P}} + \frac{(y_0 + Y) p_0}{2 \sqrt{P}} - p_0 Y $$

At entry $P = 1$, so this turns into:

$$ \frac{dV}{dP}(P = 1) = \frac{(x_0 + y_0 p_0)}{2} + \frac{(X - Y p_{0})}{2}$$

<br>

Interestingly, the first term here is equivalent to the derivative in case of v2.  
The second seems to be accounting for asymmetry between values of currency and asset put at entry.  

If we take into account the symmetry of virtualized reverves: $x_0 + X = p_0 (y_0 + Y)$, this can be simplified as:  

$$ \frac{dV}{dP}(P = 1) = y_0 p_0 \frac{\sqrt{P_b / P} - 1}{\sqrt{P_b} - 1} $$

Surprisingly, there's no dependency on $P_a$ and correspondingly - propotion of virtual reserves $X / \tilde{X_0}$.  
But that makes sense since we formulate everything in terms of currency, and currency value is obviously linear, the constant shift between x and $\tilde{X}$ which is X is not affected by the price of the asset, so it doesn't play a role in derivative.  

<br>

### Answer 3.2 

When P = 1, this simplifies to just $y_0 p_0$, meaning again, that one should short **the same amount of ETH** that was put in the pool.

In terms of $V_0$:  
$\quad y_0 =  V_0 / p_0 / (1 + \frac{1 - \sqrt{P_a}}{1 - 1 / \sqrt{P_b}}) $  
Since $P_b$ * $P_a \neq 1$, the entry won't be 50/50, it actually is around 47.56% / 52.44% (ETH / USDT, meaning shorting around 0.4756 of overall value invested)

<br>
<br>


- - -
<br>

*Personal note*: that was an interesting task as I wasn't familiar with Uniswap and barely knew anything specific about DEXs.  
The journey of finding an answer to question 3, doubting it and coming round to the same answer again was really surprising.  
At some point I supposed that my initial answer is only correct in the case of symmetrical entry: e.g. $P_b$ * $P_a = 1$
(Also I can send a few photos of handwritten way of thought that I went through to get all the equations included here)