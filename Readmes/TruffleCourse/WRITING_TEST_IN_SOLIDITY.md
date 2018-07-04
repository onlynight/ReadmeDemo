编写 Solidity 测试脚本
=====================

与 JavaScript 编写的测试脚本一样，基本特性也一直，支持净室环境，可以访问任意不说过的合约。
Truffle的可靠性测试框架是基于以下想法构建的:

- Solidity 编写的脚本不继承任何合约。这样就使得你的测试合约尽可能的小，并且给予了你对合约的所有控制权。
- 可靠性测试不应该依赖于任何断言库。Truffle为您提供了一个默认的断言库，但是您可以根据需要随时更改这个库。
- 您应该能够对任何 Ethereum 客户机运行您的可靠性测试。

## 例子

让我们看一个例子，在还没有太深入之前。下面是 ```truffle unbox metacoin``` 提供的实度测试示例:

```solidity
import "truffle/Assert.sol";
import "truffle/DeployedAddresses.sol";
import "../contracts/MetaCoin.sol";

contract TestMetacoin {
  function testInitialBalanceUsingDeployedContract() {
    MetaCoin meta = MetaCoin(DeployedAddresses.MetaCoin());

    uint expected = 10000;

    Assert.equal(meta.getBalance(tx.origin), expected, "Owner should have 10000 MetaCoin initially");
  }

  function testInitialBalanceWithNewMetaCoin() {
    MetaCoin meta = new MetaCoin();

    uint expected = 10000;

    Assert.equal(meta.getBalance(tx.origin), expected, "Owner should have 10000 MetaCoin initially");
  }
}
```

你将会看到如下输出：

```text
$ truffle test
Compiling ConvertLib.sol...
Compiling MetaCoin.sol...
Compiling truffle/Assert.sol
Compiling truffle/DeployedAddresses.sol
Compiling ../test/TestMetacoin.sol...

  TestMetacoin
    ✓ testInitialBalanceUsingDeployedContract (61ms)
    ✓ testInitialBalanceWithNewMetaCoin (69ms)

  2 passing (3s)
```

## 测试结构

为了更好的了解发生了什么，我们深入讨论下细节。

### ASSERTIONS

你的断言像 ```Assert.equal()``` 是由 ```truffle/Assert.sol``` 提供给你的。这是默认的断言库，
但是只要库通过触发正确的断言事件与 Truffle 的测试运行器松散集成，就可以包含您自己的断言库。
您可以在 ```Assert.sol``` 中找到所有可用的断言函数。

### DEPLOYED ADDRESSES 已发布账户

已部署合约的地址(例如，作为迁移的一部分部署的合同)可以通过 ```truffle/DeployedAddresses.sol``` 获得。
这是由Truffle提供的，并在运行每个套件之前重新编译和重新链接，
以向您的测试提供Truffle的洁净房间环境。本库以如下形式为你的所有已部署合约提供功能:

```solidity
DeployedAddresses.<contract name>();
```
这将返回一个地址，可以用来访问合约。请参阅上面的示例测试。

为了使用已部署的契约，您必须将契约代码导入到您的测试套件中。注意 ```import"../contracts/ MetaCoin.sol”;``` 
在这个例子中。这个导入是相对于存在于./test目录中的测试契约的，为了找到MetaCoin契约，它会在测试目录之外。
然后它使用该契约将地址转换为MetaCoin类型。

## 测试合约名

所有合约的测试必须以 ```test``` 开头，使用大写的 ```T``` 。这样的命名方式就将测试合约和普通合约区分开来了，
让测试运行器知道那个合约代表测试套件。

## 测试方法名

跟测试合约类似，所有的测试方法，都必须以小写单词 ```test``` 开头。每个测试方法都会被当作一个独立的交易，
根据在测试文件中定义的顺序执行。```truffle/Assert.sol``` 提供的断言函数,测试运行程序评估触发事件以确定测试的结果。
断言函数返回一个布尔值，表示断言成功或失败，你可以使用它从测试早期返回，以防止执行错误(例如，Ganache或Truffle开发的错误将暴露)。

## hooks 使用的前后

在如下例子中为你提供了许多测试 hooks 。这些 hooks 是 beforeAll 、 beforeEach 、 afterEach ，
它们是 Mocha 在 Javascript 测试中提供的相同的 hooks 。您可以使用这些 hooks 在每个测试之前和之后，
或者在每个套件运行之前和之后执行安装和拆卸操作。和测试方法一样，每个测试 hooks 都会是一个独立的交易。
您可以通过创建许多带有不同后缀的 hooks 来绕过这个限制，如下面的示例所示:

```solidity
import "truffle/Assert.sol";

contract TestHooks {
  uint someValue;

  function beforeEach() {
    someValue = 5;
  }

  function beforeEachAgain() {
    someValue += 1;
  }

  function testSomeValueIsSix() {
    uint expected = 6;

    Assert.equal(someValue, expected, "someValue should have been 6");
  }
}
```

这个测试合约还表明，您的测试函数和hook函数都共享相同的合约状态。您可以在测试之前设置合约数据，
在测试期间使用该数据，并在测试之后重置它，为下一个测试做准备。注意，就像Javascript测试一样，
下一个测试函数将从运行的前一个测试函数的状态继续。

## 高级特性

```Solidity``` 有一些新特新可以让你使用特殊的用力来测试。

### 异常测试

你可以很容易地测试你的合同应不应该抛出一个异常。（ 例如：require()/ assert()/revert() 语句;之前的版本的 ```throw```)。
这个话题是特约作家西蒙·德·拉·鲁维埃(Simon de la Rouviere)在他的 Truffle Solidity [测试教程][1]中首次提出的。
本教程不推荐的关键字 ```throw``` 大量使用异常。从Solidity v0.4.13开始取而代之的是 revert()、require()和assert()。

### 测试以太币交易

你也可以测试你的合约是如何接收以太币的，并且用 ```JavaScript``` 与 ```Solidity``` 交互。为了达到这目的，你需要在
 ```Solidity``` 测试脚本中写一个名为 ```initialBalance``` 的方法，并且返回 ```uint``` 。
 这可以直接写为函数或公共变量，如下所示。当你的测试合约被部署到网络上时，Truffle将从你的测试账户发送到你的测试合约。
 你的测试合约中就可以使用这个以太币来进行测试。注意，initialBalance是可选的，不是必需的。

```solidity
import "truffle/Assert.sol";
import "truffle/DeployedAddresses.sol";
import "../contracts/MyContract.sol";

contract TestContract {
  // Truffle will send the TestContract one Ether after deploying the contract.
  uint public initialBalance = 1 ether;

  function testInitialBalanceUsingDeployedContract() {
    MyContract myContract = MyContract(DeployedAddresses.MyContract());

    // perform an action which sends value to myContract, then assert.
    myContract.send(...);
  }

  function () {
    // This will NOT be executed when Ether is sent. \o/
  }
}
```

请注意，Truffle以不执行回退函数的方式发送到测试合约，因此您仍然可以在固态测试中对高级测试用例使用回退函数。

[1]: https://truffleframework.com/tutorials/testing-for-throws-in-solidity-tests
