### What is Full Relay (FR) node?

Full Relay node (FR) creates the p2p network & only relays packets for BarterDEX and allows ordermatch to happen. They don't trade. FR nodes never touch any funds of any sort and are just a bulletin board. 0 balance needed to run an FR node. Bob and Alice connect to the FR network. Bob places orders and Alice fills the orders. It is ideal to have at least 3 FR nodes in each netid.

### How to setup an FR node?

It is just `marketmaker` launched with `client:0` parameter. 

#### Install the following dependency packages:

```shell
sudo apt-get update
sudo apt-get install cmake git libcurl4-openssl-dev build-essential
```

#### Install `nanomsg`

```shell
cd ~
git clone https://github.com/nanomsg/nanomsg
cd nanomsg
cmake . -DNN_TESTS=OFF -DNN_ENABLE_DOC=OFF
make -j2
sudo make install
sudo ldconfig
```
#### Clone the SuperNET repo from github & Install:
Clone the repo, checkout dev branch for latest and install.

```shell
cd ~
git clone https://github.com/jl777/SuperNET
cd ~/SuperNET/iguana/exchanges
git checkout dev
./install
```
#### Copy the `passphrase` file & using seed passphrase

From the same dir in terminal type the following commands to copy the `passphrase` file to `~/SuperNET/iguana/dexscripts` dir and add a strong 24 words seed passphrase in between `""`. Save your passphrase properly without changing any word or space. Same seed **passphrase** will always show you the same **smartaddress**.

```shell
cp passphrase ../dexscripts/passphrase
cd ../dexscripts
nano passphrase
```
Enter your passphrase:

`export passphrase="<put a very strong passphrase here>"`

Press `CTRL+X` then `Y` then `ENTER` to save the file and exit from Nano editor.

#### Edit the `client` script
The client script should look like below to be running an FR node.

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
stdbuf -oL $1 ./marketmaker "{\"gui\":\"nogui\",\"client\":0, \"userhome\":\"/${HOME#"/"}\", \"passphrase\":\"$passphrase\", \"coins\":$coins}" &

```

#### Getting the `userpass` value

All these scripts are expecting a `userpass` file, which contains the definition of the `$userpass` variable (found inside scripts) to authenticate API access. At first you may not know the value of `userpass`. To find out, just run the `client` script first (as instructed below) and then run `./setpassphrase`. You will notice your `userpass` value at the top of output and you can copy that value and put it into `~/SuperNET/iguana/dexscripts/userpass` file. If you don't, all subsequent API calls will get authorisation error.

Open a new terminal and type the following:

```shell
cd ~/SuperNET/iguana/dexscripts
./client &
./setpassphrase
pkill -15 marketmaker
```
#### Edit the `userpass` file
Now copy the `userpass` example file to `~/SuperNET/iguana/dexscripts` dir and edit the file to save the `userpass` you got from the `./setpassphrase` script output.

```shell
cd ~/SuperNET/iguana/exchanges
cp userpass ../dexscripts/userpass
cd ../dexscripts
nano userpass
```

Once done press `CTRL+X` then `Y` then `ENTER` to save the file and exit from Nano editor.

barterDEX is now installed in your system.

#### Starting FR node

Every time you want to run Full Relay (FR) open a new terminal window and type the following:
```shell
cd ~/SuperNET/iguana/dexscripts
./client &
./setpassphrase
```

#### Stopping FR node
Just use the  command to stop marketmaker. This will stop the running FR node.

```shell
pkill -15 marketmaker
```

### How to setup FR nodes for different netid?
You need to edit the `netid` section of the `client` script and `setpassphrase` script along with `client:0` param.

##### client

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
stdbuf -oL $1 ./marketmaker "{\"gui\":\"nogui\",\"client\":0,\"netid\":1024, \"userhome\":\"/${HOME#"/"}\", \"passphrase\":\"$passphrase\", \"coins\":$coins}" &
```

##### setpassphrase

```shell
#!/bin/bash
source userpass
source passphrase
curl --url "http://127.0.0.1:7783" --data "{\"userpass\":\"1d8b27b21efabcd96571cd56f91a40fb9aa4cc623d273c63bf9223dc6f8cd81f\",\"method\":\"passphrase\",\"passphrase\":\"$passphrase\",\"netid\":1024,\"gui\":\"nogui\"}"
```