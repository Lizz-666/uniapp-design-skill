---
name: uniapp-design
description: "Use when developing uni-app projects targeting mini-programs (WeChat, Alipay, Baidu, ByteDance, QQ, DingTalk) or H5/App — writing pages, components, styles, handling cross-platform compatibility, or designing mobile UI. Triggers on uni-app, mini-program, rpx, pages.json, manifest.json, uni.xxx API usage."
---

# uniapp-design

让 AI 生成符合小程序平台约束的高质量 uni-app 代码，避免 Web 思维的惯性错误。

## 铁律

1. **用 `rpx`** 不用 `rem`/`vw` 作为主要单位（750rpx = 屏幕宽度）
2. **用 `view`/`text`/`image`/`navigator`** 不用 `div`/`span`/`img`/`a`
3. **用 `@tap`** 不用 `@click`/`onclick`
4. **不用 CSS Grid** — 用 Flexbox 嵌套实现多列布局
5. **不用 `oklch()`/`color-mix()`/`light-dark()`** — 用 `hex`/`rgba`
6. **不引入 Web Font** — 用系统字体栈
7. **不用 DOM 操作** — 不写 `document.querySelector`/`getElementById`，用 `ref` + Vue 响应式
8. **不用 `:has()`/`container queries`/`@layer`** — 小程序不支持
9. **不用 `*`/`body`/`html` 选择器** — 小程序没有这些元素
10. **平台差异用条件编译** — `#ifdef MP-WEIXIN` / `#ifdef MP-ALIPAY`

## Anti-Patterns

| 错误写法 | 正确写法 | 原因 |
|---------|---------|------|
| `<div>` / `<span>` / `<p>` | `<view>` / `<text>` | 小程序没有 HTML 标签 |
| `<a href="...">` | `<navigator url="...">` | 用 navigator 组件 |
| `<img src="...">` | `<image src="...">` | 用 image 组件 |
| `@click` / `onclick` | `@tap` | 小程序事件是 tap |
| `rem` / `vw` 布局单位 | `rpx` | rpx 是小程序响应式单位 |
| `display: grid` | Flex 嵌套 | 小程序不支持 Grid |
| `oklch()` / `color-mix()` | `#hex` / `rgba()` | 小程序 CSS 不支持 |
| `@import url(...)` Web Font | 系统字体栈 | 小程序无法加载外部字体 |
| `document.querySelector` | `this.$refs` / `ref` | 无 DOM，用 Vue 方式 |
| `vue-router` 的 `push`/`go` | `uni.navigateTo` / `uni.navigateBack` | 用 uni 路由 API |
| `body { ... }` / `* { ... }` | `.page { ... }` | 无 body/html 元素 |
| `localStorage` | `uni.setStorage` / `uni.getStorage` | 用 uni 存储 API |
| `axios` / `fetch` | `uni.request` | 用 uni 网络请求 |
| `window.addEventListener` | 页面生命周期 / `uni.onWindowResize` | 无 window 对象 |
| `created` 中调小程序 API | `onReady` 之后 | 部分原生组件在 created 时未就绪 |
| `v-html`（大量使用） | `<rich-text>` 组件 | 小程序不支持 v-html |
| `position: sticky`（随意用） | `scroll-view` + `@scroll` | 部分小程序平台 sticky 行为不一致 |

## Quick Reference

### 常用组件

| 用途 | 组件 |
|------|------|
| 容器 | `<view>` |
| 文本 | `<text>` |
| 图片 | `<image mode="aspectFill">` |
| 链接/导航 | `<navigator url="/pages/xx">` |
| 按钮 | `<button>` |
| 输入框 | `<input>` / `<textarea>` |
| 滚动容器 | `<scroll-view scroll-y>` |
| 轮播 | `<swiper>` + `<swiper-item>` |
| 列表渲染 | `<view v-for="item in list" :key="item.id">` |
| 条件渲染 | `<view v-if>` / `<view v-show>` |

### 常用事件

| 事件 | 写法 |
|------|------|
| 点击 | `@tap="handle"` |
| 输入 | `@input="handle"` |
| 改变 | `@change="handle"` |
| 长按 | `@longpress="handle"` |
| 提交 | `@submit="handle"` |
| 触摸开始 | `@touchstart="handle"` |
| 触摸结束 | `@touchend="handle"` |
| 滚动 | `@scroll="handle"` (scroll-view) |

### 页面生命周期

| 钩子 | 时机 |
|------|------|
| `onLoad(options)` | 页面加载，接收路由参数 |
| `onShow()` | 页面显示（每次都触发） |
| `onReady()` | 页面初次渲染完成 |
| `onHide()` | 页面隐藏 |
| `onUnload()` | 页面卸载 |
| `onPullDownRefresh()` | 下拉刷新 |

### 常用 API

```
uni.navigateTo({ url })      # 保留当前页跳转
uni.redirectTo({ url })      # 关闭当前页跳转
uni.switchTab({ url })       # 切换 TabBar 页
uni.navigateBack({ delta })  # 返回
uni.request({ url, method, data })  # 网络请求
uni.setStorage({ key, data })       # 异步存储
uni.getStorage({ key })             # 异步读取
uni.showToast({ title, icon })      # 轻提示
uni.showModal({ title, content })   # 弹窗
uni.showLoading({ title })          # 加载提示
```

### 路由配置 (pages.json)

```json
{
  "pages": [
    { "path": "pages/index/index", "style": { "navigationBarTitleText": "首页" } }
  ],
  "globalStyle": {
    "navigationBarTextStyle": "black",
    "navigationBarTitleText": "App",
    "navigationBarBackgroundColor": "#ffffff"
  },
  "tabBar": {
    "list": [
      { "pagePath": "pages/index/index", "text": "首页" },
      { "pagePath": "pages/my/my", "text": "我的" }
    ]
  }
}
```

## Reference Files (按需加载)

只在需要时加载对应文件，不要一次性全部加载：

| 场景 | 加载文件 | 触发关键词 |
|------|---------|-----------|
| 写组件/页面结构、生命周期、组件通信 | [component-mapping.md](reference/component-mapping.md) | view, text, navigator, 生命周期, props, emit, Vue2/3 |
| 写样式/CSS、处理兼容问题 | [css-compat.md](reference/css-compat.md) | rpx, 样式, 选择器, flex, 不支持, WXSS, 阴影 |
| 搭建设计系统、定义变量 | [design-tokens.md](reference/design-tokens.md) | token, 变量, 间距, 色彩, SCSS, 主题, 字体 |
| 处理平台差异、分包、条件编译 | [platform-patterns.md](reference/platform-patterns.md) | #ifdef, 微信, 支付宝, 分包, 条件编译, 跨端 |
| 设计 UI/布局/动效 | [design-principles.md](reference/design-principles.md) | 排版, 色彩, 空间, 触摸, 动效, 空状态, 表单 |

## 系统字体栈

```css
font-family: -apple-system, BlinkMacSystemFont, "Helvetica Neue",
  "PingFang SC", "Microsoft YaHei", sans-serif;
```

iOS 渲染为苹方，Android 渲染为各自系统字体，无需加载外部字体。
