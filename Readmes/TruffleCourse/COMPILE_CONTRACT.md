编译智能合约
===========

## 源文件位置

你的所有智能合约应该在你的工程目录下的 ```contracts``` 目录中。所有的合约都是由 ```solidity``` 编写，并且所有文件的后缀都是 ```.sol``` 。

在一个空的 ```truffle``` 项目中，只有一个 ```Migrations.sol``` 用于帮助部署进程。如果你在使用 ```truffle box``` ，那么就会有多个文件。

## 命令

在你的工程目录下执行编译命令：

```bash
$ truffle compile
```

第一次编译时会编译所有合约，以后再调用编译命令将会只编译修改的合约，你可以使用 ```--all``` 来改变编译行为。

## 构建 ```artifacts``` 

编译的实现文件都会在 ```build/contacts/``` 目录下，不要编辑这些编译生成的文件。

## 依赖

在 ```solidity``` 中使用 ```import``` 命令添加依赖。编译器会保证正确的以来顺序。以来可以有以下两种方式：

### 通过文件名添加依赖

使用 ```import``` 关键词导入其他文件：

```solidity
import "./AnotherContract.sol"
```

这个命令将会使得所有包含 ```AnotherContract.sol``` 的合约可用。这里 ```AnotherContract.sol``` 在当前合约的同一级目录下。

### 从外部包添加合约依赖

truffle 支持通过 EthPM 和 NPM 安装的依赖。使用以下语法导入依赖：

```solidity
import "somepackage/SomeContact.sol";
```

Truffle 的搜索优先级是 EthPM > NPM ，所以当两个包管理器有相同的包名时会优先使用 EthPM 中的依赖。
