## 简介

### 使用 Python 和另一种语言混编的好处

至少有以下四个原因：

1. `Best of both worlds` - 结合两种语言的优点：已经优化和测试过的代码库 + Python 的灵活
- `Python as glue` - **Python** 作为连接的桥梁，将很多其他语言的模块结合到一个大型程序中
- `Speed up Python` - 使用一个更快的语言帮助加速 **Python**
- `Division of labor` - 各司其职，让各个语言做各自更擅长的事情，例如 **Fortran** 进行数组计算，**Python** 处理测试，文件读写，文本处理，数据整理，GUI 生成，HTTP 服务等等。

### 语言扩展工具

#### 打包已有的代码和其他语言的库

- 使用手写的扩展模块
- `Cython` - **C/C++**
- `SWIG` - **C/C++**
- `f2py` - **Fortran**
- `ctypes` - 其他语言库

#### 加速 Python

- 使用手写的扩展模块
- `Cython`
- `Weave`
- `Shedskin` 和其他模块
## Python 扩展模块

### 简介

C Library | Interface | Python
---|---|---
`c header` <br> `c implementation` | Wrapper `C` $\leftrightarrows$ `Python` <br> communication between `py + c` | `import fact` <br> `fact.fact(10)`

**Python** 扩展模块将 `PyInt(10)` 转化为 `CInt(10)` 然后调用 `C` 程序中的 `fact()` 函数进行计算，再将返回的结果转换回 `PyInt`。

### 产生一个扩展模块

假设我们有这样的一个头文件和程序：


```python
%%file fact.h
#ifndef FACT_H
#define FACT_h
int fact(int n);
#endif
```

    Writing fact.h



```python
%%file fact.c
#include "fact.h"
int fact(int n)
{
    if (n <= 1) return 1;
    else return n * fact(n - 1);
}
```

    Writing fact.c


定义包装函数：


```python
%%file fact_wrap.c

/* Must include Python.h before any standard headers*/
#include <Python.h>
#include "fact.h"
static PyObject* wrap_fact(PyObject *self, PyObject *args)
{
    /* Python->C data conversion */
    int n, result;
    // the string i here means there is only one integer
    if (!PyArg_ParseTuple(args, "i", &n))
        return NULL;
    
    /* C Function Call */
    result = fact(n);
    
    /* C->Python data conversion */
    return Py_BuildValue("i", result);
}

/* Method table declaring the names of functions exposed to Python*/
static PyMethodDef ExampleMethods[] = {
    {"fact",  wrap_fact, METH_VARARGS, "Calculate the factorial of n"},
    {NULL, NULL, 0, NULL}        /* Sentinel */
};

/* Module initialization function called at "import example"*/
PyMODINIT_FUNC 
initexample(void)
{
    (void) Py_InitModule("example", ExampleMethods);
}
```

    Writing fact_wrap.c


### 手动编译扩展模块

手动使用 `gcc` 编译，`Windows` 下如果没有 `gcc`，可以通过 `conda` 进行安装：

    conda install mingw4
    
`Window 64-bit` 下编译需要加上 `-DMS_WIN64` 的选项，`include` 和 `lib` 文件夹的路径对应于本地 **Python** 安装的环境：


```python
!gcc -DMS_WIN64 -c fact.c fact_wrap.c -IC:\Miniconda\include
```


```python
!gcc -DMS_WIN64 -shared fact.o fact_wrap.o -LC:\Miniconda\libs -lpython27 -o example.pyd
```

`Windows` 下最终生成的文件后缀为 `.pyd` ， `Unix` 下生成的文件后缀名为 `.so`。

用法为：

- `Windows 32-bit`
```
gcc -c fact.c fact_wrap.c -I<your python path>\include
gcc -shared fact.o fact_wrap.o -L<your python path>\libs -lpython27 -o example.pyd
```
- `Unix`
```
gcc -c fact.c fact_wrap.c -I<your python path>
gcc -shared fact.o fact_wrap.o -L<your python path>\config -lpython27 -o example.so
```

编译完成后，我们就可以使用 `example` 这个模块了。

导入生成的包：


```python
import example
print dir(example)
```

    ['__doc__', '__file__', '__name__', '__package__', 'fact']


使用 `example` 中的函数：


```python
print 'factorial of 10:', example.fact(10)
```

    factorial of 10: 3628800


### 使用 setup.py 进行编译

清理刚才生成的文件：


```python
!rm -f example.pyd
```

写入 `setup.py`：


```python
%%file setup.py
from distutils.core import setup, Extension

ext = Extension(name='example', sources=['fact_wrap.c', 'fact.c'])

setup(name='example', ext_modules=[ext])
```

    Writing setup.py


使用 `distutils` 中的函数，我们进行 `build` 和 `install`：

    python setup.py build (--compiler=mingw64)
    python setup.py install

括号中的内容在 `windows` 中可能需要加上。

这里我们使用 `build_ext --inplace` 选项将其安装在本地文件夹：


