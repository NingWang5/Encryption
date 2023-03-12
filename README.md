# Encryption for Python Source Code
## Methods
There are some methods to encryption python code. To be clear, with enough time and effort, almost all code can be reverse engineered. 
### Publish .pyc
Using **compileall** package to tranform .py to .pyc. But compatible bad for the python interpreter, and can be decoded by uncompyle6.

### Code Obfuscation
Code Obfuscation is the process of modifying an executable so that it is no longer useful to a hacker but remains fully functional. While the process may modify actual method instructions or metadata, it does not alter the output of the program. 

 **pyobfuscate**,  easy and convenient, great compatibility. Only can obfuscate for a single file, and cannot be joint obfuscation. The structure of obfuscated code won't be changed.

### Package into Executable File
Package .py into executable file for Linux including all dependences through **pyinstaller** . It is a convenient way to publish a product, but its executable file is usually large, and what is the most important is that it can extract .pyc from reverse engineering.

### Compile to Machine Code 
Using **Cython** to compile .py to .c, and then compile .c to .so. Although the compatibility is poor, it is the safest way compared with above methods and difficult to be decrypted. 

### Pyarmor
A command line tool [Pyarmor](https://pyarmor.readthedocs.io/zh/v5.7.6/usage.html) used to obfuscate python scripts, bind obfuscated scripts to fixed machine or expire obfuscated scripts.

### Design a Python Interpretor
I think it is not realistic...

## Simple Tutorial
Here, 2 encryption methods will be demonstrated, using **Cython** and **Pyarmor** respectively.

1. Prepare

Creating 2 files, named public.py and private.py respectively, which public.py is a python file we want to show to users and we want to protect private.py. The content is shown as following:

public.py
```python
import datetime


def show_time():
    print("Public time is", datetime.datetime.now())


if __name__ == "__main__":
    show_time()
```
private.py
```python
from public import *

show_time()
```

2.  Encryption with Cython

Runing the following 2 commands, a executable file named private will be generated. 
```bash
cython private.py --embed
gcc `python3-config --cflags --ldflags` private.c -o private
```
Then running the private file directly, outputing is:
```bash
./private 
Public time is 2023-03-12 14:41:14.629632
```

3. Encryption with Pyarmor

Running the following command, and a dist folder will be generated, which contain a executor private.
```bash
pyarmor obfuscate --exact private.py
ls dist
private.py pytransform
```
Then, entering the private folder, copy the public.py to here, and run private.
```bash
cd dist
python private.py 
Public time is 2023-03-12 14:46:02.075861
```

4. Extensibility test

Since user may change the content of public.py, let's test if public.py is changed, what will happen. Changing the public.py as below.
```python
import datetime


def show_time():
    print("Extensibility")
    print("Public time is", datetime.datetime.now())


if __name__ == "__main__":
    show_time()
```
We add a new line in 5th line. What is the output using Cython and Pyarmor?

```bash
./private 
Extensibility
Public time is 2023-03-12 14:52:59.496624
```

```bash
python private.py 
Extensibility
Public time is 2023-03-12 14:52:12.413291
```
Both them can adapt to the change of import file. This meets our needs.

## References
https://blog.csdn.net/toopoo/article/details/99847710

https://zhuanlan.zhihu.com/p/109266820

https://zhuanlan.zhihu.com/p/54296517

