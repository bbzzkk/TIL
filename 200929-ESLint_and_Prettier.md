# 200927 TIL - Prettier & ESLint

## Prettier

- 엄격한 코드 스타일에 초점을 두어 탄생한 도구
- 코드 스타일을 자동으로 정리해줌
- 포맷팅과 관련된 부분만 도움이 되므로 코드 품질 규칙과 관련된 ESLint 같은 도구와 함께 사용하는 것이 좋음

### 설치 방법

```bash
$ npm install --save-dev --save-exact prettier
```

--save

- 패키지(plugin)를 ./node_modules 디렉터리에 설치하고 ./package.json 파일의 **dependencies** 항목에 플러그인 정보가 저장됨
- npm@5 부터 생략 가능
- --production 빌드시 해당 **플러그인이 포함됨**
  
--save-dev

- 패키지(plugin)를 ./node_modules 디렉터리에 설치하고 ./package.json 파일의 **devDependencies** 항목에 플러그인 정보가 저장됨
- --production 빌드시 해당 **플러그인이 포함되지 않음**

--save-exact

- npm의 기본 SemVer 연산자(`^`, `~` 같은)를 사용하는 대신 정확히 일치하는 버전의 패키지를 추가
- 버전이 달라지면서 생길 스타일 변화를 막기 위해 이 옵션을 붙이는 것을 추천

### 프로젝트에 Prettier를 적용하는 방법

### 1) .prettierrc 파일 생성

프로젝트 최상위 디렉터리에 **.prettierrc** 파일을 만들고  [공식문서](https://prettier.io/docs/en/options.html)를 참고하여 옵션을 만든다.

예시

```json
{
  "trailingComma": "all",
  "tabWidth": 2,
  "useTabs": false,
  "semi": true,
  "singleQuote": true,
  "printWidth": 80,
  "bracketSpacing": true
}
```

### 2) VSCode Prettier Extension 추가

VSCode에서 Prettier Extension을  설치하고 `ctrl + ,` 을 눌러 환경 설정을 열은 뒤 **Format on Save** 검색 후 체크하면 저장할 때마다 Prettier 적용됨  
또는 `alt + shift + f` 을 눌러 Prettier 적용

#### * javascript 파일에만 Prettier 적용하기

`ctrl + shift + p` 누른 후 open settings (JSON)  입력 후 엔터

아래 내용 추가

```json
//set the default
{
  "editor.formatOnSave": false,
  "[javascript]": {
      //default formatter
      "editor.defaultFormatter": "esbenp.prettier-vscode",
      "editor.formatOnSave": true
}
```

editor.defaultFormatter 옵션은 beautify 등 다른 포맷터들이 있을 때 기본 포맷터로 Prettier를 사용하도록 설정하는 옵션

## ESLint

- Linter란 정적 분석 도구를 말함
- ESLint는 linter 중 하나로 자바스크립트 문법을 확인해주는 도구
- CRA로 만든 프로젝트에는 이미 포함되어 있음

### 설치 방법

```bash
$ npm install eslint --save-dev // CRA 프로젝트는 ESLint 기본적으로 있음
$ npm install eslint-plugin-prettier eslint-config-prettier --save-dev
```

- `eslint-config-prettier`: Prettier와 충돌할 설정들을 비활성화
- `eslint-plugin-prettier`: 코드를 포맷할 때 Prettier를 사용하게 만드는 규칙을 추가

VSCode 에디터에서 바로 확인할 수 있도록 ESLint Extension 설치

### CRA 프로젝트에 eslint-config-airbnb 적용

eslint-config-airbnb를 설치하려면 사전에 설치해야 하는 다른 패키지들이 있음

다음 명령어로 확인 가능

```bash
$ npm info "eslint-config-airbnb@latest" peerDependencies
{
  eslint: '^5.16.0 || ^6.8.0 || ^7.2.0',
  'eslint-plugin-import': '^2.21.2',
  'eslint-plugin-jsx-a11y': '^6.3.0',
  'eslint-plugin-react': '^7.20.0',
  'eslint-plugin-react-hooks': '^4 || ^3 || ^2.3.0 || ^1.7.0'
}
```

CRA v2 기준 위 패키지들이 이미 설치가 되어있으므로 eslint-config-airbnb만 설치하면 됨

`$ npm install eslint-config-airbnb --save-dev`

CRA v2를 통해 프로젝트를 생성한게 아니라면 다음 명령어를 통해 한꺼번에 설치 가능

`$ npx install-peerdeps --dev eslint-config-airbnb`

### 설정 커스터마이징

eslint-config-airbnb 설치 후 .eslintrc.* 파일을 프로젝트의 루트 디렉터리에 생성

javascript, json, yaml 형태로 작성 가능

.eslintrc.json 예시

```json
{
  "extends": ["react-app", "airbnb", "plugin:prettier/recommended"],
  "rules": {
    "prettier/prettier": ["error", {
      "endOfLine": "auto" // CRLF
    }]
  }
}
```

rules에서 원하는 규칙 적용 및 특정 규칙들을 끄거나 경고처리만 해줄 수 있음

### ESLint 검사 무시하기

파일 최상단에 `/* eslint-disable */` 추가

### webpack alias로 인한 import/no-unresolved 오류

다음과 같이 경로에 alias 있으면 eslint 오류가 뜸

```bash
import Something from '@/something';

=> Unable to resolve path to module '@/something'. eslint(import/no-unresolved)
```

### 해결 방법

`npm install eslint-import-resolver-alias --save-dev` 설치 후

 .eslintrc.json 에 다음 내용 추가

 ```json
 {
  "settings": {
    "import/resolver": {
      "alias": {
        "map": [
          ["@", "./src/"] /* webpack-config.js 에 alias 설정한 것 */ 
        ]
      }
    }
  },
  /* ... */
 }
 ```

## Reference

- [Prettier Options](https://prettier.io/docs/en/options.html)
- [27. 리액트 개발 할 때 사용하면 편리한 도구들 - Prettier, ESLint, Snippet](https://react.vlpt.us/basic/27-useful-tools.html)
- [Create React App 에서 ESLint와 Prettier 설정 하기](https://velog.io/@gwangsuda/2019-09-25-1009-%EC%9E%91%EC%84%B1%EB%90%A8-bwk0ylejxj)
- [TOAST UI 정적 분석](https://ui.toast.com/fe-guide/ko_STATIC-ANALYSIS/)
- [리액트 프로젝트에 ESLint와 Prettier 끼얹기](https://velog.io/@velopert/eslint-and-prettier-in-react)
