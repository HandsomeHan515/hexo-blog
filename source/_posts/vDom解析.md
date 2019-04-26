---
layout: javascript
title: vDom解析
date: 2019-03-04 10:20:19
tags: JavaScript
categories: JavaScript
---

## 公共方法

```js
/**
 * utils.js
 * @param {DomObject} node
 * @param {string} key
 * @param {string} value
 */

function setAttr(node, key, value) {
    switch (key) {
        case 'value':
            if (node.tagName.toLowerCase() === 'input' || node.tagName.toLowerCase() === 'textarea') {
                node.value = value
            } else {
                node.setAttribute(key, value)
            }
            break
        case 'style':
            node.style.cssText = value
            break
        default:
            node.setAttribute(key, value)
    }
}

function isString(obj) {
    return typeof obj === 'string'
}
```

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
- 元素顺序被替换，即元素需要 reorder <需list-diff支持>
- 元素的 props 不一样
- 元素的 text 文本不一样

```js
function diff(oldTree, newTree) {
    let patches = {}
    let index = 0
    walk(oldTree, newTree, index, patches)
    return patches
}

function walk(oldNode, newNode, index, patches) {
    let current = []
    if (!newNode) {
        current.push({ type: 'REMOVE', index })
    } else if (_.isString(oldNode) && _.isString(newNode)) {
        if (oldNode !== newNode) {
            current.push({ type: 'TEXT', text: newNode })
        }
    } else if (oldNode.type === newNode.type) {
        // 属性更改
        let attr = diffAttr(oldNode.props, newNode.props)
        if (Object.keys(attr).length > 0) {
            current.push({ type: 'ATTR', attr })
        }
        // 子节点
        diffChildren(oldNode.children, newNode.children, patches)
    } else {
        // 节点替换
        current.push({ type: 'REPLACE', newNode })
    }

    if (current.length) {
        patches[index] = current
    }
}



function diffAttr(oldAttrs, newAttrs) {
    let patch = {}
    for (let key in oldAttrs) {
        if (oldAttrs[key] !== newAttrs[key]) {
            patch[key] = newAttrs[key]
        }
    }
    for (let key in newAttrs) {
        if (!oldAttrs.hasOwnProperty(key)) {
            patch[key] = newAttrs[key]
        }
    }
    return patch
}

let num = 0
function diffChildren(oldChildren, newChildren, patches) {
    oldChildren.forEach((child, index) => {
        walk(child, newChildren[index], ++num, patches)
    })
}
```

## patch 补丁更新

```js
let allPatches
let index = 0
function patch(node, patches) {
    allPatches = patches
    run(node)
}

function run(node) {
    let current = allPatches[index++]
    let childNodes = node.childNodes

    childNodes.forEach(child => run(child))

    if (current) {
        doPatch(node, current)
    }
}

function doPatch(node, patches) {
    patches.forEach(patch => {
        switch (patch.type) {
            case 'TEXT':
                node.textContent = patch.text
                break
            case 'REMOVE':
                node.parentNode.removeChild(node)
                break
            case 'ATTR':
                for (let key in patch.attr) {
                    const value = patch.attr[key]
                    if (value) {
                        _.setAttr(node, key, value)
                    } else {
                        node.removeAttribute(key)
                    }
                }
                break
            case 'REPLACE':
                let newNode = patch.newNode
                newNode = (newNode instanceof Element) ? render(newNode) : document.createTextNode(newNode)
                node.parentNode.replaceChild(newNode, node)
                break
            default:
                break
        }
    })
}

let virtualDom2 = h('ul', { class: 'list-group' }, [
    h('li', { class: 'item active' }, ['七里香']),
    h('li', { class: 'item' }, ['一千年以后']),
    h('li', { class: 'item' }, ['需要人陪'])
]);

// diff一下两个不同的虚拟DOM

let patches = diff(ulRoot, virtualDom2);

console.log(patches);

// 将变化打补丁，更新到el

setTimeout(() => {
    patch(el, patches);
}, 2000)
```

## list-diff

。。。待完善