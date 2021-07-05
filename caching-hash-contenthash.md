# Caching- hash,contenthash

웹앱은 서버와 클라이언트로 나눠 생각해볼 수 있는데, 서로 데이터를 전달하고 보냅니다. 

전달하고, 요청하는 과정에서는 비용이라는 것이 발생하는데, 비용은 금전적 뿐만아닌 시간의 개념도 포함하고 있습ㄴ디ㅏ. 

사용자가 서비스를 이용하는데 기다리는 시간을최소화하는 것은 매우 중요합니다. 

그리고 이 과정에서 이용하는 것이 캐싱이죠. 

캐싱은 우리가 필요한 리소스의 복사본이라고 생각하면됩니다. 

그리고 그 복사본을 서버보다 가깝게 놓고 재사용하게 됩니다. 

이렇게 하면 몇가지 장점이 있습니다. 

* 서버에 요청하는 횟수가 줄어든다. 
* 서버에 부하를 줄여준다. 
* 서버보다 가까운곳에서 리소스를 가져오기 때문에 빠르다. 

케싱에는 여러가지 종류가 있는데, 로컬캐시를 이용해볼것입니다. 브라우저 캐시등이 이에 해당하는데, 리로드 , 뒤로가기, 앞으로가기 등에 사용되는 캐시입니다. 

모듈들을 번들파일로 만들면 브라우저는 번들파일을 받고 웹 앱을 동작시키는데, 

이때 설정파일을 기준으로 번들파일을 만들게 되면 같은 이름으로 브라우저는 요청을 보냅니다. 

여기서 요청하는 이름이 같을경우 브라우저는 캐싱을 이용하게 됩니다. 

그렇다면, 이전에 호출했던 파일을 이용하게 되는데, 이렇게 되면 수정된 내용이 수정안된 것처럼 보일 수 있습니다. 

그래서 웹팩은 번들파일 이름에 해쉬값을 붙여 겹치는 것을 방지해줍니다. 

단, 타입이 번들링 될때만 해쉬값을 바꿔줍니다. 

번들링이 되기 전까지는 캐쉬를 이용하게 되고, 수정값이 있어 다시 번들링하게되면 해쉬값이 바뀌게 되어 캐쉬된 파일이 아닌 새로운 파일을 가져오는 것이죠.

이제 웹팩 설정파일에서 번들파일 이름이 해쉬를 이용하도록 해주겠습니다. 

해쉬, 컨텐츠해쉬 , 청크해쉬 3가지를 모두 사용해보겠습니다. 

### 해쉬

파일이 변경되어 빌드할때 부여되는 해쉬값 \(빌드시 값이 바뀜\)

```text
const path = require("path");
const HtmlWebpackPlugin = require("html-webpack-plugin");

module.exports = {
  entry: "./index.js",
  output: {
    filename: "bundle.[hash].js",
    path: path.resolve(__dirname, "dist"),
  },
  module: {
    rules: [
      {
      .
      .
      .
      .
```

빌드를 해보면 다음과 같이 파일명에 해쉬가 붙을것을 볼 수 있습니다. 

![](.gitbook/assets/image%20%2818%29.png)

그런데 계속 파일을 변경하고 빌드를 하면 새로운 해쉬값을 가진 파일들이 쌓이게 되겠죠? 

이 문제를 해결해주는 플러그인은 clean-webpack-plugin입니다. 

이 플러그인은 빌드할때마다 dist폴더를 지워주고 새로 파일들을 만들수 있도록 해줍니다. 

```javascript
  const { CleanWebpackPlugin } = require("clean-webpack-plugin");
  
  //플러그인에 추가
  plugins: [
    new HtmlWebpackPlugin({
      template: "./index.html",
    }),
    new CleanWebpackPlugin(),
  ],
```

적용시킨뒤 실행을 해보면

![&#xB2E4;&#xC74C;&#xACFC; &#xAC19;&#xC774; &#xD30C;&#xC77C;&#xB4E4;&#xC774; &#xC0C8;&#xB85C; &#xB9CC;&#xB4E4;&#xC5B4;&#xC84C;&#xC2B5;&#xB2C8;&#xB2E4;.](.gitbook/assets/image%20%2825%29.png)

### 컨텐츠 해쉬

그전에 css내용을 html문제에 포함시키지 않고 css파일을 별로도 만들어주도록 해볼 것입니다. 

`mini-css-extract-plugin` 을 설치해 주세요

```javascript
const path = require("path");
const HtmlWebpackPlugin = require("html-webpack-plugin");
const { CleanWebpackPlugin } = require("clean-webpack-plugin");
//불러오고
const MiniCssExtractPlugin = require("mini-css-extract-plugin");

module.exports = {
  entry: "./index.js",
  output: {
    filename: "bundle.[hash].js",
    path: path.resolve(__dirname, "dist"),
  },
  module: {
    rules: [
      {
        test: /\.css$/i,
        use: [
          //   {
          //     loader: "style-loader",
          //     options: {
          //       injectType: "singletonStyleTag",
          //     },
          //   },
          //써주고
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
  //써주고
    new MiniCssExtractPlugin(),
    new HtmlWebpackPlugin({
      template: "./index.html",
    }),
    new CleanWebpackPlugin(),
  ],
  mode: "none",
};
```

style-loader가 css를 읽어 html안에 넣어주는 loader였다면 mini-css는 하는일이 충돌되기 때문에 style-loader를 막아주었습니다. 

![main.css&#xAC00; &#xB530;&#xB85C; &#xC0DD;&#xC131;&#xB418;&#xC5C8;&#xC2B5;&#xB2C8;&#xB2E4;. ](.gitbook/assets/image%20%2835%29.png)

이렇게 되면 css는 캐싱이되어 재사용을 할수 있게되고, index.html의 용량은 줄어들겠죠? 

여기서는 main.css이름이 고정되어있기 값이 변해도 적용이 안될 수가 있겠죠? 그렇기 때문에 해쉬값을 적용해주어야 합니다. 

```javascript
  plugins: [
    new MiniCssExtractPlugin({
      filename: "[hash].css",
    }),
    ...
```

이렇게 플러그인에 파일명옵션을 주면 됩니다. 이러면 아래와 같이 해쉬값을 가지게 됩니다. 

![](.gitbook/assets/image%20%2834%29.png)

여기서 또하나의 문제가 발생합니다.. 

자바스크립트를 수정한다고 해서 매번 css도 수정하는 것은 아니기 때문에 둘중 하나만 수정하고 빌드해도 둘다 새로운 이름이 부여되는 것인데요. 

이렇게 되면 캐싱을 제대로 할 수 없습니다 . 

그래서 content-hash를 사용해주어야 합니다. 

이렇게 하면 js파일을 수정해도 css는 해쉬값이 유지가 됩니다. 

![before](.gitbook/assets/image%20%2833%29.png)

![css&#xD30C;&#xC77C;&#xC758; &#xC774;&#xB984;&#xC740; &#xADF8;&#xB300;&#xB85C; &#xC785;&#xB2C8;&#xB2E4;. ](.gitbook/assets/image%20%2838%29.png)

