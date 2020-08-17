---
title: Formatting problem(VUE) after VScode updating
date: 2020-08-17 19:38:40
tags: bugs/problems
---

i’v been disgusted by the format of vue code on VScode these days after its update. Checked all the extensions i've made before, still couldn't solve the problem.

So i had to uninstall formatter-related extensions on my VScode this morning, and checked one by one to see if there's any conflict among these extensions.

By the way, i'd like to tell about some disgusting errors first......🤯🤯🤯

i can't make the code (expecially the js and vue code in my vue project) formatted according to eslint automatically after "ctr+s", some snippets still show unexpected format which the parase can't analyze...

Such as :

👉this ![](https://tva1.sinaimg.cn/large/007S8ZIlly1ghu0rcvw2vj309603ymx6.jpg) 

👉and this <img src="https://tva1.sinaimg.cn/large/007S8ZIlly1ghu0tdsn9cj307j02c746.jpg"  />

👉and this![](https://tva1.sinaimg.cn/large/007S8ZIlly1ghu0vbxmj1j304i0433yf.jpg)

.......

🤢🤢🤢🤢

feel bad

.....

feel disgusting

.....

Thanks to "Enthusiastic Netizens" and "MY Persistence"...

i find answer here below🙏🙏

Step ①

of course you should add some extensions:

Vetur  

Eslint

Prettier

↓

Step②

you should add some codes in your settings.json, too

```json
"eslint.validate": [
    "javascript",
    "javascriptreact",
    {
      "language": "vue",
      "autoFix": true
    }
  ],
  "eslint.autoFixOnSave": true,
  "editor.codeActionsOnSave": {
    "source.fixAll.eslint": true
  },
  "editor.formatOnSave": true,
  "editor.formatOnPaste": true,
  "editor.formatOnType": true,
  "vetur.format.defaultFormatter.js": "vscode-typescript",
  "javascript.format.insertSpaceBeforeFunctionParenthesis": true,
  "vetur.format.defaultFormatter.html": "prettier",
  "editor.tabSize": 2,
```

Edited by https://segmentfault.com/a/1190000014785115

↓

to solve "newline at the end " warning, you can add this line in your .eslintrc.js

```js
'eol-last': 'off'
```

↓

to solve the indent and single quote regulation of vue, you can build .prettierrc in your projec, add codes

```json
{
    "tabWidth": 2,
    "semi": false,
    "singleQuote": true,
    "bracketSpacing": true
}
```

...

Ok ~ down

💪

