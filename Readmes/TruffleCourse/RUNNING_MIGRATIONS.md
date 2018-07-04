运行迁移
=======

迁移脚本是使用 JavaScript 编写的文件，用于帮助你发布智能合约到以太坊网络。
这些文件的职责就是分阶段的部署任务。所有历史的迁移都会保存在一个特殊的 ```Migrations``` 
智能合约中。详细如下：


## 命令行

执行迁移命令：

```bash
$ truffle migrate
```

这个命令将会执行所有在 ```migrations``` 目录下的迁移操作。迁移操作只会执行新添加的迁移命令，如果没有新添加的迁移任务将不会有任何变化。

### 迁移文件

一个迁移文件的例子如下：

文件名： ```4_example_migration.js```

```javascript
var MyContract = artifacts.require("MyContract");

module.exports = function(deployer) {
  // deployment steps
  deployer.deploy(MyContract);
};
```

文件名的前缀是数字开头，这个数字就是迁移脚本执行的顺序，文件名的后半部分尽量让人能读懂是什么操作。

### ARTIFACTS.REQUIRE()

在迁移脚本的开头我们可以使用 ```artifacts.require()``` 方法来告诉 truffle 我们要交互的智能合约。
这个方法和 ```Node.js``` 的 	```require``` 方法类似，但是在我们的示例中，它特别返回一个抽象合约，
我们可以在部署脚本的其余部分中使用它。

如果一个 ```.sol``` 文件中有多个合约，我们应该这样引用合约：

```solidity
// filename: Contracts.sol

contract ContractOne{
	// ...
}

contract ContractTwo{
	// ...
}
```

```javascript
var ContractOne = artifacts.require("ContractOne");
var ContractTwo = artifacts.require("ContractTwo");
```

### module.exports

所有的迁移操作必须导出 ```module.exports``` 。迁移脚本中的 exports 的方法中至少包含一个参数 ```deployer``` 。
还有一些额外的参数可配置，下面会详细讲解。

### 初始化迁移操作

为了使用迁移特性 Truffle 需要你提供一个 ```Migrations``` 合约。这个合约必须包含一个特殊的接口，
你也可以根据你的意愿修改这个合约。对于大多数工程而言，这个合约在初始化的时候迁移一次，然后不会再更新。
当你创建一个 ```truffle init``` 空工程的时候，```truffle``` 也会为你创建这个合约。

文件名： ```contracts/Migrations.sol```

```solidity
pragma solidity ^0.4.8;

contract Migrations {
  address public owner;

  // A function with the signature `last_completed_migration()`, returning a uint, is required.
  uint public last_completed_migration;

  modifier restricted() {
    if (msg.sender == owner) _;
  }

  function Migrations() {
    owner = msg.sender;
  }

  // A function with the signature `setCompleted(uint)` is required.
  function setCompleted(uint completed) restricted {
    last_completed_migration = completed;
  }

  function upgrade(address new_address) restricted {
    Migrations upgraded = Migrations(new_address);
    upgraded.setCompleted(last_completed_migration);
  }
}
```

您必须在第一次迁移中部署此合约，以便利用迁移特性。为此，创建以下迁移：

文件名： ```migrations/1_initial_migration.js```

```javascript
var Migrations = artifacts.require("Migrations");

module.exports = function(deployer) {
  // Deploy the Migrations contract as our only task
  deployer.deploy(Migrations);
};
```

从这里开始，您可以创建带有编号前缀的新迁移，以部署其他契约并执行进一步的部署步骤。
部署流程会根据部署脚本的前缀的数字依次执行。

### DEPLOYER

您的迁移文件将使用部署程序执行部署任务。因此，您可以同步地编写部署任务，它们将按照正确的顺序执行:

```javascript
// Stage deploying A before B
deployer.deploy(A);
deployer.deploy(B);
```

另外，部署人员上的每个功能都可以用 ```Promise``` ，根据前一个任务的执行对部署任务进行排队:

```javascript
// Deploy A, then deploy B, passing in A's newly deployed address
deployer.deploy(A).then(function() {
  return deployer.deploy(B, A.address);
});
```

