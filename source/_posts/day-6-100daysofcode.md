---
title: Day 6 of 100DaysOfCode - Sorta Kinda Starting Over
date: 2021-04-29 20:35:43
tags:
---

Wow, it is a little ridiculous to think that it has been two entire weeks since my last post! Around two weeks ago, my job decided to express how much they appreciate me by scheduling me to work between 60 and 70 hours until the end of May. Being salaried in the US means that overtime pay is some sort of funny joke, but I digress. You should know what you're signing up for when you're going into public accounting. I've been throwing a couple of job applications out there, and have started hearing back already, so 🤞 that I can go to working 'normal' hours sooner rather than later.

On the upside, PancakeSwaps V2 migration came along with an upgrade to their API, which decided to not work right out of the gate. So anything PancakeSwap related would have probably broken anyway. Ultimately, I decided to just drop what I had so far, and start to use CoinGeckos API exclusively with the platform and contract address provided. 

While this is a little less user friendly than simply typing in "CUB," I plan to add the ability to add a token to your favorites, which could then be referenced by typing in the name instead of the address. Including a list of the most popular coins wouldn't be a bad idea either.

My new set up asks the user if the token is on Ethereum, or BSC. I plan to add support for tokens on their own native blockchains soon enough.

```python
def token_type_request(network_type):
    """Requests user to input the type of token."""
    platform_request = None
    
    if network_type != None:
        platform_request = network_type
        if platform_request.upper() == 'ERC20':
            return 'ethereum'
        elif platform_request.upper() == 'BEP20':
            return 'binance-smart-chain'

    while platform_request != 'ERC20' or 'BEP20':
        platform_request = input("Is your token 'ERC20', or 'BEP20'?")
        if platform_request.upper() == 'ERC20':
            return 'ethereum'
        elif platform_request.upper() == 'BEP20':
            return 'binance-smart-chain'
```

This is followed by a request for the contract address, which is then plugged directly into the Coingecko API url.

```python
coingecko_response = requests.get(f'https://api.coingecko.com/api/v3/coins/{network_type}/contract/{contract_address}')
```

One thing I've been trying to think about is how to go about organizing the 'Favorites' dictionary. This is what I have so far:

```python
token_favorites = {
    1: {
        "token name": "WLEO",
        "network": "ERC20",
        "token contract address": "0x73a9fb46e228628f8f9bb9004eca4f4f529d3998",
    },
    2: {
        "token name": "CUB",
        "network": "BEP20",
        "token contract address": "0x50d809c74e0b8e49e7b4c65bb3109abe3ff4c1c1",
    }
}
```

I can't tell if this actually a good way to go about it. Having integers as the key means that it's really easy to iterate through the entire list with a for loop:
```
for k in token_favorites:
    print(token_favorites[k]['token name'])
    print(favorites_price_request(token_favorites[k]['network'],token_favorites[k]['token contract address']))
```
![](blog-post-2021-04-29-20-49-25.jpg)

Does anyone have any thoughts on the organization of the favorites dictionary? There is probably a better way to go about it than what I have currently, but at least it works.

Anyway, that's where we're at currently! I have a pretty busy next couple of days, but I'll try to squeeze some more coding in whenever I can find some of that free time. Maybe a break from price charts would be a good place to start 😉.


