# 개발모드 셋팅 \(dev-server\)

개발모드는 개발하는데 지원되는 환경입니다. 

개발과정을 위한 편의성을 지원하고, 생산성을 향상시키는 옵션을 설정할 수 있습니다. 

## webpack-dev-server

웹팩에서는 개발모드를 위한 로컬서버를 지원해주는데, 이를 위해서는 webpack-dev-server를 사용해야 합니다. 

이에 앞서 common.js에서 webpack.DefinePlugin을 통해 개발모드임을 알수있도록 상수값을 입력했는데, 이를 수정해주겠습니다. 

```javascript
new webpack.DefinePlugin({
  IS_PRODUCTION: isProduction, //접근하기 위한 값 (true면 production, 아니면 dev)
}),
```

앞서 설정한 isProduction값으로 설정을 해주었습니다. 

```javascript
./node_modules/.bin/webpack-dev-server --config webpack.dev.js
```

직접 노드모듈에 webpack-dev-server에 접근해서 dev.js를 실행하라고 터미널에 입력해줍니다. 

이렇게 사용해도 되지만 pakage.json에서 script를 등록해보겠습니다. 

```javascript
"scripts": {
  "start":"cross-env NODE_ENV=DEVELOPMENT webpack-dev-server --config webpack.dev.js",
  "dev": "cross-env NODE_ENV=DEVELOPMENT webpack --config webpack.dev.js",
  "build": "cross-env NODE_ENV=PRODUCTION webpack --config webpack.prod.js"
},
```

이렇게 하면 콘솔창에 나타만 주소로 접속을 할 수 있습니다. 

이렇게 dev-server를 켜게 된다면 일반 서버와는 다른 결과를 알 수 있습니다. 

번들파일들인 dist폴더를 삭제한뒤 새로고침해보세요 . 

