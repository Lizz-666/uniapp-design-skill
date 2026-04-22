# uniapp-design-skill

The design skill that makes your AI harness better at uni-app & mini-program development.

> **Quick start:** Copy this repo into your AI tool's skills directory. See [Installation](#installation) below.

## Why uniapp-design?

Every LLM was trained on Web code. Without guidance, you get the same predictable mistakes in uni-app projects: `div` instead of `view`, `rem` instead of `rpx`, CSS Grid that doesn't work, `@click` instead of `@tap`, Web Fonts that can't load, `document.querySelector` in a world without DOM.

uniapp-design fights that bias with:

- **10 iron rules** that prevent the most common AI mistakes
- **18 anti-patterns** with correct alternatives in a compact table
- **5 domain-specific reference files** loaded on demand to save tokens
- **Cross-platform guidance** for WeChat, Alipay, Baidu, ByteDance, QQ, DingTalk

## What's Included

### The Skill: uniapp-design

A comprehensive design & development skill with 5 domain-specific references:

| Reference | Covers |
|-----------|--------|
| [component-mapping](reference/component-mapping.md) | HTML → uni-app tags, event mapping, lifecycle hooks, Vue 2/3 differences |
| [css-compat](reference/css-compat.md) | rpx units, supported/unsupported CSS, selectors, alternatives |
| [design-tokens](reference/design-tokens.md) | Spacing scale, color system, typography, radius, shadows, SCSS templates |
| [platform-patterns](reference/platform-patterns.md) | Conditional compilation, platform differences, subpackaging, common pitfalls |
| [design-principles](reference/design-principles.md) | Mobile typography, touch-friendly layout, animation, empty states, forms |

### Iron Rules

10 rules the AI must always follow:

1. Use `rpx`, not `rem`/`vw`
2. Use `view`/`text`/`image`/`navigator`, not `div`/`span`/`img`/`a`
3. Use `@tap`, not `@click`/`onclick`
4. No CSS Grid — use nested Flexbox
5. No `oklch()`/`color-mix()`/`light-dark()` — use `hex`/`rgba`
6. No Web Fonts — use system font stack
7. No DOM operations — use `ref` + Vue reactivity
8. No `:has()`/`container queries`/`@layer`
9. No `*`/`body`/`html` selectors
10. Use conditional compilation for platform differences

### Anti-Patterns

18 common AI mistakes with correct alternatives:

| Wrong | Right | Why |
|-------|-------|-----|
| `<div>` / `<span>` | `<view>` / `<text>` | Mini-programs don't have HTML tags |
| `@click` | `@tap` | Mini-program events use tap |
| `display: grid` | Nested Flexbox | Grid not supported |
| `document.querySelector` | `ref` / `this.$refs` | No DOM in mini-programs |
| `vue-router` | `uni.navigateTo` | Use uni routing APIs |
| `localStorage` | `uni.setStorage` | Use uni storage APIs |
| ... and 12 more | | |

### Progressive Loading (Token Efficient)

The main `SKILL.md` (~140 lines) is always loaded. Reference files are loaded only when needed:

| Trigger | Loads |
|---------|-------|
| Writing components/pages | `component-mapping.md` |
| Writing styles/CSS | `css-compat.md` |
| Setting up design system | `design-tokens.md` |
| Handling platform differences | `platform-patterns.md` |
| Designing UI/layout | `design-principles.md` |

**Base cost:** ~800 tokens per conversation.  
**With one reference:** ~1600 tokens total.  
**Compare:** Loading everything = ~5500 tokens (saved ~75%).

## Installation

### OpenCode

```bash
cp -r uniapp-design-skill your-project/.opencode/skills/uniapp-design
```

### Cursor

```bash
cp -r uniapp-design-skill your-project/.cursor/skills/uniapp-design
```

### Claude Code

```bash
# Project-level
cp -r uniapp-design-skill your-project/.claude/skills/uniapp-design

# Global
cp -r uniapp-design-skill ~/.claude/skills/uniapp-design
```

### Gemini CLI

```bash
cp -r uniapp-design-skill your-project/.gemini/skills/uniapp-design
```

### Codex CLI

```bash
cp -r uniapp-design-skill ~/.codex/skills/uniapp-design
```

### Manual

Clone this repo and copy the entire folder into your AI tool's skills directory. The skill is plain Markdown — no build step required.

```bash
git clone https://github.com/Lizz-666/uniapp-design-skill.git
```

## Usage

Once installed, the skill activates automatically when the AI detects uni-app / mini-program context.

The AI will:

- Use `rpx` units by default
- Write `<view>` instead of `<div>`
- Use `@tap` instead of `@click`
- Avoid unsupported CSS features
- Handle cross-platform differences with conditional compilation

No special commands needed — the skill works as a persistent guide.

## What It Fixes

### Before (without uniapp-design)

```vue
<template>
  <div class="container">
    <h1>Welcome</h1>
    <p class="subtitle">Your order list</p>
    <a href="/pages/detail">View Details</a>
    <img src="/static/banner.png">
  </div>
</template>

<style>
.container {
  display: grid;
  grid-template-columns: repeat(3, 1fr);
  font-family: 'Inter', sans-serif;
}
h1 {
  color: oklch(0.7 0.15 250);
}
</style>
```

### After (with uniapp-design)

```vue
<template>
  <view class="container">
    <text class="title">Welcome</text>
    <text class="subtitle">Your order list</text>
    <navigator url="/pages/detail/detail">View Details</navigator>
    <image src="/static/banner.png" mode="aspectFill" />
  </view>
</template>

<style lang="scss" scoped>
.container {
  display: flex;
  flex-wrap: wrap;
  padding: $spacing-32;
}
.title {
  font-size: $font-size-3xl;
  font-weight: 600;
  color: $color-primary-500;
}
</style>
```

## Supported Platforms

| Platform | Variable |
|----------|----------|
| WeChat Mini Program | `MP-WEIXIN` |
| Alipay Mini Program | `MP-ALIPAY` |
| Baidu Mini Program | `MP-BAIDU` |
| ByteDance Mini Program | `MP-TOUTIAO` |
| QQ Mini Program | `MP-QQ` |
| DingTalk Mini Program | `MP-DINGTALK` |
| H5 | `H5` |
| App (iOS/Android) | `APP` |

## Supported AI Tools

- [OpenCode](https://opencode.ai)
- [Cursor](https://cursor.com)
- [Claude Code](https://claude.ai/code)
- [Gemini CLI](https://github.com/google-gemini/gemini-cli)
- [Codex CLI](https://github.com/openai/codex)
- Any tool that supports Markdown-based skills

## File Structure

```
uniapp-design/
├── SKILL.md                          # Main skill file (always loaded)
└── reference/
    ├── component-mapping.md          # Tags, events, lifecycle, Vue 2/3
    ├── css-compat.md                 # Supported CSS, alternatives, performance
    ├── design-tokens.md              # Spacing, color, typography, SCSS templates
    ├── platform-patterns.md          # Conditional compilation, subpackaging
    └── design-principles.md          # Mobile layout, touch, animation, forms
```

## License

MIT