```python
!python setup.py build_ext --inplace
```

    running build_ext
    building 'example' extension
    creating build
    creating build\temp.win-amd64-2.7
    creating build\temp.win-amd64-2.7\Release
    C:\Miniconda\Scripts\gcc.bat -DMS_WIN64 -mdll -O -Wall -IC:\Miniconda\include -IC:\Miniconda\PC -c fact_wrap.c -o build\temp.win-amd64-2.7\Release\fact_wrap.o
    C:\Miniconda\Scripts\gcc.bat -DMS_WIN64 -mdll -O -Wall -IC:\Miniconda\include -IC:\Miniconda\PC -c fact.c -o build\temp.win-amd64-2.7\Release\fact.o
    writing build\temp.win-amd64-2.7\Release\example.def
    C:\Miniconda\Scripts\gcc.bat -DMS_WIN64 -shared -s build\temp.win-amd64-2.7\Release\fact_wrap.o build\temp.win-amd64-2.7\Release\fact.o build\temp.win-amd64-2.7\Release\example.def -LC:\Miniconda\libs -LC:\Miniconda\PCbuild\amd64 -lpython27 -lmsvcr90 -o "C:\Users\Jin\Documents\Git\python-tutorial\07. interfacing with other languages\example.pyd"


### 使用编译的模块

进行测试：


```python
import example

print 'factorial of 10:', example.fact(10)
```

    factorial of 10: 3628800


定义 `Python` 函数：


```python
def pyfact(n):
    if n <= 1: return 1
    return n * pyfact(n-1)

print pyfact(10)
print example.fact(10)
```

    3628800
    3628800


时间测试：


```python
%timeit example.fact(10)
```

    The slowest run took 13.17 times longer than the fastest. This could mean that an intermediate result is being cached 
    1000000 loops, best of 3: 213 ns per loop



```python
%timeit pyfact(10)
```

    1000000 loops, best of 3: 1.43 µs per loop


如果使用 `fact` 计算比较大的值： 


```python
example.fact(100)
```




    0



会出现溢出的结果，因为 `int` 表示的值有限，但是 `pyfact` 不会有这样的问题：


```python
pyfact(100)
```




    93326215443944152681699238856266700490715968264381621468592963895217599993229915608941463976156518286253697920827223758251185210916864000000000000000000000000L



将生成的文件压缩到压缩文件中：


```python
import zipfile

f = zipfile.ZipFile('07-02-example.zip','w',zipfile.ZIP_DEFLATED)

names = 'fact.o fact_wrap.c fact_wrap.o example.pyd setup.py'.split()
for name in names:
    f.write(name)

f.close()
```

清理生成的文件：


```python
!rm -f fact*.*
!rm -f example.*
!rm -f setup*.*
!rm -rf build
```
## Cython：Cython 基础，将源代码转换成扩展模块

### Cython 基础

之前使用了手动的方法对 `C` 程序进行编译，而 `Cython` 则简化了这个过程。

考虑之前的斐波拉契数列，`Python` 版本：

```python
def fib(n):
    a,b = 1,1
    for i in range(n):
        a,b = a+b, a
    return a
```

`C` 版本：

```cpp
int fib(int n) {
    int tmp, i, a, b;
    a = b = 1;
    for (i=0; i<n; i++) {
        tmp = a; a += b; b = tmp;
    }
    return a;
}
```

`Cython` 版本：

```cython
def fib(int n):
    cdef int i, a, b
    a,b = 1,1
    for i in range(n):
        a,b = a+b, a
    return a
```

这里 `cdef` 定义了 `C` 变量的类型。 

**Cython** 的好处在于，我们使用了 **Python** 的语法，又有 **C/C++** 的效率，同时省去了之前直接编译成扩展模块的麻烦，并且提供了原生的 **Numpy** 支持。

官方网址：http://www.cython.org

其主要用法有两点：

- 将 Python 程序转化为 C 程序
- 包装 C/C++ 程序

### 将源代码转换成扩展模块

#### ipython 中使用 Cython 命令

导入 `Cython` `magic` 命令：


```python
%load_ext Cython
```

使用 `magic` 命令执行 `Cython`：


```cython
%%cython
def cyfib(int n):
    cdef int i, a, b
    a,b = 1,1
    for i in range(n):
        a,b = a+b, a
    return a
```


```python
cyfib(10)
```




    144



#### 使用 distutils 编译 Cython

`Cython` 代码以 `.pyx` 结尾，先通过 cython 转化为 `.c` 文件，再用 `gcc` 转化为 `.so(.pyd)` 文件。


```python
%%file fib.pyx
def cyfib(int n):
    cdef int i, a, b
    a,b = 1,1
    for i in range(n):
        a,b = a+b, a
    return a
```

    Writing fib.pyx



```python
%%file setup.py
from distutils.core import setup
from distutils.extension import Extension
from Cython.Distutils import build_ext

ext = Extension("fib", sources=["fib.pyx"])

setup(ext_modules=[ext], cmdclass={'build_ext': build_ext})
```

    Overwriting setup.py


