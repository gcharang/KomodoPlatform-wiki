
# Whitepaper Update

Please head over to [barterDEX Whitepaper v2](https://github.com/KomodoPlatform/KomodoPlatform/wiki/barterDEX-Whitepaper-v2) with latest improvements applied to barterDEX atomic swap protocol.



## Please use this document as reference for v2 of barterDEX whitepaper

_jl777 | 5th Nov, 2016 | v1.0_

# Abstract

>In this whitepaper we describe the implementation of a decentralized exchange that would allow people to trade coins without a counterparty risk. A fully featured service would need to decentralize order matching, trade clearing, and settlement. The order matching would be done through low level pubkey to pubkey messaging protocol and the final settlement through an atomic cross chain protocol. Like any exchange, a decentralized alternative also needs liquidity providers to ensure it has enough liquidity.

# Executive Summary

1. Introduction
2. The Complete Solution
3. Order Matching
4. Trade Clearing and Settlement
5. Atomic cross chain protocol
6. Bitcoin Scripts
7. Liqudity Providers
8. Time value premium
9. Conclusions
10. References

# Introduction

Money should be exchanged freely and safely from person to person, and currently, the most practical method for it is a central exchange. Such centralized solution requires the funds to be exchanged into IOU’s that are backed by the service provider. Thus the clients are under a constant risk of their assets being stolen either by an inside theft or an outside hack. In order to remove these risks, a decentralized alternative must be established.

Among all the exchanges the trading tends to centralize to very few exchanges – and there is a reason for it. The speed advantage of trading the exchange IOU attracts the traders and the traders attract liquidity, which creates the best prices, which attracts more traders. This network effect is the reason that a few central exchanges have been dominating the trading volumes while all smaller centralized and decentralized exchanges are suffering from the lack of liquidity.

A decentralized trading environment was created two years ago (in 2014) by a service called MultiGateway (MGW) that utilizes Nxt Asset Exchange (AE). With that service, it is possible to do decentralized trading through proxy tokens. It is a hybrid solution that is being increasingly used by many other blockchain platforms. The major problem with this solution is that it loses the speed of a central exchange.

Another problem with the hybrid solution is that the native coin storage is only distributed at best, which creates a small counterparty risk for the users. That combined with a need to use gateways to convert the native coins to and from the proxy tokens has made it an impractical solution.

The conclusion is that a decentralized alternative that lacks speed cannot compete with the convenience of a centralized exchange. Eliminating the counterparty risks is a secondary requirement and alone it is not enough to attract liquidity.

# The Complete Solution

BarterDEX combines three key components: order matching, trade clearing and liquidity provider into a single integrated system that allows users to make a coin conversion request, find a suitable match and complete the trade using an atomic cross-chain protocol. Additionally, there is a privacy layer in the order matching so that two nodes can do a peer-to-peer atomic swap without any direct IP contact between the two nodes.

Order matching means the process of pairing buy orders with sell orders. It is done by algorithms that define how the orders are matched and in which order they get filled. A trade is said to become ‘executed’ once it gets filled. After execution comes trade clearing, which refers to a process that enables the promise of payment to turn into an actual money transfer. Lastly comes the settlement, that is the actual process where the assets are swapped between the trading parties.

A liquidity provider (LP) refers to a trading party that acts as a market maker who buys and sells assets. They provide liquidity to the exchange and make a profit from the bid and ask spread. Liquidity providers provide price stability and make it easier for traders to execute trades.


# Order Matching

In order to obtain speeds that are almost as fast as with centralized exchanges, the order matching is done by a group of servers that all run the same low level pubkey to pubkey messaging protocol.

The ‘pubkey to pubkey’ messaging protocol is used to provide the first layer of privacy. It would be possible to add onion routing as another privacy layer to shield the IP address from the order matching nodes. The ability for two nodes to establish communication via pubkey allows the construction of higher level protocols, like the atomic swap protocol, without contaminating the higher level protocol with the details of the networking.

Each request submitted by the user's node needs to start a realtime auction. There needs to be a special pubkey where the request is made to in order to denote that any LP (Liquidity Provider) node can be used. For that there will be a ZERO (all zeroes) pubkey. In order to allow for multiple protocols to operate at the same time, in addition to the pubkey, a 32bit channel id along with a 32bit msg id is defined. These are arbitrary sized fields and could be of any size, but for most envisioned purposes 32bits is big enough.

Speed is important, especially as all node to node communication have to go through order match servers (OMS). To minimize the propagation delay, a one hop protocol is used. That means that it will take 2 to 4 hops for a message to go from any node to any other node. By not guaranteeing delivery at this layer, a small chance of routing failure can be tolerated. The method used is for each order match server to forward each incoming packet to sqrt(N) randomly selected order match servers. For sending and receiving from other pubkey nodes, sqrt(N) requests are sent. Notice this pull approach avoids most connectivity problems and without port mapping.

Let us use 64 order match servers as an example: N is 64, and sqrt(N) is 8. To send a request to another pubkey the packet is addressed with (pubkey, channel, msgid, payload) and sent to 8 randomly selected OMS. Each of those servers, then sends to 8 other randomly selected OMS. At this point, the majority of OMS will have the packet, which will default to a 60 second time to live, so it will not stay around very long. Additional time to live is possible for messages that need to be around longer. On the receiving side the same process is used, and with great likelihood – as long as the message was sent – it will be retrieved by the destination pubkey node.

On receive the (sender pubkey, destination pubkey, channel, msgid, width) is specified. The protocol requires the OMS to only return the messages that matches the wildcarding done via the ZERO pubkey and width. In order to be able to have an efficient method for both the broadcast and point to point comms, a timestamp convention is used for the msgid. This allows specifying a width that is set to the elapsed time from the last request. By this method, a node that is continuously polling will be able to retrieve virtually all messages that are meant for it, including the broadcasts.

Now we have a method where nodes can efficiently communicate with each other using only their pubkeys. This also means that adding encryption to the protocol will be relatively easy to do. For BarterDEX the higher level protocol implements a state machine on top of the pubkey messaging.

The realtime auction process uses the pubkey messaging with a coin conversion request, which all the LP nodes that are polling will see. The LP nodes that are configured to respond to the request, will send back an offer that meets the specified request. The originating node then selects the best offer, usually the best price but can also take into account reputation and other factors. Each node has total control over what requests are responded to, what offers are accepted, and with which nodes the swaps are done with.

# Trade Clearing and Settlement

Once two nodes have decided to do an agreed upon trade, the trade needs to be done in a way that ensures that neither party has undue advantage nor any chance of losing principal funds. Another constraint is that there are many coins that don't support time locking or reveal on secret methods of transactions. To support coins with minimal functionality, an asymmetric protocol is used, where one side is assumed to have bitcoin CLTV (checklocktime verify) and reveal on secret capabilities and the other side is only assumed to be able to do a 2 of 2 multisig transaction. This allows adding support for any coin that can do a 2 of 2 multisig into the BarterDEX.

# Atomic cross chain protocol

As Tier Nolan has described in various posts, the key to achieving an atomic swap is to make the act of spending one side of the swap to also reveal information that allows the other party to spend their side. Additionally, the protocol needs to make sure that regardless of either party aborting the protocol, both parties are able to recover their capital. In order to achieve this deposits and fees are required to incentivize proceeding to the next step and to disincentivize starting swaps without intent to complete.

The protocol uses statistical means to prevent cheating, so a fee of 1/777th the traded amount is used with a 1000 item set of keypairs. Both sides generate 1000 keypairs and send hashes to the other party. After receiving the 1000 hashes, each side picks a random one. Then, all private keys except the selected one is sent to the other party, who verifies that they indeed generate the 999 pubkeys that were not selected. This method is called the "cut and choose"

The selected keypairs are used to create the atomic swap transactions. Following convention, the side with the BTC is called Bob and the side with the altcoin is called Alice. Before any of the payments are made, both sides go through the order matching process and the cut and choose and each side pays the 1/777th fee to the BarterDEX account. These fees ensure that statistically, starting atomic swaps without completing them, or in hopes of being able to collect just one side, is doomed to losing money.

At the high level, the protocol alternates between Bob and Alice sending custom transactions to each other.

1. bobdeposit is sent to Alice
2. Alice verifies it confirms and sends alicepayment
3. Bob verifies it confirms and sends bobpayment
4. Alice spends bobpayment with alicespend and in doing so reveals the information Bob needs for step 5.
5. Bob spends alicepayment with bobspend and recoups bobdeposit with bobrefund

In case step 1 never happens, then both sides will lose the fee, so a miniature form of mutually assured destruction. Since it is for a small fee, it is not any large consequence as long as it is only an occasional occurence.

In case step 2 never happens, Bob is able to recoup his bobdeposit (at any time) By recouping his deposit, Bob divulges to Alice how to spend the alicepayment.

In case step 3 never happens, Alice can spend bobdeposit after a timeout with aliceclaim. Alternatively if Bob spends bobdeposit, then Alice will be able to spend alicepayment with alicereclaim.

In case step 4 never happens, Bob can recover both bobpayment and bobdeposit. After that, Alice can recover alicepayment.

In case step 5 never happens, Alice already got her side of he transaction and can further claim bobrefund after a timeout. It is incumbent upon Bob to make sure he recovers his bobrefund before this happens, so the timeout for Alice is set to be longer than for Bob.

Each step has a small to large financial incentive for each party to complete and regardless of whether the full protocol is complete or aborted at any point, each party is able to recover their capital without implicit penalties of the protocol, i.e. fees or deposit.


# Bitcoin Scripts

In order to implement the atomic swap protocol, custom scripts are needed that include conditional, timelocked, reveal on secret transactions.

bobdeposit:
```
OP_IF
    <now+INSTANTDEX_LOCKTIME*2> OP_CLTV OP_DROP <alice_pubA0>
OP_CHECKSIG
OP_ELSE
    OP_HASH160 <hash(bob_privN)> OP_EQUALVERIFY <bob_pubB0> OP_CHECKSIG
OP_ENDIF
```

alicepayment:
```
OP_2 <alice_pubM> <bob_pubN​> OP_2 OP_CHECKMULTISIG
```

bobpayment:
```
OP_IF
    <now + INSTANTDEX_LOCKTIME> OP_CLTV OP_DROP <bob_pubB1> OP_CHECKSIG
OP_ELSE
    OP_HASH160 <hash(alice_privM)> OP_EQUALVERIFY <alice_pubA0>
OP_CHECKSIG
OP_ENDIF
```


Bitcoin scripts are described in detail in [Bitcoin developer guide](https://bitcoin.org/en/developer-guide#p2sh-scripts) and while they are not the ordinary sort of code, at the high level it can be seen that it matches the described protocol. The `OP_CLTV` is only valid after the specified delay has elapsed and it can be seen that the `bobdeposit` has double the time as the `bobpayment`.

The `<bob_pub0/1>` and are normal bitcoin `pubkeys`, which in this implementation are generated for onetime usage for each swap.

The `alice_privM` and `bob_privN` are the selected `privkeys` from the cut and choose phase, with the `alice_pubM` and `bob_pubN` the associated `pubkeys`.

`alicepayment` is a 2 of 2 `multisig` that requires signatures with `alice_privM` and `bob_privN`, which can only be done by either side after they learn of the missing `privkey` from spends of the other transactions.

The `OP_HASH160 <hash(x)>` is the key linkage as spending it requires the `privkey` to be divulged. Instead of a signature, the transaction requires the magic value that hashes to the specified value. So by requiring `hash(privM/N)` for the spending of the `bobtransactions`, it satisfies the requirements.

Without going into extreme details, the `bobdeposit` and `bobpayments` have and `IF/ELSE` where the `IF` side has the `CLTV` timeout that can be spent by the designated party simply after elapsed time and the `ELSE` side requires divulging a secret along with a signature.

In order to be accepted by the bitcoin network, all these transactions are created as P2SH transactions.

The pubkey messaging is used to implement a state machine that implements the above protocol.


# Liquidity Providers

Being able to match orders and even to finalize the trade for both sides using an atomic protocol is not enough to create a practical system. The reason is that if there isn't a counterparty to the desired trade, there is no trade. And if there are no trades, there isn't much liquidity and without liquidity the prices are not very good and if the prices aren't very good, there won't be many trades. This is an anti-network effect and is thought to doom any new exchange to a long path toward building a user base, which is required to get the narrow spreads.

However, there is a method which works with the central exchanges to create a full native DEX with liquidity. By configuring special nodes with an automated trade balancing, it becomes possible for a profit margin to be specified and automatically respond to user auction requests based on the best available price at the moment.

```shell
curl --url "http://127.0.0.1:7778"  --data "{\"agent\":\"tradebot\",\"method\":\"liquidity\",\"targetcoin\":\"BTCD \",\"vals\":{\"profit\":0.01}}"
```

The above is an example API call that activates an LP node to provide liquidity for incoming requests to swap BTC for BTCD at a 1% profit margin. All the risk of the central exchange trading is on the LP node and the other side is using the native DEX using only the atomic swap. So users that want to get access to central exchange pricing, but don’t want to incur the risk and hassle of using a central exchange can indirectly obtain pricing that is close to the best available. All the LP nodes are competing with each other to make the premium needed based on market competition and no single node has any special advantage over the rest.

While a lot more functionality can and will be added to LP nodes, it is not essential to the BarterDEX. It should be noticed that by using the same real-time auction process a full central exchange limit order type of user experience can be recreated.


# Time value premium

Careful observers will notice that in the atomic swap protocol, there are points where one party is incurring market slippage risk. In the mainstream case, each side is offering the other side a covered option for a limited amount of time and with the expected amount of time to complete being small enough that valuing each side's option equivalent to the other side is a reasonable approximation.

At the cost of additional transactions (there are already quite a few per swap), it would be possible for each side to charge a specific option premium using a pricing model, like black and scholes. Bob is offering a covered put to Alice and Alice offers a covered call to Bob, so by allowing the time delay for the two parts to become a variable, it would be possible to create long term call and put options that are fully secured by the blockchain and atomic in nature.

By using the same auction process, call or put options can be priced from the LP nodes. The advantage to this is for LP nodes to be able to earn option premium fees for longer term crypto assets it holds locally.

# Conclusions

A decentralized exchange based on gateways and proxy tokens removes some counterparty risk but loses speed and convenience in comparison to a central exchange. What we propose is a completely decentralized solution with all the features of a centralized exchange without any counterparty risk. Liquidity provider (LP) nodes respond to requests and ensure there is liquidity.

The solution is a single integrated system that allows users to make a coin conversion request, find a suitable match and complete the trade using an atomic cross-chain protocol. The coin conversion request is seen by the liquidity provider (LP) nodes who then send back an offer that meets the specified request. The originating node then selects the best offer, usually by the best price but can also take reputation and other factors into account. Finally the trade is completed with an atomic swap.

The atomic cross-chain protocol is designed in a way that ensures both parties will recover their capital even if one of them would abort the protocol. First both sides go through the order matching process, and a process called ‘cut and choose’, then each side pays a 1/777th fee to BarterDEX account. The protocol itself alternates between Bob and Alice sending custom transactions to each other. Each step has a small to large financial incentive for each party to complete the step.

It is possible to connect a LP node to a central exchange and configure it with an automated trade balancing. That would ensure each desired trade has a counterparty and that the decentralized exchange has enough liquidity. The final product is a fully fledged decentralized exchange with all the advantages of a centralized exchange.

# References

Bitcoin.org. Bitcoin Developer Guide: P2SH Scripts.
https://bitcoin.org/en/developer-guide#p2sh-scripts


Euronext.com. Liquidity Providers and Market Makers.
https://www.euronext.com/nl/membership/liquidity-providers-and-market-makers


jl777. February 2016. Atomic swaps using cut and choose.
https://bitcointalk.org/index.php?topic=1364951


Othman, A., Pennock, D. M., Reeves, D. M., and Sandholm, T. 2013. A practical liquidity-sensitive automated market maker. ACM Trans. Econ. Comp. 1, 3, Article 14 (September 2013), 25 pages.
https://www.cs.cmu.edu/~sandholm/liquidity-sensitive%20automated%20market%20maker.teac.pdf


Othman, Abraham. 2012. Automated Market Making: Theory and Practice.
http://www.cs.cmu.edu/~aothman/abethesis.pdf


Tiernan, Noel. February 2016. Deployment of CLTV.
https://bitcointalk.org/index.php?topic=1340621.msg13828271#msg13828271


Tiernan, Noel. 2014. Atomic Cross Chain Transfers.
https://github.com/TierNolan/bips/blob/bip4x/bip-atom.mediawiki


Todd, Peter. 2014. OP_CHECKLOCKTIMEVERIFY.
https://github.com/bitcoin/bips/blob/master/bip-0065.mediawiki


Wikipedia. 2016. Black–Scholes model.
https://en.wikipedia.org/wiki/Black%E2%80%93Scholes_model