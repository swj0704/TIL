# Coroutine

## Coroutine을 공부하게 된 계기
RxJava를 사용하며 안드로이드 개발을 하던 중 코루틴은 그거 훨씬 쉽게 구현 가능한데? 라는 말을 듣고 RxJava도 초반반 잘 넘기면 쓸만한데 이보다 훨씬 쉽다고? 라는 생각이 들어 공부하게 되었다.   
~~정말 엄청 쉽다~~

## Coroutine이란?
먼저 코루틴은 코틀린만의 것이 아니다, 많은 언어에 이미 코루틴이 존재한다.   
그리고 코루틴의 가장 핵심적인 개념은 협력형 멀티태스킹이다. 이를 이해한다면 코루틴을 거의 다 이해했다는 것이나 마찬가지이다.   
코루틴은 영어로 협력이라는 의미를 가진 접두어인 Co, Routine은 태스크, 함수를 말한다. 즉, 협력하는 함수다. 그렇기에 정확하게 이해하기 위해서는 routine을 이해할 수 있어야한다.

### Routine?
Routine에는 흔히 알고 있는 Main Routine과 Sub Routine이 있다.    
코드로 보면,
```kotlin
fun main(){
    checkValue(10)
}

fun checkValue(value : T?) : Boolean{
    val check = if(value != null){
        true
    } else {
        false
    }

    return check
}
```

아주 우리가 흔히 짜는 코드들이다   
위 코드는 메인 함수에서 checkValue를 하는 코드이다. 여기서는 메인 함수가 Main Routine이며, checkValue가 sub Routine이 된다.   
이 Sub Routine에는 한가지 특징이 있다.

위 코드에서도 보면 메인 함수에서 checkValue를 호출하게 된다
이렇게 되면 checkValue 즉 Sub Routine로 진입을 하게 된다 그리고 return을 만나면 해당 함수는 종료된다.   
이를 통해 Sub Routine의 특징을 알 수 있게 된다. Sub Routine은 진입점과 탈출점이 있다는 점이다.

근데 코루틴은 다르다
코루틴도 Routine이기 때문에 진입점과 탈출점이 존재한다. 그러나 코루틴은 진입점과 탈출점이 여러개이다.
물론 return이 여러개라는 말은 아니다.   
즉, 코루틴은 함수 중간에 언제든지 나갔다가 그 나간 지점에 다시 들어올 수 있다. 

코드로 봐보자

```kotlin
fun checkCoroutine(db : DB){
    CoroutineScope.launch(Dispatchers.IO){
        getDBText(db)
        getDBImage(db)
    }
}

suspend fun getDBText(db : DB) : String{
    delay(2000)
    return db.getText()
}

suspend fun getDBImage(db : DB) : String{
    delay(3000)
    return db.getImageUrl()
}
```

위 코드에서 checkCoroutine에서 coroutine을 사용했다   
코루틴을 사용하면서 getDBText를 호출을 했는데, delay(2000)을 만나게 되면서
2초를 대기해야하는데, 코루닡은 이때 getDBText를 빠져나온다! 그리고 Coroutine 안의 다음 코드를 실행하게 된다. 이때 getDBText는 delay인 상태에서 getDBImage도 실행하게 된다 그러다가 getDBText의 delay가 끝나면 다시 getDBText의 `return db.getText()`를 실행하고, db.getText도 suspend 함수라면 탈출 했다가 다시 들어와서 리턴을 하고, 아니라면 바로 리턴을 하게 된다.

### 동시성 프로그래밍?
위에서 설명한 진입점과 탈출점이 많다는 특징은 Coroutine이 동시성 프로그래밍이 가능하다는걸 알려준다.   
즉, 코루틴은 동시성 프로그래밍을 지원한다   
대부분은 이를 도화지 두장에 그림 그리는 것으로 설명을 하는데, 이보다 좋은 설명 방법은 없다. 그니까 이로 설명을 해보겠다   
도화지 두장이 있는데 한 사람에게 펜을 하나만 주고 그림을 그린다 할때,
동시성 프로그래밍은 왼쪽 도화지에 조금, 오른쪽 도화지에 조금씩 그리는 이것을 아주 빠르게 반복하는 것이다.   
물론 한 시간대에 그림은 하나만 그려지고 있다. 하지만 속도가 워날 빠르기에 동시에 그려지는 것처럼 보이는 것이다.   
동시성 프로그래밍을 병렬성 프로그래밍과 비교하기도 하는데, 이 비교는 도화지로도 가능하지만 다른 설명 방법으로 해보겠다.   
쉐프가 한사람 있는데, 이 한사람이 두가지 요리를 한다 할때, A요리를 하다가 B요리를 하는것이 위의 도화지와 유사한 방법일것이다. 그러나 병렬성 프로그래밍은 쉐프 두사람이 각각 한 요리를 맡아 두 요리를 한번에 진행하는 것이다. 코드로 치면 쓰레드 두개가 각각 UI 그리는 작업, 네트워킹 작업을 맡는것과 유사하다고 볼 수 있다.   
~~바로 윗줄의 코드 예시가 맞는지 잘 모르겠습니다 혹시 아시는 분 있으시면 이슈로 달아주시면 너무나 감사하겠습니다~~

