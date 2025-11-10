
To download Uniswap V3 data The Graph service was used (thegraph.com/explorer)  
It requires obtaining a personal API key which in turn requires to sign in with a wallet.  
The API key should be inserted into variable GRAPH_API_KEY to recreate the data download phase.  

As per:  
https://rareskills.io/post/uniswap-v3-sqrtpricex96  
Uniswap v3 stores the square root of the price, and uses it to calculate reserves.  
The price which can be calculated as amount0/amount1 displays the real cost that includes fee.  

Some blocks in the notebook are auxillary and can be skipped: plots and those that are named with "==="  

As no coinciding hours with out-of-band prices where found for two venues, I've decided to paste the min/max prices column values for each venue regardless of the other.  
(Because following the task strictly would mean placing no values at all.  
The task description, if not ambiguous, is somewhat unclear on what prices to use, so I used all prices within the hour of interest to calculate min/max.  
Maybe only the relevant trades prices should've been used - this can be relatively easily adjusted in the code)


Personal note: Whew, what a tricky thing was to manage downloading data from Subgraph.
It helped that I already had an empty wallet that allowed signing in and receiving an API key.
(Though the proccess itself of signing in was the most surprising part)