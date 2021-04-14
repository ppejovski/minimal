---
title: Day 3 of 100DaysOfCode - Continuing the Tracker
date: 2021-04-12 01:15:51
tags:
---

Having [the blog](https://phil.mk) up on Hexo ready to go, the focus of today was spent mostly on the cryptocurrency portfolio tracker.

I have found myself throwing money into a bunch of random DeFi projects, which means that keeping track of all of these various tokens and pools that I've joined a bit more difficult.

Thankfully, the PancakeSwap API makes it fairly easy to pull up almost any token that is out there, assuming it's listed on PancakeSwap.

I ended up spending some extra time on the initial search for a token, such as allowing the user to input 'tokens' to see a list of everything listed on PancakeSwap, and adding some error handling to make sure that the user is typing in the name of a token correctly. Eventually, I realized that it would make more sense to create functions, as it was becoming a bit of a mess to navigate.

Here's what I had before I ended up starting over and going down the function route:
```python
x = True

while x == True:
    try: 
        token_price = input("\nWhich token did you want to check the price? For example: CUB.\n")
        print(f"The price of ${token_price} is currently {current_prices['prices'][str(token_price.upper())]}.\n")
    except KeyError:
        if token_price == 'tokens':
            ltrs = input("This is a long list. You can type in a letter to view all tokens that start with that letter, or simply press Enter or Return to view the entire list.")
            if ltrs.isalpha():
                for tkns in current_prices['prices']:
                    if tkns[0] == ltrs:
                        print(tkns)
            else:
                for tkns in current_prices['prices']:
                    print(tkns)
        elif token_price == 'quit':
            break
        
        else:
            print(f"{token_price} doesn't seem to be a valid token. Please try again. For a list of tokens currently on PancakeSwap, type 'tokens'.")
            pass
```

Unfortunately, the PancakeSwap API managed to go down on me, but I was able to get this far:


```python
def price_check_BSC():
    """Checks the current price of a BSC token on PancakeSwap.""" 
    token_name = input("What token would you like to check the price of today? As an example, you can enter 'CUB'.")
    pancakeswap_response = requests.get('https://api.pancakeswap.finance/api/v1/price')
    pancakeswap_prices = pancakeswap_response.json()  
    token_price = pancakeswap_prices['prices'][str(token_name.upper())]
    print(f"The value of {token_name} is currently {token_price}.")
```
I'm planning on having token_name be its own function, which would make sure that the user input is correct, and handles any errors that come out.


The CoinGecko API includes far more information than the PancakeSwap API ([WLEO example](https://api.coingecko.com/api/v3/coins/ethereum/contract/0x73A9fb46e228628f8f9BB9004eCa4f4F529D3998)), which made finding the price a bit more difficult. My first step was to use the keys() method on the call, to figure out how exactly this information is organized. I was viewing this in my web browser, but maybe there's a way to have a cleaner view. For now, this did get the job done.

```python
print(coingecko_prices.keys())
```

```python 
dict_keys(['id', 'symbol', 'name', 'asset_platform_id', 'platforms', 
'block_time_in_minutes', 'hashing_algorithm', 'categories', 'public_notice',
'additional_notices', 'localization', 'description', 'links', 'image',
'country_origin', 'genesis_date', 'contract_address',
'sentiment_votes_up_percentage', 'sentiment_votes_down_percentage',
'market_cap_rank', 'coingecko_rank', 'coingecko_score', 'developer_score', 
'community_score', 'liquidity_score', 'public_interest_score', 'market_data', 
'community_data', 'developer_data', 'public_interest_stats', 'status_updates', 
'last_updated', 'tickers'])
```

Eventually, I was able to find the current price within CoinGeckos API by going through market_data > current_price > usd. 

```python
def price_check_CG():
    """Checks the current price of a token on CoinGecko."""
    token_address_request = input("What token would you like to check the price of today? As an example, you can type'0x73A9fb46e228628f8f9BB9004eCa4f4F529D3998' for WLEO.")
    coingecko_response = requests.get(('https://api.coingecko.com/api/v3/coins/ethereum/contract/' + str(token_address_request)))
    coingecko_prices = coingecko_response.json()
    token_name = coingecko_prices.get('symbol')
    token_price = coingecko_prices['market_data']['current_price']['usd']
    print(f"The value of {token_name.upper()} is currently {token_price}.")
```
Similar to the BSC version, I would like to have token_name be its own function. In addition, trying to get contract addresses can be a hassle, so I'd rather be able to type 'WLEO' instead of '0x73A9fb46e228628f8f9BB9004eCa4f4F529D3998' and get the correct price. My fairly basic 'fix' is to pick the coin with the highest market cap that matches the symbol that the user inputs.


Some useful links I came across today:
- [Handling KeyErrors in Python](https://realpython.com/python-keyerror/)
