---
title: DRF自定义Pagination
date: 2017-09-15 11:28:35
tags: Django
categories: Python
---
# DRF自定义Pagination

```
def PageResponse(self, data, serializer_class):
    page = self.paginate_queryset(data)
    if page is not None:
        serialize = serializer_class(
            page,
            many=True,
            context={'request': self.request},
        )
        return self.get_paginated_response(serialize.data)

    serializer = serializer_class(
        charges,
        many=True,
        context={'request': self.request},
    )
    return Response(serializer.data)
```