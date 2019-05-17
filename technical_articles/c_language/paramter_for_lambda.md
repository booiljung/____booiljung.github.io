[Up](index.md)

# 람다를 파라미터로 받기

## 일반

일반적으로 람다를 파라미터로 받는 경우 `template` 구문을 사용 합니다.

```C++
template<typename T>
void foo(T fn)
{    
    cout << fn(3) << endl;
}

void main()
{
    auto fn = [](int a)
    {
        return a*a;
    }
    foo(fn);
}
```

이렇게 하라고 합니다. 그런데 이 구문에는 함정이 있습니다. 만일 다음 코드는 어떻게 해야 할까요?

```c++
template<typename T>
void foo(T fn)
{    
    cout << fn(3) << endl;
}

void bar()
{
    auto fn = [](int a)
    {
        return a*a;
    }
    foo(fn);    
}

void main()
{
    bar();
}
```

