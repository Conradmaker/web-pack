# Module / Bundler

## Module

프로그램을 구성하는 내부의 코드가 기능별로 나뉘어져 있는 형태



#### Module의 표준

Module을 사용하기 위해서는 

Module을 인식하는 ModuleSystem과

Module을 다루는 키워드가 제공되어야 한다. 

여러가지가 있지만 대표적인 2가지는 

* CommonJS \(Node.js\)
* ESM \(ES2015~\)



#### Module 다루기

Module을 다루는 키워드는 다음 두가지로 나눌 수 있다. 

* 내보내는것 
* 가져오는 것

내보내는 형태는 두가지가 있다. 

내보낼 값을 객체에 넣고 한번에 내보내는 형태와 각각 개별적으로 키워드를 지정해 내보내는 것이다. 

가져올때에는 객체형태를 그대로 가져오거나 비구조화를 통해 필요한 것만 가져오기도 한다. 



### CommonJS

* 가져올때 `require(모듈의 경로)`
* 내보낼때 `module.exports` 라는 객체안에 담는다. 

 내보낼때는 다음과 같이 이용할 수 있다. 

```javascript
module.exports={...}   
module.exports.키_이름=값
module.exports=값
exports.키_이름=값
```

다음 코드를 봐주세요

```javascript
/**
 * 1.원의 넓이를 구하는 공식
 * 2.PI 정의
 * 3.공식을 통한 결과
 */

const PI = 3.14;
const getCircleArea = (r) => r * r * PI;
console.log(getCircleArea(2));  //12.56
```

지금은 간단한 코드이지만 많은코드를 한 파일 안에 작성하게 되면 많은 단점들이 있습니다. 

PI값 상수와 넓이공식을 두가지파일로 나누어 보겠습니다. 

```javascript
//mathUtil.js
const PI = 3.14;
const getCircleArea = (r) => r * r * PI;

//키와 값이 같기 때문에 간소화
module.exports = {
  PI,
  getCircleArea,
};
```

```javascript
//index.js
//비구조화 할당을 통해 객체에서 함수만 꺼내온다.
const { getCircleArea } = require("./mathUtil");

console.log(getCircleArea(2)); //12.56
```

이렇게도 작성할 수 있습니다.

```javascript
const PI = 3.14;
const getCircleArea = (r) => r * r * PI;

exports.PI = PI;
exports.getCircleArea = getCircleArea;
```

하지만 여러가지 방식을 함께 쓴다면 중간에 유실될 수도 있기때문에 

한가지 방법으로 통일해서 사용하는 것을 권장합니다. 

### ESM

ESM방식은 프론트영역에서 많이 쓰이는 방식이죠? 

방법은 다음과 같습니다 .

* 가져오기 : `inport 모듈이름 from 모듈위치`
* 내보내기 : `export`    `export default`

노드js는 commonJS방식을 따르기 때문에 `npm i esm`을 통해 설치해주세요.

실행할 때에도 `node -r esm index.js`  로 실행해주세요

-r은 commonJS말고도 다른 표준도 해석할수 있도록 해줍니다.

기존 CommonJS방식의 코드를 ESM방식으로 수정해주세요 

```javascript
//mathUtil.js
const PI = 3.14;
const getCircleArea = (r) => r * r * PI;

export { PI, getCircleArea };

===========================================
//index.js
//비구조화 할당을 통해 객체에서 함수만 꺼내온다.
import { getCircleArea } from "./mathUtil";

console.log(getCircleArea(2)); //12.56
```

그렇다면 export default와는 무슨 차이가 있을까요

```javascript
const PI = 3.14;
const getCircleArea = (r) => r * r * PI;

export default { PI, getCircleArea };

===========================================

import mathUtil from "./mathUtil";

console.log(mathUtil.getCircleArea(2)); //12.56
```

default를 붙여 내보내는 경우 그 객체에 이름을 붙여 접근한뒤 다시 호출시에 안에 있는 함수로 접근을 해주어야 합니다. 



### Module의 종류

모듈의 종료는 총 3가지로 분류될수 있는데 다음과 같습니다. 

#### 1.Built-in Core Module 

내장되어 있는 코어 모듈인데, Node.js에서도 내장모듈을 제공해줍니다. \(ex: Node.js module\)

대표적으로 Node.js에 path 나 fs 등이 있겠네요. 



####  2.Comunity-based Module 

커뮤니티 기반의 모듈입니다. 위에서 살펴본 esm도 이에 해당합니다 . \(예 : NPM\)

NPM이라는 패키지를 통해 모듈을 다루는데, 개인이 만들 모듈을 공개할 수 도 있습니다. 

이런 모듈들은 npm CLI를 통해 접근할 수 있습니다. 



#### 3.Local Module 

특정 프로젝트에 정의된 모듈을 의미합니다. 

방금만든 mathUtil과 같은것을 로컬 모듈이라고 합니다. 

### Module의 장점

* 코드의 재사용성이 증가한다. 
* 코드의 관리가 편해진다. 
* 코드를 모듈화하는 기준이 명확해야 한다. 

자 웹팩이 모듈 번들러인데 이중에 모듈을 알아봤으니 이제 번들에 대하여 알아보겠습니다. 

## Bundle

![](.gitbook/assets/image%20%2826%29.png)

번들이 무슨일을 할까요?

앞에서 모듈에서는 JS를 기준으로 했지만, 왼쪽에는 JS뿐만아닌 다양한 파일들을 Module로 다뤄줍니다. 

이렇게 다양한 파일들과 확장자가 참조하고 이있는 과정이 왼쪽이면, 

오른쪽에 번들이라는 과정을 통해 참조관계가 있는 파일들을 모아 하나의 파일로 만들어줍니다. 

### 번들의 중요성

#### 1.모든 모듈을 로드하기 위한 시간이 단축된다. 

여러개의 모듈을 한파일 안에 담아주기 때문에 결과적으로 속도가 빨라집니다. 



#### 2.사용하지 않는 코드를 제거해준다. 

사용되는 기능도 있고 안하는 기능도 있는데 번들링을 하면 실제로 참조하는 코드만 남게됩니다. 

파일 크기가 감소한다는건 파싱시점도 빨라지고, 사용자도 상대적으로 더빠르게 컨텐츠를 볼수 있습니다. 



#### 3.파일 크기를 줄여줍니다. 

별도의 파일을 압축하는 것보다 하나로 모아진 파일을 압축하는 것이 파일크기가 많이 줄어듭니다. 

