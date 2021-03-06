# 发布到百度小游戏

百度小游戏是基于手机百度 app 上的智能小程序进行扩展的小游戏，它不仅提供了强大的游戏能力，还和智能小程序一样，提供了大量的原生接口，比如支付，文件系统，位置，分享等。相当于同时结合了 WEB 易于传播以及 Native 功能丰富的优势。

百度小游戏的运行环境和微信小游戏类似，基本思路也是封装必要的 WEB 接口提供给用户，尽可能追求和 WEB 同样的开发体验。百度小游戏在智能小程序环境的基础上提供了 WebGL 接口的封装，使得渲染能力和性能有了大幅度提升。不过由于这些接口都是百度团队通过自研的原生实现封装的，所以并不可以等同为浏览器环境。

作为引擎方，为了尽可能简化开发者的工作量，我们为用户完成的主要工作包括：

- 引擎框架适配百度小游戏 API，纯游戏逻辑层面，用户不需要任何额外的修改
- Cocos Creator编辑器提供了快捷的打包流程，直接发布为百度小游戏
- 自动加载远程资源，缓存资源以及缓存资源版本控制

具体百度小游戏的申请入驻，开发准备，游戏提交，审核和发布流程可以参考 [百度小游戏注册指导文档](https://smartprogram.baidu.com/docs/game/)。

## 使用 Cocos Creator 发布百度小游戏

### 准备工作

- 在 [百度开发者工具文档](https://smartprogram.baidu.com/docs/game/tutorials/howto/dev/) 里下载并安装百度开发者工具
- 在手机的应用商店中下载并安装百度应用
- 登录 [智能小程序平台](https://smartprogram.baidu.com/developer/index.html)，找到 appid

    ![](./publish-baidugame/appid.png)

## 构建选项介绍

一些通用的构建通用参数介绍，请参考 [通用构建参数介绍](build-options.md)。

| 选项名 | 可选 | 默认值 | 说明 | 字段名 |
| :-- | :-- | :-- | :-- | :-- |
| 初始场景分包 | - | false | 勾选后，首场景及其相关的依赖资源会被构建到发布包目录 assets 下的内置 Asset Bundle — [start-scene](../../asset/bundle.md#%E5%86%85%E7%BD%AE-asset-bundle) 中，提高初始场景的资源加载速度。 | startSceneAssetBundle
| 设备方向 | 必填 | landscape | 设备方向，填写后将会写入在 `game.json` 内。| orientation |
| appid | 必填 | 'testappid' | 百度小游戏 appid，填写后将会写入在 `project.swan.json` 内。| appid |
| 远程服务器地址 | - | - | 远程服务器地址，开发者需要在构建后手动将发布包目录下的 remote 文件夹上传到所填写的资源服务器地址上。 | remoteServerAddress |
| 生成开放数据域工程模板 | - | false | 用于接入开放数据域，详情可以参考 [开放数据域](./build-open-data-context.md) | buildOpenDataContextTemplate |

### 发布流程

1. 在 **构建发布** 面板中选择 **发布平台** 为 **百度小游戏**，填入 **appid**，然后点击 **构建**。

    ![](./publish-baidugame/build.png)

2. 构建完成后，会在发布包的目录下生成一个 **baidugame** 的百度小游戏工程文件夹，其中已经包含了百度小游戏环境的配置文件：`game.json` 和 `project.swan.json`

    ![](./publish-baidugame/package.png)

3. 使用 **百度开发者工具** 打开构建生成的 **baidugame** 文件夹，即可打开百度小游戏项目及预览调试游戏内容。**百度开发者工具** 的使用方式请参考 [百度开发者工具文档](https://smartprogram.baidu.com/docs/game/tutorials/howto/dev/)。

    ![](./publish-baidugame/preview.png)

**注意**：

- 请不要升级 **百度开发者工具** 到 **2.0.10** 版本。
- 预览调试时若出现了 **当前版本的开发者工具无法发布小程序，请更新最新的开发者工具** 的提示，说明填写的 appid 是小程序的 appid，不是小游戏的 appid，请重新申请一个小游戏 appid。

## 百度小游戏环境的资源管理

百度小游戏存在着包体限制，超过 4MB 的额外资源，必须通过网络请求下载。

我们建议用户只保存脚本文件在小游戏包内，其他资源都从远程服务器下载。Cocos Creator已经帮用户做好了远程资源的下载、缓存和版本管理，详情可参考 [资源管理](../../asset/cache-manager.md#资源下载流程)。

**注意**：

- 百度在真机上加载远程服务器上的资源时，目前只支持通过 https 访问，所以必须将资源文件放在 https 服务器上，否则会出现加载资源失败的情况。
- 如果缓存资源超过百度环境限制，用户需要手动清除资源，可以在百度小游戏中使用 `remoteDownloader.cleanAllCaches()` 和 `remoteDownloader.cleanOldCaches()` 接口来清除缓存。前者会清除缓存目录下的所有缓存资源，请慎重使用；而后者会清除缓存目录下目前应用中未使用到的缓存资源。

## 百度小游戏分包加载

百度小游戏的分包加载方式和微信小游戏类似，其包体限制如下：

- 所有包的总大小不超过 **8MB**
- 单个分包/主包大小不超过 **4MB**

具体的分包加载机制请参考 [小游戏分包](subpackage.md)。

## 平台 SDK 接入

除了纯游戏内容以外，百度小游戏环境还提供了非常强大的原生 SDK 接口，这些接口都是仅存在于百度小游戏环境中的，等同于其他平台的第三方 SDK 接口。这类 SDK 接口的移植工作在现阶段还是需要开发者自己处理。下面列举一些百度小游戏所提供的强大 SDK 能力：

1. 用户接口：登陆，授权，用户信息等
2. 百度收银台支付
3. 转发信息
4. 文件上传下载
5. 其他：图片、位置、广告、设备信息等等

## 百度小游戏已知问题

目前 Cocos Creator 对百度小游戏的适配工作还未完全结束，暂时还不支持以下组件：

- VideoPlayer
- WebView

## 参考链接

- [百度小游戏注册指导文档](https://smartprogram.baidu.com/docs/game/)
- [百度开发者工具文档](https://smartprogram.baidu.com/docs/game/tutorials/howto/dev/)
- [百度小游戏 API 文档](https://smartprogram.baidu.com/docs/game/api/openApi/authorize/)
- [百度小游戏分包加载](https://smartprogram.baidu.com/docs/game/tutorials/subpackages/sub/)
