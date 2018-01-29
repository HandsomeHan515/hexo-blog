---
title: DRF上下文
date: 2017-10-09 14:05:49
tags: Django
categories: Python
---

# DRF上下文
> 解决某些url中不带地址的问题

```
serializer = AccountSerializer(account, context={'request': request})
serializer.data
# {'id': 6, 'owner': u'denvercoder9', 'created': datetime.datetime(2013, 2, 12, 09, 44, 56, 678870), 'details': 'http://example.com/accounts/6/details'}
```
