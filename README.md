publicsuffixlist
===

[Public Suffix List](https://publicsuffix.org/) parser implementation for Python 2.5+/3.x.

- Compliant with [TEST DATA](http://mxr.mozilla.org/mozilla-central/source/netwerk/test/unit/data/test_psl.txt?raw=1)
- Support IDN (unicode or punycoded).
- Support Python2.5+ and Python 3.x
- Shipped with built-in PSL and the updater script.
- Written in Pure Python. No library dependencies.

[![Build Status](https://travis-ci.org/ko-zu/psl.svg?branch=master)](https://travis-ci.org/ko-zu/psl)

Install
===
`publicsuffixlist` can be installed via `pip` or `pip3`.
```
$ sudo pip install publicsuffixlist
```

If you are in a bit old distributions (RHEL/CentOS6.x), you may need to update `pip` itself before install.
```
$ sudo pip install -U pip
```

Usage
===

```python
from publicsuffixlist import PublicSuffixList

psl = PublicSuffixList()
# uses built-in PSL file

psl.publicsuffix("www.example.com")   # "com"
# longest public suffix part

psl.privatesuffix("www.example.com")  # "example.com"
# shortest domain assigned for a registrant

psl.privatesuffix("com") # None
# None if no private (non-public) part found


psl.publicsuffix("www.example.unknownnewtld") # "unkownnewtld"
# new TLDs are valid public suffix by default

psl.publicsuffix(u"www.example.香港")   # u"香港"
# accept unicode

psl.publicsuffix("www.example.xn--j6w193g") # "xn--j6w193g"
# accept punycoded IDNs by default
```

Latest PSL can be passed as a file like line-iterable object.
```python
with open("latest_psl.dat", "rb") as f:
    psl = PublicSuffixList(f)
```

Works with both Python 2.x and 3.x.
```
$ python -m publicsuffixlist.test
$ python3 -m publicsuffixlist.test
```

Drop-in compatibility code to replace [publicsuffix](https://pypi.python.org/pypi/publicsuffix/)
```python
# from publicsuffix import PublicSuffixList
from publicsuffixlist.compat import PublicSuffixList

psl = PublicSuffixList()
psl.suffix("www.example.com")   # return "example.com"
psl.suffix("com")               # return "" rather than None
```

Some convenient methods available.
```python
psl.is_private("example.com")  # True
psl.privateparts("aaa.www.example.com") # ("aaa", "www", "example.com")
psl.subdomain("aaa.www.example.com", depth=1) # "www.example.com"
```


Limitation
===
`publicsuffixlist` do NOT provide domain name validation.
In DNS protocol, most of 8-bit characters are acceptable label of domain name. ICANN compliant registries do not accept domain names that have `_` (underscore) but hostname may have. DMARC records, for example.

Users need to confirm the input is valid based on the users' context.

Partially encoded (Unicode-mixed) Punycode is not supported because of very slow Punycode en/decoding and unpredictable encoding of results.
If you are not sure the input is valid Punycode or not, you should do `unknowndomain.encode("idna")` which is idempotence.

License
===

- This module is licensed under Mozilla Public License 2.0.
- Public Suffix List maintained by Mozilla Foundation is licensed under Mozilla Public License 2.0.
- PSL testcase dataset is public domain (CC0).


Source / Link
===

- Git repository on GitHub (https://github.com/ko-zu/psl)
- PyPI (https://pypi.python.org/pypi?name=publicsuffixlist&:action=display)
