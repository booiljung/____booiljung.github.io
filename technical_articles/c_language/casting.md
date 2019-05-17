# C/C++에서 캐스트

C/C++에서 캐스트(cast)란 어떤 타입의 데이터를 다른 타입의 변수로 변환 하는 것을 말합니다.  캐스트에는 크게 두가지 부류가 있는데 각각을 묵시적 캐스트(implicit cast)와 명시적 캐스트(explicit cast)라고 합니다.

## 묵시적 캐스트

묵시적 캐스트란 캐스트 연산자를 사용하지 않고 타입을 변환하는 것을 말합니다. 















● static_cast 


  postfix-expression :
       static_cast < type-id > ( expression )

 

캐스트는 크게 묵시적 캐스트(implicit cast)와 명시적 캐스트(explicit cast) 두 가지로 나눌 수 있다.

 

특별히 캐스트 연산자를 사용하지 않고 형변환이 이루어지는 경우를 "묵시적 캐스트" 라고 하는데, 정적 캐스트(static_cast) 의 특성은 이 묵시적 캐스트와 일차적으로 같다고 보면 된다.

 

묵시적 캐스트에는 컴파일 시점에서 무결성을 검사하는데 이때, ‘허용’ 과 ‘컴파일러에 의한 값 변환’ 이라는 두 가지 관점에서 이루어 진다.

 

   int i = 10;

   char c = i;            // 묵시적 캐스트

   char c = (char) i;  // 명시적 캐스트 (c-style)


● dynamic_cast



  postfix-expression :
       dynamic_cast < type-id > ( expression )


상속 계층관계를 가로지르거나 하향시킨 클레스(파생클레스) 타입으로 캐스팅할때 사용된다.
상속 계층 구조를 오갈 때만 사용해야 하며 가상 함수가 없는 타입에는 적용할 수 가 없고, 상수성 제거에도 사용될 수 없다.

======================================================================================================

출처 : www.tipssoft.com

우리가 일반적으로 사용하는 캐스트 연산자가 static_cast라고 보시면
됩니다. 형변환하라고 하면 무조건 변환해주는 연산자 말이죠.... 그리고 일반적인 데이터 형의
형변환은 거의 static_cast를 이용합니다. 하지만 클래스가 상속적인 계층 구조를 가지고 있고
다형성을 이용하는 경우에, 실제로 그 포인터가 가지고 있는 객체가 진짜인지 가짜인지
실행시간에 체크하려면 dynamic_cast를 사용하면 됩니다... 예를들면,

[가정] 클래스 B 는 클래스 C의 부모 클래스이다...

C *p_c = new C();

B *p_sb = static_cast<B *>(p_c);
B *p_db = dynamic_cast<B *>(p_c);

위와 같은 경우에는 다형성의 정의에 의해서 B가 C의 포인터를 가리킬수 있기 때문에 문제가
p_sb나 p_db다 동일한 값을 가지게 됩니다.. 하지만 다음 경우를 보면...

B *p_b = new B();

C *p_sc = static_cast<C *>(p_b);
C *p_dc = dynamic_cast<C *>(p_b);

이렇게 하면 이야기가 틀려집니다. static_cast는 타입체크를 동적으로 하지 않기 때문에
B가 C로 변환되면 문제가 됨에도 불구하고 변환해서 값을 넘겨 주지만 dynamic_cast는
NULL을 반환해 버립니다. 따라서 p_sc에는 변환된 값이 들어있고 p_dc에는 NULL이
들어가게 됩니다. 그리고 p_sc를 사용하면 잘못된 변환이 된것이기 때문에 오류가 나게되죠....

이렇듯 동적인 캐스트는 실행시간에 다형성을 이용하여 모호한 타입캐스팅이 일어났을때,
실제 인스턴스를 조사하여 런타임 오류가 나는걸 방지하는 문법 입니다....

======================================================================================================

출처 편안한 하루 | 마린
원문 http://blog.naver.com/skim01/20014775942


1. static_cast

 

