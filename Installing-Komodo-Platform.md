**INTRODUCTION:**

Komodo Platform is now capable of working with Electrum servers. This means it is not necessary to download a coin daemon including its blockchain to your computer.
Check the list of current Electrum servers here: http://pad.supernet.org/electrum-servers

If you however choose to use a native coin daemon, please make sure it is fully synced. 
For a list of currently supported native coins and instructions for the coin specific configuration file, see https://support.supernet.org/support/solutions/articles/29000014808-installation-script-for-various-coins

You may want to back up your system or clone it after everything is synced and running correctly BEFORE you start installing coin daemons.


**SETTING UP BARTERDEX**

If not already installed, Nanomsg is needed:
Make sure you are in your home directory: 
    `cd ~`

**For Ubuntu 14.04:**

    `cd /tmp`
    `wget https://github.com/nanomsg/nanomsg/archive/1.0.0.tar.gz        -O nanomsg-1.0.0.tar.gz`
    `tar -xzvf nanomsg-1.0.0.tar.gzcd nanomsg-1.0.0`
    `mkdir buildcd build`
    `cmake .. -DCMAKE_INSTALL_PREFIX=/usr`
    `cmake --build .`
    `sudo cmake --build . --target install`
    `sudo ldconfig`

**For Ubuntu 16.04:**

    `git clone https://github.com/nanomsg/nanomsg`
    `cd nanomsg`
    `cmake .`
    `make`
    `sudo make install`
    `sudo ldconfig`


**For MacOS you'll need to install homebrew (google how to do it)**

    `brew install nanomsg`

**Install the following dependency packages:**

    `sudo apt-get install git libcurl4-openssl-dev build-essential`

Clone the Komodo Platform directory from github:

    `git clone https://github.com/KomodoPlatform/KomodoPlatform`

Run the Komodo Platform install script:

    `cd ~/KomodoPlatform/iguana/exchanges`

    `./install`
_This will copy a couple of scripts to the dexscripts folder. This folder is your working folder, containing scripts to get the current orderbook or to do a trade. These are yours to customize for trading (as you will see below). _

Change directory to the dexscripts folder and edit the passphrase file:

    `cd ../dexscripts`

    `nano passphrase`

_Create a strong passphrase of your own and place it between the quotes and remove the < > symbols._

     CTRL-O to save, CTRL-X to exit.

All these scripts are expecting a userpass file, which contains the definition of the $userpass variable (found inside scripts) to authenticate API access. This avoids evil webpages that try to issue port 7783 calls to steal your money. At first you wont know the value of userpass. To find out, just run the client first (as instructed below) and then run any API script. The first line will return all the required data, the "userpass" field is first and you can copy that value and put it into ~/SuperNET/iguana/dexscripts/userpass file. If you dont, all subsequent API calls will get authorization errors. 

Now run client to find out the value of userpass:

    `./client` (let it load)

Somewhere in the initial printout, you will find a line that looks like this: 

    `userpass.(xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx)`

Now copy this userpass value to the userpass file in the same directory:

    `nano userpass`
     CTRL-O to save, CTRL-X to exit.


To effectuate these changes, restart marketmaker by typing:

    `pkill -15 marketmaker`

and start ./client again, from the dexscripts folder:

    `./client`

**Marketmaker is now running.**


