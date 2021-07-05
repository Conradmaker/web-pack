# Mode

프로젝트 환경설정을하다보면 개발자는 개발시에는 기능을 사용하기 위해 여러가지 셋팅을 하고,

반대로 배포시에는 사용자들에게 빠른 전달을 위해 최적화를 진행해 주어야 합니다. 

개발모드와 프로덕션 모드로 나누어 설정을 할 수 있는데, 

앞에서 진행한 압축같은 것은 프로덕션 모드라고 할 수 있습니다. 



최적화와 관련되서 빌드명령어를 수행할때마다 조금씩 빌드시간이 늘어났습니다. 

빌드시간이 길어지면 개발 효율이 떨어지기 때문에 앞에서 진행한 최적화를 프로덕션모드에만 적용하도록 하겠습니다. 

개발모드에서는 빠르게 빠르게 효율이 늘어나야 하니까요. 



총 3개의 파일을 만들것입니다. 

* 공통부분의 파일
* 개발모드의 파일
* 배포모드의 파일

`webpack-merge`를 통해 진행하기 때문에 설치 해주세요!

### 공통영역

webpack.common.js 파일을 만들어 공통적인 부분을 설정해줍니다.

```javascript
const path = require("path");
const HtmlWebpackPlugin = require("html-webpack-plugin");
const { CleanWebpackPlugin } = require("clean-webpack-plugin");
const MiniCssExtractPlugin = require("mini-css-extract-plugin");

module.exports = {
  entry: "./index.js",
  output: {
    filename: "[name].[chunkhash].js", //name이라는 키워드는 nameproperty에 할당한 내용이 들어감
    path: path.resolve(__dirname, "dist"),
  },
  module: {
    rules: [
      {
        test: /\.css$/i,
        use: [
          { loader: MiniCssExtractPlugin.loader },
          {
            loader: "css-loader",
            options: {
              modules: true,
            },
          },
        ],
      },
    ],
  },
  plugins: [
    new MiniCssExtractPlugin({
      filename: "[contenthash].css",
    }),
    //html문저 최적화
    new HtmlWebpackPlugin({
      title: "webpack",
      template: "./index.html",
      minify: {
        collapseWhitespace: true,
        useShortDoctype: true,
      },
    }),
    new CleanWebpackPlugin(),
  ],
};
```

* 최적화에 대한 부분은 제거를 해주었습니다.
* mode는 따로 설정해줄것이기 때문에 제거합니다. 

### 개발모드

```javascript
const merge = require("webpack-merge");
const common = require("./webpack.common");

const config = {
  mode: "development",
};

module.exports = merge(common, config);
```

* merge를 통해 common.js를 합쳐주었습니다. 
* 일단 지금은 모드만 설정해 주었습니다. 

### 배포모드

```javascript
const merge = require("webpack-merge");
const OptimizeCssAssetsPlugin = require("optimize-css-assets-webpack-plugin");
const TerserWebpackPlugin = require("terser-webpack-plugin");
const common = require("./webpack.common");

const config = {
  plugins: [
    new OptimizeCssAssetsPlugin({
      assetNameRegExp: /\.css$/g,
      cssProcessor: require("cssnano"),
      cssProcessorPluginOptions: {
        preset: ["default", { discardComments: { removeAll: true } }],
      },
      canPrint: true,
    }),
  ],
  optimization: {
    runtimeChunk: {
      name: "runtime",
    },
    splitChunks: {
      cacheGroups: {
        commons: {
          test: /[\\/]node_modules[\\/]/,
          name: "venders",
          chunks: "all",
        },
      },
    },
    minimize: true,
    minimizer: [
      new TerserWebpackPlugin({
        cache: true,
      }),
    ],
  },
  mode: "production",
};

module.exports = merge(common, config);
```

* 배포모드에서는 배포시 최적화에 대한 플러그인들을 넣어주었습니다. 
* 내보낼때는 common과 함께 내보냅니다. 

