truffle （ETH以太坊智能合约开发集成工具） 入门教程
===============================================

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
[6. 与智能合约交互](#interactWithContract)</br>

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

```
注意：

使用 truffle unbox <box-name> 下载任意 truffle boxes。

使用 truffle init 初始化一个不包含智能合约的项目。
```

#### <span id="exploringProject">2. 浏览项目</span>

#### <span id="testing">3. 测试</span>

#### <span id="compiling">4. 编译</span>

#### <span id="migrateToTruffle">5. 迁移到truffle开发环境</span>

#### <span id="interactWithContract">6. 与智能合约交互</span>

[1]: https://github.com/ethereum/EIPs/issues/190
[2]: https://nodejs.org/en/
[3]: https://www.sublimetext.com/3
[4]: https://code.visualstudio.com/