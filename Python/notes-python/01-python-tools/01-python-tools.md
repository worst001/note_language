# Python 简介

## **Python** 历史

`Python` 的创始人为荷兰人吉多·范罗苏姆（`Guido van Rossum`）。1989年的圣诞节期间，吉多·范罗苏姆为了在阿姆斯特丹打发时间，决心开发一个新的脚本解释程序，作为 ABC 语言的一种继承。之所以选中 `Python` 作为程序的名字，是因为他是 BBC 电视剧——蒙提·派森的飞行马戏团（`Monty Python's Flying Circus`）的爱好者。

1991年，第一个 Python 编译器诞生。它是用C语言实现的，并能够调用C语言的库文件。

`Python 2.0` 于 2000 年 10 月 16 日发布，增加了实现完整的垃圾回收，并且支持 `Unicode`。

`Python 3.0` 于 2008 年 12 月 3 日发布，此版不完全兼容之前的 `Python` 源代码。不过，很多新特性后来也被移植到旧的 `Python 2.6/2.7` 版本。

## 第一行Python代码

安装好 `Python` 之后，在命令行下输入：

    python

就可以进入 `Python` 解释器的页面。

按照惯例，第一行代码应该是输出 `"hello world!"`：


```python
print "hello world!"
```

    hello world!


相对与 `Java，C` 等语言，`Python` 仅仅使用一行语句就完成的了这个任务。

可以将这句话的内容保存到一个文本文件中，并使用后缀名 `.py` 结尾，例如 `hello_world.py`，在命令行下运行这个程序：

    python hello_world.py

也会输出 `"hello world!"` 的结果。

## Python 之禅

在 **Python** 解释器下输入 

```import this```

会出来这样一首小诗：


```python
import this
```

    The Zen of Python, by Tim Peters
    
    Beautiful is better than ugly.
    Explicit is better than implicit.
    Simple is better than complex.
    Complex is better than complicated.
    Flat is better than nested.
    Sparse is better than dense.
    Readability counts.
    Special cases aren't special enough to break the rules.
    Although practicality beats purity.
    Errors should never pass silently.
    Unless explicitly silenced.
    In the face of ambiguity, refuse the temptation to guess.
    There should be one-- and preferably only one --obvious way to do it.
    Although that way may not be obvious at first unless you're Dutch.
    Now is better than never.
    Although never is often better than *right* now.
    If the implementation is hard to explain, it's a bad idea.
    If the implementation is easy to explain, it may be a good idea.
    Namespaces are one honking great idea -- let's do more of those!


这首诗反映了**Python**的设计哲学——**Python**是一种追求优雅，明确，简单的编程语言，但事实上，产生这首诗的代码并没有写的那么简单易懂：


```python
s = """Gur Mra bs Clguba, ol Gvz Crgref

Ornhgvshy vf orggre guna htyl.
Rkcyvpvg vf orggre guna vzcyvpvg.
Fvzcyr vf orggre guna pbzcyrk.
Pbzcyrk vf orggre guna pbzcyvpngrq.
Syng vf orggre guna arfgrq.
Fcnefr vf orggre guna qrafr.
Ernqnovyvgl pbhagf.
Fcrpvny pnfrf nera'g fcrpvny rabhtu gb oernx gur ehyrf.
Nygubhtu cenpgvpnyvgl orngf chevgl.
Reebef fubhyq arire cnff fvyragyl.
Hayrff rkcyvpvgyl fvyraprq.
Va gur snpr bs nzovthvgl, ershfr gur grzcgngvba gb thrff.
Gurer fubhyq or bar-- naq cersrenoyl bayl bar --boivbhf jnl gb qb vg.
Nygubhtu gung jnl znl abg or boivbhf ng svefg hayrff lbh'er Qhgpu.
Abj vf orggre guna arire.
Nygubhtu arire vf bsgra orggre guna *evtug* abj.
Vs gur vzcyrzragngvba vf uneq gb rkcynva, vg'f n onq vqrn.
Vs gur vzcyrzragngvba vf rnfl gb rkcynva, vg znl or n tbbq vqrn.
Anzrfcnprf ner bar ubaxvat terng vqrn -- yrg'f qb zber bs gubfr!"""

d = {}
for c in (65, 97):
    for i in range(26):
        d[chr(i+c)] = chr((i+13) % 26 + c)

print "".join([d.get(c, c) for c in s])
```

    The Zen of Python, by Tim Peters
    
    Beautiful is better than ugly.
    Explicit is better than implicit.
    Simple is better than complex.
    Complex is better than complicated.
    Flat is better than nested.
    Sparse is better than dense.
    Readability counts.
    Special cases aren't special enough to break the rules.
    Although practicality beats purity.
    Errors should never pass silently.
    Unless explicitly silenced.
    In the face of ambiguity, refuse the temptation to guess.
    There should be one-- and preferably only one --obvious way to do it.
    Although that way may not be obvious at first unless you're Dutch.
    Now is better than never.
    Although never is often better than *right* now.
    If the implementation is hard to explain, it's a bad idea.
    If the implementation is easy to explain, it may be a good idea.
    Namespaces are one honking great idea -- let's do more of those!


