现如今npm已经成为前端开发中必不可少的一部分，目前在npm上发布的包超过34万个，每周仍有超过1万个新包被发布，包一周下载量超过十亿次。作为一名好奇的前端，自然希望能把自己造的轮子让更多人看到。不同于传统的js模块，发布我们的组件还需要多一丢丢东西要做：

1. 设置npm账号
2. 设置忽略项
3. 使用babel cli
4. 发布
5. 组件质量认证

## 1.设置npm账号

首先，要在npm发布包，首先得[注册一个账号](https://www.npmjs.com/signup)，与github一样，npm对于公共包是免费的。

第二步，添加账号。在我们的Dos环境下，输入命令`npm adduser`，然后键入你在npm上注册的账号和密码。另外，`npm config ls`可以查看你的npm配置。

接下来，使用`npm publish`就可以发布你的包了。但在发布之前，我们还有一些工作要做。

## 2.设置忽略项

一个简单的js模块可能就只需要一个js文件，而一个完善的组件，往往还需要测试、文档、样例等目录，对于ES6或其他语法编写的源代码，还会有编译的目录。例如，ant-design的项目目录：

```
|-components
|-coverage
|-dist
|-docs
|-lib
|-node_modules
|-scripts
|-site
|-tests
|-typings
|-.babelrc
|-.gitignore
|-package.json
|-webpack.config.js
|-...
```

我们不需要把所有文件或目录提交到npm包，可以看到antd发布的包仅包含了其中几项：

```
|-dist
|-lib
|-package.json
|-...
```

npm会发布所有的文件，除了.gitignore所忽略的文件。也可以使用.npmignore来覆盖.gitignore配置的忽略。不过，有一种更便捷的方式，就是在package.json中设置你要发布的文件或路径：

```
{
	"files": [
   	"dist",
    "lib"
  ],
}
```

如上配置，就只有dist和lib目录会被发布出去，不过，有些文件是npm不会忽略的，有些文件是npm一定会忽略的:

```
// 不会被npm忽略的文件
package.json
README (and its variants)
CHANGELOG (and its variants)
LICENSE / LICENCE

// 一定会忽略的
node_modules
.*.swp
._*
.DS_Store
.git
.hg
.npmrc
.lock-wscript
.svn
.wafpickle-*
config.gypi
CVS
npm-debug.log
```

## 3.使用babel cli

现在，拜babel的强大功能，前端越来越多的使用更方便、更强大的语法，如ES6、ES7、jsx等，来编写代码。在发布代码前，我们必须要考虑到用户的使用环境，他们可能不使用这些语法进行开发，那么，最好的做法就是把所有源代码转译成兼容的ES5代码。仔细看上面的目录，会发现发布的组件并不是我们的源代码`/src`，而是编译到`/lib`和`/dist`的js文件。

首先，要先安装babel cli，安装好后就可以使用babel命令编译

```
npm install --save-dev babel-cli
```

第二步，配置`.babelrc`文件，把你需要的babel插件添加进去。如果你使用webpack，就可以把babel-loader的配置项项目复制到这个配置文件，以下是我一个React项目的配置：

```
{
  "presets": [
    "es2015",
    "stage-2",
    "react"
  ],
  plugins: ['transform-decorators-legacy'],
}
```

其中plugins配置的是ES7装饰者（非必需）。接下来，在package.json中配置脚本来运行babel：

```
"scripts": {
  "clean": "rd /s/q lib && mkdir lib",
  "lib": "npm run clean && babel src --out-dir lib 
},
```

这里，先清空lib目录，然后将`/src`的源文件，用babel转译至`/lib`目录下，`/lib`就是我们要发布的目录，在DOS中键入`npm run lib`就能看到效果了。当然，脚本的具体配置因项目而异。

## 4.发布

至此，为项目取个不重复的响亮名字，在DOS项目目录下输入`npm publish`就能发布你的组件了。每次发布时，需要注意更新版本号:

>一般来讲大部分的软件版本号分3段，比如 A.B.C
- A 表示大版本号，一般当软件整体重写，或出现不向后兼容的改变时，增加A，A为零时表示软件还在开发阶段。
- B 表示功能更新，出现新功能时增加B
- C 表示小修改，如修复bug，只要有修改就增加C

为了让更多人了解发布的包，务必加上精炼的README文档，中、英文最好。

## 5.组件质量认证

一个包能够被用户采用，除了功能满足需求，稳定的版本、充分的测试、准确地文档、合适的样例、npm下载量等等，都可能是用户的考察项。另外，最好将你的项目开源到github上，github能够自动连接CI服务，在线上测试你的代码，并提供诸如代码覆盖率报告等服务，并把这些信息用小标签的形式展现出来，来给包的质量背书。下面是antd的小标签：


[![](https://img.shields.io/travis/ant-design/ant-design.svg?style=flat-square)](https://travis-ci.org/ant-design/ant-design)
[![Codecov](https://img.shields.io/codecov/c/github/ant-design/ant-design/master.svg?style=flat-square)](https://codecov.io/gh/ant-design/ant-design/branch/master)
[![Dependency Status](https://img.shields.io/gemnasium/react-component/trigger.svg?style=flat-square)](https://gemnasium.com/ant-design/ant-design)
[![npm package](https://img.shields.io/npm/v/antd.svg?style=flat-square)](https://www.npmjs.org/package/antd)
[![NPM downloads](http://img.shields.io/npm/dm/antd.svg?style=flat-square)](https://npmjs.org/package/antd)
[![Percentage of issues still open](http://isitmaintained.com/badge/open/ant-design/ant-design.svg)](http://isitmaintained.com/project/ant-design/ant-design "Percentage of issues still open")
[![Join the chat at https://gitter.im/ant-design/ant-design](https://img.shields.io/gitter/room/ant-design/ant-design.svg?style=flat-square)](https://gitter.im/ant-design/ant-design?utm_source=badge&utm_medium=badge&utm_campaign=pr-badge&utm_content=badge)

如果对如何获得这些标签感兴趣，可以关注我之后的文章(ง •̀_•́)ง。

## 写在最后

中国互谅网行业的快速发展全世界有目共睹，希望在开源生态里，能看到越来越多的中国名字。

[★★react-freecolor★★](https://github.com/huangbuyi/react-freecolor)是我开发一个react组件，没开发完，但目录结构和项目配置基本成型，有兴趣的可以看看。


## 参考

- [understand npm](https://unpm.nodesource.com/)
- [软件的版本号是如何确定的？](https://www.zhihu.com/question/20289602)
- [shields.i](http://shields.io/)
- [4bin's Blog](http://4bin.cn)