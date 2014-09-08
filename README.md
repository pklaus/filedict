FileDict is a dictionary interface that saves and loads its data from an sqlite database using keys.
FileDict dictionary provides ACID access, has virtually no size limit, and can be accessed by several processes concurrently.

It is meant as a quick-and-simple general-purpose solution. It is rarely the best solution, but it is usually good enough, and much faster to set-up.

Performance obviously cannot compare to the builtin dictionary, but it is reasonable and of low complexity (refer to sqlite for more details on that).

**This version is a port to Python 3.3** (most likely any prior Python 3.x should work too).

Examples
-----------

```python3
$ python3.3
>>> import filedict
>>> d=filedict.FileDict(filename="example.dict")
>>> d['bla'] = 10
>>> d[(2,1)] = ['hello', (1,2) ]
-- exit --
$ python3.3
>>> import filedict
>>> d=filedict.FileDict(filename="example.dict")
>>> print(d['bla'])
10
>>> print(list(d.items()))
[['bla', 10], [(2, 1), ['hello', (1, 2)]]]
>>> print(dict(d))
{'bla': 10, (2, 1): ['hello', (1, 2)]}
```

Using batch mode for fast writing:

```python3
>>> d=filedict.FileDict(filename="try.dict")
>>> with d.batch:  # using .batch suspend commits, making a batch of changes quicker
>>>    for i in range(100000):
>>>            d[i] = i**2
(takes about 5 seconds on my laptop)
>>> print(len(d))
100000
>>> del d[103]
>>> print(len(d))
99999
```


Limitations
-----------

* All data (keys and values) must be pickle-able
* Keys and values are stored as a copy, so changing them after assignment will not update the dictionary.


Future
-------

Additions in the future may include:

* An LRU-cache for fetching entries
* A storage strategy different than Sqlite


About this port to Python 3
---------------------------

FileDict was ported to Python 3.3 in order to be used in projects requiring the latest Python version.
Due to a change of the hash function used in FileDict (now it's MD5), sqlite databases created with the
version for Python 2 are not compatible with this port to Python 3! This change of the hash function
was needed as Python's built-in hash() function changed its behaviour in Python 3
(it no longer yields the same results when given a binary string).


Resources
---------

* The **[original FileDict](https://github.com/erezsh/filedict)** had been written by Erez Sh. He published it on Github.
* There is a similar Python module available called [sqlitedict](https://pypi.python.org/pypi/sqlitedict). Since 2014-07 it also has a branch with support for Python3. It is, however, still limited to textual keys for the dictionary-like database.
