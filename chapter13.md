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
(생성자 매개변수는 수정이 불가능하고 커스텀 게터, 세터 생성 시 컴파일 오류가 난다.)  


## 2. 보조 생성자

kotlin 에는 두 종류의 생성자가 있다.  
기본 생성자와 보조 생성자이다. (기본 생성자는 위에서 살펴봤으므로 패스...)  
보조 생성자는 클래스의 헤더가 아닌 몸체에 정의하며, 매개변수를 받아서 기본 생성자를 호출하거나 또 다른 보조 생성자를 호출한다.
 

예시코드1. \(보조 생성자\)

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

    constructor(name: String) : this (name, healthPoints =100, isBlessed = true, isImmotal = false)
    
}
```

위 예시코드는 지명인자를 사용하였으며 (지명인자 = 매개변수 명을 뜻하는 듯.)  
this 키워드는 기본 생성자를 뜻한다.  
예상되다시피 위 보조 생성자는 name 값만 인수로 넘겨서 Player의 인스턴스를 생성할 수 있다.  

ex. val player = Player3("Madrigal") 

예시코드2. \(보조 생성자에 속성 초기화 코드 추가\)

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

    constructor(name: String) : this (name, healthPoints =100, isBlessed = true, isImmotal = false) {
        if (name.toLowerCase() == "kar") healthPoints = 40
    }
    
}
```

> 참고  
보조 생성자에서는 클래스 속성 정의가 불가능하다.  
클래스 속성은 기본 생성자나 클래스 몸체에 정의되어야 한다.
  


## 3. 기본 인자

기본 생성자에 기본 인자를 줄 수 있다.  
기본 인자를 줄 경우 보조 생성자에서는 해당 인자가 없어도 된다.

예시코드.

```kotlin
class Player3(_name: String,
              var healthPoints: Int = 100,
              val isBlessed: Boolean,
              private val isImmotal: Boolean) {
    var name = _name
        get() = field.capitalize()
        private set(value) {
            field = value.trim()
        }

    constructor(name: String) : this (name, isBlessed = true, isImmotal = false) {
        if (name.toLowerCase() == "kar") healthPoints = 40
    }
}
```

## 4. 지명 인자

지명 인자는 함수를 호출하기위한 인자에 이름을 지정하는 것이다.

예시코드
```kotlin
fun main() {
    // 지명인자 사용
    val player2 = Player3("Madrigal", healthPoints =100, isBlessed = true, isImmotal = false)
    player2.castFireball()
    printPlayerStatus2(player2)
}
```

지명인자를 사용할 경우 실제 매개변수의 위치가 달라도 자동으로 매핑해주고  
어떤 매개변수에 전달되는 값인지 알아보기 쉽다.   
(특히 함수의 인자가 많을 경우 유용함.)


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

