---
title: Day 4 of 100DaysOfCode - Getting Away from Token Addresses
date: 2021-04-13 00:05:16
tags: 100daysofcode
categories: development
---

Today was spent mostly trying to figure out how to navigate the CoinGecko API a bit more so I could use a token's symbol instead of its address. 

I thought this required first going through the entire list of coins on CoinGecko, looking for the platform, which then gave me the contract address if the token was on Ethereum. I ended up wasting a solid chunk of time because I messed up adding a flag to the end of the address:

```https://api.coingecko.com/api/v3/coins/list?include_platform=true```

That being said, this didn't really matter as I realized that I could instead be pulling the information from the [/simple/price... page](https://api.coingecko.com/api/v3/simple/price?ids=litecoin&vs_currencies=usd)? I'm not entirely sure what to call the different sections of the API. This also led to an issue as the id parameter wants the cryptocurrency's id, instead of its symbol. While writing out 'litecoin' isn't counter intuitive, it can get a little more messy with something like 'wrapped-leo.' Plus, I'd rather enter a token, and then figure out afterwards which API (PancakeSwap or CoinGecko) to pull from.

My convoluted solution involves going through the entire /coins/list, and pulling out the id based on what the user inputs for the symbol:

```python
coingecko_id_request = requests.get(('https://api.coingecko.com/api/v3/coins/list?include_platform=true'))
coingecko_id = coingecko_id_request.json()

for item in coingecko_id:
    if item['symbol'] == 'ltc':
        print(item['id'])
```

\>\>\> ```litecoin```

So that works, and now it's just a matter of plugging in 'litecoin': 

```python
coingecko_response = requests.get(('https://api.coingecko.com/api/v3/simple/price?ids=litecoin&vs_currencies=usd'))
coingecko_prices = coingecko_response.json()
print(coingecko_prices['litecoin']['usd'])
```

\>\>\> ```281.7```

Perfect! Now I just need to put it all together:

```python
def pull_coin_gecko_id():
    """Pulls the id after the user inputs a token."""
    coingecko_id_request = requests.get(('https://api.coingecko.com/api/v3/coins/list?include_platform=true'))
    coingecko_id = coingecko_id_request.json() 
    for crypto_ids in coingecko_id:
        if crypto_ids['symbol'] == token_input:
            print(crypto_ids['id'])
            coingecko_response = requests.get(('https://api.coingecko.com/api/v3/simple/price?ids=' + str(crypto_ids['id']) + '&vs_currencies=usd'))
            coingecko_prices = coingecko_response.json()
            print(coingecko_prices[crypto_ids['id']]['usd'])
```

So now I have working functions to pull a price from both PancakeSwap and CoinGecko based on user input. My next step is to try and separate out the input request as its own function. This is how I have it mapped out in my head:
1. System requests input.
2. User inputs 'ltc'/'btc'/'cub'
3. Function goes through and checks all coins listed on PancakeSwap (as the list is smaller than CoinGeckos)
4. If listed on PancakeSwap: return the price. Else: check CoinGecko.

APIs are bad ass.

I think this should cover almost every token someone would normally look up, but I guess that'll be something I'll confirm when I try to actually use it extensively.