## npm run server 



* 启动命令行

```javascript
function main() {
  var file = (0, _utils.normalise)(_utils.toolsPath + '/server/index.js');
  (0, _utils.commandRun)('node ' + file);
} /*
// node /Users/chenyong/Work/project/test/demo01/node_modules/hap-toolkit/tools/server/index.js
```



* server/Index.js

```javascript
// 加载模块
moduler.init(conf)
// 启动服务器
start(conf, moduler)
```



#### moduler.init(conf)// 加载模块

```javascript
onst fileModuleList = parseRouterEntry(conf)
// fileModuleList
[ { name: 'debugger',
    path: '/Users/chenyong/Work/project/cardTest/node_modules/hap-toolkit/tools/debugger/router/index.js' }, // tools/debugger中的路由模块
  { name: 'packager',
    path: '/Users/chenyong/Work/project/cardTest/node_modules/hap-toolkit/tools/packager/router/index.js' } ] // tools/packager中的路由模块
    
const moduler = {
  moduleList: [], // 导出debugger和packager中的路由模块
  moduleHash: {},
  init,
  contains
}
const moduleItem = {
      // 模块名称
      name: loadModule.name,
      // 模块路径
      path: loadModule.path,
      // 导出对象
      hash: require(loadModule.path)
    }
moduler.moduleList.push(moduleItem)
moduler.moduleHash[moduleItem.name] = moduleItem
```



### start(conf, moduler) // 启动服务器



### 一、tools/packager的router模块

```javascript
function applyRouter (app) {
  app.use(routes.logger)
  const router = new KoaRouter()
  router.get(API.index, routes.index)
  router.get(API.bundle, routes.bundle)
  return router
}
```



* app.use(routes.logger)

  ```javascript
  // 文件路径
  const { pathClientLog } = context.conf.defaults /
  // pathClientLog = /Users/chenyong/Work/project/test/demo01/node_modules/hap-toolkit/tools/client.json 客户端ip存储位置
  const { sn, clientIp, linkMode } = getClientFromRequest(context.request) // 获取手机端ip
  ...
  recordClient(pathClientLog, client)// 存储客户端ip
  ```



 * router.get(API.index, routes.index) // API.index => 'index': '/'

    ```javascript
    async function index (context, next) {
        // 默认展示二维码
        const port = context.app.server.address().port
        const data = getServerAddress(port)
        const image = qr.image(data, { size: 9 })
        context.type = 'image/png'
        context.body = image
        await next()
    }
    ```

* router.get(API.bundle, routes.bundle) // API.bundle => 'bundle': '/bundle'

    ```javascript
    // routes.bundle  向手机端显示bundle文件
    async function bundle (context, next) {
        const projectPath = process.cwd()
        const projectName = getProjectName() // "package": "com.application.demo"
        const projectDist = path.join(projectPath, `dist`)
        const projectRpk1 = path.join(projectDist, `${projectName}.debug.rpk`)
        const projectRpk2 = path.join(projectDist, `${projectName}.release.rpk`)
        ...
        context.body = fs.createReadStream(projectRpk1)
    ```





### 二、Tools/debugger的router模块

```javascript
app.use(routes.redirectHtmlReq)  // 重定向扫码页面
app.use(serve(router.conf.staticDir)) // 开启服务：client目录下静态文件
API ={
    'index': '/',
    'register': '/postwsid',
    'startDebug': '/poststdbg',
    'qrCode': '/qrcode'
}
router.get(API.index, routes.index)
router.post(API.index, koaBody(), routes.adapterForBackwardComp)
router.post(API.register, koaBody(), routes.register)
router.post(API.startDebug, koaBody(), routes.startDebug)
router.get(API.qrCode, routes.qrCode) router.get(API.index, routes.index)
```





* router.get(API.index, routes.index)

```javascript
// 发起： http://localhost:12306/ 请求时
// 返回client中的index.html页面
//   /Users/chenyong/Work/project/test/demo01/node_modules/hap-toolkit/tools/debugger/client/html/index.html
```



* router.post(API.index, koaBody(), routes.adapterForBackwardComp)

```javascript
//适配老版本的应用加载器。
//使用老版本的应用加载器，会发送post到/, 并不会向/postwsid发请求；

```



* router.post(API.register, koaBody(), routes.register) //'register': '/postwsid',

```javascript
async function register (context, next) { // 手机注册
  colorconsole.info(`### App Server ### 收到App注册信息, 格式:\n${JSON.stringify(context.request.body)}\n`)
  await next()
  const { ws, application } = context.request.body

  // 生成调试url，并且向页面输出调试APP信息
  const { serverPort } = serverConf(context).defaults // 获取配置中的serverPort
  const inspectorUrl = getInspectorUrl({ ws, serverPort })
  emitWSEvent(context.io, 'appRegistered', { inspectorUrl, application })
  colorconsole.info(`请访问以下链接进行调试：\n\n${inspectorUrl}\n`)
}
```



* router.post(API.startDebug, koaBody(), routes.startDebug) //'startDebug': '/poststdbg' 

```javascript
export async function startDebug (context, next) {
  const params = getDebugInfoFromRequest(context.request)
  // params 
      { sn: undefined,
      linkMode: 1,
      ws: '10.232.72.145:37904/inspector',
      application: 'org.hapjs.mockup(Xiaomi/Redmi Note 4X@7.0)',
      devicePort: '37904' }
  
  const { sn, linkMode, devicePort, application } = params
  let ws = params.ws

  // ADB调试模式
  if (linkMode === LINK_MODE.ADB) {
    const { localWsPort, err } = await context.adbDebugger.forwardForWsChannel(sn, devicePort)
    if (err) {
      console.error(`startDebug(): adb forward 端口映射失败: ${err.message}`)
      return await next()
    }
    // ws中是手机的端口号，ADB模式下需要替换为forward对应的pc端口
    ws = ws.replace(devicePort, localWsPort)
  }

  // 生成调试url，并且向页面输出调试APP信息
  const { serverPort } = serverConf(context).defaults
  const inspectorUrl = getInspectorUrl({ ws, serverPort })
  emitWSEvent(context.io, 'appRegistered', { inspectorUrl, application })
  colorconsole.info(`请访问以下链接进行调试：\n\n${inspectorUrl}\n`)

  await startChrome(inspectorUrl, {
    'chromePath': serverConf(context).options.chromePath // 吊起chrome调试页面
  })
  await next()
}
```



* router.get(API.qrCode, routes.qrCode)  //'qrCode': '/qrcode'

```javascript
async function qrCode (context, next) { // 显示二维码
  const { serverPort } = serverConf(context).defaults
  const qrText = `http://${getServerIPAndPort(serverPort)}`
  const image = qr.image(qrText, {
    size: 9
  })
  await next()
  context.type = 'image/png'
  context.body = image
}
```



### 手机加载器扫描二维码的过程

![image-20181019102846892](../source/image-20181019102846892.png)