![dist&#xD3F4;&#xB354;&#xAC00; &#xC5C6;&#xC74C;&#xC5D0;&#xB3C4; &#xC798; &#xC2E4;&#xD589;&#xB418;&#xACE0; &#xC788;&#xC2B5;&#xB2C8;&#xB2E4;. ](.gitbook/assets/image%20%285%29.png)

이게 어떤 차이가 있냐하면, 빌드된 결과물이 파일로 써지지 않고, 

메모리상에 build된 값이 써지기 때문에 더 빠르게 피드백을 받을 수 있습니다. 

파일 프로토콜이 아닌 환경으로 컨텐츠가 제공되기 때문에, cross-origin과 같은 설정내용들도 확인 할 수 있으며, 실제 웹에서 어떤식으로 접근해야 할 지를 알 수 있습니다. 

어떤 기능이 더 있는지 알아보겠습니다. 

index.css를 수정해보면 바로 다시 빌드가 되서 적용이되는데, 

 수정사항을 적용하면 watch해서 빌드를 해주고 브라우저를 새로고침 해주기 때문에 live server를 볼 수 있는 장점을 가지고 있습니다. 

### History API fallback 

이 키의 경우는 devserver에서 사용할 수 있는 키이기 때문에 dev.js에서 설정을 해주도록 하겠습니다. 

```javascript
const merge = require("webpack-merge");
const common = require("./webpack.common");

const config = {
  mode: "development",
  devServer: {
    historyApiFallback: false,
  },
};

module.exports = merge(common, config);
```

historyApiFallback 키는 라우팅과 관련된 키인데, 특정 라우팅을 했을때 제공하지 않았던 라우팅으로 이동하게 되면 예외처리를 하거나, 특정 지점으로 이동시킬 수 있습니다. 

현재는 false값으로 사용을 안하는 상태입니다. 이러한 경우에 특정 없는 페이지로 이동을 시켜볼까요? 

![404status&#xCF54;&#xB4DC;&#xB97C; &#xC5BB;&#xAC8C;&#xB429;&#xB2C8;&#xB2E4;. ](.gitbook/assets/image%20%2812%29.png)

historyApiFallback을 사용해 다른 페이지로 이동하도록 만들어 보겠습니다. 

```javascript
const config = {
  mode: "development",
  devServer: {
    historyApiFallback: true,
  },
};
```

![&#xC5C6;&#xB294; &#xD398;&#xC774;&#xC9C0;&#xB85C; &#xC774;&#xB3D9;&#xD574;&#xB3C4; &#xB2E4;&#xC2DC; fallback&#xB429;&#xB2C8;&#xB2E4;. ](.gitbook/assets/image%20%289%29.png)

이런 historyApiFallback의 기능은 SPA같이 html5 history를 사용하게 되는데 true가 아닌 객체값을 넣어서 특정 경로로 이동시킬 수도 있습니다. 

```javascript
const merge = require("webpack-merge");
const common = require("./webpack.common");

const config = {
  mode: "development",
  devServer: {
    historyApiFallback: {
      rewrites: [//여러가지 페이지로 이동시킬 수 있다.
        { from: /^\/subpage$/, to: "subpage.html" }, 
        //정규표현식에 적힌 경로로 들어오면, subpage.html로 이동
      ],
    },
  },
};

module.exports = merge(common, config);
```

subpage.html을 만들고 테스트를 해보겠습니다. 

```javascript
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>subpage</title>
  </head>
  <body>
    <h1>subpage입니다!</h1>
  </body>
</html>
```

![/subpage&#xB85C; &#xC774;&#xB3D9;&#xC2DC; &#xB2E4;&#xC74C; &#xD654;&#xBA74;&#xC744; &#xBCF4;&#xC5EC;&#xC90D;&#xB2C8;&#xB2E4;. ](.gitbook/assets/image%20%2828%29.png)

**404page**만드는 법을 알아볼까요

```javascript
const merge = require("webpack-merge");
const common = require("./webpack.common");

const config = {
  mode: "development",
  devServer: {
    historyApiFallback: {
      rewrites: [
        { from: /^\/subpage$/, to: "subpage.html" },
        { from: /./, to: "404.html" }, //  
      ],
    },
  },
};

module.exports = merge(common, config);
```

`/./` 라고 하게 되면 특정 경로가 지정되지 않은 나머지 모든 경로들을 가르키게 됩니다. 

![&#xC5C6;&#xB294; &#xACBD;&#xB85C;&#xB97C; &#xC785;&#xB825;&#xC2DC; 404.html&#xC774; &#xCD9C;&#xB825;&#xB429;&#xB2C8;&#xB2E4;. ](.gitbook/assets/image%20%284%29.png)

### open

이 옵션은 최초로 웹팩스크립트를 사용했을때 기본브라우저가 자동으로 열리게 됩니다. 

create-react-app을 하게 되면 자동으로 브라우저가 열리죠? 이 설정이랍니다. 

```javascript
const merge = require("webpack-merge");
const common = require("./webpack.common");

const config = {
  mode: "development",
  devServer: {
    open: true,   //open 옵션
    historyApiFallback: {
      rewrites: [
        { from: /^\/subpage$/, to: "subpage.html" },
        { from: /./, to: "404.html" }, 
      ],
    },
  },
};

module.exports = merge(common, config);

```

### overlay 

에러 메세지가 발생했을때 console창이나 terminal에 있는 에러메세지가 브라우저 화면에 나타나게 됩니다. 

```javascript
const merge = require("webpack-merge");
const common = require("./webpack.common");

const config = {
  mode: "development",
  devServer: {
    overlay: true, //overlay 옵션
    open: false,
    historyApiFallback: {
      rewrites: [
        { from: /^\/subpage$/, to: "subpage.html" },
        { from: /./, to: "404.html" },
      ],
    },
  },
};

module.exports = merge(common, config);

```

![terminal&#xC5D0;&#xC11C; &#xBCF4;&#xC774;&#xB294; &#xAC83;&#xACFC; &#xAC19;&#xC740; &#xC624;&#xB958;&#xAC00; &#xCD9C;&#xB825;&#xB429;&#xB2C8;&#xB2E4;. ](.gitbook/assets/image%20%282%29.png)

### port

자동으로 설정되있는 8080포트값을 다른 값으로 수정해줍니다. 

여러 서버를 돌리면 충돌될수 있기때문에 패턴을 따르는 것이 좋습니다. 

```javascript
const merge = require("webpack-merge");
const common = require("./webpack.common");

const config = {
  mode: "development",
  devServer: {
    port:3333, //포트번호
    overlay: true, //overlay 옵션
    open: false,
    historyApiFallback: {
      rewrites: [
        { from: /^\/subpage$/, to: "subpage.html" },
        { from: /./, to: "404.html" },
      ],
    },
  },
};

module.exports = merge(common, config);
```

![3333&#xD3EC;&#xB4DC;&#xB85C; &#xC11C;&#xBC84;&#xAC00; &#xC5F4;&#xB838;&#xC2B5;&#xB2C8;&#xB2E4;. ](.gitbook/assets/image%20%2831%29.png)

지금까지가 가장 기본적인 dev-server의 기본적인 옵션을 알아보았습니다. 