> Life is short. Use Python.
# Ipython 解释器

## 进入ipython

通常我们并不使用**Python**自带的解释器，而是使用另一个比较方便的解释器——**ipython**解释器，命令行下输入：

    ipython

即可进入**ipython**解释器。

所有在**python**解释器下可以运行的代码都可以在**ipython**解释器下运行：


```python
print "hello, world"
```

    hello, world


可以进行简单赋值操作：


```python
a = 1
```

直接在解释器中输入变量名，会显示变量的值（不需要加`print`）：


```python
a
```




    1




```python
b = [1, 2, 3]
```

## ipython magic命令

**ipython**解释器提供了很多以百分号`%`开头的`magic`命令，这些命令很像linux系统下的命令行命令（事实上有些是一样的）。

查看所有的`magic`命令：


```python
%lsmagic
```




    Available line magics:
    %alias  %alias_magic  %autocall  %automagic  %autosave  %bookmark  %cd  %clear  %cls  %colors  %config  %connect_info  %copy  %ddir  %debug  %dhist  %dirs  %doctest_mode  %echo  %ed  %edit  %env  %gui  %hist  %history  %install_default_config  %install_ext  %install_profiles  %killbgscripts  %ldir  %less  %load  %load_ext  %loadpy  %logoff  %logon  %logstart  %logstate  %logstop  %ls  %lsmagic  %macro  %magic  %matplotlib  %mkdir  %more  %notebook  %page  %pastebin  %pdb  %pdef  %pdoc  %pfile  %pinfo  %pinfo2  %popd  %pprint  %precision  %profile  %prun  %psearch  %psource  %pushd  %pwd  %pycat  %pylab  %qtconsole  %quickref  %recall  %rehashx  %reload_ext  %ren  %rep  %rerun  %reset  %reset_selective  %rmdir  %run  %save  %sc  %set_env  %store  %sx  %system  %tb  %time  %timeit  %unalias  %unload_ext  %who  %who_ls  %whos  %xdel  %xmode
    
    Available cell magics:
    %%!  %%HTML  %%SVG  %%bash  %%capture  %%cmd  %%debug  %%file  %%html  %%javascript  %%latex  %%perl  %%prun  %%pypy  %%python  %%python2  %%python3  %%ruby  %%script  %%sh  %%svg  %%sx  %%system  %%time  %%timeit  %%writefile
    
    Automagic is ON, % prefix IS NOT needed for line magics.



`line magic` 以一个百分号开头，作用与一行；

`cell magic` 以两个百分号开头，作用于整个cell。

最后一行`Automagic is ON, % prefix IS NOT needed for line magics.`说明在此时即使不加上`%`也可以使用这些命令。

使用 `whos` 查看当前的变量空间：


```python
%whos
```

    Variable   Type    Data/Info
    ----------------------------
    a          int     1
    b          list    n=3


使用 `reset` 重置当前变量空间：


```python
%reset -f
```

再查看当前变量空间：


```python
%whos
```

    Interactive namespace is empty.


使用 `pwd` 查看当前工作文件夹：


```python
%pwd
```




    u'C:\\Users\\lijin\\Documents\\Git\\python-tutorial\\01. python tools'



使用 `mkdir` 产生新文件夹：


```python
%mkdir demo_test
```

使用 `cd` 改变工作文件夹：


```python
%cd demo_test/
```

    C:\Users\lijin\Documents\Git\python-tutorial\01. python tools\demo_test


使用 `writefile` 将cell中的内容写入文件：


```python
%%writefile hello_world.py
print "hello world"
```

    Writing hello_world.py


使用 `ls` 查看当前工作文件夹的文件：


