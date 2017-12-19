### InstantDEX SWAP

Fastest swaps are here now as InstantDEX Swap. All nodes can be doing this at the same time, any `bob` to any `alice`. All you need to have is some amount of KMD deposit for dynamic trust. It does require a deposit that matches your trade size.

Trades were done in as low as 5 seconds. It took only 5 seconds to complete using native coin wallets! Using electrum nodes may take about 20 seconds.

If you are selling 10 at at time, deposit 10. If you are selling 50 at a time, deposit 50. And, you can trade without any deposit if you want to wait for coin confirmations. Use `InstantDEX swap`, just wait for deposit coin confirmations, you can sell all. The deposit just has to cover the amount you have pending in swaps, not anything regarding your total amount.

You can see your balance using the regular `balance` API. After verification your `zcredits` will be displayed with your KMD balance like this:
```JSON
{"result":"success","coin":"KMD","address":"RANyPgfZZLhSjQB9jrzztSw66zMMYDZuxQ","balance":363.94840658,"zcredits":50}
```

#### instantdex_deposit

You can `deposit` KMD using the following script into `b` addresses which will accrue interest as well. This will allow you to do InstantDEX swaps which finishes a swap within 5-20 seconds. The more KMD you deposit for InstantDEX trading, the more limit you can have.

It is a `timelocked deposit`, keeps the honest traders honest. Gives other traders the assurance that you have a valid deposit to cover any hack attempt.

`instantdex_deposit` the values are 1 to 52 weeks and the amount (minimum of 10). These figures are calibrated to the 5% APR, ie. 1 weeks interest is approx the BOTS 0.1% fee, 52 weeks is the most the 5% APR accrues, 10 KMD is min required for interest. It can take few minutes (about 10 confirmations) for the LP nodes to recognise you. This creates a dynamic trust between nodes and swaps are faster. Just issue normal buy and the LP nodes will respond with InstantDEX swaps.

It creates a binary file in DB with the unique deposit txids on an append only basis. The first time, it seeds it with the contents of the existing `instantdex.json` files. from the deposits file the `instantdex_address_append.json` is directly created and the `instantdex.json` file is created by removing the already spent deposits.

Sample script:

```shell
curl --url "http://127.0.0.1:7783" --data "{\"userpass\":\"$userpass\",\"method\":\"instantdex_deposit\",\"weeks\":1,\"amount\":10.0,\"broadcast\":1}"
```

`weeks:1` means it will be there for 300 hours or 1 week time. Don't set to `week:0` unless you are only testing.

Sample Output:
```JSON
{"rawtx":"01000000014c9758944771d62c731f9b06246ccd3301ea0e198a6bdbdcb22csdffasdc7010fgdfg00ffffffff0300ca9asdf17a914d9da8esdfsfbe957273f842617132c361546e7b8743420f00000000001976a91492816449164623c4e744009923356ff4832ba388ac998e6d07000000001976a9140c1007fc1f406a0a519886c0e59327e9c43a634088ac2b08175a","hex":"01000000014c9758944771d62c731f9b06246ccd3301ea0e1123jdsfbdcb22ce18d5daf0dc7010000006a473044022044b87624341cb4c01cdce88b592fadcf725d3e88315c2cd35a8d6a66d81ea035022047f372108e264634d610227e9c8ba69399e1609f7a37af1470b47ce6d0bde3b4012103fe754763c176e1339a3f62ee6b9484720e17ee4646b65a119e9f6370c7004abcffffffff0300ca9a3b0000000017a914d9da8e493573be957273f842617132c361546e7b8743420f00000000001976a914928104f862284623c4e744009923356ff4832ba388ac998e6d07000000001976a9140c1007fc1f406a0a519886c0e59327e9c43a634088ac2b08175a",
"tx":{"version":1,
"locktime":1511458859,
"vin":[{"txid":"c70daf5d8de12cb2dcdb6b8a190eea0133cd6c24069b1f732cd671479458974c",
"vout":1,
"scriptPubKey":{"hex":"76a9140c1007fc1f406a0a519886c0e59327e9c43a634088ac"}}],
"vout":[{"satoshis":"1000000000",
"scriptPubKey":{"hex":"a914d9da8e493573be957273f842617132c361546e7b87"}}, {"satoshis":"1000003","scriptPubKey":{"hex":"76a914928104f862284623c4e744009923356ff4832ba388ac"}}, {"satoshis":"124620441","scriptPubKey":{"hex":"76a9140c1007fc1f406a0a519886c0e59327e9c43a634088ac"}}]},
"txid":"a01b85233259e62edcaf3c171c97de0d2977ce88ae172b9b11809da9757e218c",
"complete":true,
"address":"bYbB9P5XG5j1Jr5nbD2nL5jt54WwPEzADd",
"expiration":1512201600,
"deposit":10,
"result":"success",
"broadcast":"a01b85233259e62edcaf3c171c97de0d2977ce88ae172b9b11809da9757e218c"}
```