C++ 언어에서 캐스트(cast) 연산은 가장 보편적으로 사용되는 연산 입니다. 그러나 이것 만큼 정확한 의미를 모르고 사용되는 연산 또한 없을 것입니다. Visual C++ 가 제공하는 캐스트는 크게 묵시적 캐스트(implicit cast)와 명시적 캐스트(explicit cast) 두 가지로 나눌 수 있습니다. 묵시적 캐스트는 문법적인 키워드가 존재하지 않습니다. 이에 반해 명시적 캐스트는 문법적인 키워드를 가지고 있는데, 여기에는 (), static_cast, const_cast, reinterpret_cast , dynamic_cast 다섯 가지가 있습니다. 이것에 대해 하나 하나 살펴 보겠습니다.

static_cast

static_cast 는 묵시적 캐스트(implicit cast)와 일차적으로 같습니다. 타입 변환이 필요한 상황에서 특별히 캐스트 연산자를 사용하지 않을 때, 컴파일러는 묵시적 캐스트를 수행 합니다.

다음 코드를 보겠습니다.

int i = 65;

char c = i;

위의 코드는 정수 i 변수의 값을 문자 타입 변수 c 에 대입 합니다. 이때 컴파일러는 묵시적 캐스트(implicit cast)를 수행합니다.

컴파일러의 의한 묵시적 캐스트(implicit cast)는 ‘허용’ 과 ‘컴파일러에 의한 값 변환’ 두 가지로 볼 수 있습니다. ‘허용’ 이라 하는 것은 컴파일러가 오류를 발생 시키는 않고 컴파일 한다는 뜻입니다.

그리고 ‘컴파일러에 의한 값 변환’ 은 값을 변환하기 위하여 컴파일러가 만들어 내는 기계어 코드를 의미 합니다. 그런데 컴파일러가 값을 변환하기 위하여 만들어 내는 기계어 코드는 당연히 컴파일 시점일 될 것입니다.

앞에서 static_cast 가 묵시적 캐스트(implicit cast)와 일차적으로 같다고 했습니다. 즉 static_cast 도 묵시적 캐스트와 같이 ‘허용’ 과 ‘컴파일러에 의한 값 변환’ 이라는 두 가지 관점이 있고, ‘컴파일러에 의한 값 변환’ 을 위하여 컴파일러가 기계어 코드를 만들어 내는 시점이 컴파일 시점 입니다.

그래서 이것을 정적 캐스트(static cast) 라고 부릅니다.

 

그럼 왜 굳이 묵시적 캐스트(implicit cast)도 있는데 정적 캐스트(static cast)를 사용할 까요?

문법적 엄격함 입니다. 프로그래머는 스스로 문법적으로 엄격하게 작성하는 것이 버그 없는 프로그램을 만들기 위해 좋습니다. 제가 야구를 좋아하는데, 묘기를 잘 부리는 야구 선수는 결코 야구를 잘하는 선수가 아닙니다. 모든 것을 쉽고, 그리고 엄격하게 플레이 하는 선수가 진짜 야구 잘하는 선수입니다.

프로그래밍도 이와 같습니다.

그래서 c = i; 보다는 c = static_cast<char>(i); 가 더 바람직합니다.

이제 묵시적 캐스트(implicit cast)와 정적 캐스트(static cast)의 ‘허용’ 관점에 대해 이야기 해 보겠습니다. ‘컴파일러에 의한 값 변환’ 을 위해 컴파일러가 기계어 코드를 생성하기전에 컴파일러는 캐스트 상황이 안전한 지에 대해 판단하고, 안전 하다면 이를 허용하게 됩니다.

 

어떤 변수가 값을 가지고 있다면 그 변수에 어떤 값이 들어 있던 그것은 항상 안전 합니다. 그러나 만약 어떤 변수가 포인터를 가지고, 그 포인터가 가리키는 메모리가 유효하지 않다면 그것은 안전하지 않습니다.

그렇기 때문에 컴파일러가 캐스트를 허용하기 위하여 안전성을 검사하는 기준은 포인터 변수에 한해서 수행합니다. 아래의 코드를 보겠습니다.

 

char c = 'A';

char* pc = &c;

int* pi = pc;

 

위의 코드에서 int* pi = pc; 는 컴파일러에 의해 허용되지 않은 문장으로 오류를 발생시킵니다. pi 가 가리키는 메모리는 실제 문자 변수이고, 만약 pi 를 이용한다면 4 byte 액세스(정수 타입이므로)가 이루어져 다운될 가능성이 있습니다.

