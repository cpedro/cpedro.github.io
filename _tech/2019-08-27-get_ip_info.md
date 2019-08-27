---
title: "Geolocation Bash Function"
layout: default
---

Last Updated: 2019-08-27

This handy little Bash function comes by way of
[this Twitter thread](https://twitter.com/curioman2/status/1128320604833222656),
using the IP API from [ipinfo.io](https://ipinfo.io/). Although I had to do some
tweaking to get it to work on MacOS, due to it using `dig`.  The issue I had was
that when I just gave the function an IP, `dig` wouldn't return anything, and
the function itself would give me information for my public IP.  To get around
this, I had to check the input first to see if it was a valid IP.  For this I'm
using another function `valid_ip()` with a regex
[found here](https://helloacm.com/how-to-valid-ipv6-addresses-using-bash-and-regex/)
to check first for a valid IPv4 or IPv6 IP.

Here is the resulting code, which I've put in my `.bash_profile`.

```sh
function valid_ip() {
  [[ $1 =~ ^([0-9]{1,2}|1[0-9][0-9]|2[0-4][0-9]|25[0-5])\.([0-9]{1,2}|1[0-9][0-9]|2[0-4][0-9]|25[0-5])\.([0-9]{1,2}|1[0-9][0-9]|2[0-4][0-9]|25[0-5])\.([0-9]{1,2}|1[0-9][0-9]|2[0-4][0-9]|25[0-5])$ || $1 =~ ^(([0-9a-fA-F]{1,4}:){7,7}[0-9a-fA-F]{1,4}|([0-9a-fA-F]{1,4}:){1,7}:|([0-9a-fA-F]{1,4}:){1,6}:[0-9a-fA-F]{1,4}|([0-9a-fA-F]{1,4}:){1,5}(:[0-9a-fA-F]{1,4}){1,2}|([0-9a-fA-F]{1,4}:){1,4}(:[0-9a-fA-F]{1,4}){1,3}|([0-9a-fA-F]{1,4}:){1,3}(:[0-9a-fA-F]{1,4}){1,4}|([0-9a-fA-F]{1,4}:){1,2}(:[0-9a-fA-F]{1,4}){1,5}|[0-9a-fA-F]{1,4}:((:[0-9a-fA-F]{1,4}){1,6})|:((:[0-9a-fA-F]{1,4}){1,7}|:)|fe80:(:[0-9a-fA-F]{0,4}){0,4}%[0-9a-zA-Z]{1,}|::(ffff(:0{1,4}){0,1}:){0,1}((25[0-5]|(2[0-4]|1{0,1}[0-9]){0,1}[0-9])\.){3,3}(25[0-5]|(2[0-4]|1{0,1}[0-9]){0,1}[0-9])|([0-9a-fA-F]{1,4}:){1,4}:((25[0-5]|(2[0-4]|1{0,1}[0-9]){0,1}[0-9])\.){3,3}(25[0-5]|(2[0-4]|1{0,1}[0-9]){0,1}[0-9]))$ ]]
  return $?
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

