# URL loader

작은 이미지와 같은 리소스들을 문자열 형태로 표현해 줍니다. 

fileloader는 파일을 입력받고 출력해준다면, 

urlloader는 파일을 받고, 출력을 data-uris의 형태로 출력해줍니다. 



dataURIs 는 다음과 같은 형식을 가집니다. 

```text
data:mediatype;base64,data
```

* data: 으로시작
* mediatype; 읽어들인 모듈에 대한 미디어 타입
* base64  2진수 값을 텍스트로 인코딩 할때 사용되는 스킴
* , 다음 의 공간에 들어가게 된다. 

```text
npm i url-loader
```

먼저 svg파일 하나를 준비해주세요. 

url-loader는 file-loader와 비슷합니다. 

```javascript
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
```

index.js에서 svg파일도 불러주세요. 

```javascript
import "normalize.css";
import styles from "./index.css";
import $ from "jQuery";
import IMG from "./assets/웹팩.png";
import SVG from "./assets/1.svg";

function component() {
  const element = document.createElement("div");
  element.innerHTML = "Hello Webpack";

  const imgElement = document.createElement("img");
  imgElement.src = SVG;
  element.appendChild(imgElement);

  element.classList = styles.helloWebpack;

  return element;
}
document.body.appendChild(component());
console.log($(`.${styles.helloWebpack}`).length);
console.log(`IS_PRODUCTION MODE:${IS_PRODUCTION}`);
```

![base64&#xB85C; &#xBCC0;&#xD658;&#xB418;&#xC5B4; &#xC798; &#xB098;&#xD0C0;&#xB098;&#xACE0; &#xC788;&#xC2B5;&#xB2C8;&#xB2E4;. ](.gitbook/assets/image%20%287%29.png)

이러한 방식을 통해 얻게 되는 이점은 무엇일까요? 

url-loader를 통해 변환된 이미지들은 문자열 형태로 변환되기 때문에

문서 안에 바로 적용이 되어 문서의 크기가 조금 커지는 단점은 있지만, 

이미지가 작은 경우 크게 영향을 주지 않고, 요청수를 줄이고, 속도를 높일 수 있다는 장점이 있습니다. 

url-loader 공식에 들어가보면 

limit을 넘어가는 사이즈의 이미지의 경우 file-loader를 이용하는 방법을 설명하고 있으니 들어가 보시기를 추천합니다. 

