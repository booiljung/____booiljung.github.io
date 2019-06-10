# Dart 커스텀 패키지

##  커스텀 패키지 참조

커스텀 패키지를 만드는 방법은 다른 문서에도 찾아 볼 수 있습니다. Dart 패키지 저장소에 공개되지 않은 패키지를 Dart 프로젝트에서 참조하는 방법은 2가지가 있습니다.

#### 로컬 드라이브에서 참조

`pubspec.yaml`에서 패키지의 `path` 를 추가 합니다.

``` yaml
dependencies:
  패키지이름:
    path: 패키지의경로이름(로컬경로도가능) 예를들어 ../plugin1/
```

#### Git 저장소에서 참조

`pubspec.yaml`에서 패키지의 `git:url:` 을 추가 합니다.

```yaml
dependencies:
  패키지이름:
    git:
      url: git://github.com/booilung/plugin1.git
```

---

## 참조

- [Using packages](https://flutter.dev/docs/development/packages-and-plugins/using-packages)