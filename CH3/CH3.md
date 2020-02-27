# 함수 정의와 호출
## 3장에서 다루는 내용

> - 컬렌션, 문자열, 정규식을 다루기 위한 함수
> - 이름 붙인 인자, 디폴트 파라미터 값, 중위 호출 문법 사용
> - 확장 함수와 확장 프로퍼티를 사용해 자바 라이브러리 적용
> - 최상위 및 로컬 함수와 프로퍼티를 사용해 코드 구조화

### 3.1. 코틀린에서 컬렉션 만들기

숫자로 이루어진 집합
```kotlin
val set = hashSetOf(1, 7, 53)
```
리스트와 맵
```kotlin
val list = arrayListOf(1, 7, 53)
val map = hashMapOf(1 to "one", 7 to "seven", 53 to "fifty-three")
```
 <br>
 여기서 to는 언어가 제공하는 특별한 키워드가 아니라 일반 '함수' 라는 점에 유의하자
 <br>
 위에서 만든 객체의 클래스

 ```kotlin
>>> println(set.javaClass)
class java.util.HasHset
>>> println(list.javaClass)
class java.util.ArrayList
>>> println(map.javaClass)
class java.util.HashMap

 ```
이는 코틀린이 자신만의 컬렉션 기능을 제공하지 않는다는 뜻이다.
<br>
-> 기존 자바 컬렉션을 활용할 수 있음!
<br>
<br>
하지만 코틀린 컬렉션에서는 자바보다 더 많은 기능을 쓸 수 있다.
<br>
예를 들면 리스트의 마지막 원소를 가져오거나 수로 이루어진 컬렉션에서 최댓값을 찾을 수 있다.
```kotlin
>>> val strings = listOf("first", "second", "fourteenth")
>>> printl(strings.last())
fourteenth

>>> val numbers = setOf(1, 14, 2)
println(numbers.max())
14
```
<br>

###  3.2 함수를 호출하기 쉽게 만들기

자바 기본적인 toString
```kotlin
>>> val list = listOf(1,2,3)
>>> println(list)
[1, 2, 3]
```
만약 디폴트 구현과 달리 (1;2;3) 같이 나타내고 싶다면??  
joinToString() 함수는 전달받은 컬랙션에 구분자, 접두사, 접미사를 추가한다.
```kotlin
fun <T> joinToString(
    collection: Collection<T>,
    separator: String,
    prefix: String,
    postfix: String
) : String {
    val result = StringBuilder(perfix)

    for((index, element) in collection.withIndex()) {
        if(index > 0) result.append(separator)
        result.append(element)
    }

    result.append(postfix)
    return result.toString()
}
```
제네릭 문법은 자바와 비슷하다.  
이 함수를 동작시키면 아래와 같은 결과를 출력한다.
```kotlin
>>> val list = listOf(1,2,3)
>>> println(joinToString(list, ";", "(", ")"))
(1;2;3)
```
하지만 이 함수를 어떻게 하면 조금 덜 번잡하게 만들 수 있을까?  
함수 호출시 네 인자를 매번 전달해야 할까?  
<br>
#### 3.2.1 이름 붙인 인자
해결하고픈 첫 번째 문제는 호출 부분의 가독성이다.  
예를 들어서 joinToString 호출을 살펴보자
```kotlin
joinToString(collection, " ", " ", ".")
```
전달된 각 문자열이 어떤 역할을 하는지 구분이 가능한가?  
이런 문제점들은 특히 의미없는 flag 값 전달에서 문제를 발생시킨다.  
일부 자바 코딩 스타일에서는 boolean 대신 enum 타입을 권장한다.  
또 어디에서는 다음과 같이 파라미터 이름을 주석에 넣으라고 요구하기도 한다.
```java
joinToString(collection, /* separator */ " ", /* prefix */ " ", /* postfix */ ".")
```
코틀린에서는 다음과 같이 더 잘 할 수 있다.
```kotlin
joinToString(collection, separator = " ", prefix = " ", postfix = ".")
```
코틀린에서는 전달 인자 중 일부(혹은 전부)의 이름을 명시할 수 있다.  
다만 어느 하나라도 이름을 명시한다면 혼동을 막기 위해 그 뒤에 오는 모든 인자는 이름을 명시하여야 한다.
> 불행이도 자바로 작성한 코드를 호출할 때는 이름 붙인 인자를 사용할 수 없다.

