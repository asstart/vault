---

layout: default
title: Package, distribute, install, run python code
parent: python_in
grand_parent: mongodb

---

## Sources

[Packaging Python Projects](https://packaging.python.org/en/latest/tutorials/packaging-projects/)
[Installing Packages](https://packaging.python.org/en/latest/tutorials/installing-packages/)
[Packaging and distributing projects](https://packaging.python.org/en/latest/guides/distributing-packages-using-setuptools/)
[\_\_main\_\_](https://docs.python.org/3/library/__main__.html)
[Python Modules](https://docs.python.org/3/tutorial/modules.html#tut-modules)
[Python Packages](https://docs.python.org/3/tutorial/modules.html#tut-packages)
[Good explanation of executing .py](https://stackoverflow.com/questions/419163/what-does-if-name-main-do?answertab=scoredesc#tab-top)
[Shebang in linux](https://www.baeldung.com/linux/shebang)
[Using shebang for python scripts](https://linuxhint.com/python-shebang/)
[poetry](https://python-poetry.org/docs/basic-usage/)
[pipenv guide](https://realpython.com/pipenv-guide/)
[pip](https://pip.pypa.io/en/stable/)

## Note

### module vs package vs library

> [Python Modules](https://docs.python.org/3/tutorial/modules.html#tut-modules)
> [Python Packages](https://docs.python.org/3/tutorial/modules.html#tut-packages)

*   Module - file contai. s set of functions and global variables
*   Package - directory that contains \*.py files or it's namespace for set of modules. It also can contains sub pacakages. And it contains \_\_init\_\_.py file
*   Library - collection of packages

### library vs executable \*.py

> here and below will call module, package, library just library for simplicty

There're some different types of python project exists just like in any other languages. Some projects just piece of code that couldn't be executed on its own, usually they called libraries. Some projects are running and making something usefull for users, we will call they executable. The first helps the second to achieve this providing ready-to-use solutions for basic things i.e. working with date and time, working with cryptography, etc.

In python there's not much a difference between executable and library. All of them a set of .py files. And any of these files could be ran like `python file.py`. The only difference that if you're running a library nothing should happened, and if you're running executable program should be started. So seems executable can just have  function call in the end of the file that will be called after calling `python file.py` like this

file.py:

```python
def main():
	print('hw')

main()
```

run it:

```shell
python file.py
```

Seems ok, until this script will be imported as module to another script. What will happen in this cake? Right `main()` will be called. So there's idiomatic why how to design executalbe.

> [Good explanation of executing \*.py](https://stackoverflow.com/questions/419163/what-does-if-name-main-do?answertab=scoredesc#tab-top)

**So how to design executable properly?**

*The first part is [`if __name__ == '__main__'`](https://docs.python.org/3/library/__main__.html#name-main)*

> [\_\_main\_\_](https://docs.python.org/3/library/__main__.html)

When package/module imported - module name is assigned to the \_\_name\_\_ variable, module name typically is a name of .py file without .py extension. But if it's a top level code environment \_\_main\_\_ will be assigned to the \_\_name\_\_ instead a filename. Top-level code is a first user specified Python module that starts running in other words it's entry point to the application.

Usage:

```python

import sys

def main() -> int:
	#do something usefull here
    return 0

if __name__ == '__main__':
    sys.exit(main())  # next section explains the use of sys.exit

```

*The second part is shebang*

> [Shebang in linux](https://www.baeldung.com/linux/shebang)
> [Using shebang for python scripts](https://linuxhint.com/python-shebang/)

Shebang is a string in the first line of script which contains information about program that must execute this script in the format: `#!interpreter [optional-arg]`.

So despite shebang isn't nececcary part, it make posible to run script without specifieng `python` before every run command and also make sure that script will be executed by interpterer that supposed to be used, for instance if script need to be executed with python3 it wouldn't be accedentaly executed with python2.

Example without shebang:
Script hw\.py:

```python
print("hw!")
```

To run:

```python
python hw.py #or python3 hw.py, or python2 hw.ru, or whatever
```

Example with shebang:
Script hw\.py:

```python
#!/usr/bin/env python3
print("hw!")
```

To run:

```python
chmod +x hw.py
./hw.py
```

### Package/distribute/install

> [Packaging Python Projects](https://packaging.python.org/en/latest/tutorials/packaging-projects/)
> [Installing Packages](https://packaging.python.org/en/latest/tutorials/installing-packages/)
> [Packaging and distributing projects](https://packaging.python.org/en/latest/guides/distributing-packages-using-setuptools/)

### Manage dependencies

> [poetry](https://python-poetry.org/docs/basic-usage/)
> [pipenv guide](https://realpython.com/pipenv-guide/)
> [pip](https://pip.pypa.io/en/stable/)

There're many tools which can handle dependency management for python programs like: pip, pipenv, poetry. But core idea the same: you have a file that describes dependencies of project(requirements.txt in case of pip or pipenv, pyproject.toml in case of poetry) and a tool which will execute installation/resolve version conflicts/virtualenv managing.
