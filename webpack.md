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

### babel-loader

打包的时候webpack不会打包es6的代码，有些浏览器仍然不支持es6的代码，所以需要babel-loader去将es6代码打包为es5的代码



首先安装`babel-loader @babel/core @babel/preset-env`

- babel-loader：在webpack里应用babel解析es6的桥梁
- @babel/core：babel核心模块
- @babel/preset-env：babel预设，一组babel插件的集合

```bash
npm i babel-loader @babel/core @babel/preset-env -D
```

配置babel

```js
module: {
	rules: [
		{
			test: /\.js$/,
			exclude: /node_modules/, // 排除编译node_modules中的代码
			use: {
				loader: 'babel-loader',
				options: {
					presets: ['@babel/preset-env']
				}
			}
		}
	]
}
```

上面这个配置`async/await`打包完成之后会用到``regeneratorRuntime运行环境，babel并不自带，所以需要进一步安装这个插件

```bash
npm i @babel/runtime -S // 这个插件中包含了regeneratorRuntime
npm i @babel/plugin-transform-runtime -D // 这个插件会在需要regeneratorRuntime的地方自动导包
```

```js
{
    test: /\.js$/,
    exclude: /node_modules/,
    use: {
        loader: "babel-loader",
        options: {
            presets: ["@babel/preset-env"],
            plugins: [["@babel/plugin-transform-runtime"]],
        },
    },
},
```

## 代码分离方法

### 入口启点分离代码

通过多入口分离代码会将不同入口引用的相同代码打包多份

```js
entry: { // 配置多个入口
  index: "./src/index.js",
  another: "./src/another-module.js",
},
output: {
  filename: "[name].bundle.js", // 根据入口自动匹配出口名字
  path: path.resolve(__dirname, "./dist"),
  clean: true,
  assetModuleFilename: "images/[contenthash][ext]",
},
```

可通过修改配置指定抽离某个公共文件

```js
entry: {
  index: {
    import: "./src/index.js",
    dependOn: "shared", // 当文件中有指定要分享的文件时会去抽离
  },
  another: {
    import: "./src/another-module.js",
    dependOn: "shared",  // 当文件中有指定要分享的文件时会去抽离
  },
  shared: "lodash", // 指定要抽离的某个模块
},
output: {
  filename: "[name].bundle.js",
  path: path.resolve(__dirname, "./dist"),
  clean: true,
  assetModuleFilename: "images/[contenthash][ext]",
},
```

### splitChunksPlugin

自动将多个入口公共的代码抽离出来

```js
entry: {
  index: "./src/index.js",
  another: "./src/another-module.js",
},
output: {
  filename: "[name].bundle.js",
  path: path.resolve(__dirname, "./dist"),
  clean: true,
  assetModuleFilename: "images/[contenthash][ext]",
},
optimization: {
  minimizer: [new CssMinimizerPlugin()],
  splitChunks: {
    chunks: "all",
  },
},
```

### 动态导入

动态导入的代码会自动将里面的模块抽离到公共文件中

```js
// async-module.js
function getComponent() {
  return import("lodash").then(({ default: _ }) => {
    const element = document.createElement("div");
    element.innerHTML = _.join(["hello", "webpack"], "");
    return element;
  });
}

getComponent().then((element) => {
  document.body.appendChild(element);
});


// index.js
import "./async-module";
```

*动态导入和静态导入一起使用需要将webpack中的`splitChunks`配置好*



## 缓存

不同版本的相同文件名的文件浏览器会进行缓存，所以打包的时候要用`[contenthash]`来自动生成文件名

```js
output: {
	filename: '[name].[contenthash].js'
}
```

但是这样第三方的包也会没有缓存，第三方的包是不会修改的，所以要将第三方的包单独打包到一个固定的文件中

```js
optimization: {
	splitChunks: {
        cacheGroups: {
            vendor: {
                test: /[\\/]node_modules[\\/]/, // 将node_modules中的包单独打包
                name: 'vendors', // 单独打包的文件的名字
                chunks: 'all', // 使用范围是所有
            }
        }
	}
}
```

## 公共路径

为导出文件添加公共路径，这样会在引入打包的文件时的路径是一个绝对路径

```js
output: {
	// ...
	publicPath: 'http://localhost:8080'
}
```

## 环境变量

首先将`webpack.config.js`的导出对象改为一个函数

```js
module.exports = (env) =>{
	// ...
	mode: env.production ? "production" : "development"
}
```

这样在编译的时候命令行加上参数就能识别出是什么环境了

```bash
npx webpack --env production
```