이것을 사전에 막고자 컴파일러는 이 문장을 허용하지 않습니다. 결론적으로 묵시적 캐스트(implicit cast)와 정적 캐스트(static cast)는 포인터 타입 캐스트에 대해 동일 타입인 경우에 한하여 허용합니다.

앞에서 이야기 한 것과 같이 대부분의 경우 정적 캐스트(static cast) 와 묵시적 캐스트(implicit cast)는 대부분 동일합니다. 다른 점은 없을까요?

class CBase { public: int a; };

class CDerived : public CBase { public: int b; };

 

위와 같이 클래스가 선언 되어 있을 때, 다음 코드를 보겠습니다.

 

CDerived* pDerived = new CDerived; CBase* pBase;
pBase = pDerived;

 

위의 pBase = pDerived; 이 허용 될까요? 포인터 타입의 캐스트 이고, 두 타입(pBase 와 pDerived)이 다른 타입이므로 허용되지 않아야 하겠지만 허용됩니다.

클래스 포인터에 대해 ‘is a’ 관계가 성립 한다면 묵시적 캐스트(implicit cast)는 허용됩니다. 왜냐하면 ‘is a’ 가 성립하는 두 타입 이라면 논리적으로도 하위 클래스가 상위 타입 데이터를 최소한 포함하기 때문에 100 % 안전하기 때문입니다.

 

물론 정적 캐스트(static cast)도 똑 같습니다.

즉 pBase = static_cast<CBase*>(pDerived); 도 허용됩니다.

계속 묵시적 캐스트(implicit cast) 와 정적 캐스트(static cast)의 같은 점만을 이야기 하고 있죠?

 하나 다른 것이 있습니다.

위의 클래스 선언을 그대로 포함한 상태에서 다음 코드를 보겠습니다.

 

CBase* pBase = new CDerived; CDerived* pDerived;

pDerived = pBase;

 

위의 pDerived = pBase; 이 허용 될까요? 허용 되지 않습니다.

왜냐하면 pDerived 가 실제 가리키고 있는 것은 CBase 객체인데, 타입이 CDerived 으로 되었으므로 이를 이용하여 CDerived 전용 멤버인 b 를 액세스 함으로서 다운될 가능성을 가지고 있기 때문입니다. 그러나 이 경우 정적 캐스트(static cast)는 가능 합니다.

즉 pDerived = static_cast<CDerived*>(pBase); 는 가능 합니다.

이것 하나가 묵시적 캐스트(implicit cast) 와 정적 캐스트(static cast)의 차이점 입니다. 즉 클래스 포인터에 대해 묵시적 캐스트(implicit cast)는 ‘is a’ 관계가 성립 하는 경우만 허용하고, 정적 캐스트(static cast)는 ‘is a’ 뿐만 아니라 상속 관계만 성립한다면 항상 허용합니다.

물론 상속 관계조차 성립하지 않는다면 정적 캐스트(static cast)도 캐스트를 허용하지 않습니다. 다음에는 reinterpret_cast 와 const_cast 에 대해 살펴 보겠습니다. 오타 있으면 지적해 주시면 감사하겠습니다.

2. reinterpret_cast

 

reinterpret_cast 는 연관성이 없는 포인터 타입을 변환하기 위해서 사용 됩니다.

이것은 static_cast 와 비교해 생각해 보는 것이 이해하기 쉽습니다. 앞에서 static_cast 와 묵시적 캐스트(implicit cast)는 연관성 있는 데이터 타입에 대해서 캐스트가 가능하다고 했습니다.

즉 연관성이 없는 데이터에 대해서는 static_cast 와 묵시적 캐스트(implicit cast)를 사용할 수 없습니다.

char c = 'A';

char* pc = &c;

int* pi = pc; // int* pi = static_cast<int*>(pc); (X)

 

위 코드의 경우 int* pi = pc; 은 문법적으로 허용되지 않습니다. pc 와 pi 는 연관성이 없기 때문입니다.

그러나 이 경우 int* pi = reinterpret_cast<int*>(pc); 를 사용 한다면 캐스트(cast)가 될 것입니다. 앞의 예 외에 reinterpret_cast 는 클래스 타입에 대해 클래스가 연관 관계가 없는 경우에도 캐스트(cast)를 할 수 있습니다.

