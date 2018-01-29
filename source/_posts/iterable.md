---
layout: manyrelatedmanager
title: iterable
date: 2018-01-15 12:15:16
tags: Django
categories: Python
---

# Django下 TypeError ManyRelatedManager object is not iterable 的解决方法

```
# 使用interest.category.all() 直接使用interest.category则会导致报错
@list_route()
    def recommendations(self, request):
        queryset = self.get_queryset()

        if request.auth:
            interests = request.user.interests
            interest = interests.last() if interests.exists() else None
            categories = [item for item in interest.category.all()
                          ] if interest else None

            queryset = queryset.filter(
                category__in=categories) if categories else queryset

        queryset = queryset.order_by('?')[:4]
        return PageResponse(self, queryset, FlowerSerializer)
```

