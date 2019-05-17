[Up](./index.md)

# React Native errno 11 해결

리액트 네이티브는 오픈소스인 만크 다루기 어려운 개발 도구 입니다. 더군다나 페이스북의 도구죠. `npm start`하여도 `errno 11` 오류 메시지를 표시하며 실행되지 않는 경우가 있습니다. 캐시나 모듈들을 삭제하면 해결 되는 경우가 있습니다. 이 방법은 npm 6.2.0, react native 0.56, nodejs 8.11.3에서 동작을 확인하였습니다.

```shell
npm cache clear --force
rm -rf node_modules
npm install
```

또는

```shell
npm run clean
rm -rf node_modules
npm install
```

