---
title: Day 5 of 100DaysOfCode - Handling Inputs
date: 2021-04-16 09:14:54
tags: 100daysofcode
categories: development
---

Today (and yesterday) was spent almost entirely on figuring out how to manage the input side of things, as different APIs provide different information that is organized in different ways. 

This is what I ended up with:
```python
def token_symbol_request():
    """Requests user input for the symbol of a token."""
    token_name = input("What token would you like to check the price of today? As an example, you can enter 'CUB'.")
    pancakeswap_request = requests.get('https://api.pancakeswap.finance/api/v1/price')
    pancakeswap_prices = pancakeswap_request.json()
    coingecko_id_request = requests.get(('https://api.coingecko.com/api/v3/coins/list?include_platform=true'))
    coingecko_id = coingecko_id_request.json()
    try:
        print("Checking PancakeSwap...")
        pancakeswap_prices['prices'][token_name.upper()]
        price_check_BSC()
    except KeyError:
        print("Checking CoinGecko...")
        try:
            for crypto_dicts in coingecko_id:
                if crypto_dicts['symbol'] == token_name.lower():
                    price_check_id() 
        except KeyError:
            print("Are you sure that's the correct symbol?")
```

The gist of what's going on here:
![](blog-post-2021-04-16-09-40-08.jpg)


This is pretty good so far: now I can call one function, token_symbol_request(), and then that'll route to the correct API to pull the price information from. I realized that it's probably not worth calling the API every single time I would like to check where the coin is listed, so I plan to save a copy of the list locally. Ideally, I can call a function whenever it's needed and save an updated copy to include any new listings. 

I would like my next step to be thinking about the actual portfolio itself. Specifically: how the portfolio will be organized, stored (I'll have to figure out how to write to a file!), and modified. Before doing that though, I have to change the functions that check prices to have them pull the user input from token_symbol_request(). Currently, the correct API is called, but then it requests the user to input the token again, which seems a little silly. 

I'm pretty happy with the progress so far. This project has been a good learning experience, and I expect that to continue (and probably get more difficult) as time goes on.