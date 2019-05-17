[Up](./index.md)

# React Native 에서 8081 포트를 리슨 할 수 없다는 오류

`npm start`나 `react-native start`를 하면 프로그램을 전송해야 하는데, `ERROR  Metro Bundler can't listen on port 8081` 오류를 발생하며 진행이 되지 않습니다.

이 포트를 점유하고 있는 좀비를 찾습니다.

```
sudo lsof -i :8081
```

이 포트를 점유하고 있는 node 프로세스가 보일 것입니다.

```
kill -9 PID
```

를 하면 좀비 프로세스를 제거 할 수 있습니다. 