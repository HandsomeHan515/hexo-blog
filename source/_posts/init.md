---
layout: django
title: __init__
date: 2017-09-04 22:43:06
tags: Django
categories: Python
---
# Serializer的__init__自定义fields
```
class EnterpriseContactSerializer(serializers.ModelSerializer):

    def __init__(self, *args, **kwargs):
        super(EnterpriseContactSerializer, self).__init__(*args, **kwargs)

        request = kwargs['context']['request']
        if request.method == 'GET':
            self.fields['person'] = UserEnterSerializer()

    class Meta:
        model = EnterpriseContact
        exclude = ('owner',)

    def create(self, validated_data):
        validated_data['owner'] = self.context['request'].user
        return EnterpriseContact.objects.create(**validated_data)
```
