# 常用库

##### minimist

处理命令行参数

##### inquirer

接收输入

##### path

1.父文件夹  path.dirname()

2.文件名      path.basename()

3.扩展名      path.extname()

4.resolve

```
path.resolve('/foo/bar', './baz');
// 返回: '/foo/bar/baz'

path.resolve('/foo/bar', '/tmp/file/');
// 返回: '/tmp/file'

path.resolve('wwwroot', 'static_files/png/', '../gif/image.gif');
// 如果当前工作目录是 /home/myself/node，
// 则返回 '/home/myself/node/wwwroot/static_files/gif/image.gif'

var path = require('path');

console.log(path.resolve())         //    /Users/yuan/Documents/Web 
console.log(path.resolve('/aaa'))   //    /aaa 
console.log(path.resolve('./aaa'))  //    /Users/yuan/Documents/Web/aaa
console.log(path.resolve('/aaa', '/bbb'))   //    /bbb
console.log(path.resolve('/aaa', './bbb'))  //    /aaa/bbb
console.log(path.resolve('./aaa', '/bbb'))  //    /bbb
console.log(path.resolve('./aaa', './bbb'))  //   /Users/yuan/Documents/Web/aaa/bbb
console.log(path.resolve('./aaa', './bbb', './ccc'))  // /Users/yuan/Documents/Web/aaa/bbb/ccc
console.log(path.resolve('../aaa', './bbb', '../ccc'))  // /Users/yuan/Documents/aaa/ccc
console.log(path.resolve('../aaa', '../bbb', '../ccc'))  // /Users/yuan/Documents/ccc
console.log(path.resolve('../aaa', './bbb', './ccc'))  // /Users/yuan/Documents/aaa/bbb/ccc
console.log(path.resolve('./aaa', './bbb', '../ccc'))  // /Users/yuan/Documents/Web/aaa/ccc
```

如果没有传入 `path` 片段，则 `path.resolve()` 将返回当前工作目录的绝对路径。

从后向前，若字符以 / 开头，不会拼接到前面的路径；若以 ../ 开头，拼接前面的路径，且不含最后一节路径；若以 ./ 开头 或者没有符号 则拼接前面路径。

##### fs

1.文件描述符  fs.open() & fs.openSync()

2.文件属性   fs.stat() & fs.statSync()

```javascript
fs.stat('filePath', (err, stats) => {
  if (err) {
    console.error(err)
    return
  }
  stats.isFile()        // 1.判断文件是否文件
  stats.isDirectory() 	// 2.判断文件是否目录
  stats.isSymbolicLink()// 3.判断文件是否符号链接
  stats.size  			// 4.获取文件的大小（以字节为单位）
})
```

3.读取文件  fs.readFile & fs.readFileSync()

4.写入文件  

​		fs.writeFile()  & fs.appendFile() & fs.appendFileSync() & 流

5.使用文件夹

​	1.检查文件是否存在以及Node.js 是否具有访问权限  fs.access()

​	2.读取目录的内容  fs.readdir()  & fs.readdirSync()

​	3.重命名文件夹  fs.rename() & fs.renameSync()

​	4.删除文件夹  fs.rmdir() & fs.rmdirSync()

​		删除包含内容的文件夹可能会更复杂，建议使用fs-extra模块





##### express

常用的 req 的属性：

