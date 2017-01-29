### 客户端
0.2 增加远程命令发送，预设关机开关。
```python
# coding:utf-8
import socket, time

def socket_send(command):
    sock = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
    sock.connect(('192.168.1.182', 8017))
    if command[:4] == 'cmd ':
        sock.send(command)
        result = sock.recv(2048)
        sock.close()
        print result
        return
    else:
        sock.send(command)
        sock.close()
while True:
    print '声音开关：0或1\n增大音量：+\n减小音量：-\n关机：-1\n发送命令：\'cmd+空格\'+命令'
    cmd = raw_input('请输入命令：')
    socket_send(cmd)
```
---
### 服务器端
```python
# coding:utf-8
import socket, time, os

sock = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
myname = socket.getfqdn(socket.gethostname())
ip = socket.gethostbyname(myname)
port = 8017
sock.bind((ip, port))
sock.listen(5)

from ctypes import windll

WM_APPCOMMAND = 0x319
APPCOMMAND_VOLUME_UP = 0x0a
APPCOMMAND_VOLUME_DOWN = 0x09
APPCOMMAND_VOLUME_MUTE = 0x08


def timestr():
    return time.strftime('%Y-%m-%d %H:%M:%S', time.localtime(time.time()))


def mute():
    hwnd = windll.user32.GetForegroundWindow()
    windll.user32.PostMessageA(hwnd, WM_APPCOMMAND
                               , 0, APPCOMMAND_VOLUME_MUTE * 0x10000)
    return


def upv():
    hwnd = windll.user32.GetForegroundWindow()
    windll.user32.PostMessageA(hwnd, WM_APPCOMMAND
                               , 0x30292, APPCOMMAND_VOLUME_UP * 0x10000)
    return


def downv():
    hwnd = windll.user32.GetForegroundWindow()
    windll.user32.PostMessageA(hwnd, WM_APPCOMMAND
                               , 0x30292, APPCOMMAND_VOLUME_DOWN * 0x10000)
    return


count = 0
print 'open server! '

while True:
    connection, address = sock.accept()

    try:
        connection.settimeout(5)
        buf = connection.recv(1024)
        if buf == '1' or buf == '0':
            print timestr() + ' [code=1] MUTE ---' + str(count)
            mute()
            count += 1
        elif buf == '+':
            print timestr() + ' [code=2] up volume ---' + str(count)
            for i in range(5):
                upv()
            count += 1
        elif buf == '-':
            print timestr() + ' [code=3] down volume ---' + str(count)
            for i in range(5):
                downv()
            count += 1
        elif buf == '-1':
            result = os.popen('shutdown -s -t 1').read()
        elif buf[:4] == 'cmd ':
            print 'this cmd mode'
            result = os.popen(buf[4:]).read()
            print result
            connection.send(result)
        else:
            print buf[:3]
            print 'unkown cmd'
    except socket.timeout:
        print 'time out'
        connection.close()

```
