# Chapter 8

breakpoint는 address pointer를 실행할 때를 모니터링 할 수 있지만, 메모리가 읽거나 쓸 때를 모니터링 할 수 없다. Swift object가 heap이 되거나, 특정 메모리 주소를 읽고 있는지를 알 수 없다. 이는 watchpoint로 알 수 있지만, 아키텍쳐 마다 watchpoint가 가능한 메모리 크기가 정해져있다.

watchpoint는 아래와 같은 것들을 할 수 있다.

- allocated된 Swift/Objective-C object의 property를 추적할 수 있다. direct ivar access, Objective-C stter method, Swift property setter method, hardcoded offset access 등등...
- print/printf/NSLog/cout 같은 function call에서 hardcoded string을 모니터링
- assembly instruction을 모니터링

이를 위해서는 property의 offset를 알아내야 한다.

### property의 offset을 찾아내기

```
(lldb) language objc class-table dump UnixSignalHandler -v
isa = 0x107174c28 name = UnixSignalHandler instance size = 56 num ivars = 4 superclass = NSObject
  ivar name = source type = id size = 8 offset = 24
  ivar name = _shouldEnableSignalHandling type = bool size = 1 offset = 32
  ivar name = _signals type = id size = 8 offset = 40
  ivar name = _sharedUserDefaults type = id size = 8 offset = 48
  instance method name = setShouldEnableSignalHandling: type = v20@0:8B16
  instance method name = setSharedUserDefaults: type = v24@0:8@16
  
# 생략...
```

`_shouldEnableSignalHandling`을 보면, offset은 32, size는 1 byte 임을 알 수 있다. 이는 UnixSignalHandler의 메모리 위치 (0x107174c28 아님!)에 32를 더하면 `_shouldEnableSignalHandling`의 위치가 나온다.

![](images/13.png)

UnixSignalHandler의 메모리 위치는 0x60000189f600 이므로

```
(lldb) p/x 0x60000189f600 + 32
(long) $0 = 0x000060000189f620
```

0x000060000189f620이 `_shouldEnableSignalHandling`의 주소임을 알 수 있다. 이제 이 ivar를 watchpoint 해보자.

```
(lldb) watchpoint set expression -s 1 -w write -- 0x000060000189f620
Watchpoint created: Watchpoint 1: addr = 0x60000189f620 size = 1 state = enabled type = w
    new value: 0
```

`-s` : size (아까 1 byte라고 했으므로)

`-w` : ivar에 set 할 때만은 `write`. 또는 `read`, `read-write`

그러면 아래 사진처럼 해당 ivar에 set을 할 때 watchpoint가 발동한다!

![](images/14.png)