## 비동기 처리가 아주 쉬운 Coroutine
코루틴은 비동기 처리가 아주아주아주 쉽다!
RxJava.md에서 보던 코드를 코루틴으로 적으면 진짜 이게 된다고? 라는 생각이 들정도이다. 그럼 한번 봐보자
```kotlin
//CallBack
fun 회사가기(){
    val 잠든_사람 = 사람()
    일어나기(잠든_사람){ 잠깨서_졸린_사람 ->
        씻기(잠깨서_졸린_사람){ 씻은_사람 ->
            옷입기(씻은_사람){ 깔끔한_사람 ->
                출발(깔끔한_사람){ 출발한_사람 ->
                    출근(출발한_사람){ 출근한_사람 ->
                        일하기(출근한_사람)
                    }
                }
            }
        }
    }
}
```

```kotlin
//RxJava
fun 회사가기(){
    val 잠든_사람 = 사람()
    Observable
        .just(잠든_사람)
        .flatmap { 잠든_사람 -> 일어나기(잠든_사람) }
        .flatmap { 잠깨서_졸린_사람 -> 씻기(잠깨서_졸린_사람) }
        .flatmap { 씻은_사람 -> 옷입기(씻은_사람) }
        .flatmap { 깔끔한_사람 -> 출발(깔끔한_사람) }
        .flatmap { 출발한_사람 -> 출근(출발한_사람) }
        .subscribe({ 출근한_사람 ->
            알하기(출근한_사람)
        }, {
            에러처리()
        })
}
```

callback은 그냥 보기 어렵고, RxJava는 Rx를 모르는 사람이 보면 약간은 어려울 수 있다. 근데 코루틴은 정말 쉽다
```kotlin
suspend fun 회사가기(){
    val 잠든_사람 = 사람()
    try{
        val 잠깨서_졸린_사람 = 일어나기(잠든_사람)
        val 씻은_사람 = 씻기(잠깨서_졸린_사람)
        val 깔끔한_사람 = 옷입기(씻은_사람)
        val 출발한_사람 = 출발(깔끔한_사람)
        val 출근한_사람 = 출근(출발한_사람)
        일하기(출근한_사람)
    } catch(e : Exception){
        에러처리()
    }
}
```
이게 정말 비동기 코드라고? 생각할 수 있다. 근데 정말 비동기 코드이다.
suspend fun은 kotlin에서 코루틴의 subRoutine을 만드는 키워드이다. 그리고 일어나기, 씻기 등 사람이 하는 동작들은 모두 지연시간이 있는 함수들이다.   
그리고, 회사가기 함수도 지연시간이 있는 코루틴 함수이기에, 일어나기 함수를 실행하면 회사가기 함수를 잠시 탈출 했다가 끝나면 다시 진입한다.


## Coroutine을 어떤 Thread에서 실행할지 결정하는 Dispatcher
위 내용은 코루틴에 대해서만 관련한 내용이었다면, 이번엔 Coroutine을 배우며 알게 된 Dispatcher에 관해 적어보고자 한다   
먼저 Dispatch는 '보내다' 라는 뜻이다. 우리가 배우는 Dispatcher는 Coroutine을 Thread에 보낸다. 즉, Dispatcher는 자신이 관리하는 Thread에서 Thread의 부하상태에 맞게 Coroutine을 분배한다. 

### Android Coroutine의 Dispatcher?
안드로이드에서는 Dispatcher가 만들어져있다.
위 코드에서도 Dispatchers.IO가 사용된 코드가 있는데, 이는 네트워크 작업을 하는데 최적화 된 Dispatcher라는 뜻이다.

그럼 안드로이드의 Dispatcher들에 대해 알아보자

- Dispatchers.Main : Android의 메인 스레드에서 코루틴을 실행하는 디스페처이다. Android의 메인 스레드는 네트워킹 작업과 같이 오래 걸리는 작업을 하게 되면 ANR이 발생하기 때문에, 이 Dispatcher로는 UI와 상호작용 하는 작업을 위해서만 사용해야한다
- Dispatchers.IO : Android의 네트워킹 작업이나 DB작업과 같은 I/O 작업에 최적화 되어있는 Dispatcher이다.
- Dispatchers.Default : CPU를 많이 사용하는 작업을 기본 스레드 외부에서 실행하도록 하는 디스패처이다. 정렬작업아니 Json 파싱 작업에 최적화 되어있다.

내 경험에 LiveData의 SetValue 함수는 Dispatchers.IO에서는 에러가 난다. LiveData를 data Binding와 사용하게 되면 LiveData의 value가 바뀔때 UI가 바뀌기 때문이다. 그렇기 때문에, 시간이 오래걸리는 작업은 Dispatchers.IO에서 하고, liveData에 value를 set 해주는 코드는 Dispatchers.Main에서 진행해야 한다.

```kotlin
fun getText(){
    viewModelScope.launch(Dispatchers.IO){
        val response = api.getText()
        withContext(Main){
            _text.value = response.text
        }
    }
}
```

LiveData의 값을 변경하는 또다른 방법은 Postvalue가 있다.   
PostValue는 setValue와 다르게 백그라운드 스레드에서 진행하게 되어있어 Dispatchers.IO일때도 에러가 나지 않는다.

```kotlin
fun getText(){
    viewModelScope.launch(Dispatchers.IO){
        val response = api.getText()
        _text.postValue(response.text)
    }
}
```

위와 같이 진행해도 에러는 나지 않는다.
다만 postValue의 경우에는 liveData.getValue() 에서 바로 값을 읽어오지 못할 가능성도 있기 때문에, liveData의 value를 써야한다면 withContext를 사용한 위의 방법을 사용하는 것이 좋다.