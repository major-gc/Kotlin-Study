# 13. 초기화

### 목차

* [기본 생성자](chapter13.md#기본-생성자)
* [보조 생성자](chapter13.md#보조-생성자)
* [기본 인자](chapter13.md#기본-인자)
* [지명 인자](chapter13.md#지명-인자)
* [초기화 블록](chapter13.md#초기화-블록)
* [속성 초기화](chapter13.md#속성-초기화)
* [초기화 순서](chapter13.md#초기화-순서)
* [초기화 지연시키기](chapter13.md#초기화-지연시키기)

---

>참고  
클래스의 인스턴스를 생성하는 것은 객체를 메모리에 할당하는 것.  
초기화란 객체를 사용할 수 있도록 객체의 각 속성에 초기값을 지정하는 것.

## 1. 기본 생성자

ex. 기본 생성자 예시코드1
```kotlin
class Player3(_name: String,
              _healthPoints: Int,
              _isBlessed: Boolean,
              _isImmotal: Boolean) {
    var name = _name
        get() = field.capitalize()
        private set(value) {
            field = value.trim()
        }

    val healthPoints = _healthPoints
    val isBlessed = _isBlessed
    private val isImmotal = _isImmotal
}

fun main() {
    val player = Player3("Madrigal", 89, true, false)
}

```

Player 클래스의 각 속성에 초깃값 제공을 위한 임시변수 정의.  
_ (밑줄)이 붙은 것은 1회용 임시변수를 뜻한다. (관례)
 
<br>

ex. 기본 생성자 예시코드2 (속성 초기화 중복 코드 줄여줌.)
```kotlin
class Player3(_name: String,
              var healthPoints: Int,
              val isBlessed: Boolean,
              private val isImmotal: Boolean) {
    var name = _name
        get() = field.capitalize()
        private set(value) {
            field = value.trim()
        }
}

fun main() {
    val player = Player3("Madrigal", 89, true, false)
}
```

생성자 매개변수에 var 또는 val 을 붙일 경우, 자동으로 속성이 정의된다.  
만약, name 속성과 같이 커스텀한 게터, 세터를 사용해야될 경우에는 위 코드 처럼 임시변수를 사용해야한다.  
(생성자 매개변수 수정이 불가능함.)  
(생성자 매개변수로는 커스텀 게터, 세터 생성 시 컴파일 오류남.)


## 2. 보조 생성자

kotlin의 내장 타입은 java의 타입과 비슷하다.  
\(kotlin은 java와 거의 같으므로 책에 나온 표는 책 참고.\)

예시코드. \(초기화 방법\)

```kotlin
fun main(args: Array<String>) {
    var string = "string"
    var char = 'c'
    var boolean = true
    var int = 5
    var double = 5.5
    var float = 5.5f

    var list0 = listOf(1..5)
    var list1 = arrayListOf(1..5)
    var list2 = List(5, {i-> i})
    var list3 = (1..5).toList()

    var set0 = setOf(1, 1, 3, 2)
    var set1 = hashSetOf(1, 1, 3, 2)
    var set2 = linkedSetOf(1, 1, 3, 2)
    var set3 = sortedSetOf(1, 1, 3, 2)
    var set4 = (1..5).toSet()
    var set5 = (1..5).toHashSet()

    var map0 = mapOf("one" to 1, "two" to 2)
    var map1 = mapOf(Pair("one",1), Pair("two",2))
    var map2 = hashMapOf(Pair("one",1), Pair("two",2))
    var map3 = linkedMapOf(Pair("one",1), Pair("two",2))
    var map4 = sortedMapOf(Pair("one",1), Pair("two",2))
}
```

## 3. 읽기 전용 변수

kotlin에서는 한번 변수가 저장되면 변경될 수 없도록 읽기 전용 변수를 지원한다. 읽기 전용 변수로 선언한 변수를 변경하면 컴파일 오류가 발생한다.

기본 변경 가능한 변수 키워드는 var \(variable\)  
변경 불가능한 읽기 전용 변수 키워드는 val \(value\)

예시코드.

```kotlin
fun main(args: Array<String>) {
    var name1 = "name1"
    val name2 = "name2"

    name2 = "name change" // 컴파일 오류 발생

}
```

> 참고로. intellij에서는 var로 선언한 변수가 변경되지 않으면 val로 바꾸라는 제안을 한다. 책에서는 intellij의 제안을 따르는 것이 좋다고 함.

범위 표현 예시코드\(with. 조건 표현식\)

```kotlin
fun main(args: Array<String>) {
    val healthPoints = 80
    val healthStatus = if(healthPoints in 90..99) {
        "최상의 상태"
    } else if (healthPoints in 80..89) {
        "약간의 찰과상"
    }

    println(healthStatus)
}
```

## 4. 타입 추론

2장 처음 변수를 선언하는 방법으로 다음과 같이 설명했다.

var experiencePoints : Int = 5

| var | experiencePoints | : Int | = | 5 |
| :--- | :---: | :---: | :---: | :---: |
| 변수 정의 키워드 | 변수 이름 | 타입 정의 | 대입 연산자 | 대입 값 |

kotlin에서는 타입 추론이 있다.  
처음 변수를 선언할 때, 초기값을 지정하는 경우에는 변수 타입을 생략할 수 있다.

kotlin 컴파일러가 코드를 컴파일 할 때, 알아서 변수를 처리해준다. 책에서는 코드의 간결함을 위해 타입을 생략할 것을 추천한다.

예시코드.

```kotlin
fun main(args: Array<String>) {
    var string = "string"
    var char = 'c'
    var boolean = true
    var int = 5
    var double = 5.5
    var float = 5.5f
}
```

## 5. 컴파일 시점 상수

val 키워드는 한 번 값을 저장하면, 바꿀 수 없다고 소개되었지만, 특별한 경우 값을 변경할 수 있다고 한다.  
그래서, 값을 절대 변경하지 못하게 하려면 "컴파일 시점 상수"를 고려해야 한다.

"컴파일 시점 상수"는 main 함수 포함한 모든 함수의 외부에 정의되는 상수이다. kotlin 의 시작 지점인 main 함수를 포함한 모든 함수는 모두 런타임에 호출되고 실행되지만, "컴파일 시점 상수"는 말 그대로 컴파일 시점에 값이 초기화되므로 변경할 수 없다.

컴파일 시점 상수는 컴파일 시점에 컴파일러가 알 수 있는 기본 타입이어야 한다.

* String
* Int
* Double
* Float
* Long
* Short
* Byte
* Char
* Boolean

예시코드.

```kotlin
const val MAX_EXPERIENCE: Int = 5000
const val MAX_EXPERIENCE2 = 5000

fun main(args: String<Array>) {
    // ...
}
```

## 6. 코틀린 바이트 코드

kotlin 프로그래밍 언어는 jvm에서 자바 바이트코드로 변환되어 실행된다.

kotlin 언어를 자바 바이트코드로 보는 방법은 1. intellij에서 shift 키를 두 번 누른다. 2. **Search Everywhere** 대화상자가 나오면 "show kotlin bytecode" 를 입력한다. 3. 코틀린 바이트코드 창이 보이면, 왼쪽 위에 있는 **Decompile** 버튼을 클릭한다.

## 7. 코틀린과 자바 기본 타입 비교

kotlin 프로그래밍 언어에서는 오로지, 참조 타입만을 지원하며 java의 기본 타입을 지원하지 않는다. 이유는, kotlin 프로그래밍 언어에서는 개발자가 기본 타입을 사용할지, 참조 타입을 사용할지 고민하지 않도록 오로지 참조 타입만을 지원한다. \(boxing, unboxing 관련 고민할 필요가 사라짐.\)

또한, kotlin 컴파일러는 최대한 좋은 성능을 제공하기 위해 참조 타입을 사용하더라도 가능한 한 기본 타입으로 바꿔서 사용한다.