打包生产环境的代码是如果赔了`minimizer: [new CssMinimizerPlugin()]`就不会自动启用`terser-webpack`插件了，需要手动去配置才会压缩代码

```bash
npm i terser-webpack-plugin -D
```

```js
optimization: {
      minimizer: [new CssMinimizerPlugin(), new TerserPlugin()],
}
```

## 拆分配置文件

创建一个`webpack.config.dev.js`，将其中的代码压缩部分删除即可，运行`npx webpack -c ./config/webpack.config.dev.js`

创建一个`webpack.config.prod.js`，将其中的`devServer`和`devtool`删除即可，运行`npx webpack -c ./config/webpack.config.prod.js`

## 提取合并公共配置

创建一个`webpack.config.common.js`，将dev和prod中的公共配置提取到这个文件中，dev和prod两个文件只需要写一些差异性配置即可



合并这三个配置文件需要安装`webpack-merge`插件

```bash
npm i webpack-merge -D
```

```js
const { merge } = require("webpack-merge");
const commonConfig = require("./webpack.config.common");
const productionConfog = require("./webpack.config.prod");
const developmentConfig = require("./webpack.config.dev");

module.exports = (env) => {
  switch (true) {
    case env.development:
      return merge(commonConfig, developmentConfig);
    case env.production:
      return merge(commonConfig, productionConfog);
    default:
      return new Error("No matching configuration was found");
  }
};
```

```bash
npx webpack ./config/webpack.config.js --env development
```

## source-map

source-map通过devtool选项开启，默认是eval模式

| 模式                    | 能否锁定代码行数 | 说明                                                         |
| ----------------------- | ---------------- | ------------------------------------------------------------ |
| eval                    | 能               | 每个module会封装到eval里包裹起来执行，并且会在末尾追加注释//@sourceURL |
| source-map              | 能               | 生成一个SourceMap文件                                        |
| hidden-source-map       | 不能             | 和source-map一样，但是不会再bundle末尾追加注释               |
| inline-source-map       | 能               | 生成一个DataUrl形式的SourceMap文件                           |
| eval-source-map         | 能               | 每个module通过eval来执行，并且生成一个DataUrl形式的SourceMap |
| cheap-source-map        | 能               | 生成一个没有列信息的SouceMap文件，不包含loader的sourcemap    |
| cheap-module-source-map | 能               | 生成一个没有列信息的SourceMap文件，同时loader的sourmap也被简化为只包含对应行的（开发环境推荐） |

## devServer

```js
devServer: {
	static: path.resolve(__dirname, './dist'),
	compress: true, // 开发环境是否启用代码压缩（在http传输过程中传输的的gzip）
    port: 3000, // 配置开发服务的端口
    headers: { // 设置开发服务的响应头
      "X-Access-Token": "abc123",
    },
    proxy: { // 配置代理
      "/api": {
        target: "http://localhost:80",
      },
    },
    https: true, // https开启
    http2：true， // http2开启，也是https
    historyApiFallback: true， // 当路由没有匹配到时用首页代替
    host: '0.0.0.0', // 同一局域网内的人也可以通过这个ip访问
}
```

## 模块热替换和热加载

应用程序运行时，会替换、添加或删除模块，无需重新加载整个页面

```js
devServer: {
	hot: true, // 默认开启，开启热替换，保持页面原有状态，只改变修改的代码部分
	liveReload: true, // 默认开启，开启热加载，无需刷新页面即可看到更新后的状态
}
```

js实现热替换必须加以下一段代码

```js
if(module.hot){
	module.hot.accept('./input.js', () =>{
	
	})
}
```

## eslint

安装eslint并创建eslint配置文件

```bash
npm i eslint -D
npx eslint --init // 初始化eslint配置文件
npx eslint ./src // 检查src下的代码规范
```

```json
{
    "env": {
        "browser": true, // 脚本运行在浏览器里
        "es2021": true // 脚本支持es2021
    },
    "extends": [
        "airbnb-base" // 用airbnb的规范来格式化代码
    ],
    "parserOptions": {
        "ecmaVersion": "latest", 
        "sourceType": "module"
    },
    "rules": {
    	"no-console": 0
    },
    "globals": {

    }
}
```

在webpack中应用eslint需要先安装`eslint-webpack-plugin`

```js
const ESLintPlugin = require('eslint-webpack-plugin');
const HtmlWebpackPlugin = require('html-webpack-plugin');

module.exports = {
  entry: './src/app.js',
  mode: 'development',
  plugins: [new HtmlWebpackPlugin(), new ESLintPlugin()],
};
```



