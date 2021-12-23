> Node.js 是一个基于 Chrome V8 引擎的 JavaScript 运行时。

- 单线程
- 非阻塞的I/O

## 必要的一些知识

### 导入导出

`module.exports` 和 `export` 之间有什么区别？

前者公开了它指向的对象。 后者公开了它指向的对象的属性。 

common.js

### 软件包版本

鉴于使用了 semver（语义版本控制），所有的版本都有 3 个数字，第一个是主版本，第二个是次版本，第三个是补丁版本

- 如果写入的是 `〜0.13.0`，则只更新补丁版本：即 `0.13.1` 可以，但 `0.14.0` 不可以。
- 如果写入的是 `^0.13.0`，则要更新补丁版本和次版本：即 `0.13.1`、`0.14.0`、依此类推。
- 如果写入的是 `0.13.0`，则始终使用确切的版本。

当发布新的版本时，不仅仅是随心所欲地增加数字，还要遵循以下规则

- 当进行不兼容的 API 更改时，则升级主版本。
- 当以向后兼容的方式添加功能时，则升级次版本。
- 当进行向后兼容的缺陷修复时，则升级补丁版本。

规则符号的含义

- `^`: 只会执行不更改最左边非零数字的更新。 如果写入的是 `^0.13.0`，则当运行 `npm update` 时，可以更新到 `0.13.1`、`0.13.2` 等，但不能更新到 `0.14.0` 或更高版本。 如果写入的是 `^1.13.0`，则当运行 `npm update` 时，可以更新到 `1.13.1`、`1.14.0` 等，但不能更新到 `2.0.0` 或更高版本。
- `~`: 如果写入的是 `〜0.13.0`，则当运行 `npm update` 时，会更新到补丁版本：即 `0.13.1` 可以，但 `0.14.0` 不可以。
- `>`: 接受高于指定版本的任何版本。
- `>=`: 接受等于或高于指定版本的任何版本。
- `<=`: 接受等于或低于指定版本的任何版本。
- `<`: 接受低于指定版本的任何版本。
- `=`: 接受确切的版本。
- `-`: 接受一定范围的版本。例如：`2.1.0 - 2.6.2`。
- `||`: 组合集合。例如 `< 2.1 || > 2.6`。

### npm 命令

- npm list 所有已安装的 npm 软件包（包括它们的依赖包）的最新版本
  - npm list --depth=0  列出几层的包
  - npm list -g 查看全局安装的软件包的最新版本
    - npm list -g --depth 0  
  - npm list xxx 通过指定名称来获取特定软件包的版本
  - npm view [package_name] version  软件包在 npm 仓库上最新的可用版本
- npm root -g  查看全局安装的路径

- npm install <package>@<version>  npm 软件包的旧版本
- npm view <package> versions   列出软件包所有的以前的版本
- npm outdated  仓库中一些过时的软件包的列表
- npm uninstall <package-name>

### npx

- 不需要安装任何东西。 无需全局安装依赖了

  - `npx @vue/cli create my-vue-app`
  - `npx create-react-app my-react-app`

- 可以使用 @version 语法运行同一命令的不同版本。

- 使用不同node版本运行代码

  ```bash
  npx node@10 -v #v10.18.1
  npx node@12 -v #v12.14.1
  ```

- 直接从 URL 运行任意代码片段
  - npx https://gist.github.com/zkat/4bc19503fe9e9309e2bfaa2c58074d32

## 事件循环

NodeJs是基于事件事件循环来实现非阻塞的IO的，在编写的时候我们需要尽量避免编写阻塞事件循环的代码.事件循环会去不断检查调用堆栈的函数，并按照顺序去执行每一个函数

### 一个简单的事件循环的阐释

**在不引入异步操作，一个正常的事件循环对栈进行的操作**

```js
const bar = () => console.log('bar')


const baz = () => console.log('baz')


const foo = () => {
  console.log('foo')
  bar()
  baz()
}


foo()
```

他的调用堆栈如下所示

![调用堆栈的第一个示例](http://www.maymfx.cn/pic/call-stack-first-example.png)



每次迭代中的事件循环都会查看调用堆栈中是否有东西并执行它直到调用堆栈为空：

![执行顺序的第一个示例](http://www.maymfx.cn/pic/execution-order-first-example.png)

**在引入异步操作之后，事件队列的操作，会将异步操作的回调函数放到消息队列去执行**

```js
const bar = () => console.log('bar')


const baz = () => console.log('baz')


const foo = () => {
  console.log('foo')
  setTimeout(bar, 0)
  baz()
}


foo()
```

调用栈如下

![调用堆栈的第二个示例](http://www.maymfx.cn/pic/call-stack-second-example.png)

事件循环执行的情况如下

![](http://www.maymfx.cn/pic/execution-order-second-example-20211210145555172.png)

在执行当调用 setTimeout() 时，浏览器或 Node.js 会启动定时器。 当定时器到期时（在此示例中会立即到期，因为将超时值设为 0），则回调函数会被放入“**消息队列**”中。

在消息队列中，用户触发的事件（如单击或键盘事件、或获取响应）也会在此排队，然后代码才有机会对其作出反应。 类似 `onLoad` 这样的 DOM 事件也如此。

事件循环会赋予调用堆栈优先级，它首先处理在调用堆栈中找到的所有东西，一旦其中没有任何东西，便开始处理消息队列中的东西。

我们不必等待诸如 `setTimeout`、fetch、或其他的函数来完成它们自身的工作，因为它们是由浏览器提供的，并且位于它们自身的线程中。 例如，如果将 `setTimeout` 的超时设置为 2 秒，但不必等待 2 秒，等待发生在其他地方

**es6引入了作业队列**

ECMAScript 2015 引入了作业队列的概念，Promise 使用了该队列（也在 ES6/ES2015 中引入）。 这种方式会尽快地执行异步函数的结果，而不是放在调用堆栈的末尾。在当前函数结束之前 resolve 的 Promise 会在当前函数之后被立即执行。

有个游乐园中过山车的比喻很好：消息队列将你排在队列的后面（在所有其他人的后面），你不得不等待你的回合，而工作队列则是快速通道票，这样你就可以在完成上一次乘车后立即乘坐另一趟车。

```js
const bar = () => console.log('bar')


const baz = () => console.log('baz')


const foo = () => {
  console.log('foo')
  setTimeout(bar, 0)
  new Promise((resolve, reject) =>
    resolve('应该在 baz 之后、bar 之前')
  ).then(resolve => console.log(resolve))
  baz()
}

foo()

//foo
//baz
//应该在 baz 之后、bar 之前
//bar
```

- process.nextTick()

每当事件循环进行一次完整的行程时，我们都将其称为一个滴答。当将一个函数传给 `process.nextTick()` 时，则指示引擎在当前操作结束（在下一个事件循环滴答开始之前）时调用此函数

- setImmediate()
- promise
- async await























