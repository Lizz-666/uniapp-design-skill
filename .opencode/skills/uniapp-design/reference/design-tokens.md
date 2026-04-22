# Design Tokens

## 间距系统

基于 4rpx 精度，兼顾灵活性和一致性：

```scss
$spacing-4: 8rpx;
$spacing-8: 16rpx;
$spacing-12: 24rpx;
$spacing-16: 32rpx;
$spacing-20: 40rpx;
$spacing-24: 48rpx;
$spacing-32: 64rpx;
$spacing-48: 96rpx;
$spacing-64: 128rpx;
```

### 使用场景

| Token | 场景 |
|-------|------|
| `8rpx` | 紧凑间距、图标与文字间距 |
| `16rpx` | 同组元素间距、小内边距 |
| `24rpx` | 标准内边距、表单项间距 |
| `32rpx` | 卡片内边距、段落间距 |
| `40rpx` | 较大间距 |
| `48rpx` | 区块间距、卡片间距 |
| `64rpx` | 大区块间距 |
| `96rpx` | 页面顶部/底部留白 |
| `128rpx` | 大型留白 |

## 色彩系统

小程序不支持 `oklch()`/`color-mix()`，用 hex 实现专业配色。

### 主色扩展

基于一个主色，手动定义 5 个层级：

```scss
$color-primary-50:  #f0f5ff;
$color-primary-100: #dbe6ff;
$color-primary-200: #b3cfff;
$color-primary-300: #7aadff;
$color-primary-400: #3d85ff;
$color-primary-500: #1a5fff;  // 主色
$color-primary-600: #0044e0;
$color-primary-700: #0033b8;
$color-primary-800: #002290;
$color-primary-900: #001468;
```

### 中性色带色调

不要用纯灰 `#999`，向品牌色倾斜：

```scss
// 偏蓝冷色调
$color-gray-50:  #f5f7fa;
$color-gray-100: #e8ecf1;
$color-gray-200: #d1d9e3;
$color-gray-300: #b0bdd0;
$color-gray-400: #8a9ab3;
$color-gray-500: #6b7d99;
$color-gray-600: #556680;
$color-gray-700: #3d4d66;
$color-gray-800: #2a3648;
$color-gray-900: #1a2332;

// 偏暖色调
$color-warm-50:  #faf8f5;
$color-warm-100: #f0ebe3;
$color-warm-200: #e0d5c7;
$color-warm-500: #8a7d6b;
$color-warm-800: #3d3528;
```

### 功能色

```scss
$color-success: #52c41a;
$color-success-bg: #f6ffed;
$color-warning: #faad14;
$color-warning-bg: #fffbe6;
$color-error: #ff4d4f;
$color-error-bg: #fff2f0;
$color-info: #1890ff;
$color-info-bg: #e6f7ff;
```

### 60-30-10 法则

```
60% — 中性色/背景色（页面底色、卡片白、浅灰分隔）
30% — 次要色（文字色、边框、次级操作）
10% — 强调色（主色按钮、关键图标、重要标签）
```

## 排版系统

### 系统字体栈

```scss
$font-family: -apple-system, BlinkMacSystemFont, "Helvetica Neue",
  "PingFang SC", "Microsoft YaHei", sans-serif;
```

### 字号标量

```scss
$font-size-xs: 20rpx;     // 辅助文字、标签
$font-size-sm: 24rpx;     // 次要文字、描述
$font-size-base: 28rpx;   // 正文
$font-size-md: 30rpx;     // 重要正文
$font-size-lg: 32rpx;     // 小标题
$font-size-xl: 36rpx;     // 标题
$font-size-2xl: 40rpx;    // 大标题
$font-size-3xl: 48rpx;    // 页面主标题
$font-size-4xl: 56rpx;    // 数字展示
```

### 字重

```scss
$font-weight-normal: 400;
$font-weight-medium: 500;
$font-weight-semibold: 600;
```

小程序中 `300` (light) 渲染效果不稳定，建议只使用 `400/500/600`。

### 行高

```scss
$line-height-tight: 1.3;    // 标题
$line-height-normal: 1.5;   // 正文
$line-height-loose: 1.8;    // 长文阅读
```

移动端正文建议 `1.5-1.8`，比 Web 端略宽松。

## 圆角

```scss
$radius-sm: 8rpx;
$radius-md: 12rpx;
$radius-lg: 16rpx;
$radius-xl: 24rpx;
$radius-round: 999rpx;    // 胶囊/圆形
```

移动端圆角比 Web 端稍大更协调，避免 `4rpx` 这种几乎看不出的小圆角。

## 阴影

小程序阴影性能有限，轻量使用：

```scss
$shadow-sm: 0 2rpx 8rpx rgba(0, 0, 0, 0.06);
$shadow-md: 0 4rpx 16rpx rgba(0, 0, 0, 0.08);
$shadow-lg: 0 8rpx 32rpx rgba(0, 0, 0, 0.12);
```

**避免**：`spread` 值过大、`blur` 超过 `40rpx`、彩色阴影。

## 动效 Token

```scss
$duration-fast: 150ms;
$duration-normal: 250ms;
$duration-slow: 350ms;

$ease-out: cubic-bezier(0.22, 1, 0.36, 1);
$ease-in-out: cubic-bezier(0.45, 0, 0.55, 1);
```

**不用 bounce/elastic**，小程序动画容易卡顿，保持简洁。

## 暗色模式

小程序可通过 `@media (prefers-color-scheme: dark)` 或 JS 检测：

```js
const isDark = uni.getSystemInfoSync().theme === 'dark'
```

pages.json 中配置：

```json
{
  "globalStyle": {
    "darkmode": true,
    "themeLocation": "theme.json"
  }
}
```

theme.json 中定义亮/暗两套变量。
