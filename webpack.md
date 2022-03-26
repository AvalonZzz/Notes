## 安装webpack

```bash
// webpack主包和webpack命令行工具
npm i webpack webpack-cli -D
```

查看webpack打包细节

```bash
webpack --status detailed
```

## 自定义webpack

自定义的webpack配置存放在`webpack.config.js`中

```js
const path = require('path')
module.exports = {
    entry: './src/index.js', // 打包入口文件
    output: { // 打包输出信息
        filename: 'bundle.js', // 打包输出文件名
        path: path.resolve(__dirname, './dist') // 打包输出目录，绝对路径
        clean: true, // 打包时自动清理上次打包的文件
    },
    mode: '', // 打包输出模式，分为production和development
}
```

## 使用HTMLWebpackPlugin

首先安装插件

```bash
npm i html-webpack-plugin -D
```

然后调整`webpack.config.js`

```js
module.exports = {
	// ...
	plugins: [
		new HtmlWebpackPlugin({
          template: "./index.html", // 打包的html模板入口路径
          filename: "app.html", // 打包的输出的html模板名字
          inject: "body", // 打包的html模板插入的script标签位置
        }),
	]
}
```

## sourceMap

```js
module.exports = {
    // ...
    devtool: 'inline-source-map', // 调试的时候精准定位错误的行数
}
```

## webpack-dev-server

首先安装`webpack-dev-server`

```bash
npm i webpack-dev-server -D
npx webpack-dev-server // 启动webpack-dev-server
```

配置`webpack-dev-server`

```js
devServer: {
	static: './dist' // 告诉webpack-dev-server，将dist目录作为web服务的根目录
}
```

## 资源模块打包

### resource资源打包

resource资源打包会存储一个单独的资源文件并引入一个url

*generator里定义的打包路径高于output里定义的*

```js
module.exports = {
	// ...
	output: {
		// ...
		assetModuleFilename: 'images/[contenthash][ext]' // 指定静态资源的打包路径
	},
	module: {
        rules: [
			{
				test: /\.png$/, // 文件后缀名为png
				type: 'asset/resource'， // type为asset/resource的文件
                generator: {
                	filename: 'images/[contenthash][ext]'  // 指定静态资源的打包路径
            	}
			}
        ]
	}
}
```

### inline资源

inline资源会被打包dataURI内立案到bundle中

```js
module.exports = {
	// ...
	module: {
		rules: [
			{
				test: /\.jpg/,
				type: 'asset/inline', // type为inline会被打包为base64字符串
			}
		]
	}
}
```

### source资源

source导出资源的源代码

```js
module.exports = {
	module: {
		rules: [
			{
				test: /\.txt/,
				type: 'asset/source'
			}
		]
	}
}
```

### 通用资源类型

asset资源类型在导出dataURI(inline)和导出单独文件(resource)之间自动选择，当资源文件大于临界值时导出单独文件

```js
module.exports = {
	// ...
	module: {
		rules: [
			{
				test: /\.(png|jpg|gif)$/,
				type: 'asset',
                parser: {
                    dataUrlCondition: {
                        maxSize: 4*1024*1024 // 定义临界值
                    }
                }
			}
		]
	}
}
```

## loader

### 加载css模块

首先安装`style-loader`和`css-loader`

```bash
npm i style-loader css-loader -D
```

```js
module.exports = {
	// ...
	module: {
		rules: [
			{
                test: /\.css$/,
                use: ["style-loader", "css-loader"], // 顺序是从后往前执行，所以需要先用css-loader处理导入的css文件内容，然后用style-loader将处理的内容插入创建的style标签中
            },
		]
	}
}
```

### 抽离和压缩css

安装`mini-css-extract-plugin`，这个插件可以抽离css，这个插件基于webpack5

```bash
npm i mini-css-extract-plugin -D
```

```js
module.exports = {
	// ...
	plugins: [
		new MiniCssExtractPlugin({
          filename: "styles/[contenthash].css", // 指定打包的目录
        }),
	],
	module: {
		rules: [
			{
				test: /\.css$/,
        		use: [MiniCssExtractPlugin.loader, "css-loader"],
			}
		]
	}
}
```

安装`css-minimizer-webpack-plugin`，这个插件可以压缩css

```bash
npm i css-miniizer-webpack-plugin -D
```

```js
module.exports = {
	// ...
	mode: 'production',
	optimization: {
    	minimizer: [new CssMinimizerPlugin()],
    },
}
```

### 加载font字体

```js
module.exports = {
	// ...
	module: {
		rules: [
			 {
                 test: /\.(woff|woff2|eot|ttf|otf)$/,
                 type: "asset/resource",
             },
		]
	}
}
```

```css
@font-face {
  font-family: "fira";
  src: url("./assets/FiraCode-Regular.ttf") format("truetype");
}
.body {
  color: green;
  font-family: "fira";
  font-size: 30px;
}
```

