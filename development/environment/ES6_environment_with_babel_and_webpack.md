ES6+(ES6 이상의 버전)를 사용하여 프로젝트를 진행하려면 ES6+로 작성된 코드를 IE를 포함한 모든 브라우저에서 문제 없이 동작시키기 위한 개발 환경을 구축하는 것이 필요하다. 특히 모듈의 경우, 모듈 로더가 필요하다.

2019년 5월 현재, 모던 브라우저(Chrome 61, FF 60, SF 10.1, Edge 16 이상)에서 ES6 모듈을 사용할 수 있다. 단, 아래와 같은 이유로 아직까지는 브라우저가 지원하는 ES6 모듈 기능보다는 Webpack 등의 모듈 번들러를 사용하는 것이 일반적이다.

- IE를 포함한 구형 브라우저는 ES6 모듈을 지원하지 않는다.
- 브라우저의 ES6 모듈 기능을 사용하더라도 트랜스파일링이나 번들링이 필요하다.
- 아직 지원하지 않는 기능(Bare import 등)이 있다. ([ECMAScript modules in browsers](https://jakearchibald.com/2017/es-modules-in-browsers/) 참고)
- 점차 해결되고는 있지만 아직 몇가지 이슈가 있다. ([ECMAScript modules in browsers](https://jakearchibald.com/2017/es-modules-in-browsers/) 참고)

트랜스파일러(Transpiler) [Babel](https://babeljs.io/)과 모듈 번들러(Module bundler) [Webpack](https://webpack.js.org/)을 이용하여 ES6+ 개발환경을 구축하여 보자. 아울러 Webpack을 통해 ES6+ 코드와 Sass를 트랜스파일링하는 방법도 알아볼 것이다.

환경 구성에서 사용 된 node.js와 npm 버전은 다음과 같다.

> **Node.js**<br>
> 12.3.1

> **npm**<br>
> 6.9.0

# 1. Babel
## 1.2 Babel이란?

Babel은 ES6+ 코드를 ES5 이하의 버전으로 트랜스파일링한다.

```javascript
// ES6(Arrow Function) + ES7(Exponentiation operator)
[1, 2, 3].map(n => n ** n);
```

위 코드는 ES6에서 도입된 화살표 함수와 ES7에서 도입된 거듭제곱 연산자를 사용하고 있다. 이 두가지 기능은 IE는 물론이고 구형 브라우저에서 지원하지 않는다. 따라서 IE나 구형 브라우저에서도 동작하는 애플리케이션을 구현하기 위해 ES6+ 코드를 ES5 이하의 버전으로 변환(트랜스파일링)할 필요가 있다. Babel을 사용하면 위 코드를 아래와 같이 ES5 이하의 버전으로 트랜스파일링할 수 있다.

```javascript
// ES5
"use strict";

[1, 2, 3].map(function (n) {
  return Math.pow(n, n);
});
```

# webpack
## webpack 이란?
웹팩은 프로젝트의 구조를 분석하고 자바스크립트 모듈을 비롯한 관련 리소스들을 찾은 다음 이를 브라우저에서 이용할 수 있는 번들로 묶고 패킹하는 모듈 번들러(Module bundler)다.

# npm package 설치

```bash
npm install --save--dev @babel/cli @babel/core @babel/preset-env babel-loader
npm install --save--dev webpack webpack-cli webpack-node-externals
```

| package name           | description                                 |
| :--------------------- | :------------------------------------------ |
| @babel/cli             | command line을 통해 코드를 transpile 할 수 있는 도구    |
| @babel/core            | transpile을 수행하는 패키지                         |
| @babel/preset-env      | 브라우저 환경에 맞는 구문 변환 관리 패키지                    |
| babel-loader           | webpack에서 babel을 이용해 transpile 하기 하기 위한 패키지 |
| webpack                | 모듈 번들러                                      |
| webpack-cli            |                                             |
| webpack-node-externals | node.js 구동 용 node target으로 실행하기 위한 패키지      |

패치지 설치가 완료되면 webpack.config.js 파일을 생성한다. 

```javascript
const webpack = require('webpack');
const path = require('path');
const nodeExternals = require('webpack-node-externals')

module.exports = {
  target: 'node',
  externals: [nodeExternals()],
    entry: './server.js',
    output: {
        path: path.resolve(__dirname, 'public'),
        publicPath: '/public/',
        filename: 'bundle.js'
    },
    module: {
        rules: [
            {
                test: /\.js$/,
                include: path.join(__dirname),
                exclude: /(node_modules)|(public)/,
                use: {
                    loader: 'babel-loader',
                    options: {
                        presets: ['@babel/preset-env']
                    }
                }
            }
        ]
    }
};
```

다음으로 package.json 에서 webpack을 통한 번들링 후 node를 실행할 수 있도록 다음과 같이 수정한다. 

```json
  "scripts": {
    "start": "node ./public/bundle.js",
    "dev": "webpack --config webpack.config.js --mode  development && node ./public/bundle.js",
    "build": "webpack --config webpack.config.js --mode  production"
  },
```

다음 명령어로 테스트 진행한다. 
```bash
$ npm run dev
> backend@1.0.0 dev D:\development\repositories\Gtihub\petra-manual\backend
> webpack --config webpack.config.js --mode  production && node ./dist/bundle.js

Hash: 43be121705cf4046d43b
Version: webpack 4.39.2
Time: 634ms
Built at: 2019-08-27 12:27:22 PM
    Asset      Size  Chunks             Chunk Names
bundle.js  1.22 KiB       0  [emitted]  main
Entrypoint main = bundle.js
[0] external "express" 42 bytes {0} [built]
[1] ./server.js 394 bytes {0} [built]
the server is running
```

## babel-polyfill

babel-polyfill은 ES6 환경을 제공해줍니다.
polyfill이 없는 경우를 예제를 통해 살펴봅니다.

```javascript
ES6
function allAdd() {
    return Array.from(arguments).map(a => a + 2);
```
위 코드는 babel에 의해 다음과 같이 transpile됩니다.

```javascript
ES5
function allAdd() {
    return Array.from(argument).map(function(a) {
        return a + 2;
    });
}
```

Array.from()은 ES6 syntax이므로 지원하지 않는 브라우저가 존재하기 때문에 위 코드는 transpile은 되었지만 모든 브라우저에서 작동하지 않습니다. 이 문제를 해결하기 위해서 polyfill을 사용해야 합니다. polyfill이란 code 조각으로 런타임에 존재하지 않는 native API의 복사본을 말합니다.

babel-polyfill 사용을 위해서 다음과 같이 npm을 설치해줍니다.

```bash
$ npm install --save-dev babel-polyfill
```
그리고 해당 polyfill이 필요한 곳에서 import해줍니다.

```javascript
import 'babel-polyfill';
```
만약 webpack과 함께 사용한다면 entry point에 babel-polyfill을 추가해줍니다.

```javascript
webpack.config.js
module.exports = {
  entry: ['babel-polyfill', '...']
  ...
};
```