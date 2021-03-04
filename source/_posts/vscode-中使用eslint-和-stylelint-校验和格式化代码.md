---
title: vscode 中使用eslint 和 stylelint 校验和格式化代码
date: 2021-03-04 10:56:27
tags: vscode
categories: JavaScript
---

## vscode 插件安装
```json
{
  "recommendations": [// 安装插件
    "octref.vetur", // Vetur
    "dbaeumer.vscode-eslint", // Eslint
    "stylelint.vscode-stylelint", // Stylelint
    "editorconfig.editorconfig" // editorconfig 只控制编辑器的展示效果
  ]
}
```

## vscode 配置

```json
{
    "editor.formatOnSave": false, // 不启用 vscode 的格式化
    "editor.codeActionsOnSave": { // 文件保存启用 eslint 自动修复同时格式化
        "source.fixAll.eslint": true,
        "source.fixAll.stylelint": true
    },
    "eslint.validate": [
        "javascript",
        "javascriptreact",
        "vue",
    ],
    // 使用 eslint 进行 .js .vue(template/script) 的代码格式化，关闭 vetur 的 formatter
    "vetur.format.enable": false,
    "vetur.validation.template": false,
    "vetur.validation.script": false,
    "vetur.validation.style": false,
}
```


## 使用 vue-cli 搭建 vue 项目
1. Pick a linter / formatter config
2. select "ESLint + Standard config"

### eslint 相关的包
用来格式化
```json
"devDependencies": {
    "@vue/cli-plugin-eslint": "~4.5.0",
    "@vue/eslint-config-standard": "^5.1.2",
    "babel-eslint": "^10.1.0",
    "eslint": "^6.7.2",
    "eslint-plugin-import": "^2.20.2",
    "eslint-plugin-node": "^11.1.0",
    "eslint-plugin-promise": "^4.2.1",
    "eslint-plugin-standard": "^4.0.0",
    "eslint-plugin-vue": "^6.2.2",
}
```

### stylelint 相关的包
```json
"devDependencies": {
    "stylelint": "^13.11.0",
    "stylelint-config-recess-order": "^2.3.0",
    "stylelint-config-standard": "^20.0.0",
    "stylelint-scss": "^3.19.0",
  }
```

### vscode editorconfig 配置

```
// .editorconfig
[*.{js,jsx,ts,tsx,vue}]
root = true
indent_style = space
indent_size = 4
trim_trailing_whitespace = true
insert_final_newline = true
```

### vscode eslint 配置

```javascript
// .eslintrc.js
module.exports = {
    root: true,
    env: {
        node: true
    },
    extends: [
        // 'plugin:vue/essential', 默认只校验 .vue 的 script 代码
        'plugin:vue/recommended', // vue template + script formatter
        '@vue/standard'
    ],
    parserOptions: {
        parser: 'babel-eslint'
    },
    rules: {
        'no-console': process.env.NODE_ENV === 'production' ? 'warn' : 'off',
        'no-debugger': process.env.NODE_ENV === 'production' ? 'warn' : 'off',
        'space-before-function-paren': ['error', 'always'], // 括号之前有空格
        quotes: ['error', 'single'], // 使用单引号
        indent: ['error', 4], // js tab size (包含 .vue 的 script)
        'vue/html-indent': ['error', 4], // template 格式化 tab size
        'vue/script-indent': ['error', 4] // script 格式化 tab size
    }
}
```

### vscode stylelint 配置

```javascript
// .stylelintrc.js 
module.exports = {
    extends: [
        'stylelint-config-standard',
        'stylelint-config-recess-order'
    ],
    syntax: 'scss',
    fix: true,
    rules: {
        'at-rule-no-unknown': null,
        indentation: 4, // 格式化 .vue style 代码
    }
}
```

