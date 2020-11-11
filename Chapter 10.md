# Chapter 10

[Chapter 9](Chapter 9.md)에서 `alias`를 배웠는데, 이거는 명령어를 대체하지만 명령어 중간에 껴넣는 것은 지원하지 않아서, **Regex Commands**를 써야 한다.

## regex

`alias`와 비슷하지만, `regex`는 Regular Expression을 제공한다. 아래가 일반적인 문법이다.

```
s/<regex>/<subst>/
```

`s/`로 시작하고 (substitute command), `<regex>`는 대체될 문구, `<subst>`는 대체할 문구다. 예를 들어 아래 명령어를 실행할 경우 (참고로 아래 명령어를 `.lldbinit`에 넣으면 유지된다.)

```
(lldb) command regex rlook 's/(.+)/image lookup -rn %1/'
```

`rlook`이라는 regex가 생긴건데, `rlook`를 실행해보면

```
(lldb) rlook F00
```

이 명령어는 아래와 같다.

```
(lldb) image lookup -rn F00
```

중간에 명령어를 넣을 수 없던 `alias` 와는 다른 점을 보인다. 사실 이는 `rl`이라는 명령어로 기본적으로 제공하고 있다.

```
(lldb) rl viewDidLoad
(lldb) rl viewDidLoad Signals
```

### 복잡하게

```
(lldb) command regex -- tv 's/(.+)/expression -l objc -O -- @import QuartzCore; [%1 setHidden:!(BOOL)[%1 isHidden]]; (void)[CATransaction flush];/'
```

```
(lldb) tv [[[UIApp keyWindow] rootViewController] view]
```

이러면 `rootViewController`의 view의 hidden 상태를 toggle 할 수 있다!

### 더 복잡하게

```
(lldb) command regex getcls 's/(([0-9]|\$|\@|\[).*)/cpo [%1 class]/'
```

설명하자면, argument가

- `[0-9]` : 0부터 9 사이일 경우
- `\$` : 문자 `$`가 포함되어 있을 경우

- `\@` : 문자 `@`가 포함되어 있을 경우
- `\[` : 문자 `[`가 포함되어 있을 경우

이렇게 새로 만든 `getcls`를 실행해보면

```
(lldb) getcls @"hello world"
__NSCFString

(lldb) getcls @[@"hello world"]
__NSSingleObjectArrayI

(lldb) getcls [UIDevice currentDevice]
UIDevice

(lldb) cpo [UIDevice currentDevice]
<UIDevice: 0x600001dec720>

(lldb) getcls 0x600001dec720
UIDevice
```

하지만 `self`는 오류난다.

```
(lldb) getcls self
error: getcls
```

왜냐하면 `getcls`에 이 부분을 지원하도록 정의를 안해줬기 때문이다. 저렇게 오류가 나면 catch-all을 넣어주자.

```
(lldb) command regex getcls 's/(([0-9]|\$|\@|\[).*)/cpo [%1 class]/' 's/(.+)/expression -l swift -O -- type(of: %1)/'
```

그러면 된다!

```
(lldb) getcls self
Signals.MasterViewController

(lldb) getcls self .title
Swift.Optional<Swift.String>
```

두번째꺼 보면 self랑 .title 사이에 띄어쓰기가 있는데, newline을 제외하고는 대충 알아먹는다고 한다.

### multiple parameters

```
(lldb) command regex swiftperfsel 's/(.+)\s+(\w+)/expression -l swift -O -- %1.perform(NSSelectorFromString("%2"))/'
(lldb) swiftperfsel UIApplication.shard statusBar
error: swiftperfsel
```

`\s+`로 여러개 만드는듯? 근데 iOS 14 이후로 statusBar 접근이 막혀서 오류난다.

```
(lldb) ex -l swift -O -- UIApplication.shared.perform(NSSelectorFromString("statusBar"))
2020-11-12 01:01:09.028280+0900 Signals[5857:184641] *** Assertion failure in -[UIApplication _createStatusBarWithRequestedStyle:orientation:hidden:], UIApplication.m:5117
error: Execution was interrupted, reason: internal ObjC exception breakpoint(-5)..
The process has been returned to the state before expression evaluation.
```

## 마치며

이제 **Section II: Understanding Assembly** 의 세계로 떠나자