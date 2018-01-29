---
layout: drf
title: partial_update
date: 2017-09-18 09:33:01
tags: Django
categories: Python
---

# DRF ModelViewSet 重写部分更新函数 partial_update

```
def partial_update(self, request, pk=None):
    instance = self.get_object()
    serializer = ArticleCreateSerializer(
        instance, 
        data=request.data, 
        partial=True,
    )
    serializer.is_valid(raise_exception=True)
    newer = serializer.save()
    newSD = ArticleSerializer(newer, context={'request': request})
    return Response(newSD.data)
```