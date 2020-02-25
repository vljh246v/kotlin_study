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
