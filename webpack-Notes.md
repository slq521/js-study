## webpack核心

### webpack基础

- 入口（entry）

- 出口（output）

- 加载器（loader）

- 插件（plugin）

- 模式（mode）

- 模块（module）

- 依赖图（dependency Graph）

####  Loader：加载器

- 专门用来处理一类文件（非js和json）的工具

- 一般以xxx-loader命名

- 原理，把其他类型的文件加载到bundle.js文件中(也可以分离出来)
  - plugin：实现loader之外的功能
    	1、压缩、导出到不同文件等
    	2、xxx-webpack-plugin命名
  -  注意：loader和plugin本质都是npm包
    mode：development、production、none
    module：webpack一切皆模块
    devServer: 开发服务器的配置
    ​

- 命令行打包：webpack ./src/index.js —output-path ./dist --mode=development
  注意：webpack.config.js是以common.js为规范的

### 配置打包流程

#### 添加样式前缀

1、安装：npm install postcss-loader autoprefixer -D

2、配置webpack.config.js
	use: [‘css-loader’, ‘postcss-loader’]
3、新建postcss.config.js
	plugins: [require(‘autoprefixer’)]
4、package.json中指定browserslist
	“browserslist”: { “last 1 version”, “> %1” }
​

#### 校验css代码格式
1、安装：npm i stylelint stylelint-config-standard stylelint-webpack-plugin -D
2、引入：const StylelintPlugin = require(‘stylelint-webpack-plugin’);
3、配置：npm StylelintPlugin({});
4、指定校验规则（在package.json中指定stylelint）
	“stylelint”: { “extends”: “stylelint-config-standard” }
​

#### 压缩css文件

1、安装：npm install optimize-css-assets-webpack-plugin -D
2、引入：const OptimizeCssAssetsPlugin = require(‘optimize-css-assets-webpack-plugin’)
3、配置： new OptimizeCssAssetsPlugin()
​

#### 打包HTML