물론 이것이 캐스트(cast) 된다고 바람직 한 것이 아닙니다. 논리적으로 안전하지 않을 수 있기 때문입니다.

int* pi = reinterpret_cast<int*>(pc); 가 문법적으로 허용 된다고 하지만 pi 를 이용하여 메모리에 접근 한다면 프로그램이 다운될 수도 있기 때문입니다.

reinterpret_cast 는 C/C++ 개발자가 일반적으로 사용 했던 () 연산자를 이용한 캐스트와 대부분 같습니다. () 는 모든 경우에 강제 캐스팅을 수행 합니다.

물론 논리적으로 안전하지는 않는 경우가 있을 수 있지만,,,

 

reinterpret_cast 도 강제 캐스팅을 수행합니다.

reinterpret_cast 와 () 의 차이는 reinterpret_cast 는 포인터 타입에 대한 캐스트만 수행할 수 있다는 것과 const 타입과 volite 타입의 포인터 타입에 대해서는 캐스트를 수행할 수 없다는 것입니다.

 

int i = 65;

char c = reinterpret_cast <char>(i);

 

물론 위와 같이 코딩하실 분은 없으시겠지만 위의 코드에서 reinterpret_cast 는 허용되지 않습니다. 저의 프로그래밍 철학으로 볼 때, 저는 이 reinterpret_cast 의 사용을 권장 하지 않습니다.

저는 항상 문법적 허용과 논리적 허용이 일치해야 한다고 생각합니다. 그러나 reinterpret_cast 는 문법적 허용과 논리적 허용이 일치 하지 않는 경우도 있습니다.

물론 () 연산자를 이용한 캐스트도 마찬가지 입니다. 다음에는 dynamic_cast 와 const_cast 에 대해 살펴 보겠습니다. 오타 있으면 지적해 주시면 감사하겠습니다.

3. const_cast

 

 const_cast 는 포인터 타입과 참조형에 대해서만 사용됩니다. 그리고 동일 타입의 포인터와 참조형에 대해서만 사용됩니다.

동일 타입 외에 어떤 연관 관계 타입(is a 타입 포함)에 대해서도 허용되지 않습니다. 그렇다면 const_cast 의 사용 목적은 무엇일까요?

 

const CSample* pSample = new CSample;

CSample* pSample1 = pSample; pSample1->m = 10;

 

위의 코드에서 처럼 상수 목적으로 생성된 CSample 객체가 필요에 의해 변수 목적(pSample1->m = 10)으로 사용되어야 한다면 이것을 일단 변수 객체 포인터로 변환해야 합니다.

그러나 이 상황(CSample* pSample1 = pSample)에서 묵시적 캐스트(implicit cast)나 static_cast 또는 reinterpret_cast 를 사용한다면 컴파일러는 이를 허용하지 않을 것입니다.

이 때 사용할 수 있는 것이 const_cast 입니다.

즉 위의 코드의 경우 다음과 같이 사용해야 합니다.

 

CSample* pSample1 = const_cast<CSample*>(pSample);

 

const_cast 는 보시는 분에 따라 명시적 캐스트 연산자인 () 도 있는데, 왜 const_cast 가 필요한지 의문을 갖는 분도 있을 것입니다. 이것 역시 문법적인 엄격함 때문에 사용됩니다.

저는 사실 const_cast 사용을 권장하지 않습니다. 모든 변수는 선언 시에 목적을 가지고 선언 되어야 하는데, 만약 변수 목적으로 사용될 가능성이 있었다면 애초에 상수로 선언되어서는 안됩니다. 프로그램 버그는 목적을 가지지않고 선언된 변수나 목적에 어긋나게 사용되는 변수에 의해 발생되는 경우가 많습니다.

 

C++ 언어에서 많이 사용되지 않는 한정자 중에 volatile 타입 한정자가 있습니다. const_cast 는 volatile 타입 객체를 일반 타입 포인터나 참조형으로 변환하기 위해서도 사용됩니다.

 

volatile BOOL g_bCalc = FALSE; :

BOOL* p = const_cast<BOOL*>(&g_bCalc);

 

물론 이 경우도 저는 권장하고 싶지 않습니다.


4. dynamic_cast

 

dynamic_cast 는 상당히 능동적인 캐스트 입니다. 다른 캐스트와 달리 dynamic_cast 는 실행 중에 캐스트가 이루어 집니다. 실행 중에 캐스트의 대상이 되는 데이터를 능동적으로 판단하여 실행 코드가 캐스트를 수행합니다.

