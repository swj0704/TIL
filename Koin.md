# Koin

~~Koin을 공부한 이유는 DI 참고...~~   

## 안드로이드에서 DI
안드로이드에서는 Context에 따라 다르게 동작하는 경우가 있다   
하지만 DI를 주입받아 사용하면 공통의 재사용 가능한 객체가 생성된다.   

~~볼수록 싱글톤과 다를게...~~ 

안드로이드에서는 두 가지 방식의 의존성 주입이 가능하다.

생성자 주입 (Constructor Injection) : 생성자를 통해 의존하는 객체 전달   
필드 주입 (Field Injection) : 객체가 초기화된 후에 의존하는 객체 전달

~~뭐 그렇다고 한다~~

그럼 Koin을 자세히 알아보자


## Koin?
안드로이드 DI 라이브러리로는 Koin, Dagger 그리고 최근에 떠오르는 Hilt가 있다. 
~~Hilt는 나중에 알아보자~~ 그 중에 Koin은 Kotlin에 최적화된 경량화 DI 라이브러리이다. 

## 사용하기
### 설치
먼저 build.gradle에 설치를 해보자

```gradle
dependencies {
    implementation "io.insert-koin:koin-core:3.3.3"
    testImplementation "io.insert-koin:koin-test:3.3.3"       //만약 테스트를 하고자 한다면 추가하자
    implementation "io.insert-koin:koin-androidx-compose:3.4.2"     //compose 앱은 이걸로 추가하면 된다
}
```

### 모듈 선언
모듈 선언을 위해 주입 받을 객체를 만들어보자
```kotlin
interface TextRepository{
    fun getText() : String
}

class TextRepositoryImpl : TextRepository {
    override fun getText() : String = "Hello Koin!"
}
```
이제 repository를 주입받을, 그리고 액티비티에 주입될 뷰모델을 만들어보자
```kotlin
class TextViewModel(val repository : TextRepository) : ViewModel(){
    private val _repoText = MutableLiveData<String>()
    val repoText : LiveData<String>
        get() = _repoText
    fun getText(){
        _repoText.value = repository.getText()
    }
}
```
생성된 뷰모델과 레포지토리를 이용해서 모듈을 만들어보자
```kotlin
val appModule = module {
    single<TextRepository> { TextRepositoryImpl() }     //객체 생성은 TextRepositoryImpl로 하지만 실제 타입은 TextRepository
    //single은 싱글톤 객체로 해당 객체를 생성한다.
    //factory<TextRepository> { TextRepositoryImpl() }
    //factory는 요청할때마다 새로운 객체를 생성한다.
    viewModel{ TextViewModel(get()) }
    //get()은 주입받는 타입의 모듈에서 생성된 객체를 찾아 주입해준다 만약, 해당 타입 객체가 생성되지 않았다면 해당 부분을 실행해야 에러를 알 수 있다.
    //viewModel은 viewModel 키워드로 선언해야한다.
}
```

~~이제 싱글톤과 다른 점이 보이는거 같다~~

위의 주석에서 get()에 대한 주석에 보면 
`해당 타입 객체가 생성되지 않았다면 해당 부분을 실행해야 에러를 알 수 있다.`
이 부분이 Koin의 가장 큰 문제점이라 생각한다.   
원래 Di에서는 타입의 변수를 직접 넣기 때문에 컴파일 과정에서 에러를 알수 있지만 Koin은 get() 함수가 제네릭 T이기 때문에 `get<Type>` 으로 가져온다고 해도 에러는 실행을 해봐야 알 수밖에 없다

### 모듈 등록
이제 Koin에 모듈들을 등록해보자

Application을 상속받은 클래스에 onCreate에서 startKoin의 modules를 사용하여 등록하면 된다

```kotlin
class SampleApplication : Application(){
    override fun onCreate(){
        super.onCreate()
        startKoin{
            androidContext(this@SampleApplication)      //앱의 context를 해당 application을 사용하도록 하는
            modules(appModule)
            //modules(listOf(appModule))        모듈이 여러개인 경우 리스트로 한번에 넣어줄 수 있다.
        }
    }
}
```
이렇게 Application을 만들어주었다면 manifest에 등록해주어야 한다
```xml
<application
    ...
    android:name".SampleApplication">
```

### 의존성 주입받기

변수를 의존성 주입받는 방법은 정말 쉽다.
```kotlin
    val repository : TextRepository by inject()
    val repository : TextRepository by inject(TextRepository::class.java)
    val viewModel : TextViewModel by viewModel()
```
~~와 너무 쉬워요~~

### Koin의 장단점

이렇게 Koin을 간단하게 알아보았는데, 개인적인 장단점을 찾아보자면
#### 장점
- 러닝커브가 낮아 빠르게 DI를 적용할 수 있다
- 어노테이션을 사용하지 않아 컴파일 시간이 적다
- 뷰모델 주입이 쉽다.
#### 단점
- 컴파일에서 `get()`의 타입 에러가 잡히지 않아 그 부분을 실행을 해야만 에러를 확인할 수 있다.
- `Java`에서 적용이 안된다

~~너무 쉽고 가벼워서 사실 `get()` 문제만 해결 되면 좋은 라이브러리 같다~~