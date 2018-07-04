truffle （ETH以太坊智能合约集成开发工具） 入门教程
===============================================

## 前言

在你了解区块链开发之前，你有必要了解区块链的一些基础知识，什么是DApp，DApp与传统app的区别，
什么是以太坊，以太坊中的智能合约是什么，智能合约能干什么，什么是共识算法，目前为止常用的共识算法有哪些，
共识算法解决了什么问题，（PoW，PoS，DPoS等等共识算法），基于以太坊的智能合约开发使用哪些工具，使用哪些语言开发，
solidity是什么，智能合约开发与普通开发有什么相同与不同，等等。

你需要提前看下这本书来了解一些入门知识： [《区块链开发指南》][5]

## 简介

truffle是一个以太坊智能合约集成开发测试环境，他和一般的IDE不同，它并没有代码编辑环境，
但是它能够方便的管理智能合约运行的环境，并且提供一套便捷开发智能合约(Smart Contract)的API，
能够有效的提升开发效率。truffle拥有以下特性：

- 内建的智能合约编译、连接、发布和二进制文件管理工具。
- 快速开发自动化测试智能合约脚本。
- 脚本化、可拓展的发布、迁移框架。
- 管理任意公有以及私有的发布网络（1或者其他数字，1是以太坊正式运行网络，其他数字是本地开发网络环境）。
- 使用 **EthPM** 和 **NPM** 进行包管理，使用[ERC190][1]标准规范。
- 拥有与智能合约能直接通信的交互式控制台。
- 可配置的构建管道，支持紧密集成。
- 外部脚本运行器，用于在Truffle环境中执行脚本。

## 运行环境

- 操作系统： windows, linux or mac OS
- NodeJS 5.0+ [node download][2]

## 安装 truffle

```bash
$ npm install -g truffle
```

**当前 truffle 版本为**

```bash
$ truffle version
Truffle v4.1.13 (core: 4.1.13)
Solidity v0.4.24 (solc-js)
```

## 代码编辑器

尽管你可以使用文本编辑器编写智能合约，但是编辑器会让你事半功倍，这里推荐两个solildity代码编辑器。

**1. Sublime Text 3**

熟悉前端开发的同时都知道这个编辑器，安装几个插件就能配置好开发环境。
下载 [Sublime Text 3][3], 安装solildity，nodejs插件，基本的开发环境就算是好了。

**2. VS Code**

VS Code也是轻量级的开发工具，下载 [VS Code][4] , 下载后安装solidity, nodejs 插件。

以上，VSCode在写代码的时候就能够提示语法错误，是VS系列工具的一贯优点，Sublime的优点就是快速轻量。
使用哪个工具完全取决于个人。

## 快速入门

### 目录

