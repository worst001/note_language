## sys 模块简介


```python
import sys
```

### 命令行参数

`sys.argv` 显示传入的参数：


```python
%%writefile print_args.py
import sys
print sys.argv
```

    Writing print_args.py


运行这个程序：


```python
%run print_args.py 1 foo
```

    ['print_args.py', '1', 'foo']


第一个参数 （`sys.args[0]`） 表示的始终是执行的文件名，然后依次显示传入的参数。

删除刚才生成的文件：


```python
import os
os.remove('print_args.py')
```

### 异常消息

`sys.exc_info()` 可以显示 `Exception` 的信息，返回一个 `(type, value, traceback)` 组成的三元组，可以与 `try/catch` 块一起使用： 


```python
try:
    x = 1/0
except Exception:
    print sys.exc_info()
```

    (<type 'exceptions.ZeroDivisionError'>, ZeroDivisionError('integer division or modulo by zero',), <traceback object at 0x0000000003C6FA08>)


`sys.exc_clear()` 用于清除所有的异常消息。

### 标准输入输出流

- sys.stdin
- sys.stdout
- sys.stderr

### 退出Python

`sys.exit(arg=0)` 用于退出 Python。`0` 或者 `None` 表示正常退出，其他值表示异常。

### Python Path

`sys.path` 表示 Python 搜索模块的路径和查找顺序：


```python
sys.path
```




    ['',
     'C:\\Anaconda\\python27.zip',
     'C:\\Anaconda\\DLLs',
     'C:\\Anaconda\\lib',
     'C:\\Anaconda\\lib\\plat-win',
     'C:\\Anaconda\\lib\\lib-tk',
     'C:\\Anaconda',
     'C:\\Anaconda\\lib\\site-packages',
     'C:\\Anaconda\\lib\\site-packages\\Sphinx-1.3.1-py2.7.egg',
     'C:\\Anaconda\\lib\\site-packages\\cryptography-0.9.1-py2.7-win-amd64.egg',
     'C:\\Anaconda\\lib\\site-packages\\win32',
     'C:\\Anaconda\\lib\\site-packages\\win32\\lib',
     'C:\\Anaconda\\lib\\site-packages\\Pythonwin',
     'C:\\Anaconda\\lib\\site-packages\\setuptools-17.1.1-py2.7.egg',
     'C:\\Anaconda\\lib\\site-packages\\IPython\\extensions']



在程序中可以修改，添加新的路径。

### 操作系统信息

`sys.platform` 显示当前操作系统信息：

- `Windows: win32`
- `Mac OSX: darwin`
- `Linux:   linux2`


```python
sys.platform
```




    'win32'



返回 `Windows` 操作系统的版本：


```python
sys.getwindowsversion()
```




    sys.getwindowsversion(major=6, minor=2, build=9200, platform=2, service_pack='')



标准库中有 `planform` 模块提供更详细的信息。

### Python 版本信息


```python
sys.version
```




    '2.7.10 |Anaconda 2.3.0 (64-bit)| (default, May 28 2015, 16:44:52) [MSC v.1500 64 bit (AMD64)]'




```python
sys.version_info
```




    sys.version_info(major=2, minor=7, micro=10, releaselevel='final', serial=0)


## 与操作系统进行交互：os 模块

`os` 模块提供了对系统文件进行操作的方法：


```python
import os
```

### 文件路径操作

- `os.remove(path)` 或 `os.unlink(path)` ：删除指定路径的文件。路径可以是全名，也可以是当前工作目录下的路径。
- `os.removedirs`：删除文件，并删除中间路径中的空文件夹
- `os.chdir(path)`：将当前工作目录改变为指定的路径
- `os.getcwd()`：返回当前的工作目录
- `os.curdir`：表示当前目录的符号
- `os.rename(old, new)`：重命名文件
- `os.renames(old, new)`：重命名文件，如果中间路径的文件夹不存在，则创建文件夹
- `os.listdir(path)`：返回给定目录下的所有文件夹和文件名，不包括 `'.'` 和 `'..'` 以及子文件夹下的目录。（`'.'` 和 `'..'` 分别指当前目录和父目录）
- `os.mkdir(name)`：产生新文件夹
- `os.makedirs(name)`：产生新文件夹，如果中间路径的文件夹不存在，则创建文件夹

当前目录：


```python
os.getcwd()
```




    '/home/lijin/notes-python/05. advanced python'



当前目录的符号：


```python
os.curdir
```




    '.'



当前目录下的文件：


```python
os.listdir(os.curdir)
```




    ['05.01 overview of the sys module.ipynb',
     '05.05 datetime.ipynb',
     '05.13 decorator usage.ipynb',
     '.ipynb_checkpoints',
     '05.03 comma separated values.ipynb',
     '05.02 interacting with the OS - os.ipynb',
     '05.10 generators.ipynb',
     '05.15 scope.ipynb',
     '05.12 decorators.ipynb',
     '05.09 iterators.ipynb',
     'my_database.sqlite',
     '05.11 context managers and the with statement.ipynb',
     '05.16 dynamic code execution.ipynb',
     '05.14 the operator functools itertools toolz fn funcy module.ipynb',
     '05.04 regular expression.ipynb',
     '05.07 object-relational mappers.ipynb',
     '05.08 functions.ipynb',
     '05.06 sql databases.ipynb']



产生文件：


```python
f = open("test.file", "w")
f.close()

print "test.file" in os.listdir(os.curdir)
```

    True


重命名文件：


```python
os.rename("test.file", "test.new.file")

print "test.file" in os.listdir(os.curdir)
print "test.new.file" in os.listdir(os.curdir)
```

    False
    True


删除文件：


```python
os.remove("test.new.file")
```

### 系统常量

当前操作系统的换行符：


```python
## windows 为 \r\n
os.linesep
```




    '\n'



当前操作系统的路径分隔符：


```python
os.sep
```




    '/'



当前操作系统的环境变量中的分隔符（`';'` 或 `':'`）：


```python
os.pathsep
```




    ':'



### 其他

`os.environ` 是一个存储所有环境变量的值的字典，可以修改。


```python
os.environ["USER"]
```




    'lijin'



`os.urandom(len)` 返回指定长度的随机字节。

### os.path 模块

不同的操作系统使用不同的路径规范，这样当我们在不同的操作系统下进行操作时，可能会带来一定的麻烦，而 `os.path` 模块则帮我们解决了这个问题。


```python
import os.path
```

#### 测试

- `os.path.isfile(path)` ：检测一个路径是否为普通文件
- `os.path.isdir(path)`：检测一个路径是否为文件夹
- `os.path.exists(path)`：检测路径是否存在
- `os.path.isabs(path)`：检测路径是否为绝对路径

#### split 和 join

- `os.path.split(path)`：拆分一个路径为 `(head, tail)` 两部分
- `os.path.join(a, *p)`：使用系统的路径分隔符，将各个部分合成一个路径

#### 其他

- `os.path.abspath()`：返回路径的绝对路径
- `os.path.dirname(path)`：返回路径中的文件夹部分
- `os.path.basename(path)`：返回路径中的文件部分
- `os.path.splitext(path)`：将路径与扩展名分开
- `os.path.expanduser(path)`：展开 `'~'` 和 `'~user'`
## CSV 文件和 csv 模块

标准库中有自带的 `csv` (逗号分隔值) 模块处理 `csv` 格式的文件：


```python
import csv
```

### 读 csv 文件

假设我们有这样的一个文件：


```python
%%file data.csv
"alpha 1",  100, -1.443
"beat  3",   12, -0.0934
"gamma 3a", 192, -0.6621
"delta 2a",  15, -4.515
```

    Writing data.csv


打开这个文件，并产生一个文件 reader：


```python
fp = open("data.csv")
r = csv.reader(fp)
```

可以按行迭代数据：


```python
for row in r:
    print row
    
fp.close()
```

    ['alpha 1', '  100', ' -1.443']
    ['beat  3', '   12', ' -0.0934']
    ['gamma 3a', ' 192', ' -0.6621']
    ['delta 2a', '  15', ' -4.515']


默认数据内容都被当作字符串处理，不过可以自己进行处理：


```python
data = []

with open('data.csv') as fp:
    r = csv.reader(fp)
    for row in r:
        data.append([row[0], int(row[1]), float(row[2])])
    
data
```




    [['alpha 1', 100, -1.443],
     ['beat  3', 12, -0.0934],
     ['gamma 3a', 192, -0.6621],
     ['delta 2a', 15, -4.515]]




