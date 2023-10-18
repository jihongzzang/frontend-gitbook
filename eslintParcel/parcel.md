# Parcel

- 번들링이란 기본적으로 여러 개로 흩어져 있는 파일들을 압축, 난독화 등을 하여 하나의 파일로 모아주는 역할을 한다.

- 모듈들의 의존성 관계를 파악하여 그룹화시켜주는 작업

Q. 모듈끼리 import하고 export하면 되지 번들링을 해야해?

- 여러 개의 파일을 브라우저에서 로딩한다면 네트워크가 그만큼 소모되어 속도가 저하될 수 있습니다.

- 모듈 간의 변수 충돌 등의 위험성이 존재합니다.

Tree Shaking

- 번들링과정에서 실제로 사용하지 않는 함수들이나 모듈들까지 같이 팩킹되는데, 팩킹 전에 사용하지 않는 함수와 모듈들을 떨쳐내어 최종 결과물을 컴팩트하게 만드는 것.

[Parcel Official Site](https://parceljs.org/)

Parcel 이란 Webpack과 함께 bundler 시장의 점유율을 나눠갖고 있는 모듈 번들러

Parcel 공식사이트에 특징을 보면

- 멀티코어 활용

- 안정적인 캐싱

- 개발 서버 시작 시간 단축

- 이미지 최적화 등등이 많으니 한번 써보자

```json
package.json

{
  "scripts": {
    "start": "parcel index.html"
  }
}
```

```node
설치 및 실행, 세팅

$ npm i -D parcel

$ parcel-reporter-static-files-copy

parcelrc. 작성

{
  "extends": ["@parcel/config-default"],
  "reporters":  ["...", "parcel-reporter-static-files-copy"]
}
```

```node
TIP. 빌드 정적 실행

$ npx parcel build
$ npx servor dist
```
