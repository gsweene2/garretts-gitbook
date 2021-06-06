# Python

<!-- toc -->

## Where are python installations on my mac?
```
# ls /usr/bin/python*

/usr/bin/python                 /usr/bin/python2                /usr/bin/python2.7-config       /usr/bin/pythonw
/usr/bin/python-config          /usr/bin/python2.7              /usr/bin/python3                /usr/bin/pythonw2.7
```

## Which python am I using

```
which python
```

## pyenv basics, changing versions

List Current Version
```
$ pyenv versions
  system
  3.6.11
* 3.7.8 (set by /Users/garrettsweeney/.pyenv/version)
```

Install new versions
```
$ pyenv install 3.8.0
```

Switch Version
```
$ pyenv global 3.8.0
$ pyenv versions
  system
  3.6.11
  3.7.8
* 3.8.0 (set by /Users/garrettsweeney/.pyenv/version)

$ python -V
Python 3.8.0
```

## Basic project structure

Refer to Real [Python's Python Application Layouts](https://realpython.com/python-application-layouts/)