编译成功：


```python
!python setup.py build_ext --inplace
```

    running build_ext
    cythoning fib.pyx to fib.c
    building 'fib' extension
    creating build
    creating build\temp.win-amd64-2.7
    creating build\temp.win-amd64-2.7\Release
    C:\Miniconda\Scripts\gcc.bat -DMS_WIN64 -mdll -O -Wall -IC:\Miniconda\include -IC:\Miniconda\PC -c fib.c -o build\temp.win-amd64-2.7\Release\fib.o
    writing build\temp.win-amd64-2.7\Release\fib.def
    C:\Miniconda\Scripts\gcc.bat -DMS_WIN64 -shared -s build\temp.win-amd64-2.7\Release\fib.o build\temp.win-amd64-2.7\Release\fib.def -LC:\Miniconda\libs -LC:\Miniconda\PCbuild\amd64 -lpython27 -lmsvcr90 -o "C:\Users\Jin\Documents\Git\python-tutorial\07. interfacing with other languages\fib.pyd"


使用模块：


```python
import fib

fib.cyfib(10)
```




    144




```python
import zipfile

f = zipfile.ZipFile('07-03-fib.zip','w',zipfile.ZIP_DEFLATED)

names = 'fib.pyd fib.pyx fib.c setup.py'.split()
for name in names:
    f.write(name)

f.close()
```

### 使用 pyximport

清理之前生成的文件：


```python
!rm -f fib.pyd
!rm -f fib.pyc
!rm -f fib.C
```

清理之前导入的模块：


```python
%reset -f
```

使用 `pyximport`：


```python
import pyximport
pyximport.install()

import fib

fib.cyfib(10)
```




    144



`install` 函数会自动检测 Cython 程序的变化，并自动导入，不过一般用于简单文件的编译。

清理生成的文件：


```python
!rm -f setup*.*
!rm -f fib.*
!rm -rf build
```
## Cython：Cython 语法，调用其他C库

### Cython 语法

#### cdef 关键词

`cdef` 定义 `C` 类型变量。 

可以定义局部变量：

```cython
def fib(int n):
    cdef int a,b,i
    ...
```

定义函数返回值：

```cython
cdef float distance(float *x, float *y, int n):
    cdef:
        int i
        float d = 0.0
    for i in range(n):
        d += (x[i] - y[i]) ** 2
    return d
```

定义函数：
```cython
cdef class Particle(object):
    cdef float psn[3], vel[3]
    cdef int id
```

注意函数的参数不需要使用 cdef 的定义。

#### def, cdef, cpdef 函数

`Cython` 一共有三种定义方式，`def, cdef, cpdef` 三种：

- `def` - Python, Cython 都可以调用
- `cdef` - 更快，只能 Cython 调用，可以使用指针
- `cpdef` - Python, Cython 都可以调用，不能使用指针

#### cimport


```python
from math import sin as pysin
from numpy import sin as npsin
```


```python
%load_ext Cython
```

从标准 `C` 语言库中调用模块，`cimport` 只能在 `Cython` 中使用：


```cython
%%cython
from libc.math cimport sin
from libc.stdlib cimport malloc, free
```

#### cimport 和 pxd 文件

如果想在多个文件中复用 `Cython` 代码，可以定义一个 `.pxd` 文件（相当于头文件 `.h`）定义方法，这个文件对应于一个 `.pyx` 文件（相当于源文件 `.c`），然后在其他的文件中使用 `cimport` 导入：

`fib.pxd, fib.pyx` 文件存在，那么可以这样调用：
```cython
from fib cimport fib
```

还可以调用 `C++` 标准库和 `Numpy C Api` 中的文件：
```cython
from libcpp.vector cimport vector
cimport numpy as cnp
```

### 调用其他C库

从标准库 `string.h` 中调用 `strlen`：


```python
%%file len_extern.pyx
cdef extern from "string.h":
    int strlen(char *c)
    
def get_len(char *message):
    return strlen(message)
```

    Writing len_extern.pyx


不过 `Cython` 不会自动扫描导入的头文件，所以要使用的函数必须再声明一遍：


```python
%%file setup_len_extern.py
from distutils.core import setup
from distutils.extension import Extension
from Cython.Distutils import build_ext

setup(
  ext_modules=[ Extension("len_extern", ["len_extern.pyx"]) ],
  cmdclass = {'build_ext': build_ext}
)
```

    Writing setup_len_extern.py


编译：


```python
!python setup_len_extern.py build_ext --inplace
```

    running build_ext
    cythoning len_extern.pyx to len_extern.c
    building 'len_extern' extension
    creating build
    creating build\temp.win-amd64-2.7
    creating build\temp.win-amd64-2.7\Release
    C:\Miniconda\Scripts\gcc.bat -DMS_WIN64 -mdll -O -Wall -IC:\Miniconda\include -IC:\Miniconda\PC -c len_extern.c -o build\temp.win-amd64-2.7\Release\len_extern.o
    writing build\temp.win-amd64-2.7\Release\len_extern.def
    C:\Miniconda\Scripts\gcc.bat -DMS_WIN64 -shared -s build\temp.win-amd64-2.7\Release\len_extern.o build\temp.win-amd64-2.7\Release\len_extern.def -LC:\Miniconda\libs -LC:\Miniconda\PCbuild\amd64 -lpython27 -lmsvcr90 -o "C:\Users\Jin\Documents\Git\python-tutorial\07. interfacing with other languages\len_extern.pyd"