### 빌드시 모드설정

각 빌드마다 다른 모듈을 불러올 수 있도록 pakage.json을 설정하도록 하겠습니다. 

```javascript
  "scripts": {
    "dev": "webpack --config webpack.dev.js",
    "build": "webpack --config webpack.prod.js"
  },
```

이렇게 하면 `npm run dev` 시에는 개발모드로 빌드가 되고, 

`npm run build`를 하면 배포모드로 빌드가 되게 됩니다.

참고로 webpack-merge는 4버전입니다. 

5버전부터는 사용방법이 다르니 명심해주세요.



### Define plugin

이어서 define plugin을 설치해주겠습니다. 

웹팩이 빌드를 할때 특정한 상속값을 만들어 어디서든 사용할 수있게 합니다. 

예를들어 현재 빌드되는 모드를 알고싶으면 어디서든 알 수 있습니다. 

웹팩 내장 모듈입니다.  

```javascript
const path = require("path");
const HtmlWebpackPlugin = require("html-webpack-plugin");
const { CleanWebpackPlugin } = require("clean-webpack-plugin");
const MiniCssExtractPlugin = require("mini-css-extract-plugin");
const webpack = require("webpack"); //불러와서

module.exports = {
  entry: "./index.js",
  output: {
    filename: "[name].[chunkhash].js",
    path: path.resolve(__dirname, "dist"),
  },
  module: {
    rules: [
      {
        test: /\.css$/i,
        use: [
          { loader: MiniCssExtractPlugin.loader },
          {
            loader: "css-loader",
            options: {
              modules: true,
            },
          },
        ],
      },
    ],
  },
  plugins: [
    new MiniCssExtractPlugin({
      filename: "[contenthash].css",
    }),
    //html문저 최적화
    new HtmlWebpackPlugin({
      title: "webpack",
      template: "./index.html",
      minify: {
        collapseWhitespace: true,
        useShortDoctype: true,
      },
    }),
    new CleanWebpackPlugin(),
    //설정
    new webpack.DefinePlugin({
      IS_PRODUCTION: true, //접근하기 위한 값 (true면 production, 아니면 dev)
    }),
  ],
};

```

index.js에서 확인해봅시다. 

```javascript
import "normalize.css";
import styles from "./index.css";
import $ from "jQuery";

function component() {
  const element = document.createElement("div");
  element.innerHTML = "Hello Webpack";

  element.classList = styles.helloWebpack;

  return element;
}
document.body.appendChild(component());
console.log($(`.${styles.helloWebpack}`).length);
console.log(`IS_PRODUCTION MODE:${IS_PRODUCTION}`);
```

pakage.json에서 값을 바꿔주도록 하겠습니다.

```javascript
"scripts": {
  "dev": "NODE_ENV=DEVELOPMENT webpack --config webpack.dev.js",
  "build": "NODE_ENV=PRODUCTION webpack --config webpack.prod.js"
},
```

앞에 NODE\_ENV를 적어주면 노드환경에서 process.env안에서 접근할 수 있습니다. 

안되면 cross-env를 설치한뒤 앞에 cross-env를 붙여주세요

그렇다면 이제 공통 설정 파일에서 HTML플러그인을 모드에 따라 압축이 되도록 혹은 안되도록 설정해 주겠습니다. 

```javascript
    new HtmlWebpackPlugin({
      title: "webpack",
      template: "./index.html",
      minify: {
        collapseWhitespace: true,
        useShortDoctype: true,
      },
    }),
```

```javascript
//불러와서
const isProduction = process.env.NODE_ENV;
    
    new HtmlWebpackPlugin({
      title: "webpack",
      template: "./index.html",
      minify: isProduction
        ? {
            collapseWhitespace: true,
            useShortDoctype: true,
          }
        : false,
    }),
```

3항연산자를 통해 조건을 걸어주었습니다. 