<br>

#### 3.2.2 디폴트 파라미터 값
자바에서는 일부 클래스에서 오버로딩한 메소드가 너무 많아진다는 문제가 있다.  
오버로딩 메소드들은 하위 호환, 확장등 여러 가지 이유로 만들어진다.  
하지만 이는 모호함을 동반한다.  
모틀린에서는 함수 선언에서 파라미터의 디폴트 값을 지정할 수 있다.  
디폴트 값을 통해 joinToString 함수를 개선해보자.
```kotlin
fun <T> joinToString(
    collection: Collection<T>,
    separator: String = ", ",
    prefix: String = "",
    postfix: String = ""
) : String {
    val result = StringBuilder(perfix)

    for((index, element) in collection.withIndex()) {
        if(index > 0) result.append(separator)
        result.append(element)
    }

    result.append(postfix)
    return result.toString()
}
```
이제 함수를 호출할 때 모든 인자를 쓸 수도 있고, 일부를 생략할 수도 있다.
```kotlin
>>> joinToString(list, ", ", "", "")
1, 2, 3
>>> joinToString(list)
1, 2, 3
>>> joinToString(list, "; ")
1; 2; 3
```
이름 붙은 인자를 사용하는 경우에는 인자 목록의 중간에 있는 인자 생략도 가능하다.
```kotlin
>>> joinToString(list, postfix - ";", prefix = "# ")
# 1, 2, 3;
```
또한 이러한 함수들을 꼭 어떠한 클래스 안에 선언해야하는 것은 아니다.  
코틀린에서는 함수를 클래스 안에 선언할 필요가 전혀 없다.  
<br>
#### 3.2.3 정적인 유틸리티 클래스 없애기: 최상위 함수와 프로퍼티

개발을 하다보면 어느 한 클래스에 포함시키기 어려운 코드가 생긴다.  
그 결과 정적 메소드를 모아두는 역할만 담당하며, 특별한 상태나 인스턴스 메소드는 없는 클래스가 생겨난다.  
코틀린에서는 이런 무의미한 클래스가 필요 없다.  
대신 함수를 직ㅈ버 소스 파일의 최상위 수준, 모든 클래스의 밖에 위치시키면 된다.  
해당 함수를 사용하고 싶을 때에는 그 함수가 정의된 패키지를 임포트해서 사용하면 된다.  
joinToString 함수를 strings 패키지에 직접 넣어보자.
```kotlin
package strings

fun joinToString(...): String {...}
```
JVM은 클래스 안에 들어 있는 코드만을 실행할 수 있기 때문에 컴파일러는 이 파일을 컴파일할 때 새로운 클래스를 정의해준다.  
해당 메소드가 join.kt 라는 코틀린 파일에 들어있다면 아래와 같은 컴파일 결과가 나온다.  
```java
/* 자바 */
package strings;

public class JoinKt {
    public static String joinToString(...) {...}
}
```
코틀린 컴파일러가 생성하는 클래스의 이름은 최상위 함수가 들어있던 코틀린 소스 파일 이름과 대응한다.  
코틀린 파일의 모든 최상위 함수는 이 클래스의 정적(static) 메소드가 된다.  
따라서 자바에서는 joinToString을 호출하기 쉽다.
```java
import srtrings.JoinKt;

JoinKt.joinToString(list, ", ", "", "")
```
<br>
함수와 마찬가지로 프로퍼티또한 파일의 최상위 수준에 놓을 수 있다.  
최상위 프로퍼티를 활용해 코드에 상수를 추가할 수 있다.

