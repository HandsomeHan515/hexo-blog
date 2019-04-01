---
layout: javascript
title: vDom解析
date: 2019-03-04 10:20:19
tags: JavaScript
categories: JavaScript
---

## h (用 JS 对象模拟 DOM 树)

```js
/**
 * h.js
 * class Element
 * @param {String} tagName
 * @param {Object} props
 * @param {Array<Element>|String} children
 */

class Element {
    constructor(tagName, props, children) {
        if (_.isArray(props)) {
            children = props
            props = {}
        }

        this.tagName = tagName
        this.props = props || {}
        this.children = children || []
        this.key = props ? props.key : void 0
    }

    render() {
        let el = document.createElement(this.tagName)
        let props = this.props

        for (let key in props) {
            _.setAttr(el, key, props[key])
        }

        _.each(this.children, function (child) {
            const childEl = child instanceof Element
                ? child.render()
                : document.createTextNode(child)
            el.appendChild(childEl)
        })
        return el
    }
}

function h(tagName, props, children) {
    return new Element(tagName, props, children)
}
/* End */

const ul = h('ul', { id: 'list' }, [
    h('li', { class: 'item' }, 'Item 1'),
    h('li', { class: 'item' }, 'Item 2'),
    h('li', { class: 'item' }, 'Item 3')
])

const ulRoot = ul.render()
document.body.appendChild(ulRoot)
```

## diff (比较两棵虚拟 DOM 树的差异)

### 同级元素比较时的四种情况

- 整个元素都不一样，即元素被 replace
- 元素顺序被替换，即元素需要 reorder
- 元素的 props 不一样
- 元素的 text 文本不一样
