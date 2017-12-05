## 使用eslint做代码规范检查

#### 规范检查器：eslint

eslint的配置十分灵活，也较为复杂，选用参数如下：

```json
//.eslintrc.json
{
  "env": {
    "browser": true
  },
  "parser": "babel-eslint",
  "parserOptions": {
    "sourceType": "module"
  },
  "extends": "standard",
  "rules": {}
}

```
其中规则列表选用standard的配置

#### 构建时检查

为了能够在构建的时候随时检查代码规范，解决方案为在webpack的配置中添加eslint-loader

```javascript
// build/webapck.jsconfig.js
module.exports = {
    module: {
        rules: [{
        test: /\.(js|vue)$/,
        loader: 'eslint-loader',
        enforce: 'pre',
        include: [resolve('src'), resolve('test')],
        options: {
          fix: falese, //是否自动修复，默认false
          parser: 'babel-eslint'// other config options to be passed through to standard
        }
      }]
    }
}
```

我们为.js和.vue文件添加了一个eslint-loader，这样在构建的时候就会检查代码规范。
eslint的配置默认会去读取.eslintrc.json


#### 集成编辑器

要在ide做实时错误提示，要根据ide安装插件，插件配置默认也会读取.eslintrc.json
编辑器插件列表http://eslint.cn/docs/user-guide/integrations

#### git提交前检查

为了保证代码的干净，提交前的规范检查是特别重要的。比如说我们为了修复一个很简单的bug，所以快速的写了一段代码，没有经过再次构建就直接提交了，那这段代码就逃过了规范检查。所以为了全面的保证代码的规范性，在git commit这个节点增加一个钩子，再次对代码规范性进行检查。

解决方案：使用husky，并配置package.json，如下：

```json
// package.json
{
    "scripts": {
        "precommit": "fet lint"
    }
}
```

每次对整个项目进行校验还是很影响效率的，所以我们更希望的是，只对某一部分进行检查就可以了，所以再进一步优化，只对修改的部分进行规范检查

解决方案：增加lint-staged，并更改package.json，如下：

```json
// package.json
{
    "scripts": {
        "precommit": "lint-staged"
    },
  	"lint-staged": {
        "src/*.{js,vue}": ["eslint --fix", "git add"]
    }
}
```

lint-staged的配置还可以独立为文件：.lintstagedrc，如下：

```json
// .lintstagedrc.json
{
    "src/*.{js,vue}": ["eslint --fix", "git add"]
}
```

上面这段的意思是，对文件进行规范性检查之后，将修改的文件add进去。



