# Cross-Platform Patterns

## 条件编译

### 语法

```
#ifdef PLATFORM       — 仅在某平台生效
#ifndef PLATFORM      — 除了某平台都生效
#endif                — 结束

可用的 PLATFORM 值：
  MP-WEIXIN     微信小程序
  MP-ALIPAY     支付宝小程序
  MP-BAIDU      百度小程序
  MP-TOUTIAO    字节跳动小程序（抖音/头条）
  MP-QQ         QQ 小程序
  MP-DINGTALK   钉钉小程序
  MP            所有小程序
  H5            H5
  APP           App（iOS/Android）
```

### 模板中

```html
<!-- #ifdef MP-WEIXIN -->
<button open-type="getPhoneNumber">微信获取手机号</button>
<!-- #endif -->

<!-- #ifdef MP-ALIPAY -->
<button open-type="getAuthorize">支付宝授权</button>
<!-- #endif -->
```

### JS 中

```js
// #ifdef MP-WEIXIN
wx.someWeixinOnlyAPI()
// #endif

// #ifdef MP-ALIPAY
my.someAlipayOnlyAPI()
// #endif

// 或用运行时判断
const platform = uni.getSystemInfoSync().uniPlatform
```

### CSS 中

```css
/* #ifdef MP-WEIXIN */
.weixin-only { color: red; }
/* #endif */

/* #ifdef MP-ALIPAY */
.alipay-only { color: blue; }
/* #endif */
```

## 平台差异

### API 差异

| 功能 | 微信 | 支付宝 | 说明 |
|------|------|--------|------|
| 登录 | `uni.login` + `wx.login` | `uni.login` + `my.getAuthCode` | code 换取方式不同 |
| 支付 | `uni.requestPayment` provider: `wxpay` | provider: `alipay` | orderInfo 格式不同 |
| 分享 | `onShareAppMessage` | `onShareAppMessage` | 支付宝需要额外配置 |
| 地图 | `<map>` 组件 | `<map>` 组件 | key 申请平台不同 |
| 扫码 | `uni.scanCode` | `uni.scanCode` | 基本一致 |
| 订阅消息 | `wx.requestSubscribeMessage` | 需用模板消息 | 机制完全不同 |
| 获取手机号 | `open-type="getPhoneNumber"` | `open-type="getAuthorize"` | 回调结构不同 |
| 小程序跳转 | `wx.navigateToMiniProgram` | `my.navigateToMiniProgram` | 参数名有差异 |

### 组件差异

| 组件 | 微信 | 支付宝 | 百度 | 头条 |
|------|------|--------|------|------|
| `<button open-type>` | 丰富 | 部分 | 部分 | 有限 |
| `<input>` | 功能完整 | 功能完整 | 功能完整 | 部分属性不支持 |
| `<canvas>` | type="2d" | 旧规范 | 旧规范 | 部分支持 |
| `<web-view>` | 支持 | 支持 | 不支持 | 部分支持 |
| `<ad>` | 支持 | 支持 | 支持 | 支持 |

## 分包加载

### pages.json 配置

```json
{
  "pages": [
    { "path": "pages/index/index" },
    { "path": "pages/my/my" }
  ],
  "subPackages": [
    {
      "root": "pages/order",
      "pages": [
        { "path": "list" },
        { "path": "detail" }
      ]
    },
    {
      "root": "pages/product",
      "pages": [
        { "path": "list" },
        { "path": "detail" }
      ]
    }
  ],
  "preloadRule": {
    "pages/index/index": {
      "network": "all",
      "packages": ["pages/order"]
    }
  }
}
```

### 各平台包体积限制

| 平台 | 主包 | 总包 |
|------|------|------|
| 微信 | 2MB | 20MB（分包后） |
| 支付宝 | 2MB | 20MB |
| 百度 | 2MB | 8MB |
| 头条 | 2MB | 20MB |

图片和静态资源尽量放 CDN，不要放项目里。

