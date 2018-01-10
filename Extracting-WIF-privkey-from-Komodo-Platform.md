To be able to get the privkey of the Smart Address created by the Komodo Platform Passphrase you will need to add the `wif:1` to the _**MarketMaker**_ command. The _**MarketMaker**_ excution command is in `./client`. 


`cd ~/KomodoPlatform/iguana/dexscripts`


`nano ./client`

You will see the `./client` script like this:

  `./marketmaker "{\"gui\":\"nogui\",\"client\":1, \"userhome\":\"/${HOME#"/"}\", \"passphrase\":\"$passphrase\", \"coins\":$coins}" &`

**Now you need to add the wif: haondle like this:**

`./marketmaker "{\"gui\":\"nogui\",\"client\":1,**\"wif\":1**, \"userhome\":\"/${HOME#"/"}\", \"passphrase\":\"$passphrase\", \"coins\":$coins}" &`

Once this edit is done, now restart **Market Maker**:

`cd ~/KomodoPlatform/iguana/dexscripts`

`./client`

**Execute **./setpassphrase** API command**

With that command you will see all active coins listed with their respective WIF privkeys.