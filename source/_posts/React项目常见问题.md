---
title: React项目常见问题
date: 2017-09-06 15:23:15
tags: React
categories: JavaScript
---
# 项目中遇到的一些问题
## 1. height: 10; 或者: height: '10px'; 而不是 height: '10'
## 2. “ ； ”使用分号时，只在css中书写时添加引号，jsx中不建议添加分号；
## 3. map数组时return返回jsx代码时，最外层需要添加key={index}；
```
data.map((item, index) => {
    return (
        <div key={index}>
            {content}
        </div>
    )
})
```
## 4. for => htmlFor; class => className；
```
<label htmlFor='example'></label>
<input id='example' />
```
## 5. 函数之间用空行空开；不同共能的代码尽量用空行空出来

## 6. defaultProps的书写
```
class DrawingBoard extends Component {
    ....
}

DrawingBoard.propTypes = {
    height: React.PropTypes.number,
    width: React.PropTypes.number,
}

DrawingBoard.defaultPorps = {
    height: 10,
    width: 20,s
}

export default DrawingBoard
```

## 7.函数的书写方式
### 方式一：函数bind法
```
class AttendClass extends Component {
    constructor(props) {
        super(props)
        
        this.select = this.select.bind(this)
        this.delete = this.delete.bind(this)
    }
    
    select() {
        ...
    }
    
    delete() {
        ...
    }
    
    render() {
        ...
    }
}
```
### 方式二：箭头函数方式
```
class AttendClass extends Component {
    select = () => {
        ...
    }
    
    delete = () => {
        ...
    }
    
    render() {
        
    }
}
```

##  8.单词及括号等之间用空格隔开
```
if (this.props.display === true) {
    ...
} else {
    ...
}

const { tasks, subTasks, units } = this.props
```
### 9. == --------> === 

### 10.!!!warning Missing radix parameter  radix
    parsetInt('string', radix)
    
    radix是一个数字，表示几进制数字，为必填项。

### 11.The Body of a for-in should be wrapped in an if statement to filter unwanted properties from the prototype grard-for-in
```
for (let i in datas) {
    if (datas.hasOwnProperty(i) {
        console.log(i)
    }
}
```
### 12. Do not mutate state directly. Use setState react/no-direct-mutation-state
    this.state.example 不能直接操作，要修改state，只能通过setState()来修改。