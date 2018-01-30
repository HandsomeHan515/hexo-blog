---
title: react中事件绑定的三种方式
date: 2018-01-30 10:45:21
tags: React
categories: JavaScript
---

+ 在执行事件上bind

```bind
import React, { Component } from 'react';

class Progress extends Component {
  handleClick() {
    console.log('this: %o', this.handleClick)
  }

  render() {
    return (
      <div className="center">
        <button onClick={this.handleClick.bind(this)}>按钮</button>
      </div>
    );
  }
}

export default Progress;
```

+ 在构造器内声明

```在构造器内声明
import React, { Component } from 'react';

class Progress extends Component {
  constructor(props) {
    super(props);
    this.state = {}

    this.handleClick = this.handleClick.bind(this)
  }

  handleClick() {
    console.log('this: %o', this.handleClick)
  }

  render() {
    return (
      <div className="center">
        <button onClick={this.handleClick}>按钮</button>
      </div>
    );
  }
}

export default Progress;
```

+ 使用箭头函数

```使用箭头函数
import React, { Component } from 'react';

class Progress extends Component {
  handleClick = () => {
    console.log('this: %o', this.handleClick)
  }

  render() {
    return (
      <div className="center">
        <Button onClick={this.handleClick}>按钮</Button>
      </div>
    );
  }
}

export default Progress;
```
