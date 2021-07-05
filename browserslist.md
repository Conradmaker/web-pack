# Browserslist

Browserslist 는 지원하고자 하는 브라우저들에 대한 설정을 말합니다. 

다른 플러그인에도 영향을 주는 설정인데요, 

{% embed url="https://www.npmjs.com/package/browserslist" %}

이 페이지에 들어가보면 확인할 수 있는 내용중 targetbrowser 간단히 알아보겠습니다. 

targetbrowser는 지원 범위에 해당하는 대상을 설정합니다. 

### 정의하는 방법

browserslist를 정의하는 방법은 크게 3가지가 있습니다. 

* pakage.json에 정의 
  * pakage.json에 browserslist라는 키에 설정을 넣는것
  * browserslist에서 권장
* .browserslistrc파일을 만들어 정의
  * 파일을 만들고 정의를 작성하면 이 파일을 참조합니다. 
  * autoprefixer를 사용하고 있다면, 여기 작성한 브라우저에 대한 venderprefix가 들어갑니다. 
* js 모듈로 만들어서 browserslist를 관리하는 방법도 있습니다. 

### 지원 범위 설정

특정 브라우저의 범위를 포함한다는것을 Query Composition이라고 표현하고 있는데, 특정 범위에 대한 조건을 작성하고 어떻게 조합하는지에 따라 결과가 만들어 지는 것입니다. 

조건들을 만들때 지정하는 타입이 있는데 , 문서에서는 다음과 같이 표현하고 있습니다. 

![](.gitbook/assets/image%20%2837%29.png)

FullList에서는 범위를 지정할때 어떤 조건들을 사용할 수 있는지 설명하고 있습니다. 

#### Full List

You can specify the browser and Node.js versions by queries \(case insensitive\):

* `defaults`: Browserslist’s default browsers \(`> 0.5%, last 2 versions, Firefox ESR, not dead`\).
* `> 5%`: browsers versions selected by global usage statistics. `>=`, `<` and `<=` work too.
* `> 5% in US`: uses USA usage statistics. It accepts [two-letter country code](https://en.wikipedia.org/wiki/ISO_3166-1_alpha-2#Officially_assigned_code_elements).
* `> 5% in alt-AS`: uses Asia region usage statistics. List of all region codes can be found at [`caniuse-lite/data/regions`](https://github.com/ben-eb/caniuse-lite/tree/master/data/regions).
* `> 5% in my stats`: uses [custom usage data](https://www.npmjs.com/package/browserslist#custom-usage-data).
* `> 5% in browserslist-config-mycompany stats`: uses [custom usage data](https://www.npmjs.com/package/browserslist#custom-usage-data) from `browserslist-config-mycompany/browserslist-stats.json`.
* `cover 99.5%`: most popular browsers that provide coverage.
* `cover 99.5% in US`: same as above, with [two-letter country code](https://en.wikipedia.org/wiki/ISO_3166-1_alpha-2#Officially_assigned_code_elements).
* `cover 99.5% in my stats`: uses [custom usage data](https://www.npmjs.com/package/browserslist#custom-usage-data).
* `dead`: browsers without official support or updates for 24 months. Right now it is `IE 10`, `IE_Mob 11`, `BlackBerry 10`, `BlackBerry 7`, `Samsung 4` and `OperaMobile 12.1`.
* `last 2 versions`: the last 2 versions for _each_ browser.
* `last 2 Chrome versions`: the last 2 versions of Chrome browser.
* `last 2 major versions` or `last 2 iOS major versions`: all minor/patch releases of last 2 major versions.
* `node 10` and `node 10.4`: selects latest Node.js `10.x.x` or `10.4.x` release.
* `current node`: Node.js version used by Browserslist right now.
* `maintained node versions`: all Node.js versions, which are [still maintained](https://github.com/nodejs/Release) by Node.js Foundation.
* `iOS 7`: the iOS browser version 7 directly.
* `Firefox > 20`: versions of Firefox newer than 20. `>=`, `<` and `<=` work too. It also works with Node.js.
* `ie 6-8`: selects an inclusive range of versions.
* `Firefox ESR`: the latest \[Firefox ESR\] version.
* `PhantomJS 2.1` and `PhantomJS 1.9`: selects Safari versions similar to PhantomJS runtime.
* `extends browserslist-config-mycompany`: take queries from `browserslist-config-mycompany` npm package.
* `supports es6-module`: browsers with support for specific features. `es6-module` here is the `feat` parameter at the URL of the [Can I Use](https://caniuse.com/) page. A list of all available features can be found at [`caniuse-lite/data/features`](https://github.com/ben-eb/caniuse-lite/tree/master/data/features).
* `since 2015` or `last 2 years`: all versions released since year 2015 \(also `since 2015-03` and `since 2015-03-10`\).
* `unreleased versions` or `unreleased Chrome versions`: alpha and beta versions.
* `not ie <= 8`: exclude browsers selected by previous queries.

You can add `not` to any query.



먼저 pakage.json을 통해 수정하는 방법을 알아보도록 하겠습니다. 

```javascript
.
.
.
    "webpack": "^4.44.1",
    "webpack-cli": "^3.3.12",
    "webpack-dev-server": "^3.11.0",
    "webpack-merge": "^4.2.2"
  },
  "browserslist": [
    "last 2 versions",
    "IE 10",
    "Firefos > 20"
  ]
}
```

* last 2 versions는 최신버전과 직전버전 2가지를 가리킵니다. 
* IE 10 은 특정 브라우저 특정 버전을 말합니다. 
* Firefox &gt; 20 은 Firefox 20버전 이상을 지원하겠다는 의미입니다. 

이렇게 설정해주면 flex를 지원하는 않는 브라우저들에게 자동으로 venderprefix를 적용해 줄 수 있습니다. 

