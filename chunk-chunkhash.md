# Chunk-chunkhash

우리가 번들링을 하다보면 생성되는 번들파일의 사이즈는 점점 늘어나게 됩니다. 

파일이 적으면 요청수는 줄어들지만, 파일하나가 커져서 받아오는 시간이 오래걸리게 됩니다. 

 

그래서 우리는 몇가지 기준을 통해 번들파일을 몇가지로 분리합니다. 

여기서 분리된 번들파일들을 Chunk라고 하게 되죠. 

이 Chunk들을 나누는 기준이 있는데, 2가지 기준에 대하여 알아보겠습니다.

### Runtime Chunk

웹팩이 의존성그래프를 해석하고 하나의 번들에 모두 모아 만들어주는데 모듈들을 순서대로 읽을수 있도록 하는 **초기화 코드**가 있습니다. 

앱이 메모리를 할당받고 실행되는 환경을 런타임이라고 하는데, 이 런타임 환경에서 모듈을 안전하게 사용할 수 있게 하는 **초기화 코드**들은 모듈이 몇개가 있든 변함이 없습니다. 

이 초기화 코드를 분리하는 것이 Runtime Chunk라고 할 수 있습니다. 

* Runtime Chunk는 변함이 없기에 캐싱이 적용되 속도가 향상됩니다. 

### Vender Chunk

Vender는 외부 패키지에 해당하는 모듈입니다. 

예를들어 immer를 사용한다고 하면, 외부에서 업데이트 되지 않는이상 변함이 없겠죠? 

이런 모듈만 따로 분리를 하면, 우리가 작업하는 번들만 남게 되고 Vender는 캐싱을 사용해 속도가 빨라질 수 있습니다. 



Chunk Hash

청크 해쉬는 나누어진 청크별로 해쉬를 부여하기 때문에 Content Hash처럼 파일명에 변화를 주지 않습니다. 

optimization이라는 키는 웹팩의 번들파일을 최적화해주는 역할을 담당하는데, Chunk를 나누는 것도 최적화에 해당하기 때문에 여기서 설정합니다. 

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
    new HtmlWebpackPlugin({
      template: "./index.html",
    }),
    new CleanWebpackPlugin(),
  ],
  optimization: { //최적화 관련은 여기서!
    runtimeChunk: {
      name: "runtime",
    },
  },
  mode: "none",
};
```

![](.gitbook/assets/image%20%2830%29.png)

런타임청크가 분리가 잘 되었습니다. 

두번째로는 vender파일에 대한 chunk를 만들어 볼 것입니다. 

예시를 위해 jQuery를 설치하고 적용해보겠습니다.

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
```

```javascript
optimization: {
    runtimeChunk: {//Runtime Chunk
      name: "runtime",
    },
    splitChunks: { //Vender Chunk
      cacheGroups: {
        commons: {
          test: /[\\/]node_modules[\\/]/,
          name: "venders",
          chunks: "all",
        },
      },
    },
  },
```

공통으로 묶는 chunk파일이 node\_modules에 있는 파일들이라고 했고, 

이름은 venders chunk는 all로 설정해 주었습니다.

![&#xBCA4;&#xB354;&#xD30C;&#xC77C;&#xC774; &#xB9CC;&#xB4E4;&#xC5B4; &#xC84C;&#xB294;&#xB370;, ](.gitbook/assets/image%20%288%29.png)

벤더청크 파일을 보면 jquery에 대한 내용들이 담겨 있습니다. 