dynamic_cast 의 문법적 형식은 다음과 같습니다.

 

dynamic_cast <타입>(표현식)

 

표현식은 가상함수와 RTTI(Runtime Type Information)를 포함하는 클래스에 대한 포인터나 참조형, 혹은 객체 일 수 있습니다. 그리고 타입은 역시 가상함수와 RTTI 를 포함하는 클래스의 포인터나 참조형일 수 있습니다.

타입에 한가지 예외적으로 void* 를 명시할 수도 있습니다. 표현식과 타입의 형식만 맞다면 컴파일러는 컴파일을 허용합니다. 표현식과 타입이 연관 관계가 있던 없던 가상함수와 RTTI 를 포함하기만 하면 dynamic_cast 는 무조건 컴파일을 허용합니다. 다른 캐스트와 달리 dynamic_cast 는 컴파일러의 허용 관점 보다는 실행 시 허용관점이 더 중요합니다.

우리도 실행 시 허용에 관심을 두어야 합니다.

RTTI(Runtime Type Information)란 클래스 타입 정보를 가지고 있는 클래스(type_info) 입니다. RTTI 는 컴파일러가 내부적으로 사용하는 정보 클래스로 아래와 같이 정의 되어있습니다.

 

class type_info {

    public: virtual ~type_info();
    
    int operator==(const type_info& rhs) const;
    
    int operator!=(const type_info& rhs) const;
    
    int before(const type_info& rhs) const;
    
    const char* name() const;
    
    const char* raw_name() const;
    
    private: void *_m_data;
    
    char _m_d_name[1];
    
    type_info(const type_info& rhs);
    
    type_info& operator=(const type_info& rhs);

};


type_info 클래스의 _m_data 멤버 변수는 이 글을 쓰는 저도 이해하지 못하며 NULL 로 설정되어 있습니다. 다음 _m_d_name 멤버 변수는 실제 char 하나로 구성되어 있지만 클래스 이름을 가지고 있는 배열로서 type_info 가 컴파일러에 의해 만들어 질 때 클래스 이름을 문자열로 갖도록 확장되어 생성 됩니다.

RTTI(Runtime Type Information) 를 만들기 위해 우리가 해야 할 일은 /GR 컴파일 옵션을 지정 하기만 하면 됩니다.

 

 Visual C++ 6.0 같은 경우 [Project]-[Setting] 메뉴를 선택하여 [Project Settings] 대화상자를 나타낸 후, [C/C++] 탭의 [C++ Language] 카테고리 컴파일 옵션을 선택하고, [Enable Run-Time Type Information (RTTI)] 를 선택하면 됩니다. 앞의 옵션이 선택된 경우 컴파일러는 RTTI 를 컴파일 하는 모든 클래스에 대해 생성합니다.

그러나 실제 각 클래스가 RTTI 를 사용하기 위해서는 각 클래스는 최소한 하나 이상의 가상함수를 가져야 합니다. 만약 클래스가 가상함수를 하나도 가지지 않는다면 해당 클래스는 타입으로는 RTTI 를 이용할 수 있지만 객체로는 RTTI 를 이용할 수 없습니다.

우리가 지금 알아보고 있는 dynamic_cast 도 객체의 RTTI 를 이용한 동적(실행시) 캐스트 이므로 dynamic_cast 를 위해서도 해당 클래스는 최소한 하나 이상의 가상함수를 가져야 합니다. 컴파일러는 RTTI 와 객체를 연결하기 위해서 가상함수 포인터 테이블을 이용합니다. 이것은 변칙입니다.

언제부터 RTTI 가 C++ 언어에 도입 되었는지 알 수 없지만, 원래 C++ 언어의 가상함수 포인터 테이블은 순수한 가상함수에 대한 함수 포인터 배열입니다. RTTI 와 객체의 연결을 위해 C++ 언어는 가상함수 포인터 테이블 앞에 4 byte 를 만들고 이것을 RTTI 와의 연결 고리로 사용하고 있습니다.

class CBase {

public: int m_base;

virtual void fun1 (void);

virtual void fun2 (void);

};

 

void CBase::fun1 (void) {

cout << "Base fun1" << endl;

}

 