从 `Python` 中调用：


```python
import len_extern
```

调用这个模块后，并不能直接使用 `strlen` 函数，可以看到，这个模块中并没有 `strlen` 这个函数：


```python
dir(len_extern)
```




    ['__builtins__',
     '__doc__',
     '__file__',
     '__name__',
     '__package__',
     '__test__',
     'get_len']



不过可以调用 `get_len` 函数： 


```python
len_extern.get_len('hello')
```




    5



因为调用的是 `C` 函数，所以函数的表现与 `C` 语言的用法一致，例如 `C` 语言以 `\0` 为字符串的结束符，所以会出现这样的情况：


```python
len_extern.get_len('hello\0world!')
```




    5



除了对已有的 `C` 函数进行调用，还可以对已有的 `C` 结构体进行调用和修改：


```python
%%file time_extern.pyx
cdef extern from "time.h":

    struct tm:
        int tm_mday
        int tm_mon
        int tm_year

    ctypedef long time_t
    tm* localtime(time_t *timer)
    time_t time(time_t *tloc)

def get_date():
    """Return a tuple with the current day, month and year."""
    cdef time_t t
    cdef tm* ts
    t = time(NULL)
    ts = localtime(&t)
    return ts.tm_mday, ts.tm_mon + 1, ts.tm_year + 1900
```

    Writing time_extern.pyx


这里我们只使用 `tm` 结构体的年月日信息，所以只声明了要用了三个属性。


```python
%%file setup_time_extern.py
from distutils.core import setup
from distutils.extension import Extension
from Cython.Distutils import build_ext

setup(
  ext_modules=[ Extension("time_extern", ["time_extern.pyx"]) ],
  cmdclass = {'build_ext': build_ext}
)
```

    Writing setup_time_extern.py


编译：


```python
!python setup_time_extern.py build_ext --inplace
```

    running build_ext
    cythoning time_extern.pyx to time_extern.c
    building 'time_extern' extension
    C:\Miniconda\Scripts\gcc.bat -DMS_WIN64 -mdll -O -Wall -IC:\Miniconda\include -IC:\Miniconda\PC -c time_extern.c -o build\temp.win-amd64-2.7\Release\time_extern.o
    writing build\temp.win-amd64-2.7\Release\time_extern.def
    C:\Miniconda\Scripts\gcc.bat -DMS_WIN64 -shared -s build\temp.win-amd64-2.7\Release\time_extern.o build\temp.win-amd64-2.7\Release\time_extern.def -LC:\Miniconda\libs -LC:\Miniconda\PCbuild\amd64 -lpython27 -lmsvcr90 -o "C:\Users\Jin\Documents\Git\python-tutorial\07. interfacing with other languages\time_extern.pyd"


测试：


```python
import time_extern

time_extern.get_date()
```




    (19, 9, 2015)



清理文件：


```python
import zipfile

f = zipfile.ZipFile('07-04-extern.zip','w',zipfile.ZIP_DEFLATED)

names = ['setup_len_extern.py',
         'len_extern.pyx',
         'setup_time_extern.py',
         'time_extern.pyx']
for name in names:
    f.write(name)

f.close()

!rm -f setup*.*
!rm -f len_extern.*
!rm -f time_extern.*
!rm -rf build
```
## Cython：class 和 cdef class，使用 C++

### class 和 cdef class

`class` 定义属性变量比较自由，`cdef class` 可以定义 `cdef`

`class` 使用 `__init__` 初始化，`cdef class` 在使用 `__init__` 之前用 `__cinit__` 对 `C` 相关的参数进行初始化。

`cdef class` 中的方法可以是 `def, cdef, cpdef` 三种，只有 `public` 的属性才可以被访问，不可以添加新的属性。

`__dealloc__` 函数类似析构函数，负责释放申请的内存。

`Cython` 属性可以使用关键词 `property` 来定义，然后定义 `__get__` 和  `__set__` 方法来进行获取和设置：

```cython
property name:
    def __get__(self):
        return something
    def __set__(self):
        set_something
```

### 使用 C++ 类

使用 `C++` 类时要加上 `cppclass` 关键词，在编译时 `setup` 中要加上 `language="c++"` 的选项。

假设我们有这样一个 `C++` 类：


