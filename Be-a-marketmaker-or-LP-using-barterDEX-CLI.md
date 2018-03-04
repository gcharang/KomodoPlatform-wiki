It is very easy to be a marketmaker or Liquidity Provider (LP) in barterDEX. Anyone can be LP using any supported coin and can provide liquidity for any pair in the market.

### Benefits of being an LP node:
* LP node / seller doesn't have to pay 0.15% DEX fee
* Can do margin trading

### Requirements:

1. barterDEX CLI installed - [guide](https://github.com/KomodoPlatform/KomodoPlatform/wiki/Installing-and-Using-Komodo-Platform-(barterDEX))
2. Native coin wallet(s)

### APIs needed
You will need `run`, `setpassprhase`, `enable` or `electrum` & `autoprice` only to be a marketmaker or LP in barterDEX. All the API usage explained below as steps.

### Steps:

#### run
Use `./run &` to start barterDEX as LP mode. You can use `./client &` but, better to use `run`, which is specifically to start as marketmaker or LP. Don't change the file contents unless you know what you are doing.

#### setpassphrase
This API will set your passphrase and let you use the userpass value in every script. This is the second API/script you need to run.

#### enable or electrum
If you are planning to use native mode, coin daemons need to be installed, blockchain fully synced and passphrase imported if using native. Native is recommended for faster performance and reliability. Alternatively, you can use electrum which does not require blockchain to be downloaded.

Edit the `enable` script with the coin names you want to activate for trading. Or, edit the `electrum` script with list of electrum servers to activate coins in electrum mode.

#### autoprice
This is the most important API for LP or marketmaker. You need to edit this file to your liking. This contains the price, coin name for any pair your are making. Check [here](https://github.com/KomodoPlatform/KomodoPlatform/wiki/barterDEX-API-Summary-by-Category#autoprice) for more info on the api.

Once you issue the `./autoprice` script, you just need to wait for alice/buyer to buy from your order. All the trade info will be shown in your console as they are happening.