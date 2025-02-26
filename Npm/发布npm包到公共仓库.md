NPM (node package manager)，通常称为node包管理器。顾名思义，它的主要功能就是管理node包，包括：安装、卸载、更新、查看、搜索、发布等。

npm 可以让 JavaScript 开发者在共享代码、复用代码以及更新共享的代码上更加方便。

例如我有一个项目，项目中有一个index.js的文件，我就可以把它发布到npm仓库中让其他开发者使用或者让其他项目调用。

```javascript
// index.js
window.onload = function(){
    console.log('Hello world!')
}
```
# package.json
在项目跟目录执行`npm init -y`后会根据默认配置生成package.json文件，package.json的详细说明可以参考[npm官方文档](https://docs.npmjs.com/cli/v7/configuring-npm/package-json#description)。

```json
{
  "name": "js-test",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "keywords": [],
  "author": "",
  "license": "ISC"
}

```
name和version一起构成了一个标识符，该标识符被认为是完全唯一的。软件包和版本号要同时进行修改。如果要发布软件包，则name和version字段是必填的。
## name

name不能与现有的npm包重复，长度必须小于或等于 214 个字符，不能以 “.” 和“_”开头，不能包含大写字母。

该名称最终成为URL的一部分、命令行上的参数和文件夹名称。因此，名称不能包含任何非URL安全字符。

一个名称可以有选择地以一个作用域作为前缀，例如@myorg/mypackage。


## version

一个完整的版本号，由三部分组成：主版本号（major）、次版本号(minor)、修订版本号(patch)，简称X.Y.Z。

主版本号（major）：项目（包）做了大量的变更，与旧的版本存在一定的不兼容性等问题。

次版本号（minor）：做了少量的变更或向下做了兼容。

修订版本号（patch）：修复上一个版本的bug或向下做兼容。

```shell
npm version major  // 0.1.0 -> 1.0.0
npm version minor  // 0.1.0 -> 0.2.0
npm version patch  // 0.1.0 -> 0.1.1
```
## main
main字段是一个模块ID，它是程序的主要入口点。也就是说，如果你的包名为foo，用户安装了它，然后执行了`require("foo")`，那么你的主模块的exports对象将被返回。

这应该是一个相对于包文件夹根目录的模块。

对于大多数模块来说，拥有一个主脚本是最有意义的，通常没有太多其他东西。

如果未设置main，则默认为包根文件夹中的index.js。


# 注册NPM账号
进入[https://www.npmjs.com/signup](https://www.npmjs.com/signup)注册npm账号。

npm账号用户名不支持修改。



## 终端登录
登录前要切换源为官方地址，登录时会打开浏览器进行验证，验证成功后身份信息会保存在本地。
```shell
PS D:\project\js-test> npm config get registry
https://registry.npm.taobao.org
PS D:\project\js-test> npm config set registry https://registry.npmjs.org/ 
PS D:\project\js-test> npm login
npm notice Log in on https://registry.npmjs.org/
Login at:
https://www.npmjs.com/login?next=/login/cli/28ce6e2f-b37c-41c4-b40f-3dd20fd960e8
Press ENTER to open in the browser...

Logged in on https://registry.npmjs.org/.
PS D:\project\js-test> 
```
# 发布到公共仓库
例如我的用户名是kitashirakawa_tamako，我就可以为包名添加前缀，使其独一无二（@kitashirakawa_tamako/js-test）。但是带有前缀的name会被默认为私有包，私有包需要进行付费，所以在发布时要加上作用域的参数，使其变为共有包。

```
PS D:\project\js-test> npm publish --access public
npm notice
npm notice 📦  @kitashirakawa_tamako/js-test@1.0.0
npm notice === Tarball Contents ===
npm notice 76B  index.js
npm notice 244B package.json
npm notice === Tarball Details ===
npm notice name:          @kitashirakawa_tamako/js-test
npm notice version:       1.0.0
npm notice filename:      kitashirakawa_tamako-js-test-1.0.0.tgz  
npm notice package size:  347 B
npm notice unpacked size: 320 B
npm notice shasum:        f00b51fbf07bd8dac4a2bf4552889363bae49b91
npm notice integrity:     sha512-4FJyLIihdZF3r[...]W4dJ30FWRYzlg==
npm notice total files:   2
npm notice
npm notice Publishing to https://registry.npmjs.org/ with tag latest and public access
+ @kitashirakawa_tamako/js-test@1.0.0
```
发布成功后就可以在npm网站的package页面查看了。
# 在其他项目中引入
新建测试项目，引入我们刚发布的@kitashirakawa_tamako/js-test包试试效果，结果就是在浏览器会输出“Hello world!”。

```
// 在终端里添加依赖
npm install @kitashirakawa_tamako/js-test

// 在js文件中引入
import '@kitashirakawa_tamako/js-test'

// 浏览器控制台打印
Hello world!
```