void CBase::fun2 (void) {

cout << "Base fun2" << endl;

}

 

class CDerived : public CBase {

 public: int m_derived; virtual void fun3 (void);

};

 

int main(int argc, char* argv[]) {

CBase* p1 = new CBase;

CDerived* p2 = new CDerived;

CDerived* p3 = dynamic_cast<CDerived*>(p1);

CBase* p4 = dynamic_cast<CBase*>(p2);

return 0;

}

 

RTTI 가 생성된 경우 객체와 가상함수 포인터 테이블 모양이 조금 복잡해 집니다.

객체(p1, p2)는 여전히 가상함수 포인터 테이블을 가리키고 있지만 가상함수 포인터 테이블 앞에 숨겨진 4 byte 가 컴파일러에 의해 추가로 만들어 집니다.

그리고 바로 이 숨겨진 4 byte 가 클래스의 RTTI 포인터 테이블과 객체의 연결 고리로 사용됩니다.

 

RTTI 포인터 테이블은 객체 자신의 RTTI 나 상위 클래스의 RTTI 를 가리키는 또 다른 연결 고리입니다. 여러 단계에 걸쳐 상속된 경우 실제 모양은 위의 그림보다 복잡해 집니다. 위의 그림은 원리를 설명하기 위하여 간단히 그려본 것입니다.

프로그램이 dynamic_cast 를 이용하여 캐스트를 한 경우 실행 코드는 dynamic_cast 의 표현식에 기술된 객체를 이용하여 RTTI 포인터 테이블을 검색하고, 만약 RTTI 포인터 테이블 상에 일치하는 RTTI 가 존재 한다면 표현식에 기술된 객체의 타입을 변환하여 반환하고, RTTI 포인터 테이블 상에 일치하는 RTTI 가 존재 하지 않는다면 dynamic_cast 는 NULL(0) 을 반환할 것 입니다.

앞의 코드의 경우 p3 는 NULL 을 반환하고 p4 는 유효한 값을 반환할 것입니다.

이와 같이 dynamic_cast 는 다른 캐스트와 달리 표현식의 실제 객체를 추적하여 캐스트를 수행하므로 상당히 능동적이 캐스트라 할 수 있습니다. 잘 설계된 클래스를 바탕으로 어플리케이션이 개발 될 때 어플리케이션 상의 클래스 포인터 타입은 대부분 상위의 몇 가지 타입으로 운영됩니다. 그러나 간혹 실제 객체의 클래스 타입에 따라 어떤 논리를 처리해야 하는 경우가 생기는데, 이때 우리는 dynamic_cast 를 다음과 같이 이용할 수 있습니다.

 

if (dynamic_cast<CDerived*>(p)) { : : }

 

위의 if 블록은 p 가 CDerived 타입 인 경우만 수행될 것입니다. dynamic_cast 만큼 일반적이지는 않지만 RTTI 가 사용되는 경우가 한가지 더 있습니다.

바로 typeid 연산자가 바로 그것입니다. typeid(표현식) typeid 는 표현식이 RTTI 를 가지고 있다면 그것의 RTTI 클래스(type_info) 참조형을 반환합니다. RTTI 클래스(type_info) 참조형을 이용하여 클래스 이름이나 비교를 수행할 수 있습니다.

 typeid 를 사용할 때 주의할 것은 클래스 포인터가 전달된 경우 클래스 포인터 타입에 대해 별도의 RTTI 클래스 참조형이 반환된다는 것이다.

 

int main(int argc, char* argv[]) {

CBase* p1 = new CBase;

CDerived* p2 = new CDerived;

const type_info& t = typeid (p1);

const type_info& t1= typeid(*p1);

return 0;

}

 

위의 typeid(p1) 과 typeid(*p1) 은 서로 다른 type_info 가 반환됩니다. typeid(*p1) 은 dynamic_cast 와 같이 p1 객체를 추적하여 RTTI 를 알아내고 그것의 type_info 참조형으로 반환합니다. 그러나 typeid(p1) 은 p1 포인터 타입(CBase*)에 대해 RTTI 가 반환됩니다.

이때 반환되는 RTTI 는 객체와 어떠한 연결 고리도 가지지 않는 RTTI 입니다.


출처: https://javawoo.tistory.com/40 []