```kotlin
val UNIX_LINE_SEPARATOR = "\n"
```
기본적으로 최상위 프로퍼티도 다른 모든 프로퍼티처럼 접근자 메소드를 통해 자바 코드에 노출된다.  
하지만 상수처럼 사용하기 원한다면 자연스럽지 못할 수 있다.  
그렇기 때문에 상수를 public static final 필드로 컴파일 해야 한다.  
이때 const  변경자를 추가해주면 프로퍼티를 public static final 필드로 컴파일하게 만들 수 있다.  
```kotlin
const val UNIX_LINE_SEPARATOR = "\n"
```
이 코드는 자바 코드와 동등한 바이트코드를 만들어낸다.
```java
/* 자바 */
public static final String UNIX_LINE_SEPARATOR = "\n"
```
<br>

### 3.3 메소드를 다른 클래스에 추가: 확장 함수와 확장 프로퍼티
기존 코드와 코틀린 코드를 자연스럽게 통합하는 것은 코틀린의 핵심 목표 중 하나다.  
자바 API를 재작성하지 않고도 코틀린이 제공하는 여러 편리한 기능을 사용할 수 있다면?  
확장 함수를 사용하자.  
확장 함수는 어떤 클래스의 멤버 메소드인 것처럼 호출할 수 있지만 그 클래스의 밖에 선언된 함수다.  
어떤 문자열의 마지막 문자를 돌려주는 메소드를 추가해보자.  
```kotlin
package strings

fun String.lastChar () : Char = this.get(this.length - 1)
```
확장 함수를 만들기 위해해서는 추가하려는 함수 이름 앞에 그 함수가 확장할 클래스의 이름을 덧붙이기만 하면 된다.  
클래스의 이름을 수신 객체 타입이라 부르며, 확장 함수가 호출되는 대상이 되는 값을 수신 객체라고 부른다.

```kotlin
 /* 수신 객체 타입 (String) */ /* 수신 객체 (this) */
fun String.lastChar () : Char = this.get(this.length - 1)
```
이 함수를 호출하는 구문은 다른 일반 클래스 멤버를 호출하는 구문과 똑같다.
```kotlin
>>> println("Kotlin".lastChar())
n
```
이 예제에서는 String이 수신 객체 타입, "Kotlin" 이 수신 객체이다.  
클래스를 직접 작성한 것도 아니고 String 클래스의 소스코드를 소유한 것도 아니지만, 원하는 메소드를 String 클래스에 추가할 수있다.  
자바 클래스로 컴파일한 클래스 파일이 있는 한 그 클래스에 원하는 대로 확장을 추가할 수 있다.  
<br>
일반 메소드의 본문엣 this를 사용할 때와 마찬가지로 확장 함수 본문에도 this를 쓸 수 있다.  
그리고 일반 메소드와 마찬가지로 확장 함수 본문에서도 this를 생략할 수 있다.
```kotlin
package strings

fun String.lastChar () : Char = get(length - 1)
```
확장 함수 내부에서도 수신 객체의 메소드나 프로퍼티를 사용할 수 있다.  
하지만 이것이 캡슐화를 깨지는 않는다.  
private 멤버나 protected 멤버는 사용할 수 없다.
<br>
<br>
#### 3.3.1 임포트와 확장 함수
확장 함수를 사용하기 위해서는 그 함수를 다른 클래스나 함수와 마찬가지로 임포트해야만 한다.  
동일한 이름의 확장함수를 구분하기 위해서 꼭 임포트가 필요하다.  
코틀린에서는 클래스를 임포트할 때와 동일한 구문을 사용해 개별 함수를 임포트할 수 있다.  
```kotlin
import strings.lastChar

val c = "Kotlin".lastChar()
```
물론 *를 사용한 임포트도 잘 작동한다.
```kotlin
import strings.*

val c = "Kotlin".lastChar()
```
as 키워드를 사용하면 임포트한 클래스나 함수를 다른 이름으로 부를 수 있다.
```kotlin
import strings.lastChar as last
val c = "Kotlin".last()
```
한 파일 안에서 여러 패키지에 속해이는 동일 함수들을 가져와 사용해야 한다면 충돌을 막을 수 있다.
<br>
<br>

