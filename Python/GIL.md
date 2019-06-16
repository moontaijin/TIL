# GIL

GIL이란 [Global Interpreter Lock](http://en.wikipedia.org/wiki/Global_Interpreter_Lock)의 약자로 여러개의 쓰레드가 있을떄 쓰레드간의 동기화를 위해 사용되는 기술 중 하나이다. GIL은 전역에 lock을 두고 이 lock을 점유해야만 코드를 실행할 수 있도록 제한한다. 따라서 동시에 하나 이상의 쓰레드가 실행되지 않는다. 



## 역사적 배경과 존재의 이유

GIL은 처음 파이썬에 쓰레드 구현이 들어갈 때 같이 생긴 이후로, 지금까지 계속 내려오고 있다. 파이썬 구현은 상당 부분이 라이브러리 전역 변수에 의존하고 있고, 여기 저기 객체 구조에 따라서 참조를 따라서 마구 접근을 하기 때문에, 동시에 쓰레드가 여러 개 돌아갈 때 심각한 문제가 발생할 수 있다. 따라서, 파이썬은 초창기부터 파이썬 라이브러리 코드가 돌아가는 중에는 전역적인 락(GIL)을 걸어서 동시에는 절대로 같이 못 돌아가도록 해서 이 문제를 해결하였다.



#### thread가 1개만 돌아갈 때 생기는 일

![img](https://t1.daumcdn.net/cfile/tistory/99231D465A7BD9C81C)![img](https://t1.daumcdn.net/cfile/tistory/9923D8495A7BD9D406)



## GIL의 efficiency

 직관적으로 멀티코어에서도 코어를 하나밖에 사용 못 한다면 GIL을 사용해서 multi threads를 지원하는 것은 성능에 큰 문제가 있을 거라고 생각된다. 하지만 이는 대부분의 경우에 큰 문제가 되지 않는다. 정확히 말해서 프로그램은 대부분  I/O bound 이기 때문에 문제가 되지 않는다. I/O bound의 경우 대부분 시간을 I/O event를 기다리는 데 사용하기 때문에 event를 기다리는 동안 다른 thread가 CPU를 사용하면 된다.

 반대로 말해서 프로그램이 CPU bound인 경우에는 multi-threaded program을 작성해도 성능이 향상되지 않는다. 오히려 lock을 acquire하고 release하는 시간 때문에 성능이 떨어지기도 한다.





## GIL의 장점

 멀티 쓰레드 프로그램에서 성능이 떨어질 수도 있지만, CPython, PyPy, Ruby MRI, Rubinius, Lua interpreter 등 많은 인터프리터 구현체들이 GIL을 사용하고 있다. 그 이유는 우선 GIL을 이용한 multi-threads를 구현하는 것이 parallel 한 multi-threads를 **구현하는 것보다 훨씬 쉽다는 것이다.**

 게다가 이런 parallel 한 multi-threads 구현체들의 문제는 싱글 쓰레드에서 오히려 더 느려진다는 것이다. 그래서 CPython이나 Ruby MRI에서는GIL을 없애려는 많은 시도가 있었지만, 결국 싱글 쓰레드에서의 성능 저하를 극복하지 못하고 GIL로 돌아왔다. 결국, 파이썬의 창시자인 귀도 반 로섬은 CPython에서 GIL을 없애는 대신 싱글 쓰레드에서 성능을 떨어뜨릴 구현은 받아들이지 않겠다고 선언하기도 했다. 