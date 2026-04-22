# Component & Event Mapping

## HTML → uni-app 组件

| HTML | uni-app | 注意事项 |
|------|---------|---------|
| `<div>` | `<view>` | 最常用容器 |
| `<span>` / `<p>` | `<text>` | text 内只能放文本，不能嵌套 view |
| `<a href="">` | `<navigator url="">` | url 为页面路径 |
| `<img>` | `<image>` | 必须设宽高，用 mode 控制裁剪 |
| `<input>` | `<input>` | 事件用 `@input` 不是 `@change` |
| `<textarea>` | `<textarea>` | 固定高度需设 `auto-height` |
| `<button>` | `<button>` | 有 size/type/form-type 属性 |
| `<form>` | `<form>` | 用 `@submit` + `form-type="submit"` |
| `<ul>/<li>` | `<view v-for>` | 无原生列表组件 |
| `<select>` | `<picker>` | 用 picker 组件实现 |
| `<dialog>` | 自定义或 uni.showModal | 无原生 dialog |
| `<video>` | `<video>` | 注意平台差异 |
| `<audio>` | `<audio>` (部分平台) | 或用 `uni.createInnerAudioContext()` |
| `<canvas>` | `<canvas>` | 用 `type="2d"` (微信新规范) |
| `<iframe>` | `<web-view>` | 域名需配置白名单 |
| `<map>` | `<map>` | 经纬度用 Number 不用 String |
| `<scroll>` | `<scroll-view>` | 必须设固定高度 |
| `-` | `<swiper>` | 轮播图专用 |
| `-` | `<rich-text>` | 渲染 HTML 富文本 |
| `-` | `<movable-view>` | 可拖动元素 |
| `-` | `<cover-view>` | 覆盖原生组件 (map/video 上层) |

## image mode 说明

```
scaleToFill    — 默认，拉伸填满（会变形）
aspectFit      — 保持比例，完整显示（留白）
aspectFill     — 保持比例，填满容器（裁剪）
widthFix       — 宽度固定，高度自适应
heightFix      — 高度固定，宽度自适应
```

## Web 事件 → uni-app 事件

| Web | uni-app | 说明 |
|-----|---------|------|
| `onclick` / `@click` | `@tap` | 最常用 |
| `@change` (input) | `@input` | 输入时实时触发 |
| `@change` (select) | `@change` (picker) | 选择后触发 |
| `@focus` | `@focus` | 聚焦 |
| `@blur` | `@blur` | 失焦 |
| `@submit` | `@submit` | 表单提交 |
| `@scroll` | `@scroll` | scroll-view 专用 |
| `@touchstart` | `@touchstart` | 触摸开始 |
| `@touchmove` | `@touchmove` | 触摸移动 |
| `@touchend` | `@touchend` | 触摸结束 |
| `-` | `@longpress` | 长按（推荐用这个） |
| `-` | `@longtap` | 长按（兼容，推荐 longpress） |

事件对象 `event` 没有 `preventDefault`/`stopPropagation`，用 `.catch` 修饰符阻止冒泡：`@tap.stop`。

## 页面生命周期

```
onLoad(options)     — 页面加载时触发一次，接收路由参数
onShow()            — 页面显示时触发（每次从后台切回都触发）
onReady()           — 页面初次渲染完成（只触发一次）
onHide()            — 页面隐藏
onUnload()          — 页面卸载
onPullDownRefresh() — 下拉刷新（需在 pages.json 开启）
onReachBottom()     — 触底加载更多
onShareAppMessage() — 用户点击分享
onPageScroll()      — 页面滚动
```

**注意**：`onLoad` 的参数是路由 query 对象，`onShow` 没有参数。

## Vue 2 vs Vue 3 差异

### 页面写法

```vue
<!-- Vue 2 Options API -->
<template>
  <view class="page">
    <text>{{ message }}</text>
    <button @tap="handleTap">点击</button>
  </view>
</template>

<script>
export default {
  data() {
    return { message: 'Hello' }
  },
  onLoad(options) {
    console.log(options.id)
  },
  methods: {
    handleTap() { /* ... */ }
  }
}
</script>
```

```vue
<!-- Vue 3 Composition API -->
<template>
  <view class="page">
    <text>{{ message }}</text>
    <button @tap="handleTap">点击</button>
  </view>
</template>

<script setup>
import { ref } from 'vue'

const message = ref('Hello')

const handleTap = () => { /* ... */ }

// 生命周期
onLoad((options) => {
  console.log(options.id)
})
</script>
```

### Vue 3 需要从 @dcloudio/uni-app 导入生命周期：

```js
import { onLoad, onShow, onReady, onHide, onUnload,
         onPullDownRefresh, onReachBottom } from '@dcloudio/uni-app'
```

## 组件通信

| 方式 | 场景 | 写法 |
|------|------|------|
| `props` / `$emit` | 父子组件 | Vue 标准写法 |
| `provide` / `inject` | 深层嵌套 | Vue 标准写法 |
| `uni.$emit` / `uni.$on` | 跨组件/跨页面 | 全局事件总线，注意 `$off` 防泄漏 |
| `uni.setStorageSync` | 简单状态共享 | 适合非频繁变化的数据 |
| Vuex / Pinia | 复杂全局状态 | 推荐大型项目 |

**uni.$emit 用法**：

```js
// 页面 A：发送
uni.$emit('refreshList', { type: 'add' })

// 页面 B：监听
onLoad(() => {
  uni.$on('refreshList', (data) => { /* ... */ })
})
onUnload(() => {
  uni.$off('refreshList')  // 必须移除，防止内存泄漏
})
```