```python
%%file particle_extern.h
#ifndef _PARTICLE_EXTERN_H_
#define _PARTICLE_EXTERN_H_

class Particle {

    public:

        Particle() :
            mass(0), charge(0) {}
        
        Particle(float m, float c, float *p, float *v);

        ~Particle() {}

        float getMass() {return mass; }

        void setMass(float m) { mass = m; }

        float getCharge() { return charge; }

        const float *getVel() { return vel; }
        const float *getPos() { return pos; }

        void applyImpulse(float *f, float t);

    private:
        float mass, charge;
        float pos[3], vel[3];
};

#endif
```

    Overwriting particle_extern.h



```python
%%file particle_extern.cpp
#include "particle_extern.h"

Particle::Particle(float m, float c, float *p, float *v) :
    mass(m), charge(c) 
{
    for (int i=0; i<3; ++i) {
        pos[i] = p[i]; vel[i] = v[i];
    }
}

void Particle::applyImpulse(float *f, float t)
{
    float newvi;
    for(int i=0; i<3; ++i) {
        newvi = vel[i] + t / mass * f[i];
        pos[i] = (newvi + vel[i]) * t / 2.;
        vel[i] = newvi;
    }
}
```

    Overwriting particle_extern.cpp


在 `Cython` 中调用这个类：


```python
%%file particle.pyx
import numpy as np

cdef extern from "particle_extern.h":

    cppclass _Particle "Particle":
        _Particle(float m, float c, float *p, float *v)
        float getMass()
        void setMass(float m)
        float getCharge()
        const float *getVel()
        const float *getPos()
        void applyImpulse(float *f, float t)


cdef class Particle:
    cdef _Particle *thisptr ## ptr to C++ instance

    def __cinit__(self, m, c, float[::1] p, float[::1] v):
        if p.shape[0] != 3 or v.shape[0] != 3:
            raise ValueError("...")
        self.thisptr = new _Particle(m, c, &p[0], &v[0])

    def __dealloc__(self):
        del self.thisptr

    def apply_impulse(self, float[::1] v, float t):
        self.thisptr.applyImpulse(&v[0], t)

    def __repr__(self):
        args = ', '.join('%s=%s' % (n, getattr(self, n)) for n in ('mass', 'charge', 'pos', 'vel'))
        return 'particle.Particle(%s)' % args

    property charge:

        def __get__(self):
            return self.thisptr.getCharge()

    property mass:  ## Cython-style properties.
        def __get__(self):
            return self.thisptr.getMass()

        def __set__(self, m):
            self.thisptr.setMass(m)

    property vel:

        def __get__(self):
            cdef const float *_vel = self.thisptr.getVel()
            cdef float[::1] arr = np.empty((3,), dtype=np.float32)
            for i in range(3):
                arr[i] = _vel[i]
            return np.asarray(arr)

    property pos:

        def __get__(self):
            cdef const float *_pos = self.thisptr.getPos()
            cdef float[::1] arr = np.empty((3,), dtype=np.float32)
            for i in range(3):
                arr[i] = _pos[i]
            return np.asarray(arr)

```

    Overwriting particle.pyx


首先从头文件声明这个类：
```cython
cdef extern from "particle_extern.h":

    cppclass _Particle "Particle":
        _Particle(float m, float c, float *p, float *v)
        float getMass()
        void setMass(float m)
        float getCharge()
        const float *getVel()
        const float *getPos()
        void applyImpulse(float *f, float t)
```

这里要使用 `cppclass` 关键词，并且为了方便，我们将 `Particle` 类的名字在 `Cython` 中重命名为 `_Particle`。

```cython
cdef class Particle:
    cdef _Particle *thisptr
    def __cinit__(self, m, c, float[::1] p, float[::1] v):
        if p.shape[0] != 3 or v.shape[0] != 3:
            raise ValueError("...")
        self.thisptr = new _Particle(m, c, &p[0], &v[0])
```
为了使用这个类，我们需要定义一个该类的指针，然后用指针指向一个 `_Particle` 对象。


```python
%%file setup.py
from distutils.core import setup
from distutils.extension import Extension
from Cython.Distutils import build_ext

ext = Extension("particle", ["particle.pyx", "particle_extern.cpp"], language="c++")

setup(
    cmdclass = {'build_ext': build_ext},
    ext_modules = [ext],
)
```

    Overwriting setup.py


要加上 `language="c++"` 的选项，然后编译：


