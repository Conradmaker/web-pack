# File loader

이번에는 이미지 파일을 모듈로 다루는 법을 알아보겠습니다. 

webpack은 css, js 파일 뿐만아니라 image와 같은 정적 파일들도 모두 다룰 수 있습니다. 

다양한 모듈을 해석하기 위해서는 loader가 필요하다고 전시간에 배웠는데, 사용해보도록 하겠습니다. 

먼저 image를 받아온뒤 assets폴더 안에 넣어주세요 . 

## File loader

모듈 안에서 import 또는 require\(\)을 통해 파일을 읽을 수 있도록 해주는 loader입니다. 

```text
npm i file-loader
```

loader는 개발모드 배포모드 공용으로 사용되기 때문에 common.js안에 정의해주겠습니다. 

```javascript
...
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
      {
        test: /\.(png|jpe?g|gif)$/i, //text는 어떤 모듈을 읽을지 정규표현식 i는 대소문자 구분 x
        use: [
          {
            loader: "file-loader",
            options: {
              name: "[contenthash].[ext]", 
            },
          },
        ],
      },
    ],
  },
  ...
```

그뒤 index.js에서 img를 추가해주면 되겠죠? 

```javascript
import "normalize.css";
import styles from "./index.css";
import $ from "jQuery";
import IMG from "./assets/웹팩.png";

function component() {
  const element = document.createElement("div");
  element.innerHTML = "Hello Webpack";

  const imgElement = document.createElement("img");
  imgElement.src = IMG;
  div.appendChild(imgElement);

  element.classList = styles.helloWebpack;

  return element;
}
document.body.appendChild(component());
console.log($(`.${styles.helloWebpack}`).length);
console.log(`IS_PRODUCTION MODE:${IS_PRODUCTION}`);
```

![&#xD30C;&#xC77C; &#xC774;&#xB984;&#xC5D0; &#xCEE8;&#xD150;&#xD2B8; &#xD574;&#xC26C;&#xB610;&#xD55C; &#xC801;&#xC6A9;&#xC774; &#xB418;&#xC5C8;&#xC2B5;&#xB2C8;&#xB2E4;. ](.gitbook/assets/image%20%2840%29.png)

개발모드에서는 잘 되는데 빌드를 하게 되면 어떻게 될까요? 

![&#xC774;&#xB807;&#xAC8C; &#xD30C;&#xC77C;&#xB4E4;&#xC774; &#xB4A4;&#xC8FD;&#xBC15;&#xC8FD;&#xC785;&#xB2C8;&#xB2E4;. ](.gitbook/assets/image%20%286%29.png)

그렇다면 이미지 파일을 나누어 저장 할 수 있도록 설정을 한번 해보겠습니다. 

그전에 모듈들을 src 폴더안에서 관리 하도록 추가로 하겠스빈다. 

![src&#xB85C; css&#xC640; js , img&#xB97C; &#xB123;&#xC5B4;&#xC8FC;&#xACE0;](.gitbook/assets/image%20%2814%29.png)

웹팩 설정에서 Entry를 다시 설정해 주어야 겠죠

```javascript
module.exports = {
  entry: "./src/index.js",
  output: {
    filename: "[name].[chunkhash].js",
    .
    .
    .
```

여기서 수정사항은 2가지 입니다. 

* 파일 경로에 asset이 추가 되어야 하고, 
* dist파일에 바로 생성이 아니라 asset폴더 안에 생성을 해주어야 합니다. 

```javascript
{
   test: /\.(png|jpe?g|gif)$/i, //text는 어떤 모듈을 읽을지 정규표현식 i는 대소문자 구분 x
   use: [
      {
         loader: "file-loader",
         options: {
            name: "[contenthash].[ext]",
            publicPath: "assets/", //읽을때 assets폴더를 읽도록
            outputPath: "assets/", //dist 안에 asset폴더 안에 만들어진다.
          },
       },
    ],
},
```

이렇게 하면 결국 읽을때 Entry 경로 + publicPath + js파일에서 지정한 경로 가 합쳐지게 되는 것입니다. 

![&#xC774;&#xBBF8;&#xC9C0; &#xD30C;&#xC77C;&#xB4E4;&#xC774; assets&#xD3F4;&#xB354; &#xC548;&#xC5D0; &#xB4E4;&#xC5B4;&#xAC00; &#xC788;&#xC2B5;&#xB2C8;&#xB2E4;. ](.gitbook/assets/image%20%2832%29.png)

하지만, 브라우저 개발자 도구에서 이미지의 이름이 해쉬값으로 나오기 때문에 개발중에도 이미지를 확인하기가 어렵습니다. 

이번에는 개발모드시 이름값 즉, fileloader에 option에서 이름을 지정해주는데 이때 name키 값에 함수를 넣어줄 것입니다. 

공식문서에 다 나온답니다. 

```javascript
{
  test: /\.(png|jpe?g|gif)$/i, //text는 어떤 모듈을 읽을지 정규표현식 i는 대소문자 구분 x
   use:[
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
```



