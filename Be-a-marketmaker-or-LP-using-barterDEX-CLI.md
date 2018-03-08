It is very easy to be a marketmaker or Liquidity Provider (LP) in barterDEX. Anyone can be LP using any supported coin and can provide liquidity for any pair in the market.

### Benefits of being an LP node:
* LP node / seller doesn't have to pay 0.15% DEX fee
* Can do margin trading

### Requirements:

1. barterDEX CLI installed - [guide](https://github.com/KomodoPlatform/KomodoPlatform/wiki/Installing-and-Using-Komodo-Platform-(barterDEX))
2. Native coin wallet(s) or Electrum server list (see below)


### APIs needed
You will need [`run`](https://github.com/KomodoPlatform/KomodoPlatform/wiki/barterDEX-API-Summary-by-Category#run), [`setpassprhase`](https://github.com/KomodoPlatform/KomodoPlatform/wiki/barterDEX-API-Summary-by-Category#setpassphrase), [`enable`](https://github.com/KomodoPlatform/KomodoPlatform/wiki/barterDEX-API-Summary-by-Category#enable) or [`electrum`](https://github.com/KomodoPlatform/KomodoPlatform/wiki/barterDEX-API-Summary-by-Category#electrum) & [`autoprice`](https://github.com/KomodoPlatform/KomodoPlatform/wiki/barterDEX-API-Summary-by-Category#autoprice) only to be a marketmaker or LP in barterDEX. All the API usage explained below as steps.

### Steps:

#### run
Use `./run &` to start barterDEX as LP mode. You can use `./client &` but, better to use `run`, which is specifically to start as marketmaker or LP. Don't change the file contents unless you know what you are doing.

#### setpassphrase
This API will set your passphrase and let you use the userpass value in every script. This is the second API/script you need to run.

#### enable or electrum
If you are planning to use native mode, coin daemons need to be installed, blockchain fully synced if using native. Native is recommended for faster performance and reliability. Alternatively, you can use electrum which does not require blockchain to be downloaded.

Edit the `enable` script with the coin names you want to activate for trading. Or, edit the `electrum` script with [list of electrum servers](http://pad.supernet.org/electrum-servers) to activate coins in electrum mode.

#### autoprice
This is the most important API for LP or marketmaker. You need to edit this file to your liking. This contains the price, coin name for any pair your are making. Check [here](https://github.com/KomodoPlatform/KomodoPlatform/wiki/barterDEX-API-Summary-by-Category#autoprice) for more info on the api.

Once you issue the `./autoprice` script, you just need to wait for alice/buyer to buy from your order. All the trade info will be shown in your console as they are happening.

## Private Trading using BarterDEX
You can use BarterDEX using 14,000+ [`netid`](https://github.com/KomodoPlatform/KomodoPlatform/wiki/BarterDEX-API-Summary-by-Category#netid). For private trading, both LP node and client need to use a same `netid` and `seednode`. It is better to choose higher number netid (i.e.: 1000+). 

#### LP Node
You need to edit your `run` and `setpassphrase` with same `netid` and `seednode` like `\"netid\":999,\"seednode\":\"51.255.10.25\"` and follow the steps above. Your orders will not be visible in the global netid and orderbook. Only users who are connected to that specific netid on that ip can perform swaps. Check the example file below:

**run**
```shell
#!/bin/bash
source passphrase
source coins
./stop
git pull;
cd ..; 
./m_mm;
pkill -15 marketmaker; 
stdbuf -oL $1 ./marketmaker "{\"gui\":\"nogui\", \"profitmargin\":0.01, \"netid\":999,\"seednode\":\"51.255.10.25\",\"userhome\":\"/${HOME#"/"}\", \"passphrase\":\"$passphrase\", \"coins\":$coins}" &
```

**setpassphrase**
```shell
#!/bin/bash
source userpass
source passphrase
curl --url "http://127.0.0.1:7783" --data "{\"userpass\":\"1d8b27b21efabcd96571cd56f91a40fb9aa4cc623d273c63bf9223dc6f8cd81f\",\"method\":\"passphrase\",\"passphrase\":\"$passphrase\",\"netid\":999,\"seednode\":\"51.255.10.25\",\"gui\":\"nogui\"}"
```

#### Client Node
You need to edit your `client` and `setpassphrase` with same `netid` and `seednode` like `\"netid\":999,\"seednode\":\"51.255.10.25\"` as the LP node. Check the example files below:

**client**
```shell
#!/bin/bash
source passphrase
source coins
./stop
git pull;
cd ..; 
./m_mm;
pkill -15 marketmaker; 
./marketmaker "{\"gui\":\"nogui\",\"client\":1,\"netid\":999,\"seednode\":\"51.255.10.25\", \"userhome\":\"/${HOME#"/"}\", \"passphrase\":\"$passphrase\", \"coins\":$coins}" &
```

**setpassphrase**
```shell
#!/bin/bash
source userpass
source passphrase
curl --url "http://127.0.0.1:7783" --data "{\"userpass\":\"1d8b27b21efabcd96571cd56f91a40fb9aa4cc623d273c63bf9223dc6f8cd81f\",\"method\":\"passphrase\",\"passphrase\":\"$passphrase\",\"netid\":999,\"seednode\":\"51.255.10.25\",\"gui\":\"nogui\"}"
```