[1. 创建项目](#createProject)</br>
[2. 浏览项目](#exploringProject)</br>
[3. 测试](#testing)</br>
[4. 编译](#compiling)</br>
[5. 迁移到truffle开发环境](#migrateToTruffle)</br>
[6. 使用 Ganache 迁移智能合约](#ganacheMigrateContacts)</br>
[7. 与智能合约交互](#interactWithContract)</br>

#### <span id="createProject">1. 创建项目</span>

1. 创建项目文件夹

```bash
$ mkdir MetaCoin
$ cd MetaCoin
```

2. 下载并解压MetaCoin项目（unbox）

```bash
$ truffle unbox metacoin
```

**注意：**

```
使用 truffle unbox <box-name> 下载任意 truffle boxes。

使用 truffle init 初始化一个不包含智能合约的项目。
```

完成以上步骤后，你将会看到工程目录下有如下文件夹。

- ```contracts/``` 合约放在这个目录下（Solidity contracts）
- ```migrations``` 脚本化部署文件
- ```test/``` 测试你的智能合约和应用文件夹
- ```truffle.js``` truffle配置文件

#### <span id="exploringProject">2. 浏览项目</span>

这一章我们是快速入门这里不再详细说明每个文件中的内容，只大概描述下文件的作用，详细的说明请查看truffle的文档。

1. 打开 ```contracts/MetaCoin.sol``` ，这是一个用solidity编写的智能合约，它创建了一个MetaCoin token。
它使用了另外一个在该目录下的合约文件```ConvertLib.sol```。

2. 打开 ```contracts/Migrations.sol``` ，这个是一个独立的文件，没有依赖其他合约，用来更新你发布的只能合约状态。
这个是truffle生成的文件，一般我们不编辑它。

3. 打开 ```migrations/1_initial_deployment.js``` ，这个是用于迁移 ```Migrations.sol``` 的脚本文件。

4. 打开 ```migrations/2_deploy_contracts.js``` ，这个是用于迁移 ```MetaCoin.sol``` 的脚本。
(迁移的脚本会依次执行，以上两个文件的开头都是数字，执行顺序将会按照数字顺序执行。)

5. 打开 ```test/TestMetaCoin.sol``` ，这个是一个用solidity编写的测试文件，他将确保你的智能合约按照你预期的结果运行。

6. 打开 ```test/metacoin.js```，这个是一个用javascript脚本编写的测试文件，他和上面的solidity编写的测试文件功能基本相同。

7. 打开 ```truffle.js``` ，这个是 truffle 配置文件，用来设置网络一起其他项目相关的设置。 这是一个空白文件，
你可以不进行任何配置，truffle 的命令行中有一些内置的默认参数。

#### <span id="testing">3. 测试</span>

1. 在终端中运行脚本：

```bash
$ truffle test ./test/TestMetaCoin.sol
```

**注意：**

```text
在windows中执行这个命令的时候，你会发现 truffle.js 被执行了，而 truffle 并没有执行。
具体解决办法，看
```
[这里][6]解决 truffle 命令无法执行问题。

你将会看到如下输出：

```text
  TestMetacoin
    √ testInitialBalanceUsingDeployedContract (58ms)
    √ testInitialBalanceWithNewMetaCoin (53ms)


  2 passing (648ms)
```

2. 运行 js 测试脚本：

```bash
$ truffle test ./test/metacoin.js
```

你将会看到如下输出：

```text
  Contract: MetaCoin
    √ should put 10000 MetaCoin in the first account
    √ should call a function that depends on a linked library (38ms)
    √ should send coin correctly (95ms)


  3 passing (193ms)
```

#### <span id="compiling">4. 编译</span>

1. 编译智能合约

```bash
$ truffle compile
```

你将会看到如下输出：

```text
Compiling .\contracts\ConvertLib.sol...
Compiling .\contracts\MetaCoin.sol...
Compiling .\contracts\Migrations.sol...
Compiling .\contracts\MyContract.sol...
Writing artifacts to .\build\contracts
```

编译后的文件将会在工程目录下的 ```build``` 目录中。

#### <span id="migrateToTruffle">5. 迁移到truffle开发环境</span>

```text
注意：使用 Ganache，请跳过本节到下一节。
```

为了开发智能合约，我们需要连接到区块链。truffle有内置的私有连用于测试。
这个网络是你电脑上的本地网络，并不会连接到以太坊的主网络。

使用如下命令创建私有连并与其交互：

1. 运行 Truffle develop

```bash
$ truffle develop
```

你将会看到如下输出：

```text
Truffle Develop started at http://127.0.0.1:9545/

Accounts:
(0) 0x627306090abab3a6e1400e9345bc60c78a8bef57
(1) 0xf17f52151ebef6c7334fad080c5704d77216b732
(2) 0xc5fdf4076b8f3a5357c5e395ab970b5b54098fef
(3) 0x821aea9a577a9b44299b9c15c88cf3087f3b5544
(4) 0x0d1d4e623d10f9fba5db95830f7d3839406c6af2
(5) 0x2932b7a2355d6fecc4b5c0b6bd44cc31df247a2e
(6) 0x2191ef87e392377ec08e7c08eb105ef5448eced5
(7) 0x0f4f2ac550a1b4e2280d04c21cea7ebd822934b5
(8) 0x6330a553fc93768f612722bb8c2ec78ac90b3bbc
(9) 0x5aeda56215b167893e80b4fe645ba6d5bab767de

Private Keys:
(0) c87509a1c067bbde78beb793e6fa76530b6382a4c0241e5e4a9ec0a0f44dc0d3
(1) ae6ae8e5ccbfb04590405997ee2d52d2b330726137b875053c36d94e974d162f
(2) 0dbbe8e4ae425a6d2687f1a7e3ba17bc98c673636790f1b8ad91193c05875ef1
(3) c88b703fb08cbea894b6aeff5a544fb92e78a18e19814cd85da83b71f772aa6c
(4) 388c684f0ba1ef5017716adb5d21a053ea8e90277d0868337519f97bede61418
(5) 659cbb0e2411a44db63778987b1e22153c086a95eb6b18bdf89de078917abc63
(6) 82d052c865f5763aad42add438569276c00d3d88a2d062d36b2bae914d58b8c8
(7) aa3680d5d48a8283413f7a108367c7299ca73f553735860a87b08f39395618b7
(8) 0f62d96d6675f32685bbdb8ac13cda7c23436f63efbb9d07700d8669ff12b7c4
(9) 8d5366123cb560bb606379f90a0bfd4769eecc0557f1b362dcae9012b548b1e5

Mnemonic: candy maple cake sugar pudding cream honey rich smooth crumble sweet treat

⚠️  Important ⚠️  : This mnemonic was created for you by Truffle. It is not secure.
Ensure you do not use it on production blockchains, or else you risk losing funds.

truffle(develop)>
```

这里显示了10个账户和它们的私钥，用于和区块链交互。

```truffle(develop)>``` 和 ```geth attach``` 是相同的交互式控制台。

2. 在 **truffle** 交互控制台中，你可以省略 **truffle** 前缀，例如使用 ```truffle compile``` 你可以直接使用 ```compile``` 。
用于发布的命令行是 ```truffle migrate``` , 在交互命令行中使用 ```migrate``` 即可：

```bash
truffle(develop)> migrate
```

你将会看到如下输出：

```text
Using network 'develop'.

Running migration: 1_initial_migration.js
  Deploying Migrations...
  ... 0xfc1305e8437792f4111d189a7215a57700f15211d17cf4d5b723c5cef054c313
  Migrations: 0x8cdaf0cd259887258bc13a92c0a6da92698644c0
Saving successful migration to network...
  ... 0xd7bc86d31bee32fa3988f1c1eabce403a1b5d570340a3a9cdba53a472ee8c956
Saving artifacts...
Running migration: 2_deploy_contracts.js
  Deploying ConvertLib...
  ... 0x9bad657102554ee0183b260e9144c45cff6f1b8acc158535b1d96a03e21a67a1
  ConvertLib: 0x345ca3e014aaf5dca488057592ee47305d9b3e10
  Linking ConvertLib to MetaCoin
  Deploying MetaCoin...
  ... 0xc68fe7303339ebd996b0b1c5ed7dd72498839ad3729ef991419c40b7ea8378fe
  MetaCoin: 0xf25186b5081ff5ce73482ad761db0eb0d25abfbf
Saving successful migration to network...
  ... 0x059cf1bbc372b9348ce487de910358801bbbd1c89182853439bec0afaee6c7db
Saving artifacts...
Running migration: 4_example_migration.js
  Deploying MyContract...
  ... 0x8c2aeb4d8be63db2d5e04459689ef1a84e0116d8805e6149b1b69d26ecc9095a
  MyContract: 0x9fbda871d559710256a2502a2517b794b482db40
Saving successful migration to network...
  ... 0xcfa8ca90b302e023e613f1cd644dd62942c82277d36ceef1df5b97b13f67ae6d
Saving artifacts...
```

这里显示交易IDs，和智能合约的部署地址。

#### <span id="ganacheMigrateContacts">6. 使用 Ganache 迁移智能合约</span>

truffle develop 是一个将多个工具集中的私有连、控制台，你也可以使用 Ganache ，它是一个桌面应用，用于启动你的私有连。
Ganache 能够让新入门以太坊和区块链的开发者更加容易理解，它预先显示了更多信息。

唯一的额外步骤，运行 Ganache ，你可以修改 ```truffle``` 配置文件来指向 ```Ganache``` 实例。

1. 下载并安装 [Ganache][8] 。
2. 打开 ```truffle.js``` 配置文件，替换 ```networks``` 配置：

	```javascript
	module.exports = {
	  networks: {
	    development: {
	      host: "127.0.0.1",
	      port: 7545,
	      network_id: "*"
	    }
	  }
	};
	```

	这个配置就是指定 ```truffle``` 连接到 ```Ganache``` 私有连。

3. 保存并关闭文件。
4. 启动 ```Ganache```

	![ganache_accounts.png](./images/ganache_accounts.png)

5. 在终端中输入 ```migrate``` 命令，迁移智能合约到由 ```Ganache``` 创建的区块链：

	```bash
	$ truffle migrate
	```

	你将会看到如下输出：

	```text
	Using network 'development'.

	Running migration: 1_initial_migration.js
	  Deploying Migrations...
	  ... 0xfc1305e8437792f4111d189a7215a57700f15211d17cf4d5b723c5cef054c313
	  Migrations: 0x13f70ff9440b96e783a9000e2755ac6dbbf33ac4
	Saving successful migration to network...
	  ... 0x0f002d7dc7b04ec9b8e05bc3be03fa11ceaea0e5d5c99ac594dc0fa33e74df39
	Saving artifacts...
	Running migration: 2_deploy_contracts.js
	  Deploying ConvertLib...
	  ... 0x9bad657102554ee0183b260e9144c45cff6f1b8acc158535b1d96a03e21a67a1
	  ConvertLib: 0x45daf5b81c0238d7fc3f094b2d54c00badf96745
	  Linking ConvertLib to MetaCoin
	  Deploying MetaCoin...
	  ... 0x89b661e01f3b910276a9e123881c0dbba728d30fbc0997befaaf0cbad998c6ff
	  MetaCoin: 0x11a3dcd88766fe67f29c73a662b6c40bb63ddce8
	Saving successful migration to network...
	  ... 0x2487356aa6d044b475b8213b14d627541c69447e65645da673f83fc75c39df99
	Saving artifacts...
	Running migration: 4_example_migration.js
	  Deploying MyContract...
	  ... 0x8c2aeb4d8be63db2d5e04459689ef1a84e0116d8805e6149b1b69d26ecc9095a
	  MyContract: 0x09c654238c3fc3bcddda064c637951c7a8a9ce23
	Saving successful migration to network...
	  ... 0xc55fdf833d88082943602c691ec72c62cb2fdc1b5adcc7c232f970c7d69d266b
	Saving artifacts...
	```

	这里显示的是交易id和你部署的合约地址。

6. 在 ```Ganache``` 中，点击 ```Transactions``` 按钮查看已经被处理的交易。
7. 为了与智能合约交互，你可使用 ```truffle``` 控制台。```truffle``` 控制台类似于 ```Truffle Develop``` ，
期望他连接到已经存在的区块链（由 ```Ganache``` 保证创建区块链）。

	```bash
	$ truffle console
	```

	你将会看到如下输出：

	```text
	truffle(development)>
	```

#### <span id="interactWithContract">7. 与智能合约交互</span>

使用控制台与智能合约交互有以下几种方式：

```text
注意：我们这个例子中使用 web3.eth.accounts[] 中的所有账户由助记词（mnemonic）保证。
所以，demo中出现的地址跟你本地区块链中的地址有所不同是很正常的。通常我们这样使用地址 web3.eth.accounts[0] 。
```

- 查询 ```metacoin``` 发布后智能合约中的账户余额：

```javascript
MetaCoin.deployed().then(function(instance){return instance.getBalance(web3.eth.accounts[0]);}).then(function(value){return value.toNumber()});
```

- 查询账户余额值多少ETH

```javascript
MetaCoin.deployed().then(function(instance){return instance.getBalanceInEth(web3.eth.accounts[0]);}).then(function(value){return value.toNumber()});
```

- 转 metacoin 从一个账户转账到另外一个账户

```javascript
MetaCoin.deployed().then(function(instance){return instance.sendCoin(web3.eth.accounts[1], 500);});
```

- 查询接收 metacoin 的账户的余额：

```javascript
MetaCoin.deployed().then(function(instance){return instance.getBalance(web3.eth.accounts[1]);}).then(function(value){return value.toNumber()});
```

- 查询转出 metacoin 账户的余额：

```javascript
MetaCoin.deployed().then(function(instance){return instance.getBalance(web3.eth.accounts[0]);}).then(function(value){return value.toNumber()});
```

## 继续学习

这个快速教程主要展示了 truffle 的基础教程，但是任然有很多要学习。请继续学习我们剩下的教程，尤其是 [教程][7]

[1]: https://github.com/ethereum/EIPs/issues/190
[2]: https://nodejs.org/en/
[3]: https://www.sublimetext.com/3
[4]: https://code.visualstudio.com/
[5]: https://download.csdn.net/download/tgbus18990140382/10507133
[6]: todo
[7]: https://truffleframework.com/tutorials/
[8]: https://truffleframework.com/ganache