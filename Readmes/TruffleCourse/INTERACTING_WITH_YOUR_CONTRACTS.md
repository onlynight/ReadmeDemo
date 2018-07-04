与智能合约交互
============

## 概述

为了与合约交互而向Ethereum网络编写原始请求，那么您很快就会意识到，编写这些请求是笨重而麻烦的。
同样，你可能会发现管理每个请求的状态非常复杂。幸运的是，Truffle 为您解决了这种复杂性，使得与合约的交互变得轻而易举。

## 读写数据

Ethereum网络对向网络写入数据和从网络读取数据进行了区分，这种区分在如何编写应用程序中起着重要作用。
通常，写入数据称为交易( transaction )，而读取数据称为调用( call )。事务和调用的处理方式非常不同，具有以下特征。

### 交易 （Transactions）

交易从根本上改变了网络的状态。交易可以是简单到将以太币发送到另一个帐户，也可以是复杂到执行合约函数或向网络添加新合约。
交易的定义特性是它写入(或更改)数据。交易的运行成本很高，称为“gas”，交易的处理需要时间。
当您通过交易执行合约的功能时，您不能接收该函数的返回值，因为交易没有立即处理。一般来说，通过交易执行的函数不会返回值;
它们将返回一个交易id。所以总的来说,交易（Transactions）特性如下:

- 消费 gas （以太币 ether）
- 修改网络状态
- 不能马上执行
- 不会暴露一个返回值（之返回一个交易id）

### 调用 （Calls）

调用正好相反。调用可以是在网络中执行代码，不会永久的改变数据（状态）。调用是免费的，它的特性就是读取数据。
当你使用 call 调用一个合约中的一份方法时，函数会马上返回。总的来说 Calls 的特性：

- 免费（不消耗 gas）
- 不改变网络状态
- 马上执行
- 会暴露一个返回值

选择使用 Tranaction 或 Call 很简单，取决于你是读取数据还是写入数据。

## 合约抽象简介

合约抽象是使用 Javascript 与 Ethereum 合约交互的 bread 和 butter 。
简而言之，合约抽象是一种包装代码，它使与合约的交互变得容易，从而让你忘记了在引擎下执行的许多引擎和齿轮。
Truffle通过 [Truffle-contract][1] 模块使用它自己的合约抽象，下面描述的就是这个合约抽象。

为了理解合约抽象的作用，我们首先需要一个合约例子。我们将使用MetaCoin合约，通过 ```truffle unbox metacoin``` 提供给您。

```solidity
pragma solidity ^0.4.2;

import "./ConvertLib.sol";

// This is just a simple example of a coin-like contract.
// It is not standards compatible and cannot be expected to talk to other
// coin/token contracts. If you want to create a standards-compliant
// token, see: https://github.com/ConsenSys/Tokens. Cheers!

contract MetaCoin {
    mapping (address => uint) balances;

    event Transfer(address indexed _from, address indexed _to, uint256 _value);

    function MetaCoin() {
        balances[tx.origin] = 10000;
    }

    function sendCoin(address receiver, uint amount) returns(bool sufficient) {
        if (balances[msg.sender] < amount) return false;
        balances[msg.sender] -= amount;
        balances[receiver] += amount;
        Transfer(msg.sender, receiver, amount);
        return true;
    }

    function getBalanceInEth(address addr) returns(uint){
        return ConvertLib.convert(getBalance(addr),2);
    }

    function getBalance(address addr) returns(uint) {
        return balances[addr];
    }
}
```

除了构造函数(sendCoin、getBalanceInEth和getBalance)之外，该契约还有三个方法。
这三个方法都可以作为 transaction 或 call 执行。

现在让我们看看 Truffle 提供给我们的名为 MetaCoin 的 Javascript 对象，
这是在 truffle console 中的:

```javascript
// Print the deployed version of MetaCoin.
// Note that getting the deployed version requires a promise, hence the .then.
MetaCoin.deployed().then(function(instance) {
  console.log(instance);
});

// outputs:
//
// Contract
// - address: "0xa9f441a487754e6b27ba044a5a8eb2eec77f6b92"
// - allEvents: ()
// - getBalance: ()
// - getBalanceInEth: ()
// - sendCoin: ()
// ...
```

请注意，合约抽象包含与我们的合约中相同的功能。它还包含一个指向MetaCoin合同已部署版本的地址。

## 执行合约方法

使用合约抽象你可以很容易的在以太坊网络中调用合约。

### 创建一个交易

MetaCoin 中有三个方法可以执行。如果你自己看你会发现 ```setCoin``` 方法是用来在网络中发送币的。
这个方法需要修改网络中的状态。

