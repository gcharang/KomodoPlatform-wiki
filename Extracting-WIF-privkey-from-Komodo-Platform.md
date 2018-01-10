To be able to get the privkey of the Smart Address created by the Komodo Platform Passphrase you will need to add the `wif:1` handle to the _**MarketMaker**_ command. The _**MarketMaker**_ execution command is in `./client`. 


`cd ~/KomodoPlatform/iguana/dexscripts`


`nano ./client`

You will see the `./client` script like this:

  `./marketmaker "{\"gui\":\"nogui\",\"client\":1, \"userhome\":\"/${HOME#"/"}\", \"passphrase\":\"$passphrase\", \"coins\":$coins}" &`

**Now you need to add the wif:1 handle like this:**

`./marketmaker "{\"gui\":\"nogui\",\"client\":1,\"wif\":1, \"userhome\":\"/${HOME#"/"}\", \"passphrase\":\"$passphrase\", \"coins\":$coins}" &`

Once this edit is done, now start **_MarketMaker_**:

`cd ~/KomodoPlatform/iguana/dexscripts`

`./client`

**Execute **./setpassphrase** API command**

With that command you will see all active coins listed with their respective WIF privkeys.