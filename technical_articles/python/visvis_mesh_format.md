# `visvis` mesh format

`visvis`  패키지는 파이썬에서 3차원 개체를 표현하는데 유용합니다. 하지만 `matplotlib`처럼 많이 사용되지도 않으며 문서나 예제가 부족합니다. `visvis`는 `Mesh`클래스를 제공하는데 역시나 내부 데이터 구조에 대한 문서가 없습니다. 이 문서는 역공학으로 작성되었습니다.

### `_vertices`:

정점들의 집합으로  `numpy.ndarray` 타입니다.  `list` 타입으로 전달하여도 `numpy.ndarray`로변환합니다. 내부 데이터 타입은 `float32`입니다. 크기는 2차원으로 `(정점갯수, 3)`이며, `x, y, z`을 나타냅니다.

### `_faces`:

표면의 집합으로 `numpy.ndarray` 타입니다.`list` 타입으로 전달하여도 `numpy.ndarray`로변환합니다. 내부 데이터 타입은 `uint16, uint32, uint64`중 하나를 입력하면 `uint32`로 변환됩니다. 크기는 1차원으로 `(표면갯수 * _verticesPerFace)`이며, `_vertices`의 인덱스를 나타냅니다.

### `_normals`:

각 정점의 방향의 집합으로  `numpy.ndarray` 타입니다. `list` 타입으로 전달하여도 `numpy.ndarray`로변환합니다. 내부 데이터 타입은 `float32`입니다. 크기는 2차원으로 `(정점갯수, 3)`이며, `x, y, z`을 나타냅니다.

### `_values`:

정확히 알 수 없습니다. 내부 데이터 타입은 `float32`입니다. 크기는 2차원으로 `(정점갯수, 2)입니다.

### `_verticesPerFace`:

표면당 정점의 갯수를 나타냅니다. `_faces` 항목을 참조합니다.

## 참조

- visvis package source code.



 