#### claim

After the deposit is expired, you can claim at any time, though it is rounded up to the next week boundary. You need to use `./claim` API to claim your InstantDEX deposit (`zcredits`) back. All of your deposits are secured as long as you have the txid handy. If you use `week:0` you need to save the details of timestamp and the `b` address that the funds were sent in order to use with the `./claim` script to claim it back. Your funds will be automatically be sent back to your wallet within 24 hours of claim.

> Minutes or hours is not long enough. Remember, the point here is to resolve any blockchain attacks.

`claim` API will display the balance of the `txid` listed in `DB/instantdex.json` along with interest.

Sample File Contents:
```shell
curl --url "http://127.0.0.1:7783" --data "{\"userpass\":\"$userpass\",\"method\":\"instantdex_claim\"}"
```

Output:
```JSON
{
  "result": "success",
  "claimed": 0,
  "txids": [
    {
      "txid": "0604d11bad3ce44c5070c3d11dfc966b8a76be23b9526dcc072bc191db1707a8",
      "deposit": 10,
      "interest": 0.0100111,
      "waittime": 22325065
    },
    {
      "txid": "a3e5227235f9a45f74adca44aa3b2082b65ebacef9e20958f4b46ff49adf5b71",
      "deposit": 10,
      "interest": 0.01001775,
      "waittime": 485065
    },
    {
      "txid": "a01b85233259e62edcaf3c171c97de0d2977ce88ae172b9b11809da9757e218c",
      "deposit": 10,
      "interest": 0.01093925,
      "waittime": 48264
    },
    {
      "txid": "425f98b0763dffb6cb4c41dca18db8b89a9987502802443dde077c6c1f8b073c",
      "deposit": 10,
      "interest": 0.0100149,
      "waittime": 921864
    },
    {
      "txid": "356e8ab78c4bd47e0566cfc578e5b7b3a93ab446aab7bcd72502278a9f7c417f",
      "deposit": 10,
      "interest": 0.010013,
      "waittime": 1358664
    },
    {
      "txid": "0604d11bad3ce44c5070c3d11dfc966b8a76be23b9526dcc072bc191db1707a8",
      "deposit": 10,
      "interest": 0.0100111,
      "waittime": 22325064
    }
  ]
}
```

#### Manually Enable 0conf deposits to work with the GUI

You need to put the `txid` of the 0conf / instantdex deposit in an `instantdex.json` file inside `DB` dir. The `DB` dir is located `%appdata%/BarterDEX/DB` in Windows and `~/.BarterDEX/DB` in Linux. The content inside should look like following:
`["d5705d948a5a4e0171acec3eb718ca1421ef998b37d0af5c37ac3b440898aef5"]`
 
Then delete any deposit.address i.e.: `deposits.RPZVpjptzfZnFZZoLnuSbfLexjtkhe6uvn` files if there are any, all this is in `DB` dir. Don't delete any dir in here.

Start KMD native wallet, let the blockchain to complete sync and restart BarterDEX GUI.

It should then generate a `deposits.<smartaddress>` with the binary of the txid. From that it generates the `instantdex.json`, which is used to calculate credits.

> Negative `zcredits balance` is the balance being used for 0conf trade, will be available after the next notarized block.