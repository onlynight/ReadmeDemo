使用 Truffle Develop 和 console
==============================

使用测试或者调试器与合约进行交互非常好，或者你也可以手动执行交易。 Truffle 提供了两种简单的方式与合约交互。

- **Truffle console: ** 一个可以和任何以太坊客户端交互的交互式控制台。
- **Truffle Develop: ** 一个提供了本地私有开发链的交互式控制台。

## 为什么有两个不同的控制台

提供两个控制台是为了让你选择最合适的控制台。

选择 ```truffle console``` 的理由：

- 你有一个你正在使用的以太坊客户都，例如 Ganache 或者 geth
- 你想迁移到测试网络
- 你想使用特殊的著几次或正虎列表

选择 ```truffle develop``` 的理由：

- 你想在不马上发布的状况下测试智能合约
- 你不需要特殊的账户
- 你不想管理不同的以太坊客户端

## 命令

所有的命令行都需要在你的工程目录下执行。

### CONSOLE

启动 console：

```bash
$ truffle console
```

执行这个命令的时候回去读取 ```truffle.js``` 中的 ```development``` 网络配置信息，
你需要配置 ```development``` 否则无法连接到客户端。你也可以在命令行中执行网络配置，
使用 ```--network <name>``` 选项。更多进阶的网络配置，请查看网络配置板块中的内容。

启动 ```truffle console``` 后，你会立即看到如下提示：

```bash
truffle(console)>
```

这就表示 ```truffle console``` 已经连接到 ```development``` 网络中了。

## TRUFFLE DEVELOP

启动 ```truffle develop```:

```bash
$ truffle develop
```

这个命令执行后无论你的 ```truffle.js``` 配置什么网络，都会在 ```9545``` 端口创建一个私有链。

当你启动 ```truffle develop``` 后将会看到如下输出：

```text
Truffle Develop started at http://localhost:9545/

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
```

This shows you the addresses, private keys, and mnemonic for this particular blockchain.
