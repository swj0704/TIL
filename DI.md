# DI

## DI를 공부하게 된 계기
고등학교 2학년 때 3개교 해커톤에 나가게 되었는데 다른 학교 친구들과 안드로이드 개발을 하면서 DI, MVVM과 같은 디자인 패턴, 라이브러리 사용법 등에서 부족한 점을 느끼게 되었고, DI를 사용한 코드의 예시를 보는데 기존 코드에서 불편한 점을 해결해줄 수 있을 것 같았고, 안드로이드 DI 라이브러리 중 가장 러닝커브가 낮은 Koin을 공부하게 되었다.   
~~다른 공부들은 스스로 찾아서 해보자~~

## DI
DI는 (Dependency Injection) 의존성 주입의 줄임말이다   

## 의존성?
A가 B에 의존한다 라 했을때,   
B가 수정된다면? 당연히 A에도 영향이 있다.   
쉽게 음식으로 비유를 하자면,   
음식은 레시피에 의존한다  
```kotlin
class Recipe{
    val time = 1
}

class Food{
    val recipe = Recipe()

    var makingTime = 0

    fun makeFood(){
        makingTime += recipe.time
    }
}
```
레시피가 변경 되면? 당연히 음식도 바뀌게 될것이다.   
만약 레시피가 여러가지가 나와서 Recipe를 인터페이스로 바꾼다면?

```kotlin
interface Recipe{
    val time : Int
}

class BreadRecipe{
    override val time = 1
}

class SoupRecipe{
    override val time = 5
}

class Food{
    val recipe = Recipe() //Error!

    var makingTime = 0

    fun makeFood(){
        makingTime += recipe.time
    }
}
```
위와 같이 Food는 Recipe에 의존하고 있어 에러가 나게 된다   
그렇다면 의존성을 주입해보는 방식을 사용해보자

```kotlin
class Food(val recipe : Recipe){
    var makingTime = 0

    fun makeFood(){
        makingTime += recipe.time
    }
}
```
위 방법대로 하게 되면 에러가 발생하지 않는 정상적인 코드가 완성된다


## 그래서 왜 DI를 써야 하는가?
우선 알려진 이유는 다음과 같다.   
- 코드의 가독성을 높여준다.
- Unit Test 가 쉬워진다.
- 코드의 재활용성을 높인다.
- 객체 간의 의존성(종속성)을 직접 설정하여 줄이거나 없앨 수 있다.
- 객체 간의 결합도를 낮추면서 유연하게 만들 수 있다.

직접 사용해보면서 느끼기에는 객체간의 결합도를 낮춘다는 점이 가장 크게 느껴지는것 같다.  
```kotlin
class Food(val recipe : Recipe)
``` 
위의 예시를 보면 Recipe 객체는 어떻게 변경 되어도 time이 number이기만 하면 수정사항은 없기 때문이다. 