# stylelint

코드를 작성하다보면 일관적이지 않게 작성할 때가 있는데, 

팀문화에 따라 일관된 코드 표현을 지켜야 하는 경우가 있는데, 

stylelint를 사용하면 표현의 일관성을 맞추는 규칙을 적용해줍니다. 

eslint처럼 개별적으로 설정해줄 수도 있지만, 이번에는 웹팩상에서 변경해 주는 방법을 알아보겠습니다. 

먼저 lint에게 어떤 설정을 지켜야 하는지를 알려주어야 하는데, 

{% embed url="https://github.com/stylelint/stylelint-config-standard" %}

여기 들어가 보면 가이드가 있는데, 각 회사별로 가이드가 다른데 이를 가져와서 사용할 수도 있습니다. 

이 표준설정을 기반으로 룰을 포함해서 규칙 추가를 하거나 뺄수도 있게끔 하는 방법도 설명되어 있습니다. 

먼저 stylelint와 stylelint-config-standard 그리고, stylelint-webpack-plugin , sass를 사용한다면 stylelint-sass도 설치할 수 있습니다. 

```text
npm i stylelint stylelint-scss stylelint-webpack-plugin stylelint-config-standard
```

그럼 이제 총 두가지를 해야 하는데 , 

* 웹팩에서 구동될 수 있도록 plugin추가 
* stylelint-config-standard적용될 수 있도록 설정파일 만들기

lint는 개발환경에서 사용되기 떄문에 dev.js에 추가를 해줍니다. 

```javascript
const merge = require("webpack-merge");
const common = require("./webpack.common");
const StyleLintPlugin = require("stylelint-webpack-plugin");

const config = {
  mode: "development",
  devServer: {
    port: 3333, //포트번호
    overlay: true, //overlay 옵션
    open: false,
    historyApiFallback: {
      rewrites: [
        { from: /^\/subpage$/, to: "subpage.html" },
        { from: /./, to: "404.html" },
      ],
    },
  },
  plugins: [new StyleLintPlugin()],
};

module.exports = merge(common, config);
```

stylelint설정을 위해 eslint처럼 .stylelintrc 를 만들어 주세요.

```javascript
{
    "extends": "stylelint-config-standard"
}
```

이렇게 하면 sass나 css에서 오류가 있을경우 error를 알려준답니다. 



