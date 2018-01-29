---
layout: django
title: Channels
date: 2017-09-06 17:29:26
tags: Django
categorys: Python
---
# Django Channels处理实时通讯
## WS <=> WS
前端连接到ws的都放到online_dict中，即在线用户，channels中Group，ws地址只有一个， 例：127.0.0.1:12345/message/<param>/

```
from channels import Group, Channel
from channels.sessions import channel_session
from chats.models import Record

import json
import time
online_dict = {}


@channel_session
def ws_connect(message, username):
    message.channel_session['username'] = username
    Group(username).add(message.reply_channel)
    Group('all').add(message.reply_channel)
    online_dict[username] = int(round(time.time() * 1000))
    return message.reply_channel.send({"accept": True})


@channel_session
def ws_receive(message):
    '''
    {
      'to': {
        'username': 18510843396,
      },
      message: {
        'content': 'hah',
      }
    }
    '''
    from_username = message.channel_session['username']
    msg_dict = json.loads(message.content['text'])
    to_username = msg_dict['to']['username']

    send_content = {}
    send_content['from'] = {}
    send_content['from']['username'] = from_username
    send_content['message'] = {}
    send_content['message']['content'] = msg_content

    if to_username in online_dict:
        Group(to_username).send({
            'text': json.dumps(send_content)
        })

@channel_session
def ws_disconnect(message):
    username = message.channel_session['username']
    Group(username).discard(message.reply_channel)
    Group('all').discard(message.reply_channel)
    online_dict.pop(username, None)
```


## HTTP => WS
在创建http请求的同时执行下列函数即可

```
def notify_all(notify):
    Group('all').send({'text': json.dumps(notify)})


def notify_user(from_user, to_user, msg):
    if user in online_dict:
        Group(user).send({'text': json.dumps(msg)})
```