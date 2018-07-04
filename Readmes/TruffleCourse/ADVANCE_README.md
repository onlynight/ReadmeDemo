truffle 进阶
============

## 配置

### 配置文件位置

你的配置文件在项目根目录下 ```truffle.js``` 。这是一个JavaScript文件，它包含了执行你编写的代码的必要配置。
它必须 export 一个代码你项目配置的对象，例子如下：

```javascript
module.exports = {
	networks: {
		development: {
			host: "127.0.0.1",
			port: 8545,
			network_id: "*", //匹配所有以太坊网络，开发、测试、发布。
		}
	}
}
```

上面这个配置就是配置当前的网络运行环境主机名和端口号。 ```127.0.0.1:8545```

### 在Windows环境下解决命名冲突问题

在windows中使用命令行时，默认的配置文件名称会导致truffle命令无法执行。

愿意在于 ```truffle.cmd``` ```truffle.js``` 在 windows 中都是可执行脚本，你在当前目录下执行 ```truffle``` 也就是执行了 ```truffle.js```。
这不是我们想要的结果，不知道 truffle 为什么没有通过修改默认配置文件名来解决这个问题，下面说几个可以解决的方案：

- 在调用	```truffle``` 命令时加上后缀 ```truffle.cmd``` 。
- 修改系统环境变量，从可执行文件列表中删除 ```.JS;``` 。
- 重命名 ```truffle.js``` ， 例如 ```truffle-config.js``` 。
- 使用 ```Windows PowerShell``` 或者 ```Git BASH``` ，就不会有这个冲突问题。
