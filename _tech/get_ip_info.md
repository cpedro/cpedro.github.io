---
title: "Geolocation Bash Function"
layout: default
---

Last Updated: 2019-05-15

This handy little Bash function comes by way of
[this Twitter thread](https://twitter.com/curioman2/status/1128320604833222656),
using the IP API from [ipinfo.io](https://ipinfo.io/). Although I had to do some
tweaking to get it to work on MacOS, due to it using `dig`.  The issue I had was
that when I just gave the function an IP, `dig` wouldn't return anything, and
the function itself would give me information for my public IP.  To get around
this, I had to check the input first to see if it was a valid IP.  For this I'm
using another function `valid_ip()` from
[a blog post](https://www.linuxjournal.com/content/validating-ip-address-bash-script)
from [Mitch Frazier](https://www.linuxjournal.com/users/mitch-frazier).

Here is the resulting code, which I've put in my `.bash_profile`.

```sh
function valid_ip() {
  local ip=$1
  local stat=1

  if [[ $ip =~ ^[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}$ ]]; then
    OIFS=$IFS
    IFS='.'
    ip=($ip)
    IFS=$OIFS
    [[ ${ip[0]} -le 255 && ${ip[1]} -le 255 \
      && ${ip[2]} -le 255 && ${ip[3]} -le 255 ]]
    stat=$?
  fi
  return $stat
}

function gii() {
  ip=$(valid_ip $1 && echo $1 || dig +short $1 | egrep "^[0-9]+" | head -1)
  curl -s http://ipinfo.io/${ip} | jq;
}
```

The script also uses the `jq` command that parses JSON.  To get this, you can
install it by running:

### MacOS:
(Requires [Homebrew](https://brew.sh/))
```sh
$ brew install jq
```

### Debian / Ubuntu
```sh
$ apt install jq
```

### Fedora / CentOS / RedHat
```sh
$ yum install jq
```

### Examples:

```sh
$ gii www.google.com
{
  "ip": "172.217.9.228",
  "hostname": "lga34s11-in-f4.1e100.net",
  "city": "",
  "region": "",
  "country": "US",
  "loc": "37.7510,-97.8220",
  "org": "AS15169 Google LLC"
}
$ gii 4.2.2.2
{
  "ip": "4.2.2.2",
  "hostname": "b.resolvers.Level3.net",
  "city": "",
  "region": "",
  "country": "US",
  "loc": "37.7510,-97.8220",
  "org": "AS3356 Level 3 Parent, LLC"
}
```

