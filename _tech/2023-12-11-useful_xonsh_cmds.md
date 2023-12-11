---
title: "Useful xonsh Commands"
layout: default
---

Last Updated: 2023-12-11

I've recently started using [xonsh](https://xon.sh/) as a Linux shell.  It's
extremely powerful Python-based shell.  However, as the days go one, I'm trying
to figure out how to do the same things in xonsh as I have in Bash, but also
trying to figure out how to use things like Python list comprehension in xonsh.

Below is a list of useful things I've figured out how to do along the way.

## List Comprehension

[Python List Comprehension](https://www.w3schools.com/python/python_lists_comprehension.asp)
is a powerful tool, and figuring out how to do it in xonsh is a bit tricky, but
below is the easiest way I've figured out how to do it.

Example:
```python
> for i in ['hello', 'world']:
>     $[echo @(i)]
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

The only downside with this after the commands are written, an array with `None`
is printed at the end of execution, this is because of how uncaptured
subprocesses are handled in xonsh.  To get around this, you can just assign the
list comprehension to a variable.
```python
> x = [$[echo @(i)] for i in ['hello', 'world']]
hello
world
```

