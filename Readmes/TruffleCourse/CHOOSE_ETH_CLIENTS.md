选择以太坊客户端
===============

有很多以太坊客户端供我们选择。我们推荐在开发和部署时使用不同的客户端。

## 当开发时

### GANACHE

我们推荐 ```Ganache``` ，它是一个运行在你个人电脑上的私有连客户端。它是 truffle 套种中的一部分，
```Ganache``` 将智能合约和交易放在前台并且中心化，从而简化了dapp的开发。使用 ```Ganache``` 你可以
快速查看你们的应用是如何影响区块链的，并且对账户，余额，智能合约创建以及燃料消费进行自省。

```Ganache``` 运行在 **http://127.0.0.1:7545** 。默认会创建是个账户，重启后账户依然不会变，
当然也可以手动随机账户，你也可以用你自己的账户。

```text
警告：不要再以太坊主网络中使用自动创建账户。
```

### TRUFFLE DEVELOP

我们同样也推荐使用 ```truffle develop``` ，它是 ```truffle``` 内置的开发链工具。不需要任何的额外安装，
你要使用它只需要一条命令行即可：

```bash
$ truffle develop
```

```Truffle Develop``` 运行在 **http://127.0.0.1:9545** 上。

```text
警告：同样不要在以太坊主网络中使用自己生成的地址，这些地址是你的本地开发私有地址，
一旦你在以太坊中向这些私有地址转账，那么这些eth就永远丢失了。
```

### Ganache CLI

当你的开发机没有图形界面时就无法直接使用 ```Ganache``` ，而 ```Ganache CLI``` 就提供了没有图形界面系统的能力。

## 发布到线上网络

有很多官方和非官网的以太坊客户端你可以选择。以下是部分:

- geth https://github.com/ethereum/go-ethereum
- WebThree https://github.com/ethereum/cpp-ethereum
- Parity https://github.com/paritytech/parity
- More https://www.ethereum.org/cli