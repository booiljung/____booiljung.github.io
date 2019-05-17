[Up](index.md)

# Torch 설치시 CUDA 관련 half-precision 오류 해결.

이 글은 2017년 3월 18일 현재 유효합니다.

[Torch](http://torch.ch/)를 설치하려면 [가이드](http://torch.ch/docs/getting-started.html#_)에 따라 설치를 하면 되는데 다음과 같은 오류가 발생합니다.

```sh
/extra/cutorch/lib/THC/generic/THCTensorMath.cu(393): error: more than one operator "==" matches these operands:
            function "operator==(const half &, const half &)"
            function "operator==(half, half)"
            operand types are: half == half
/extra/cutorch/lib/THC/generic/THCTensorMath.cu(414): error: more than one operator "==" matches these operands:
            function "operator==(const half &, const half &)"
            function "operator==(half, half)"
            operand types are: half == half
2 errors detected in the compilation of "/tmp/tmpxft_00001a34_00000000-6_THCTensorMath.cpp1.ii".
CMake Error at THC_generated_THCTensorMath.cu.o.cmake:267 (message):
  Error generating file
 /extra/cutorch/build/lib/THC/CMakeFiles/THC.dir//./THC_generated_THCTensorMath.cu.o
lib/THC/CMakeFiles/THC.dir/build.make:112: 'lib/THC/CMakeFiles/THC.dir/THC_generated_THCTensorMath.cu.o' 타겟에 대한 명령이 실패했습니다
make[2]: *** [lib/THC/CMakeFiles/THC.dir/THC_generated_THCTensorMath.cu.o] 오류 1
make[2]: *** 끝나지 않은 작업을 기다리고 있습니다....
CMakeFiles/Makefile2:172: 'lib/THC/CMakeFiles/THC.dir/all' 타겟에 대한 명령이 실패했습니다
make[1]: *** [lib/THC/CMakeFiles/THC.dir/all] 오류 2
Makefile:127: 'all' 타겟에 대한 명령이 실패했습니다
make: *** [all] 오류 2
Error: Build error: Failed building.
```

cuda가 7.5 버전부터 half-precision을 지원하는데 cutorch의 코드가 cuda 7.5용으로 수정이 안되었나 싶었고, torch 개발자들은 cuda 7.5 이상을 사용하지 않나 생각이 들어 cutorch 수정을 시도했습니다만 갈수록 미궁이었습니다. 오류는 매개변수를 점검하는 ``assert``에 해당하는 ``THArgCheck``에서 발생했습니다. 처음부터 오류 메시지를 구글에서 찾았다면 빠른 시간에 해결 할 수 있었을텐데요. toch는 아직 half-precision을 지원하지 않습니다. 설치시 CLI에서 다음과 같이 입력하면 오류는 경고로 대치됩니다.

```sh
$./clean.sh
$ TORCH_NVCC_FLAGS="-DCUDA_NO_HALF_OPERATORS" ./install.sh
```

clean.sh파일은 install.sh를 실행하면 만들어 집니다. install.sh를 한번도 실행하지 않았다면 clean.sh 파일은 없으므로 첫 실행에서는 다음처럼 clean.sh를 입력하지 않습니다.

```sh
$ TORCH_NVCC_FLAGS="-DCUDA_NO_HALF_OPERATORS" ./install.sh
```

아래처럼 따로 입력하면 오류는 해결 되지 않습니다.

```sh
$ ./clean.sh
$ export TORCH_NVCC_FLAGS="-DCUDA_NO_HALF_OPERATORS"
$ ./install.sh
```

## 참조

[cuda 9.0 "error: more than one operator "==" matches these operands" #797](https://github.com/torch/cutorch/issues/797)