- 插件：html-webpack-plugin
  1. 生成html文件（用户服务器访问），并在HTML中加载所有的打包资源
  2. 指定HTML模版、设置HTML变量、压缩HTML
  3. HTML压缩 minify
     	扩展ejs（js模版引擎）： [https://ejs.co](https://ejs.co)
     ​

#### Webpack编辑JS

- 目的：将ES6+转成ES5，保证Js在低版本浏览器的兼容性
- 安装：npm install babel-loader @babel/core @babel/preset-env -D
- 注意：@babel/preset-env 只能转换基本js语法（不能转换promise）
  	@babel/polyfill(转义所有的js新语法)
  	使用： npm i @babel/polyfill -D   (这个一般不会用，打包后的文件太大)
  	import ‘@babel/polyfill’ (入口文件中引入)
- core-js(按需转译js新语法)
  配置：按需加载useBuiltIns: ‘usage’
  指定版本 corejs: 3

#### webpack校验JS代码格式

- 安装: tnpm i eslint eslint-config-airbnb-base eslint-webpack-plugin eslint-plugin-import -D
- eslint (校验JS代码格式的工具)
  - 地址：[https://eslint.org](https://eslint.org)
  - eslint-config-airbnb-base(最流行的JS代码格式规范)
    	[https://www.npmjs.com/package/eslint-config-airbnb-base](https://www.npmjs.com/package/eslint-config-airbnb-base)
    	[https://github.com/airbnb/javascript](https://github.com/airbnb/javascript)
  - eslint-webpack-plugin （webpack的eslint插件）
  - eslint-plugin-import：用于在package.json中读取eslintConfig配置项
  - eslint-webpack-plugin：实例化插件到webpack.config.js
  - eslintConfig(package.json)：“eslintConfig”: { “extends”: “airbnb-base” }
    ​

#### 打包图片

- filter-loader：将用到的图片复制到输出目录，过滤掉不用的图片
- url-loader
  - 地址：https//www.npmjs.com/package/file-loader
  - Html-loader: 处理html静态图片问题
    ​

#### 打包字体文件

- test: /\.(eot|svg|ttf|woff|woff2)$/i,
- 不需要特殊处理的文件，直接复制到输出目录中
  - copy-webpack-plugin
  - clean-webpack-plugin // 清除webpack打包前的资源文件
    ​

#### 资源模块

- webpack4

  - raw-loader(将文件导入为字符串)

  - File-loader（将文件发送输出目录）
  - url-loader (将文件发送到输出目录，或转为dataUrl内联到bundle中)​

- webpack 5

  - asset/resource 发送一个单独的文件并导出url（之前通过使用file-loader实现）

  - asset/inline 导出一个资源的data URL（之前通过使用url-loader实现）

  - asset/source 导出资源的源代码（之前通过使用raw-loader实现）

  - asset  在导出一个data url和发送一个单独的文件之间自动选择（url-loader）

    

#### Webpack Dev Server

- 作用：发布web服务，提高开发效率

- 使用：webpack 4: webpack-dev-server …
           Webpack 5: webpack serve ….

- 热更新

  - webpack 4:

    -  hot: true,

  - webpack 5

    -  liveReload: true,(禁用hot)
    -  target: ‘web’（热更新只适用于web相关的targets）

    ```javascript
    // 开发服务器
    devServer: {
      // 启动服务后会自动打开浏览器
      open: true,
      // 指定加载内容的路径
      contentBase: resolve(__dirname, 'dist'),
      // 启用gzip压缩
      compress: true,
      // 端口号
      port: 8080,
      //  启动自动更新 （禁用hot）
      liveReload: true,
    }
    // web模式才用热更新
    target: 'web',
    ```

#### proxy（配置接口代理）

- 解决webpack-dev-server下，访问接口的跨域问题
- 配置：

```javascript
// 配置代理：解决接口跨域问题
proxy: {
  // [http://localhost:8080/api](http://localhost:8080/api)
  '/api': {
    // [http://localhost:8080/api/users](http://localhost:8080/api/users) => [https://api.github.com/api/users](https://api.github.com/api/users)
    target: '[https://api.github.com',](https://api.github.com',)
    // [http://localhost:8080/api/users](http://localhost:8080/api/users) => [https://api.github.com/users](https://api.github.com/users)
    pathRewrite: {
      '^/api': '',
    },
    // 不能使用localhost:8080作为github的主机名
    changeOrigin: true,
  },
},
```


### Webpack进阶

#### 区分打包环境

- 通过环境变量区分

  - webpack 4: webpack —env.production
  - webpack 5: webpack —env production
  - weback.config.js中判断env(读取env.production)

- 通过配置文件区分

  - webpack.dev.conf.js （mode: development）
  - webpack.prod.conf.js (mode: production)
  - webpack.base.conf.js (公共配置)
  - 执行打包时，指定配置文件，（webpack —config webapck.[dev|prod].conf.js）
  - 使用：通过调用 webpack-merge（包）将多个配置合并在一起

  ```javascript
  const { merge } = require('webpack-merge');
  merge(basicConfig, {
  ...
  });
  ```

  

  - webpack DefinePlugin

    - 为配置注入全局变量

    - 开发环境和生产环境的接口地址不同

       2、自定义plugin
       3、自定义loader
       4、代码分离（code Splitting）
       5、源码映射（source map）
       6、删除冗余代码（tree shaking）
       7、缓存
       8、模块解析（resolve）
       9、排除依赖（externals）
       10、模块联邦
      ​

#### 自定义插件

- 定义：webpack插件是一个具有apply方法的javaScript对象，apply方法会被webpack compiler调用，并且在这个那个编译生命周期都可以访问compiler对象
- 原理：通过在生命周期的钩子中挂载函数，来实现功能扩展

- 钩子：钩子是提前在可能增加功能的地方，埋好（预设）一个函数
- 生命周期中的函数

  - webpack常用的钩子
- environment: 环境准备好
- compile： 编辑开始
- compilation： 编译结束
- emit： 打包资源到output之前
- afterEmit： 打包资源到output之后
- done：打包完成

#### 自定义loader

- loader本质上是一个ESM模块，它导出一个函数，在函数中对打包资源进行转换
- 声明一个读取markdown（.md）文件内容的loader
   - marked（将markdown语法转成html）
   - loader-utils（接受loader的配置项）

#### 代码分离

- 为什么
   - 如果吧所有代码打包在一起，可能最终的代码非常大，从而影响加载时间
   - 而且很多代码初始是不需要的，因此，我们可以根据代码使用的紧急程度，将代码分割打包后，按需加载
- 怎么分离
   - 多入口打包：配置entry加载多个入口文件(后面写对象)
      - {index: './src/index.js', about: './src/about.js'}
      - output.filename(不能写成固定名称，否则报错) [name].bundle.js
      - HtmlWebpackPlugin(不同页面加载各自的bundle)   chunks: ['index'] .  chunks: ['about']
   - 提取公用模块：
      - 如果多个页面都用到了一个公共文件（例如：lodash），每个页面都将公共文件打包一次是不合理的，更好的办法是将公共文件提取出来
      - optimization.splitChunks.chunks: all（将公共文件提取出来，单独打包）
- 动态导入：按需加载 | 预加载
   - 懒加载
      - 默认不加载，事件触发后才加载
      - webpackChunkName： '加载名称’（导入时，注释的形式）
   - 预加载
      - 先等待其他资源加载，浏览器空闲时，再加载
      - webpackPrefetch： true
      - 缺点：在移动端有兼容性问题

#### 源码映射（Source Map）

- 什么是Source Map
   - 是一种源代码与构建后代码之间的映射技术
   - 通过.map文件，将构建后的代码与源代码之间建立映射关系
- 为什么要用Source Map
   - 问题： 构建后的代码，出了问题之后不好定位
   - 方案：有了Source Map后，可以快速定位问题代码
- 如果生成Source Map
   - devtool：’映射模式‘
   - 映射模式（devtool的值）
      - 不同映射模式的报错定位效果和打包执行速度不同
      - webpack4中，一共有13种不同的映射模式
      - webpack5中，一共有26种映射模式
   - webpack5中命名更加严格
      - cheap-module-eval-source-map => eval-cheap-module-source-map
- 如果选取合适的映射模式（个人建议-不绝对）
   - 开发环境（eval-cheap-module-source-map）
   - 生产环境（none | nosource-source-map）

#### Tree Shaking（摇树）

- Tree Shaking的作用是删除未引用代码（dead code）
   - return 后面的代码
   - 只声明，而未使用的函数
   - 只引用，未使用的代码
- 前提
   - 使用ES Module规范的代码，才能执行Tree Shaking
   - Tree Shaking依赖于ES Modules的静态语法分析
- 如何使用
   - 生产模式： Tree shaking会自动开启
   - 开发模式
      - usedExports （optimazation.usedExports: 标记没用的代码） 
         - /* unused harmony export xxxx */
         - terser-webpack-plugin(删除没用的代码)
            - optimization.minimize: true(删除unused harmony export xxxx标记的代码)
            - webpack 4需要单独安装（webpack 5无需安装）	
         - Tree Shaking与Source Map存在兼容性问题
            - devtool：source-map | inline-source-map | hidden-source-map | nosource-source-map
            - eval模式，将JS输出为字符串（不是ES Module规范），导致Tree Shaking失效
      - sideEffects
         - 副作用
            - 无副作用： 如果一个模块单纯的导入导出变量，那它就无副作用
            - 有副作用：如果一个模块还修改了其他模块或者全局的一些东西，就有副作用
               - 修改全局变量
               - 在原型上扩展方法
               - css的引用
            - sideEffects的作用：把未使用但无副作用的模块一并删除
               - 对于没有副作用的模块，未使用代码不会被打包（相当于压缩了输出内容）
         - 使用：开启副作用（webpack.config.js）
            - optimization.sideEffects: true
            - 标识代码是否有副作用（package.json）
               - "sideEffects"
                  - fase: 所有代码都没有副作用（告诉webpack可以安全地删除未用的exports）
                  - true： 所有代码都有副作用
                  - 数组：告诉webpack哪些模块有副作用，不删除  ['./src/wp.js', '*.css']

#### 缓存

- Babel 缓存
   - cacheDirectory: true (第二次构建时，会读取之前的缓存)
- 文件资源缓存
   - 如果代码在缓存期内，代码更新后看不到实施效果
   - 方案：将代码文件名称，设置为哈希名称，名称发生变化时，就加载最新的内容
- Webpack 哈希值 （output.filename: '[name].[chunkhash].js'）
   - [hash] 每次webpack打包生成的hash值
   - [chunkhash] 不同chunk的hash值不同 - 同一次打包可能生成不同的chunk
   - [contenthash] 不同内容的hash值不同 - 同一个chunk中可能有不同的内容

#### 模块解析（resolve）

- 配置模块解析的规则
- alias：配置模块加载的路径别名
   - alias：{ '@': resolve('src')  }
- extensions: 引入模块时，可以省略哪些后缀
   - extensions：[".js", ".json"]

- 排除依赖（externals）
   - 排除打包依赖项（防止对某个依赖项进行打包）
   - 一般来说，一些成熟的第三方库，是不需要打包的
   - 例如：jQuery，我们可以在模版文件中直接引用CDN中的压缩

#### 模块联邦（Module Federation）

- 多个应用，可以共享一个模块（本地可以调远程的模块）
- 模块提供方
   - name： 当前应用名称（供调用方使用）
   - filename：打包后的文件名称（相当于export导出）
   - exposes：暴露模块（相当于export导出）
      - 模块名称： 模块文件路径
- 模块使用方
   - remote：导入模块（相当于import）
   - 导入后的别名： “远程应用的名称@远程地址/远程导出的文件”
   - import(“导入后的别名/模块名称”).then(//...)
