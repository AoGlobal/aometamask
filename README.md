# aometamask



## Install

```bash
npm install aometamask -S
```



## Quick Start

main.js

```javascript
import Vue from "vue";
import aometamask from "aometamask";
import "aometamask/lib/aometamask.css";
Vue.use(aometamask);
```

template

```vue
<aometamask />
```



## Properties

`tokenInfo: Object`
{symbol, decimals}
公链默认币种信息

`browserurl: String`
区块浏览器地址

##### Example

```vue
<aometamask browserurl="https://hecoinfo.com" :tokenInfo="{
	symbol: "HT",
	decimals: "18"
}" />
```





## Contents

- [API](#api)
  - [1. isMetaMaskInstalled](#isMetaMaskInstalled)
  - [2. isMetaMask](#isMetaMask)
  - [3. connect](#connect)
  - [4. getAccount](#getAccount)
  - [5. getChainId](#getChainId)
  - [6. getBalance](#getBalance)
  - [7. onChainChanged](#onChainChanged)
  - [8. onAccountsChanged](#onAccountsChanged)
  - [9. onMessage](#onMessage)
  - [10. sendTx](#sendTx)
  - [11. callContract](#callContract)
  - [12. sendContract](#sendContract)
  - [13. onTxHash](#onTxHash)
  - [14. getTxRecord](#getTxRecord)
  - [15. getPendingTxRecord](#getPendingTxRecord)
  - [16. clearTxRecord](#clearTxRecord)
  - [17. onTxStatusChange](#onTxStatusChange)



### <a name='api'></a>API



#### <a name='isMetaMaskInstalled'></a>1. isMetaMaskInstalled

是否安装了 MetaMask

##### Returns

`Boolean`

##### Example

```javascript
this.$metamask.isMetaMaskInstalled();
```



---



#### <a name='isMetaMask'></a>2. isMetaMask

是否 MetaMask

##### Returns

`Boolean`

##### Example

```javascript
this.$metamask.isMetaMask();
```



---



#### <a name='connect'></a>3. connect

连接插件

##### Returns

`Array<string>`

##### Example

```javascript
this.$metamask.connect().then(console.log);
> ["0x40e5A542087FA4b966209707177b103d158Fd3A4"];
```



---



#### <a name='getAccount'></a>4. getAccount

获取账户

##### Returns

`Array<string>`

##### Example

```javascript
this.$metamask.getAccount().then(console.log);
> ["0x40e5A542087FA4b966209707177b103d158Fd3A4"];
```



---



#### <a name="getChainId"></a>5. getChainId

获取当前公链 ID

##### Returns

`String`

##### Example

```javascript
this.$metamask.getChainId().then(console.log);
> '1'
```



---



#### <a name="getBalance"></a>6. getBalance

获取余额

##### Returns

`String`

##### Example

```javascript
this.$metamask.getBalance().then(console.log);
> '1000000000000000000'
```



---



#### <a name="onChainChanged"></a>7. onChainChanged

监听公链变化

##### Parameters

`Function`

##### Example

```javascript
this.$metamask.onChainChanged((chainId) => {
  console.log(chainId);
});
```



---



#### <a name="onAccountsChanged"></a>8. onAccountsChanged

监听账户变化

##### Parameters

`Function`

##### Example

```javascript
this.$metamask.onAccountsChanged((accounts) => {
  console.log(accounts);
});
> ["0x40e5A542087FA4b966209707177b103d158Fd3A4"];
```



---



#### <a name="onMessage"></a>9. onMessage

监听消息

##### Parameters

`Function`

##### Example

```javascript
this.$metamask.onMessage((message) => {
  console.log(message);
});
```



------



#### <a name="sendTx"></a>10. sendTx

发送交易

##### Parameters

`transactionParameters`- `Object`:

- `chainId`: `String`
- `nonce`: `String`
- `gasPrice`: `String`
- `gas`: `String`
- `from`: `String`
- `to`: `String`
- `value`: `String`
- `data`: `String`

##### Returns

`String`

##### Example

```javascript
this.$metamask.sendTx({
	from: "0x6c999dbc796102774E7CF2b45eD9097a8C0F4d7A",
	to: "0x867f1469356D37313406b75c461fA057c829c749",
	value: "0xfffffff",
}).then(console.log);
> '0xddf72a5196e9464194a7377fb94831d36875f2d8ebb6f9b4829838b8bcd780ca'
```



---



#### <a name="callContract"></a>11. callContract

调用合约读的方法

##### Parameters

`contractAddress`-`String`: 合约地址

`abi`-`JSON|Array`: 合约ABI

`name`-`String`: 调用的合约方法名

`params`-`Array`调用的合约方法需要传的参数

##### Example

```javascript
this.$metamask.callContract("0x0298c2b32eae4da002a15f36fdf7615bea3da047", abi, 'symbol').then(console.log)
```



---



#### <a name="sendContract"></a>12. sendContract

调用合约写的方法

##### Parameters

`contractAddress`-`String`: 合约地址

`abi`-`JSON|Array`: 合约ABI

`name`-`String`: 调用的合约方法名

`params`-`Array`调用的合约方法需要传的参数

##### Example

```javascript
this.$metamask.sendContract("0x0298c2b32eae4da002a15f36fdf7615bea3da047", abi, 'transfer', ["0x867f1469356D37313406b75c461fA057c829c749", "0x1232889"]).then(console.log)
```



---



#### <a name="onTxHash"></a>13. onTxHash

监听交易

##### Parameters

`txHash`- `String`: 交易 hash

##### Example

```javascript
this.$metamask.onTxHash('0xddf72a5196e9464194a7377fb94831d36875f2d8ebb6f9b4829838b8bcd780ca').then(console.log)
> {
	from: "0x...",
	to: "0x...",
	tansactionHash: "0x...",
	status: 1,
	blockHash: "0x...",
	blockNumber: 5388714,
	.....
}
```



---



#### <a name="getTxRecord"></a>14. getTxRecord

获取交易记录

##### Returns

`Array`:

- `address`: `String` 地址
- `chainId`: `String` 公链 ID
- `txHash`: `String` 交易 hash
- `status`: `String` 交易状态 pending | success | failed

##### Example

```javascript
this.$metamask.getTxRecord().then(console.log) >
  [{ from, to, chainId, txHash, status }];
```



---



#### <a name="getPendingTxRecord"></a>15. getPendingTxRecord

获取 pending 状态交易记录

##### Returns

`Array`:

- `address`: `String` 地址
- `chainId`: `String` 公链 ID
- `txHash`: `String` 交易 hash
- `status`: `String` 交易状态 pending

##### Example

```javascript
this.$metamask.getPendingTxRecord().then(console.log) >
  [{ from, to, chainId, txHash, status: "pending" }];
```



---



#### <a name="clearTxRecord"></a>16. clearTxRecord

获取 pending 状态交易记录

##### Example

```javascript
this.$metamask.clearTxRecord();
```



---



#### <a name="onTxStatusChange"></a>17. onTxStatusChange

监听交易状态的变化

##### Parameters

`Function`

##### Example

```javascript
this.$metamask.onTxStatusChange((list) => {
  console.log(list); // 交易记录
});
```

---

