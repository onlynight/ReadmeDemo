用 JavaScript 编写测试脚本
=========================

Truffle使用 [Mocha][1] 测试框架和 [Chai][2] 断言，为编写 JavaScript 测试提供了坚实的框架。
让我们深入研究，看看 Truffle 是如何建立在 Mocha 之上，让测试你的合约变得轻而易举。

```text
注意：如果你不熟悉用 Mocha 编写单元测试，你需要先看看这个文档 [Mocha's][1]
```

## 使用 CONTRACT() 代替 DESCRIBE()

在结构上，您的测试应该与Mocha的测试保持基本不变:您的测试应该存在于./test目录中，它们应该以.js扩展结束，
并且它们应该包含Mocha将识别为自动测试的代码。使 Truffle 测试与摩卡测试不同的是contract()函数:这个函数
的工作原理与describe()完全相同，只是它支持松露的洁净室特性。

- 在每一个 ```contract()``` 运行之前，你的合约被重新部署到正在运行的Ethereum客户端，
因此其内部的测试运行时具有一个干净的契约状态。

- ```contract()``` 方法将会提供一个账户列表，这个列表是由你的以太坊客户端建立的，
你可以用这些账户来编写测试脚本。

由于Truffle使用的是Mocha，所以在没有使用Truffle clean-room 功能的情况下，你仍然可以使用describe()来进行普通的Mocha测试。

## 在测试中使用抽象合约

合约抽象提供了 js 和智能合约交互的可能。由于 Truffle 不知道你要和哪个合约交互，所以你需要明确的申明这些你要使用的合约。
使用 ```artifacts.require()``` 方法来告诉 ```truffle``` 你要使用哪个抽象合约。
正如您在下面的示例中所看到的，您可以使用这个抽象来确保您的合约正常工作。

更多智能合约抽象请查看 [与智能合约交互][3] 板块。


## 使用 ARTIFACTS.REQUIRE()

在测试中使用artifact .require()与在迁移中使用相同;你只需要传递合约的名称。请参阅迁移部分中的artifacts.require()文档以了解详细的用法。

## 使用 WEB3

每个测试文件中都有一个web3实例，配置为正确的提供者。所以直接调用web3.eth.getBalance没有问题。

## 例子

### 使用 ```.THEN```

这个一个 Truffle Box 提供的 MetaCoin 项目中的例子，指定可用的 Ethereum 账户的account数组，
以及我们使用 ```artifactes .require()``` 用于直接与我们的合约交互。

文件： ```./test/metacoin.js```

```javascript
var MetaCoin = artifacts.require("MetaCoin");

contract('MetaCoin', function(accounts) {
  it("should put 10000 MetaCoin in the first account", function() {
    return MetaCoin.deployed().then(function(instance) {
      return instance.getBalance.call(accounts[0]);
    }).then(function(balance) {
      assert.equal(balance.valueOf(), 10000, "10000 wasn't in the first account");
    });
  });
  it("should call a function that depends on a linked library", function() {
    var meta;
    var metaCoinBalance;
    var metaCoinEthBalance;

    return MetaCoin.deployed().then(function(instance) {
      meta = instance;
      return meta.getBalance.call(accounts[0]);
    }).then(function(outCoinBalance) {
      metaCoinBalance = outCoinBalance.toNumber();
      return meta.getBalanceInEth.call(accounts[0]);
    }).then(function(outCoinBalanceEth) {
      metaCoinEthBalance = outCoinBalanceEth.toNumber();
    }).then(function() {
      assert.equal(metaCoinEthBalance, 2 * metaCoinBalance, "Library function returned unexpected function, linkage may be broken");
    });
  });
  it("should send coin correctly", function() {
    var meta;

    // Get initial balances of first and second account.
    var account_one = accounts[0];
    var account_two = accounts[1];

    var account_one_starting_balance;
    var account_two_starting_balance;
    var account_one_ending_balance;
    var account_two_ending_balance;

    var amount = 10;

    return MetaCoin.deployed().then(function(instance) {
      meta = instance;
      return meta.getBalance.call(account_one);
    }).then(function(balance) {
      account_one_starting_balance = balance.toNumber();
      return meta.getBalance.call(account_two);
    }).then(function(balance) {
      account_two_starting_balance = balance.toNumber();
      return meta.sendCoin(account_two, amount, {from: account_one});
    }).then(function() {
      return meta.getBalance.call(account_one);
    }).then(function(balance) {
      account_one_ending_balance = balance.toNumber();
      return meta.getBalance.call(account_two);
    }).then(function(balance) {
      account_two_ending_balance = balance.toNumber();

      assert.equal(account_one_ending_balance, account_one_starting_balance - amount, "Amount wasn't correctly taken from the sender");
      assert.equal(account_two_ending_balance, account_two_starting_balance + amount, "Amount wasn't correctly sent to the receiver");
    });
  });
});
```

### 使用 ASYNC/AWAIT

下面是一个类似的例子：

```javascript
const MetaCoin = artifacts.require("MetaCoin");

contract('2nd MetaCoin test', async (accounts) => {

  it("should put 10000 MetaCoin in the first account", async () => {
     let instance = await MetaCoin.deployed();
     let balance = await instance.getBalance.call(accounts[0]);
     assert.equal(balance.valueOf(), 10000);
  })

  it("should call a function that depends on a linked library", async () => {
    let meta = await MetaCoin.deployed();
    let outCoinBalance = await meta.getBalance.call(accounts[0]);
    let metaCoinBalance = outCoinBalance.toNumber();
    let outCoinBalanceEth = await meta.getBalanceInEth.call(accounts[0]);
    let metaCoinEthBalance = outCoinBalanceEth.toNumber();
    assert.equal(metaCoinEthBalance, 2 * metaCoinBalance);

  });

  it("should send coin correctly", async () => {

    // Get initial balances of first and second account.
    let account_one = accounts[0];
    let account_two = accounts[1];

    let amount = 10;


    let instance = await MetaCoin.deployed();
    let meta = instance;

    let balance = await meta.getBalance.call(account_one);
    let account_one_starting_balance = balance.toNumber();

    balance = await meta.getBalance.call(account_two);
    let account_two_starting_balance = balance.toNumber();
    await meta.sendCoin(account_two, amount, {from: account_one});

    balance = await meta.getBalance.call(account_one);
    let account_one_ending_balance = balance.toNumber();

    balance = await meta.getBalance.call(account_two);
    let account_two_ending_balance = balance.toNumber();

    assert.equal(account_one_ending_balance, account_one_starting_balance - amount, "Amount wasn't correctly taken from the sender");
    assert.equal(account_two_ending_balance, account_two_starting_balance + amount, "Amount wasn't correctly sent to the receiver");
  });

})
```

## 特定测试

测试单个脚本如下：

```bash
$ truffle test ./test/metacoin.js
```

查看所有的[命令行][4]

## 进阶

truffle 可以让你访问 Mocha 的配置，这样你就可以改变 Mocha 的行为。有关详细信息，请参阅[项目配置][5]部分。

[1]: https://mochajs.org/
[2]: http://www.chaijs.com/
[3]: todo
[4]: https://truffleframework.com/docs/advanced/commands#test
[5]: https://truffleframework.com/docs/advanced/configuration#mocha
