# Vim

<!-- toc -->

## Vimrc configuration

```
set number              " show line numbers
set wrap                " wrap lines
set encoding=utf-8      " set encoding to UTF-8 (default was "latin1")

"""" Tab settings

set tabstop=4           " width that a <TAB> character displays as
set expandtab           " convert <TAB> key-presses to spaces
set shiftwidth=4        " number of spaces to use for each step of (auto)indent
set softtabstop=4       " backspace after pressing <TAB> will remove up to this many spaces

set autoindent          " copy indent from current line when starting a new line
set smartindent         " even better autoindent (e.g. add indent after '{')
```

## Basics

### Open file

```
vim <file>
```

### Save file
```
# Write + quit
:wq
```

### Navigation

#### Top of File

```
gg
```

#### Bottom of File

```
GG
```

#### Left, Right, Up, Down (hjkl)
```
# left
h
# down
j
# up
k
# right
l
```
