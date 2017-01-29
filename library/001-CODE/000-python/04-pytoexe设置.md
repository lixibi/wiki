## py2exe设置方法
---

* [选择](https://sourceforge.net/projects/py2exe/files/py2exe/0.6.9/)正确的python版本，安装py2exe。
* py文件目录新建一个setup.py
  ```python
  from distutils.core import setup
import py2exe
setup(console=["myscript.py"])
```
* 目录执行```python mysetup.py py2exe ```
---
### 程序静默运行方法
* py文件拓展名改为pyw
