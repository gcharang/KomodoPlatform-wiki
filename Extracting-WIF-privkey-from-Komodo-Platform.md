To be able to get the privkey of the Smart Address created by the Komodo Platform Passphrase you will need to add the `wif:1` to the _**MarketMaker**_ command. The _**MarketMaker**_ excution command is in `./client`. 


`cd ~/KomodoPlatform/iguana/dexscripts`


`nano ./client`

You will see the `./client` script like this:

### `#!/bin/bash
### source passphrase
### source coins
### pkill -15 marketmaker; 
###  git pull;
###  cd ..; 
###  ./m_mm;
###  ./marketmaker "{\"gui\":\"nogui\",\"client\":1, \"userhome\":\"/${HOME#"/"}\", \"passphrase\":\"$passphrase\", \"coins\":$coins}" &`

