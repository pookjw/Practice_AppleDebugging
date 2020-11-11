# Chapter 9

lldb의 설정값을 `.lldbinit`에 저장해서 유지(persist)하자

### Persist?

lldb가 실행되면, lldb는 여러 directories를 검색해서 init 파일을 찾는다. 파일이 발견되면, attach하기 전에 불러온다.

lldb는 아래와 같은 규칙으로 init 파일을 찾는다.

1. `~/.lldbinit-[context]` : Terminal에서 lldb를 실행하면 `~/.lldbinit-lldb`, Xcode라면 `~/.lldbinit-lldb-Xcode`.
2. `~/.lldbinit`
3. `/`

## .lldbfile 생성 & alias

```bash
$ nano ~/.lldbinit
```

그리고 아래 alias 명령어를 추가

```
command alias -- Yay_Autolayout expression -l objc -O -- [[[[[UIApplication sharedApplication] keyWindow] rootViewController] view] recursiveDescription]
```

그러면 `Yay_Autolayout`를 입력하면 `expression -l objc -O -- [[[[[UIApplication sharedApplication] keyWindow] rootViewController] view] recursiveDescription]`가 실행된다!

```
(lldb) Yay_Autolayout
<_UISplitViewControllerPanelImplView: 0x7ff2d1d05b20; frame = (0 0; 428 926); autoresize = W+H; gestureRecognizers = <NSArray: 0x600003c38090>; layer = <CALayer: 0x600003279300>>
   | <_UIPanelControllerContentView: 0x7ff2d1d0a0e0; frame = (0 0; 428 926); autoresize = W+H; layer = <CALayer: 0x6000032793e0>>
   |    | <UILayoutContainerView: 0x7ff2d1c110d0; frame = (0 0; 428 926); clipsToBounds = YES; autoresize = W+H; gestureRecognizers = <NSArray: 0x600003c57a50>; layer = <CALayer: 0x600003219ee0>>
   
# 생략...
```

### with arguments...

alias로 만든 명령어 뒤에 arguments도 지원한다!

```
command alias cpo expression -l objc -O --
```

```
(lldb) po self
<Signals.MasterContainerViewController: 0x7f8d8e508840>

(lldb) cpo 0x7f8d8e508840
<Signals.MasterContainerViewController: 0x7f8d8e508840>
```

