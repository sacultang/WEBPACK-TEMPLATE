# WEBPACK TEMPLATE
작은 프로젝트보단 규모있는 프로젝트에서 유용한 번들러
```cli
$ npm i -D webpack
$ npm i -D webpack-cli //설치한 패키지 명령어를 위한 패키지
$ npm i -D webpack-dev-server@next //개발 서버를 자동 새로고침 해주는 패키지
```
```json
"scripts": {
    "dev": "webpack-dev-server --mode development ",
    // --mode 옵션 값으로 development
    "build":"webpack --mode production"
    // --mode 옵션 값으로 production
  }
```
### webpack.config.js
- webpack 구성 옵션 작성하는 파일
- nodejs 환경에서 동작
```js
const path = require('path)

module.exports = {
  //진입점 설정
  //webpack은 자바스크립트 파일을 진입점으로 설정
  // == parcel index.html
  entry : './js/main.js',

  //결과물(번들) 반환 설정
  output: {
    //nodejs에서 필요로 하는 절대 경로로 작성
    // const path = require('path')
    // __dirname : node에서 사용가능한 전역 변수, 현재 파일이 있는 경로를 지칭함 

    path:path.resolve(__dirname,'dist'),
    filename : 'main.js',
    clean:true // 기존에 생성된 불필요한 파일을 제거
  }
}
```

## HTML Plugin
진입점인 main.js와 플러그인에 지정한 html을 병합해서
output 경로로 내어줌
```
$ npm i -D html-webpack-plugin
```
```js
webpack.config.js에 작성

const htmlPlugin = require('html-webpack-plugin')  
//번들링 후 결과물의 처리 방식 등 다양한 플러그인들을 설정
plugins : [
    new htmlPlugin({
      template :'./index.html' //root 경로에 있는 index.html
    })
  ]
```

## 정적 파일 연결
```
$ npm i -D copy-webpack-plugin
```

```js
webpack.config.js에 작성

const CopyPlugin = require('copy-webpack-plugin')
plugins : [
  new CopyPlugin({
    patterns:[
      {from:'static} // static 폴더 내용이 dist로 카피 되는 플러그인
    ]
  })
]
```

```js
import '../css/main.css' // root main.js에 작성
webpack 자체는 css 파일을 읽을 수 없음  
단지 main.js와 합쳐와 dist 폴더로 내어주는 역할만 함

추가 패키지 설치 요구
$npm i -D css-loader
$npm i -D style-loader

scss 추가 패키지 
$npm i -D sass-loader
$npm i -D sass

webpack.config.js에 작성

module.exports = {
  module: {
    rules : [
      {
        test:/\.s?css$/, // $ = 줄 끝에 문자와 일치 // \.s?css$ = s가 있을수도 없을수도 있음
        use :[ // 순서 명확하게 작성
          'style-loader', // 순서 3 해석된 css를 html에 적용위한 패키지
          'css-loader', // 순서 2 js가 css 문법 해석하기 위한 패키지
          'sass-loader' // 순서 1
        ]
      }
    ]
  }
}
```
## autoprefixer 패키지
css 공급 업체 접두사를 구현하기 위한 패키지
```js
$ npm i -D postcss
$ npm i -D autoprefixer
$ npm i -D postcss-loader //postcss를 webpack 에서 동작하게 해주는 패키지
```
```js
webpack.config.js에 작성

module.exports= {
  module: {
    rules:[
      {
        test:/\.s?css$/, // \.css$ = 앞에 문자와 일치 // \.s?css$ = s가 있을수도 없을수도 있음
        use:[ //순서 명확하게 작성
          'style-loader', // 순서 4.해석된 내용을 html에 삽입해주는 역할 
          'css-loader', // 순서 3.자바스크립트에서 css를 해석하기 위한 패키지
          'postcss-loader', //순서 2
          'sass-loader' // 순서 1. sass loader
        ]
      }
    ]
  }
}
 ----------------------------------------------------------------------------

.postcssrc.js에 작성

module.exports = {
  plugins: [
    require('autoprefixer')
  ]
}
 ----------------------------------------------------------------------------
 
package.json 에 작성

"browserslist": [
    "> 1%", // 전세계의 점유율이 1% 이상인 브라우저
    "last 2 versions" // 마지막 2버전인 브라우저
  ]
```

## Babel
```
$ npm i -D @babel/core
$ npm i -D @babel/preset-env
$ npm i -D @babel/plugin-transform-runtime
$ npm i -D babel-loader
```
```js
webpack.config.js에 작성

module.exports= {
  module: {
    rules:[
      {
        test:/\.js$/,
        use:[
          'babel-loader' //webpack이 해석하기 위한 패키지
        ]
      }
    ]
  }
}
 ----------------------------------------------------------------------------
 
.babelrc.js에 작성

module.exports = {
  presets :['@babel/preset-env'], // 명시해야되는 자바스크립트 기능을 지원해주는 패키지
  plugins: [
    ['@babel/plugin-transform-runtime'] // 비동기 처리를 위한 패키지
  ]
}