```python
%ls
```

     驱动器 C 中的卷是 System
     卷的序列号是 DC4B-D785
    
     C:\Users\lijin\Documents\Git\python-tutorial\01. python tools\demo_test 的目录
    
    2015/09/18  11:32    <DIR>          .
    2015/09/18  11:32    <DIR>          ..
    2015/09/18  11:32                19 hello_world.py
                   1 个文件             19 字节
                   2 个目录 121,763,831,808 可用字节


使用 `run` 命令来运行这个代码：


```python
%run hello_world.py
```

    hello world


删除这个文件：


```python
import os
os.remove('hello_world.py')
```

查看当前文件夹，`hello_world.py` 已被删除：


```python
%ls
```

     驱动器 C 中的卷是 System
     卷的序列号是 DC4B-D785
    
     C:\Users\lijin\Documents\Git\python-tutorial\01. python tools\demo_test 的目录
    
    2015/09/18  11:32    <DIR>          .
    2015/09/18  11:32    <DIR>          ..
                   0 个文件              0 字节
                   2 个目录 121,763,831,808 可用字节


返回上一层文件夹：


```python
%cd ..
```

    C:\Users\lijin\Documents\Git\python-tutorial\01. python tools


使用 `rmdir` 删除文件夹：


```python
%rmdir demo_test
```

使用 `hist` 查看历史命令：


```python
%hist
```

    print "hello, world"
    a = 1
    a
    b = [1, 2, 3]
    %lsmagic
    %whos
    %reset -f
    %whos
    %pwd
    %mkdir demo_test
    %cd demo_test/
    %%writefile hello_world.py
    print "hello world"
    %ls
    %run hello_world.py
    import os
    os.remove('hello_world.py')
    %ls
    %cd ..
    %rmdir demo_test
    %hist


## ipython 使用

使用 `?` 查看函数的帮助：


```python
sum?
```

使用 `??` 查看函数帮助和函数源代码（如果是用**python**实现的）：


```python
# 导入numpy和matplotlib两个包
%pylab
# 查看其中sort函数的帮助
sort??
```

    Using matplotlib backend: Qt4Agg
    Populating the interactive namespace from numpy and matplotlib


**ipython** 支持使用 `<tab>` 键自动补全命令。

使用 `_` 使用上个cell的输出结果：


```python
a = 12
a
```




    12




```python
_ + 13
```




    25



可以使用 `!` 来执行一些系统命令。


```python
!ping baidu.com
```

    
    正在 Ping baidu.com [180.149.132.47] 具有 32 字节的数据:
    来自 180.149.132.47 的回复: 字节=32 时间=69ms TTL=49
    来自 180.149.132.47 的回复: 字节=32 时间=64ms TTL=49
    来自 180.149.132.47 的回复: 字节=32 时间=61ms TTL=49
    来自 180.149.132.47 的回复: 字节=32 时间=63ms TTL=49
    
    180.149.132.47 的 Ping 统计信息:
        数据包: 已发送 = 4，已接收 = 4，丢失 = 0 (0% 丢失)，
    往返行程的估计时间(以毫秒为单位):
        最短 = 61ms，最长 = 69ms，平均 = 64ms


当输入出现错误时，**ipython**会指出出错的位置和原因：


```python
1 + "hello"
```


    ---------------------------------------------------------------------------

    TypeError                                 Traceback (most recent call last)

    <ipython-input-25-d37bedb9732a> in <module>()
    ----> 1 1 + "hello"
    

    TypeError: unsupported operand type(s) for +: 'int' and 'str'

# Ipython notebook

在命令行下输入命令：

    ipython notebook

会打开一个notebook本地服务器，一般地址是 http://localhost:8888

**`ipython notebook`** 支持两种模式的cell：

* Markdown
* Code

这里不做过多介绍。
# 使用 Anaconda