如果您发现语法更清晰的话，可以将部署编写为一个单一的 ```Promise``` 链。部署程序API在本页的底部进行了讨论。

### 考虑网络配置

可以根据不同的网络进行部署操作。这是一个高级特性，所以在继续之前先看一下网络部分。

为了条件部署，在写迁移脚本的时候要接受第二个参数，称为 ```network``` 。

例子:

```javascript
module.exports = function(deployer, network) {
  if (network == "live") {
    // Do something specific to the network named "live".
  } else {
    // Perform a different step otherwise.
  }
}
```

### 可用账户

迁移也会通过以太坊客户端和 web3 提供的帐户列表，供您在部署过程中使用。这和 ```web3.eth.getAccounts()``` 返回的相同的帐户列表。

```javascript
module.exports = function(deployer, network, accounts) {
  // Use the accounts within your migrations.
}
```

### DEPLOYER API

```deployer``` 有很多功能可以简化你的迁移操作。

#### DEPLOYER.DEPLOY(CONTRACT, ARGS…, OPTIONS)

使用可选的构造函数参数部署合约。这对于单例合约很有用，这类合约只存在一个 dapp 的实例。
部署后会重新设置的合约地址(即： ```contact.address``` 等于新部署的地址)，并且覆盖之前存储的地址。

你可以选择传递一组合约或数组，以加速部署多个合约。另外，最后一个参数是一个可选对象，它可以包含名为 overwrite 和其他交易参数，如 gas 和 from 。
overwrite 被设置为false，同时已经部署了该合约，```deployer``` 将不会重新部署该合约。这对于由外部依赖提供合约地址的某些情况很有用。

在调用 ```deploy``` 命令发布你的合约之前你需要发布并连接依赖的库。下面会详细介绍 ```link``` 方法。

更多信息查看 [truffle-contact][1] 文档

例子：

```javascript
// Deploy a single contract without constructor arguments
deployer.deploy(A);

// Deploy a single contract with constructor arguments
deployer.deploy(A, arg1, arg2, ...);

// Don't deploy this contract if it has already been deployed
deployer.deploy(A, {overwrite: false});

// Set a maximum amount of gas and `from` address for the deployment
deployer.deploy(A, {gas: 4612388, from: "0x...."});

// Deploy multiple contracts, some with arguments and some without.
// This is quicker than writing three `deployer.deploy()` statements as the deployer
// can perform the deployment as a single batched request.
deployer.deploy([
  [A, arg1, arg2, ...],
  B,
  [C, arg1]
]);

// External dependency example:
//
// For this example, our dependency provides an address when we're deploying to the
// live network, but not for any other networks like testing and development.
// When we're deploying to the live network we want it to use that address, but in
// testing and development we need to deploy a version of our own. Instead of writing
// a bunch of conditionals, we can simply use the `overwrite` key.
deployer.deploy(SomeDependency, {overwrite: false});
```

#### DEPLOYER.LINK(LIBRARY, DESTINATIONS)

连接一个已经发布的库合约或者多个合约。 ```destinations``` 可以是一个或者多个合约。如果任何一个合约的 ```destination``` 
都没有依赖已经连接的库合约，那么这个合约库将被忽略。

例子：

```javascript
// Deploy library LibA, then link LibA to contract B, then deploy B.
deployer.deploy(LibA);
deployer.link(LibA, B);
deployer.deploy(B);

// Link LibA to many contracts
deployer.link(LibA, [B, C, D]);
```

#### DEPLOYER.THEN(FUNCTION() {…})

和 ```Promise``` 类似，用来运行部署步骤。使用它在迁移期间调用特定的合约函数来添加、编辑和重组合约数据。

例子：

```javascript
var a, b;
deployer.then(function() {
  // Create a new version of A
  return A.new();
}).then(function(instance) {
  a = instance;
  // Get the deployed instance of B
  return B.deployed();
}).then(function(instance) {
  b = instance;
  // Set the new instance of A's address on B via B's setA() function.
  return b.setA(a.address);
});
```

[1]: https://github.com/trufflesuite/truffle-contract
