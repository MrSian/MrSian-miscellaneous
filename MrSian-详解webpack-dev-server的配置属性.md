**1.devServer.contentBase**

它指定了服务器资源的根目录，如果不写入 contentBase 的值，那么 contentBase 默认是项目的目录。

在上面例子中产生错误和后来解决错误的原因：

产生错误：因为 bundle.js 被**"放在了"**我们的项目根目录里，在 dist/html 里\<script src="./bundle.js">\</script>此时显然不能根据路径找到 bundle.js

解决错误：通过配置 contentBase: path.join\(\_\_dirname, "dist"\)将 bundle.js**"放在了"**dist 目录下，此时 bundle.js 和 dist/index.html 位于同一目录下，通过 src="./bundle.js"自然就找到 bundle.js 了

**webpack 打包和 webpack-dev-server 开启服务的区别——**

webpack 输出真实的文件，而 webpack-dev-server 输出的文件只存在于内存中,不输出真实的文件！（注意下面两张图的区别）

webpack:当我们在终端运行"webpack"后：

![](https://images2015.cnblogs.com/blog/1060770/201706/1060770-20170604190233493-941277650.png)

webpack-dev-server:当我们在终端运行"node_modules/.bin/webpack-dev-server 后：

![](https://images2015.cnblogs.com/blog/1060770/201706/1060770-20170604190253446-2006223050.png)

这也就是我在上面的阐述中将 bundle.js"放在了"加上双引号的原因

你要提供哪里的内容给虚拟服务器用。这里最好填 绝对路径。 // 单目录
contentBase: path.join\(\_\_dirname, "public"\) // 多目录
contentBase: \[path.join\(\_\_dirname, "public"\), path.join\(\_\_dirname, "assets"\)\]
默认情况下，它将使用您当前的工作目录来提供内容。

**2.devServer.port**

port 配置属性指定了开启服务的端口号：

devServer: \{
port:7000
\}

设置端口号为 7000:

运行：node_modules/.bin/webpack-dev-server

![](https://images2015.cnblogs.com/blog/1060770/201706/1060770-20170604190350868-619045248.png)

这个时候就不是默认的 8080 的端口了，而是我们设置的 7000

**3.devServer.host**

host 设置的是服务器的主机号：

修改配置为：

devServer: \{
contentBase: path.join\(\_\_dirname, "dist"\),
port:7000,
host:'0.0.0.0'
\}

此时 localhost:7000 和 0.0.0.0:7000 都能访问成功

![](https://images2015.cnblogs.com/blog/1060770/201706/1060770-20170604190408008-390953000.png)

**4.devServer.historyApiFallback**

在文档里面说的很清楚，**这个配置属性是用来应对返回 404 页面时定向到特定页面用的**（the index.html page will likely have to be served in place of any 404 responses\)

在 dist 目录下新增一个 HTML 页面：

/\*剩下的都是很常规的 HTML 内容，故省略\*/
\<p>这里是 404 界面\</p>

我们把 webpack.config.js 修改如下：

[![ ](https://common.cnblogs.com/images/copycode.gif)](null " ")

module.exports = \{
/\*这里省略 entry 和 output，参照上面写的内容\*/
devServer: \{
contentBase: path.join\(\_\_dirname, "dist"\),
historyApiFallback:\{
rewrites:\[
\{from:/./,to:'/404.html'\}
\]
\}
\}
\}

[![ ](https://common.cnblogs.com/images/copycode.gif)](null " ")

打开页面，输入一个不存在的路由地址：

![](https://images2015.cnblogs.com/blog/1060770/201706/1060770-20170604190432149-898528771.png)

如果为 true ，页面出错不会弹出 404 页面。
如果为 \{...\} , 看看一般里面有什么。

rewrites
rewrites: \[
\{ from: /\^\\/subpage/, to: '/views/subpage.html' \},
\{ from: /\^\\/helloWorld\\/.\*\$/,
to: function\(\) \{ return '/views/hello_world.html;
\}
\}
\] // 从代码可以看出 url 匹配正则，匹配成功就到某个页面。 // 并不建议将路由写在这，一般 historyApiFallback: true 就行了。
verbose：如果 true ，则激活日志记录。
disableDotRule： 禁止 url 带小数点 . 。

5.watchOptions （[文档](https://webpack.js.org/configuration/dev-server/#devserver-watchoptions-)）

- 一组自定义的监听模式，用来监听文件是否被改动过。

```css
watchOptions: {
  aggregateTimeout: 300,
  poll: 1000，
  ignored: /node_modules/
}
```

1.  **aggregateTimeout**：一旦第一个文件改变，在重建之前添加一个延迟。填以毫秒为单位的数字。
2.  **ignored**：观察许多文件系统会导致大量的 CPU 或内存使用量。可以排除一个巨大的文件夹。
3.  **poll**：填以毫秒为单位的数字。每隔（你设定的）多少时间查一下有没有文件改动过。不想启用也可以填`false`。

### 6.proxy \([文档](https://webpack.js.org/configuration/dev-server/#devserver-proxy)\)

- 当您有一个单独的 API 后端开发服务器，并且想要在同一个域上发送 API 请求时，则代理这些 _url_ 。看例子好理解。

```
  proxy: {
    '/proxy': {
        target: 'http://your_api_server.com',
        changeOrigin: true,
        pathRewrite: {
            '^/proxy': ''
        }
  }
```

1.  假设你主机名为 _localhost:8080_ , 请求 _API_ 的 _url_ 是 _http：//your_api_server.com/user/list_
2.  **_'/proxy'_**：如果点击某个按钮，触发请求 _API_ 事件，这时请求 _url_ 是`http：//localhost:8080**/proxy**/user/list`。
3.  **_changeOrigin_**：如果 _true_ ，那么 `http：//localhost:8080/proxy/user/list 变为 http：//your_api_server.com/proxy/user/list` 。但还不是我们要的 _url_ 。
4.  **_pathRewrite_**：重写路径。匹配 _/proxy_ ，然后变为`''` ，那么 _url_ 最终为 `http：//your_api_server.com/user/list` 。

### 7.publicPath （[文档](https://webpack.js.org/configuration/dev-server/#devserver-publicpath-)）

- 配置了 *publicPath*后， `_url_ = '主机名' + '_publicPath_配置的' + '原来的_url.path_'`。这个其实与 _output.publicPath_ 用法大同小异。
- _output.publicPath_ 是作用于 _js, css, img_ 。而 _devServer.publicPath_ 则作用于请求路径上的。

```
// devServer.publicPath
publicPath: "/assets/"

// 原本路径 --> 变换后的路径
http://localhost:8080/app.js --> http://localhost:8080/assets/app.js
```

``

**8.devServer.overlay**

- 如果为 _true_ ，在浏览器上全屏显示编译的 errors 或 warnings。默认 _false_ （关闭）

- 如果你只想看 _error_ ，不想看 _warning_。

overlay：\{
errors：true，
warnings：false \}

配置属性用来在编译出错的时候，在浏览器页面上显示错误，默认是 false，可设置为 true

首先我们人为制造一个编译错误：在我们尚未配置 babel loader 的项目里使用 ES6 写法：

在 src/index.js 里写入“const a”

在 shell 里提示编译错误：

![](https://images2015.cnblogs.com/blog/1060770/201706/1060770-20170604190527743-1443101700.png)

但在浏览器里没有提示：

![](https://images2015.cnblogs.com/blog/1060770/201706/1060770-20170604190559524-1858831788.png)

所以我们把 webpack.config.js 修改为：

[![ ](https://common.cnblogs.com/images/copycode.gif)](null " ")

module.exports = \{
/\*这里省略 entry 和 output，参照上面写的内容\*/
devServer: \{
contentBase: path.join\(\_\_dirname, "dist"\),
overlay: true
\}
\}

[![ ](https://common.cnblogs.com/images/copycode.gif)](null " ")

再重新运行 node_modules/.bin/webpack-dev-server，浏览器上把错误显示出来了

![](https://images2015.cnblogs.com/blog/1060770/201706/1060770-20170604190640868-1082983933.png)

**9.devServer.stats（字符串）**

这个配置属性用来控制编译的时候 shell 上的输出内容，我们没有设置 devServer.stats 时候编译输出是这样子的：

（其中看起来有许多看似不重要的文件也被打印出来了）

![](https://images2015.cnblogs.com/blog/1060770/201706/1060770-20170604190701196-363692050.png)

**stats: "errors-only"表示只打印错误：**

我们把配置改成：

devServer: \{
contentBase: path.join\(\_\_dirname, "dist"\),
stats: "errors-only"
\}

因为只有错误才被打印，所以，大多数信息都略过了

![](https://images2015.cnblogs.com/blog/1060770/201706/1060770-20170604190755758-1042074512.png)

除此之外还有"minimal"，"normal"，"verbose"，这里不多加赘述

**10.devServer.quiet**

**_true_，则终端输出的只有初始启动信息。 _webpack_ 的警告和错误是不输出到终端的**

当这个配置属性和 devServer.stats 属于同一类型的配置属性

当它被设置为 true 的时候，控制台只输出第一次编译的信息，**当你保存后再次编译的时候不会输出任何内容，包括错误和警告**

来做个对比吧：

**quiet：false（默认）：**

第一次编译：

![](https://images2015.cnblogs.com/blog/1060770/201706/1060770-20170604190903883-1418376528.png)

第二次编译（当你保存的时候）

![](https://images2015.cnblogs.com/blog/1060770/201706/1060770-20170604190912461-1780150621.png)

**quiet:true**

第一次编译同上

第二次编译什么都不输出

**11.devServer.compress**

这是一个布尔型的值，当它被设置为 true 的时候对所有的服务器资源采用 gzip 压缩

采用 gzip 压缩的优点和缺点：

优点：对 JS，CSS 资源的压缩率很高，可以极大得提高文件传输的速率，从而提升 web 性能

缺点：服务端要对文件进行压缩，而客户端要进行解压，增加了两边的负载

**12.devServer.hot 和 devServer.inline**

在这之前，首先要说一下的是 webpack-dev-server 的自动刷新和模块热替换机制

webpack-dev-server 的自动刷新和模块热替换机制 ,这两个机制是紧紧联系在一起的

**从外部角度看——自动刷新**

当我们对业务代码做了一些修改然后保存后（command+s），页面会自动刷新，我们所做的修改会直接同步到页面上，而不需要我们刷新页面，或重新开启服务

（The webpack-dev-server supports multiple modes to automatically refresh the page）

**从内部角度看——模块热替换**

在热替换（HMR）机制里，不是重载整个页面，HMR 程序会只加载被更新的那一部分模块，然后将其注入到运行中的 APP 中

（In Hot Module Replacement, the bundle is notified that a change happened. Rather than a full page reload, a Hot Module Replacement runtime could then load the updated modules and inject them into a running app.）

**webpack-dev-server 有两种模式可以实现自动刷新和模块热替换机制**

1\. Iframe mode**\(默认,无需配置\)**

页面被嵌入在一个 iframe 里面，并且在模块变化的时候重载页面

2.inline mode（需配置）添加到 bundle.js 中

当刷新页面的时候，一个小型的客户端被添加到 webpack.config.js 的入口文件中

例如在我们的例子中，在使用 inline mode 的热替换后，相当于入口文件从

entry:\{
app:path.join\(\_\_dirname,'src','index.js'\)
\}

变成了：

entry:\{
app:\[path.join\(\_\_dirname,'src','index.js'\),
'webpack-dev-server/client\?http://localhost:8080/'
\]
\}

从一个入口变成了两个入口，并实现刷新

**那怎么才能 inline mode 模式的刷新呢？**

你需要做这些：

1 在配置中写入 devServer.hot：true 和 devServer.inline：true

2 增加一个插件配置 webpack.HotModuleReplacementPlugin\(\)

例如：

[![ ](https://common.cnblogs.com/images/copycode.gif)](null " ")

var webpack = require\('webpack'\)
module.exports = \{
/\*省略 entry ,output 等内容\*/
plugins:\[
new webpack.HotModuleReplacementPlugin\(\)
\],
devServer: \{
inline:true,
hot:true
\}
\}

[![ ](https://common.cnblogs.com/images/copycode.gif)](null " ")

打开页面：

![](https://images2015.cnblogs.com/blog/1060770/201706/1060770-20170604190932618-464188592.png)

如果有上面两行输出则表明你已经配置成功

转载于:https://www.cnblogs.com/jkr666666/p/11067270.html
