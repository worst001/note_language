# 错误汇总

## 字符串越界

```python
# example
 
numbers = "12345678"
print(numbers[8])

# output

Traceback (most recent call last):
  File "sample.py", line 2, in <module>
    print(numbers[8])
IndexError: string index out of range

```