```python
!python setup.py build_ext -i
```

    running build_ext
    cythoning particle.pyx to particle.cpp
    building 'particle' extension
    C:\Anaconda\Scripts\gcc.bat -DMS_WIN64 -mdll -O -Wall -IC:\Anaconda\include -IC:\Anaconda\PC -c particle.cpp -o build\temp.win-amd64-2.7\Release\particle.o
    C:\Anaconda\Scripts\gcc.bat -DMS_WIN64 -mdll -O -Wall -IC:\Anaconda\include -IC:\Anaconda\PC -c particle_extern.cpp -o build\temp.win-amd64-2.7\Release\particle_extern.o
    writing build\temp.win-amd64-2.7\Release\particle.def
    C:\Anaconda\Scripts\g++.bat -DMS_WIN64 -shared -s build\temp.win-amd64-2.7\Release\particle.o build\temp.win-amd64-2.7\Release\particle_extern.o build\temp.win-amd64-2.7\Release\particle.def -LC:\Anaconda\libs -LC:\Anaconda\PCbuild\amd64 -lpython27 -lmsvcr90 -o "C:\Users\lijin\Documents\Git\python-tutorial\07. interfacing with other languages\particle.pyd"


    particle.cpp: In function 'void __Pyx_RaiseArgtupleInvalid(const char*, int, Py_ssize_t, Py_ssize_t, Py_ssize_t)':
    particle.cpp:14931:59: warning: unknown conversion type character 'z' in format [-Wformat]
    particle.cpp:14931:59: warning: format '%s' expects argument of type 'char*', but argument 5 has type 'Py_ssize_t {aka long long int}' [-Wformat]
    particle.cpp:14931:59: warning: unknown conversion type character 'z' in format [-Wformat]
    particle.cpp:14931:59: warning: too many arguments for format [-Wformat-extra-args]
    particle.cpp: In function 'int __Pyx_BufFmt_ProcessTypeChunk(__Pyx_BufFmt_Context*)':
    particle.cpp:15498:78: warning: unknown conversion type character 'z' in format [-Wformat]
    particle.cpp:15498:78: warning: unknown conversion type character 'z' in format [-Wformat]
    particle.cpp:15498:78: warning: too many arguments for format [-Wformat-extra-args]
    particle.cpp:15550:67: warning: unknown conversion type character 'z' in format [-Wformat]
    particle.cpp:15550:67: warning: unknown conversion type character 'z' in format [-Wformat]
    particle.cpp:15550:67: warning: too many arguments for format [-Wformat-extra-args]
    particle.cpp: In function 'PyObject* __pyx_buffmt_parse_array(__Pyx_BufFmt_Context*, const char**)':
    particle.cpp:15612:69: warning: unknown conversion type character 'z' in format [-Wformat]
    particle.cpp:15612:69: warning: format '%d' expects argument of type 'int', but argument 3 has type 'size_t {aka long long unsigned int}' [-Wformat]
    particle.cpp:15612:69: warning: too many arguments for format [-Wformat-extra-args]
    particle.cpp: In function 'int __Pyx_GetBufferAndValidate(Py_buffer*, PyObject*, __Pyx_TypeInfo*, int, int, int, __Pyx_BufFmt_StackElem*)':
    particle.cpp:15797:73: warning: unknown conversion type character 'z' in format [-Wformat]
    particle.cpp:15797:73: warning: format '%s' expects argument of type 'char*', but argument 3 has type 'Py_ssize_t {aka long long int}' [-Wformat]
    particle.cpp:15797:73: warning: unknown conversion type character 'z' in format [-Wformat]
    particle.cpp:15797:73: warning: too many arguments for format [-Wformat-extra-args]
    particle.cpp: In function 'void __Pyx_RaiseTooManyValuesError(Py_ssize_t)':
    particle.cpp:16216:94: warning: unknown conversion type character 'z' in format [-Wformat]
    particle.cpp:16216:94: warning: too many arguments for format [-Wformat-extra-args]
    particle.cpp: In function 'void __Pyx_RaiseNeedMoreValuesError(Py_ssize_t)':
    particle.cpp:16222:48: warning: unknown conversion type character 'z' in format [-Wformat]
    particle.cpp:16222:48: warning: format '%s' expects argument of type 'char*', but argument 3 has type 'Py_ssize_t {aka long long int}' [-Wformat]
    particle.cpp:16222:48: warning: too many arguments for format [-Wformat-extra-args]
    particle.cpp: In function 'int __Pyx_ValidateAndInit_memviewslice(int*, int, int, int, __Pyx_TypeInfo*, __Pyx_BufFmt_StackElem*, __Pyx_memviewslice*, PyObject*)':
    particle.cpp:16941:50: warning: unknown conversion type character 'z' in format [-Wformat]
    particle.cpp:16941:50: warning: format '%s' expects argument of type 'char*', but argument 3 has type 'Py_ssize_t {aka long long int}' [-Wformat]
    particle.cpp:16941:50: warning: unknown conversion type character 'z' in format [-Wformat]
    particle.cpp:16941:50: warning: too many arguments for format [-Wformat-extra-args]



```python
import particle
```

注意这里类型要设成 `float32`，因为 `C++` 程序中接受的是 `float` 类型，默认是 `float64(double)` 类型：


```python
import numpy as np

pos = vel = np.arange(3., dtype='float32')
mass = 1.0
charge = 2.0

p = particle.Particle(mass, charge, pos, vel)
p
```




    particle.Particle(mass=1.0, charge=2.0, pos=[ 0.  1.  2.], vel=[ 0.  1.  2.])



调用 `apply_impulse` 方法：


```python
p.apply_impulse(np.arange(3., dtype='float32'), 1.0)

p
```




    particle.Particle(mass=1.0, charge=2.0, pos=[ 0.   1.5  3. ], vel=[ 0.  2.  4.])



