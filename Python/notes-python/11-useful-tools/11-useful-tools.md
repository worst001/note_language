## pprint 模块：打印 Python 对象

`pprint` 是 pretty printer 的缩写，用来打印 Python 数据结构，与 `print` 相比，它打印出来的结构更加整齐，便于阅读。


```python
import pprint
```

生成一个 Python 对象：


```python
data = (
    "this is a string", 
    [1, 2, 3, 4], 
    ("more tuples", 1.0, 2.3, 4.5), 
    "this is yet another string"
    )
```

使用普通的 `print` 函数：


```python
print data
```

    ('this is a string', [1, 2, 3, 4], ('more tuples', 1.0, 2.3, 4.5), 'this is yet another string')


使用 `pprint` 模块中的 `pprint` 函数：


```python
pprint.pprint(data)
```

    ('this is a string',
     [1, 2, 3, 4],
     ('more tuples', 1.0, 2.3, 4.5),
     'this is yet another string')


可以看到，这样打印出来的公式更加美观。
## pickle, cPickle 模块：序列化 Python 对象

`pickle` 模块实现了一种算法，可以将任意一个 `Python` 对象转化为一系列的字节，也可以将这些字节重构为一个有相同特征的新对象。

由于字节可以被传输或者存储，因此 `pickle` 事实上实现了传递或者保存 `Python` 对象的功能。

`cPickle` 使用 `C` 而不是 `Python` 实现了相同的算法，因此速度上要比 `pickle` 快一些。但是它不允许用户从 `pickle` 派生子类。如果子类对你的使用来说无关紧要，那么 `cPickle` 是个更好的选择。


```python
try:
    import cPickle as pickle
except:
    import pickle
```

### 编码和解码

使用 `pickle.dumps()` 可以将一个对象转换为字符串（`dump string`）：