[Anaconda](http://www.continuum.io/downloads)是一个很好用的Python IDE，它集成了很多科学计算需要使用的**python**第三方工具包。

## conda 的使用 

根据自己的操作系统安装好[Anaconda](http://www.continuum.io/downloads)后，在命令行下输入：

    conda list

可以看已经安装好的**python**第三方工具包，这里我们使用 `magic` 命令 `%%cmd` 在 `ipython cell` 中来执行这个命令：


```python
!conda list
```

    # packages in environment at C:\Anaconda:
    #
    _license                  1.1                      py27_0  
    alabaster                 0.7.3                    py27_0  
    anaconda                  2.3.0                np19py27_0  
    argcomplete               0.8.9                    py27_0  
    astropy                   1.0.3                np19py27_0  
    babel                     1.3                      py27_0  
    backports.ssl-match-hostname 3.4.0.2                   <pip>
    basemap                   1.0.7                np19py27_0  
    bcolz                     0.9.0                np19py27_0  
    beautiful-soup            4.3.2                    py27_1  
    beautifulsoup4            4.3.2                     <pip>
    binstar                   0.11.0                   py27_0  
    bitarray                  0.8.1                    py27_1  
    blaze                     0.8.0                     <pip>
    blaze-core                0.8.0                np19py27_0  
    blz                       0.6.2                np19py27_1  
    bokeh                     0.9.0                np19py27_0  
    boto                      2.38.0                   py27_0  
    bottleneck                1.0.0                np19py27_0  
    cartopy                   0.13.0               np19py27_0  
    cdecimal                  2.3                      py27_1  
    certifi                   14.05.14                 py27_0  
    cffi                      1.1.0                    py27_0  
    clyent                    0.3.4                    py27_0  
    colorama                  0.3.3                    py27_0  
    conda                     3.17.0                   py27_0  
    conda-build               1.14.1                   py27_0  
    conda-env                 2.4.2                    py27_0  
    configobj                 5.0.6                    py27_0  
    cryptography              0.9.1                    py27_0  
    cython                    0.22.1                   py27_0  
    cytoolz                   0.7.3                    py27_0  
    datashape                 0.4.5                np19py27_0  
    decorator                 3.4.2                    py27_0  
    docutils                  0.12                     py27_1  
    dynd-python               0.6.5                np19py27_0  
    enum34                    1.0.4                    py27_0  
    fastcache                 1.0.2                    py27_0  
    flask                     0.10.1                   py27_1  
    funcsigs                  0.4                      py27_0  
    geopy                     1.11.0                    <pip>
    geos                      3.4.2                         3  
    gevent                    1.0.1                    py27_0  
    gevent-websocket          0.9.3                    py27_0  
    greenlet                  0.4.7                    py27_0  
    grin                      1.2.1                    py27_2  
    h5py                      2.5.0                np19py27_1  
    hdf5                      1.8.15.1                      2  
    idna                      2.0                      py27_0  
    ipaddress                 1.0.7                    py27_0  
    ipython                   3.2.0                    py27_0  
    ipython-notebook          3.2.0                    py27_0  
    ipython-qtconsole         3.2.0                    py27_0  
    itsdangerous              0.24                     py27_0  
    jdcal                     1.0                      py27_0  
    jedi                      0.8.1                    py27_0  
    jinja2                    2.7.3                    py27_2  
    jsonschema                2.4.0                    py27_0  
    launcher                  1.0.0                         1  
    libpython                 1.0                      py27_1  
    llvmlite                  0.5.0                    py27_0  
    lxml                      3.4.4                    py27_0  
    markupsafe                0.23                     py27_0  
    matplotlib                1.4.3                np19py27_1  
    menuinst                  1.0.4                    py27_0  
    mingw                     4.7                           1  
    mistune                   0.5.1                    py27_1  
    mock                      1.3.0                    py27_0  
    multipledispatch          0.4.7                    py27_0  
    networkx                  1.9.1                    py27_0  
    nltk                      3.0.3                np19py27_0  
    node-webkit               0.10.1                        0  
    nose                      1.3.7                    py27_0  
    numba                     0.19.1               np19py27_0  
    numexpr                   2.4.3                np19py27_0  
    numpy                     1.9.2                    py27_0  
    odo                       0.3.2                np19py27_0  
    openpyxl                  1.8.5                    py27_0  
    owslib                    0.9.0                    py27_0  
    pandas                    0.16.2               np19py27_0  
    patsy                     0.3.0                np19py27_0  
    pbr                       1.3.0                    py27_0  
    pep8                      1.6.2                    py27_0  
    pillow                    2.9.0                    py27_0  
    pip                       7.1.2                    py27_0  
    ply                       3.6                      py27_0  
    proj4                     4.9.1                    py27_1  
    psutil                    2.2.1                    py27_0  
    py                        1.4.27                   py27_0  
    pyasn1                    0.1.7                    py27_0  
    pycosat                   0.6.1                    py27_0  
    pycparser                 2.14                     py27_0  
    pycrypto                  2.6.1                    py27_3  
    pyepsg                    0.2.0                    py27_0  
    pyflakes                  0.9.2                    py27_0  
    pygments                  2.0.2                    py27_0  
    pyopenssl                 0.15.1                   py27_1  
    pyparsing                 2.0.3                    py27_0  
    pyqt                      4.10.4                   py27_1  
    pyreadline                2.0                      py27_0  
    pyshp                     1.2.1                    py27_0  
    pytables                  3.2.0                np19py27_0  
    pytest                    2.7.1                    py27_0  
    python                    2.7.10                        0  
    python-dateutil           2.4.2                    py27_0  
    pytz                      2015.4                   py27_0  
    pywin32                   219                      py27_0  
    pyyaml                    3.11                     py27_2  
    pyzmq                     14.7.0                   py27_0  
    requests                  2.7.0                    py27_0  
    rope                      0.9.4                    py27_1  
    runipy                    0.1.3                    py27_0  
    scikit-image              0.11.3               np19py27_0  
    scikit-learn              0.16.1               np19py27_0  
    scipy                     0.16.0               np19py27_0  
    setuptools                18.1                     py27_0  
    shapely                   1.5.11                 nppy27_0  
    six                       1.9.0                    py27_0  
    snowballstemmer           1.2.0                    py27_0  
    sockjs-tornado            1.0.1                    py27_0  
    sphinx                    1.3.1                    py27_0  
    sphinx-rtd-theme          0.1.7                     <pip>
    sphinx_rtd_theme          0.1.7                    py27_0  
    spyder                    2.3.5.2                  py27_0  
    spyder-app                2.3.5.2                  py27_0  
    sqlalchemy                1.0.5                    py27_0  
    ssl_match_hostname        3.4.0.2                  py27_0  
    statsmodels               0.6.1                np19py27_0  
    sympy                     0.7.6                    py27_0  
    tables                    3.2.0                     <pip>
    theano                    0.7.0                     <pip>
    toolz                     0.7.2                    py27_0  
    tornado                   4.2                      py27_0  
    ujson                     1.33                     py27_0  
    unicodecsv                0.9.4                    py27_0  
    werkzeug                  0.10.4                   py27_0  
    wheel                     0.24.0                   py27_0  
    xlrd                      0.9.3                    py27_0  
    xlsxwriter                0.7.3                    py27_0  
    xlwings                   0.3.5                    py27_0  
    xlwt                      1.0.0                    py27_0  
    zlib                      1.2.8                         0  


第一次安装好 [Anaconda](http://www.continuum.io/downloads) 以后，可以在命令行输入以下命令使 [Anaconda](http://www.continuum.io/downloads) 保持最新：

    conda update conda
    conda update anaconda

conda 是一种很强大的工具，具体用法可以参照它的[文档](http://conda.pydata.org/docs/)。

也可以参考它的 [cheat sheet](http://conda.pydata.org/docs/_downloads/conda-cheatsheet.pdf) 来快速查看它的用法。

可以使用它来安装，更新，卸载第三方的 **python** 工具包：

    conda install <some package>
    conda update <some package>
    conda remove <some package>

在安装或更新时可以指定安装的版本号，例如需要使用 `numpy 1.8.1`：

    conda install numpy=1.8.1
    conda update numpy=1.8.1

查看 `conda` 的信息：

    conda info


```python
!conda info
```

    Current conda install:
    
                 platform : win-64
            conda version : 3.17.0
      conda-build version : 1.14.1
           python version : 2.7.10.final.0
         requests version : 2.7.0
         root environment : C:\Anaconda  (writable)
      default environment : C:\Anaconda
         envs directories : C:\Anaconda\envs
            package cache : C:\Anaconda\pkgs
             channel URLs : https://repo.continuum.io/pkgs/free/win-64/
                            https://repo.continuum.io/pkgs/free/noarch/
                            https://repo.continuum.io/pkgs/pro/win-64/
                            https://repo.continuum.io/pkgs/pro/noarch/
              config file : None
        is foreign system : False
    


一个很棒的功能是 `conda` 可以产生一个自定义的环境，假设在安装的是 **Python 2.7** 的情况下，想使用 **Python 3.4**，只需要在命令行下使用 `conda` 产生一个新的环境：

    conda create -n py34 python=3.4

这里这个环境被命名为 `py34` ，可以根据喜好将 `py34` 改成其他的名字。

使用这个环境时，只需要命令行下输入：

``` python
activate py34 #(windows)
source activate py34 #(linux, mac)
```

此时，我们的 **Python** 版本便是 **`python 3.4`**了。

## spyder 编辑器

`Anaconda` 默认使用的编辑器是 `spyder`，可以在命令行下输入：

    spyder

来进入这个编辑器，具体使用方法不做介绍。
