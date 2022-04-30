# 前端技术栈扫盲

# 东西们

| 名称        | 分工    |  备注  |
| :---------:   | :-----:   | :----: |
| nodejs | js服务端运行环境 | 目前是v10 |
| nvm | node.js版本管理器|  |
| npm | js包管理器 | package.json/package-lock.json/node_modules |
| npm脚本 | | 用来定义一些快捷命令，在package.json中定义，如npm run .. |
| webpack | 静态模块打包器 | 把所有的js、图片、css等内容打包发布，供浏览器请求；但是webpack只解析import和export，所以需要babel等插件 |
| babel | js语法转换器 | 把js的最新语法转换为浏览器支持的语法 |

# 协议们
| 协议 | 导出 | 导入 | 应用场景 |  备注  |
| :-: | :-: | :-: | :-: | :-: |
|CommonJS|module.exports|require|nodejs(服务端)|运行时加载/传递值拷贝|
|AMD|module.exports|rquire|浏览器|运行时加载/传递值拷贝|
|ES6|export|import|服务端/浏览器|传递值引用|


# webpack
webpack 配置是标准的 Node.js CommonJS 模块
## 入口 entry
```
entry: './path/to/my/entry/file.js'
```
## 输出 output
```
output: {
	path: path.resolve(__dirname, 'dist'),
	filename: 'my-first-webpack.bundle.js'
}
```
## loader
* webpack只处理js文件，所以其他类型文件需要对应的loader处理；
* 关键因素有：test(正则匹配对应的实验类型) use(使用哪个loader) 

```
module: {
	rules: [
	  { test: /\.txt$/, use: 'raw-loader' }
	]
}
```
## 插件(plugins)
* 更大范围的特殊处理
* 需要require


```
const HtmlWebpackPlugin = require('html-webpack-plugin'); // 通过 npm 安装
plugins: [
	new HtmlWebpackPlugin({template: './src/index.html'})
]
```
## 模式
```
mode: 'production'
```

# npm

## 常用命令
* npm install moduleName@version
* npm uninstall moduleName
* npm search moduleName
* npm list # 查看当前项目已安装的模块
* npm update moduleName

## 常用参数
* -S, --save
* -D, --save-dev
* -g

## 配置项
* dependencies
* devDependencies
