# 14. 상속

## 서브클래

기본적으로 우리가 알고 있는 자바의 상속과 크게 다르지 않다. 다만 Kotlin에서는 기본적으로 서브클래스\(자식클래스\)를 만들 수 없게 되어 있다는 점이다. 그래서 사용하는 것이 open 키워드.

```kotlin
open class Car(val name: String) {
    protected open val carSize = 5;

    fun description() = "Car : $name\r\n" +
            "자동차 사이즈 : $carSize"

    open fun sound() = "부릉부릉!"
}
```

open 키워드를 사용함으로 이제 Car클래스는 서브 클래스를 가질 수 있다. Car를 상속받으면 Car의 name 속성과 함수들을 상속받는다. 다만 서브클래스에서 슈퍼클래스에 정의된 속성을 참조하거나 함수들을 오버라이딩하기 위해서도 open 키워드를 붙여 줘야한다. 그리고 protected 가시성도 사용가능하다.

```kotlin
open class Car(val name: String) {
    protected open val carSize = 5;

    fun description() = "Car : $name\r\n" +
            "자동차 사이즈 : $carSize"

    //오버라이딩하기위해서 open 키워드를 붙여준다.
    open fun sound() = "부릉부릉!"
}

class SportCar : Car("Sport Car") {
    override val carSize = super.carSize - 1
    override fun sound() = "우우우우우우우웅"
    
    //sportCar만이 가지는 함
    private fun openCover() = "뚜껑이 열립니다."
}

class Truck : Car("Truck") {
    override val carSize = super.carSize + 2
    override fun sound() = "덜컹덜컹"
}
```

Kotlin은 클래스가 정의될 때 기본적으로 서브클래스를 만들지 못하게 되어 있고 속성을 참조하거나 함수를 재정의하기 위해서 이러한 점 때문에 open 키워드를 사용한다고 했다. 하지만 서브 클래스가 open 이 붙은 클래스이고 이 서브 클래스의 또 다른 서브 클래스의 속성이나 함수는 open이 된다. 그래서 언제든 오버라이딩할 수 있다. 코드로 보면서 이해해보자.

```kotlin
open class Car(val name: String) {
    protected open val carSize = 5;

    fun description() = "Car : $name\r\n" +
            "자동차 사이즈 : $carSize"

    open fun sound() = "부릉부릉!"
    open fun message() = "아직 완성되지 않았습니다."
}

open class SportCar : Car("Sport Car") {
    override val carSize = super.carSize - 1
    override fun sound() = "우우우우우우우웅"
    
    //sportCar만이 가지는 함수
    private fun openCover() = "뚜껑이 열립니다."
    override fun message() = "운전에 주의하세요"
}

class Truck : Car("Truck") {
    override val carSize = super.carSize + 2
    override fun sound() = "덜컹덜컹"
}

class ToySportCar : SportCar() {
    override val carSize = 1
    override fun sound() = "끼깅"
    override fun message() = "????"
}
```

SportCar의 message는 open 키워드가 붙어 있지 않지만 ToySportCar에서 override가 된다. 하지만 이 함수를 더 이상 override하지 않고 싶다면 final 키워드를 붙여주면 된다.

## 타입 검사

Kotlin에서 타입 검사는 다음과 같이 할 수 있다.

```kotlin
val car1 = SportCar()
val car2 = Car("SportCar")

println(car1 is Car) // true
println(car2 is Car) // true
```

그러면 조건문이나 표현식을 사용하여 타입을 검사할 수 있다. 하지만 이 때 주의할 것은 ToySportCar의 경우 Car 객체도 SportCar객체도 true가 된다. 따라서 서브클래스가 여러 개가 있을 경우에 정확한 타입을 검사하기 위해서 슈퍼클래스보다 서브클래스의 타입을 우선적으로 검사해야한다. 

```kotlin
var car = ToySportCar()
var className = when(car) {
    is ToySportCar -> "TSP"
    is SportCar -> "SC"
    is Car -> "C"
    else -> throw IllegalArgumentException()
}
```

## 코틀린 타입의 상속 계층

자바에서 모든 클래스는 Object의 서브클래스인 것과 같이 코틀린에서 모든 null 불가능 클래스는 Any라는 최상위 슈퍼 클래스로부터 상속받는다. 따라서 Any 타입을 사용해서 타입을 검사하고 처리하면 편하지만 그렇게 하는 것이 항상 유용한 것은 아니다. 우리가 자바에서 Object 형 매개 변수를 사용하지 않는 것처럼...

```kotlin
fun printName(any: Any) {
    val namePrint = if(any is SportCar) {
        any.name
    } else {
        //이런식으로 작성하면 Car의 name을 참조하여 결과 반
        (any as Car)
        
        //이런식으로 작성하면 name 이 Car이면 true 아니면 false로 결과 반
        (any as Car) == "Car"
    }
    println($namePrint)
}
```

위처럼 타입 검사를 통해 어떠한 타입을 다른 타입으로 변환하기 위해서는 as 연산자를 사용한다. 당연한 이야기지만 as 연산자에서 타입을 변환시키는 것이 가능한 경우는 두 타입 간에 상속관계가 있을 경우에만 변환이 가능하다. 왼쪽의 객체가 Int 오른쪽 객체가 String 일 경우에는 상속관계가 아니기 때문에 변환이 불가능하고 ClassCastException이 발생한다.

위에서 보면 if 문 안에서 name속성을 참조하는 것을 볼 수 있는데 any 클래스에 name속성은 존재하지 않는다. 하지만 저것이 가능한 것은 스마트 캐스팅이 일어나기 때문이다. if문 안이 실행되었다는 것은 이미 any 객체의 타입이 SportCar라는 것을 증명해 준 것이기 때문에 코틀린 컴파일러는 알아서 이것을 SportCar로 변환하여 처리해준다.

## Any 클래스

Any 클래스에는 equals, hashCode, toString 함수는 자바와 같이 기본적인 구현만 되어 있다. 그리고 타입마다 특성이 다르기에 해당 타입에 맞게 적절히 오버라이딩해서 구현하면 된다. 