查看质量：


```python
p.mass
```




    1.0



修改质量：


```python
p.mass = 3.0
```

查看 `charge`：


```python
p.charge
```




    2.0



因为 `charge` 没有定义 `__set__` 方法，所以它是只读的属性，不能进行修改。


```python
import zipfile

f = zipfile.ZipFile('07-05-particle.zip','w',zipfile.ZIP_DEFLATED)

names = ['particle.pyx',
         'particle_extern.cpp',
         'particle_extern.h',
         'setup.py']
for name in names:
    f.write(name)

f.close()

!rm -f setup*.*
!rm -f particle*.*
!rm -rf build
```
## Cython：Typed memoryviews

### 例子

这里 `double[::1]` 是一种 `memoryview` 方法，效率跟 `Numpy` 数组差不多，可以给 `C` 数组赋值，可以给 `Numpy` 数组赋值，可以像 `Numpy` 一样切片：


```python
%%file cython_sum.pyx
def cython_sum(double[::1] a):
    cdef double s = 0.0
    cdef int i, n = a.shape[0]
    for i in range(n):
        s += a[i]
    return s
```

    Writing cython_sum.pyx



```python
%%file setup.py
from distutils.core import setup
from distutils.extension import Extension
from Cython.Distutils import build_ext

ext = Extension("cython_sum", ["cython_sum.pyx"])

setup(
    cmdclass = {'build_ext': build_ext},
    ext_modules = [ext],
)
```

    Writing setup.py



```python
!python setup.py build_ext -i
```

    running build_ext
    cythoning cython_sum.pyx to cython_sum.c
    building 'cython_sum' extension
    creating build
    creating build\temp.win-amd64-2.7
    creating build\temp.win-amd64-2.7\Release
    C:\Anaconda\Scripts\gcc.bat -DMS_WIN64 -mdll -O -Wall -IC:\Anaconda\include -IC:\Anaconda\PC -c cython_sum.c -o build\temp.win-amd64-2.7\Release\cython_sum.o
    writing build\temp.win-amd64-2.7\Release\cython_sum.def
    C:\Anaconda\Scripts\gcc.bat -DMS_WIN64 -shared -s build\temp.win-amd64-2.7\Release\cython_sum.o build\temp.win-amd64-2.7\Release\cython_sum.def -LC:\Anaconda\libs -LC:\Anaconda\PCbuild\amd64 -lpython27 -lmsvcr90 -o "C:\Users\lijin\Documents\Git\python-tutorial\07. interfacing with other languages\cython_sum.pyd"


    cython_sum.c: In function '__Pyx_BufFmt_ProcessTypeChunk':
    cython_sum.c:13561:26: warning: unknown conversion type character 'z' in format [-Wformat]
    cython_sum.c:13561:26: warning: unknown conversion type character 'z' in format [-Wformat]
    cython_sum.c:13561:26: warning: too many arguments for format [-Wformat-extra-args]
    cython_sum.c:13613:20: warning: unknown conversion type character 'z' in format [-Wformat]
    cython_sum.c:13613:20: warning: unknown conversion type character 'z' in format [-Wformat]
    cython_sum.c:13613:20: warning: too many arguments for format [-Wformat-extra-args]
    cython_sum.c: In function '__pyx_buffmt_parse_array':
    cython_sum.c:13675:25: warning: unknown conversion type character 'z' in format [-Wformat]
    cython_sum.c:13675:25: warning: format '%d' expects argument of type 'int', but argument 3 has type 'size_t' [-Wformat]
    cython_sum.c:13675:25: warning: too many arguments for format [-Wformat-extra-args]
    cython_sum.c: In function '__Pyx_GetBufferAndValidate':
    cython_sum.c:13860:7: warning: unknown conversion type character 'z' in format [-Wformat]
    cython_sum.c:13860:7: warning: format '%s' expects argument of type 'char *', but argument 3 has type 'Py_ssize_t' [-Wformat]
    cython_sum.c:13860:7: warning: unknown conversion type character 'z' in format [-Wformat]
    cython_sum.c:13860:7: warning: too many arguments for format [-Wformat-extra-args]
    cython_sum.c: In function '__Pyx_RaiseArgtupleInvalid':
    cython_sum.c:14032:18: warning: unknown conversion type character 'z' in format [-Wformat]
    cython_sum.c:14032:18: warning: format '%s' expects argument of type 'char *', but argument 5 has type 'Py_ssize_t' [-Wformat]
    cython_sum.c:14032:18: warning: unknown conversion type character 'z' in format [-Wformat]
    cython_sum.c:14032:18: warning: too many arguments for format [-Wformat-extra-args]
    cython_sum.c: In function '__Pyx_RaiseTooManyValuesError':
    cython_sum.c:14552:18: warning: unknown conversion type character 'z' in format [-Wformat]
    cython_sum.c:14552:18: warning: too many arguments for format [-Wformat-extra-args]
    cython_sum.c: In function '__Pyx_RaiseNeedMoreValuesError':
    cython_sum.c:14558:18: warning: unknown conversion type character 'z' in format [-Wformat]
    cython_sum.c:14558:18: warning: format '%s' expects argument of type 'char *', but argument 3 has type 'Py_ssize_t' [-Wformat]
    cython_sum.c:14558:18: warning: too many arguments for format [-Wformat-extra-args]
    cython_sum.c: In function '__Pyx_ValidateAndInit_memviewslice':
    cython_sum.c:15253:22: warning: unknown conversion type character 'z' in format [-Wformat]
    cython_sum.c:15253:22: warning: format '%s' expects argument of type 'char *', but argument 3 has type 'Py_ssize_t' [-Wformat]
    cython_sum.c:15253:22: warning: unknown conversion type character 'z' in format [-Wformat]
    cython_sum.c:15253:22: warning: too many arguments for format [-Wformat-extra-args]



