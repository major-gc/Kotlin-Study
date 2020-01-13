# 18. 확장

### 목차

* [확장 함수 정의하기](chapter18.md#확장-함수-정의하기)
* [슈퍼 클래스에 확장 함수 정의하기](chapter18.md#슈퍼-클래스에-확장-함수-정의하기)
* [제네릭 확장 함수](chapter18.md#제네릭-확장-함수)
* [지명 인자](chapter13.md#지명-인자)
* [초기화 블록](chapter13.md#초기화-블록)
* [속성 초기화](chapter13.md#속성-초기화)
* [초기화 순서](chapter13.md#초기화-순서)
* [초기화 지연시키기](chapter13.md#초기화-지연시키기)

---

## 1. 확장 함수 정의하기

확장은 클래스를 상속받지 않고도 해당 클래스에 함수를 추가하는 방법이다.  

ex. 예시코드1
```kotlin
fun String.addEnthusiasm(amount: Int = 1) = this + "!".repeat(amount)

fun main() {
    println("마드리갈이 그 건물에서 나왔습니다".addEnthusiasm())
}
```

String 은 수신자 타입이라고 하며 확장할 대상이 되는 클래스다.  
위 코드는 String 클래스에 함수를 추가한 것과 같다.  

위 코드처럼 String 클래스에 함수를 추가하려면 String 클래스를 상속받은
서브 클래스를 만들고 메소드를 만들어야 하지만,  
코틀린의 String 클래스는 open 키워드가 없으므로 상속불가능하다.  

<br>

## 2. 슈퍼 클래스에 확장 함수 정의하기

Any 와 같이 최상위 클래스에 확장 함수를 정의하면 모든 타입에 대해서 호출될 수 있다. 

예시코드1.
```kotlin
fun String.addEnthusiasm(amount: Int = 1) = this + "!".repeat(amount)

fun Any.easyPrint() = println(this)

fun main() {
    "마드리갈이 그 건물에서 나왔습니다".addEnthusiasm().easyPrint()
    42.easyPrint()
}
```
  
<br>

## 3. 제네릭 확장 함수

확장함수는 이전에도 보았듯이 수신자 타입을 받아야 한다.  
수신자 타입을 제네릭으로 변경하여 어떤 타입이든 받을 수 있도록 변경해보자.  

제네릭을 사용하지 않은 예시코드.
```kotlin
fun String.addEnthusiasm(amount: Int = 1) = this + "!".repeat(amount)

fun Any.easyPrint() : Any {
    println(this)
    return this
}

fun main() {
//    println("마드리갈이 그 건물에서 나왔습니다".addEnthusiasm())
    "마드리갈이 그 건물에서 나왔습니다".easyPrint().addEnthusiasm().easyPrint()
    42.easyPrint()
}
```

위 코드에서 addEnthusiasm() 확장함수는 수신자 타입으로 String을 받아야하지만,  
Any를 받고있으므로 컴파일 에러가 발생한다.  
이런 경우 easyPrint() 확장함수의 수신자 타입을 제네릭으로 변경하여 오류를 제거할 수 있다.  



제네릭을 사용한 예시코드.

```kotlin
fun String.addEnthusiasm(amount: Int = 1) = this + "!".repeat(amount)

fun <T> T.easyPrint() : T {
    println(this)
    return this
}

fun main() {
//    println("마드리갈이 그 건물에서 나왔습니다".addEnthusiasm())
    "마드리갈이 그 건물에서 나왔습니다".easyPrint().addEnthusiasm().easyPrint()
    42.easyPrint()
}
```

수신자 타입 Any를 T 라는 제네릭 타입으로 변경할 경우,  어떤 타입이든지 받을 수 있게되어 유연해지고    
또한, 위 예제코드에서 String을 리턴하므로 addEnthusiasm() 확장함수를 호출할 수 있게된다.   


제네릭 타입을 사용하는 코틀린 표준 라이브러리는 많다.  
그중 let을 보면

```kotlin
public inline fun <T, R> T.let(block : (T) -> R) : R {
    return block(this)
}
``` 

let 은 제네릭 타입을 사용하여 어떤 타입이든지 받을 수 있고 다른 타입으로 실행결과를 반환해준다.  


보통 let은 null check에서 사용한다. (기억 더듬기용..)
```kotlin
fun process(str: String?) { 
    str?.let { /*Do something*/ } 
}
```


또한, inline 키워드는 람다의 메모리 부담을 없애기 위해 만들어진 키워드이다. (이것도 기억 더듬기용..)  
inline을 붙이지 않으면 jvm에서 람다는 새로운 객체로 생성하여 메모리에 할당하는데,,  
inline을 붙일 경우 객체를 새로 생성하는 것이 아니라 해당 람다 코드를 통째로 복사하여  
람다를 호출한 곳에 붙여넣기하여 사용한다고 한다.   
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