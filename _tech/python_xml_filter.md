---
title: "Filter XML Data to remove elements using Python"
layout: default
---

Last Updated: 2019-07-08

I recently had to setup a script that would remove certain elements from XML
data.  I found code examples online on how to do this in Python using
ElementTree.  However, nothing was successful in removing sub-elements as well.
The issue is, the `remove()` function requires you to pass the parent element,
but there's no easy way to find the parent of a node when looping through XML
data.

To work around this, I first specified a dictionary, with the element as the key
and the [path](https://docs.python.org/3.4/library/xml.etree.elementtree.html#elementtree-xpath) 
as the value.  Then, find the 'parent' by doing a `findall()` on value and then
call `remove()` on the parent.

In the snippet below, `<revision>` is on the root (`.`), and 
`<last_rule_upd_time>` is under `<installedpackages> ... <snortglobal>`.

### Code
```python
import xml.etree.ElementTree as ET

def filterconfig(cfg):
    """Filter out elements from config.
    """
    # Update this dictionary to filter more.
    # Format is '<element>': '<path>'
    # See below URL for path format:
    # https://docs.python.org/3.4/library/xml.etree.elementtree.html#elementtree-xpath
    toRemove = {
        'revision': '.',
        'last_rule_upd_time': './installedpackages/snortglobal' 
    }
    
    tree = ET.parse(cfg)
    root = tree.getroot()

    for element in toRemove:
        for parent in root.findall(toRemove[element]):
            for child in parent.findall(element):
                parent.remove(child)

    tree.write(cfg)
```

### Caveats

Because I'm using a dictionary, which doesn't support duplicate keys, if you
have multiple elements with the same name, but under different paths, only the
first instance as defined in the dictionary will be removed.  If they're under
the same path, they will all be removed, since `findall()` is being used.  If
you need to remove elements named the same under different paths, a different
method will need to be used.

### Background

I used this method for backing up [pfSense](https://www.pfsense.org/) firewall
configus. The issue I had was I was using [RANCID](https://www.shrubbery.net/rancid/) 
to keep a track of changes in configs.  However, we were using [Snort](https://www.snort.org/)
on the firewalls, which was auto-updating rulesets.  Each time a rule set was
updated, we would get something similar to below in from RANCID:

```
diff -u -4 -r1.52
@@ -3611,11 +3611,11 @@
      <hideversion></hideversion>
      <dnssecstripped></dnssecstripped>
    </unbound>
    <revision>
-     <time>1562267070</time>
-     <description><![CDATA[admin@10.201.0.170 (Local Database): Configuration saved on completion of the pfSense setup wizard.]]></description>
-     <username>admin@10.201.0.170 (Local Database)</username>
+     <time>1562282138</time>
+     <description><![CDATA[(system): Snort pkg: updated status for updated rules package(s) check.]]></description>
+     <username>(system)</username>
    </revision>
    <vlans>
      <vlan>
        <if>vmx0</if>
@@ -3980,10 +3980,10 @@
        <rm_blocked>1h_b</rm_blocked>
        <autorulesupdate7>6h_up</autorulesupdate7>
        <rule_update_starttime>00:05</rule_update_starttime>
        <forcekeepsettings>on</forcekeepsettings>
        <last_rule_upd_status>success</last_rule_upd_status>
-       <last_rule_upd_time>1562252703</last_rule_upd_time>
+       <last_rule_upd_time>1562282138</last_rule_upd_time>
        <rule>
          <interface>wan</interface>
          <enable>on</enable>
          <uuid>9156</uuid>
```

Obviously, the `<revision>`, `<last_rule_upd_time>` elements in the XML data
were a bit irrelevant, and not really needed to backup configs.  So I decided
to filter out these elements, but the method above can be used for any elements
in XML data.
