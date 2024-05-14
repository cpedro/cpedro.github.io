---
title: "Useful xonsh Commands"
layout: default
---

Last Updated: 2024-05-04

I've recently started using [xonsh](https://xon.sh/) as a Linux shell.  It's
extremely powerful Python-based shell.  However, as the days go one, I'm trying
to figure out how to do the same things in xonsh as I have in Bash, but also
trying to figure out how to use things like Python list comprehension in xonsh.

Below is a list of useful things I've figured out how to do along the way.

## Loop Through Directory

A common thing that I tend to do in Bash is loop through directories or files in
the current directory to perform some tasks on them.  For example (in Bash):
```bash
for i in */
do
  cd $i
  git pull origin main
  cd ../
done
```

The same can be done from xonsh by using the path string, `p` and globbing, `g`:
```python
for i in pg`*`:
    cd @(i)
    git pull origin main
    cd ../
```

Or if you need to ensure that `i` is a directory, you can add additional logic:
```python
for i in pg`*`:
    if i.is_dir():
        cd @(i)
        git pull origin main
        cd ../
```

## Loop through command output

Another common task is to run a command and loop through the output of it to do
something else.  For example (I know this can be done from `find`), if I want
to find all `requirements.txt` files in the current directory and run `pip3`
to install them, you can use captured subproccess with `$()` and `split()`:
```python
for i in $(find . -name requirements.txt).split():
    pip3 install -r @(i)
```

## List Comprehension

[Python List Comprehension](https://www.w3schools.com/python/python_lists_comprehension.asp)
is a powerful tool, and figuring out how to do it in xonsh is a bit tricky, but
below is the easiest way I've figured out how to do it.

Example:
```python
> for i in ['hello', 'world']:
>     echo @(i)
>
hello
world
```

Can be written as:
```python
> [$[echo @(i)] for i in ['hello', 'world']]
hello
world
[None, None]
```

The only downside with this is that after the commands are written, an array
with `None` is printed. This is because of how uncaptured subprocesses are
handled in xonsh.  To get around this, you can just assign the list
comprehension to a variable.
```python
> x = [$[echo @(i)] for i in ['hello', 'world']]
hello
world
```

If you want to loop through the output of a command though, use the following:
```python
> [$[pip3 install -r @(i)] for i in $(find . -name requirements.txt).split()]
```