```python
import os
os.remove('data.csv')
```

### 写 csv 文件

可以使用 `csv.writer` 写入文件，不过相应地，传入的应该是以写方式打开的文件，不过一般要用 `'wb'` 即二进制写入方式，防止出现换行不正确的问题：


```python
data = [('one', 1, 1.5), ('two', 2, 8.0)]
with open('out.csv', 'wb') as fp:
    w = csv.writer(fp)
    w.writerows(data)
```

显示结果：


```python
!cat 'out.csv'
```

    one,1,1.5
    two,2,8.0


### 更换分隔符

默认情况下，`csv` 模块默认 `csv` 文件都是由 `excel` 产生的，实际中可能会遇到这样的问题：


```python
data = [('one, \"real\" string', 1, 1.5), ('two', 2, 8.0)]
with open('out.csv', 'wb') as fp:
    w = csv.writer(fp)
    w.writerows(data)
```


```python
!cat 'out.csv'
```

    "one, ""real"" string",1,1.5
    two,2,8.0


可以修改分隔符来处理这组数据：


```python
data = [('one, \"real\" string', 1, 1.5), ('two', 2, 8.0)]
with open('out.psv', 'wb') as fp:
    w = csv.writer(fp, delimiter="|")
    w.writerows(data)
```


```python
!cat 'out.psv'
```

    "one, ""real"" string"|1|1.5
    two|2|8.0



```python
import os
os.remove('out.psv')
os.remove('out.csv')
```

### 其他选项

`numpy.loadtxt()` 和 `pandas.read_csv()` 可以用来读写包含很多数值数据的 `csv` 文件：


```python
%%file trades.csv
Order,Date,Stock,Quantity,Price
A0001,2013-12-01,AAPL,1000,203.4
A0002,2013-12-01,MSFT,1500,167.5
A0003,2013-12-02,GOOG,1500,167.5
```

    Writing trades.csv


使用 `pandas` 进行处理，生成一个 `DataFrame` 对象：


```python
import pandas
df = pandas.read_csv('trades.csv', index_col=0)
print df
```

                 Date Stock  Quantity  Price
    Order                                   
    A0001  2013-12-01  AAPL      1000  203.4
    A0002  2013-12-01  MSFT      1500  167.5
    A0003  2013-12-02  GOOG      1500  167.5


通过名字进行索引：


```python
df['Quantity'] * df['Price']
```




    Order
    A0001    203400
    A0002    251250
    A0003    251250
    dtype: float64




```python
import os
os.remove('trades.csv')
```
## 正则表达式和 re 模块

### 正则表达式

