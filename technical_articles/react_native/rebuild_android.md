[Up](./index.md)

# React Native에서 안드로이드 rebuild 다시 빌드 하기.

리액트 네이트브 빌드 중이나 실행 중에 중단되면 이후 잘 연결 되지 않습니다. 다시 빌드해야 할 경우도 있는데요. 다음 순서로 다시 빌드 합나디.

```shell
./android/gradlew clean
```

심각한 문제가 있으면 오류가 발생하고 진행되지 않을 것입니다. 그렇다면 다음 절차를 따릅니다.

```shell
react-native upgrade
```
파일을 업그레이드 할것이냐고 물을 것입니다. 질문에 y 또는 n로 답합니다. y를 선택하면 파일을 init 상태로 회복합니다.

그리고

```shell
react-native link
react-native run-android
```

를 하면 빌드가 시작 됩니다.
