# Flutter Material Theme Lookup

Flutter 머터리얼 테마 룩업을 위해 만들어 졌습니다.

- [`ThemeData`](#theme_data)
- [`IconThemeData`](#icon_theme_data)
- [`TextTheme`](#text_theme)
- [`AppBarTheme`](#app_bar_theme)
- [`BottomAppBarTheme`](#bottom_app_bar_theme)
- [`ButtonThemeData`](#button_theme_data)
- [`CardTheme`](#card_theme)
- [`CupertinoThemeData`](#cupertino_override_theme)
- [`DialogTheme`](#dialog_theme)

- [`FloatingActionButtonThemeData`](#floating_action_button_theme_data)
- [`InputDecorationTheme`](#input_decoration_theme)
- [`SliderThemeData`](#slider_theme_data)
- [`TabBarTheme`](#tab_bar_theme)

<p id="theme_data"/>

## ThemeData

| 프로퍼티                    | 반환 타입                       | 설명                                                         |
| --------------------------- | ------------------------------- | ------------------------------------------------------------ |
| `accentColor`               | `Color`                         | 위젯의 포그라운드 색상.                                      |
| `accentColorBrightness`     | `Brightness`                    | `accentColor`의 밝기.                                        |
| `accentIconTheme`           | `IconThemeData`                 | `accentColor`의 아이콘 테마                                  |
| `accentTextTheme`           | `TextTheme`                     | `accentColor`의 텍스트 테마                                  |
| `appBarTheme`               | `AppBarTheme`                   | `AppBar`의 테마                                              |
| `backgroundColor`           | `Color`                         | `primaryColor`                                               |
| `bottomAppBarColor`         | `Color`                         | `BottomAppBar`의 컬러                                        |
| `bottomAppBarTheme`         | `BottomAppBarTheme`             | `BottomAppBar`의 테마                                        |
| `brightness`                | `Brightness`                    | 앱 전체의 밝기                                               |
| `buttonColor`               | `Color`                         | 버튼의 색상                                                  |
| `buttonTheme`               | `ButtonThemeData`               | 버튼의 테마                                                  |
| `canvasColor`               | `Color`                         | `MaterialType.canvas` `Material`의 기본 색상                 |
| `cardColor`                 | `Color`                         | `Card`의 `Material` 색상                                     |
| `cardTheme`                 | `CardTheme`                     |                                                              |
| `chipTheme`                 | `ChipThemeData`                 |                                                              |
| `colorScheme`               | `ColorCheme`                    | A set of thirteen colors that can be used to configure the color properties of most components |
| `cupertinoOverrideTheme`    | `CupertinoThemeData`            |                                                              |
| `cursorColor`               | `Color`                         |                                                              |
| `dialogBackgroundColor`     | `Color`                         |                                                              |
| `dialogTheme`               | `DialogTheme`                   |                                                              |
| `disabledColor`             | `Color`                         |                                                              |
| `dividerColor`              | `Color`                         |                                                              |
| `errorColor`                | `Color`                         |                                                              |
| `floatingActionButtonTheme` | `FloatingActionButtonThemeData` |                                                              |
| `hashCode`                  | `int`                           |                                                              |
| `highlightColor`            | `Color`                         |                                                              |
| `hitColor`                  | `Color`                         |                                                              |
| `iconTheme`                 | `IconThemeData`                 |                                                              |
| `indicatorColor`            | `Color`                         |                                                              |
| `InputDecorationTheme`      | `InputDecorationTheme`          |                                                              |
| `materialTapTargetSize`     | `MaterialTapTargetSize`         |                                                              |
| `pageTransitionsTheme`      | `PageTransitionsTheme`          |                                                              |
| `platform`                  | `TargetPlatform`                |                                                              |
| `primaryColor`              | `Color`                         |                                                              |
| `primaryColorBrightness`    | `Brightness`                    |                                                              |
| `primaryColorDark`          | `Color`                         |                                                              |
| `primaryColorLight`         | `Color`                         |                                                              |
| `primaryIconTheme`          | `IconThemeData`                 |                                                              |
| `primaryTextTheme`          | `TextTheme`                     |                                                              |
| `scaffoldBackgroundColor`   | `Color`                         |                                                              |
| `secondaryHeaderColor`      | `Color`                         |                                                              |
| `selectedRowColor`          | `Color`                         |                                                              |
| `sliderTheme`               | `SliderThemeData`               |                                                              |
| `splashColor`               | `Color`                         |                                                              |
| `splashFactory`             | `InteractiveInkFeatureFactory`  |                                                              |
| `tabBarTheme`               | `TabBarTheme`                   |                                                              |
| `textSelectionColor`        | `Color`                         |                                                              |
| `textSelectionHandleColor`  | `Color`                         |                                                              |
| `textTheme`                 | `TextTheme`                     |                                                              |
| `toggleableActiveColor`     | `Color`                         |                                                              |
| `typography`                | `Typography`                    |                                                              |
| `unselectedWidgetColor`     | `Color`                         |                                                              |

<p id="icon_theme_data"/>

## IconThemeData

| 프로퍼티     | 반환 타입 | 설명 |
| ------------ | --------- | ---- |
| `color`      | `Color`   |      |
| `hashCode`   | `int`     |      |
| `isConcrete` | `bool`    |      |
| `opacity`    | `double`  |      |
| `size`       | `double`  |      |

<p id="text_theme"/>

## TextTheme

| 프로퍼티   | 반환타입    | 설명                          |
| ---------- | ----------- | ----------------------------- |
| `body1`    | `TextStyle` | 기본 텍스트                   |
| `body2`    | `TextStyle` | 강조 텍스트                   |
| `button`   | `TextStyle` |                               |
| `caption`  | `TextStyle` | 이미지와 사용                 |
| `display1` | `TextStyle` | 큰 텍스트                     |
| `display2` | `TextStyle` | 더 큰 텍스트                  |
| `display3` | `TextStyle` | 더 더 큰 텍스트               |
| `display4` | `TextStyle` | 더 더 더 큰 텍스트            |
| `hashCode` | `int`       |                               |
| `headline` | `TextStyle` | 대화 상자의 헤드라인          |
| `overline` |             | 가장 작은 텍스트              |
| `subhead`  |             | 리스트의 주요 텍스트          |
| `title`    |             | 앱바나 대화상자의 주요 텍스트 |

<p id = "app_bar_theme"/>

## AppBarTheme

| 프로퍼티           | 반환 타입       | 설명 |
| ------------------ | --------------- | ---- |
| `actionsIconTheme` | `IconThemeData` |      |
| `brightness`       | `Brightness`    |      |
| `elevation`        | `double`        |      |
| `hashCode`         | `int`           |      |
| `iconTheme`        | `IconThemeData` |      |
| `textTheme`        | `TextTheme`     |      |

<p id = "bottom_app_bar_theme"/>

## BottomAppBarTheme

| 프로퍼티    | 반환 타입      | 설명 |
| ----------- | -------------- | ---- |
| `color`     | `Color`        |      |
| `elevation` | `double`       |      |
| `hashCode`  | `int`          |      |
| `shape`     | `NotchedShape` |      |

<p id = "button_theme_data"/>

## ButtonThemeData

| 프로퍼티          | 반환 타입                 | 설명 |
| ----------------- | ------------------------- | ---- |
| `alginedDropdown` | `bool`                    |      |
| `colorScheme`     | `ColorScheme`             |      |
| `constraints`     | `BoxConstraints`          |      |
| `hashCode`        | `int`                     |      |
| `height`          | `double`                  |      |
| `layoutBehavior`  | `ButtonBarLayoutBehavior` |      |
| `minWidth`        | `double`                  |      |
| `padding`         | `EdgeInsectsGeometry`     |      |
| `shape`           | `ShapeBorder`             |      |
| `textTheme`       | `ButtonTextTheme`         |      |

<p id = "card_theme"/>

## CardTheme

| 프로퍼티       | 반환 타입            | 설명 |
| -------------- | -------------------- | ---- |
| `clipBehavior` | `Clip`               |      |
| `color`        | `Color`              |      |
| `elevation`    | `double`             |      |
| `hashCode`     | `int`                |      |
| `margin`       | `EdgeInsetsGeometry` |      |
| `shape`        | `ShapeBorder`        |      |

<p id = "chip_theme_data"/>

## ChipThemeData

| 프로퍼티                 | 반환 타입            | 설명 |
| ------------------------ | -------------------- | ---- |
| `backgroundColor`        | `Color`              |      |
| `brightness`             | `Brightness`         |      |
| `deleteIconColor`        | `Color`              |      |
| `disabledColor`          | `Color`              |      |
| `elevation`              | `double`             |      |
| `hashCode`               | `int`                |      |
| `labelPadding`           | `EdgeInsetsGeometry` |      |
| `labelStyle`             | `TextStyle`          |      |
| `padding`                | `EdgeInsetsGeometry` |      |
| `pressElevation`         | `double`             |      |
| `secondaryLabelStyle`    | `TextStyle`          |      |
| `secondarySelectedColor` | `Color`              |      |
| `selectedColor`          | `Color`              |      |
| `selectedShadowColor`    | `Color`              |      |
| `shadowColor`            | `Color`              |      |
| `shape`                  | `ShapeBorder`        |      |

<p id = "cupertino_override_theme"/>

## CupertinoThemeData

| 프로퍼티                  | 타입                     | 설묭 |
| ------------------------- | ------------------------ | ---- |
| `barBackgroundColor`      | `Color`                  |      |
| `brightness`              | `Brightness`             |      |
| `primaryColor`            | `Color`                  |      |
| `primaryContrastingColor` | `Color`                  |      |
| `scaffoldBackgroundColor` | `Color`                  |      |
| `textTheme`               | `CupertinoTextThemeData` |      |

<p id = "dialog_theme"/>

## DialogTheme

| 프로퍼티           | 타입          | 설명 |
| ------------------ | ------------- | ---- |
| `backgroundColor`  | `Color`       |      |
| `contentTextStyle` | `TextStyle`   |      |
| `elevation`        | `double`      |      |
| `hashCode`         | `int`         |      |
| `shape`            | `ShapeBorder` |      |
| `titleTextStyle`   | `TextStyle`   |      |

<p id = "floating_action_button_theme_data"/>

## FloatingActionButtonThemeData

| 프로퍼티             | 타입          | 설명 |
| -------------------- | ------------- | ---- |
| `backgroundColor`    | `Color`       |      |
| `disabledElevation`  | `double`      |      |
| `elevation`          | `double`      |      |
| `foregroundColor`    | `Color`       |      |
| `hashCode`           | `int`         |      |
| `highlightElevation` | `double`      |      |
| `shape`              | `ShapeBorder` |      |

<p id = "input_decoration_theme"/>

## InputDecorationTheme

| 프로퍼티                 | 타입                 | 설명 |
| ------------------------ | -------------------- | ---- |
| `alignLabelWithHint`     | `bool`               |      |
| `border`                 | `InputBorder`        |      |
| `contentPadding`         | `EdgeInsetsGeometry` |      |
| `counterStyle`           | `TextStyle`          |      |
| `disabledBorder`         | `InputBorder`        |      |
| `enabledBorder`          | `InputBorder`        |      |
| `errorBorder`            | `InputBorder`        |      |
| `errorMaxLines`          | `int`                |      |
| `errorStyle`             | `TextStyle`          |      |
| `fillColor`              | `Color`              |      |
| `filled`                 | `bool`               |      |
| `focusedBorder`          | `InputBorder`        |      |
| `focusedErrorBorder`     | `InputBorder`        |      |
| `hasFloatingPlacehorder` | `bool`               |      |
| `helperStyle`            | `TextStyle`          |      |
| `hintStyle`              | `TextStyle`          |      |
| `isCollapsed`            | `bool`               |      |
| `isDense`                | `bool`               |      |
| `labelStyle`             | `TextStyle`          |      |
| `prefixStyle             | `TextStyle`          |      |
| `suffixStyle`            | `TextStyle`          |      |

<p id = "slider_theme_data"/>

## SliderThemeData

| 프로퍼티                        | 타입                   | 설명                                |
| ------------------------------- | ---------------------- | ----------------------------------- |
| `activeTickMarkColor`           | `Color`                |                                     |
| `activeTrackColor`              | `Color`                |                                     |
| `disabledActiveTickMarkColor`   | `Color`                |                                     |
| `diabledActiveTrackColor`       | `Color`                |                                     |
| `disabledInactiveTickMarkColor` | `Color`                |                                     |
| `diabledInactiveTrackColor`     | `Color`                |                                     |
| `disabledThumbColor`            | `Color`                |                                     |
| `hashCode`                      | `int`                  |                                     |
| `inactiveTickMarkColor`         | `Color`                |                                     |
| `inactiveTrackColor`            | `Color`                |                                     |
| `overlayColor`                  | `Color`                | Thumb가 눌렸을때 주변 오버레이 컬러 |
| `overlayShape`                  | `SliderComponentShape` |                                     |
| `showValueIndicator`            | `ShowValueIndicator`   |                                     |
| `thumbColor`                    | `Color`                |                                     |
| `thumbShape`                    | `SliderComponentShape` |                                     |
| `tickMarkShape`                 | `SliderTickMarkShape`  |                                     |
| `trackHeight`                   | `double`               |                                     |
| `trackShape`                    | `SilderTrackShape`     |                                     |
| `valueIndicatorColor`           | `Color`                |                                     |
| `valueIndicatorShape`           | `SliderComponentShape` |                                     |
| `valueIndicatorTextStyle`       | `TextStyle`            |                                     |

<p  id = "tab_bar_theme"/>

## TabBarTheme

| 프로퍼티               | 타입                  | 설명 |
| ---------------------- | --------------------- | ---- |
| `hashCode`             | `int`                 |      |
| `indicator`            | `Decoration`          |      |
| `indicatorSize`        | `TabBarIndicatorSize` |      |
| `labelColor`           | `Color`               |      |
| `labelPadding`         | `EdgeInsetsGeometry`  |      |
| `labelStyle`           | `TextStyle`           |      |
| `unselectedLabelColor` | `Color`               |      |
| `unselectedLabelStyle` | `TextStyle`           |      |

---

## 참조

- [Flutter ThemeData class](https://api.flutter.dev/flutter/material/ThemeData-class.html)