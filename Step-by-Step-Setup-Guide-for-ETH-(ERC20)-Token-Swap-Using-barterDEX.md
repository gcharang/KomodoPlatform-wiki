**This guide will set you up with ETH swaps in BarterDEX. If you are not looking for non ETH based token swap, please follow [this installation guide](https://github.com/KomodoPlatform/KomodoPlatform/wiki/Installing-and-Using-Komodo-Platform-(barterDEX)). The project is still in development and highly experimental. It is for ETH testnet, but mainnet for the BTC protocol coins. This guide may change accordingly in the future. Make sure you are following the latest guide before reporting anything is broken.**

# Installation steps
Please follow all the steps by their order of appearance in this guide. If you get any error for any step and skip to the next, things will not work for you. If you have any questions or need help, head over to `#etomic` channel in Komodo Platform Slack and ask there.

## Install required dependencies
Install all the following dependencies. Without installing these, your `marketmaker` binary may crash and not work.

### Install gcc-7.2 & g++-7.2 and symlink them as gcc & g++
```shell
sudo add-apt-repository ppa:jonathonf/gcc-7.2
sudo apt-get update
sudo apt-get install gcc-7 g++-7
sudo rm /usr/bin/gcc && sudo rm /usr/bin/g++
sudo ln -s /usr/bin/gcc-7 /usr/bin/gcc
sudo ln -s /usr/bin/g++-7 /usr/bin/g++
```

### Install `LevelDB`
```shell
cd ~
git clone https://github.com/google/leveldb.git
cd leveldb
make  
sudo scp -r out-static/lib* out-shared/lib* /usr/local/lib/
cd include
sudo scp -r leveldb /usr/local/include/
sudo ldconfig
```

### Install `nanomsg`
```shell
cd ~
git clone https://github.com/nanomsg/nanomsg
cd nanomsg
cmake . -DNN_TESTS=OFF -DNN_ENABLE_DOC=OFF
make -j2
sudo make install
sudo ldconfig
```

## Copy the source repo and compile
### Clone `SuperNET` repo and compile `marketmaker` for ETH swaps
```shell
cd ~
git clone https://github.com/jl777/SuperNET
cd ~/SuperNET
git checkout etomic
git submodule update --init --recursive
mkdir build
cd build
cmake ..
cmake --build . --target marketmaker
```

### Copy `marketmaker` binary to `iguana` dir
The compiled `marketmaker` binary file can be found in `~/SuperNET/build/iguana/exchanges`. Copy `marketmaker` into `~/SuperNET/iguana/` dir.
```shell
cp ~/SuperNET/build/iguana/exchanges/marketmaker ~/SuperNET/iguana/marketmaker
```

## Install barterDEX & preparation
### Install, copy `passphrase` file from `exchanges` dir to `dexscripts` dir
The following command will install `barterDEX` and copy all the required API scripts in `~/SuperNET/iguana/dexscripts` dir. You need to issue all the required API scripts (e.g.: `client`, `run`, `orderbook`, `buy`, `sell`, `balance`, `stop`,) from `~/SuperNET/iguana/dexscripts` dir while running as normal after installation.

```shell
cd ~/SuperNET/iguana/exchanges
./install
cp passphrase ../dexscripts/passphrase
cd ~/SuperNET/iguana/dexscripts
nano passphrase
```

Enter a strong 24 words seed passphrase or WIF key or SHA256 of `userpass` or ETH private key in between "", save the file and close nano editor using `CTRL+X` then `Y` then `ENTER`.

### Edit the `client` script
You need to edit the `client` or `run` script to disable `git pull` & `./m_mm` commands. Otherwise, the commands will compile the `marketmaker` for non ETH/ERC20 token swap. After the changes, the file should look like similar to the following:

```shell
#!/bin/bash
source passphrase
source coins
./stop
#git pull;
cd ..; 
#./m_mm;
pkill -15 marketmaker; 
./marketmaker "{\"gui\":\"nogui\",\"client\":1, \"userhome\":\"/${HOME#"/"}\", \"passphrase\":\"$passphrase\", \"coins\":$coins}" &
```

### Run `marketmaker` using `client` script for the first time to get the `userpass` value
```shell
./client &
./setpassphrase
```
This should display the `userpass` value and your smartaddresses in console. Make a note of the `userpass`. Stop `maketmaker` using the following command before proceeding to the next step.
```shell
pkill -15 marketmaker
```
**The above command should always be used to stop `marketmaker` binary.**

### Copy the `userpass` file from `exchanges` dir to `dexscripts` dir and enter userpass value
```shell
cd ~/SuperNET/iguana/exhanges
cp userpass ../dexscripts/userpass
cd ~/SuperNET/iguana/dexscripts
nano userpass
```
Enter the passphrase we got earlier in between "", save the file and close nano editor using `CTRL+X` then `Y` then `ENTER`. All these scripts found in `~/SuperNET/iguana/dexscripts` are expecting a userpass file, which contains the definition of the $userpass variable (found inside scripts) to authenticate API access. This avoids evil webpages that try to issue port 7783 calls to steal your money.

## Run ETOMIC

You need to have ETOMIC running in native mode or electrum mode and KMD or other coins either running native or electrum (native is faster). If running native mode, make sure you have blockchain synced and seed passphrase / WIF key / private key imported into the chain.

For ETOMIC you need to have utxos. You can get free BEER from [this faucet](http://atomicexplorer.com/#/faucet) and swap it for ETOMIC in normal BarterDEX.  You also need to have ETH testnet coins as long as we are testing testnet. Use main-net ETH when testing main-net. If you are unsure which net to use, ask in Slack.

```shell
cd ~/SuperNET/iguana/dexscripts
./client &
./setpassphrase
```

Then, we need to enable ETH and other coins to start swapping. Use / add the following line into `enable` script and run in terminal to enable ETH.
```shell
curl --url "http://127.0.0.1:7783" --data "{\"userpass\":\"$userpass\",\"method\":\"enable\",\"coin\":\"ETH\"}"
```

Example `enable` script:
```shell
#!/bin/bash
source userpass
curl --url "http://127.0.0.1:7783" --data "{\"userpass\":\"$userpass\",\"method\":\"enable\",\"coin\":\"BEER\"}"
curl --url "http://127.0.0.1:7783" --data "{\"userpass\":\"$userpass\",\"method\":\"enable\",\"coin\":\"ETOMIC\"}"
curl --url "http://127.0.0.1:7783" --data "{\"userpass\":\"$userpass\",\"method\":\"enable\",\"coin\":\"PIZZA\"}"
curl --url "http://127.0.0.1:7783" --data "{\"userpass\":\"$userpass\",\"method\":\"enable\",\"coin\":\"KMD\"}"
curl --url "http://127.0.0.1:7783" --data "{\"userpass\":\"$userpass\",\"method\":\"enable\",\"coin\":\"BTC\"}"
curl --url "http://127.0.0.1:7783" --data "{\"userpass\":\"$userpass\",\"method\":\"enable\",\"coin\":\"CHIPS\"}"
curl --url "http://127.0.0.1:7783" --data "{\"userpass\":\"$userpass\",\"method\":\"enable\",\"coin\":\"ETH\"}"
```

And, rest of things are same as normal BarterDEX. More helpful info using the following links:
https://github.com/KomodoPlatform/KomodoPlatform/wiki
https://github.com/KomodoPlatform/KomodoPlatform/wiki/BarterDEX-API-Summary-by-Category
https://github.com/KomodoPlatform/KomodoPlatform/wiki/Enabling-Native-Wallet-Coins-for-Trading
https://github.com/KomodoPlatform/KomodoPlatform/wiki/Enabling-Electrum-Wallet-Coins
https://github.com/KomodoPlatform/KomodoPlatform/wiki/Electrum-servers-list
https://github.com/KomodoPlatform/KomodoPlatform/wiki/Trade