我们调用 setCoin 方法时候我们使用 transaction 调用。在下面的例子中将会转账10个 Meta 币，
就是说要永久的修改网络状态：

```javascript
var account_one = "0x1234..."; // an address
var account_two = "0xabcd..."; // another address

var meta;
MetaCoin.deployed().then(function(instance) {
  meta = instance;
  return meta.sendCoin(account_two, 10, {from: account_one});
}).then(function(result) {
  // If this callback is called, the transaction was successfully processed.
  alert("Transaction successful!")
}).catch(function(e) {
  // There was an error! Handle it.
})
```

上面的代码有一些有趣的地方:

- 我们直接调用合约抽象的 ```setCoin``` 方法。这个调用将会返回一个 transactin 结果。
- 当交易成功执行以后，回调方法才会执行。这就让生命周期管理改变的简单，你不需要自己检查交易状态。
- 我们将对象作为第三个参数传递给sendCoin。注意，在我们的Solidity合约中，sendCoin函数没有第三个参数。
上面看到的是一个特殊的对象，它总是作为最后一个参数传递给一个函数，该函数允许您编辑交易的特定细节。
在这里，我们设置了from address确保该交易来自account_one。

### 创建一个调用 call

继续使用 MetaCion 例子，注意 ```getBalance``` 函数是从网络读取数据的一个很好的候选函数。
它不需要做任何更改，因为它只返回传递给它的地址的 Meta Coin 余额。让我们试一试:

```javascript
var account_one = "0x1234..."; // an address

var meta;
MetaCoin.deployed().then(function(instance) {
  meta = instance;
  return meta.getBalance.call(account_one, {from: account_one});
}).then(function(balance) {
  // If this callback is called, the call was successfully executed.
  // Note that this returns immediately without any waiting.
  // Let's print the return value.
  console.log(balance.toNumber());
}).catch(function(e) {
  // There was an error! Handle it.
})
```

这里有意思的地方：

- 我们执行了 ```call``` 方法，告诉以太坊网络我们不会修改网络中的数据。
- 我们接收到了一个返回值，而不是一个交易id。

```text
警告：
我们把返回值转换为数字，因为这个demo中数字很小。通常给如果如果你要转换一个超大整形需要
使用 JavaScript 提供 ```BigNumber``` 库，否则会报错或者抛出异常。
```

## 捕获事件

你的合约可以触发事件，你可以通过这些事件获得更多的了解你的合约在做什么。
处理事件的最简单方法是处理触发事件的交易的结果对象，如下所示:

```solidity
var account_one = "0x1234..."; // an address
var account_two = "0xabcd..."; // another address

var meta;
MetaCoin.deployed().then(function(instance) {
  meta = instance;  
  return meta.sendCoin(account_two, 10, {from: account_one});
}).then(function(result) {
  // result is an object with the following values:
  //
  // result.tx      => transaction hash, string
  // result.logs    => array of decoded events that were triggered within this transaction
  // result.receipt => transaction receipt object, which includes gas used

  // We can loop through result.logs to see if we triggered the Transfer event.
  for (var i = 0; i < result.logs.length; i++) {
    var log = result.logs[i];

    if (log.event == "Transfer") {
      // We found the event!
      break;
    }
  }
}).catch(function(err) {
  // There was an error! Handle it.
});
```

## 处理交易结果

当我们创建交易时，将获得一个结果对象，该对象将提供关于交易的大量信息。
具体来说，有以下信息:

- ```result.tx``` (string) - 交易哈希值
- ```result.log``` (array) - 解码后的事件
- ```result.receipt``` （object）- 交易收据

更多信息请查看 ```truffle-contract``` 的[说明][2]。 

## 向网络中添加一个新的合约

如果您已经有一个合约地址，您可以创建一个新的合约抽象来表示该地址的合约。

```javascript
var instance = MetaCoin.at("0x1234....");
```

## 给合约转账

你可能只想直接发送以太币 ether 到合约，或者触发合约的回退功能。
你可以使用以下两个选项之一来实现这一点：

方式一:
直接向合约发送一个交易使用 ```instance.sendTransaction()``` 。这就像所有可用的合约实例函数一样，
和 ```web3.eth.sendTransaction``` 方法相同，但是 ```web3.eth.sendTransaction``` 没有回调。
如果你没有申明 ```to``` ，它将会被自动填充。

```javascript
instance.sendTransaction({...}).then(function(result){
	// same transction result object as above.
});
```

方式二：
直接发送以太币也有简写：

```javascript
instance.send(web3.toWei(1, "ether")).then(function(result){
	// some result oject as above
});
```

[1]: https://github.com/trufflesuite/truffle-contract
[2]: https://github.com/trufflesuite/truffle-contract
