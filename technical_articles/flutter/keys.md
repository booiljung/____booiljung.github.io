원문: [Flutter Keys](https://medium.com/flutter/keys-what-are-they-good-for-13cb51742e7d)

# Flutter Keys

키 매개 변수는 기본적으로 모든 위젯 생성자에서 찾을 수 있지만 사용 빈도는 낮습니다. 위젯이 위젯 트리에서 이동할 때 키는 상태를 유지합니다. 실제로 이는 콜렉션을 수정할 때 사용자의 스크롤 위치를 유지하거나 상태를 유지하는 데 유용 할 수 있음을 의미합니다.

다음 기사는 다음 비디오에서 채택되었습니다.

<iframe src="https://cdn.embedly.com/widgets/media.html?src=https%3A%2F%2Fwww.youtube.com%2Fembed%2Fkn0EOS-ZiIc%3Ffeature%3Doembed&amp;url=http%3A%2F%2Fwww.youtube.com%2Fwatch%3Fv%3Dkn0EOS-ZiIc&amp;image=https%3A%2F%2Fi.ytimg.com%2Fvi%2Fkn0EOS-ZiIc%2Fhqdefault.jpg&amp;key=a19fcc184b9711e1b4764040d3dc5c07&amp;type=text%2Fhtml&amp;schema=youtube" title="When to Use Keys - Flutter Widgets 101 Ep. 4" class="fv p q fu ac" width="854" height="480" frameborder="0"></iframe>

읽기보다는 듣기 /보기를 선호한다면, 그 비디오는 모든 동일한 내용을 포함해야합니다.

------

# The Inside Scoop on Keys

Most  of the time… you don’t need keys! Generally, there’s no harm adding  them, but it’s also unnecessary and just takes up unnecessary space,  just like the new keyword, or declaring types on both the right side and  left side of a new variable (I’m looking at you, `Map<Foo, Bar> aMap = Map<Foo, Bar>()`). But, if you find yourself adding, removing, or reordering a collection of widgets of the same type that *hold some state*, using keys is likely in your future!

대부분의 경우 ... 키가 필요하지 않습니다! 일반적으로 그것들을 추가해도 아무런 해가 없지만 불필요하며 새로운 키워드와 마찬가지로 불필요한 공간을 차지하거나 새로운 변수의 오른쪽과 왼쪽 모두에 유형을 선언합니다 (저는 여러분을 보고 있습니다, `Map<Foo, Bar> aMap = Map<Foo, Bar> ()`). 하지만, 상태를 유지하는 같은 유형의 위젯 모음을 추가, 제거 또는 재정렬하는 경우 키를 사용하는 것이 미래에 가능할 것입니다!

> 일부 상태를 보유하는 동일한 유형의 위젯 모음을 추가, 제거 또는 재정렬하는 경우 키를 사용하여 미래에 가능성이 높습니다.

왜 위젯 모음을 수정할 때 키가 필요한지 설명하기 위해 단추를 누를 때 장소를 바꿀 수 있는 두 가지 색상이있는 위젯이있는 매우 간단한 응용 프로그램을 작성했습니다.

![img](https://miro.medium.com/max/320/1*edgczyvaQRgGRy8yhht0QQ.gif)

stateless 버전의 앱에는 무작위로 생성 된 색상이 있는 stateless 타일 두 개와 '타일'의 위치를 저장하는 `PositionedTiles`라는 `StatefulWidget`이 있습니다. 아래쪽에 있는 `FloatingActionButton`을 탭하면 목록에서의 위치가 바뀌게됩니다.

```dart
List<Widget> tiles = [
   StatefulColorfulTile(),
   StatefulColorfulTile(),
];

...
class StatefulColorfulTile extends StatefulWidget {
 @override
 ColorfulTileState createState() => ColorfulTileState();
}

class ColorfulTileState extends State<ColorfulTile> {
 Color myColor;

 @override
 void initState() {
   super.initState();
   myColor = UniqueColorGenerator.getColor();
 }

 @override
 Widget build(BuildContext context) {
   return Container(
       color: myColor,
       child: Padding(
         padding: EdgeInsets.all(70.0),
       ));
 }
}
```

그러나 상태 저장이 아닌 `ColorfulTiles` *stateful*을 만들어 상태에 저장할 경우 버튼을 누르면 아무 일도 일어나지 않는 것처럼 보입니다.

![img](https://miro.medium.com/max/320/1*T7TBQx9DhaQ16gbX68XxVw.gif)

위의 코드는 사용자가 "swap"버튼을 눌렀을 때 색상이 서로 바뀌지 않는다는 점에서 *buggy*입니다. 이 문제를 해결하기 위해 Stateful 위젯에 키 매개 변수를 추가 한 다음 위젯이 원하는대로 스왑합니다.

![img](https://miro.medium.com/max/320/1*3XbdhaQ9_lPfILdViiipeQ.gif)

```dart
List<Widget> tiles = [
  StatefulColorfulTile(key: UniqueKey()), // Keys added here
  StatefulColorfulTile(key: UniqueKey()),
];

...
class StatefulColorfulTile extends StatefulWidget {
  StatefulColorfulTile({Key key}) : super(key: key);  // NEW CONSTRUCTOR
 
  @override
  ColorfulTileState createState() => ColorfulTileState();
}

class ColorfulTileState extends State<ColorfulTile> {
  Color myColor;

  @override
  void initState() {
    super.initState();
    myColor = UniqueColorGenerator.getColor();
  }

  @override
  Widget build(BuildContext context) {
    return Container(
        color: myColor,
        child: Padding(
          padding: EdgeInsets.all(70.0),
        ));
  }
}
```

그러나 이것은 수정중인 하위 트리에 *stateful* 위젯이 있는 경우에만 필요합니다. 컬렉션의 전체 위젯 하위 트리가  stateless 이면 키가 필요하지 않습니다.

그게 다야! Flutter에서 키를 사용하려면 기술적으로 모두 알아야 합니다. 그러나 당신이 그것의 모든 근본적인 이유를 이해하고 싶다면 ....

------

# The Nitty Gritty of Why Keys are Sometimes Needed

너 아직 여기있어? 그런 다음, 근처에 모여 위젯과 Element Tree의 진정한 본성을 배우고 Flutter 소서러가 되십시오! Mwahahaha! 하하! HA 하! 에헴, 실례합니다.

아는 바와 같이, 모든 위젯에 대해 Flutter는 해당 요소를 만듭니다. Flutter는 위젯 트리를 생성하는 것과 마찬가지로`Element` 트리를 만듭니다. `ElementTree`는 매우 간단합니다. 각 위젯의 type에 대한 정보와 children 요소에 대한 참조 만 가지고 있습니다. Flutter 앱의 골격처럼 `ElementTree`를 생각해보십시오. 앱의 구조를 보여 주지만 모든 추가 정보는 원래 위젯을 참조하여 조회 할 수 있습니다.

위의 예제에서`Row` 위젯은 기본적으로 각 자식에 대해 정렬 된 슬롯 세트를 보유합니다. `Row`에서 Tile 위젯의 순서를 바꿀 때, Flutter는 `ElementTree`를 거쳐 골격 구조가 같은지 확인합니다.

![img](https://miro.medium.com/max/600/1*sHDIVXBu9RpJYN9Zdn8iBw.gif)

`RowElement`로 시작하여 자식으로 이동합니다. `ElementTree`는 새로운 위젯이 이전 위젯과 동일 유형  및 키인지 확인하고, 일치하면 새로운 위젯에 대한 참조를 업데이트합니다. Stateless 버전에서는 위젯에 키가 없으므로 Flutter가 유형을 확인합니다. (한 번에 많은 정보가 있는것 같으면 위의 애니메이션 다이어그램을보십시오.)

stateful 위젯의 기본 `ElementTree` 구조는 약간 다르게 보입니다. 이전과 같은 위젯과 요소가 있지만 상태 객체 쌍이 있으며 색상 정보는 위젯 자체가 아닌 해당 위치에 저장됩니다.

![img](https://miro.medium.com/max/600/1*noTkKudlGuaAkiGaubEcNA.gif)

상태가 없는 `Tile` (키가 없는 경우)에서 두 위젯의 순서를 바꾸면 Flutter는 ElementTree를 탐색하고 RowWidget의 유형을 확인한 다음 참조를 업데이트합니다. 그런 다음 `TileElement`는 해당 위젯이 동일한 type (`TileWidget`)인지 확인하여 참조를 업데이트합니다. 두 번째 child에게도 똑같은 일이 일어납니다. Flutter는 `ElementTree`와 해당 state를 사용하여 장치에 실제 표시 할 내용을 결정하므로 우리의 관점에서 볼 때 위젯이 올바르게 스왑되지 않은 것처럼 보입니다!

![img](https://miro.medium.com/max/600/1*7n-u4yexzRZDEtNvbrsG1g.gif)

Stateful `Tiles`가 있는 고친 버전에서는 위젯에 키 속성을 추가했습니다. 이제 우리가 위젯을 바꾸는 경우 `Row` 위젯은 이전처럼 유사하지만, 타일 요소의 키가 해당 타일 위젯의 키와 일치하지 않습니다. 이렇게하면 Flutter가 Elements를 비활성화하고 ElementTree에서 Tile Elements에 대한 참조를 제거합니다. 일치하지 않는 첫 번째 Element부터 시작합니다.

![img](https://miro.medium.com/max/600/1*AcBxC8IF_irZpFARt-Nqyw.gif)

그런 다음 Flutter는 일치하는 키가 있는 element에 대해 `Row`의 일치하지 않는 하위 항목을 살펴 봅니다. 일치하는 항목을 찾고 해당 위젯에 대한 참조를 업데이트합니다. 플러터는 두 번째 child에게 똑같은 일을 합니다. 이제 Flutter는 버튼을 누르면 위젯이 장소를 교체하고 색상을 업데이트하여 예상 한 것을 표시합니다.

요약하면 키는 컬렉션에서 상태 저장 위젯의 order 또는 number를 수정하는 경우에 유용합니다. 설명을 위해 이 예제에서는 상태로 색상을 저장했습니다. 종종 state는 훨씬 더 미묘합니다. 애니메이션 재생, 사용자가 입력 한 데이터 표시 및 스크롤 위치는 모두 상태를 포함합니다.

------

# Where do I put ‘em?

짧은 대답 : 앱에 키를 추가해야 하는 경우 보존해야 하는 상태로 위젯 하위 트리의 **상단**에 키를 추가해야 합니다.

내가 보았던 흔한 실수는 사람들이 처음으로 상태 저장 위젯에 키를 넣을 필요가 있다고 생각하지만 용(dragon)이 있을뿐입니다. 나를 믿지 않습니까? 우리가 어떤 종류의 문제를 일으킬 수 있는지 보여주기 위해 나는 `colorfulTile` 위젯을 padding 위젯으로 감쌌지만 타일 위에 키를 두었습니다.

```dart
void main() => runApp(new MaterialApp(home: PositionedTiles()));

class PositionedTiles extends StatefulWidget {
  @override
  State<StatefulWidget> createState() => PositionedTilesState();
}

class PositionedTilesState extends State<PositionedTiles> {
  // Stateful tiles now wrapped in padding (a stateless widget) to increase height 
  // of widget tree and show why keys are needed at the Padding level.
  List<Widget> tiles = [
    Padding(
      padding: const EdgeInsets.all(8.0),
      child: StatefulColorfulTile(key: UniqueKey()),
    ),
    Padding(
      padding: const EdgeInsets.all(8.0),
      child: StatefulColorfulTile(key: UniqueKey()),
    ),
  ];

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      body: Row(children: tiles),
      floatingActionButton: FloatingActionButton(
          child: Icon(Icons.sentiment_very_satisfied), onPressed: swapTiles),
    );
  }

  swapTiles() {
    setState(() {
      tiles.insert(1, tiles.removeAt(0));
    });
  }
}

class StatefulColorfulTile extends StatefulWidget {
  StatefulColorfulTile({Key key}) : super(key: key);
 
  @override
  ColorfulTileState createState() => ColorfulTileState();
}

class ColorfulTileState extends State<ColorfulTile> {
  Color myColor;

  @override
  void initState() {
    super.initState();
    myColor = UniqueColorGenerator.getColor();
  }

  @override
  Widget build(BuildContext context) {
    return Container(
        color: myColor,
        child: Padding(
          padding: EdgeInsets.all(70.0),
        ));
  }
}
```

이제 버튼을 클릭하면 타일이 완전히 다른 임의의 색상으로 바뀝니다.

![img](https://miro.medium.com/max/600/1*uC-SRZpRkOZCEr_rGisF9g.gif)

추가 된 패딩 위젯과 함께 WidgetTree와 ElementTree가 어떤 모습인지 보여줍니다.

![img](https://miro.medium.com/max/700/1*0NNY0KOBQGCWvdrWvorOQA.jpeg)

Flutter의 element-to-widget-matching 알고리즘은 children들의 위치를 교환 할 때 한 번에 한 레벨의 트리를 봅니다. 다이어그램은 다이어그램에서 children들의 children들을 회색으로 만들어서 한 번에 한 수준에 집중할 수 있습니다. Padding 요소가 있는 첫 번째 수준의 children에게는 모든 것이 올바르게 일치합니다.

![img](https://miro.medium.com/max/600/1*vD86ZINBC-1Ctx9kudEGaw.gif)

두 번째 레벨에서 Flutter는 타일 요소의 키가 위젯의 키와 일치하지 않으므로 타일 요소를 비활성화하여 해당 연결을 삭제합니다. 이 예제에서 사용하는 키는 `LocalKeys`입니다. 즉 위젯을 element와 일치 시키면 Flutter는 트리의 특정 수준에서 주요 일치 항목을 찾습니다.

해당 키 값으로 해당 레벨의 타일 element를 찾을 수 없으므로 새로운 element를 만들고 새 상태를 초기화합니다.이 경우에는 위젯을 주황색으로 만듭니다!

![img](https://miro.medium.com/max/600/1*JI1Ex87QRMTCJwBWmbNI5A.gif)

패딩 위젯 수준에서 키를 추가하는 경우 :

```dart
void main() => runApp(new MaterialApp(home: PositionedTiles()));

class PositionedTiles extends StatefulWidget {
  @override
  State<StatefulWidget> createState() => PositionedTilesState();
}

class PositionedTilesState extends State<PositionedTiles> {
  List<Widget> tiles = [
    Padding(
      // Place the keys at the *top* of the tree of the items in the collection.
      key: UniqueKey(), 
      padding: const EdgeInsets.all(8.0),
      child: StatefulColorfulTile(),
    ),
    Padding(
      key: UniqueKey(),
      padding: const EdgeInsets.all(8.0),
      child: StatefulColorfulTile(),
    ),
  ];

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      body: Row(children: tiles),
      floatingActionButton: FloatingActionButton(
          child: Icon(Icons.sentiment_very_satisfied), onPressed: swapTiles),
    );
  }

  swapTiles() {
    setState(() {
      tiles.insert(1, tiles.removeAt(0));
    });
  }
}

class StatefulColorfulTile extends StatefulWidget {
  StatefulColorfulTile({Key key}) : super(key: key);
 
  @override
  ColorfulTileState createState() => ColorfulTileState();
}

class ColorfulTileState extends State<ColorfulTile> {
  Color myColor;

  @override
  void initState() {
    super.initState();
    myColor = UniqueColorGenerator.getColor();
  }

  @override
  Widget build(BuildContext context) {
    return Container(
        color: myColor,
        child: Padding(
          padding: EdgeInsets.all(70.0),
        ));
  }
}
```

Flutter는 이전 예제에서와 같이 문제를 발견하고 연결을 올바르게 업데이트 합니다. 우주에서 질서가 회복됩니다.

![img](https://miro.medium.com/max/600/1*FkCvw_LCfQ2x02wj7cmrpA.gif)

# What kind of Key should I use?

Flutter API의 훌륭한 공급 업체는 선택할 수 있는 다양한 Key 클래스를 제공합니다. 사용해야 하는 키 유형은 키를 필요로하는 항목의 구별되는 특성에 따라 다릅니다. 위젯에 저장하고 있는 정보를 살펴보십시오. 여기서는 네 가지 유형의 키, `ValueKey` , `ObjectKey`, `UniqueKey` 및 `UniqueKey`에 대해 설명하겠습니다.

다음 할 일 목록 app¹을 고려하십시오. 우선 순위에 따라 TODO 목록의 항목을 재정렬 한 다음 완료되면 제거 할 수 있습니다.

![img](https://miro.medium.com/max/320/1*wHJZnNPhMkePFEw1ihrbEA.gif)

이 시나리오에서는 To-do 항목의 텍스트가 일정하고 고유 할 것으로 예상 할 수 있습니다. 이 경우, 아마도 `ValueKey`에 대한 좋은 후보가 될 것입니다. 텍스트는 `value`입니다.

```dart
return TodoItem(
  key: ValueKey(todo.task),
  todo: todo,
  onDismissed: (direction) => _removeTodo(context, todo),
);
```

다른 시나리오에서는 아마도 각 사용자에 대한 정보가 나열된 주소록 앱을 사용했을 것입니다. 이 경우 각 하위 위젯은 더 복잡한 데이터 조합을 저장합니다. 이름이나 생일과 같은 개별 필드는 다른 항목과 같을 수 있지만 조합은 고유합니다. 이 시나리오에서는 아마도 `ObjectKey`가 가장 적절할 것입니다.

![img](https://miro.medium.com/max/700/1*vZV_QjG1GEg7nJILMbhEkA.png)

콜렉션에 동일한 값을 가진 여러 위젯을 가지고 있거나 각 위젯이 all 다른 것들과 확실히 구별되도록하려면 `UniqueKey `를 사용할 수 있습니다. 우리가 우리 타일에 저장하고있는 다른 상수 데이터를 가지고 있지 않았고 위젯을 만들 때 색상이 무엇인지 알지 못하기 때문에 예제 색상 전환 애플릿에서 `UniqueKey`를 사용했습니다. 그래도 `UniqueKey`와 조심하세요! `build` 메쏘드 안에서 새로운 `UniqueKey`를 생성하면, 그 키를 사용하는 위젯은 빌드 메소드가 다시 실행될 때마다 다른 `unique` 키를 갖게됩니다. 이렇게 하면 키 사용의 이점을 없앨 수 있습니다!

마찬가지로, 사용하지 않으려는 것은 키에 임의의 숫자를 사용하는 것입니다. 위젯이 빌드 될 때마다 새로운 난수가 생성되고 프레임 사이의 일관성을 잃게 됩니다. 그렇다면 처음부터 키를 사용하지 않았을 수도 있습니다!

`PageStorageKey`는 사용자가 스크롤 위치를 저장하는 특수 키로서, 앱이 나중에 그것을 보존 할 수 있도록합니다.

![img](https://miro.medium.com/freeze/max/30/1*KgQeq1LDIPVuE2dwNzZbRQ.gif?q=20)

![img](https://miro.medium.com/max/320/1*KgQeq1LDIPVuE2dwNzZbRQ.gif)

`GlobalKeys`는 위젯이 상태를 잃지 않고 앱의 어느 위치에서든 부모를 바꿀 수 있게 하거나 위젯 트리의 전혀 다른 부분에있는 다른 위젯에 대한 정보에 액세스하는 데 사용할 수 있습니다. 첫 번째 시나리오의 예는 동일한 위젯을 두 개의 다른 화면에 표시하고 싶지만 모두 동일한 상태를 유지하는 경우 `GlobalKey`를 사용하는 것이 좋습니다. 두 번째 시나리오에서는 암호를 검증하려고 하지만 상태 정보를 트리의 다른 위젯과 공유하지 않으려고 합니다. `GlobalKeys`는 키를 사용하여 특정 위젯에 액세스하고 그 상태에 대한 정보를 쿼리함으로써 테스트에 유용 할 수 있습니다.

![img](https://miro.medium.com/max/320/1*JIPjn-gM6OIG_TfPJvtuVA.gif)

흔히 (항상 그런 것은 아니지만!) `GlobalKeys`는 전역 변수와 조금 비슷합니다. 일반적으로 `InheritedWidgets` 또는 Redux 나 BLoC 패턴과 같은 것을 사용하여 그 상태를 찾는 더 좋은 방법이 있습니다.

------

# Quick Recap

요약하면 위젯 트리에서 상태를 보존하려면 Keys를 사용하십시오. 이는 목록과 같은 유형의 위젯 모음을 수정할 때 가장 일반적으로 발생합니다. 키를 보관할 위젯 트리의 top에 놓고 위젯에 저장하는 데이터를 기반으로 사용하는 키 유형을 선택하십시오.

축하해, 이제 너는 Flutter Sorcerer가되는 길에 잘 다다를거야! 오, 내가 마법사라고 했니? 나는 앱 소스 코드를 작성하는 사람처럼 * sourcerer *를 의미했다. …거의. ⚡

------

[1] Code for the To-do app inspired by https://github.com/brianegan/flutter_architecture_samples/tree/master/example/vanilla