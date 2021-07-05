# Webpack 기본구조

Webpack 공식문서에는 다음 이미지가 있습니다.

![](.gitbook/assets/image%20%2813%29.png)

왼쪽에는 의존성을 가진 모듈이라고 써져있죠. 

### Webpack이 바라보는 Module

* js
* sass
* hbs - 핸들바스 
* jpg,png
* ...

hbs가 좀 생소할수 있을것 같습니다 . 

hbs는 핸들바스의 약자인데, 데이터를 특정한 양식에 따라 출력해주는 템플릿 엔진입니다. 

SSR시에 서버에서 가지고 있는 데이터를 분석해 바로 출력해주는 역할을 하고 있습니다. 

이러한 모듈들을 웹팩이 다루게 됩니다. 

웹팩을 통해 확장자별로 하나의 파일이 나오게 되는데 이런 과정을 **번들**이라고 하죠? 

![&#xC774; &#xADF8;&#xB9BC;&#xC744; &#xBCF4;&#xBA74; &#xD30C;&#xC77C;&#xB4E4;&#xC774; &#xC11C;&#xB85C;&#xB97C; &#xBC14;&#xB77C;&#xBCF4;&#xACE0; &#xC788;&#xC2B5;&#xB2C8;&#xB2E4;. ](.gitbook/assets/image%20%283%29.png)

이렇게 파일과 파일들이 엮여있는 것을 의존성을 가지고 있는 파일들이라고 합니다. 

번들이라는 과정은 모듈들의 의존성을 안전하게 유지시키면서 하나의 파일로 만드는 과정입니다. 

모듈과 번들은 둘다 가지고 있는 의미가 많습니다. 이제 이 두가지에 관련된 용어들을 알아보겠습니다. 

## 기본구조

### Entry&Output

#### Entry

Entry는 모듈의 의존관계를 이해하기 위한 시작점을 설정합니다. 

복잡한 참조관계를 가지고 있는 모듈중에 어떤 모듈에서 시작을 해야 하는지 알려주는 역할을 하는 것입니다. 

![&#xBAA8;&#xB4C8;A&#xB294; B,C&#xC758; &#xAE30;&#xB2A5;&#xC744; &#xCC38;&#xC870;&#xD558;&#xACE0; &#xC788;&#xC2B5;&#xB2C8;&#xB2E4;.](.gitbook/assets/image%20%2819%29.png)

웹팩은 모듈들의 의존관계를 해석한뒤에 **의존성 그래프**를 만들고 번들링을 하게 됩니다.

이 과정은 파일들로 쪼개진 모듈을 번들파일에 담는 것을 말하는데, 

위 그림에서는 가장 상위에 있는 A를 Entry로 정해줍니다. 

#### Output

Output은 Webpack이 생성하는 번들파일에 대한 정보를 설정합니다. 

파일의 이름이나 위치를 설정해주죠. 



웹팩을 설치해보도록 해요. 

```text
npm i webpack webpack-cli -dev
```

이제 웹팩 명령어를 통해 번들파일을 만들어보겠습니다. 

넓이를 구하는 mathUtil / 로그를 출력하는 log / 사용자에게 정보를 받는 index가 있습니다. 

index.js라는 모듈은 다른 두 모듈을 사용하고 있기때문에 다른 두 모듈과 의존관계가 있다고 말할 수 있습니다. 

이제 이를 통해 번들파일을 만들어 보겠습니다. 

먼저 `npx webpack` 명령어로 웹팩을 실행하면 어떻게 될까요? 

![Entry&#xC640; Output&#xC744; &#xC124;&#xC815;&#xD558;&#xC9C0; &#xC54A;&#xC558;&#xB2E4;&#xACE0; &#xC5D0;&#xB7EC;&#xAC00; &#xCD9C;&#xB825;&#xB429;&#xB2C8;&#xB2E4;. ](.gitbook/assets/image%20%2815%29.png)

웹팩이 4버전으로 올라가면서 설정을 하지 않아도 자동으로 해주는 zero-configuration을 업데이트 해주었는데요, 

 단, Entry경로를 src/index.js로 위치시켜주어야 하며, 

bundle파일이 위치한 장소도 dist폴더에 main.js라는 파일이름으로 결정되어 있습니다. 

이는 따로 설정을 하지 않아도 설정이 된다는 것이죠. 

모듈들을 모두 src로 옮겨서 실행해 볼까요? 

![&#xC774;&#xBC88;&#xC5D0;&#xB294; &#xC5D0;&#xB7EC;&#xC758; &#xB0B4;&#xC6A9;&#xC774; &#xC870;&#xAE08; &#xBC14;&#xB00C;&#xC5C8;&#xC2B5;&#xB2C8;&#xB2E4;. ](.gitbook/assets/image%20%2824%29.png)

Readline이라는 Node의 내장모듈을 읽지 못해 나오는 에러입니다. 

웹팩이 실행하는 환경이 Node라고 target을 통해 Webpack에게 알려주면 됩니다. 

Command 라인에서--를 쓰고 값을 입력하면 키를 입력할 수 있습니다. 

```text
npx webpack --target=node
```

![&#xC790; dist&#xC5D0; main.js&#xD30C;&#xC77C;&#xC774; &#xC0DD;&#xC131;&#xB418;&#xC5C8;&#xC2B5;&#xB2C8;&#xB2E4;. ](.gitbook/assets/image%20%2817%29.png)

`node ./dist/main.js` 를 입력하면 똑같이 실행되는 것을 확인할 수 있습니다.



