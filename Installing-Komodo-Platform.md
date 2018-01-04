### INTRODUCTION:

**For Windows installations please refer to this [guide](https://github.com/DeckerSU/SuperNET/blob/dev-decker-dev/iguana/dexscripts.win32/how_to_use.md) and use this [link for the binaries](https://github.com/KomodoPlatform/BarterDEX/tree/v0.7/assets/bin/win64)**

Komodo Platform is now capable of working with Electrum servers. This means it is not necessary to download a coin daemon including its blockchain to your computer.
Check the list of current Electrum servers here: [Electrum Servers List](https://github.com/KomodoPlatform/KomodoPlatform/wiki/Electrum-servers-list).

If you however choose to use a native coin daemon, please make sure it is fully synced. 
For a list of currently supported native coins and instructions for the coin specific configuration file, see https://support.supernet.org/support/solutions/articles/29000014808-installation-script-for-various-coins

You may want to back up your system or clone it after everything is synced and running correctly BEFORE you start installing coin daemons.


### SETTING UP KOMODO PLATFORM

#### Install the following dependency packages:

```shell
sudo apt-get update
sudo apt-get install git libcurl4-openssl-dev build-essential
```

If not already installed, Nanomsg is needed:
Make sure you are in your home directory: 

`cd ~`

#### For Ubuntu 14.04:

```shell
cd /tmp
wget https://github.com/nanomsg/nanomsg/archive/1.0.0.tar.gz -O nanomsg-1.0.0.tar.gz
tar -xzvf nanomsg-1.0.0.tar.gz
cd nanomsg-1.0.0
mkdir build
cd build
cmake .. -DCMAKE_INSTALL_PREFIX=/usr
cmake --build .
sudo cmake --build . --target install
sudo ldconfig
```

#### For Ubuntu 16.04:

```shell
git clone https://github.com/nanomsg/nanomsg
cd nanomsg
cmake .
make
sudo make install
sudo ldconfig
```

#### For MacOS you'll need to install homebrew (google how to do it)

```shell
brew install nanomsg
```

#### Clone the Komodo Platform directory from github:

```shell
git clone https://github.com/KomodoPlatform/KomodoPlatform
cd KomodoPlatform/iguana/exchanges
git checkout spvdex
./install
```

From the same dir in terminal type the following commands to copy the `passphrase` file to `~/KomodoPlatform/iguana/dexscripts` dir and add a strong 24 words seed passphrase in between `""`. Save your passphrase properly without changing any word or space. Same passphrase will always show you the same smartaddress.

```shell
cp passphrase ../dexscripts/passphrase
cd ../dexscripts
nano passphrase
```
Enter your passphrase:

`export passphrase="<put a very strong passphrase here>"`

Press `CTRL+X` then `Y` then `ENTER` to save the file and exit

All these scripts are expecting a `userpass` file, which contains the definition of the `$userpass` variable (found inside scripts) to authenticate API access. This avoids evil webpages that try to issue port 7783 calls to steal your money. At first you wont know the value of u`serpass`. To find out, just run the client first (as instructed below) and then run any API script. The first line will return all the required data, the `userpass` field is first and you can copy that value and put it into `~/KomodoPlatform/iguana/dexscripts/userpass` file. If you don't, all subsequent API calls will get authorisation error.

Open a new terminal and type the following:

```shell
cd ~/KomodoPlatform/iguana/dexscripts
./client
```

Now copy the `userpass` example file to `~/KomodoPlatform/iguana/dexscripts` dir and edit the file to save the `userpass` you got from the command `./client`

```shell
cd ~/KomodoPlatform/iguana/exchanges
cp userpass ../dexscripts/userpass
cd ../dexscripts
nano userpass
```

Once done press `CTRL+X` then `Y` then `ENTER` to save the file and exit

Get all available api list by typing `./help` inside `~/KomodoPlatform/iguana/dexscripts` dir

You also need native coin daemon of your choice running in order to test native mode. Or use electrum mode if you don't want to download blockchain.