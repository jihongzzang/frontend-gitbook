## 개발환경 세팅해보기

**`NPM` 시작**

```
$ npm init or $ npm init-y
```

**`.gitignore` 를 통해 깃헙에 관리하지 않을 파일들 설정**

```
$ touch .gitignore
```

**`typescript` 세팅**

```
$ npm i -D typescript
$ npx tsc —init

후에 tsconfig.json 설정

jsx on
```

**`eslint` 세팅**

```
$ npm i -D eslint
$ npx eslint --init

이것저것 묻는데 프로젝트 성격에 맞게 세팅하자.

- How would you like to use ESLint? style
- What type of modules does your project use? esm
- Which framework does your project use? react
- Does your project use TypeScript? Yes
- Where does your code run?  browser
...
- What format do you want your config file to be in? JavaScript
```

**`airbnb lint` 세팅을 위한 추가 패키지 설치**

```
$ npm i -D
eslint-config-airbnb
eslint-plugin-import
eslint-plugin-jsx-a11y
eslint-plugin-import
eslint-plugin-react-hooks
@typescript-eslint/parser
@typescript-eslint/eslint-plugin
```

**`.eslintrc.js`**

```js
주요 변경사항

extends: ['airbng', 'plugin:@typescript-eslint/recommended', 'plugin:react/recommended', 'plugin:react/jsx-runtime'],
Parser: '@typescript-eslint/parser',
parserOptions: {
    ecmaFeatures: {
      jsx: true,
    },
    ecmaVersion: 'latest',
    sourceType: 'module',
  },
plugins: ['react', '@typescript-eslint'],
```

**`.eslintignore` 에 마찬가지로 관리하지 않을 파일 설정**

```
$ touch .eslintignore
```

**`react` , `typescript` 설치**

```
$ npm i react react-dom
$ npm i -D @types/react @types/react-dom
```

**`jest` 설치**

```
$ npm i -D
jest
@types/jest
@swc/core
@swc/jest
jest-environment-jsdom
@testing-library/react
@testing-library/jest-dom
```

**`jest.config.js` 작성**

```js
$ touch jest.config.js

module.exports = {
  testEnvironment: 'jsdom',
  setupFilesAfterEnv: ['@testing-library/jest-dom/extend-expect'],
  transform: {
    '^.+\\.(t|j)sx?$': [
      '@swc/jest',
      {
        jsc: {
          parser: {
            syntax: 'typescript',
            jsx: true,
            decorators: true,
          },
          transform: {
            react: {
              runtime: 'automatic',
            },
          },
        },
      },
    ],
  },
  testPathIgnorePatterns: ['<rootDir>/node_modules/', '<rootDir>/dist/'],
}
```

**`parcel` 설치**

```
$ npm i -D parcel

change package.json scripts
change main => source in package.json
```

```json
"source": "index.html",

"scripts": {
    "start": "parcel --port 8080",
    "build": "parcel build",
    "check": "tsc --noEmit",
    "lint": "eslint --fix --ext .js,.jsx,.ts,.tsx .",
    "test": "jest",
    "coverage": "jest --coverage --coverage-reporters html",
    "watch:test": "jest --watchAll"
  },
```

**`코드테스팅`을 위한 파일 생성**

```
* index.html
* src/main.tsx
* src/App.tsx
* src/App.test.tsx
```