#### 3.3.2 자바에서 확장 함수 호출
내부적으로 확장 함수는 수신 객체를 첫 번째 인자로 받는 정적 메소드다.  
그래서 확장 함수를 호출해도 다른 어댑터 객체나 실행 시점 부가 비용이 들지 않는다.  
자바에서는 단지 정적 메소드를 호출하면서 첫 번째 인자로 수신 객체를 넘기기만 하면 된다.  
다른 최상위 함수와 마찬가지로 확장 함수가 들어있는 자바 클래스 이름도 확장 함수가 들어있는 파일 이름에 따라 결정된다.  
```java
char c = StringUtilKt.lastChar("java");
```
<br>

#### 3.3.3 확장 함수로 유틸리티 함수 정의
아래는 joinToString 함수의 최종 버전이다.
```kotlin
fun <T> Collection<T>.joinToString(
    separator: String = ", ",
    prefix: String = "",
    postfix: String = ""
) : String {
    val result = StringBuilder(prefix)

    for ((index, element) in this.withIndex()){
        if(index > 0) result.append (separator)
        result.append(element)
    }

    result.append(postfix)
    return result.toString()
}

>>> val list = listOf(1,2,3)
>>> println(list.joinToString(separator = "; ",
...     prefix = "(", postfix - ")"))
(1; 2; 3)
```
구체적인 타입을 수신 객체 타입으로 지정할 수도 있다.  
아래는 문자열의 컬렉션에 대해서만 호출할 수 있는 join 함수를 정의하고 있다.
```kotlin
fun Collection<String>.join(
    separator: String = ", ",
    prefix: String = "",
    postfix: String = ""
) = joinToString(separator, prefix, postfix)

>>> println(listOf("one", "two", "eigth").join(" "))
one tow eigth
```
<br>

#### 3.3.4 확장 함수는 오버라이드할 수 없다.
확장 함수는 오버라이드할 수 없다.  
View와 그 하위 클래스인 Button이 있는데, Button이 상위 클래스의 click 함수를 오버라이드하는 경우를 생각해보자.
```kotlin
open clss View {
    open fun click() = println("View clicked")
}

class Button : View() {
    override fun click() = println("Button clicked")
}
```
click 메소드를 Button 클래스가 오버라이드 했다면 실제로는 Button 이 오버라이드한 click가 호출된다.
```kotlin
>>> val view: View = Button()
>>> view.click()
Button clicked
```
하지만 확장함수는 이렇게 동작하지 않는다.
확장함수는 호출할 때 수신 객체로 지정한 변수의 정적 타입에 의해 어떤 확장 함수가 호출될지 결정된다. 
```kotlin
fun View.showOff() = println("I'm a view")
fun Button.showOff() = println("I'm a button!")
>>> val view: View = Button()
>>> view.showOff()
I'm a view

```
위 경우에는 view 타입이 View 이기 떄문에 무조건 View의 확장 함수가 호출된다.  
수신 객체를 인자로 하는 자바 메소드로 컴파일 된다는 것을 기억한다면 당연한 동작이다.
```java
/* 자바 */
>>> View view = new Button();
>>> ExtensionsKt.showOff(view);
I'm a view
```
추가로 확장 함수와 동일한 이름의 멤버 함수가 있다면 멤버 변수를 호출한다.  
<br>

#### 3.3.5 확장 프로퍼티
확장 프로퍼티를 사용하면 기존 클래스 객체에 대한 프로퍼티 형식의 구문으로 사용할 수 있는 API를 추가할 수 있다.  
확장 프로퍼티는 뒷받침하는 필드, 즉 값을 저장해두는 필드가 없기때문에 기본 게터 구현을 꼭 정의해야 한다.
```kotlin
val String.lastChar: Char
    get() = get(length - 1) 
```
StringBuilder 맨 마지막 문자는 변경 가능하므로 프로퍼티를 var 로 만들 수 있다.
```kotlin
var StringBuilder.lastChar: Char
    get() = get(length - 1)
    set(value: Char) {
        this.setCharAt(lemgth - 1, value)
    }

>>> println("Kotlin".lastChar)
n
>>> val sb = StringBuilder("Kotlin?")
>>> sb.lastChar = '!'
>>> println(sb)
Kotlin!
```
<br>

### 3.4 컬렉션 처리: 가변 길이 인자, 중위 함수 호출, 라이브러리 지원
