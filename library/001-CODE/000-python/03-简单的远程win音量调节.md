### 客户端
```python
#coding:utf-8
import socket,time
while True:
    cmd = raw_input('请输入命令：')
    sock = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
    sock.connect(('192.168.1.182', 8017))
    sock.send(cmd)
    sock.close()
```
---
### 服务器端
```python
# coding:utf-8
import socket,time

sock = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
myname = socket.getfqdn(socket.gethostname(  ))
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

count=0
print 'open server! '
while True:
    connection, address = sock.accept()

    try:
        connection.settimeout(5)
        buf = connection.recv(1024)
        if buf == '1':


            print timestr() +' [code=1] MUTE ---'+str(count)
            mute()
            count+=1
        elif buf == '0':
            print '[0]power off'

        elif buf == '2':

            print timestr() +' [code=2] up volume ---'+str(count)
            for i in range(5):

                upv()
            count += 1
        elif buf == '3':
            print timestr()+' [code=3] down volume ---'+str(count)
            for i in range(5):
                downv()
            count += 1
        else:
            print 'unkown cmd'
    except socket.timeout:
        print 'time out'
        connection.close()
```
