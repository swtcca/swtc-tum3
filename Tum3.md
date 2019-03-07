---
name: Javascript API

---

# Tum3 JavaScript API
MOAC JavaScript API was built for MOAC chain. It was developed based on the Ethereum web3.js API routines and made some methods compatiable with the web3.js methods so the users can easily move their Ðapp to MOAC chain. In
Tum3 also supported additional methods for MOAC platform, such as VNODE and SCS methods.

To make a Ðapp work on MOAC network, user should use the `tum3` object provided by the [tum3.js library](https://github.com/MOACChain/tum3). It communicates to a local MOAC  node through [JSON RPC](https://github.com/MOACChain/moac-core/wiki/JSON-RPC). tum3.js works with any MOAC VNODE and SCS, which exposes an RPC layer.

`tum3` contains the `mc` object - `tum3.mc` (for specifically MOAC Mother blockchain interactions), and the `scs` object - `tum3.scs` (for MicroChain interactions). Over time we'll introduce other objects for each of the other tum3 protocols. Working [examples can be found here](https://github.com/MOACChain/tum3/tree/master/example).


## Using callbacks

As this API is designed to work with a local RPC node and all its functions are by default use synchronous HTTP requests.con

If you want to make an asynchronous request, you can pass an optional callback as the last parameter to most functions.
All callbacks are using an [error first callback](http://fredkschott.com/post/2014/03/understanding-error-first-callbacks-in-node-js/) style:

```js
tum3.mc.getBlock(48, function(error, result){
    if(!error)
        console.log(result)
    else
        console.error(error);
})
```

## Batch requests

Batch requests allow queuing up requests and processing them at once.

```js
var batch = tum3.createBatch();
batch.add(tum3.mc.getBalance.request('0x0000000000000000000000000000000000000000', 'latest', callback));
batch.add(tum3.mc.contract(abi).at(address).balance.request(address, callback2));
batch.execute();
```

## A note on big numbers in tum3.js

You will always get a BigNumber object for balance values as JavaScript is not able to handle big numbers correctly.
Look at the following examples:

```js
"101010100324325345346456456456456456456"
// "101010100324325345346456456456456456456"
101010100324325345346456456456456456456
// 1.0101010032432535e+38
```

tum3.js depends on the [BigNumber Library](https://github.com/MikeMcl/bignumber.js/) and adds it automatically.

```js
var balance = new BigNumber('131242344353464564564574574567456');
// or var balance = tum3.mc.getBalance(someAddress);

balance.plus(21).toString(10); // toString(10) converts it to a number string
// "131242344353464564564574574567477"
```

The next example wouldn't work as we have more than 20 floating points, therefore it is recommended that you always keep your balance  in *sha* and only transform it to other units when presenting to the user:
```js
var balance = new BigNumber('13124.234435346456466666457455567456');

balance.plus(21).toString(10); // toString(10) converts it to a number string, but can only show max 20 floating points 
// "13145.23443534645646666646" // you number would be cut after the 20 floating point
```

## Tum3 Javascript Ðapp API Reference

* [tum3](#tum3)
  * [version](#chain3versionapi)
     * [api](#chain3versionapi)
     * [node](#chain3versionnode)
     * [network](#chain3versionnetwork)
     * [moac](#chain3versionmoac)
  * [isConnected()](####chain3isconnected)
  * [setProvider(provider)](#chain3setprovider)
  * [currentProvider](#chain3currentprovider)
  * [reset()](#chain3reset)
  * [sha3(string)](#chain3sha3)
  * [toHex(stringOrNumber)](#chain3tohex)
  * [toAscii(hexString)](#chain3toascii)
  * [fromAscii(textString, [padding])](#chain3fromascii)
  * [toDecimal(hexString)](#chain3todecimal)
  * [toChecksumAddress(string)](#chain3tochecksumaddress)
  * [fromDecimal(number)](#chain3fromdecimal)
  * [fromSha(numberStringOrBigNumber, unit)](#chain3fromsha)
  * [toSha(numberStringOrBigNumber, unit)](#chain3tosha)
  * [toBigNumber(numberOrHexString)](#chain3tobignumber)
  * [isAddress(hexString)](#chain3isAddress)
  * [net](#chain3net)
     * [listening/getListening](#chain3netlistening)
     * [peerCount/getPeerCount](#chain3mcpeercount)
  * [mc](#chain3mc)
     * [defaultAccount](#chain3mcdefaultaccount)
     * [defaultBlock](#chain3mcdefaultblock)
     * [syncing/getSyncing](#chain3mcsyncing)
     * [isSyncing](#chain3mcissyncing)
     * [coinbase/getCoinbase](#chain3mccoinbase)
     * [hashrate/getHashrate](#chain3mchashrate)
     * [gasPrice/getGasPrice](#chain3mcgasprice)
     * [accounts/getAccounts](#chain3mcaccounts)
     * [mining/getMining](#chain3mcmining)
     * [blockNumber/getBlockNumber](#chain3mcblocknumber)
     * [getBalance(address)](#chain3mcgetbalance)
     * [getStorageAt(address, position)](#chain3mcgetstorageat)
     * [getCode(address)](#chain3mcgetcode)
     * [getBlock(hash/number)](#chain3mcgetblock)
     * [getBlockTransactionCount(hash/number)](#chain3mcgetblocktransactioncount)
     * [getUncle(hash/number)](#chain3mcgetuncle)
     * [getBlockUncleCount(hash/number)](#chain3mcgetblockunclecount)
     * [getTransaction(hash)](#chain3mcgettransaction)
     * [getTransactionFromBlock(hashOrNumber, indexNumber)](#chain3mcgettransactionfromblock)
     * [getTransactionReceipt(hash)](#chain3mcgettransactionreceipt)
     * [getTransactionCount(address)](#chain3mcgettransactioncount)
     * [sendTransaction(object)](#chain3mcsendtransaction)
     * [call(object)](#chain3mccall)
     * [estimateGas(object)](#chain3mcestimategas)
     * [filter(array (, options) )](#chain3mcfilter)
        - [watch(callback)](#chain3mcfilter)
        - [stopWatching(callback)](#chain3mcfilter)
        - [get()](#chain3mcfilter)
    * [contract(abiArray)](#chain3mccontract)
    * [contract.myMethod()](#contract-methods)
    * [contract.myEvent()](#contract-events)
    * [contract.allEvents()](#contract-allevents)
    * [encodeParams](#chain3encodeParams)
    * [namereg](#chain3mcnamereg)
    * [sendIBANTransaction](#chain3mcsendibantransaction)
    * [iban](#chain3mciban)
      * [fromAddress](#chain3mcibanfromaddress)
      * [fromBban](#chain3mcibanfrombban)
      * [createIndirect](#chain3mcibancreateindirect)
      * [isValid](#chain3mcibanisvalid)
      * [isDirect](#chain3mcibanisdirect)
      * [isIndirect](#chain3mcibanisindirect)
      * [checksum](#chain3mcibanchecksum)
      * [institution](#chain3mcibaninstitution)
      * [client](#chain3mcibanclient)
      * [address](#chain3mcibanaddress)
      * [toString](#chain3mcibantostring)
  	* [vnode](#chain3vnode)
	  	* [vnodeAddress](#chain3vnode_address)
  		* [scsService](#vnode_scsservice)
  		* [serviceCfg](#vnode_servicecfg)
  		* [showToPublic](#vnode_showtopublic)
  		* [vnodeIP](#vnode_vnodeip)
  	* [scs](#chain3scs)
  	  * [directCall](#scs_directcall)
  	  * [getBlock](#scs_getblock)
  	  * [getBlockNumber](#scs_getblocknumber)
  	  * [getDappState](#scs_getdappstate)
  	  * [getMicroChainList](#scs_getmicrochainlist)
  	  * [getMicroChainInfo](#scs_getmicrochaininfo)
  	  * [getNonce](#scs_getnonce)
  	  * [getSCSId](#scs_getscsid)
  	  * [getTransactionReceipt](#scs_gettransactionreceipt)

### Usage
***

<h4 id="tum3">tum3.version.api</h4>

The `tum3` object provides all methods. 

##### Example

```js
var Tum3 = require('tum3');
// create an instance of tum3 using the HTTP provider.
var tum3 = new Tum3(new Tum3.providers.HttpProvider("http://localhost:8545"));
```

***

<h4 id="chain3versionapi">tum3.version.api</h4>

```js
tum3.version.api
// or async
tum3.version.getApi(callback(error, result){ ... })
```

##### Returns

`String` - The moac js api version.

##### Example

```js
var version = tum3.version.api;
console.log(version); // "0.2.0"
```

***

<h4 id="chain3versionnode">tum3.version.node</h4>

    tum3.version.node
    // or async
    tum3.version.getClient(callback(error, result){ ... })


##### Returns

`String` - The client/node version.

##### Example

```js
var version = tum3.version.node;
console.log(version); // "Moac/v0.1.0-develop/darwin-amd64/go1.9"
```

***

<h4 id="chain3versionnetwork">tum3.version.network</h4>


    tum3.version.network
    // or async
    tum3.version.getNetwork(callback(error, result){ ... })


##### Returns

`String` - The network protocol version.

##### Example

```js
var version = tum3.version.network;
console.log(version); // 54
```

***

<h4 id="chain3versionnetmoac">tum3.version.moac </h4>


    tum3.version.moac
    // or async
    tum3.version.getMoac(callback(error, result){ ... })


##### Returns

`String` - The moac protocol version.

##### Example

```js
var version = tum3.version.moac;
console.log(version); // 0x3f
```

***

<h4 id="chain3isconnected">tum3.isConnected </h4>


    tum3.isConnected()

Should be called to check if a connection to a node exists

##### Parameters
none

##### Returns

`Boolean`

##### Example

```js
if(!tum3.isConnected()) {
  
   // show some dialog to ask the user to start a node

} else {
 
   // start tum3 filters, calls, etc
  
}
```

***

<h4 id="chain3setprovider">tum3.setProvider </h4>


    tum3.setProvider(provider)

Should be called to set provider.

##### Parameters
none

##### Returns

`undefined`

##### Example

```js
tum3.setProvider(new tum3.providers.HttpProvider('http://localhost:8545')); // 8545 for go/moac
```

***

<h4 id="chain3currentprovider">tum3.currentProvider </h4>

    tum3.currentProvider

Will contain the current provider, if one is set. This can be used to check if moac etc. set already a provider.


##### Returns

`Object` - The provider set or `null`;

##### Example

```js
// Check if moac etc. already set a provider
if(!tum3.currentProvider)
    tum3.setProvider(new tum3.providers.HttpProvider("http://localhost:8545"));

```

***

<h4 id="chain3currentreset">tum3.reset </h4>

    tum3.reset(keepIsSyncing)

Should be called to reset state of tum3. Resets everything except manager. Uninstalls all filters. Stops polling.

##### Parameters

1. `Boolean` - If `true` it will uninstall all filters, but will keep the [tum3.mc.isSyncing()](#chain3mcissyncing) polls

##### Returns

`undefined`

##### Example

```js
tum3.reset();
```

***

<h4 id="chain3sha3">tum3.sha3 </h4>

    tum3.sha3(string [, callback])

##### Parameters

1. `String` - The string to hash using the SHA3 algorithm
2. `Function` - (optional) If you pass a callback the HTTP request is made asynchronous. See [this note](#using-callbacks) for details.


##### Returns

`String` - The SHA3 of the given data.

##### Example

```js
var str = tum3.sha3("Some ASCII string to be hashed in MOAC");
console.log(str); // "0xbfa24877cd68e6734710402a2af3e29cf18bd6d2f304aa528ffa3a32fa7652d2"

```

***

<h4 id="chain3tohex">tum3.toHex</h4>

    tum3.toHex(mixed);
 
Converts any value into HEX.

##### Parameters

1. `String|Number|Object|Array|BigNumber` - The value to parse to HEX. If its an object or array it will be `JSON.stringify` first. If its a BigNumber it will make it the HEX value of a number.

##### Returns

`String` - The hex string of `mixed`.

##### Example

```js
var str = tum3.toHex(moac network);
console.log(str); // '0x6d6f6163206e6574776f726b'

console.log(tum3.toHex({moac: 'test'}));
//'0x7b226d6f6163223a2274657374227d'
```

***

<h4 id="chain3toascii">tum3.toAscii</h4>

#### tum3.toAscii

    tum3.toAscii(hexString);

Converts a HEX string into a ASCII string.

##### Parameters

1. `String` - A HEX string to be converted to ascii.

##### Returns

`String` - An ASCII string made from the given `hexString`.

##### Example

```js
var str = tum3.toAscii("0x0x6d6f6163206e6574776f726b");
console.log(str); // "moac network"
```

***

<h4 id="chain3fromascii">tum3.fromAscii</h4>

    tum3.fromAscii(string);

Converts any ASCII string to a HEX string.

##### Parameters

`String` - An ASCII string to be converted to HEX.

##### Returns

`String` - The converted HEX string.

##### Example

```js
var str = tum3.fromAscii('moac network');
console.log(str); // "0x0x6d6f6163206e6574776f726b"
```

***

<h4 id="chain3tochecksumaddress">tum3.toChecksumAddress </h4>


    tum3.toChecksumAddress(hexString);

Converts a string to the checksummed address equivalent.

##### Parameters

1. `String` - A string to be converted to a checksummed address.


##### Returns

`String` - A string containing the checksummed address.

##### Example

```js
var myAddress = tum3.toChecksumAddress('0xa0c876ec9f2d817c4304a727536f36363840c02c');
console.log(myAddress); // '0xA0C876eC9F2d817c4304A727536f36363840c02c'
```

***

<h4 id="chain3todecimal">tum3.toDecimal </h4>

    tum3.toDecimal(hexString);

Converts a HEX string to its number representation.

##### Parameters

1. `String` - An HEX string to be converted to a number.


##### Returns

`Number` - The number representing the data `hexString`.

##### Example

```js
var number = tum3.toDecimal('0x15');
console.log(number); // 21
```

***

<h4 id="chain3fromdecimal">tum3.fromDecimal</h4>

    tum3.fromDecimal(number);

Converts a number or number string to its HEX representation.

##### Parameters

1. `Number|String` - A number to be converted to a HEX string.

##### Returns

`String` - The HEX string representing of the given `number`.

##### Example

```js
var value = tum3.fromDecimal('21');
console.log(value); // "0x15"
```

***

<h4 id="chain3fromsha">tum3.fromSha</h4>

    tum3.fromSha(number, unit)

Converts a number of sha into the following moac units:

- `ksha`/`femtomc`
- `msha`/`picomc`
- `gsha`/`nano`/`xiao`
- `micro`/`sand`
- `milli`
- `mc`
- `kmc`/`grand`
- `mmc`
- `gmc`
- `tmc`

##### Parameters

1. `Number|String|BigNumber` - A number or BigNumber instance.
2. `String` - One of the above moac units.


##### Returns

`String|BigNumber` - Either a number string, or a BigNumber instance, depending on the given `number` parameter.

##### Example

```js
var value = tum3.fromSha('21000000000000', 'Xiao');
console.log(value); // "21000"
```

***

<h4 id="chain3tosha">tum3.toSha </h4>

    tum3.toSha(number, unit)

Converts a moac unit into sha. Possible units are:

- `ksha`/`femtomc`
- `msha`/`picomc`
- `gsha`/`nano`/`xiao`
- `micro`/`sand`
- `milli`
- `mc`
- `kmc`/`grand`
- `mmc`
- `gmc`
- `tmc`

##### Parameters

1. `Number|String|BigNumber` - A number or BigNumber instance.
2. `String` - One of the above moac units.

##### Returns

`String|BigNumber` - Either a number string, or a BigNumber instance, depending on the given `number` parameter.

##### Example

```js
var value = tum3.toSha('1', 'mc');
console.log(value); // "1000000000000000000" = 1e18
```

***

<h4 id="chain3tobignumber">tum3.toBigNumber </h4>

    tum3.toBigNumber(numberOrHexString);

Converts a given number into a BigNumber instance.

See the [note on BigNumber](#a-note-on-big-numbers-in-javascript).

##### Parameters

1. `Number|String` - A number, number string or HEX string of a number.


##### Returns

`BigNumber` - A BigNumber instance representing the given value.


##### Example

```js
var value = tum3.toBigNumber('200000000000000000000001');
console.log(value); // instanceOf BigNumber
console.log(value.toNumber()); // 2.0000000000000002e+23
console.log(value.toString(10)); // '200000000000000000000001'
```

***

<h3 id="chain3net"> tum3.net </h3>

<h4 id="chain3netlistening">tum3.net.listening</h4>

    tum3.net.listening
    // or async
    tum3.net.getListening(callback(error, result){ ... })

This property is read only and says whether the node is actively listening for network connections or not.

##### Returns

`Boolean` - `true` if the client is actively listening for network connections, otherwise `false`.

##### Example

```js
var listening = tum3.net.listening;
console.log(listening); // true of false
```

***

<h4 id="chain3netpeercount">tum3.net.peerCount </h4>

    tum3.net.peerCount
    // or async
    tum3.net.getPeerCount(callback(error, result){ ... })

This property is read only and returns the number of connected peers.

##### Returns

`Number` - The number of peers currently connected to the client.

##### Example

```js
var peerCount = tum3.net.peerCount;
console.log(peerCount); // 4
```

***

<h3 id="chain3mc"> tum3.mc </h3>

Contains the MOAC blockchain related methods.

##### Example

```js
var mc = tum3.mc;
```

***

<h4 id="chain3mcdefaultaccount">tum3.mc.defaultAccount </h4>

    tum3.mc.defaultAccount

This default address is used for the following methods (optionally you can overwrite it by specifying the `from` property):

- [tum3.mc.sendTransaction()](#chain3mcsendtransaction)
- [tum3.mc.call()](#chain3mccall)

##### Values

`String`, 20 Bytes - Any address you own, or where you have the private key for.

*Default is* `undefined`.

##### Returns

`String`, 20 Bytes - The currently set default address.

##### Example

```js
var defaultAccount = tum3.mc.defaultAccount;
console.log(defaultAccount); // ''

// set the default block
tum3.mc.defaultAccount = '0x8888f1f195afa192cfee860698584c030f4c9db1';
```

***

<h4 id="chain3mcdefaultblock">tum3.mc.defaultBlock </h4>

    tum3.mc.defaultBlock

This default block is used for the following methods (optionally you can overwrite the defaultBlock by passing it as the last parameter):

- [tum3.mc.getBalance()](#chain3mcgetbalance)
- [tum3.mc.getCode()](#chain3mcgetcode)
- [tum3.mc.getTransactionCount()](#chain3mcgettransactioncount)
- [tum3.mc.getStorageAt()](#chain3mcgetstorageat)
- [tum3.mc.call()](#chain3mccall)

##### Values

Default block parameters can be one of the following:

- `Number` - a block number
- `String` - `"earliest"`, the genisis block
- `String` - `"latest"`, the latest block (current head of the blockchain)
- `String` - `"pending"`, the currently mined block (including pending transactions)

*Default is* `latest`

##### Returns

`Number|String` - The default block number to use when querying a state.

##### Example

```js
var defaultBlock = tum3.mc.defaultBlock;
console.log(defaultBlock); // 'latest'

// set the default block
tum3.mc.defaultBlock = 231;
```

***

<h4 id="chain3mcsyncing">tum3.mc.syncing </h4>

    tum3.mc.syncing
    // or async
    tum3.mc.getSyncing(callback(error, result){ ... })

This property is read only and returns the either a sync object, when the node is syncing or `false`.

##### Returns

`Object|Boolean` - A sync object as follows, when the node is currently syncing or `false`:
   - `startingBlock`: `Number` - The block number where the sync started.
   - `currentBlock`: `Number` - The block number where at which block the node currently synced to already.
   - `highestBlock`: `Number` - The estimated block number to sync to.

##### Example

```js
var sync = tum3.mc.syncing;
console.log(sync);
/*
{
   startingBlock: 300,
   currentBlock: 312,
   highestBlock: 512
}
*/
```

***

<h4 id="chain3mcissyncing">tum3.mc.isSyncing </h4>

    tum3.mc.isSyncing(callback);

This convenience function calls the `callback` everytime a sync starts, updates and stops.

##### Returns

`Object` - a isSyncing object with the following methods:

  * `syncing.addCallback()`: Adds another callback, which will be called when the node starts or stops syncing.
  * `syncing.stopWatching()`: Stops the syncing callbacks.

##### Callback return value

- `Boolean` - The callback will be fired with `true` when the syncing starts and with `false` when it stopped.
- `Object` - While syncing it will return the syncing object:
   - `startingBlock`: `Number` - The block number where the sync started.
   - `currentBlock`: `Number` - The block number where at which block the node currently synced to already.
   - `highestBlock`: `Number` - The estimated block number to sync to.


##### Example

```js
tum3.mc.isSyncing(function(error, sync){
    if(!error) {
        // stop all app activity
        if(sync === true) {
           // we use `true`, so it stops all filters, but not the tum3.mc.syncing polling
           tum3.reset(true);
        
        // show sync info
        } else if(sync) {
           console.log(sync.currentBlock);
        
        // re-gain app operation
        } else {
            // run your app init function...
        }
    }
});
```

***

<h4 id="chain3mcissyncing">tum3.mc.isSyncing </h4>

    tum3.mc.coinbase
    // or async
    tum3.mc.getCoinbase(callback(error, result){ ... })

This property is read only and returns the coinbase address were the mining rewards go to.

##### Returns

`String` - The coinbase address of the client.

##### Example

```js
var coinbase = tum3.mc.coinbase;
console.log(coinbase); // "0x407d73d8a49eeb85d32cf465507dd71d507100c1"
```

***

<h4 id="chain3mcmining">tum3.mc.mining </h4>

    tum3.mc.mining
    // or async
    tum3.mc.getMining(callback(error, result){ ... })


This property is read only and says whether the node is mining or not.


##### Returns

`Boolean` - `true` if the client is mining, otherwise `false`.

##### Example

```js
var mining = tum3.mc.mining;
console.log(mining); // true or false
```

***

<h4 id="chain3mchashrate">tum3.mc.hashrate </h4>

    tum3.mc.hashrate
    // or async
    tum3.mc.getHashrate(callback(error, result){ ... })

This property is read only and returns the number of hashes per second that the node is mining with.


##### Returns

`Number` - number of hashes per second.

##### Example

```js
var hashrate = tum3.mc.hashrate;
console.log(hashrate); // 493736
```

***

<h4 id="chain3mcgasprice">tum3.mc.gasPrice </h4>

    tum3.mc.gasPrice
    // or async
    tum3.mc.getGasPrice(callback(error, result){ ... })


This property is read only and returns the current gas price.
The gas price is determined by the x latest blocks median gas price.

##### Returns

`BigNumber` - A BigNumber instance of the current gas price in sha.

See the [note on BigNumber](#a-note-on-big-numbers-in-javascript).

##### Example

```js
var gasPrice = tum3.mc.gasPrice;
console.log(gasPrice.toString(10)); // "10000000000000"
```

***

<h4 id="chain3mcaccounts">tum3.mc.accounts </h4>

    tum3.mc.accounts
    // or async
    tum3.mc.getAccounts(callback(error, result){ ... })

This property is read only and returns a list of accounts the node controls.

##### Returns

`Array` - An array of addresses controlled by client.

##### Example

```js
var accounts = tum3.mc.accounts;
console.log(accounts); // ["0x407d73d8a49eeb85d32cf465507dd71d507100c1"] 
```

***

<h4 id="chain3mcblocknumber">tum3.mc.blockNumber </h4>

    tum3.mc.blockNumber
    // or async
    tum3.mc.getBlockNumber(callback(error, result){ ... })

This property is read only and returns the current block number.

##### Returns

`Number` - The number of the most recent block.

##### Example

```js
var number = tum3.mc.blockNumber;
console.log(number); // 2744
```

***

<h4 id="chain3mcgetbalance">tum3.mc.getBalance </h4>

    tum3.mc.getBalance(addressHexString [, defaultBlock] [, callback])

Get the balance of an address at a given block.

##### Parameters

1. `String` - The address to get the balance of.
2. `Number|String` - (optional) If you pass this parameter it will not use the default block set with [tum3.mc.defaultBlock](#chain3mcdefaultblock).
3. `Function` - (optional) If you pass a callback the HTTP request is made asynchronous. See [this note](#using-callbacks) for details.

##### Returns

`String` - A BigNumber instance of the current balance for the given address in sha.

See the [note on BigNumber](#a-note-on-big-numbers-in-javascript).

##### Example

```js
var balance = tum3.mc.getBalance("0x407d73d8a49eeb85d32cf465507dd71d507100c1");
console.log(balance); // instanceof BigNumber
console.log(balance.toString(10)); // '1000000000000'
console.log(balance.toNumber()); // 1000000000000
```

***

<h4 id="chain3mcgetstorageat">tum3.mc.getStorageAt </h4>

    tum3.mc.getStorageAt(addressHexString, position [, defaultBlock] [, callback])

Get the storage at a specific position of an address.

##### Parameters

1. `String` - The address to get the storage from.
2. `Number` - The index position of the storage.
3. `Number|String` - (optional) If you pass this parameter it will not use the default block set with [tum3.mc.defaultBlock](#chain3mcdefaultblock).
4. `Function` - (optional) If you pass a callback the HTTP request is made asynchronous. See [this note](#using-callbacks) for details.


##### Returns

`String` - The value in storage at the given position.

##### Example

```js
var state = tum3.mc.getStorageAt("0x407d73d8a49eeb85d32cf465507dd71d507100c1", 0);
console.log(state); // "0x03"
```

***

<h4 id="chain3mcgetcode">tum3.mc.getCode </h4>

    tum3.mc.getCode(addressHexString [, defaultBlock] [, callback])

Get the code at a specific address.

##### Parameters

1. `String` - The address to get the code from.
2. `Number|String` - (optional) If you pass this parameter it will not use the default block set with [tum3.mc.defaultBlock](#chain3mcdefaultblock).
3. `Function` - (optional) If you pass a callback the HTTP request is made asynchronous. See [this note](#using-callbacks) for details.

##### Returns

`String` - The data at given address `addressHexString`.

##### Example

```js
var code = tum3.mc.getCode("0xd5677cf67b5aa051bb40496e68ad359eb97cfbf8");
console.log(code); // "0x600160008035811a818181146012578301005b601b6001356025565b8060005260206000f25b600060078202905091905056"
```

***

<h4 id="chain3mcgetblock">tum3.mc.getBlock </h4>

     tum3.mc.getBlock(blockHashOrBlockNumber [, returnTransactionObjects] [, callback])

Returns a block matching the block number or block hash.

##### Parameters

1. `String|Number` - The block number or hash. Or the string `"earliest"`, `"latest"` or `"pending"` as in the [default block parameter](#chain3mcdefaultblock).
2. `Boolean` - (optional, default `false`) If `true`, the returned block will contain all transactions as objects, if `false` it will only contains the transaction hashes.
3. `Function` - (optional) If you pass a callback the HTTP request is made asynchronous. See [this note](#using-callbacks) for details.

##### Returns

`Object` - The block object:

  - `number`: `Number` - the block number. `null` when its pending block.
  - `hash`: `String`, 32 Bytes - hash of the block. `null` when its pending block.
  - `parentHash`: `String`, 32 Bytes - hash of the parent block.
  - `nonce`: `String`, 8 Bytes - hash of the generated proof-of-work. `null` when its pending block.
  - `sha3Uncles`: `String`, 32 Bytes - SHA3 of the uncles data in the block.
  - `logsBloom`: `String`, 256 Bytes - the bloom filter for the logs of the block. `null` when its pending block.
  - `transactionsRoot`: `String`, 32 Bytes - the root of the transaction trie of the block
  - `stateRoot`: `String`, 32 Bytes - the root of the final state trie of the block.
  - `miner`: `String`, 20 Bytes - the address of the beneficiary to whom the mining rewards were given.
  - `difficulty`: `BigNumber` - integer of the difficulty for this block.
  - `totalDifficulty`: `BigNumber` - integer of the total difficulty of the chain until this block.
  - `extraData`: `String` - the "extra data" field of this block.
  - `size`: `Number` - integer the size of this block in bytes.
  - `gasLimit`: `Number` - the maximum gas allowed in this block.
  - `gasUsed`: `Number` - the total used gas by all transactions in this block.
  - `timestamp`: `Number` - the unix timestamp for when the block was collated.
  - `transactions`: `Array` - Array of transaction objects, or 32 Bytes transaction hashes depending on the last given parameter.
  - `uncles`: `Array` - Array of uncle hashes.

##### Example

```js
var info = tum3.mc.block(3150);
console.log(info);
/*
{
  difficulty: 142913,
  extraData: "0xd5820100846d6f616385676f312e398664617277696e",
  gasLimit: 1732862932,
  gasUsed: 20000,
  hash: "0x43ab9feea269585e11b0c4c9d91449cbeae44395dcc2eb1d726a64e5a00047f7",
  logsBloom: "0x00000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000",
  miner: "0xa8863fc8ce3816411378685223c03daae9770ebb",
  mixHash: "0x9c879f1f4570f832091222f31495db378b43496e3115c96bc40f9f57915f1b59",
  nonce: "0x71b4365850bc8a10",
  number: 929,
  parentHash: "0x9e460922544a32fb9c634ee93793f19bcb033db53ac07c6008cbb2b354904d74",
  receiptsRoot: "0x1a8c28ca2760b9534a1d1edfb4b3748f62f875631eccd2656babeb9cefdc6fdb",
  sha3Uncles: "0x1dcc4de8dec75d7aab85b567b6ccd41ad312451b948a7413f0a142fd40d49347",
  size: 667,
  stateRoot: "0xb788c2ba4111b8ad4bd7fc39908dd63200fa93618f105bee8495723ac0f227c4",
  timestamp: 1518453915,
  totalDifficulty: 135134971,
  transactions: ["0x0c2a15d54db54f69d81dbb0046fc0e3acd8e790fda9e198d09827292ceb0ba13"],
  transactionsRoot: "0xdc160a6056769aa497940af2155ab2d4d106aab5fca352559a2918b17ff6087f",
  uncles: []
}
*/
```

***

<h4 id="chain3mcgetblocktransactioncount">tum3.mc.getBlockTransactionCount </h4>

    tum3.mc.getBlockTransactionCount(hashStringOrBlockNumber [, callback])

Returns the number of transaction in a given block.

##### Parameters

1. `String|Number` - The block number or hash. Or the string `"earliest"`, `"latest"` or `"pending"` as in the [default block parameter](#chain3mcdefaultblock).
2. `Function` - (optional) If you pass a callback the HTTP request is made asynchronous. See [this note](#using-callbacks) for details.

##### Returns

`Number` - The number of transactions in the given block.

##### Example

```js
var number = tum3.mc.getBlockTransactionCount("0x407d73d8a49eeb85d32cf465507dd71d507100c1");
console.log(number); // 1
```

***

<h4 id="chain3mcgetuncle">tum3.mc.getUncle </h4>

    tum3.mc.getUncle(blockHashStringOrNumber, uncleNumber [, returnTransactionObjects] [, callback])

Returns a blocks uncle by a given uncle index position.

##### Parameters

1. `String|Number` - The block number or hash. Or the string `"earliest"`, `"latest"` or `"pending"` as in the [default block parameter](#chain3mcdefaultblock).
2. `Number` - The index position of the uncle.
3. `Boolean` - (optional, default `false`) If `true`, the returned block will contain all transactions as objects, if `false` it will only contains the transaction hashes.
4. `Function` - (optional) If you pass a callback the HTTP request is made asynchronous. See [this note](#using-callbacks) for details.


##### Returns

`Object` - the returned uncle. For a return value see [tum3.mc.getBlock()](#chain3mcgetblock).

**Note**: An uncle doesn't contain individual transactions.

##### Example

```js
var uncle = tum3.mc.getUncle(500, 0);
console.log(uncle); // see tum3.mc.getBlock

```

***

<h4 id="chain3mcgettransaction">tum3.mc.getTransaction </h4>


    tum3.mc.getTransaction(transactionHash [, callback])

Returns a transaction matching the given transaction hash.

##### Parameters

1. `String` - The transaction hash.
2. `Function` - (optional) If you pass a callback the HTTP request is made asynchronous. See [this note](#using-callbacks) for details.


##### Returns

`Object` - A transaction object its hash `transactionHash`:

  - `hash`: `String`, 32 Bytes - hash of the transaction.
  - `nonce`: `Number` - the number of transactions made by the sender prior to this one.
  - `blockHash`: `String`, 32 Bytes - hash of the block where this transaction was in. `null` when its pending.
  - `blockNumber`: `Number` - block number where this transaction was in. `null` when its pending.
  - `transactionIndex`: `Number` - integer of the transactions index position in the block. `null` when its pending.
  - `from`: `String`, 20 Bytes - address of the sender.
  - `to`: `String`, 20 Bytes - address of the receiver. `null` when its a contract creation transaction.
  - `value`: `BigNumber` - value transferred in Sha.
  - `gasPrice`: `BigNumber` - gas price provided by the sender in Sha.
  - `gas`: `Number` - gas provided by the sender.
  - `input`: `String` - the data sent along with the transaction.


##### Example

```js
var txhash = "0x0c2a15d54db54f69d81dbb0046fc0e3acd8e790fda9e198d09827292ceb0ba13";

var transaction = tum3.mc.getTransaction(txhash);
console.log(transaction);
/*
{
  blockHash: "0x43ab9feea269585e11b0c4c9d91449cbeae44395dcc2eb1d726a64e5a00047f7",
  blockNumber: 929,
  from: "0x7312f4b8a4457a36827f185325fd6b66a3f8bb8b",
  gas: 2000000,
  gasPrice: 0,
  hash: "0x0c2a15d54db54f69d81dbb0046fc0e3acd8e790fda9e198d09827292ceb0ba13",
  input: "0x",
  nonce: 1,
  r: "0x5487c81b1ab58a7364b39263fd51afee9e7ad32be1c3b5ddeb656f06d75b1d68",
  s: "0x3f06414699c02f74c5075e24c5f266a25a64dbdf77ce82e57b1def6c5e198f9f",
  to: "0xd814f2ac2c4ca49b33066582e4e97ebae02f2ab9",
  transactionIndex: 0,
  v: "0x25",
  value: 500000000000000000
}
*/

```

***

<h4 id="chain3mcgettransactionfromBlock">tum3.mc.getTransactionFromBlock </h4>

    getTransactionFromBlock(hashStringOrNumber, indexNumber [, callback])

Returns a transaction based on a block hash or number and the transactions index position.

##### Parameters

1. `String` - A block number or hash. Or the string `"earliest"`, `"latest"` or `"pending"` as in the [default block parameter](#chain3mcdefaultblock).
2. `Number` - The transactions index position.
3. `Function` - (optional) If you pass a callback the HTTP request is made asynchronous. See [this note](#using-callbacks) for details.

##### Returns

`Object` - A transaction object, see [tum3.mc.getTransaction](#chain3mcgettransaction):


##### Example

```js
var transaction = tum3.mc.getTransactionFromBlock(921, 2);
console.log(transaction); // see tum3.mc.getTransaction

```

***

<h4 id="chain3mcgettransactionreceipt">tum3.mc.getTransactionReceipt </h4>


    tum3.mc.getTransactionReceipt(hashString [, callback])

Returns the receipt of a transaction by transaction hash.

**Note** That the receipt is not available for pending transactions.


##### Parameters

1. `String` - The transaction hash.
2. `Function` - (optional) If you pass a callback the HTTP request is made asynchronous. See [this note](#using-callbacks) for details.

##### Returns

`Object` - A transaction receipt object, or `null` when no receipt was found:

  - `blockHash`: `String`, 32 Bytes - hash of the block where this transaction was in.
  - `blockNumber`: `Number` - block number where this transaction was in.
  - `transactionHash`: `String`, 32 Bytes - hash of the transaction.
  - `transactionIndex`: `Number` - integer of the transactions index position in the block.
  - `from`: `String`, 20 Bytes - address of the sender.
  - `to`: `String`, 20 Bytes - address of the receiver. `null` when its a contract creation transaction.
  - `cumulativeGasUsed `: `Number ` - The total amount of gas used when this transaction was executed in the block.
  - `gasUsed `: `Number ` -  The amount of gas used by this specific transaction alone.
  - `contractAddress `: `String` - 20 Bytes - The contract address created, if the transaction was a contract creation, otherwise `null`.
  - `logs `:  `Array` - Array of log objects, which this transaction generated.

##### Example
```js
var receipt = tum3.mc.getTransactionReceipt('0x9fc76417374aa880d4449a1f7f31ec597f00b1f6f3dd2d66f4c9c6c445836d8b');
console.log(receipt);
{
  "transactionHash": "0x9fc76417374aa880d4449a1f7f31ec597f00b1f6f3dd2d66f4c9c6c445836d8b",
  "transactionIndex": 0,
  "blockHash": "0xef95f2f1ed3ca60b048b4bf67cde2195961e0bba6f70bcbea9a2c4e133e34b46",
  "blockNumber": 3,
  "contractAddress": "0xa94f5374fce5edbc8e2a8697c15331677e6ebf0b",
  "cumulativeGasUsed": 314159,
  "gasUsed": 30234,
  "logs": [{
         // logs as returned by getFilterLogs, etc.
     }, ...]
}
```

***

<h4 id="chain3mcgettransactioncount">tum3.mc.getTransactionCount </h4>

    tum3.mc.getTransactionCount(addressHexString [, defaultBlock] [, callback])

Get the numbers of transactions sent from this address.

##### Parameters

1. `String` - The address to get the numbers of transactions from.
2. `Number|String` - (optional) If you pass this parameter it will not use the default block set with [tum3.mc.defaultBlock](#chain3mcdefaultblock).
3. `Function` - (optional) If you pass a callback the HTTP request is made asynchronous. See [this note](#using-callbacks) for details.

##### Returns

`Number` - The number of transactions sent from the given address.

##### Example

```js
var number = tum3.mc.getTransactionCount("0x407d73d8a49eeb85d32cf465507dd71d507100c1");
console.log(number); // 1
```

***

<h4 id="chain3mcsendtransaction">tum3.mc.sendTransaction </h4>


    tum3.mc.sendTransaction(transactionObject [, callback])

Sends a transaction to the network.

##### Parameters

1. `Object` - The transaction object to send:
  - `from`: `String` - The address for the sending account. Uses the [tum3.mc.defaultAccount](#chain3mcdefaultaccount) property, if not specified.
  - `to`: `String` - (optional) The destination address of the message, left undefined for a contract-creation transaction.
  - `value`: `Number|String|BigNumber` - (optional) The value transferred for the transaction in Sha, also the endowment if it's a contract-creation transaction.
  - `gas`: `Number|String|BigNumber` - (optional, default: To-Be-Determined) The amount of gas to use for the transaction (unused gas is refunded).
  - `gasPrice`: `Number|String|BigNumber` - (optional, default: To-Be-Determined) The price of gas for this transaction in sha, defaults to the mean network gas price.
  - `data`: `String` - (optional) Either a byte string containing the associated data of the message, or in the case of a contract-creation transaction, the initialisation code.
  - `nonce`: `Number`  - (optional) Integer of a nonce. This allows to overwrite your own pending transactions that use the same nonce.
  - `scsConsensusAddr`: `String` - (optional) The address of the SCS consensus protocol, left undefined for global contract-creation transaction, required for direct contract-creation transaction
2. `Function` - (optional) If you pass a callback the HTTP request is made asynchronous. See [this note](#using-callbacks) for details.

##### Returns

`String` - The 32 Bytes transaction hash as HEX string.

If the transaction was a contract creation use [tum3.mc.getTransactionReceipt()](#chain3mcgettransactionreceipt) to get the contract address, after the transaction was mined.

##### Example

```js

// compiled solidity source code using https://chriseth.github.io/cpp-ethereum/
var code = "603d80600c6000396000f3007c01000000000000000000000000000000000000000000000000000000006000350463c6888fa18114602d57005b600760043502
8060005260206000f3";

tum3.mc.sendTransaction({data: code}, function(err, address) {
  if (!err)
    console.log(address); // "0x7f9fade1c0d57a7af66ab4ead7c2eb7b11a91385"
});
```

***

<h4 id="chain3mccall">tum3.mc.call </h4>


    tum3.mc.call(callObject [, defaultBlock] [, callback])

Executes a message call transaction, which is directly executed in the VM of the node, but never mined into the blockchain.

##### Parameters

1. `Object` - A transaction object see [tum3.mc.sendTransaction](#chain3mcsendtransaction), with the difference that for calls the `from` property is optional as well.
2. `Number|String` - (optional) If you pass this parameter it will not use the default block set with [tum3.mc.defaultBlock](#chain3mcdefaultblock).
3. `Function` - (optional) If you pass a callback the HTTP request is made asynchronous. See [this note](#using-callbacks) for details.

##### Returns

`String` - The returned data of the call, e.g. a codes functions return value.

##### Example

```js
var result = tum3.mc.call({
    to: "0xc4abd0339eb8d57087278718986382264244252f", 
    data: "0xc6888fa10000000000000000000000000000000000000000000000000000000000000003"
});
console.log(result); // "0x0000000000000000000000000000000000000000000000000000000000000015"
```

***

<h4 id="chain3mcestimategas">tum3.mc.estimateGas </h4>


    tum3.mc.estimateGas(callObject [, defaultBlock] [, callback])

Executes a message call or transaction, which is directly executed in the VM of the node, but never mined into the blockchain and returns the amount of the gas used.

##### Parameters

See [tum3.mc.sendTransaction](#chain3mcsendtransaction), expect that all properties are optional.

##### Returns

`Number` - the used gas for the simulated call/transaction.

##### Example

```js
var result = tum3.mc.estimateGas({
    to: "0xc4abd0339eb8d57087278718986382264244252f", 
    data: "0xc6888fa10000000000000000000000000000000000000000000000000000000000000003"
});
console.log(result); // "20465"
```

***

<h4 id="chain3mcfilter">tum3.mc.filter </h4>


```js
// can be 'latest' or 'pending'
var filter = tum3.mc.filter(filterString);
// OR object are log filter options
var filter = tum3.mc.filter(options);

// watch for changes
filter.watch(function(error, result){
  if (!error)
    console.log(result);
});

// Additionally you can start watching right away, by passing a callback:
tum3.mc.filter(options, function(error, result){
  if (!error)
    console.log(result);
});
```

##### Parameters

1. `String|Object` - The string `"latest"` or `"pending"` to watch for changes in the latest block or pending transactions respectively. Or a filter options object as follows:
  * `fromBlock`: `Number|String` - The number of the earliest block (`latest` may be given to mean the most recent and `pending` currently mining, block). By default `latest`.
  * `toBlock`: `Number|String` - The number of the latest block (`latest` may be given to mean the most recent and `pending` currently mining, block). By default `latest`.
  * `address`: `String` - An address or a list of addresses to only get logs from particular account(s).
  * `topics`: `Array of Strings` - An array of values which must each appear in the log entries. The order is important, if you want to leave topics out use `null`, e.g. `[null, '0x00...']`. You can also pass another array for each topic with options for that topic e.g. `[null, ['option1', 'option2']]`

##### Returns

`Object` - A filter object with the following methods:

  * `filter.get(callback)`: Returns all of the log entries that fit the filter.
  * `filter.watch(callback)`: Watches for state changes that fit the filter and calls the callback. See [this note](#using-callbacks) for details.
  * `filter.stopWatching()`: Stops the watch and uninstalls the filter in the node. Should always be called once it is done.

##### Watch callback return value

- `String` - When using the `"latest"` parameter, it returns the block hash of the last incoming block.
- `String` - When using the `"pending"` parameter, it returns a transaction hash of the last add pending transaction.
- `Object` - When using manual filter options, it returns a log object as follows:
    - `logIndex`: `Number` - integer of the log index position in the block. `null` when its pending log.
    - `transactionIndex`: `Number` - integer of the transactions index position log was created from. `null` when its pending log.
    - `transactionHash`: `String`, 32 Bytes - hash of the transactions this log was created from. `null` when its pending log.
    - `blockHash`: `String`, 32 Bytes - hash of the block where this log was in. `null` when its pending. `null` when its pending log.
    - `blockNumber`: `Number` - the block number where this log was in. `null` when its pending. `null` when its pending log.
    - `address`: `String`, 32 Bytes - address from which this log originated.
    - `data`: `String` - contains one or more 32 Bytes non-indexed arguments of the log.
    - `topics`: `Array of Strings` - Array of 0 to 4 32 Bytes `DATA` of indexed log arguments. (In *solidity*: The first topic is the *hash* of the signature of the event (e.g. `Deposit(address,bytes32,uint256)`), except you declared the event with the `anonymous` specifier.)

**Note** For event filter return values see [Contract Events](#contract-events)

##### Example

```js
var filter = tum3.mc.filter('pending');

filter.watch(function (error, log) {
  console.log(log); //  {"address":"0x0000000000000000000000000000000000000000", "data":"0x0000000000000000000000000000000000000000000000000000000000000000", ...}
});

// get all past logs again.
var myResults = filter.get(function(error, logs){ ... });

...

// stops and uninstalls the filter
filter.stopWatching();

```

***

<h4 id="chain3mccontract">tum3.mc.contract </h4>


    tum3.mc.contract(abiArray)

Creates a contract object for a solidity contract, which can be used to initiate contracts on an address.
You can read more about events [here](https://github.com/MOACChain/wiki/wiki/MOAC-Contract-ABI#example-javascript-usage).

##### Parameters

1. `Array` - ABI array with descriptions of functions and events of the contract.

##### Returns

`Object` - A contract object, which can be initiated as follows:

```js
var MyContract = tum3.mc.contract(abiArray);

// instantiate by address
var contractInstance = MyContract.at([address]);

// deploy new contract
var contractInstance = MyContract.new([contructorParam1] [, contructorParam2], {data: '0x12345...', from: myAccount, gas: 1000000});

// Get the data to deploy the contract manually
var contractData = MyContract.new.getData([contructorParam1] [, contructorParam2], {data: '0x12345...'});
// contractData = '0x12345643213456000000000023434234'
```

And then you can either initiate an existing contract on an address,
or deploy the contract using the compiled byte code:

```js
// Instantiate from an existing address:
var myContractInstance = MyContract.at(myContractAddress);


// Or deploy a new contract:

// Deploy the contract asyncronous:
var myContractReturned = MyContract.new(param1, param2, {
   data: myContractCode,
   gas: 300000,
   from: mySenderAddress}, function(err, myContract){
    if(!err) {
       // NOTE: The callback will fire twice!
       // Once the contract has the transactionHash property set and once its deployed on an address.

       // e.g. check tx hash on the first call (transaction send)
       if(!myContract.address) {
           console.log(myContract.transactionHash) // The hash of the transaction, which deploys the contract
       
       // check address on the second call (contract deployed)
       } else {
           console.log(myContract.address) // the contract address
       }

       // Note that the returned "myContractReturned" === "myContract",
       // so the returned "myContractReturned" object will also get the address set.
    }
  });

// Deploy contract syncronous: The address will be added as soon as the contract is mined.
// Additionally you can watch the transaction by using the "transactionHash" property
var myContractInstance = MyContract.new(param1, param2, {data: myContractCode, gas: 300000, from: mySenderAddress});
myContractInstance.transactionHash // The hash of the transaction, which created the contract
myContractInstance.address // undefined at start, but will be auto-filled later
```

**Note** When you deploy a new contract, you should check for the next 12 blocks or so if the contract code is still at the address (using [tum3.mc.getCode()](#chain3mcgetcode)), to make sure a fork didn't change that.

##### Example

```js
// contract abi
var abi = [{
     name: 'myConstantMethod',
     type: 'function',
     constant: true,
     inputs: [{ name: 'a', type: 'string' }],
     outputs: [{name: 'd', type: 'string' }]
}, {
     name: 'myStateChangingMethod',
     type: 'function',
     constant: false,
     inputs: [{ name: 'a', type: 'string' }, { name: 'b', type: 'int' }],
     outputs: []
}, {
     name: 'myEvent',
     type: 'event',
     inputs: [{name: 'a', type: 'int', indexed: true},{name: 'b', type: 'bool', indexed: false]
}];

// creation of contract object
var MyContract = tum3.mc.contract(abi);

// initiate contract for an address
var myContractInstance = MyContract.at('0xc4abd0339eb8d57087278718986382264244252f');

// call constant function
var result = myContractInstance.myConstantMethod('myParam');
console.log(result) // '0x25434534534'

// send a transaction to a function
myContractInstance.myStateChangingMethod('someParam1', 23, {value: 200, gas: 2000});

// short hand style
tum3.mc.contract(abi).at(address).myAwesomeMethod(...);

// create filter
var filter = myContractInstance.myEvent({a: 5}, function (error, result) {
  if (!error)
    console.log(result);
    /*
    {
        address: '0x8718986382264244252fc4abd0339eb8d5708727',
        topics: "0x12345678901234567890123456789012", "0x0000000000000000000000000000000000000000000000000000000000000005",
        data: "0x0000000000000000000000000000000000000000000000000000000000000001",
        ...
    }
    */
});
```

***

#### Contract Methods

```js
// Automatically determines the use of call or sendTransaction based on the method type
myContractInstance.myMethod(param1 [, param2, ...] [, transactionObject] [, callback]);

// Explicitly calling this method
myContractInstance.myMethod.call(param1 [, param2, ...] [, transactionObject] [, callback]);

// Explicitly sending a transaction to this method
myContractInstance.myMethod.sendTransaction(param1 [, param2, ...] [, transactionObject] [, callback]);

// Explicitly sending a transaction to this method
myContractInstance.myMethod.sendTransaction(param1 [, param2, ...] [, transactionObject] [, callback]);

// Get the call data, so you can call the contract through some other means
var myCallData = myContractInstance.myMethod.getData(param1 [, param2, ...]);
// myCallData = '0x45ff3ff6000000000004545345345345..'
```

The contract object exposes the contracts methods, which can be called using parameters and a transaction object.

##### Parameters

- `String|Number` - (optional) Zero or more parameters of the function.
- `Object` - (optional) The (previous) last parameter can be a transaction object, see [tum3.mc.sendTransaction](#chain3mcsendtransaction) parameter 1 for more.
- `Function` - (optional) If you pass a callback as the last parameter the HTTP request is made asynchronous. See [this note](#using-callbacks) for details.

##### Returns

`String` - If its a call the result data, if its a send transaction a created contract address, or the transaction hash, see [tum3.mc.sendTransaction](#chain3mcsendtransaction) for details.


##### Example

```js
// creation of contract object
var MyContract = tum3.mc.contract(abi);

// initiate contract for an address
var myContractInstance = MyContract.at('0x78e97bcc5b5dd9ed228fed7a4887c0d7287344a9');

var result = myContractInstance.myConstantMethod('myParam');
console.log(result) // '0x25434534534'

myContractInstance.myStateChangingMethod('someParam1', 23, {value: 200, gas: 2000}, function(err, result){ ... });
```

***


#### Contract Events

```js
var event = myContractInstance.MyEvent({valueA: 23} [, additionalFilterObject])

// watch for changes
event.watch(function(error, result){
  if (!error)
    console.log(result);
});

// Or pass a callback to start watching immediately
var event = myContractInstance.MyEvent([{valueA: 23}] [, additionalFilterObject] , function(error, result){
  if (!error)
    console.log(result);
});

```

You can use events like [filters](#chain3mcfilter) and they have the same methods, but you pass different objects to create the event filter.

##### Parameters

1. `Object` - Indexed return values you want to filter the logs by, e.g. `{'valueA': 1, 'valueB': [myFirstAddress, mySecondAddress]}`. By default all filter values are set to `null`. It means, that they will match any event of given type sent from this contract.
2. `Object` - Additional filter options, see [filters](#chain3mcfilter) parameter 1 for more. By default filterObject has field 'address' set to address of the contract. Also first topic is the signature of event.
3. `Function` - (optional) If you pass a callback as the last parameter it will immediately start watching and you don't need to call `myEvent.watch(function(){})`. See [this note](#using-callbacks) for details.

##### Callback return


`Object` - An event object as follows:

- `args`: `Object` - The arguments coming from the event.
- `event`: `String` - The event name.
- `logIndex`: `Number` - integer of the log index position in the block.
- `transactionIndex`: `Number` - integer of the transactions index position log was created from.
- `transactionHash`: `String`, 32 Bytes - hash of the transactions this log was created from.
- `address`: `String`, 32 Bytes - address from which this log originated.
- `blockHash`: `String`, 32 Bytes - hash of the block where this log was in. `null` when its pending.
- `blockNumber`: `Number` - the block number where this log was in. `null` when its pending.


##### Example

```js
var MyContract = tum3.mc.contract(abi);
var myContractInstance = MyContract.at('0x78e97bcc5b5dd9ed228fed7a4887c0d7287344a9');

// watch for an event with {some: 'args'}
var myEvent = myContractInstance.MyEvent({some: 'args'}, {fromBlock: 0, toBlock: 'latest'});
myEvent.watch(function(error, result){
   ...
});

// would get all past logs again.
var myResults = myEvent.get(function(error, logs){ ... });

...

// would stop and uninstall the filter
myEvent.stopWatching();
```

***

#### Contract allEvents

```js
var events = myContractInstance.allEvents([additionalFilterObject]);

// watch for changes
events.watch(function(error, event){
  if (!error)
    console.log(event);
});

// Or pass a callback to start watching immediately
var events = myContractInstance.allEvents([additionalFilterObject,] function(error, log){
  if (!error)
    console.log(log);
});

```

Will call the callback for all events which are created by this contract.

##### Parameters

1. `Object` - Additional filter options, see [filters](#chain3mcfilter) parameter 1 for more. By default filterObject has field 'address' set to address of the contract. Also first topic is the signature of event.
2. `Function` - (optional) If you pass a callback as the last parameter it will immediately start watching and you don't need to call `myEvent.watch(function(){})`. See [this note](#using-callbacks) for details.

##### Callback return


`Object` - See [Contract Events](#contract-events) for more.

##### Example

```js
var MyContract = tum3.mc.contract(abi);
var myContractInstance = MyContract.at('0x78e97bcc5b5dd9ed228fed7a4887c0d7287344a9');

// watch for an event with {some: 'args'}
var events = myContractInstance.allEvents({fromBlock: 0, toBlock: 'latest'});
events.watch(function(error, result){
   ...
});

// would get all past logs again.
events.get(function(error, logs){ ... });

...

// would stop and uninstall the filter
myEvent.stopWatching();
```

***

<h4 id="chain3encodeParams">tum3.encodeParams </h4>


    tum3.encodeParams

Encode a list of parameters array into HEX codes.
##### Parameters

- `types` - list of types of params
- `params ` - list of values of params

##### Example

```js
var Tum3 = require('../index.js');
var tum3 = new Tum3();


// Test with list of parameters
var types = ['int','string'];
var args = [100, '4000'];

var dataHex = '0x' + tum3.encodeParams(types, args);
console.log("encoded params:", dataHex);

// outputs
encoded params:0x0000000000000000000000000000000000000000000000000000000000000064000000000000000000000000000000000000000000000000000000000000004000000000000000000000000000000000000000000000000000000000000000043430303000000000000000000000000000000000000000000000000000000000
```


***

<h4 id="chain3mcnamereg">tum3.mc.namereg </h4>


    tum3.mc.namereg

Returns GlobalRegistrar object.

##### Usage

see [namereg](https://github.com/MOACChain/tum3.js/blob/master/example/namereg.html) example

***

<h4 id="chain3mcsendibantransaction">tum3.mc.sendIBANTransaction </h4>


```js
var txHash = tum3.mc.sendIBANTransaction('0x00c5496aee77c1ba1f0854206a26dda82a81d6d8', 'XE81ETHXREGGAVOFYORK', 0x100);
```

Sends IBAN transaction from user account to destination IBAN address.

##### Parameters

- `string` - address from which we want to send transaction
- `string` - IBAN address to which we want to send transaction
- `value` - value that we want to send in IBAN transaction

***

<h4 id="chain3mciban">tum3.mc.iban </h4>


```js
var i = new tum3.mc.iban("XE81ETHXREGGAVOFYORK");
```

***

<h4 id="chain3mcibanfromaddress">tum3.mc.iban.fromAddress </h4>


```js
var i = tum3.mc.iban.fromAddress('0x00c5496aee77c1ba1f0854206a26dda82a81d6d8');
console.log(i.toString()); // 'XE7338O073KYGTWWZN0F2WZ0R8PX5ZPPZS
```

***

<h4 id="chain3mcibanfrombban">tum3.mc.iban.fromBban </h4>


```js
var i = tum3.mc.iban.fromBban('ETHXREGGAVOFYORK');
console.log(i.toString()); // "XE81ETHXREGGAVOFYORK"
```

***

<h4 id="chain3mcibancreateindirect">tum3.mc.iban.createIndirect </h4>


```js
var i = tum3.mc.iban.createIndirect({
  institution: "XREG",
  identifier: "GAVOFYORK"
});
console.log(i.toString()); // "XE81ETHXREGGAVOFYORK"
```

***

<h4 id="chain3mcibanisvalid">tum3.mc.iban.isValid </h4>


```js
var valid = tum3.mc.iban.isValid("XE81ETHXREGGAVOFYORK");
console.log(valid); // true

var valid2 = tum3.mc.iban.isValid("XE82ETHXREGGAVOFYORK");
console.log(valid2); // false, cause checksum is incorrect

var i = new tum3.mc.iban("XE81ETHXREGGAVOFYORK");
var valid3 = i.isValid();
console.log(valid3); // true

```

***

<h4 id="chain3mcibanisdirect">tum3.mc.iban.isDirect </h4>


```js
var i = new tum3.mc.iban("XE81ETHXREGGAVOFYORK");
var direct = i.isDirect();
console.log(direct); // false
```

***

<h4 id="chain3mcibanisindirect">tum3.mc.iban.isIndirect </h4>


```js
var i = new tum3.mc.iban("XE81ETHXREGGAVOFYORK");
var indirect = i.isIndirect();
console.log(indirect); // true
```

***

<h4 id="chain3mcibanchecksum">tum3.mc.iban.checksum </h4>


```js
var i = new tum3.mc.iban("XE81ETHXREGGAVOFYORK");
var checksum = i.checksum();
console.log(checksum); // "81"
```

***

<h4 id="chain3mcibaninstitution">tum3.mc.iban.institution </h4>


```js
var i = new tum3.mc.iban("XE81ETHXREGGAVOFYORK");
var institution = i.institution();
console.log(institution); // 'XREG'
```

***

<h4 id="chain3mcibanclient">tum3.mc.iban.client </h4>


```js
var i = new tum3.mc.iban("XE81ETHXREGGAVOFYORK");
var client = i.client();
console.log(client); // 'GAVOFYORK'
```

***

<h4 id="chain3mcibanaddress">tum3.mc.iban.address </h4>


```js
var i = new tum3.mc.iban('XE7338O073KYGTWWZN0F2WZ0R8PX5ZPPZS');
var address = i.address();
console.log(address); // '00c5496aee77c1ba1f0854206a26dda82a81d6d8'
```

***

<h4 id="chain3mcibantostring">tum3.mc.iban.toString </h4>


```js
var i = new tum3.mc.iban('XE7338O073KYGTWWZN0F2WZ0R8PX5ZPPZS');
console.log(i.toString()); // 'XE7338O073KYGTWWZN0F2WZ0R8PX5ZPPZS'
```

***

<h4 id="chain3vnode_address">tum3.vnode.address </h4>


```js
tum3.setProvider(new tum3.providers.HttpProvider('http://localhost:8545'));
console.log("VNODE:", tum3.vnode.address);
```

***

<h4 id="vnode_scsservice">tum3.vnode.scsService </h4>


```js
console.log("VNODE service:", tum3.vnode.scsService);
```

***

<h4 id="vnode_servicecfg">tum3.vnode.servicecfg </h4>


```js
console.log("VNODE servicecfg:", tum3.vnode.servicecfg);
```


***

<h4 id="vnode_showtopublic">tum3.vnode.showToPublic </h4>


```js
console.log("VNODE showToPublic:", tum3.vnode.showToPublic);
```


***

<h4 id="vnode_vnodeip">tum3.vnode.ip </h4>


```js
console.log("VNODE IP:", tum3.vnode.ip);
```

***

<h4 id="scs_directcall">tum3.scs.directCall </h4>


```js
console.log("Get MicroChain constant call:", tum3.scs.directCall(tx));
```

***

<h4 id="scs_getblock">tum3.scs.getBlock </h4>


```js
tum3.setScsProvider(new tum3.providers.HttpProvider('http://localhost:8548'));
mlist = tum3.scs.getMicroChainList();
console.log("SCS MicroChain List:", mlist);
mcAddress=mlist[0];
console.log("block 1:", tum3.scs.getBlock(mcAddress, '0x1'));
```

***

<h4 id="scs_getblocklist">tum3.scs.getBlockList </h4>


```js
tum3.setScsProvider(new tum3.providers.HttpProvider('http://localhost:8548'));
mlist = tum3.scs.getMicroChainList();
console.log("SCS MicroChain List:", mlist);
mcAddress=mlist[0];
console.log("block 1:", tum3.scs.getBlock(mcAddress, '0x1', '0x5'));
```

***

<h4 id="scs_getblocknumber">tum3.scs.getBlockNumber</h4>


```js
console.log("MicroChain block number:", tum3.scs.getBlockNumber('0xECd1e094Ee13d0B47b72F5c940C17bD0c7630326'));
```

***

<h4 id="scs_getdappstate">tum3.scs.getDappState</h4>


```js
console.log("MicroChain status:", tum3.scs.getDappState('0xECd1e094Ee13d0B47b72F5c940C17bD0c7630326'));
```

***

<h4 id="scs_getmicrochainlist">tum3.scs.getMicroChainList </h4>


```js
tum3.setScsProvider(new tum3.providers.HttpProvider('http://localhost:8548'));
mlist = tum3.scs.getMicroChainList();
console.log("SCS MicroChain List:", mlist);
```

***

<h4 id="scs_getmicrochaininfo">tum3.scs.getMicroChainInfo </h4>


```js
tum3.setScsProvider(new tum3.providers.HttpProvider('http://localhost:8548'));
minfo = tum3.scs.getMicroChainInfo('0xECd1e094Ee13d0B47b72F5c940C17bD0c7630326');
console.log("SCS MicroChain Info:", minfo);
```


***

<h4 id="scs_getnonce">tum3.scs.getNonce </h4>


```js
tum3.setScsProvider(new tum3.providers.HttpProvider('http://localhost:8548'));
console.log("Account Nonce on MicroChain:", tum3.scs.getNonce('0xECd1e094Ee13d0B47b72F5c940C17bD0c7630326', '0x7d0cba876cb9da5fa310a54d29f4687f5dd93fd7'));
```


***

<h4 id="scs_getscsid">tum3.scs.getScsid </h4>


```js
tum3.setScsProvider(new tum3.providers.HttpProvider('http://localhost:8548'));
console.log("SCS ID:", tum3.scs.getScsid());
```


***

<h4 id="scs_gettransactionreceipt">tum3.scs.getTransactionReceipt </h4>


```js
txreceipt = tum3. scs.getTransactionReceipt('0xECd1e094Ee13d0B47b72F5c940C17bD0c7630326','0x38c9536cc53920017fb5edeea80d181f52e1d52c75b194c8a08485ebe0ed538e');
console.log("MicroChain TX Info:", txreceipt);
```
