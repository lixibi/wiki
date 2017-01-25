
## 把mac的习惯弄到windows来
```autohotkey
Control & TAB::AltTab
#HotkeyInterval 1000
#MaxHotkeysPerInterval 200
WheelUp::WheelDown
WheelDown::WheelUp
WheelLeft::WheelRight
WheelRight::WheelLeft

#q:: !F4
#w:: ^w
#n:: ^n
#o:: ^o
#t:: ^t
#s:: ^s
#c:: ^c
#v:: ^v
#x:: ^x
#a:: ^a
#z::
  Send ^z
  return
#+z::
  Send ^y
  return
#f::
  Send ^f
  return
#p::
  Send ^p
  return
!#h::
  WinMinimizeAll
  return
#h::
  WinMinimize, A
  return
#SC029::
  Send ^{Tab}
  return
+#SC029::
  Send +^{Tab}
  return

#Left::
  Send {Home}
  return
+#Left::
  Send +{Home}
  return
#Right::
  Send {End}
  return
+#Right::
  Send +{End}
  return
#Up::
  WinGet, pname, ProcessName, A
  IfEqual, pname, explorer.exe, {
    Send {Browser_Back}
  } else {
    Send ^{Home}
  }
  return
+#Up::
  Send +^{Home}
  return
#Down::
  WinGet, pname, ProcessName, A
  IfEqual, pname, explorer.exe, {
    Send {Enter}
  } else {
    Send ^{End}
  }
  return
+#Down::
  Send +^{End}
  return
!Left::
  Send ^{Left}
  return
+!Left::
  Send +^{Left}
  return
!Right::
  Send ^{Right}
  return
+!Right::
  Send +^{Right}
  return

#[:: Browser_Back
#]:: Browser_Forward
#i::
  WinGet, pname, ProcessName, A
  IfEqual, pname, explorer.exe, {
    Send !{Enter}
  }
  return
#+3::
  Send {PrintScreen}
  return
^#+3::
  Send {PrintScreen}
  return
#+4::
  Send !{PrintScreen}
  return
^#+4::
  Send !{PrintScreen}
  return

+#Backspace::
  WinGet, pname, ProcessName, A
  IfEqual, pname, explorer.exe, {
    FileRecycleEmpty
  } else {
    Send Send +{Home}
    Send {Delete}
  }
  return
+!Backspace::
  Shutdown 0
  return
#Backspace::
  WinGet, pname, ProcessName, A
  IfEqual, pname, explorer.exe, {
    Send {Delete}
  } else {
    Send +{Home}
    Send {Delete}
  }
  return
!Backspace::
  Send ^{Backspace}
  return

```
