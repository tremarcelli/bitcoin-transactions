bitcoin-transactions
===

Bitcoin transactions made simple, create and send by your own your Bitcoin, Bitcoin Cash, Bitcoin Gold, Zcash, etc transactions, manage your keys and do not disclose them to dubious wallets software

## Rationale

This module is equivalent to bitcoin-cli for some features but much more simple to use. To use bitcoin-cli you need to run the full bitcoin software, sync with the network and then sync your wallet.

This is a nightmare for quite a lot of people since syncing a full node can take 10 days and most likely will fail at the end, at this time of writing people are still desperately trying to sync bitcoin gold two weeks after the launch

And this does not help the network since even if you succeed to sync from home most likely you will run a non efficient full node

In addition many people lost everything by using malware wallets trying to get their 'free' coins, and apparently there are some common misunderstanding with the addresses format, please see the section below

And finally, if you succeeded to sync you can prune after but you still need to run constantly the bitcoin software

Given the size of the blockchain and number of different networks, at a certain point of time it will become impossible for people to run the full sw for each one just to have their wallet synced and be able to send transactions

And it is crazy to let wallet sw manage your keys

Then even if the restart of this module was inspired by the current bitcoin gold situation it is intended for the long term

## Implementation and Code

This module is using [elliptic](https://github.com/indutny/elliptic) and [bs58](https://github.com/cryptocoinjs/bs58)

The code is provided in clear so you can check it but please read the specific conditions of the license

This module was started a year ago, the intent was at that time to make non trivial transactions, then was stopped, the basic intent now is to allow you to make simply your transactions (for example to send your mined coins to an exchange)

This module is secure, it does not send anything outside (except the transactions when you request it) and does not get anything from the outside, therefore your keys are just managed by you locally

## Fees

Unlike bitcoin-cli this modules allows you to manage your fees too, do not go below 1000 satoshis for the network fees or your transaction might not be accepted by the network

There are development fees of 0.78% (with a minimum of 0.00008500) that are added to each transaction that you broadcast and paid to the address GSBbeuKPu4d6HKJhtPgk7XayMcaXyQy8TS (or the equivalent one for each network), of course the fees apply only when your broadcasted transaction to the network is included in a block, no fees apply to create/test your transactions

Most likely people will not like the dev fees (see https://github.com/BTCGPU/BTCGPU/issues/226#issuecomment-346798767) but since you can adjust the high network fees that you can't decide with bitcoin-cli for example you can compensate, then <b>please realize that you will pay at the end less fees for small amounts since you can decide with this module not to follow the (exaggerated) advised/default fees</b>, however if you set too low network fees your transactions might be delayed or just not taken into account by the network

This module is not trivial, the bitcoin protocol and formats do not make things easy, it is not recommended (neither authorized by the license) to try to modify anything, if you send wrong transactions to the network at best you will be immediately banned by the nodes for one day and at worse you could send transactions that could spend your funds at a wrong place

Should this project be funded the fees will be removed and full open source license will apply

## Installation

Install [nodejs for Windows,Mac,Linux](https://nodejs.org/en/download/package-manager/), usually this is easy

Create a bitcoin-transactions directory, install elliptic and bs58 with npm in node-modules and copy tx.js

Or just unzip [bitcoin-transactions.zip](http://www.peersm.com/bitcoin-transactions.zip) (137 kB)

## Use

See [example-1.js](https://github.com/Ayms/bitcoin-transactions/blob/master/example-1.js), Bitcoin Gold transaction 118d6160c8ae2465835ad41908a154cd9be6c78ca4012f79edbf65ca96407f97 was created with this module and mined in block 501249, see https://btgexp.com/tx/118d6160c8ae2465835ad41908a154cd9be6c78ca4012f79edbf65ca96407f97

Please note that the initial transaction was at 1% fees which are now 0.78% as stated above in the released version (with a minimum of 0.00008500)

[example-1.js](https://github.com/Ayms/bitcoin-transactions/blob/master/example-1.js) is now deprecated, please see [example-2.js](https://github.com/Ayms/bitcoin-transactions/blob/master/example-2.js) for the new format and transaction https://btgexp.com/tx/cc9684a4243999d1a1fc21c7ad7dbd1b3462bb1fb29614ed16b4d2763ab12bd4

We will follow this transaction for our examples, the previous transaction was [d5a80b216e5966790617dd3828bc13152bad82f121b16208496e9d718664e206](https://btgexp.com/tx/d5a80b216e5966790617dd3828bc13152bad82f121b16208496e9d718664e206)

### Important Warning about addresses

<b>Many wallets provide by default P2SH segwit/BIP141 addresses</b>

<b>Do not use them with this module for now, use standard P2PKH addresses (starting with a 1 for Bitcoin and Bitcoin Cash, by a G for Bitcoin gold and by t1 for Zcash)</b>

### The easy way

Once you know this module (if not please read what follows), most likely you will do:

	node tx.js BTG create prevtx= prevaddr= prevamount= previndex= privkey= addr= fees=0.00001001
	
	node tx.js BTG send <complete transaction> <IP>
	
The module will calculate the amount to be spent according to the fees and advise if the numbers are not coherent:

	amount+dev fees+network fees=prevamount

### Important - Understanding transactions

In our example we are going to spend output 31 of transaction d5a80b216e5966790617dd3828bc13152bad82f121b16208496e9d718664e206 with a prevamount of 0.00998277 that belongs to us since GSjwHAAYmFfQ4WPArc2ErtjQGr3Q2nkjvo is one of our addresses, with 0.00001001 network fees

``prevamount`` is like a bank note, you cannot cut it in half, give half to a merchant and keep the rest, you have to give the totality to the merchant and get back the rest from him

Here we can decide to spend the whole ``0.00998277`` amount or just a part

In case we decide to spend a part, the delta will be refunded to our address GSjwHAAYmFfQ4WPArc2ErtjQGr3Q2nkjvo

This is what we do in our example, where we have decided to spend 0.005 to a given address and will get back 0.00488776 on address GSjwHAAYmFfQ4WPArc2ErtjQGr3Q2nkjvo which corresponds to the total amount minus the dev and network fees ``0.00998277=0.005 + 0.00488776 + fees``

The calculation is simply:

	amount+dev fees+network fees=prevamount
	
But this is easy to make mistakes, then we have added the ``testamount`` feature which is recommended to use before using the ``create`` command where you explicity say how much network fees you want to pay

### Setting/Checking your parameters

#### You have decided to spend the whole amount with 0.0001001 fees and want to know what will be the amount (including dev fees) to put in the ``create`` command, do:

	node tx.js BTG testamount prevamount=0.00998277 fees=0.00001001
	
	--- Prevamount is small, min dev fees of 8500 apply - amount should be 0.00988776

Then you should use ``0.00988776`` in the ``amount`` parameter of the ``create`` command or use the ``create`` command without the ``amount`` parameter as explained in the "easy way" section above

#### You have decided to spend 0.005 with 0.0001001 fees like example-2, do:

	node tx.js BTG testamount prevamount=0.00998277 fees=0.00001001 amount=0.005

	--- Previous amount is: 0.00998277
	--- Amount to spend is: 0.00500000
	--- Network fees are: 0.00001001
	--- Dev fees are: 0.00008500
	--- Refunded amount to spending address is: 0.00488776
	
You will spend 0.005, pay ``0.00001001+0.00008500`` as fees and get back 0.00488776 on your initial address

As you can see in [example-2.js](https://github.com/Ayms/bitcoin-transactions/blob/master/example-2.js) since the numbers are rounded and checked again by the ``create`` command there can be a the end a difference of 0.00000001 with that numbers as network fees

Transactions are usually ~250 bytes, current rate for fees on Bitcoin is 250 satoshis per byte, which represents more than 0.00060000 fees by transaction and is very exaggerated, you can lower quite a lot your fees with this module but it is not advised to go below 1000 satoshis

### Create transactions

Once the numbers are correct you can create your transaction:

`node tx.js BTG create prevtx=d5a80b216e5966790617dd3828bc13152bad82f121b16208496e9d718664e206 prevaddr=GSjwHAAYmFfQ4WPArc2ErtjQGr3Q2nkjvo prevamount=0.00998277 previndex=31 privkey=privkey addr=GKz5ii8tWQG9hd196vNkwkLKsWHqaeKSoB fees=0.00001001 amount=0.005 `
	
	first argument (BTG here) is the network you want to use, it can be BTC (Bitcoin Core), BCH (Bitcoin Cash), ZEC (Zcash) or BTG (Bitcoin Gold)
	prevtx is the previous transaction containing the output that you want to spend
	prevaddr is the address where was sent the output that you want to spend (then one of yours)
	prevamount is the amount of the output that you want to spend
	previndex is the index of the output in the transaction
	privkey is your private key as you can see it in your wallet.dat dump
	addr is the address where to spend the output
	amount is the amount the address will receive
	fees are the network fees that you have decided to pay

You can get all those information simply from a blockchain explorer, in case of a transaction with many outputs, to get the index of your output just copy/paste from the site and look at the line number for your output (<b>don't forget that it starts from 0, the first output index is 0 not 1</b>)
	
You will get a summary of everything at the end of the command and warnings if the numbers are not correct, the ``create`` command does check the transaction again after it has been created, you must get at the end the message 'Transaction verified', if not something went wrong

Most likely if you get 'Bad transaction' this is because you made a mistake with the private key, or are trying to spend something that does not belong to you, if this happens and everything looks correct, please report (<b>but never send to us/advertise your private keys, remove them and post the logs</b>)

### Decode transaction

It's a good idea to check again your transaction once it has been created, use the ``transaction body``

`node tx.js BTG decode 020000000106e26486719d6e490862b121f182ad2b1513bc2838dd17067966596e210ba8d51f0000006a473044022039e2eee9a14fd18665eceeaac8af87888704ef2bfa14afe850b850e6fc7fdea702201d3c4340cc6998738295176320dc2597b3b6fcb93abc01add17b57b9ea70f0754121039f1e160a02079a6d6b7be0334cc4d76a125cd13a6f8d7131b11c263bc20bf918ffffffff0320a10700000000001976a914177b585b5401ad21b60b78b1b3c91996f250296d88ac47750700000000001976a91461975b3a4b9d5059e3db3e301e394d6d13275b3688ac34210000000000001976a9145b79a9d29a34f2f284ecdd33009ffa5e0252b68988ac00000000`
	
If you want you can double check again using something else like bitcoin-cli

### Send your transaction

Once you are sure that everything is correct, you can send your transaction to the network using the ``complete transaction``:

`node tx.js BTG send e1476d4474780000000000000000000003010000d42bb13a020000000106e26486719d6e490862b121f182ad2b1513bc2838dd17067966596e210ba8d51f0000006a473044022039e2eee9a14fd18665eceeaac8af87888704ef2bfa14afe850b850e6fc7fdea702201d3c4340cc6998738295176320dc2597b3b6fcb93abc01add17b57b9ea70f0754121039f1e160a02079a6d6b7be0334cc4d76a125cd13a6f8d7131b11c263bc20bf918ffffffff0320a10700000000001976a914177b585b5401ad21b60b78b1b3c91996f250296d88ac47750700000000001976a91461975b3a4b9d5059e3db3e301e394d6d13275b3688ac34210000000000001976a9145b79a9d29a34f2f284ecdd33009ffa5e0252b68988ac00000000`
	
Some addresses of nodes are hard coded, they don't belong to us and we can't say how long they will work, so if you want to use a specific node, do ``node tx.js BTG send <tx> A.B.C.D`` where A.B.C.D is the IP address of the node
	
Before sending your transaction it's a good idea too to check the network by doing ``node tx.js BTG testconnect`` or ``node tx.js BTG testconnect A.B.C.D``
	
A more easy way if available for your network is to copy and paste the ``body of the transaction`` (and not the ``complete transaction``) to a blockchain explorer that will broadcast it, there are no risks of doing this except that you don't know if the explorer will do its job, the explorer can't modify the transaction

## Addresses and getting 'free' coins

The addresses between the different networks are the same, it always ends up with the hash of your public key and is encoded in a specficic way for each network, but they still are the same
	
There are some tools existing to convert addresses, you might consider using the very excellent [Ayms/bitcoin-wallets](https://github.com/Ayms/bitcoin-wallets) module to create your wallet and/or convert addresses
	
If we consider bitcoin gold, this module is not intended to push you to get your 'free' coins from bitcoin core
	
Because, unlike many people think, you have them already so there is no need to rush to 'convert' them, many people did not get their 'free' coins and just lost all what they had by using malware wallets
	
However, if you want to move your bitcoins "from bitcoin core to bitcoin gold" (which as explained above means nothing) or from "bitcoin core to a bitcoin gold exchange", you can just use the ``create`` command:
	
`node tx.js BTG create prevtx= prevaddr= prevamount= previndex= privkey=privkey addr= fees= amount=(optional)`

where prev[tx,addr,amount,index] refers very exactly to the same that you can see in a bitcoin core explorer like https://blockchain.info before block 491407 (same transaction id, same address, same amount, same index) and privkey is the private key corresponding to your bitcoin core address
	
and addr can be a bitcoin address too that will be converted into a bitcoin gold address as you will see in the output of the command
	
You can convert before if you like the prevaddr and addr from a bitcoin core one to a bitcoin gold one but this is of no use, the create command will work with the original bitcoin addresses

If this explanation is unclear, please see the example given [here](https://github.com/BTCGPU/BTCGPU/issues/213#issuecomment-350449253)

<b>Again, if you do this, never use P2SH or Segwit/BIP141 addresses for the destination and the origin</b>

## Double check again

If for any reason you don't trust this project then it's easy to use bitcoin-cli to check that the transactions are correct, especially ``decoderawtransaction`` and ``signrawtransaction`` using the body of the transaction to compare the output and the signatures

## Signatures

The most complicate part is to generate correct signatures for the transactions, this is the only part of the code that is slightly minified which does not impact anything in terms of security

## Warning

This module has been tested for Bitcoin Gold only for now, we are pretty confident that it works for the other networks (and more globally for any network reusing the bitcoin core code) but it needs to be tested

## License

This module is subject to the following modified MIT license which removes the rights to modify, merge, sublicense, and sell:

Permission is hereby granted, free of charge, to any person obtaining a copy of this software and associated documentation files (the "Software"), to deal in the Software without restriction, including without limitation the rights to use, copy, publish, and/or distribute copies of the Software, and to permit persons to whom the Software is furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.

## Related projects :

* [Ayms/bitcoin-wallets](https://github.com/Ayms/bitcoin-wallets)
* [Ayms/zcash-wallets](https://github.com/Ayms/zcash-wallets)
* [Ayms/bittorrent-nodeid](https://github.com/Ayms/bittorrent-nodeid)
* [Ayms/torrent-live](https://github.com/Ayms/torrent-live)
* [Ayms/node-Tor](https://github.com/Ayms/node-Tor)
* [Ayms/iAnonym](https://github.com/Ayms/iAnonym)
* [Interception Detector](http://www.ianonym.com/intercept.html)
* [Ayms/abstract-tls](https://github.com/Ayms/abstract-tls)
* [Ayms/websocket](https://github.com/Ayms/websocket)
* [Ayms/node-typedarray](https://github.com/Ayms/node-typedarray)
* [Ayms/node-dom](https://github.com/Ayms/node-dom)
* [Ayms/node-bot](https://github.com/Ayms/node-bot)
* [Ayms/node-gadgets](https://github.com/Ayms/node-gadgets)
