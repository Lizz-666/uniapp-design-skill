# CSS Compatibility

## 单位系统

### rpx（Responsive Pixel）

```
750rpx = 屏幕宽度
1rpx ≈ 0.5px（在 375pt 宽设备上）
iPhone 6: 1rpx = 0.5px
iPhone 6 Plus: 1rpx ≈ 0.6px
```

**什么时候用 rpx**：需要适配不同屏幕的尺寸（宽度、间距、字号）
**什么时候用 px**：固定尺寸（1px 边框、阴影偏移）
**什么时候不用 rpx**：大屏设备（iPad）rpx 会过大，建议限制 max-width 或用 px

**不要用**：`rem`（小程序 root font-size 不确定）、`vw/vh`（部分平台不支持）

### 尺寸单位对比

| 单位 | 是否推荐 | 场景 |
|------|---------|------|
| `rpx` | 推荐 | 响应式尺寸、间距、字号 |
| `px` | 推荐 | 固定值：1px 边框、阴影 |
| `%` | 可用 | 相对父容器 |
| `vh/vw` | 部分支持 | 全屏高度可用 `100vh`，但避免用于宽度 |
| `em/rem` | 不推荐 | 小程序中行为不确定 |

## 支持的 CSS 特性

### 可用

```css
display: flex / inline-flex / block / inline-block / none
flex-direction / flex-wrap / justify-content / align-items / flex / align-self
position: relative / absolute / fixed
top / right / bottom / left / z-index
width / height / min-width / max-width / min-height / max-height
margin / padding
border / border-radius
background / background-color / background-image (linear-gradient)
color / font-size / font-weight / font-family / line-height / text-align
opacity / overflow
transform: translate / scale / rotate
transition: property duration timing-function
animation / @keyframes
box-shadow
```

### 不支持

```css
display: grid                              /* 用 flex 嵌套替代 */
:has()                                     /* 用 JS/class 切换 */
:is() / :where() / :not(复杂选择器)         /* 只支持简单 :not(.class) */
container queries / @container             /* 用 media query 或 JS */
@layer                                     /* 不支持 */
oklch() / color-mix() / light-dark()       /* 用 hex/rgba */
@property                                  /* 不支持 */
@supports                                  /* 不支持 */
aspect-ratio                               /* 部分平台不支持，用 padding-top hack */
text-decoration: underline dashed          /* 部分平台只支持实线 */
backdrop-filter: blur()                    /* 微信支持，其他平台不一定 */
scroll-snap / scroll-behavior: smooth      /* 大部分不支持 */
mix-blend-mode                             /* 不支持 */
columns / column-count                     /* 不支持 */
object-fit / object-position (image)       /* 用 image 组件的 mode 属性 */
::before / ::after                         /* 微信支持，部分平台不支持或有限制 */
```

## 选择器限制

```css
/* 不支持 */
* { }                    /* 通配符 */
body { }                 /* 无 body 元素 */
html { }                 /* 无 html 元素 */
div:nth-child(3n) { }   /* 复杂伪类 */
[data-type="xx"] { }    /* 属性选择器（部分平台支持，但不稳定） */

/* 支持 */
.class { }
#id { }
view { }
view text { }
view > text { }
view, text { }
.view::after { }         /* 微信支持 */
view:first-child { }     /* 部分平台 */
view:last-child { }
```

## 替代方案

| 不支持 | 替代方案 |
|--------|---------|
| Grid | Flex 嵌套：外层 `flex-direction: row; flex-wrap: wrap`，子项设固定宽度 |
| `:has()` | 用 JS 计算状态，动态绑定 class |
| Container queries | 用 `uni.getSystemInfoSync()` 获取容器尺寸 + 动态 class |
| `oklch()` | 用在线工具换算为 hex，或直接用 hex/rgba |
| `gap`（flex） | 用 margin 间距：`margin: 0 16rpx` |
| Web Font | 系统字体栈，或 base64 内嵌（仅小图标字体） |
| `position: sticky` | `scroll-view` + `@scroll` + 动态 `position: fixed` |
| `100vh`（安全区域） | `calc(100vh - env(safe-area-inset-bottom))` |
| `aspect-ratio` | `padding-top: 56.25%` hack（16:9） |
| `@layer` | 不用，直接按顺序写 CSS |
| `backdrop-filter` | 渐变遮罩替代毛玻璃 |

## Flex 替代 Grid 的常见模式

```css
/* 两列等宽 */
.grid-2 {
  display: flex;
  flex-wrap: wrap;
}
.grid-2 > .item {
  width: calc(50% - 12rpx);
  margin-right: 12rpx;
  margin-bottom: 12rpx;
}
.grid-2 > .item:nth-child(2n) {
  margin-right: 0;
}

/* 三列等宽 */
.grid-3 > .item {
  width: calc(33.333% - 16rpx);
  margin-right: 16rpx;
}
.grid-3 > .item:nth-child(3n) {
  margin-right: 0;
}
```

## 样式隔离

组件默认样式隔离，需要在组件选项中配置：

```js
// Vue 2
export default {
  options: {
    styleIsolation: 'isolated'     // 默认，组件内外样式互不影响
    // 'apply-shared'              // 接受外部样式
    // 'shared'                    // 双向共享（不推荐）
  }
}

// Vue 3
<script setup>
defineOptions({
  options: {
    styleIsolation: 'apply-shared'
  }
})
</script>
```

外部要修改组件内部样式，需使用 `:class` 传入或用 `apply-shared`。

## 性能建议

- 避免大面积 `box-shadow`（尤其是 blur 值大的）
- 避免频繁修改 `style`，优先用 `class` 切换
- 长列表用虚拟列表，不要渲染上万个节点
- 图片用 `lazy-load` 属性：`<image lazy-load>`
- 动画优先用 `transform` + `opacity`，不要动画 `width`/`height`/`margin`
- `v-if` vs `v-show`：频繁切换用 `v-show`，不频繁用 `v-if`（减少节点数）
