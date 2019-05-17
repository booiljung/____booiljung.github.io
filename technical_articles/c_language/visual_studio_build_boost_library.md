# Visual Studio: Boost Library 빌드.

Visual Studio에서 Boost가 필요한 경우 사전에 빌드 된 Boost 라이브러리를 사용할 수 있습니다. 그러나 사전 빌드된 Boost 라이브러리가 없다면 직접 빌드해야 합니다.

Visual Studio 용 Boost를 빌드하기 위해서는 다음 단계에 따라 작업 합니다.

Boost 소스를 [여기](https://www.boost.org/users/download/)에서 다운로드하고 압축을 풉니다.

Visual Studio 용 개발자 명령 프롬프트를 엽니 다. Boost가 압축 해제 된 디렉토리로 이동합니다.

다음 명령을 사용하여 Boost.Build 도구 (b2.exe)를 빌드합니다.

```
bootstrap.bat
```

다음을 사용하여 부스트를 빌드합니다.

```
 b2 toolset=msvc-14.0 --build-type=complete --abbreviate-paths architecture=x86 address-model=64 install -j4
```

`toolset`: 사용할 Visual C++ 컴파일러를 지정하려면이 옵션을 사용하십시오. Visual Studio 2015의 경우 `msvc-14.0`입니다. 다른 버전의 Visual Studio의 경우 이 게시물을 참조하십시오.

`architecture`: 이것은 프로세서 아키텍처입니다. 이 `x86`을 `32` 비트 및 `64` 비트 빌드 모두에 사용하십시오.

`address-model` : `32` 비트 또는 `64` 비트 라이브러리를 구축할지 여부를 지정하려면 이 옵션을 사용하십시오.

`-j` : 병렬 컴파일에 사용할 코어 수를 지정합니다.

​    이 컴파일 작업은 약 30 분이 소요되었으며 완료되면 Boost 헤더 파일과 빌드 된 라이브러리가 `C:\ Boost`에 덤프됩니다.

## 참조

- [Code Yarns: How to build Boost using Visual Studio](https://codeyarns.com/2014/06/06/how-to-build-boost-using-visual-studio/)