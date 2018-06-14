It is very easy to be a marketmaker or bob in barterDEX. Anyone can be bob using any supported coin and can provide liquidity for any pair in the market.

### Benefits of being an LP node:
* Bob node / marketmaker doesn't have to pay 0.15% DEX fee
* Can do margin trading

### Requirements:

1. barterDEX CLI installed - [guide](https://github.com/KomodoPlatform/KomodoPlatform/wiki/Installing-and-Using-Komodo-Platform-(barterDEX))
2. Native coin wallet

### APIs needed
You will need [`client`](https://github.com/KomodoPlatform/KomodoPlatform/wiki/barterDEX-API-Summary-by-Category#client), [`setpassprhase`](https://github.com/KomodoPlatform/KomodoPlatform/wiki/barterDEX-API-Summary-by-Category#setpassphrase), [`enable`](https://github.com/KomodoPlatform/KomodoPlatform/wiki/barterDEX-API-Summary-by-Category#enable) & [`autoprice`](https://github.com/KomodoPlatform/KomodoPlatform/wiki/barterDEX-API-Summary-by-Category#autoprice) only to be a marketmaker or bob in barterDEX. All the API usage explained below as steps.

### Steps:

#### client
Use `./client &` to start barterDEX. Do **NOT** use `./run` unless you have reliable fast connection from a datacenter or VPS. Don't change the file contents unless you know what you are doing.

#### setpassphrase
This API will set your passphrase and let you use the userpass value in every script. This is the second API/script you need to run.

#### enable
For native mode coin daemons need to be installed, blockchain fully synced if using native. Native is recommended for faster performance and reliability. Edit the `enable` script with the coin names you want to activate for trading.

**[Note: `electrum` is not recommended to setup an bob node. It will create connectivity issues and you may run into troubles.]**

#### autoprice
This is the most important API for bob or marketmaker. You need to edit this file to your liking. This contains the price, coin name for any pair your are making. Check [here](https://github.com/KomodoPlatform/KomodoPlatform/wiki/barterDEX-API-Summary-by-Category#autoprice) for more info on the API.

Once you issue the `./autoprice` script, you just need to wait for alice/buyer to buy from your order. All the trade info will be shown in your console as they are happening.

## Private Trading using BarterDEX
You can use BarterDEX using 14,000+ [`netid`](https://github.com/KomodoPlatform/KomodoPlatform/wiki/BarterDEX-API-Summary-by-Category#netid). For private trading, both bob node and client need to use a same `netid` and `seednode`. It is better to choose higher number netid (i.e.: 1000+). 

#### Bob Node
You need to edit your `client` and `setpassphrase` with same `netid` and `seednode` like `\"netid\":1024,\"seednode\":\"51.255.10.25\"` and follow the steps above. Your orders will not be visible in the global netid and orderbook. Only users who are connected to that specific netid on that ip can perform swaps. Check the example file below:

**client**
```shell
#!/bin/bash
source passphrase
source coins
./stop
git pull;
cp ../exchanges/updateprices .;./updateprices
cd ..; 
./m_mm;
pkill -15 marketmaker; 
stdbuf -oL $1 ./marketmaker "{\"gui\":\"nogui\",\"client\":1,\"netid\":1024,\"seednode\":\"51.255.10.25\",\"canbind\":1, \"userhome\":\"/${HOME#"/"}\", \"passphrase\":\"$passphrase\", \"coins\":$coins}" &
```

**setpassphrase**
```shell
#!/bin/bash
source userpass
source passphrase
curl --url "http://127.0.0.1:7783" --data "{\"userpass\":\"1d8b27b21efabcd96571cd56f91a40fb9aa4cc623d273c63bf9223dc6f8cd81f\",\"method\":\"passphrase\",\"passphrase\":\"$passphrase\",\"netid\":1024,\"seednode\":\"51.255.10.25\",\"gui\":\"nogui\"}"
```

#### Alice Node
You need to edit your `client` and `setpassphrase` with same `netid` and `seednode` like `\"netid\":1024,\"seednode\":\"51.255.10.25\"` as the bob node. Check the example files below:

**client**
```shell
#!/bin/bash
source passphrase
source coins
./stop
git pull;
cp ../exchanges/updateprices .;./updateprices
cd ..; 
./m_mm;
pkill -15 marketmaker; 
stdbuf -oL $1 ./marketmaker "{\"gui\":\"nogui\",\"client\":1,\"netid\":999,\"seednode\":\"51.255.10.25\", \"userhome\":\"/${HOME#"/"}\", \"passphrase\":\"$passphrase\", \"coins\":$coins}" &
```

**setpassphrase**
```shell
#!/bin/bash
source userpass
source passphrase
curl --url "http://127.0.0.1:7783" --data "{\"userpass\":\"1d8b27b21efabcd96571cd56f91a40fb9aa4cc623d273c63bf9223dc6f8cd81f\",\"method\":\"passphrase\",\"passphrase\":\"$passphrase\",\"netid\":1024,\"seednode\":\"51.255.10.25\",\"gui\":\"nogui\"}"
```