- `req.query`: 解析后的 url 中的 querystring，如 `?name=haha`，req.query 的值为 `{name: 'haha'}`
- `req.params`: 解析 url 中的占位符，如 `/:name`，访问 /haha，req.params 的值为 `{name: 'haha'}`
- `req.body`: 解析后请求体，需使用相关的模块，如 [body-parser](https://www.npmjs.com/package/body-parser)，请求体为 `{"name": "haha"}`，则 req.body 为 `{name: 'haha'}`
- `req.fields`: 解析表单数据，如果表单中有文件，则req的contentType需设置为`multipart/form-data`



# 基础知识

## API

##### setTimeout()

当计时结束时，添加到宏任务队列中。

##### setImmediate()

在文件I/O 、 网络I/O中 ：setImmediate 会先于 settimeout (0) 

否则一般情况下 ：setTimeout(0)  会先于 setImmediate

##### nextTick()

process.nextTick()属于微任务，但优先级高于promise.then。

##### Buffer

###### buffer.write()

​		写入时，从头到尾覆盖。

```js
const buf = Buffer.from('Hey!')
buf.write('w')   // wey!
```

###### buffer.copy()

​		第一个参数是复制到的对象，调用copy方法的是被复制的buffer。

```
const buf = Buffer.from('Hey!')
let bufcopy = Buffer.alloc(4) //分配 4 个字节。
buf.copy(bufcopy)  // bufcopy => 'Hey'
```

###### buffer.slice()

​		切片不是副本：原始 buffer 仍然是真正的来源。 如果那改变了，则切片也会改变。

```
const buf = Buffer.from('Hey!')
buf.slice(0).toString() //Hey!
const slice = buf.slice(0, 2)
console.log(slice.toString()) //He
buf[1] = 111 //o
console.log(slice.toString()) //Ho
```





## 模板引擎

##### ejs

ejs 有 3 种常用标签：

1. `<% code %>`：运行 JavaScript 代码，不输出
2. `<%= code %>`：显示转义后的 HTML内容
3. `<%- code %>`：显示原始 HTML 内容

> 注意：`<%= code %>` 和 `<%- code %>` 都可以是 JavaScript 表达式生成的字符串，当变量 code 为普通字符串时，两者没有区别。当 code 比如为 `<h1>hello</h1>` 这种字符串时，`<%= code %>` 会原样输出 `<h1>hello</h1>`，而 `<%- code %>` 则会显示 H1 大的 hello 字符串。

下面的例子解释了 `<% code %>` 的用法：

Data

```
supplies: ['mop', 'broom', 'duster']
```

Template

```
<ul>
<% for(var i=0; i<supplies.length; i++) {%>
   <li><%= supplies[i] %></li>
<% } %>
</ul>
```

Result

```
<ul>
  <li>mop</li>
  <li>broom</li>
  <li>duster</li>
</ul>
```

更多 ejs 的标签请看 [官方文档](https://www.npmjs.com/package/ejs#tags)。

# 相关工具

##### Kerberos

```shell
zzzz@MacBook-Pro ~ % cd /usr/local
zzzz@MacBook-Pro local % source ~/.bash_profile
zzzz@MacBook-Pro local % zzlogin yangshuangxing work@192.168.152.223
#spawn /usr/local/ssh -o GSSAPIAuthentication=yes -o 			  		#GSSAPIDelegateCredentials=yes -o GSSAPITrustDNS=yes -o#GSSAPIKeyExchange=yes yangshuangxing@rig.zhuanos.com
#spawn_id: spawn id exp6 not open
#    while executing
#"interact  "
#    (file "./rig.expect" line 16)
zzzz@MacBook-Pro local % ssh yangshuangxing@rig.zhuanos.com
(密码提示：首字母大写，有特殊符号)
#Heimdall > 欢迎来到彩虹桥。
INPUT:work@192.168.152.223
[work(yangshuangxing)@vm-bjdhj-149.14 ~]$ cd /opt/web/node/
# (如果该环境下还没clone项目需要先git cloe )
[work(yangshuangxing)@vm-bjdhj-149.14 node]$ ls
[work(yangshuangxing)@vm-bjdhj-149.14 node]$ cd zz-wechat-robot-egg/
# 重新部署前记得先停掉之前的服务
[work(yangshuangxing)@vm-bjdhj-149.14 zz-wechat-robot-egg]$ npm run stop
[work(yangshuangxing)@vm-bjdhj-149.14 zz-wechat-robot-egg]$ npm run start
# [egg-scripts] egg started on http://127.0.0.1:8423 

# 输入node -v 如果版本较低（v8.9.1）,需要执行下面的命令升级node
[work(yangshuangxing)@vm-bjdhj-149.14 zz-wechat-robot-egg]$ nvm use 12.16.1
```



# 实践

### 升级node20&pnpm

#### 1.根目录新建文件

```
// .nvmrc
20.11.0
```

```
// .npmrc
auto-install-peers=true
strict-peer-dependencies=false
legacy-peer-deps=true
side-effects-cache=false
xprofiler_binary_host_mirror=https://npmmirror.com/mirrors/xprofiler
sentrycli_cdnurl=https://npmmirror.com/mirrors/sentry-cli/
ELECTRON_MIRROR=https://npmmirror.com/mirrors/electron/
chromedriver_cdnurl=https://npmmirror.com/mirrors/chromedriver
disturl=https://npmmirror.com/dist
chromedriver-cdnurl=https://npmmirror.com/mirrors/chromedriver
couchbase-binary-host-mirror=https://npmmirror.com/mirrors/couchbase/v{version}
debug-binary-host-mirror=https://npmmirror.com/mirrors/node-inspector
electron-mirror=https://npmmirror.com/mirrors/electron/
flow-bin-binary-host-mirror=https://npmmirror.com/mirrors/flow/v
fse-binary-host-mirror=https://npmmirror.com/mirrors/fsevents
fuse-bindings-binary-host-mirror=https://npmmirror.com/mirrors/fuse-bindings/v{version}
git4win-mirror=https://npmmirror.com/mirrors/git-for-windows
gl-binary-host-mirror=https://npmmirror.com/mirrors/gl/v{version}
grpc-node-binary-host-mirror=https://npmmirror.com/mirrors
hackrf-binary-host-mirror=https://npmmirror.com/mirrors/hackrf/v{version}
leveldown-binary-host-mirror=https://npmmirror.com/mirrors/leveldown/v{version}
leveldown-hyper-binary-host-mirror=https://npmmirror.com/mirrors/leveldown-hyper/v{version}
mknod-binary-host-mirror=https://npmmirror.com/mirrors/mknod/v{version}
node-sqlite3-binary-host-mirror=https://npmmirror.com/mirrors
node-tk5-binary-host-mirror=https://npmmirror.com/mirrors/node-tk5/v{version}
nodegit-binary-host-mirror=https://npmmirror.com/mirrors/nodegit/v{version}/
operadriver-cdnurl=https://npmmirror.com/mirrors/operadriver
phantomjs-cdnurl=https://npmmirror.com/mirrors/phantomjs
profiler-binary-host-mirror=https://npmmirror.com/mirrors/node-inspector/
PUPPETEER_DOWNLOAD_HOST = https://cdn.npmmirror.com/binaries/chrome-for-testing
PUPPETEER_DOWNLOAD_BASE_URL = https://cdn.npmmirror.com/binaries/chrome-for-testing
python-mirror=https://npmmirror.com/mirrors/python
rabin-binary-host-mirror=https://npmmirror.com/mirrors/rabin/v{version}
sass-binary-site=https://npmmirror.com/mirrors/node-sass
sodium-prebuilt-binary-host-mirror=https://npmmirror.com/mirrors/sodium-prebuilt/v{version}
sqlite3-binary-site=https://npmmirror.com/mirrors/sqlite3
utf-8-validate-binary-host-mirror=https://npmmirror.com/mirrors/utf-8-validate/v{version}
utp-native-binary-host-mirror=https://npmmirror.com/mirrors/utp-native/v{version}
zmq-prebuilt-binary-host-mirror=https://npmmirror.com/mirrors/zmq-prebuilt/v{version}
canvas_binary_host_mirror=https://registry.npmmirror.com/-/binary/canvas
```

#### 2.安装node20 & pnpm

```shell
nvm install v20.11.0
npm install -g pnpm
```



