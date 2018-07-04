使用 EthPM 包管理工具
====================

[EthPM][4] 是 Ethereum 提供的软件包注册表。它遵循[ERC190][1]规范来发布和使用智能合约包，
并获得了许多不同的Ethereum开发工具的广泛支持。为了表示我们的支持，我们将 Ethereum 
公司的软件包注册表直接集成到 Truffle 中。

## 安装包

使用 EthPM 安装包和 NPM 类似。运行如下命令即可：

```bash
$ truffle install <package name>
```

你也可以通过指定版本号来安装：

```bash
$ truffle install <package name>@<version>
```

与NPM一样，EthPM版本遵循[semver][2]。您可以在Ethereum[包注册表][3]中找到所有可用包的列表。

## 安装依赖

你的项目定义一个 ```ethpm.json``` 文件，用来配置项目的依赖的包以及版本号（类似与npm的package.json）。
安装所有的依赖，运行如下命令行：

```bash
$ truffle install
```

更多 ```ethpm.json``` 请查看下面板块的[包配置][5]。

## 使用已经安装的合约

已经安装的合约都在工程目录下的 ```installed_contracts``` 目录中。如果文件夹不存在，将会自动创建这个文件夹。
把这个文件夹看作跟 node.js 中的 ```node_modules``` 文件夹一样，不要修改里面的东西。

安装好的包何以在测试、迁移以及智能合约中使用( import or require )。例如，```solidity```编写的智能合约，想要
从 ```owned``` 包中 import ```owned.sol``` ：

```solidity
pragma solidity ^0.4.2;

import "owned/woned.sol";

contract MyContract is owned {
	// ...
}
```

类似的，迁移脚本中想要使用 ```ens``` 包中的 ```ens.sol``` ：

文件： ```./migrations/2_deploy_contracts.js```

```javascript
var ENS = artifacts.require("ens/ENS");
var MyContract = artificats.require("MyContract");

module.exports = function(deployer){

	deployer.deploy({overite: false}).then(function(){
		return deployer.deploy(MyContract, ENS.address);
	});
};
```

注意在上面的迁移文件中，我们使用ens包，并基于ens是否已经有一个地址集有条件地部署ens合约。
这是 deployer 提供给您的一种巧妙的技巧，它使根据网络工件的存在编写迁移变得更加容易。
在这种情况下，如果我们在Ropsten网络上运行我们的迁移，这个迁移不会部署ENS合约，
因为(在撰写本文时)Ropsten是规范的合约存在的地方——我们不希望部署我们自己的合约。
但是如果我们对不同的网络或者测试网络运行迁移，那么我们需要部署ENS契约，这样我们就有了一个依赖合约。

## 发布你自己的包

发布你自己的包和安装一样简单，但是像NPM一样，需要更多的配置。

### Ropsten, Ropsten, Ropsten

Ethereum包注册表目前存在于Ropsten测试网络上。要发布到注册中心，我们需要设置自己的Ropsten配置，
因为我们将进行需要签名的交易。

在本例中，我们将使用 Infura 发布包，以及 truffle-hdwallet-provider NPM模块和一个12字的hd-wallet助记符，
该助记符表示我们在Ropsten网络上的 Ethereum 地址。首先，通过NPM在项目目录中安装truffle-hdwallet-provider:

```bash
$ npm install truffle-hdwallet-provider --save
```

然后编辑你的 ```truffle.js``` 配置文件，使用十二个字符的地址 添加 ```ropsten``` 网络：

文件 ```truffle.js``` 

```javascript
var HDWalletProvider = require("truffle-hdwallet-provider");

var mnemonic = "opinion destroy betray ...";

module.exports = {

	networks: {
		development: {
			host: "127.0.0.1",
			port: "8545",
			network_id: "*"
		},
		ropsten: {
			provier: new HDWAlletProvider(mnemonic, "https://ropsten.infura.io/");
			network_id: 3 // official id of the ropsten network
		}
	}
}
```

## 包配置

与NPM一样，EthPM的配置选项也包含在一个名为 ethpm.json 的单独JSON文件中。这个文件与Truffle配置放在一起
，它提供了发布软件包所需的所有信息。您可以在[Configuration][6]部分中看到可用选项的完整列表。

文件 ethpm.json

```json
{
	"package_name": "adder",
	"version": "0.0.3",
	"description": "simpe contract to add two numbers",
	"authors": [
		"Tim Coulter <tim.coulter@consensys.net>"
	],
	"keywords": [
		"ethereum",
		"addition"
	],
	"dependencies": {
		"owned": "^0.0.1"
	},
	"license": "MIT"
}
```

## 命令

配置成功以后，发布包：

```bash
$ truffle publish
```

会有如下输出：

```text
$ truffle publish
Gathering contracts...
Finding publishable artifacts...
Uploading sources and publishing to registry...
+ adder@0.0.3
```

## 发布之前

当使用默认开发网络(如配置为匹配任何Ethereum客户端(如Ganache或Truffle development))这样的网络时，
您肯定会有不希望发布的网络工件。在发布包之前，考虑运行以下命令来删除任何无关的网络构件:

```bash
$ truffle networks --clean
```

查看[命令行参考文档][7]，获取更多信息

[1]: https://github.com/ethereum/EIPs/issues/190
[2]: https://semver.org/
[3]: https://www.ethpm.com/registry
[4]: https://www.ethpm.com/
[5]: https://truffleframework.com/docs/getting_started/packages-ethpm#package-configuration
[6]: https://truffleframework.com/docs/advanced/configuration
[7]: https://truffleframework.com/docs/advanced/commands#networks
