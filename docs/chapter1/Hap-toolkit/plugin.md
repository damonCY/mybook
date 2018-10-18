### plugin

- 1、new webpack.DefinePlugin() // 定义环境变量
- 2、new HandlerPlugin()
- 3、new ResourcePlugin()   // 
- 4、new ZipPlugin() // 压缩代码
- 5、NotifyPlugin()



#### 1、[new webpack.DefinePlugin()](https://www.jianshu.com/p/887f0454ef96) // 定义环境变量

```
new webpack.DefinePlugin({
    // 平台：na
    ENV_PLATFORM: JSON.stringify(nodeConf.NODE_PLATFORM),
    // 阶段: dv|qa|ol
    ENV_PHASE: JSON.stringify(nodeConf.NODE_PHASE),
    ENV_PHASE_DV: nodeConf.NODE_PHASE === 'dv',
    ENV_PHASE_QA: nodeConf.NODE_PHASE === 'qa',
    ENV_PHASE_OL: nodeConf.NODE_PHASE === 'ol'
})
// DefinePlugin可以在编译时期创建全局变量。
```



#### 2、new HandlerPlugin()

跨平台，在window下也能运行

```
process.env['NODE_TEST'] === 'Y' ? 'global = typeof window === "undefined" ? global.__proto__  : window ;' : ''
```



#### 3、new ResourcePlugin()  

收集资源文件，复制到输出目录

* 1、处理.9.png图

* 2、拷贝manifest.json 和 图片资源等

* 3、删除webpack-dev-server注入的watch依赖

webpack/plugin/resource-plugin



#### 4、new ZipPlugin()

```
innerOutputFS.mkdirp(options.output) // 创建文件夹
// options= { name: 'com.application.demo',
  versionCode: '1',
  output: '/Users/chenyong/Work/project/cardTest/dist',
  pathBuild: '/Users/chenyong/Work/project/cardTest/build',
  sign: 'debug' }

// options.output = /Users/chenyong/Work/project/cardTest/dist // 输出目录
```

[Jszip](https://blog.csdn.net/sujun10/article/details/76038886) 打包文件



#### 5、notify-plugin()

编译完成时done: 发起手机刷新请求

```
compiler.plugin('done', function (stats) {
    // 发送通知
    const serverPath = path.join(__dirname, '../../..', 'server')
    const cmdStr = 'cd ' + serverPath + ' && node ./command/notify.js'
    childProcess.exec(cmdStr, function (err, stdout, stderr) {
      colorconsole.log('### App Loader ### 请求手机自动刷新')
      err && colorconsole.log(err.message)
      stdout && colorconsole.log(stdout)
      stderr && colorconsole.log(stderr)
    })
  })
```

