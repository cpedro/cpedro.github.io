---
title: "Salt, improving Jinja usage"
layout: default
---

Last Updated: 2021-10-07

### *NOTE*

This is actually a mirror from a blog post done by Thomas Martin I/O.  I have
personally referred to this multiple times while configuring Salt states in
Jinja.  When I recently went back to refer to, I found that the blog no longer
exists and the domain itself has been abandoned.  Luckily, I found a copy of it
on [Wayback Machine](https://web.archive.org/web/20180814194012/http://www.tmartin.io:80/articles/2014/salt-improving-jinja-usage)
so I decided to make my own mirror.  Enjoy.

## Salt and Jinja

[Salt](https://www.saltstack.com/) is a configuration management and remote
execution software. One of its main usage is to define intents about system
configuration, which are described in [states](https://docs.saltproject.io/en/latest/topics/tutorials/starting_states.html)
files. In these states files, the [Jinja](https://jinja.palletsprojects.com/)
rendering language can be used to manipulate data coming from external sources
(like [Pillar](https://docs.saltproject.io/en/latest/topics/tutorials/pillar.html)).
The following assumes you've a basic understanding of how states and pillars
work.

Last week, I pushed this commit on a repository containing a collection of
Salt's states. It substantially simplify the way I access data using Jinja.
Furthermore, it fixes what I consider an important bug : previously, my states
were not working if some pillar data were not defined. This is something I care,
because I want my states to be reusable by other people, and I don't want to
break their Salt setup if they don't have the right Pillar data defined.

So, I made the following changes.

## Using pillar.get

First, I switched to use systematically the [pillar.get](https://docs.saltproject.io/en/latest/ref/modules/all/salt.modules.pillar.html#salt.modules.pillar.get)
function to access Pillar data:

```jinja
{% raw %}{{ salt['pillar.get']('some:nested:dict', 'my_default') }}{% endraw %}
```

This Jinja variable interpolation calls the [get](https://docs.saltproject.io/en/latest/ref/modules/all/salt.modules.pillar.html#salt.modules.pillar.get)
function of the salt's [pillar module](https://docs.saltproject.io/en/latest/ref/modules/all/salt.modules.pillar.html)
in order to:

* Access a nested pillar data (value of the dict key in the nested dictionary
in the some dictionary)
* Provide a defaut value (`my_default`), avoiding to break the state execution
if the pillar data is not defined

## Iterating over Pillar

Next, I improved the way I perform iterations over lists of pillar. I will use
this Pillar tree as an example:

```yaml
users:
    bob:
        uid: 8001
        fullname: "Bob"
        password: '...'
    audrey:
        uid: 8002
        fullname: "Audrey"
        password: '...'
```

Previously, I used to iterate over the username and associated user information
as this:

```jinja
{% raw %}{% if pillar['users'] is defined %}
{% for user, userinfo in pillar['users'].iteritems() %}
[...]
{% endfor %}
{% endif %}{% endraw %}
```

The `{% raw %}{%{% endraw %} if` test was here to avoid `iteritems()` to fail
if `pillar['users']` is not defined.

Instead, it is much better and simpler to use `pillar.get()` and to define an
empty dictionary as default value:

```jinja
{% raw %}{% for user, userinfo in salt['pillar.get']('users', {}).iteritems() %}
[...]
{% endfor %}{% endraw %}
```

In this case, `iteritems()` cannot fails.

## Using .get()

Finally, I need sometimes to test if an item is present in a list stored in a
dictionary coming from Pillar (`root_pathinfo['config_tags']` in the example
below). This list can be undefined when no entries are relevant in a particular
case.

I was used to surround these tests with a `{% raw %}{%{% endraw %} if` to check
if the list is defined:

```jinja
{% raw %}{% if root_pathinfo['config_tags'] is defined %}
{% if 'php5' in root_pathinfo['config_tags'] %}
[...]
{% endif %}
{% endif %}{% endraw %}
```

Instead, I now use the `.get()` Python dictionary method with a empty list as
default value:

```jinja
{% raw %}{% if 'php5' in root_pathinfo.get('config_tags', []) %}
[...]
{% endif %}{% endraw %}
```

Two lines removed.

## Conclusion

In conclusion, one should try to keep Jinja usage the simplest, to allow better
maintainability, readability and debugging.

