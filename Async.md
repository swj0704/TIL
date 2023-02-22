# Async

## 비동기 처리를 공부하게 된 계기
Android에서 Retrofit을 사용하면 call.enqueue 라는 함수를 통해 Retrofit의 call만 비동기 처리를 해줄 수 있다. 그러나 다른 네트워킹이나 DB 작업, 예를 들어 Room 같은 경우에는 메인 쓰레드에서 작업을 하게 되면 에러가 난다. 물론 새 Thread를 여는 방법도 있지만 리소스를 많이 차지하기 때문에 비효율적인 방법이라 생각하여 비동기 처리에 대해 공부해보게 되었다.

## 동기 vs 비동기
먼저 동기와 비동기 차이를 알아봐야 하는데, 동기는 영어로 Synchronous라 한다 사전 그대로 말하면 동시성의라는 의미인데, 요청과 결과가 동시에 일어나는 작업이다.    
~~비동기 처리가 동시에 여러 작업을 한다는 말 때문에 어 그럼 비동기가 동시성 아닌가 라고 헷갈리기도 했다~~   
반대로 비동기는 요청을 보내면 결과가 일어나기 전까지 기다리며, 기다리는 동안 다른 작업을 수행할 수 있다. ~~이게 동시성 아닌가 싶다~~   
안드로이드에서는 UI Thread라는 메인 쓰레드가 UI 작업을 관리하고 처리한다. 즉, UI Thread는 항상 막힘 없이 UI를 업데이트 해야하는데, 만약 UI Thread에 네트워킹이나 DB 작업을 하게 된다면, ANR(Application Not Responding) 상태가 되어 앱이 튕기게 된다. 따라서 Android에서 네트워킹이나 DB 작업을 하려면 비동기가 필수이다.

## Android 의 비동기 처리를 위한 방법
Android에서는 RxJava, RxKotlin, Coroutine, AsyncTask 등이 있는데, AsyncTask 는 deprecated 되고 크게 Rx 계열과, Coroutine이 있다.   
~~라이브러리들의 콜백은 별개인가보다... Retrofit만 봐도...~~