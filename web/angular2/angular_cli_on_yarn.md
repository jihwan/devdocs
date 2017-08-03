# angular CLI에서 yarn 사용하기

## 1. [yarn 설치](https://yarnpkg.com/lang/en/)

nodeJS 가 설치 되어있어야 함

## 2. [yarn 명령어](https://yarnpkg.com/en/docs/migrating-from-npm)

npm | Yarn |
--- | --- |
npm install | yarn install
npm install --save [package] | yarn add [package]
npm install --save-dev [package] | yarn add [package] [--dev / -D]
npm install --global [package] | yarn global add [package]
npm uninstall --save [package] | yarn remove [package]
npm uninstall --save-dev [package] | yarn remove [package]

## 3. yarn 설치 확인

```bash
yarn --version
```

## 4. yarn angular cli 설치

npm 으로 이미 설치 하였다면 아래와 같이 다시 설치할 필요는 없음

```bash
yarn global add @angular/cli
```

`yarn global list` 명령어를 통해, 설치 된 패키지 확인이 가능하다.

```code
❯ yarn global list
yarn global v0.27.5
warning `yarn global ls` is deprecated. Please use `yarn global list`.
warning package.json: No license field
warning No license field
info "@angular/cli@1.2.5" has binaries:
   - ng
info "create-react-app@1.3.0" has binaries:
   - create-react-app
Done in 3.62s.
```

## 5. angular package manager 를 yarn 으로 변경

```bash
ng get --global packageManager
```

`default` 로 나온다면, npm 이다. 아래와 같이 yarn 으로 변경.

```bash
ng set --global packageManager=yarn
```

사용자 홈 디렉토리에 `.angular-cli.json` 파일이 생성되고, 내용은 아래와 같다.

```json
{
  "packageManager": "yarn"
}
```

## 6. yarn 으로 anglular 프로젝트 생성

```bash
❯ ng new project-yarn
installing ng
  create .editorconfig
  create README.md
  create src/app/app.component.css
  create src/app/app.component.html
  create src/app/app.component.spec.ts
  create src/app/app.component.ts
  create src/app/app.module.ts
  create src/assets/.gitkeep
  create src/environments/environment.prod.ts
  create src/environments/environment.ts
  create src/favicon.ico
  create src/index.html
  create src/main.ts
  create src/polyfills.ts
  create src/styles.css
  create src/test.ts
  create src/tsconfig.app.json
  create src/tsconfig.spec.json
  create src/typings.d.ts
  create .angular-cli.json
  create e2e/app.e2e-spec.ts
  create e2e/app.po.ts
  create e2e/tsconfig.e2e.json
  create .gitignore
  create karma.conf.js
  create package.json
  create protractor.conf.js
  create tsconfig.json
  create tslint.json
Successfully initialized git.
Installing packages for tooling via yarn.
```

> 추후 일반 개발자 들은 소스 레파지토리에서 다운로드 받아 `yarn` 명령어만 실행하면, `package.json` 파일의 정보를 이용하여, 의존 라이브러리가 설치 된다. `ng serve` 명령어를 통해 angular app 을 실행한다.