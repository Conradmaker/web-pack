# Minification & Mangling

네트워크를 통해 전송되는 리소스의 양이 커지면 사용자에게 전달되는 양도 커지기 때문에 

불필요한 리소스들을 제거해 주어야만 합니다. 

Mode를 사용하지 않고 직접 소스코드를 최적화 하는 과정을 알아봅시다!!

### Minify 

코드를 압축시키는 과정입니다.

* 동작에 관여하지 않는 것들을 제거
  * console.log\(\)
  * 주석제거
* 표현을 간결화
  * 들여쓰기, 띄어쓰기 최소화
  * 분기문\(if문등\) 을 3항연산자로
* 표현의 난독화 \(uglyfy\)

  * 의미있는 변수명들을 알파벳 한두글자를 치환할수도. 
  * 용량을 줄이기도 하지만, 외부의 접근을 어렵게 할 수도 있다. 

## HTML

먼저 Markup \(HTML\)을 최적화를 알아보겠습니다. 

HtmlwebpackPlugin이라는것을 통해 할수 있는데, minify키에 할당하는 값을 객체로 만들면 선택적으로 진행할 수 있고, bolean타입 true로 설정해주면, 전체를 최적화 할 수 있습니다. 

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
  },
  mode: "none",
};

```

이렇게 설정해주면 코드의 공백을 없애주는 등 코드를 최소화시켜줍니다. 

물론 공식문서를 보면 더 많은 기능들을 볼 수 있습니다. 

## CSSNANO

[https://cssnano.co/](https://cssnano.co/)

css도 최적화를 해줄 수 있습니다. 

cssnano모듈을 설치한뒤에 커맨드로 직접 선정하고 배치할지를 정할수 있는데, 

직접 커맨드로 설정하지는 않고, 웹팩 빌드와 함께 사용해 보도록 하겠습니다. 

그러기 위해서는 optimize-css-assets-webpack-plugin 도 함께 다운받아주어야 합니다. 

{% embed url="https://www.npmjs.com/package/optimize-css-assets-webpack-plugin" %}



```javascript
npm i cssnano optimize-css-assets-webpack-plugin
```

공식문서에 나와있는 그대로를 한번 사용해보도록 하겠습니다. 

```javascript
const OptimizeCssAssetsPlugin = require("optimize-css-assets-webpack-plugin");
.
.
.
.
      },
    ],
  },
  plugins: [
  //요부분
    new OptimizeCssAssetsPlugin({
      assetNameRegExp: /\.css$/g,
      cssProcessor: require("cssnano"),
      cssProcessorPluginOptions: {
        preset: ["default", { discardComments: { removeAll: true } }],
      },
      canPrint: true,
    }),
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
  },
  mode: "none",
};
//아래처럼 최적화 됩니다. 
//html{line-height:1.15;-webkit-text-size-adjust:100%}body{margin:0}main{display:block}h1{font-size:2em;margin:.67em 0}hr{box-sizing:content-box;height:0;overflow:visible}pre{font-family:monospace,monospace;font-size:1em}a{background-color:transparent}abbr[title]{border-bottom:none;text-decoration:underline;text-decoration:underline dotted}b,strong{font-weight:bolder}code,kbd,samp{font-family:monospace,monospace;font-size:1em}small{font-size:80%}sub,sup{font-size:75%;line-height:0;position:relative;vertical-align:baseline}sub{bottom:-.25em}sup{top:-.5em}img{border-style:none}button,input,optgroup,select,textarea{font-family:inherit;font-size:100%;line-height:1.15;margin:0}button,input{overflow:visible}button,select{text-transform:none}[type=button],[type=reset],[type=submit],button{-webkit-appearance:button}[type=button]::-moz-focus-inner,[type=reset]::-moz-focus-inner,[type=submit]::-moz-focus-inner,button::-moz-focus-inner{border-style:none;padding:0}[type=button]:-moz-focusring,[type=reset]:-moz-focusring,[type=submit]:-moz-focusring,button:-moz-focusring{outline:1px dotted ButtonText}fieldset{padding:.35em .75em .625em}legend{box-sizing:border-box;color:inherit;display:table;max-width:100%;padding:0;white-space:normal}progress{vertical-align:baseline}textarea{overflow:auto}[type=checkbox],[type=radio]{box-sizing:border-box;padding:0}[type=number]::-webkit-inner-spin-button,[type=number]::-webkit-outer-spin-button{height:auto}[type=search]{-webkit-appearance:textfield;outline-offset:-2px}[type=search]::-webkit-search-decoration{-webkit-appearance:none}::-webkit-file-upload-button{-webkit-appearance:button;font:inherit}details{display:block}summary{display:list-item}[hidden],template{display:none}
```

## JS

uglyfyJS, terser 등등 여러가지가 있는데, 

그중 우리는 Webpack의 기본 모듈인 terser를 사용해보도록 하겠습니다.

terser를 이용할수 있게 하는 plugin의 이름은 `terser-webpack-plugin` 입니다 설치해주세요

```javascript
const TerserWebpackPlugin = require("terser-webpack-plugin");
.
.
.
//이 플러그인은 Plugin 키에 넣지 않고 optimize키 안에 넣어줄 것입니다. 
       useShortDoctype: true,
      },
    }),
    new CleanWebpackPlugin(),
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
    // -----요기부터 -----
    minimize: true, //이렇게 하면 빌드가 끝나고 바로 압축이 진행되는데, terserplugin을 안에 추가해줍니다.
    minimizer: [
      new TerserWebpackPlugin({
        //minimizer의 경우 어떤 컴프레셔를 넣을수 있습니다. 다른걸 넣어도 됩니다.
        cache: true, //빌드가 반복될때 파일에 변화가 없으면 캐싱을 사용해 시간을 줄여줍니다.
      }),
    ],
  },
  mode: "none",
};
```

결과는 다음과 같이 한줄로 쭉 나오게 되는데요

```javascript
!function(e){function r(r){for(var n,l,f=r[0],i=r[1],a .........
```

보면 알 수 있듯이 변수명과 같은 것들에 난독화 까지 진행이 되어있는 것을 확인할 수 있습니다. 



