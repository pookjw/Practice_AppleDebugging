# Chapter 1

### attach 및 breakpoint

```bash
$ lldb -n Finder
```

/dev/ttys027에서 Xcode 실행되고, pause해서 command를 날리고 싶으면 Ctrl + C

```bash
$ lldb
(lldb) file /Applications/Xcode.app/Contents/MacOS/Xcode
(lldb) process launch -e /dev/ttys027 --
Process 9243 launched: '/Applications/Xcode.app/Contents/MacOS/Xcode' (x86_64)

# Ctrl + C

(lldb) b -[NSView hitTest:]
(lldb) continue
Process 9243 resuming

# Xcode를 누르면 breakpoint가 걸림. continue를 해도 또 breakpoint가 걸리는 이유는 hit가 subviews에 모두 걸리기 때문

* thread #1, queue = 'com.apple.main-thread', stop reason = breakpoint 1.1
    frame #0: 0x00007fff22f48db8 AppKit`-[NSView hitTest:]
AppKit`-[NSView hitTest:]:
->  0x7fff22f48db8 <+0>: pushq  %rbp
    0x7fff22f48db9 <+1>: movq   %rsp, %rbp
    0x7fff22f48dbc <+4>: pushq  %r15
    0x7fff22f48dbe <+6>: pushq  %r14
Target 0: (Xcode) stopped.

# breakpoint가 걸린 지점 얻기 (po : print object)
(lldb) po $rdi
<NSThemeFrame: 0x132cb68d0>

# breakpoint 삭제
(lldb) breakpoint delete

# -[NSView hitTest:]가 호출될 때마다 명령어 (아래에서는 po $rdi) 출력하기
# G1 : breakpoint가 걸리고 자동으로 resume
(lldb) breakpoint set -n "-[NSView hitTest:]" -C "po $rdi" -G1

# 아래는 자동으로 호출됨...
(lldb)  po $rdi
<IDEEditorContainerViewControllerView: 0x139c91d30>


(lldb)  po $rdi
<NSView: 0x139c92a00>
# 등등...

# 이미 설정된 breakpoint에서, condition이 YES일 때만 breakpoint 걸리도록
# G0 : breakpoint가 걸리고 resume하지 않기
(lldb) breakpoint modify -c '(BOOL)[NSStringFromClass((id)[$rdi class]) containsString:@"IDESourceEditorView"]' -G0

# continue하면 미리 설정된 po $rdi가 실행되면서, 위 condition이 YES일 때만 설정되도록 나온걸 확인할 수 있음
(lldb)  po $rdi
IDESourceEditorView: Frame: (0.0, 0.0, 739.5, 900.0), Bounds: (0.0, 0.0, 739.5, 900.0) contentViewOffset: 0.0

# description
(lldb) p/x $rdi
(unsigned long) $257 = 0x0000000111243800
(lldb) po 0x0000000111243800
IDESourceEditorView: Frame: (0.0, 0.0, 739.5, 900.0), Bounds: (0.0, 0.0, 739.5, 900.0) contentViewOffset: 0.0

# 속성 변경 (IDESourceEditorView가 사라짐)
(lldb) po [$rdi setHidden:!(BOOL)[$rdi isHidden]]; [CATransaction flush]

# IDESourceEditorView의 내용 가져오기
(lldb) po [$rdi string]
//
//  ViewController.swift
//  Hello Debugger
//
//  Created by Jinwoo Kim on 10/25/20.
//

import UIKit

class ViewController: UIViewController {

    override func viewDidLoad() {
        super.viewDidLoad()
        // Do any additional setup after loading the view.
    }


}

# Obj-C가 아닌 Swift...
(lldb) ex -l swift -- import Foundatoin
(lldb) ex -l swift -- import AppKit
(lldb) ex -l swift -o -- unsafeBitCast(0x0000000111243800, to: NSView.self)
IDESourceEditorView: Frame: (0.0, 0.0, 739.5, 900.0), Bounds: (0.0, 0.0, 739.5, 900.0) contentViewOffset: 0.0

```

### tty

```bash
$ tty
/dev/ttys002

###

$ echo "hello debugger" 1>/dev/ttys002
```

### 프로세스의 파일 경로 얻기

```bash
$ ps -ef `pgrep -x Xcode`
  UID   PID  PPID   C STIME   TTY           TIME CMD
  501  8911     1   0 10:09PM ??         0:02.75 /Applications/Xcode.app/Contents/MacOS/Xcode
```