## 导航与路由

### pages.json 配置

```json
{
  "pages": [
    {
      "path": "pages/index/index",
      "style": {
        "navigationBarTitleText": "首页",
        "enablePullDownRefresh": true
      }
    }
  ],
  "globalStyle": {
    "navigationBarTextStyle": "black",
    "navigationBarBackgroundColor": "#ffffff",
    "backgroundColor": "#f5f5f5"
  },
  "tabBar": {
    "color": "#999999",
    "selectedColor": "#1a5fff",
    "backgroundColor": "#ffffff",
    "borderStyle": "black",
    "list": [
      {
        "pagePath": "pages/index/index",
        "text": "首页",
        "iconPath": "static/tab/home.png",
        "selectedIconPath": "static/tab/home-active.png"
      }
    ]
  }
}
```

### 自定义 NavigationBar

```json
// pages.json 中设置
{
  "path": "pages/detail/detail",
  "style": {
    "navigationStyle": "custom"
  }
}
```

自定义导航栏需处理状态栏高度：

```js
const sysInfo = uni.getSystemInfoSync()
const statusBarHeight = sysInfo.statusBarHeight // 状态栏高度
const navBarHeight = 44 // 导航栏内容高度
// 总高度 = statusBarHeight + navBarHeight
```

## 网络请求

### 域名白名单

| 平台 | 配置位置 | 说明 |
|------|---------|------|
| 微信 | mp.weixin.qq.com → 开发管理 | request 合法域名 |
| 支付宝 | open.alipay.com | 白名单设置 |
| 头条 | microapp.bytedance.com | 服务器域名 |

开发阶段可在开发者工具中关闭域名校验，**上线前必须配置白名单**。

### uni.request 封装

```js
const request = (options) => {
  return new Promise((resolve, reject) => {
    uni.request({
      url: BASE_URL + options.url,
      method: options.method || 'GET',
      data: options.data,
      header: {
        'Authorization': uni.getStorageSync('token') || '',
        'Content-Type': 'application/json',
        ...options.header
      },
      success: (res) => {
        if (res.statusCode === 200) {
          resolve(res.data)
        } else if (res.statusCode === 401) {
          uni.navigateTo({ url: '/pages/login/login' })
          reject(res)
        } else {
          reject(res)
        }
      },
      fail: reject
    })
  })
}
```

## 常见坑

| 问题 | 原因 | 解决 |
|------|------|------|
| 图片不显示 | 域名未配白名单 | 后台配置 + wx.previewImage 不受限制 |
| `scroll-view` 不滚动 | 没有设固定高度 | 必须设 `height` 或 `max-height` |
| `v-for` 报错缺 key | 没有写 `:key` | 必须写 `:key` 且值唯一 |
| 自定义组件样式不生效 | 样式隔离 | 配置 `styleIsolation: 'apply-shared'` |
| `position: fixed` 在 scroll-view 内失效 | scroll-view 会创建新层叠上下文 | fixed 元素放到 scroll-view 外面 |
| `setData`/`this.setData` 不是 Vue 写法 | Vue 中不用 setData | 直接 `this.xxx = val` 或 `ref.value = val` |
| `input` 在 iOS 上有内边距 | 系统默认样式 | `box-sizing: border-box` + 重置 padding |
| Android 底部安全区 | 刘海/底部横条 | `padding-bottom: env(safe-area-inset-bottom)` |
| 文字不自动换行 | text 默认不换行 | `word-break: break-all` 或 `word-wrap: break-word` |
| tabBar 图标不显示 | 图片太大或格式问题 | 用 png，建议 81x81px，不超过 40KB |

## 长列表优化

- 使用 `scroll-view` 的 `@scrolltolower` 实现分页加载
- 数据量超过 100 条时考虑虚拟列表
- 图片必须 `lazy-load`
- 列表项避免过多嵌套层级
- `v-for` 中避免复杂计算，用 computed 预处理
