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

<br>

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
  
<br>

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

<br>

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

<br>

## 5. 초기화 블록

kotlin에는 기본, 보조 생성자 뿐만 아니라 초기화 블록을 이용하여 인스턴스를 생성할 수 있다.  
초기화 블록은 보통 매개변수 속성의 전제조건을 검사할 때 사용한다.

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

    init {
        require(healthPoints > 0, {"healthPoints는 0보다 커야 합니다."})
        require(name.isNotBlank(), {"플레이어는 이름이 있어야 합니다."})
    }
}
``` 

초기화 블록은 인스턴스가 생성될때마다 호출되며, 전제조건을 만족하지 못할 경우  
IllegalArgumentException 이 발생한다.

<br>

## 6. 속성 초기화

kotlin에서는 무조건 클래스의 속성은 초기화 되어야 한다.

속성을 초기화하지 않을 경우 컴파일 에러가 발생한다.

```kotlin
class ~~ {
 val hometown: String // 컴파일 에러 발생
}
```

kotlin은 함수의 리턴값을 속성으로 초기화할 수 있다.

```kotlin
class Player3(_name: String,
              var healthPoints: Int,
              val isBlessed: Boolean,
              private val isImmotal: Boolean) {
    var name = _name
        get() = "${field.capitalize()} of $hometown"
        private set(value) {
            field = value.trim()
        }

    val hometown: String

    private fun selectHometown() = File("data/towns.txt")
                                    .readText()
                                    .split("\r\n")
                                    .shuffled()
                                    .first()
}
```

kotlin의 클래스 속성을 무조건 초기화해야 하는것 과는 다르게 지역변수는 초기화되지 않아도 사용가능하다.  
그 이유는 지역변수는 해당 함수 내부에서만 사용되지만,  
클래스의 속성은 인스턴스가 생성되면 다른 클래스들에서 해당 인스턴스를 사용할 수 있기 때문에   
다른 클래스에서 예외가 발생하기 때문이다.

<br>

## 7. 초기화 순서

(책 참고)
앞에서 기본 생성자, 보조 생성자, 초기화 블록이라는 3가지 속성 초기화 방법을 확인했다.  
속성 초기화의 순서는 기본 생성자 -> 클래스 내부에 지정된 속성 초기화 값 -> 초기화 블록 -> 보조 생성자 순이다.

<br>

## 8. 초기화 지연시키기

kotlin에서는 인스턴스 생성 시점에 바로 속성을 초기화해주는 생성자나 기본 타입 속성 지정이나 초기화블록이 없다면  
컴파일 오류가 난다.  
하지만, 개발을 할때 필수적으로 다른 클래스의 인스턴스를 참조해야 하는 경우 컴파일 타임에 초기화가 불가능하다.  

java를 예로 들면 다음과 같다.
```java
public class A {
    public A() {
    }
}

public class B {
    @Autowired
    A a;
}
```

B 클래스는 A 클래스의 메소드를 사용하기 위해 A 객체의 인스턴스를 Autowired 어노테이션으로 참조하고 있다.  
하지만, Autowired는 런타임 시에 실행되어 객체를 할당한다.  
  
위와 같은 경우를 해결하기 위해 lateinit 키워드를 사용하여 초기화를 지연시킬 수 있다.    
lateinit 키워드는 단지 초기화되지 않은 변수를 컴파일 오류가 나지 않도록 해주는 것일뿐이다. (또한 객체 참조만 가능)  

예시코드  
```kotlin
class Wheel {
    lateinit var aligment : String

    fun initAligment() {
        aligment = "Good"
    }

    fun printAligment() {
        if (::aligment.isInitialized) println(aligment)
    }
}
```

isInitialized() 함수는 코틀린 표준 라이브러리에 있는 함수인데, 해당 참조변수가 초기화되었는지 확인할때 사용한다.    
또한, 해당 함수에는 값이 아닌 참조를 전달해야 해서 :: 썼다. (참조를 전달할 때는 ::을 붙여야 되나 보다)

lateinit 키워드를 사용할 경우, val는 사용 못하며 (final 이기 때문에 초기화 시점 이후에는 값 변경이 안됨.)  
커스텀 게터, 세터 정의가 불가하다.

> 참고  
lateinit은 클래스 외부에서와 함수의 지역변수에서도 사용가능.

<br>

## 9. 늦 초기화

kotlin에서 속성이 사용될 때 초기화하도록 할 수 있다.
우리가 기본적으로 알고 있는 지연초기화이다.  

```kotlin
class Player3(_name: String,
              var healthPoints: Int,
              val isBlessed: Boolean,
              private val isImmotal: Boolean) {
    var name = _name
        get() = "${field.capitalize()} of $hometown"
        private set(value) {
            field = value.trim()
        } 
    
    val hometown by lazy { selectHometown() }

    private fun selectHometown() = File("data/towns.txt")
                                    .readText()
                                    .split("\r\n")
                                    .shuffled()
                                    .first()
}
```

getter에 $hometown이 호출될 때 초기화된다.
초기화된 후 변경이 불가능하도록 val 키워드로 사용된다