```python
data = [ { 'a':'A', 'b':2, 'c':3.0 } ]

data_string = pickle.dumps(data)

print "DATA:"
print data
print "PICKLE:"
print data_string
```

    DATA:
    [{'a': 'A', 'c': 3.0, 'b': 2}]
    PICKLE:
    (lp1
    (dp2
    S'a'
    S'A'
    sS'c'
    F3
    sS'b'
    I2
    sa.


虽然 `pickle` 编码的字符串并不一定可读，但是我们可以用 `pickle.loads()` 来从这个字符串中恢复原对象中的内容（`load string`）：


```python
data_from_string = pickle.loads(data_string)

print data_from_string
```

    [{'a': 'A', 'c': 3.0, 'b': 2}]


### 编码协议

`dumps` 可以接受一个可省略的 `protocol` 参数（默认为 0），目前有 3 种编码方式：

- 0：原始的 `ASCII` 编码格式
- 1：二进制编码格式
- 2：更有效的二进制编码格式

当前最高级的编码可以通过 `HIGHEST_PROTOCOL` 查看：


```python
print pickle.HIGHEST_PROTOCOL
```

    2


例如：


```python
data_string_1 = pickle.dumps(data, 1)

print "Pickle 1:", data_string_1

data_string_2 = pickle.dumps(data, 2)

print "Pickle 2:", data_string_2
```

    Pickle 1: ]q}q(UaUAUcG@      UbKua.
    Pickle 2: �]q}q(UaUAUcG@      UbKua.


如果 `protocol` 参数指定为负数，那么将调用当前的最高级的编码协议进行编码：


```python
print pickle.dumps(data, -1)
```

    �]q}q(UaUAUcG@      UbKua.


从这些格式中恢复对象时，不需要指定所用的协议，`pickle.load()` 会自动识别：


```python
print "Load 1:", pickle.loads(data_string_1)
print "Load 2:", pickle.loads(data_string_2)
```

    Load 1: [{'a': 'A', 'c': 3.0, 'b': 2}]
    Load 2: [{'a': 'A', 'c': 3.0, 'b': 2}]


### 存储和读取 pickle 文件

除了将对象转换为字符串这种方式，`pickle` 还支持将对象写入一个文件中，通常我们将这个文件命名为 `xxx.pkl`，以表示它是一个 `pickle` 文件： 

存储和读取的函数分别为：

- `pickle.dump(obj, file, protocol=0)` 将对象序列化并存入 `file` 文件中
- `pickle.load(file)` 从 `file` 文件中的内容恢复对象

将对象存入文件：


```python
with open("data.pkl", "wb") as f:
    pickle.dump(data, f)
```

从文件中读取：


```python
with open("data.pkl") as f:
    data_from_file = pickle.load(f)
    
print data_from_file
```

    [{'a': 'A', 'c': 3.0, 'b': 2}]


清理生成的文件：


```python
import os
os.remove("data.pkl")
```
## json 模块：处理 JSON 数据

[JSON (JavaScript Object Notation)](http://json.org) 是一种轻量级的数据交换格式，易于人阅读和编写，同时也易于机器解析和生成。

### JSON 基础

`JSON` 的基础结构有两种：键值对 (`name/value pairs`) 和数组 (`array`)。

`JSON` 具有以下形式：

- `object` - 对象，用花括号表示，形式为（数据是无序的）：
    - `{ pair_1, pair_2, ..., pair_n }`
- `pair` - 键值对，形式为：
    - `string : value`
- `array` - 数组，用中括号表示，形式为（数据是有序的）：
    - `[value_1, value_2, ..., value_n ]`
- `value` - 值，可以是
    - `string` 字符串
    - `number` 数字
    - `object` 对象
    - `array` 数组
    - `true / false / null` 特殊值
- `string` 字符串

例子：

```json
{
    "name": "echo",
    "age": 24,
    "coding skills": ["python", "matlab", "java", "c", "c++", "ruby", "scala"],
    "ages for school": { 
        "primary school": 6,
        "middle school": 9,
        "high school": 15,
        "university": 18
    },
    "hobby": ["sports", "reading"],
    "married": false
}
```

### JSON 与 Python 的转换

假设我们已经将上面这个 `JSON` 对象写入了一个字符串：


```python
import json
from pprint import pprint

info_string = """
{
    "name": "echo",
    "age": 24,
    "coding skills": ["python", "matlab", "java", "c", "c++", "ruby", "scala"],
    "ages for school": { 
        "primary school": 6,
        "middle school": 9,
        "high school": 15,
        "university": 18
    },
    "hobby": ["sports", "reading"],
    "married": false
}
"""
```

我们可以用 `json.loads()` (load string) 方法从字符串中读取 `JSON` 数据：


```python
info = json.loads(info_string)

pprint(info)
```

    {u'age': 24,
     u'ages for school': {u'high school': 15,
                          u'middle school': 9,
                          u'primary school': 6,
                          u'university': 18},
     u'coding skills': [u'python',
                        u'matlab',
                        u'java',
                        u'c',
                        u'c++',
                        u'ruby',
                        u'scala'],
     u'hobby': [u'sports', u'reading'],
     u'married': False,
     u'name': u'echo'}


此时，我们将原来的 `JSON` 数据变成了一个 `Python` 对象，在我们的例子中这个对象是个字典（也可能是别的类型，比如列表）：


```python
type(info)
```




    dict



可以使用 `json.dumps()` 将一个 `Python` 对象变成 `JSON` 对象：


```python
info_json = json.dumps(info)

print info_json
```

    {"name": "echo", "age": 24, "married": false, "ages for school": {"middle school": 9, "university": 18, "high school": 15, "primary school": 6}, "coding skills": ["python", "matlab", "java", "c", "c++", "ruby", "scala"], "hobby": ["sports", "reading"]}


从中我们可以看到，生成的 `JSON` 字符串中，数组的元素顺序是不变的（始终是 `["python", "matlab", "java", "c", "c++", "ruby", "scala"]`），而对象的元素顺序是不确定的。

### 生成和读取 JSON 文件

与 `pickle` 类似，我们可以直接从文件中读取 `JSON` 数据，也可以将对象保存为 `JSON` 格式。

- `json.dump(obj, file)` 将对象保存为 JSON 格式的文件
- `json.load(file)` 从 JSON 文件中读取数据


```python
with open("info.json", "w") as f:
    json.dump(info, f)
```

可以查看 `info.json` 的内容：


```python
with open("info.json") as f:
    print f.read()
```

    {"name": "echo", "age": 24, "married": false, "ages for school": {"middle school": 9, "university": 18, "high school": 15, "primary school": 6}, "coding skills": ["python", "matlab", "java", "c", "c++", "ruby", "scala"], "hobby": ["sports", "reading"]}


从文件中读取数据：


```python
with open("info.json") as f:
    info_from_file = json.load(f)
    
pprint(info_from_file)
```

    {u'age': 24,
     u'ages for school': {u'high school': 15,
                          u'middle school': 9,
                          u'primary school': 6,
                          u'university': 18},
     u'coding skills': [u'python',
                        u'matlab',
                        u'java',
                        u'c',
                        u'c++',
                        u'ruby',
                        u'scala'],
     u'hobby': [u'sports', u'reading'],
     u'married': False,
     u'name': u'echo'}


删除生成的文件：


```python
import os
os.remove("info.json")
```
## glob 模块：文件模式匹配


```python
import glob
```

`glob` 模块提供了方便的文件模式匹配方法。

例如，找到所有以 `.ipynb` 结尾的文件名：


```python
glob.glob("*.ipynb")
```




    ['11.03 json.ipynb',
     '11.01 pprint.ipynb',
     '11.02 pickle and cpickle.ipynb',
     '11.04 glob.ipynb']



`glob` 函数支持三种格式的语法：

- `*` 匹配单个或多个字符
- `?` 匹配任意单个字符
- `[]` 匹配指定范围内的字符，如：[0-9]匹配数字。

假设我们要匹配第 09 节所有的 `.ipynb` 文件：


```python
glob.glob("../09*/*.ipynb")
```




    ['../09. theano/09.05 configuration settings and compiling modes.ipynb',
     '../09. theano/09.03 gpu on windows.ipynb',
     '../09. theano/09.07 loop with scan.ipynb',
     '../09. theano/09.13 modern net on mnist.ipynb',
     '../09. theano/09.11 net on mnist.ipynb',
     '../09. theano/09.09 logistic regression .ipynb',
     '../09. theano/09.10 softmax on mnist.ipynb',
     '../09. theano/09.01 introduction and installation.ipynb',
     '../09. theano/09.02 theano basics.ipynb',
     '../09. theano/09.12 random streams.ipynb',
     '../09. theano/09.04 graph structures.ipynb',
     '../09. theano/09.14 convolutional net on mnist.ipynb',
     '../09. theano/09.08 linear regression.ipynb',
     '../09. theano/09.15 tensor module.ipynb',
     '../09. theano/09.06 conditions in theano.ipynb']



匹配数字开头的文件夹名：


```python
glob.glob("../[0-9]*")
```




    ['../04. scipy',
     '../02. python essentials',
     '../07. interfacing with other languages',
     '../11. useful tools',
     '../05. advanced python',
     '../10. something interesting',
     '../03. numpy',
     '../06. matplotlib',
     '../08. object-oriented programming',
     '../01. python tools',
     '../09. theano']


## shutil 模块：高级文件操作


```python
import shutil
import os
```

`shutil` 是 `Python` 中的高级文件操作模块。

### 复制文件


```python
with open("test.file", "w") as f:
    pass

print "test.file" in os.listdir(os.curdir)
```

    True


`shutil.copy(src, dst)` 将源文件复制到目标地址：


```python
shutil.copy("test.file", "test.copy.file")

print "test.file" in os.listdir(os.curdir)
print "test.copy.file" in os.listdir(os.curdir)
```

    True
    True


如果目标地址中间的文件夹不存在则会报错：


```python
try:
    shutil.copy("test.file", "my_test_dir/test.copy.file")
except IOError as msg:
    print msg
```

    [Errno 2] No such file or directory: 'my_test_dir/test.copy.file'


另外的一个函数 `shutil.copyfile(src, dst)` 与 `shutil.copy` 使用方法一致，不过只是简单复制文件的内容，并不会复制文件本身的读写可执行权限，而 `shutil.copy` 则是完全复制。

### 复制文件夹

将文件转移到 `test_dir` 文件夹：


```python
os.renames("test.file", "test_dir/test.file")
os.renames("test.copy.file", "test_dir/test.copy.file")
```

使用 `shutil.copytree` 来复制文件夹：


```python
shutil.copytree("test_dir/", "test_dir_copy/")

"test_dir_copy" in os.listdir(os.curdir)
```




    True



### 删除非空文件夹

`os.removedirs` 不能删除非空文件夹：


```python
try:
    os.removedirs("test_dir_copy")
except Exception as msg:
    print msg
```

    [Errno 39] Directory not empty: 'test_dir_copy'


使用 `shutil.rmtree` 来删除非空文件夹：


```python
shutil.rmtree("test_dir_copy")
```

### 移动文件夹

`shutil.move` 可以整体移动文件夹，与 `os.rename` 功能差不多。

### 产生压缩文件

查看支持的压缩文件格式：


```python
shutil.get_archive_formats()
```




    [('bztar', "bzip2'ed tar-file"),
     ('gztar', "gzip'ed tar-file"),
     ('tar', 'uncompressed tar file'),
     ('zip', 'ZIP file')]



产生压缩文件：

`shutil.make_archive(basename, format, root_dir)`


```python
shutil.make_archive("test_archive", "zip", "test_dir/")
```




    '/home/lijin/notes-python/11. useful tools/test_archive.zip'



清理生成的文件和文件夹：


```python
os.remove("test_archive.zip")
shutil.rmtree("test_dir/")
```
## gzip, zipfile, tarfile 模块：处理压缩文件


```python
import os, shutil, glob
import zlib, gzip, bz2, zipfile, tarfile
```

gzip 

### zilb 模块

`zlib` 提供了对字符串进行压缩和解压缩的功能：


```python
orginal = "this is a test string"

compressed = zlib.compress(orginal)

print compressed
print zlib.decompress(compressed)
```

    x�+��,V �D�����⒢̼t S��
    this is a test string


同时提供了两种校验和的计算方法：


```python
print zlib.adler32(orginal) & 0xffffffff
```

    1407780813



```python
print zlib.crc32(orginal) & 0xffffffff
```

    4236695221


### gzip 模块

`gzip` 模块可以产生 `.gz` 格式的文件，其压缩方式由 `zlib` 模块提供。

我们可以通过 `gzip.open` 方法来读写 `.gz` 格式的文件： 


```python
content = "Lots of content here"
with gzip.open('file.txt.gz', 'wb') as f:
    f.write(content)
```

读：


```python
with gzip.open('file.txt.gz', 'rb') as f:
    file_content = f.read()

print file_content
```

    Lots of content here


将压缩文件内容解压出来：


```python
with gzip.open('file.txt.gz', 'rb') as f_in, open('file.txt', 'wb') as f_out:
    shutil.copyfileobj(f_in, f_out)
```

此时，目录下应有 `file.txt` 文件，内容为：


```python
with open("file.txt") as f:
    print f.read()
```

    Lots of content here



```python
os.remove("file.txt.gz")
```

#### bz2 模块

`bz2` 模块提供了另一种压缩文件的方法：


```python
orginal = "this is a test string"

compressed = bz2.compress(orginal)

print compressed
print bz2.decompress(compressed)
```

    BZh91AY&SY*�v  	��@ "�   1 0"zi��FLT`�軒)�P�˰
    this is a test string


### zipfile 模块

产生一些 `file.txt` 的复制：


```python
for i in range(10):
    shutil.copy("file.txt", "file.txt." + str(i))
```

将这些复制全部压缩到一个 `.zip` 文件中：


```python
f = zipfile.ZipFile('files.zip','w')

for name in glob.glob("*.txt.[0-9]"):
    f.write(name)
    os.remove(name)
    
f.close()
```

解压这个 `.zip` 文件，用 `namelist` 方法查看压缩文件中的子文件名：


```python
f = zipfile.ZipFile('files.zip','r')
print f.namelist()
```

    ['file.txt.9', 'file.txt.6', 'file.txt.2', 'file.txt.1', 'file.txt.5', 'file.txt.4', 'file.txt.3', 'file.txt.7', 'file.txt.8', 'file.txt.0']


使用 `f.read(name)` 方法来读取 `name` 文件中的内容：


```python
for name in f.namelist():
    print name, "content:", f.read(name)

f.close()
```

    file.txt.9 content: Lots of content here
    file.txt.6 content: Lots of content here
    file.txt.2 content: Lots of content here
    file.txt.1 content: Lots of content here
    file.txt.5 content: Lots of content here
    file.txt.4 content: Lots of content here
    file.txt.3 content: Lots of content here
    file.txt.7 content: Lots of content here
    file.txt.8 content: Lots of content here
    file.txt.0 content: Lots of content here


可以用 `extract(name)` 或者 `extractall()` 解压单个或者全部文件。

### tarfile 模块

支持 `.tar` 格式文件的读写：

例如可以这样将 `file.txt` 写入：


```python
f = tarfile.open("file.txt.tar", "w")
f.add("file.txt")
f.close()
```

清理生成的文件：


```python
os.remove("file.txt")
os.remove("file.txt.tar")
os.remove("files.zip")
```
## logging 模块：记录日志

`logging` 模块可以用来记录日志：


```python
import logging
```

`logging` 的日志类型有以下几种：

- `logging.critical(msg)`
- `logging.error(msg)`
- `logging.warning(msg)`
- `logging.info(msg)`
- `logging.debug(msg)`

级别排序为：`CRITICAL > ERROR > WARNING > INFO > DEBUG > NOTSET`

默认情况下，`logging` 的日志级别为 `WARNING`，只有不低于 `WARNING` 级别的日志才会显示在命令行。


```python
logging.critical('This is critical message')
logging.error('This is error message')
logging.warning('This is warning message')

## 不会显示
logging.info('This is info message')
logging.debug('This is debug message')
```

    CRITICAL:root:This is critical message
    ERROR:root:This is error message
    WARNING:root:This is warning message


可以这样修改默认的日志级别：


```python
logging.root.setLevel(level=logging.INFO)

logging.info('This is info message')
```

    INFO:root:This is info message


可以通过 `logging.basicConfig()` 函数来改变默认的日志显示方式：


```python
logging.basicConfig(format='%(asctime)s: %(levelname)s: %(message)s')

logger = logging.getLogger("this program")

logger.critical('This is critical message')
```

    CRITICAL:this program:This is critical message

## string 模块：字符串处理


```python
import string
```

标点符号：


```python
string.punctuation
```




    '!"#$%&\'()*+,-./:;<=>?@[\\]^_`{|}~'



字母表：


```python
print string.letters
print string.ascii_letters
```

    ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz
    abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ


小写和大写：


```python
print string.ascii_lowercase
print string.lowercase

print string.ascii_uppercase
print string.uppercase
```

    abcdefghijklmnopqrstuvwxyz
    abcdefghijklmnopqrstuvwxyz
    ABCDEFGHIJKLMNOPQRSTUVWXYZ
    ABCDEFGHIJKLMNOPQRSTUVWXYZ



```python
print string.lower
```

    <function lower at 0x7efda4f2ae60>


数字：


```python
string.digits
```




    '0123456789'



16 进制数字：


```python
string.hexdigits
```




    '0123456789abcdefABCDEF'



每个单词的首字符大写：


```python
string.capwords("this is a big world")
```




    'This Is A Big World'



将指定的单词放到中央：


```python
string.center("test", 20)
```




    '        test        '


## collections 模块：更多数据结构


```python
import collections
```

### 计数器

可以使用 `Counter(seq)` 对序列中出现的元素个数进行统计。

例如，我们可以统计一段文本中出现的单词及其出现的次数：


```python
from string import punctuation

sentence = "One, two, three, one, two, tree, I come from China."

words_count = collections.Counter(sentence.translate(None, punctuation).lower().split())

print words_count
```

    Counter({'two': 2, 'one': 2, 'from': 1, 'i': 1, 'tree': 1, 'three': 1, 'china': 1, 'come': 1})


### 双端队列

双端队列支持从队头队尾出入队：


```python
dq = collections.deque()

for i in xrange(10):
    dq.append(i)
    
print dq

for i in xrange(10):
    print dq.pop(), 

print 

for i in xrange(10):
    dq.appendleft(i)
    
print dq

for i in xrange(10):
    print dq.popleft(),
```

    deque([0, 1, 2, 3, 4, 5, 6, 7, 8, 9])
    9 8 7 6 5 4 3 2 1 0
    deque([9, 8, 7, 6, 5, 4, 3, 2, 1, 0])
    9 8 7 6 5 4 3 2 1 0


与列表相比，双端队列在队头的操作更快：


```python
lst = []
dq = collections.deque()

%timeit -n100 lst.insert(0, 10)
%timeit -n100 dq.appendleft(10)
```

    100 loops, best of 3: 598 ns per loop
    100 loops, best of 3: 291 ns per loop


### 有序字典

字典的 `key` 按顺序排列：


```python
items = (
    ('A', 1),
    ('B', 2),
    ('C', 3)
)

regular_dict = dict(items)
ordered_dict = collections.OrderedDict(items)

print 'Regular Dict:'
for k, v in regular_dict.items():
    print k, v

print 'Ordered Dict:'
for k, v in ordered_dict.items():
    print k, v
```

    Regular Dict:
    A 1
    C 3
    B 2
    Ordered Dict:
    A 1
    B 2
    C 3


### 带默认值的字典

对于 `Python` 自带的词典 `d`，当 `key` 不存在的时候，调用 `d[key]` 会报错，但是 `defaultdict` 可以为这样的 `key` 提供一个指定的默认值，我们只需要在定义时提供默认值的类型即可，如果 `key` 不存在返回指定类型的默认值：


```python
dd = collections.defaultdict(list)

print dd["foo"]

dd = collections.defaultdict(int)

print dd["foo"]

dd = collections.defaultdict(float)

print dd["foo"]
```

    []
    0
    0.0

## requests 模块：HTTP for Human


```python
import requests
```

Python 标准库中的 `urllib2` 模块提供了你所需要的大多数 `HTTP` 功能，但是它的 `API` 不是特别方便使用。

`requests` 模块号称 `HTTP for Human`，它可以这样使用：


```python
r = requests.get("http://httpbin.org/get")
r = requests.post('http://httpbin.org/post', data = {'key':'value'})
r = requests.put("http://httpbin.org/put")
r = requests.delete("http://httpbin.org/delete")
r = requests.head("http://httpbin.org/get")
r = requests.options("http://httpbin.org/get")
```

### 传入 URL 参数

假如我们想访问 `httpbin.org/get?key=val`，我们可以使用 `params` 传入这些参数：


```python
payload = {'key1': 'value1', 'key2': 'value2'}
r = requests.get("http://httpbin.org/get", params=payload)
```

查看 `url` ：


```python
print(r.url)
```

    http://httpbin.org/get?key2=value2&key1=value1


### 读取响应内容

`Requests` 会自动解码来自服务器的内容。大多数 `unicode` 字符集都能被无缝地解码。


```python
r = requests.get('https://github.com/timeline.json')

print r.text
```

    {"message":"Hello there, wayfaring stranger. If you’re reading this then you probably didn’t see our blog post a couple of years back announcing that this API would go away: http://git.io/17AROg Fear not, you should be able to get what you need from the shiny new Events API instead.","documentation_url":"https://developer.github.com/v3/activity/events/#list-public-events"}


查看文字编码：


```python
r.encoding
```




    'utf-8'



每次改变文字编码，`text` 的内容也随之变化：


```python
r.encoding = "ISO-8859-1"

r.text
```




    u'{"message":"Hello there, wayfaring stranger. If you\xe2\x80\x99re reading this then you probably didn\xe2\x80\x99t see our blog post a couple of years back announcing that this API would go away: http://git.io/17AROg Fear not, you should be able to get what you need from the shiny new Events API instead.","documentation_url":"https://developer.github.com/v3/activity/events/#list-public-events"}'



`Requests` 中也有一个内置的 `JSON` 解码器处理 `JSON` 数据：


```python
r.json()
```




    {u'documentation_url': u'https://developer.github.com/v3/activity/events/#list-public-events',
     u'message': u'Hello there, wayfaring stranger. If you\xe2\x80\x99re reading this then you probably didn\xe2\x80\x99t see our blog post a couple of years back announcing that this API would go away: http://git.io/17AROg Fear not, you should be able to get what you need from the shiny new Events API instead.'}



如果 `JSON` 解码失败， `r.json` 就会抛出一个异常。

### 响应状态码


```python
r = requests.get('http://httpbin.org/get')

r.status_code
```




    407



### 响应头


```python
r.headers['Content-Type']
```




    'text/html'


