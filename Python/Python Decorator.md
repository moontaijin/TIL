# Python Decorator

## 사전 지식

1. **일급 객체**
   * 함수 내에 함수를 정의할 수 있다.
   * 변수에 할당할 수 있다.
   * 함수를 인자로 전달할 수 있다.
   * 함수의 반환값이 될 수 있다.
2. **클로저**
   * 내부 함수가 외부 함수의 인자를 기억하고 있다.
3. **args(위치인자)와 kwargs(키워드인자)**
   * 위치인자 = 함수에서 정의한 위치대로 대입하는 방식
   * 키워드 인자 = 함수에서 정의한 키워드에 대입을 하는 방식, 인자 순서를 무시하고 입력 가능



## Decorate 개념

데코레이터(Decorator)란 기존에 정의된 함수의 능력을 확장할 수 있게 해주는 함수로서 @를 이용하여 사용한다.



## Decorator를 언제 사용해야 할까

* 기존 함수에 기능을 추가하고, 새로운 함수를 만드는 역할
* 어떤 동작을 함수의 전/후에 수행해야 하거나, 공통적으로 사용하는 코드를 쉽게 관리하기 위해 사용



## Decorator 문법(Syntax)

데코레이터의 문법을 이해하기 위해 우선 같을 역할의 함수를 만들어보며 이해해보자.

```python
class Greet(object):
    current_user = None
    def set_name(self, name):
        if name == 'admin':
            self.current_user = name
        else:
            raise Exception("권한이 없네요")

    def get_greeting(self, name):
        if name == 'admin':
            return "Hello {}".format(self.current_user)

greet = Greet()
greet.set_name('eunwoo')
```

위 코드에서 전달받은 ```name```인자가 "admin"일 떄만 수행하는 부분들을 갖고있다.

공통적으로 사용하는 부분을 따로 때어내어 함수화 시키면,

```python
def is_admin(user_name):
    if user_name != 'admin':
        raise Exception("권한이 없다니까요")

class Greet(object):
    current_user = None
    def set_name(self, name):
        is_admin(name)
        self.current_user = name

    def get_greeting(self, name):
        is_admin(name)
        return "Hello {}".format(self.current_user)

greet = Greet()
greet.set_name('admin')
greet.get_greeting('eunwoo')
```

위와 같이 나타낼 수 있다.

이를 다시 데코레이터를 이용하여 나타내면

```python
def is_admin(func):
    def wrapper(*args, **kwargs):
        """
        decorate
        """
        if kwargs.get('username') != 'admin':
            raise Exception("아 진짜 안된다니까 그러네..")
        return func(*args, **kwargs)
    return wrapper

class Greet(object):
    current_user = None

    @is_admin
    def set_name(self, username):
        self.current_user = username

    @is_admin
    def get_greeting(self, username):
        """
        greeting

        :param username: 이름
        :type username: string
        """
        return "Hello {}".format(self.current_user)

greet = Greet()
greet.set_name(username='admin')
greet.get_greeting(username='admin')
```

위처럼 나타낼 수 있다.

이 때, 데코레이터를 만들게 되면 원래 함수의 속성들이 사라지는 문제점이 발생하게 되는데 

이럴경우 데코레이터 내부에서 인자로 전달받은 함수가 [익명함수(람다함수)]([https://github.com/moontaijin/TIL/blob/master/Python/%ED%8C%8C%EC%9D%B4%EC%8D%AC%20%EC%9A%A9%EC%96%B4%20%EC%A0%95%EB%A6%AC.md](https://github.com/moontaijin/TIL/blob/master/Python/파이썬 용어 정리.md))처럼 취급되어 버리므로 디버깅이 난해해지는 단점이 생긴다.

이것을 보완하기 위해 ```fucntools``` 모듈에는 데코레이터를 위한 데코레이터 ```@wraps```가 있다.

```python
from functools import wraps

def is_admin(func):
    @wraps(func)
    def wrapper(*args, **kwargs):
        if kwargs.get('username') != 'admin':
            raise Exception("아 진짜 안된다니까 그러네..")
        return func(*args, **kwargs)
    return wrapper

class Greet(object):
    current_user = None

    @is_admin
    def set_name(self, username):
        self.current_user = username

    @is_admin
    def get_greeting(self, username):
        """
        greeting

        :param username: 이름
        :type username: string
        """
        return "Hello {}".format(self.current_user)


greet = Greet()
greet.get_greeting.__name__
greet.get_greeting.__doc__
```

메소드의 ```__name__```, ```__doc__``` 속성을 확인해보면 , 함수의 속성이 나오게 된다.

```python
greet.get_greeting.__name__
# 'wrapper'

greet.set_name.__doc__
# '\n        decorate\n      
```



## 데코레이터에게 인자 넘기기

데코레이터에도 추가 인자를 넘겨줄 수 있다.

이때는 데코레이터를 정의하는 것이 아니라 데코레이터를 만들어주는 함수를 정의한다고 볼 수 있다.

```python
def add_tags(tag_name):
    print("Gernerate decorator")
    def set_decorator(func):
        def wrapper(username):
            return "<{0}>{1}</{0}>".format(tag_name, func(username))
        return wrapper
    return set_decorator

@add_tags("div")
def greeting(name):
    return "Hello " + name

print greeting("Jonnung")
```

> 실행결과

```python
Gernerate decorator
<div>Hello Jonnung</div>
```



## 클래스(class)로 데코레이터 만들기

```python
class Sample(object):
    def __init__(self):
        print("init")
    def __call__(self):
        print("call")


sample = Sample()
# init

sample()
# call
```

위 코드에서 알 수 있듯, 클래스의 인스턴스를 함수처럼 호출하기 위해서는 클래스에 ```__call__```이라는 매직 메소드를 정의해야한다.

이 원리를 이용하여 클래스를 데코레이터로 구현할 수 있다.

```python
from functools import wraps

class OnlyAdmin(object):
    def __init__(self, func):
        self.func = func

    def __call__(self, *args, **kwargs):
        name = kwargs.get('name').upper()
        self.func(name)

@OnlyAdmin
def greet(name):
    print("Hello {}".format(name))


greet(name='Eunwoo')
```