```python
from cython_sum import cython_sum
from numpy import *
```


```python
a = arange(1e6)
```

检查正确性：


```python
cython_sum(a)
```




    499999500000.0




```python
a.sum()
```




    499999500000.0



效率：


```python
%timeit cython_sum(a)
```

    100 loops, best of 3: 2.14 ms per loop



```python
%timeit a.sum()
```

    100 loops, best of 3: 2.38 ms per loop



```python
import zipfile

f = zipfile.ZipFile('07-06-cython-sum.zip','w',zipfile.ZIP_DEFLATED)

names = ['cython_sum.pyx',
         'setup.py']
for name in names:
    f.write(name)

f.close()

!rm -f setup*.*
!rm -f cython_sum*.*
!rm -rf build
```
## 生成编译注释


```python
%%file fib_orig.pyx
def fib(n):
    a,b = 1,1
    for i in range(n):
        a,b = a+b, a
    return a
```

    Writing fib_orig.pyx



```python
!cython -a fib_orig.pyx
```

在浏览器中打开 `fib_orig.html` 可以查看内容，`windows` 下打开网页使用：


```python
!start fib_orig.html
```

`linux` 下使用：
```
open fib_orig.html
```

其界面可能如图所示：
![界面](fib_orig.png)

点击某一行可以查看该 `Python` 代码对应的 `C` 代码。
## ctypes

### 基本用法

`ctypes` 是一个方便 `Python` 调用本地已经编译好的外部库的模块。


```python
from ctypes import util, CDLL
```

### 标准 C 库

使用 `util` 来找到标准 `C` 库：


```python
libc_name = util.find_library('c')

## on WINDOWS
print libc_name
```

    msvcr90.dll


使用 `CDLL` 来加载 `C` 库：


```python
libc = CDLL(libc_name)
```

libc 包含 `C` 标准库中的函数：


```python
libc.printf
```




    <_FuncPtr object at 0x0000000003CEE048>



调用这个函数：


```python
libc.printf("%s, %d\n", "hello", 5)
```




    9



这里显示的 `9` 是 `printf` 的返回值表示显示的字符串的长度（包括结尾的 `'\0'`），但是并没有显示结果，原因是 `printf` 函数默认是写在标准输出流上的，与 `IPython` 使用的输出流不一样，所以没有显示结果。

### C 数学库

找到数学库：


```python
libm_name = util.find_library('m')

print libm_name
```

    msvcr90.dll


调用 `atan2` 函数：


```python
libm = CDLL(libm_name)

libm.atan2(1.0, 2.0)
```


    ---------------------------------------------------------------------------

    ArgumentError                             Traceback (most recent call last)

    <ipython-input-7-e784e41ee9f3> in <module>()
          1 libm = CDLL(libm_name)
          2 
    ----> 3 libm.atan2(1.0, 2.0)
    

    ArgumentError: argument 1: <type 'exceptions.TypeError'>: Don't know how to convert parameter 1


调用这个函数出错，原因是我们需要进行一些额外工作，告诉 `Python` 函数的参数和返回值是什么样的：


```python
from ctypes import c_double

libm.atan2.argtypes = [c_double, c_double]
libm.atan2.restype = c_double
```


```python
libm.atan2(1.0, 2.0)
```




    0.4636476090008061



与 `Python` 数学库中的结果一致：


```python
from math import atan2
```


```python
atan2(1.0, 2.0)
```




    0.4636476090008061



### Numpy 和 ctypes

假设我们有这样的一个函数：
```c
float _sum(float *vec, int len) {
    float sum = 0.0;
    int i;
    for (i = 0; i < len; i++) {
        sum += vec[i];
    }
    return sum
}
```

并且已经编译成动态链接库，那么我们可以这样调用：

```python
from ctypes import c_float, CDLL, c_int
from numpy import array, float32
from numpy.ctypeslib import ndpointer

x = array([1,2,3,4], dtype=float32)

lib = CDLL(<path>)

ptr = ndpointer(float32, ndim=1, flags='C')
lib._sum.argtypes = [ptr, c_int]
lib._sum.restype = c_float

result = lib._sum(x, len(x))
```
