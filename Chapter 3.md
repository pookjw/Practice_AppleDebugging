# Chapter 3

### Attach

```bash
# 프로세스 이름으로...
$ lldb -n Xcode

# PID로...
$ pgrep -x Xcode
9693
$ lldb -p 9693

# 파일 경로로...
$ lldb -f /Applications/Xcode.app/Contents/MacOS/Xcode

# 실행
(lldb) process launch

# 또는
(lldn) run
```

### Attaching to a future process...

```bash
# -w 또는 --waitfor
$ lldb -n Finder -w

# Finder 재실행...
$ pkill Finder
```

### launch options...

```bash
# target을 /bin/ls로 설정 - target create "/bin/ls"는 자동으로 입력됨
$ lldb -f /bin/ls
(lldb) target create "/bin/ls"
Current executable set to '/bin/ls' (x86_64).

# target 실행
(lldb) process launch
Process 10197 launched: '/bin/ls' (x86_64)
Chapter 1.md	Chapter 2.md	Chapter 3.md	README.md
Process 10197 exited with status = 0 (0x00000000)

# option과 함께 실행 (ls /Volumes/Macintosh\ HD와 동일!)
(lldb) process launch -- /Volumes/Macintosh\ HD
Process 10540 launched: '/bin/ls' (x86_64)
AppleInternal	Users		dev		private		var
Applications	Volumes		etc		sbin		xarts
Library		bin		home		tmp
System		cores		opt		usr
Process 10540 exited with status = 0 (0x00000000) 

# ~를 원하면 -X를 쓰기... 안 쓰면 오류남
(lldb) process launch -X true -- ~/Desktop
Process 10552 launched: '/bin/ls' (x86_64)
Apple_Debugging_and_Reverse_Engineering_v3.0
Apple_Debugging_and_Reverse_Engineering_v3.0.zip
Hello Debugger
Process 10552 exited with status = 0 (0x00000000) 

# process launch 대신 run 명령어도 가능
(lldb) run ~/Desktop
Process 10559 launched: '/bin/ls' (x86_64)
Apple_Debugging_and_Reverse_Engineering_v3.0
Apple_Debugging_and_Reverse_Engineering_v3.0.zip
Hello Debugger
Process 10559 exited with status = 0 (0x00000000) 
```

### Environment variables...

```bash
# 현재 설정된 env를 표시 - 설정된 것이 없기 때문에 아무것도 표시 안 됨
(lldb) env
target.env-vars (dictionary of strings) =

# env를 설정해서 process launch - 이 글에서는 안 보이겠지만 실제로 실행해보면 빨간색으로 출력됨
# `LSCOLORS=Af CLICOLOR=1 ls /Applications/`랑 동일
(lldb) process launch -v LSCOLORS=Ab -v CLICOLOR=1 -X true -- ~/Desktop
Process 10576 launched: '/bin/ls' (x86_64)
Apple_Debugging_and_Reverse_Engineering_v3.0
Apple_Debugging_and_Reverse_Engineering_v3.0.zip
Hello Debugger
Process 10576 exited with status = 0 (0x00000000) 
```

### stout

```bash
# launch해서 나온 결과를 /tmp/ls_output.txt에 저장 - ls /Applications > /tmp/ls_output.txt와 동일
(lldb) process launch -o /tmp/ls_output.txt -- /Applications
Process 10604 launched: '/bin/ls' (x86_64)
Process 10604 exited with status = 0 (0x00000000) 

# 잘 나옴
$ cat /tmp/ls_output.txt

# 근데 -o를 쓰면 -X true는 안 됨... 왜지?
(lldb) process launch -o /tmp/ls_output.txt -X true -- ~/Desktop
error: invalid combination of options for the given command
```

### stdin

```bash
# target 삭제
(lldb) target delete

# 다시 target 설정
(lldb) target create /usr/bin/wc

# 테스트를 위해 /tmp/wc_input.txt 저장
$ echo "hello world" > /tmp/wc_input.txt

# wc < /tmp/wc_input.txt와 동일
(lldb) process launch -i /tmp/wc_input.txt
Process 10674 launched: '/usr/bin/wc' (x86_64)
       1       2      12
Process 10674 exited with status = 0 (0x00000000) 

# argument 없이 실행하면 wc는 멈추는데, 이럴 경우 Ctrl + D를 누르면 종료할 수 있음
(lldb) run
Process 10715 launched: '/usr/bin/wc' (x86_64)
# 이 상태에서 멈추므로, Ctrl + D 누르면
Process 10715 launched: '/usr/bin/wc' (x86_64)
       0       0       0
Process 10715 exited with status = 0 (0x00000000) 
#이렇게 멈춤
```

