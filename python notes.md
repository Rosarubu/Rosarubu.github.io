# python notes

[Top 10 Python idioms I wish I'd learned earlier](http://prooffreaderplus.blogspot.com/2014/11/top-10-python-idioms-i-wished-id.html) 

[What does the “yield” keyword do?](https://stackoverflow.com/questions/231767/what-does-the-yield-keyword-do)

## enumerate

Iterate over different types of python objects and get back both the index and the value of each item

```python
enumerate(seq,[start=0]) #index start from 0

#iterate over a list/tuple

my_list = ['a', 'b', 'c']
for index, item in enumerate(my_list):
    print(index, item)
#output:
0 a
1 b
2 c
```

Iterate over a list with tuple as elements. Also could iterate over string

```python
L = [('Matt', 20), ('Karim', 30), ('Maya', 40)]
for idx, (name, age) in enumerate(L):
    print(idx,name,age)

#output
0 Matt 20
1 Karim 30
2 Maya 40
```

```python

my_phrase = ["No", "one", "expects", "the", "Spanish", "Inquisition"]
my_dict = {key: value for value, key in enumerate(my_phrase)}
print(my_dict)
reversed_dict = {value: key for key, value in my_dict.items()}
print(reversed_dict)

#output
{'Inquisition': 5, 'No': 0, 'expects': 2, 'one': 1, 'Spanish': 4, 'the': 3}
{0: 'No', 1: 'one', 2: 'expects', 3: 'the', 4: 'Spanish', 5: 'Inquisition'}
	
```



## sort a dictionary by values

```python
d = {"Pierre": 42, "Anne": 33, "Zoe": 24}

import operator
sorted_d = sorted(d.items(), key=operator.itemgetter(1))
sorted_d = sorted(d.items(), key=lambda x: x[1])
#output
[ ('Zoe', 24), ('Anne', 33), ('Pierre', 42)]

#reverse
sorted_d = sorted(d.items(), key=operator.itemgetter(1),reverse=True)
#output
[('Pierre', 42), ('Anne', 33), ('Zoe', 24)]


#OrderedDict
from collections import OrderedDict
dd = OrderedDict(sorted(d.items(), key=lambda x: x[1]))
print(dd)
OrderedDict([('Pierre', 24), ('Anne', 33), ('Zoe', 42)])
```

## specify python 2/3 while run code & pip

```python
py -2 hello.py
py -3 hello.py
```

or insert below line to the first line of your .py file. Coding  information in the second line.

```python
#! python2
# coding: utf-8 

#! python3
# coding: utf-8 
```

pip

```shell
py -2 -m pip install XXXX
py -3 -m pip install XXXX
```

## list

```python
#deduplicate:
a = [1,1,2,3]
a = list(set(a))
[1, 2, 3]

#intersection：
a = [1,2,3,4,5]
b = [1,3,5,6]
list(set(a) & set(b))
[1, 3, 5]

a = [1,2,3,4,5]
b = [1,3,5,6]
set(a).intersection(b)
```

