[Up](index.md)

# IPython에서 실행중인지 체크

Google Colab이나 Jupyter Notebook은 IPython을 사용합니다. Python과 IPython을 오가며 실행하려면 두 환경에서 모두 호환되는 코드를 작성해야 합니다.

먼저 실행 환경이 IPython인지 체크 합니다.

```python
def is_running_on_ipython():
    try:
        __IPYTHON__
        return True
    except NameError:
        return False
```

함수 ``is_running_on_ipython()``는 IPython에서 실행하면 ``True``를, IPython이 아니면 ``False``를 돌려 줍니다. 이 결과를 변수에 담아 둘 수 있습니다.

```python
use_ipython = is_running_on_ipython()
```

