To enable SPV wallets, also know as Electrum Wallets, just edit the `./electrum` script to add the command for the prefered coin. A list of all available coins is [here](https://github.com/KomodoPlatform/KomodoPlatform/wiki/Electrum-servers-list).

**Copy `./electrum` to `../dexscripts` directory:

`cd /KomodoPlatform/iguana/echanges`

`cp ./electrum ../dexscripts`

**Open `./electrum` to add a coin:**

`cd ../dexscripts`

`nano electrum`

Will show: 

> #!/bin/bash
> source userpass
> curl --url "http://127.0.0.1:7783" --data "{\"userpass\":\"$userpass\",\"method\":\"electrum\",\"coin\":\"BTC\",\"ipaddr\":\"173.212.225.176\",\"port\":50001}"

Add any curl command from [this list](https://github.com/KomodoPlatform/KomodoPlatform/wiki/Electrum-servers-list) to the file.

Press CTRL+X then "Y" then "ENTER" to save the file and exit

**Execute: `./electrum`**

**Now electrum coins specified will be active and available for trading**

