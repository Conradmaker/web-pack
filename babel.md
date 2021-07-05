# Babel

이전 과정에서 css를 하위버전에 호환성을 맞추기 위해서는 venderprefix를 추가해 주었는데요 , JS도 마찬가지 입니다. 

ES6 스펙의 기능들은 하위 브라우저에서는 적용이 되지 않는 경우가 있습니다. 

이런 발전된 언어의 기능을 하위브라우저에서 사용이 가능하도록 만들어 주는 것이 babel입니다. 

babel적용은 webpack이 JS 파일들을 모듈로 인식하고 번들파일로 만들때 함께 적용될 수 있도록 하겠습니다. 

babel이 실행될때 필요한 컴파일러 babel-cli를 설치합니다. 

```text
 npm i @babel/cli
```

바벨도 cli를 설치하면 babel을 실행할 환경만 만들어 진 것입니다. 

이제 모듈을 어떻게 할것인지를 설정하는 모듈을 설치합니다. 

```text
npm i @babel/core
```

@babel-core가 있어야 바벨이 변활할 파일을 input으로 받아들이면 core에 의해 js를 변환시킵니다. \(변환용입니다\)

```text
npm i @babel/preset-env
```

프리셋은 제공하고자 하는 기능에 따라 내용을 담고 있는 패키지 입니다. 여러 기능을 제공하기 위해 패키지 역할을 하는데, 모던 JS에서 ES5기준으로 하위 브라우저에서도 제공될 수 있도록 많은 스펙에 대한 내용을 담고 있습니다. 

preset-env 말고도, babel-preset-react 등 여러가지 프리셋이 있습니다.

자 이제 여기까지 설치를 하면, babel을 사용할 준비가 된것입니다. 



하지만, 우리는 webpack의 구동과정에서 사용해줄 것이기 loader도 받아주어야 합니다. 

```text
npm i babel-loader
```

이제 바벨을 설정해 줄것인데요, 

바벨도 마찬가지로 .babelrc파일로 설정할 수도 있지만, 현재에는 babel.config.js 라는 모듈로 설정하는 것을 권장합니다. 

```javascript
module.exports = {
  // 이 안에는 preset과 plugin을 사용가능합니다.
  presets: ["@babel/preset-env"], //preset-env를 추가함 , 다른것도 추가 가능
};
```

직접 바벨을 사용하지 않고 웹팩이 실행될때 함꼐 실행될 수 있도록 webpack에서 loader를 설정해주어야 겠죠. 

common.js에서 작업해주세요

```javascript
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
      {
        test: /\.(png|jpe?g|gif)$/i, //text는 어떤 모듈을 읽을지 정규표현식 i는 대소문자 구분 x
        use: [
          {
            loader: "file-loader",
            options: {
              name() {
                if (!isProduction) {
                  return "[path][name].[ext]";
                }
                return "[contenthash].[ext]";
              },
              publicPath: "assets/", //읽을때 assets폴더를 읽도록
              outputPath: "assets/", //dist 안에 asset폴더 안에 만들어진다.
            },
          },
        ],
      },
      {
        test: /\.svg$/i,
        use: [
          {
            loader: "url-loader",
            options: {
              limit: 8192, //byte크기 (파일크기의 제한)
            },
          },
        ],
      },
      // ----------요 부 분------------
      {
        test: /.js/,
        exclude: /node_modules/,
        loader: "babel-loader",
      },
      // ----------요 부 분------------
    ],
  },
```

원래 loader에 객체형태로 옵션을 줘서 babel.config.js의 설정을 할 수도 있지만, 설정파일을 따로 만들어 주었습니다. 

만약 안에서 설정을 하고 싶다면 babel-loader 문서를 활용하면 됩니다. 

* exclude 는 node\_modules 폴더를 포함하지 않기 위해 설정합니다. 

이제 번들링 된 파일을 보면 const와 같은 문법이 var로 바뀌었을 것입니다. 

babel-polyfill

현재 환경에서 기능이 지원되지 않을때 대체기능을 추가해 줍니다. 

es5기준으로 변환을 했는데, 특정 브라우저에서는 es5를 지원 안될수도 있기 때문에, ployfill을 적용하면 지원안되는 기능들을 채워줘서 지원하게 만들어줄 수 있습니다. 

```javascript
npm i @babel/polyfill
```

polyfill 사이트에서 cdn으로 넣는 방법도 있습니다. 

ployfill은 최초 한번만 로드가 되야 하는데, 일단 지금은 index.js 파일밖에 없기 때문에 index.js에서 불러와줍니다. 

```javascript
import "normalize.css";
import styles from "./index.css";
import $ from "jQuery";
import SVG from "./assets/1.svg";
import "@babel/polyfill";
.
.
.
```

이렇게 import만 해주면 된답니다. 