[正则表达式](http://baike.baidu.com/view/94238.htm)是用来匹配字符串或者子串的一种模式，匹配的字符串可以很具体，也可以很一般化。

`Python` 标准库提供了 `re` 模块。 


```python
import re
```

### re.match & re.search

在 `re` 模块中， `re.match` 和 `re.search` 是常用的两个方法：

    re.match(pattern, string[, flags])
    re.search(pattern, string[, flags])

两者都寻找第一个匹配成功的部分，成功则返回一个 `match` 对象，不成功则返回 `None`，不同之处在于 `re.match` 只匹配字符串的开头部分，而 `re.search` 匹配的则是整个字符串中的子串。

### re.findall & re.finditer

`re.findall(pattern, string)` 返回所有匹配的对象， `re.finditer` 则返回一个迭代器。

### re.split

`re.split(pattern, string[, maxsplit])` 按照 `pattern` 指定的内容对字符串进行分割。

### re.sub

`re.sub(pattern, repl, string[, count])` 将 `pattern` 匹配的内容进行替换。

### re.compile

`re.compile(pattern)` 生成一个 `pattern` 对象，这个对象有匹配，替换，分割字符串的方法。

### 正则表达式规则

正则表达式由一些普通字符和一些元字符（metacharacters）组成。普通字符包括大小写的字母和数字，而元字符则具有特殊的含义：

子表达式|匹配内容
---|---
`.`| 匹配除了换行符之外的内容
`\w` | 匹配所有字母和数字字符
`\d` | 匹配所有数字，相当于 `[0-9]`
`\s` | 匹配空白，相当于 `[\t\n\t\f\v]`
`\W,\D,\S`| 匹配对应小写字母形式的补
`[...]` | 表示可以匹配的集合，支持范围表示如 `a-z`, `0-9` 等
`(...)` | 表示作为一个整体进行匹配
&#166; | 表示逻辑或
`^` | 表示匹配后面的子表达式的补
`*` | 表示匹配前面的子表达式 0 次或更多次
`+` | 表示匹配前面的子表达式 1 次或更多次
`?` | 表示匹配前面的子表达式 0 次或 1 次
`{m}` | 表示匹配前面的子表达式 m 次
`{m,}` | 表示匹配前面的子表达式至少 m 次
`{m,n}` | 表示匹配前面的子表达式至少 m 次，至多 n 次

例如：

- `ca*t       匹配： ct, cat, caaaat, ...`
- `ab\d|ac\d  匹配： ab1, ac9, ...`
- `([^a-q]bd) 匹配： rbd, 5bd, ...`

### 例子

假设我们要匹配这样的字符串：


```python
string = 'hello world'
pattern = 'hello (\w+)'

match = re.match(pattern, string)
print match
```

    <_sre.SRE_Match object at 0x0000000003A5DA80>


一旦找到了符合条件的部分，我们便可以使用 `group` 方法查看匹配的部分：


```python
if match is not None:
    print match.group(0)
```

    hello world



```python
if match is not None:
    print match.group(1)
```

    world


我们可以改变 string 的内容：


```python
string = 'hello there'
pattern = 'hello (\w+)'

match = re.match(pattern, string)
if match is not None:
    print match.group(0)
    print match.group(1)
```

    hello there
    there


通常，`match.group(0)` 匹配整个返回的内容，之后的 `1,2,3,...` 返回规则中每个括号（按照括号的位置排序）匹配的部分。

如果某个 `pattern` 需要反复使用，那么我们可以将它预先编译：


```python
pattern1 = re.compile('hello (\w+)')

match = pattern1.match(string)
if match is not None:
    print match.group(1)
```

    there


由于元字符的存在，所以对于一些特殊字符，我们需要使用 `'\'` 进行逃逸字符的处理，使用表达式 `'\\'` 来匹配 `'\'` 。

但事实上，`Python` 本身对逃逸字符也是这样处理的：


```python
pattern = '\\'
print pattern
```

    \


因为逃逸字符的问题，我们需要使用四个 `'\\\\'` 来匹配一个单独的 `'\'`：


```python
pattern = '\\\\'
path = "C:\\foo\\bar\\baz.txt"
print re.split(pattern, path)
```

    ['C:', 'foo', 'bar', 'baz.txt']


这样看起来十分麻烦，好在 `Python` 提供了 `raw string` 来忽略对逃逸字符串的处理，从而可以这样进行匹配：


```python
pattern = r'\\'
path = r"C:\foo\bar\baz.txt"
print re.split(pattern, path)
```

    ['C:', 'foo', 'bar', 'baz.txt']


如果规则太多复杂，正则表达式不一定是个好选择。

### Numpy 的 fromregex()


```python
%%file test.dat 
1312 foo
1534    bar
444  qux
```

    Writing test.dat


    fromregex(file, pattern, dtype)

`dtype` 中的内容与 `pattern` 的括号一一对应：


```python
pattern = "(\d+)\s+(...)"
dt = [('num', 'int64'), ('key', 'S3')]

from numpy import fromregex
output = fromregex('test.dat', pattern, dt)
print output
```

    [(1312L, 'foo') (1534L, 'bar') (444L, 'qux')]


显示 `num` 项：


```python
print output['num']
```

    [1312 1534  444]



```python
import os
os.remove('test.dat')
```
## datetime 模块


```python
import datetime as dt
```

`datetime` 提供了基础时间和日期的处理。

### date 对象

可以使用 `date(year, month, day)` 产生一个 `date` 对象：


```python
d1 = dt.date(2007, 9, 25)
d2 = dt.date(2008, 9, 25)
```

可以格式化 `date` 对象的输出：


```python
print d1
print d1.strftime('%A, %m/%d/%y')
print d1.strftime('%a, %m-%d-%Y')
```

    2007-09-25
    Tuesday, 09/25/07
    Tue, 09-25-2007


可以看两个日期相差多久：


```python
print d2 - d1
```

    366 days, 0:00:00


返回的是一个 `timedelta` 对象：


```python
d = d2 - d1
print d.days
print d.seconds
```

    366
    0


查看今天的日期：


```python
print dt.date.today()
```

    2015-09-10


### time 对象

可以使用 `time(hour, min, sec, us)` 产生一个 `time` 对象：


```python
t1 = dt.time(15, 38)
t2 = dt.time(18)
```

改变显示格式：


```python
print t1
print t1.strftime('%I:%M, %p')
print t1.strftime('%H:%M:%S, %p')
```

    15:38:00
    03:38, PM
    15:38:00, PM


因为没有具体的日期信息，所以 `time` 对象不支持减法操作。

### datetime 对象

可以使用 `datetime(year, month, day, hr, min, sec, us)` 来创建一个 `datetime` 对象。 

获得当前时间：


```python
d1 = dt.datetime.now()
print d1
```

    2015-09-10 20:58:50.148000


给当前的时间加上 `30` 天，`timedelta` 的参数是 `timedelta(day, hr, min, sec, us)`：


```python
d2 = d1 + dt.timedelta(30)
print d2
```

    2015-10-10 20:58:50.148000


除此之外，我们还可以通过一些指定格式的字符串来创建 `datetime` 对象：


```python
print dt.datetime.strptime('2/10/01', '%m/%d/%y')
```

    2001-02-10 00:00:00


### datetime 格式字符表

字符|含义
--|--
`%a` | 星期英文缩写
`%A` | 星期英文
`%w` | 一星期的第几天，`[0(sun),6]`
`%b` | 月份英文缩写
`%B` | 月份英文
`%d` | 日期，`[01,31]`
`%H` | 小时，`[00,23]`
`%I` | 小时，`[01,12]`
`%j` | 一年的第几天，`[001,366]`
`%m` | 月份，`[01,12]`
`%M` | 分钟，`[00,59]`
`%p` | AM 和 PM
`%S` | 秒钟，`[00,61]` （大概是有闰秒的存在）
`%U` | 一年中的第几个星期，星期日为第一天，`[00,53]`
`%W` | 一年中的第几个星期，星期一为第一天，`[00,53]`
`%y` | 没有世纪的年份
`%Y` | 完整的年份
## SQL 数据库

`Python` 提供了一系列标准的数据库的 API，这里我们介绍 sqlite 数据库的用法，其他的数据库的用法大同小异：


```python
import sqlite3 as db
```

首先我们要建立或者连接到一个数据库上：


```python
connection = db.connect("my_database.sqlite")
```

不同的数据库有着不同的连接方法，例如 cx-oracle 数据库的链接方式为：

    connection = db.connect(username, password, host, port,  'XE')

一旦建立连接，我们可以利用它的 `cursor()` 来执行 SQL 语句：


```python
cursor = connection.cursor()
cursor.execute("""CREATE TABLE IF NOT EXISTS orders(
        order_id TEXT PRIMARY KEY,
        date TEXT,
        symbol TEXT,
        quantity INTEGER,
        price NUMBER)""")
cursor.execute("""INSERT INTO orders VALUES
        ('A0001', '2013-12-01', 'AAPL', 1000, 203.4)""")
connection.commit()
```

不过为了安全起见，一般不将数据内容写入字符串再传入，而是使用这样的方式：


```python
orders = [
          ("A0002","2013-12-01","MSFT",1500,167.5),
          ("A0003","2013-12-02","GOOG",1500,167.5)
]
cursor.executemany("""INSERT INTO orders VALUES
    (?, ?, ?, ?, ?)""", orders)
connection.commit()
```

cx-oracle 数据库使用不同的方式：

    cursor.executemany("""INSERT INTO orders VALUES
    (:order_id, :date, :symbol, :quantity, :price)""",
    orders)

查看支持的数据库格式：


```python
db.paramstyle
```




    'qmark'



在 `query` 语句执行之后，我们需要进行 `commit`，否则数据库将不会接受这些变化，如果想撤销某个 `commit`，可以使用 `rollback()` 方法撤销到上一次 `commit()` 的结果：

    try:
        ... ## perform some operations
    except:
        connection.rollback()
        raise
    else:
        connection.commit()

使用 `SELECT` 语句对数据库进行查询：


```python
stock = 'MSFT'
cursor.execute("""SELECT *
    FROM orders
    WHERE symbol=?
    ORDER BY quantity""", (stock,))
for row in cursor:
    print row
```

    (u'A0002', u'2013-12-01', u'MSFT', 1500, 167.5)


`cursor.fetchone()` 返回下一条内容， `cursor.fetchall()` 返回所有查询到的内容组成的列表（可能非常大）：


```python
stock = 'AAPL'
cursor.execute("""SELECT *
    FROM orders
    WHERE symbol=?
    ORDER BY quantity""", (stock,))
cursor.fetchall()
```




    [(u'A0001', u'2013-12-01', u'AAPL', 1000, 203.4)]



关闭数据库：


```python
cursor.close()
connection.close()
```
## 对象关系映射

数据库中的记录可以与一个 `Python` 对象对应。

例如对于上一节中的数据库：

Order|Date|Stock|Quantity|Price
--|--|--|--|--
A0001|2013-12-01|AAPL|1000|203.4
A0002|2013-12-01|MSFT|1500|167.5
A0003|2013-12-02|GOOG|1500|167.5

可以用一个类来描述：

Attr.|Method
--|--
Order id| Cost
Date|
Stock|
Quant.|
Price|

可以使用 `sqlalchemy` 来实现这种对应：


```python
from sqlalchemy.ext.declarative import declarative_base
from sqlalchemy import Column, Date, Float, Integer, String

Base = declarative_base()

class Order(Base):
    __tablename__ = 'orders'
    
    order_id = Column(String, primary_key=True)
    date = Column(Date)
    symbol = Column(String)
    quantity = Column(Integer)
    price = Column(Float)
    
    def get_cost(self):
        return self.quantity*self.price
```

生成一个 `Order` 对象：


```python
import datetime
order = Order(order_id='A0004', date=datetime.date.today(), symbol='MSFT', quantity=-1000, price=187.54)
```

调用方法：


```python
order.get_cost()
```




    -187540.0



使用上一节生成的数据库产生一个 `session`：


```python
from sqlalchemy import create_engine
from sqlalchemy.orm import sessionmaker

engine = create_engine("sqlite:///my_database.sqlite")   ## 相当于 connection
Session = sessionmaker(bind=engine) ## 相当于 cursor
session = Session()
```

使用这个 `session` 向数据库中添加刚才生成的对象：


```python
session.add(order)
session.commit()
```

显示是否添加成功：


```python
for row in engine.execute("SELECT * FROM orders"):
    print row
```

    (u'A0001', u'2013-12-01', u'AAPL', 1000, 203.4)
    (u'A0002', u'2013-12-01', u'MSFT', 1500, 167.5)
    (u'A0003', u'2013-12-02', u'GOOG', 1500, 167.5)
    (u'A0004', u'2015-09-10', u'MSFT', -1000, 187.54)


使用 `filter` 进行查询，返回的是 `Order` 对象的列表：


```python
for order in session.query(Order).filter(Order.symbol=="AAPL"):
    print order.order_id, order.date, order.get_cost()
```

    A0001 2013-12-01 203400.0


返回列表的第一个：


```python
order_2 = session.query(Order).filter(Order.order_id=='A0002').first()
```


```python
order_2.symbol
```




    u'MSFT'


## 函数进阶：参数传递，高阶函数，lambda 匿名函数，global 变量，递归

### 函数是基本类型

在 `Python` 中，函数是一种基本类型的对象，这意味着

- 可以将函数作为参数传给另一个函数
- 将函数作为字典的值储存
- 将函数作为另一个函数的返回值


```python
def square(x):
    """Square of x."""
    return x*x

def cube(x):
    """Cube of x."""
    return x*x*x
```

作为字典的值：


```python
funcs = {
    'square': square,
    'cube': cube,
}
```

例子：


```python
x = 2

print square(x)
print cube(x)

for func in sorted(funcs):
    print func, funcs[func](x)
```

    4
    8
    cube 8
    square 4


### 函数参数

#### 引用传递

`Python` 中的函数传递方式是 `call by reference` 即引用传递，例如，对于这样的用法：

    x = [10, 11, 12]
    f(x)

传递给函数 `f` 的是一个指向 `x` 所包含内容的引用，如果我们修改了这个引用所指向内容的值（例如 `x[0]=999`），那么外面的 `x` 的值也会被改变。不过如果我们在函数中赋给 `x` 一个新的值（例如另一个列表），那么在函数外面的 `x` 的值不会改变：


```python
def mod_f(x):
    x[0] = 999
    return x

x = [1, 2, 3]

print x
print mod_f(x)
print x
```

    [1, 2, 3]
    [999, 2, 3]
    [999, 2, 3]



```python
def no_mod_f(x):
    x = [4, 5, 6]
    return x

x = [1,2,3]

print x
print no_mod_f(x)
print x
```

    [1, 2, 3]
    [4, 5, 6]
    [1, 2, 3]


#### 默认参数是可变的！

函数可以传递默认参数，默认参数的绑定发生在函数定义的时候，以后每次调用默认参数时都会使用同一个引用。

这样的机制会导致这种情况的发生：


```python
def f(x = []):
    x.append(1)
    return x
```

理论上说，我们希望调用 `f()` 时返回的是 `[1]`， 但事实上：


```python
print f()
print f()
print f()
print f(x = [9,9,9])
print f()
print f()
```

    [1]
    [1, 1]
    [1, 1, 1]
    [9, 9, 9, 1]
    [1, 1, 1, 1]
    [1, 1, 1, 1, 1]


而我们希望看到的应该是这样：


```python
def f(x = None):
    if x is None:
        x = []
    x.append(1)
    return x

print f()
print f()
print f()
print f(x = [9,9,9])
print f()
print f()
```

    [1]
    [1]
    [1]
    [9, 9, 9, 1]
    [1]
    [1]


### 高阶函数

以函数作为参数，或者返回一个函数的函数是高阶函数，常用的例子有 `map` 和 `filter` 函数：

`map(f, sq)` 函数将 `f` 作用到 `sq` 的每个元素上去，并返回结果组成的列表，相当于：
```python
[f(s) for s in sq]
```


```python
map(square, range(5))
```




    [0, 1, 4, 9, 16]



`filter(f, sq)` 函数的作用相当于，对于 `sq` 的每个元素 `s`，返回所有 `f(s)` 为 `True` 的 `s` 组成的列表，相当于：
```python
[s for s in sq if f(s)]
```


```python
def is_even(x):
    return x % 2 == 0

filter(is_even, range(5))
```




    [0, 2, 4]



一起使用：


```python
map(square, filter(is_even, range(5)))
```




    [0, 4, 16]



`reduce(f, sq)` 函数接受一个二元操作函数 `f(x,y)`，并对于序列 `sq` 每次合并两个元素：


```python
def my_add(x, y):
    return x + y

reduce(my_add, [1,2,3,4,5])
```




    15



传入加法函数，相当于对序列求和。

返回一个函数：


```python
def make_logger(target):
    def logger(data):
        with open(target, 'a') as f:
            f.write(data + '\n')
    return logger

foo_logger = make_logger('foo.txt')
foo_logger('Hello')
foo_logger('World')
```


```python
!cat foo.txt
```

    Hello
    World



```python
import os
os.remove('foo.txt')
```

### 匿名函数

在使用 `map`， `filter`，`reduce` 等函数的时候，为了方便，对一些简单的函数，我们通常使用匿名函数的方式进行处理，其基本形式是：

    lambda <variables>: <expression>

例如，我们可以将这个：


```python
print map(square, range(5))
```

    [0, 1, 4, 9, 16]


用匿名函数替换为：


```python
print map(lambda x: x * x, range(5))
```

    [0, 1, 4, 9, 16]


匿名函数虽然写起来比较方便（省去了定义函数的烦恼），但是有时候会比较难于阅读：


```python
s1 = reduce(lambda x, y: x+y, map(lambda x: x**2, range(1,10)))
print(s1)
```

    285


当然，更简单地，我们可以写成这样：


```python
s2 = sum(x**2 for x in range(1, 10))
print s2
```

    285


## global 变量

一般来说，函数中是可以直接使用全局变量的值的：


```python
x = 15

def print_x():
    print x
    
print_x()
```

    15


但是要在函数中修改全局变量的值，需要加上 `global` 关键字：


```python
x = 15

def print_newx():
    global x
    x = 18
    print x
    
print_newx()

print x
```

    18
    18


如果不加上这句 `global` 那么全局变量的值不会改变：


```python
x = 15

def print_newx():
    x = 18
    print x
    
print_newx()

print x
```

    18
    15


### 递归

递归是指函数在执行的过程中调用了本身，一般用于分治法，不过在 `Python` 中这样的用法十分地小，所以一般不怎么使用：

Fibocacci 数列：


```python
def fib1(n):
    """Fib with recursion."""

    ## base case
    if n==0 or n==1:
        return 1
    ## recurssive caae
    else:
        return fib1(n-1) + fib1(n-2)

print [fib1(i) for i in range(10)]
```

    [1, 1, 2, 3, 5, 8, 13, 21, 34, 55]


一个更高效的非递归版本：


```python
def fib2(n):
    """Fib without recursion."""
    a, b = 0, 1
    for i in range(1, n+1):
        a, b = b, a+b
    return b

print [fib2(i) for i in range(10)]
```

    [1, 1, 2, 3, 5, 8, 13, 21, 34, 55]


速度比较：


```python
%timeit fib1(20)
%timeit fib2(20)
```

    100 loops, best of 3: 5.35 ms per loop
    100000 loops, best of 3: 2.2 µs per loop


对于第一个递归函数来说，调用 `fib(n+2)` 的时候计算 `fib(n+1), fib(n)`，调用 `fib(n+1)` 的时候也计算了一次 `fib(n)`，这样造成了重复计算。

使用缓存机制的递归版本，这里利用了默认参数可变的性质，构造了一个缓存：


```python
def fib3(n, cache={0: 1, 1: 1}):
    """Fib with recursion and caching."""

    try:
        return cache[n]
    except KeyError:
        cache[n] = fib3(n-1) + fib3(n-2)
        return cache[n]

print [fib3(i) for i in range(10)]

%timeit fib1(20)
%timeit fib2(20)
%timeit fib3(20)
```

    [1, 1, 2, 3, 5, 8, 13, 21, 34, 55]
    100 loops, best of 3: 5.37 ms per loop
    100000 loops, best of 3: 2.19 µs per loop
    The slowest run took 150.16 times longer than the fastest. This could mean that an intermediate result is being cached 
    1000000 loops, best of 3: 230 ns per loop

## 迭代器

### 简介

迭代器对象可以在 `for` 循环中使用：


```python
x = [2, 4, 6]

for n in x:
    print n
```

    2
    4
    6


其好处是不需要对下标进行迭代，但是有些情况下，我们既希望获得下标，也希望获得对应的值，那么可以将迭代器传给 `enumerate` 函数，这样每次迭代都会返回一组 `(index, value)` 组成的元组：


```python
x = [2, 4, 6]

for i, n in enumerate(x):
    print 'pos', i, 'is', n
```

    pos 0 is 2
    pos 1 is 4
    pos 2 is 6


迭代器对象必须实现 `__iter__` 方法：


```python
x = [2, 4, 6]
i = x.__iter__()
print i
```

    <listiterator object at 0x0000000003CAE630>


`__iter__()` 返回的对象支持 `next` 方法，返回迭代器中的下一个元素：


```python
print i.next()
```

    2


当下一个元素不存在时，会 `raise` 一个 `StopIteration` 错误：


```python
print i.next()
print i.next()
```

    4
    6



```python
i.next()
```


    ---------------------------------------------------------------------------

    StopIteration                             Traceback (most recent call last)

    <ipython-input-6-e590fe0d22f8> in <module>()
    ----> 1 i.next()
    

    StopIteration: 


很多标准库函数返回的是迭代器：


```python
r = reversed(x)
print r
```

    <listreverseiterator object at 0x0000000003D615F8>


调用它的 `next()` 方法：


```python
print r.next()
print r.next()
print r.next()
```

    6
    4
    2


字典对象的 `iterkeys, itervalues, iteritems` 方法返回的都是迭代器：


```python
x = {'a':1, 'b':2, 'c':3}
i = x.iteritems()
print i
```

    <dictionary-itemiterator object at 0x0000000003D51B88>


迭代器的 `__iter__` 方法返回它本身：


```python
print i.__iter__()
```

    <dictionary-itemiterator object at 0x0000000003D51B88>



```python
print i.next()
```

    ('a', 1)


### 自定义迭代器

自定义一个 list 的取反迭代器：


```python
class ReverseListIterator(object):
    
    def __init__(self, list):
        self.list = list
        self.index = len(list)
        
    def __iter__(self):
        return self
    
    def next(self):
        self.index -= 1
        if self.index >= 0:
            return self.list[self.index]
        else:
            raise StopIteration
```


```python
x = range(10)
for i in ReverseListIterator(x):
    print i,
```

    9 8 7 6 5 4 3 2 1 0


只要我们定义了这三个方法，我们可以返回任意迭代值：


```python
class Collatz(object):
    
    def __init__(self, start):
        self.value = start
        
    def __iter__(self):
        return self
    
    def next(self):
        if self.value == 1:
            raise StopIteration
        elif self.value % 2 == 0:
            self.value = self.value / 2
        else:
            self.value = 3 * self.value + 1
        return self.value
```

这里我们实现 [Collatz 猜想](http://baike.baidu.com/view/736196.htm)：

- 奇数 n：返回 3n + 1
- 偶数 n：返回 n / 2

直到 n 为 1 为止：


```python
for x in Collatz(7):
    print x,
```

    22 11 34 17 52 26 13 40 20 10 5 16 8 4 2 1


不过迭代器对象存在状态，会出现这样的问题：


```python
i = Collatz(7)
for x, y in zip(i, i):
    print x, y
```

    22 11
    34 17
    52 26
    13 40
    20 10
    5 16
    8 4
    2 1


一个比较好的解决方法是将迭代器和可迭代对象分开处理，这里提供了一个二分树的中序遍历实现：


```python
class BinaryTree(object):
    def __init__(self, value, left=None, right=None):
        self.value = value
        self.left = left
        self.right = right

    def __iter__(self):
        return InorderIterator(self)
```


```python
class InorderIterator(object):
    
    def __init__(self, node):
        self.node = node
        self.stack = []
    
    def next(self):
        if len(self.stack) > 0 or self.node is not None:
            while self.node is not None:
                self.stack.append(self.node)
                self.node = self.node.left
            node = self.stack.pop()
            self.node = node.right
            return node.value
        else:
            raise StopIteration()
```


```python
tree = BinaryTree(
    left=BinaryTree(
        left=BinaryTree(1),
        value=2,
        right=BinaryTree(
            left=BinaryTree(3),
            value=4,
            right=BinaryTree(5)
        ),
    ),
    value=6,
    right=BinaryTree(
        value=7,
        right=BinaryTree(8)
    )
)
```


```python
for value in tree:
    print value,
```

    1 2 3 4 5 6 7 8


不会出现之前的问题：


```python
for x,y in zip(tree, tree):
    print x, y
```

    1 1
    2 2
    3 3
    4 4
    5 5
    6 6
    7 7
    8 8

## 生成器

`while` 循环通常有这样的形式：

```python
<do setup>
result = []
while True:
    <generate value>
    result.append(value)
    if <done>:
        break
```

使用迭代器实现这样的循环：

```python
class GenericIterator(object):
    def __init__(self, ...):
        <do setup>
        ## 需要额外储存状态
        <store state>
    def next(self): 
        <load state>
        <generate value>
        if <done>:
            raise StopIteration()
        <store state>
        return value
```

更简单的，可以使用生成器：

```python
def generator(...):
    <do setup>
    while True:
        <generate value>
        ## yield 说明这个函数可以返回多个值！
        yield value
        if <done>:
            break
```

生成器使用 `yield` 关键字将值输出，而迭代器则通过 `next` 的 `return` 将值返回；与迭代器不同的是，生成器会自动记录当前的状态，而迭代器则需要进行额外的操作来记录当前的状态。

对于之前的 `collatz` 猜想，简单循环的实现如下：


```python
def collatz(n):
    sequence = []
    while n != 1:
        if n % 2 == 0:
            n /= 2
        else:
            n = 3*n + 1
        sequence.append(n)
    return sequence

for x in collatz(7):
    print x,
```

    22 11 34 17 52 26 13 40 20 10 5 16 8 4 2 1


迭代器的版本如下：


```python
class Collatz(object):
    def __init__(self, start):
        self.value = start

    def __iter__(self):
        return self
    
    def next(self):
        if self.value == 1:
            raise StopIteration()
        elif self.value % 2 == 0:
            self.value = self.value/2
        else:
            self.value = 3*self.value + 1
        return self.value

for x in Collatz(7):
    print x,
```

    22 11 34 17 52 26 13 40 20 10 5 16 8 4 2 1


生成器的版本如下：


```python
def collatz(n):
    while n != 1:
        if n % 2 == 0:
            n /= 2
        else:
            n = 3*n + 1
        yield n

for x in collatz(7):
    print x,
```

    22 11 34 17 52 26 13 40 20 10 5 16 8 4 2 1


事实上，生成器也是一种迭代器：


```python
x = collatz(7)
print x
```

    <generator object collatz at 0x0000000003B63750>


它支持 `next` 方法，返回下一个 `yield` 的值：


```python
print x.next()
print x.next()
```

    22
    11


`__iter__` 方法返回的是它本身：


```python
print x.__iter__()
```

    <generator object collatz at 0x0000000003B63750>


之前的二叉树迭代器可以改写为更简单的生成器模式来进行中序遍历：


```python
class BinaryTree(object):
    def __init__(self, value, left=None, right=None):
        self.value = value
        self.left = left
        self.right = right

    def __iter__(self):
        ## 将迭代器设为生成器方法
        return self.inorder()
    
    def inorder(self):
        ## traverse the left branch
        if self.left is not None:
            for value in self.left:
                yield value
                
        ## yield node's value
        yield self.value
        
        ## traverse the right branch
        if self.right is not None:
            for value in self.right:
                yield value
```

非递归的实现：


```python
def inorder(self):
    node = self
    stack = []
    while len(stack) > 0 or node is not None:
        while node is not None:
            stack.append(node)
            node = node.left
        node = stack.pop()
        yield node.value
        node = node.right
```


```python
tree = BinaryTree(
    left=BinaryTree(
        left=BinaryTree(1),
        value=2,
        right=BinaryTree(
            left=BinaryTree(3),
            value=4,
            right=BinaryTree(5)
        ),
    ),
    value=6,
    right=BinaryTree(
        value=7,
        right=BinaryTree(8)
    )
)
for value in tree:
    print value,
```

    1 2 3 4 5 6 7 8

## with 语句和上下文管理器

```python
## create/aquire some resource
...
try:
    ## do something with the resource
    ...
finally:
    ## destroy/release the resource
    ...
```

处理文件，线程，数据库，网络编程等等资源的时候，我们经常需要使用上面这样的代码形式，以确保资源的正常使用和释放。

好在`Python` 提供了 `with` 语句帮我们自动进行这样的处理，例如之前在打开文件时我们使用： 


```python
with open('my_file', 'w') as fp:
    ## do stuff with fp
    data = fp.write("Hello world")
```

这等效于下面的代码，但是要更简便：


```python
fp = open('my_file', 'w')
try:
    ## do stuff with f
    data = fp.write("Hello world")
finally:
    fp.close()
```

### 上下文管理器

其基本用法如下：
```
with <expression>:
    <block>
```

`<expression>` 执行的结果应当返回一个实现了上下文管理器的对象，即实现这样两个方法，`__enter__` 和 `__exit__`：


```python
print fp.__enter__
print fp.__exit__
```

    <built-in method __enter__ of file object at 0x0000000003C1D540>
    <built-in method __exit__ of file object at 0x0000000003C1D540>


`__enter__` 方法在 `<block>` 执行前执行，而 `__exit__` 在 `<block>` 执行结束后执行：

比如可以这样定义一个简单的上下文管理器：


```python
class ContextManager(object):
    
    def __enter__(self):
        print "Entering"
    
    def __exit__(self, exc_type, exc_value, traceback):
        print "Exiting"
```

使用 `with` 语句执行：


```python
with ContextManager():
    print "  Inside the with statement"
```

    Entering
      Inside the with statement
    Exiting


即使 `<block>` 中执行的内容出错，`__exit__` 也会被执行：


```python
with ContextManager():
    print 1/0
```

    Entering
    Exiting



    ---------------------------------------------------------------------------

    ZeroDivisionError                         Traceback (most recent call last)

    <ipython-input-6-b509c97cb388> in <module>()
          1 with ContextManager():
    ----> 2     print 1/0
    

    ZeroDivisionError: integer division or modulo by zero


### `__`enter`__` 的返回值

如果在 `__enter__` 方法下添加了返回值，那么我们可以使用 `as` 把这个返回值传给某个参数：


```python
class ContextManager(object):
    
    def __enter__(self):
        print "Entering"
        return "my value"
    
    def __exit__(self, exc_type, exc_value, traceback):
        print "Exiting"
```

将 `__enter__` 返回的值传给 `value` 变量：


```python
with ContextManager() as value:
    print value
```

    Entering
    my value
    Exiting


一个通常的做法是将 `__enter__` 的返回值设为这个上下文管理器对象本身，文件对象就是这样做的：


```python
fp = open('my_file', 'r')
print fp.__enter__()
fp.close()
```

    <open file 'my_file', mode 'r' at 0x0000000003B63030>



```python
import os
os.remove('my_file')
```

实现方法非常简单：


```python
class ContextManager(object):
    
    def __enter__(self):
        print "Entering"
        return self
    
    def __exit__(self, exc_type, exc_value, traceback):
        print "Exiting"
```


```python
with ContextManager() as value:
    print value
```

    Entering
    <__main__.ContextManager object at 0x0000000003D48828>
    Exiting


### 错误处理

上下文管理器对象将错误处理交给 `__exit__` 进行，可以将错误类型，错误值和 `traceback` 等内容作为参数传递给 `__exit__` 函数：


```python
class ContextManager(object):
    
    def __enter__(self):
        print "Entering"
    
    def __exit__(self, exc_type, exc_value, traceback):
        print "Exiting"
        if exc_type is not None:
            print "  Exception:", exc_value
```

如果没有错误，这些值都将是 `None`, 当有错误发生的时候：


```python
with ContextManager():
    print 1/0
```

    Entering
    Exiting
      Exception: integer division or modulo by zero



    ---------------------------------------------------------------------------

    ZeroDivisionError                         Traceback (most recent call last)

    <ipython-input-14-b509c97cb388> in <module>()
          1 with ContextManager():
    ----> 2     print 1/0
    

    ZeroDivisionError: integer division or modulo by zero


在这个例子中，我们只是简单的显示了错误的值，并没有对错误进行处理，所以错误被向上抛出了，如果不想让错误抛出，只需要将 `__exit__` 的返回值设为 `True`： 


```python
class ContextManager(object):
    
    def __enter__(self):
        print "Entering"
    
    def __exit__(self, exc_type, exc_value, traceback):
        print "Exiting"
        if exc_type is not None:
            print " Exception suppresed:", exc_value
            return True
```


```python
with ContextManager():
    print 1/0
```

    Entering
    Exiting
     Exception suppresed: integer division or modulo by zero


在这种情况下，错误就不会被向上抛出。

### 数据库的例子

对于数据库的 transaction 来说，如果没有错误，我们就将其 `commit` 进行保存，如果有错误，那么我们将其回滚到上一次成功的状态。


```python
class Transaction(object):
    
    def __init__(self, connection):
        self.connection = connection
    
    def __enter__(self):
        return self.connection.cursor()
    
    def __exit__(self, exc_type, exc_value, traceback):
        if exc_value is None:
            ## transaction was OK, so commit
            self.connection.commit()
        else:
            ## transaction had a problem, so rollback
            self.connection.rollback()
```

建立一个数据库，保存一个地址表：


```python
import sqlite3 as db
connection = db.connect(":memory:")

with Transaction(connection) as cursor:
    cursor.execute("""CREATE TABLE IF NOT EXISTS addresses (
        address_id INTEGER PRIMARY KEY,
        street_address TEXT,
        city TEXT,
        state TEXT,
        country TEXT,
        postal_code TEXT
    )""")
```

插入数据：


```python
with Transaction(connection) as cursor:
    cursor.executemany("""INSERT OR REPLACE INTO addresses VALUES (?, ?, ?, ?, ?, ?)""", [
        (0, '515 Congress Ave', 'Austin', 'Texas', 'USA', '78701'),
        (1, '245 Park Avenue', 'New York', 'New York', 'USA', '10167'),
        (2, '21 J.J. Thompson Ave.', 'Cambridge', None, 'UK', 'CB3 0FA'),
        (3, 'Supreme Business Park', 'Hiranandani Gardens, Powai, Mumbai', 'Maharashtra', 'India', '400076'),
    ])
```

假设插入数据之后出现了问题：


```python
with Transaction(connection) as cursor:
    cursor.execute("""INSERT OR REPLACE INTO addresses VALUES (?, ?, ?, ?, ?, ?)""",
        (4, '2100 Pennsylvania Ave', 'Washington', 'DC', 'USA', '78701'),
    )
    raise Exception("out of addresses")
```


    ---------------------------------------------------------------------------

    Exception                                 Traceback (most recent call last)

    <ipython-input-20-ed8abdd56558> in <module>()
          3         (4, '2100 Pennsylvania Ave', 'Washington', 'DC', 'USA', '78701'),
          4     )
    ----> 5     raise Exception("out of addresses")
    

    Exception: out of addresses


那么最新的一次插入将不会被保存，而是返回上一次 `commit` 成功的状态：


```python
cursor.execute("SELECT * FROM addresses")
for row in cursor:
    print row
```

    (0, u'515 Congress Ave', u'Austin', u'Texas', u'USA', u'78701')
    (1, u'245 Park Avenue', u'New York', u'New York', u'USA', u'10167')
    (2, u'21 J.J. Thompson Ave.', u'Cambridge', None, u'UK', u'CB3 0FA')
    (3, u'Supreme Business Park', u'Hiranandani Gardens, Powai, Mumbai', u'Maharashtra', u'India', u'400076')


### contextlib 模块

很多的上下文管理器有很多相似的地方，为了防止写入很多重复的模式，可以使用 `contextlib` 模块来进行处理。

最简单的处理方式是使用 `closing` 函数确保对象的 `close()` 方法始终被调用：


```python
from contextlib import closing
import urllib

with closing(urllib.urlopen('http://www.baidu.com')) as url:
    html = url.read()

print html[:100]
```

    <!DOCTYPE html><!--STATUS OK--><html><head><meta http-equiv="content-type" content="text/html;charse


另一个有用的方法是使用修饰符 `@contextlib`：


```python
from contextlib import contextmanager

@contextmanager
def my_contextmanager():
    print "Enter"
    yield
    print "Exit"

with my_contextmanager():
    print "  Inside the with statement"
```

    Enter
      Inside the with statement
    Exit


`yield` 之前的部分可以看成是 `__enter__` 的部分，`yield` 的值可以看成是 `__enter__` 返回的值，`yield` 之后的部分可以看成是 `__exit__` 的部分。

使用 `yield` 的值：


```python
@contextmanager
def my_contextmanager():
    print "Enter"
    yield "my value"
    print "Exit"
    
with my_contextmanager() as value:
    print value
```

    Enter
    my value
    Exit


错误处理可以用 `try` 块来完成：


```python
@contextmanager
def my_contextmanager():
    print "Enter"
    try:
        yield
    except Exception as exc:
        print "   Error:", exc
    finally:
        print "Exit"
```


```python
with my_contextmanager():
    print 1/0
```

    Enter
       Error: integer division or modulo by zero
    Exit


对于之前的数据库 `transaction` 我们可以这样定义：


```python
@contextmanager
def transaction(connection):
    cursor = connection.cursor()
    try:
        yield cursor
    except:
        connection.rollback()
        raise
    else:
        connection.commit()
```
## 修饰符

### 函数是一种对象

在 `Python` 中，函数是也是一种对象。


```python
def foo(x):
    print x
    
print(type(foo))
```

    <type 'function'>


查看函数拥有的方法：


```python
dir(foo)
```




    ['__call__',
     '__class__',
     '__closure__',
     '__code__',
     '__defaults__',
     '__delattr__',
     '__dict__',
     '__doc__',
     '__format__',
     '__get__',
     '__getattribute__',
     '__globals__',
     '__hash__',
     '__init__',
     '__module__',
     '__name__',
     '__new__',
     '__reduce__',
     '__reduce_ex__',
     '__repr__',
     '__setattr__',
     '__sizeof__',
     '__str__',
     '__subclasshook__',
     'func_closure',
     'func_code',
     'func_defaults',
     'func_dict',
     'func_doc',
     'func_globals',
     'func_name']



在这些方法中，`__call__` 是最重要的一种方法： 


```python
foo.__call__(42)
```

    42


相当于：


```python
foo(42)
```

    42


因为函数是对象，所以函数可以作为参数传入另一个函数：


```python
def bar(f, x):
    x += 1
    f(x)
```


```python
bar(foo, 4)
```

    5


### 修饰符

修饰符是这样的一种函数，它接受一个函数作为输入，通常输出也是一个函数：


```python
def dec(f):
    print 'I am decorating function', id(f)
    return f
```

将 `len` 函数作为参数传入这个修饰符函数：


```python
declen = dec(len)
```

    I am decorating function 33716168


使用这个新生成的函数：


```python
declen([10,20,30])
```




    3



上面的例子中，我们仅仅返回了函数的本身，也可以利用这个函数生成一个新的函数，看一个新的例子：


```python
def loud(f):
    def new_func(*args, **kw):
        print 'calling with', args, kw
        rtn = f(*args, **kw)
        print 'return value is', rtn
        return rtn
    return new_func
```


```python
loudlen = loud(len)
```


```python
loudlen([10, 20, 30])
```

    calling with ([10, 20, 30],) {}
    return value is 3





    3



### 用 @ 来使用修饰符

`Python` 使用 `@` 符号来将某个函数替换为修饰符之后的函数： 

例如这个函数：


```python
def foo(x):
    print x
    
foo = dec(foo)
```

    I am decorating function 64021672


可以替换为：


```python
@dec
def foo(x):
    print x
```

    I am decorating function 64021112


事实上，如果修饰符返回的是一个函数，那么可以链式的使用修饰符：

```python
@dec1
@dec2
def foo(x):
    print x
```

使用修饰符 `loud` 来定义这个函数：


```python
@loud
def foo(x):
    print x
```


```python
foo(42)
```

    calling with (42,) {}
    42
    return value is None


### 例子

定义两个修饰器函数，一个将原来的函数值加一，另一个乘二：


```python
def plus_one(f):
    def new_func(x):
        return f(x) + 1
    return new_func

def times_two(f):
    def new_func(x):
        return f(x) * 2
    return new_func
```

定义函数，先乘二再加一：


```python
@plus_one
@times_two
def foo(x):
    return int(x)
```


```python
foo(13)
```




    27



### 修饰器工厂

`decorators factories` 是返回修饰器的函数，例如：


```python
def super_dec(x, y, z):
    def dec(f):
        def new_func(*args, **kw):
            print x + y + z
            return f(*args, **kw)
        return new_func
    return dec
```

它的作用在于产生一个可以接受参数的修饰器，例如我们想将 `loud` 输出的内容写入一个文件去，可以这样做：


```python
def super_loud(filename):
    fp = open(filename, 'w')
    def loud(f):
        def new_func(*args, **kw):
            fp.write('calling with' + str(args) + str(kw))
            ## 确保内容被写入
            fp.flush()
            fp.close()
            rtn = f(*args, **kw)
            return rtn
        return new_func
    return loud
```

可以这样使用这个修饰器工厂：


```python
@super_loud('test.txt')
def foo(x):
    print x
```

调用 `foo` 就会在文件中写入内容：


```python
foo(12)
```

    12


查看文件内容：


```python
with open('test.txt') as fp:
    print fp.read()
```

    calling with(12,){}



```python
import os
os.remove('test.txt')
```
## 修饰符的使用

### @classmethod 修饰符

在 `Python` 标准库中，有很多自带的修饰符，例如 `classmethod` 将一个对象方法转换了类方法： 


```python
class Foo(object):
    @classmethod
    def bar(cls, x):
        print 'the input is', x
        
    def __init__(self):
        pass

```

类方法可以通过 `类名.方法` 来调用：


```python
Foo.bar(12)
```

    the input is 12


### @property 修饰符

有时候，我们希望像 __Java__ 一样支持 `getters` 和 `setters` 的方法，这时候就可以使用 `property` 修饰符：


```python
class Foo(object):
    def __init__(self, data):
        self.data = data
    
    @property
    def x(self):
        return self.data
```

此时可以使用 `.x` 这个属性查看数据（不需要加上括号）：


```python
foo = Foo(23)
foo.x
```




    23



这样做的好处在于，这个属性是只读的：


```python
foo.x = 1
```


    ---------------------------------------------------------------------------

    AttributeError                            Traceback (most recent call last)

    <ipython-input-5-e5e7e6c675ef> in <module>()
    ----> 1 foo.x = 1
    

    AttributeError: can't set attribute


如果想让它变成可读写，可以加上一个修饰符 `@x.setter`：


```python
class Foo(object):
    def __init__(self, data):
        self.data = data
    
    @property
    def x(self):
        return self.data
    
    @x.setter
    def x(self, value):
        self.data = value
```


```python
foo = Foo(23)
print foo.x
```

    23


可以通过属性改变它的值：


```python
foo.x = 1
print foo.x
```

    1


### Numpy 的 @vectorize 修饰符

`numpy` 的 `vectorize` 函数讲一个函数转换为 `ufunc`，事实上它也是一个修饰符：


```python
from numpy import vectorize, arange

@vectorize
def f(x):
    if x <= 0:
        return x
    else:
        return 0

f(arange(-10.0,10.0))
```




    array([-10.,  -9.,  -8.,  -7.,  -6.,  -5.,  -4.,  -3.,  -2.,  -1.,   0.,
             0.,   0.,   0.,   0.,   0.,   0.,   0.,   0.,   0.])



### 注册一个函数

来看这样的一个例子，定义一个类：


```python
class Registry(object):
    def __init__(self):
        self._data = {}
    def register(self, f, name=None):
        if name == None:
            name = f.__name__
        self._data[name] = f
        setattr(self, name, f)
```

`register` 方法接受一个函数，将这个函数名作为属性注册到对象中。

产生该类的一个对象：


```python
registry = Registry()
```

使用该对象的 `register` 方法作为修饰符：


```python
@registry.register
def greeting():
    print "hello world"
```

这样这个函数就被注册到 `registry` 这个对象中去了：


```python
registry._data
```




    {'greeting': <function __main__.greeting>}




```python
registry.greeting
```




    <function __main__.greeting>



[flask](http://flask.pocoo.org) ，一个常用的网络应用，处理 url 的机制跟这个类似。

### 使用 @wraps

一个通常的问题在于：


```python
def logging_call(f):
    def wrapper(*a, **kw):
        print 'calling {}'.format(f.__name__)
        return f(*a, **kw)
    return wrapper

@logging_call
def square(x):
    '''
    square function.
    '''
    return x ** 2

print square.__doc__, square.__name__
```

    None wrapper


我们使用修饰符之后，`square` 的 `metadata` 完全丢失了，返回的函数名与函数的 `docstring` 都不对。

一个解决的方法是从 `functools` 模块导入 `wraps` 修饰符来修饰我们的修饰符：


```python
import functools

def logging_call(f):
    @functools.wraps(f)
    def wrapper(*a, **kw):
        print 'calling {}'.format(f.__name__)
        return f(*a, **kw)
    return wrapper

@logging_call
def square(x):
    '''
    square function.
    '''
    return x ** 2

print square.__doc__, square.__name__
```

    
        square function.
         square


现在这个问题解决了，所以在自定义修饰符方法的时候为了避免出现不必要的麻烦，尽量使用 `wraps` 来修饰修饰符！

### Class 修饰符

与函数修饰符类似，类修饰符是这样一类函数，接受一个类作为参数，通常返回一个新的类。
## operator, functools, itertools, toolz, fn, funcy 模块

### operator 模块


```python
import operator as op
```

`operator` 模块提供了各种操作符（`+,*,[]`）的函数版本方便使用：

加法：


```python
print reduce(op.add, range(10))
```

    45


乘法：


```python
print reduce(op.mul, range(1,10))
```

    362880


`[]`：


```python
my_list = [('a', 1), ('bb', 4), ('ccc', 2), ('dddd', 3)]

## 标准排序
print sorted(my_list)

## 使用元素的第二个元素排序
print sorted(my_list, key=op.itemgetter(1))

## 使用第一个元素的长度进行排序：
print sorted(my_list, key=lambda x: len(x[0]))
```

    [('a', 1), ('bb', 4), ('ccc', 2), ('dddd', 3)]
    [('a', 1), ('ccc', 2), ('dddd', 3), ('bb', 4)]
    [('a', 1), ('bb', 4), ('ccc', 2), ('dddd', 3)]


### functools 模块

`functools` 包含很多跟函数相关的工具，比如之前看到的 `wraps` 函数，不过最常用的是 `partial` 函数，这个函数允许我们使用一个函数中生成一个新函数，这个函数使用原来的函数，不过某些参数被指定了：


```python
from functools import partial

## 将 reduce 的第一个参数指定为加法，得到的是类似求和的函数
sum_ = partial(reduce, op.add)

## 将 reduce 的第一个参数指定为乘法，得到的是类似求连乘的函数
prod_ = partial(reduce, op.mul)

print sum_([1,2,3,4])
print prod_([1,2,3,4])
```

    10
    24


`partial` 函数还可以按照键值对传入固定参数。

### itertools 模块

`itertools` 包含很多与迭代器对象相关的工具，其中比较常用的是排列组合生成器 `permutations` 和 `combinations`，还有在数据分析中常用的 `groupby` 生成器：


```python
from itertools import cycle, groupby, islice, permutations, combinations
```

`cycle` 返回一个无限的迭代器，按照顺序重复输出输入迭代器中的内容，`islice` 则返回一个迭代器中的一段内容：


```python
print list(islice(cycle('abcd'), 0, 10))
```

    ['a', 'b', 'c', 'd', 'a', 'b', 'c', 'd', 'a', 'b']


`groupby` 返回一个字典，按照指定的 `key` 对一组数据进行分组，字典的键是 `key`，值是一个迭代器： 


```python
animals = sorted(['pig', 'cow', 'giraffe', 'elephant',
                  'dog', 'cat', 'hippo', 'lion', 'tiger'], key=len)

## 按照长度进行分组
for k, g in groupby(animals, key=len):
    print k, list(g)
print
```

    3 ['pig', 'cow', 'dog', 'cat']
    4 ['lion']
    5 ['hippo', 'tiger']
    7 ['giraffe']
    8 ['elephant']
    


排列：


```python
print [''.join(p) for p in permutations('abc')]
```

    ['abc', 'acb', 'bac', 'bca', 'cab', 'cba']


组合：


```python
print [list(c) for c in combinations([1,2,3,4], r=2)]
```

    [[1, 2], [1, 3], [1, 4], [2, 3], [2, 4], [3, 4]]


### toolz, fn 和 funcy 模块

这三个模块的作用是方便我们在编程的时候使用函数式编程的风格。
## 作用域

在函数中，`Python` 从命名空间中寻找变量的顺序如下：

- `local function scope`
- `enclosing scope`
- `global scope`
- `builtin scope`

例子：

## local 作用域


```python
def foo(a,b):
    c = 1
    d = a + b + c
```

这里所有的变量都在 `local` 作用域。

### global 作用域


```python
c = 1
def foo(a,b):
    d = a + b + c
```

这里的 `c` 就在 `global` 作用域。

### global 关键词

使用 `global` 关键词可以在 `local` 作用域中修改 `global` 作用域的值。


```python
c = 1
def foo():
    global c
    c = 2
    
print c
foo()
print c
```

    1
    2


其作用是将 `c` 指向 `global` 中的 `c`。

如果不加关键词，那么 `local` 作用域的 `c` 不会影响 `global` 作用域中的值：


```python
c = 1
def foo():
    c = 2
    
print c
foo()
print c
```

    1
    1


### built-in 作用域


```python
def list_length(a):
    return len(a)

a = [1,2,3]
print list_length(a)
```

    3


这里函数 `len` 就是在 `built-in` 作用域中：


```python
import __builtin__

__builtin__.len
```




    <function len>



### class 中的作用域

Global | MyClass
---|---
`var = 0` <br> `MyClass` <br> `access_class` | `var = 1`<br>`access_class` 


```python
## global
var = 0

class MyClass(object):
    ## class variable
    var = 1
    
    def access_class_c(self):
        print 'class var:', self.var
    
    def write_class_c(self):
        MyClass.var = 2
        print 'class var:', self.var
        
    def access_global_c(self):
        print 'global var:', var
    
    def write_instance_c(self):
        self.var = 3
        print 'instance var:', self.var
```

Global | MyClass | obj
---|---|----
`var = 0` <br> `MyClass` <br> [`access_class`] <br> `obj` | `var = 1`<br>`access_class`  |


```python
obj = MyClass()
```

查询 `self.var` 时，由于 `obj` 不存在 `var`，所以跳到 MyClass 中：

Global | MyClass | obj
---|---|----
`var = 0` <br> `MyClass` <br> [`access_class` <br> `self`] <br> `obj` | `var = 1`<br>`access_class`  |


```python
obj.access_class_c()
```

    class var: 1


查询 `var` 直接跳到 `global` 作用域：

Global | MyClass | obj
---|---|----
`var = 0` <br> `MyClass` <br> [`access_class` <br> `self`] <br> `obj` | `var = 1`<br>`access_class`  |


```python
obj.access_global_c()
```

    global var: 0


修改类中的 `MyClass.var`：

Global | MyClass | obj
---|---|----
`var = 0` <br> `MyClass` <br> [`access_class` <br> `self`] <br> `obj` | `var = 2`<br>`access_class`  |


```python
obj.write_class_c()
```

    class var: 2


修改实例中的 `var` 时，会直接在 `obj` 域中创建一个：

Global | MyClass | obj
---|---|----
`var = 0` <br> `MyClass` <br> [`access_class` <br> `self`] <br> `obj` | `var = 2`<br>`access_class`  | `var = 3`


```python
obj.write_instance_c()
```

    instance var: 3



```python
MyClass.var
```




    2



`MyClass` 中的 `var` 并没有改变。

### 词法作用域

对于嵌套函数：


```python
def outer():
    a = 1
    def inner():
        print "a =", a
    inner()
    
outer()
```

    a = 1


如果里面的函数没有找到变量，那么会向外一层寻找变量，如果再找不到，则到 `global` 作用域。

返回的是函数的情况：


```python
def outer():
    a = 1
    def inner():
        return a
    return inner
    
func = outer()

print 'a (1):', func()
```

    a (1): 1


func() 函数中调用的 `a` 要从它定义的地方开始寻找，而不是在 `func` 所在的作用域寻找。
## 动态编译

### 标准编程语言

对于 **C** 语言，代码一般要先编译，再执行。

    .c -> .exe

### 解释器语言

shell 脚本

    .sh -> interpreter

### Byte Code 编译

**Python, Java** 等语言先将代码编译为 byte code（不是机器码），然后再处理：

    .py -> .pyc -> interpreter

### eval 函数

    eval(statement, glob, local)

使用 `eval` 函数动态执行代码，返回执行的值：


```python
a = 1

eval("a+1")
```




    2



可以接收明明空间参数：


```python
local = dict(a=2)
glob = {}
eval("a+1", glob, local)
```




    3



这里 `local` 中的 `a` 先被找到。

### exec 函数

    exec(statement, glob, local)

使用 `exec` 可以添加修改原有的变量。


```python
a = 1

exec("b = a+1")

print b
```

    2



```python
local = dict(a=2)
glob = {}
exec("b = a+1", glob, local)

print local
```

    {'a': 2, 'b': 3}


执行之后，`b` 在 `local` 命名空间中。

### 警告

动态执行的时候要注意，不要执行不信任的用户输入，因为它们拥有 `Python` 的全部权限。

### compile 函数生成 byte code

    compile(str, filename, mode)


```python
a = 1
c = compile("a+2", "", 'eval')

eval(c)
```




    3




```python
a = 1
c = compile("b=a+2", "", 'exec')

exec(c)
b
```




    3



### abstract syntax trees


```python
import ast
```


```python
tree = ast.parse("a+2", "", "eval")

ast.dump(tree)
```




    "Expression(body=BinOp(left=Name(id='a', ctx=Load()), op=Add(), right=Num(n=2)))"



改变常数的值：


```python
tree.body.right.n = 3

ast.dump(tree)
```




    "Expression(body=BinOp(left=Name(id='a', ctx=Load()), op=Add(), right=Num(n=3)))"




```python
a = 1
c = compile(tree, '', 'eval')

eval(c)
```




    4



安全的使用方法 `literal_eval` ，只支持基本值的操作：


```python
ast.literal_eval("[10.0, 2, True, 'foo']")
```




    [10.0, 2, True, 'foo']


