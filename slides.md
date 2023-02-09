---
theme: default
highlighter: shiki
colorSchema: light
fonts:
  mono: Fira Mono
lineNumbers: false
drawings:
  persist: true
title: ブラウザバック体験で見るNext.js
---

# ブラウザバック体験で見る<br>Next.js

Vercel Meetup #0

---

# 自己紹介

- name: 佐藤 昭文
  - twitter: akfm_sato
  - github: AkifumiSato
  - zenn: akfm
  - フロントエンド/サーバーサイドエンジニア
- Next.js
  - 仕事でもNext.jsのプロジェクトを担当
  - 自身のサイトなどもNext.js

---
layout: message
---

今日話したいこと:

Next.jsとブラウザバックの話

---

<Title>SPAとブラウザバック</Title>

---
layout: sub-section
breadcrumb: SPAとブラウザバック
---

# ブラウザバック体験の重要性

ブラウザバックはユーザーにとって、最も重要なWebブラウジング体験の1つ。

- 元ネタ: [リッチなWebアプリケーションのための7つの原則](https://yosuke-furukawa.hatenablog.com/entry/2014/11/14/141415)
  - 原著: [7 Principles of Rich Web Applications](https://rauchg.com/2014/7-principles-of-rich-web-applications)
  - rauchg氏による過去のブログ
- **Don't break history, enhance it**
  - 履歴は壊すべきじゃない
  - 「ユーザーは"戻る"事によってデータが変更されるような事は期待していない。」
  - スクロール位置やデータはhistoryに関連づけられ、復元されることが望ましい

---
layout: sub-section
breadcrumb: SPAとブラウザバック
---

# ブラウザバック体験の重要性

しかし、こういった原則を重視してるサイトは少ない...

- 多くのサイトではスクロール位置や状態が失われる
- ユーザーからの不平不満も存在する
  - https://rentwi.hyuki.net/?1576010373357965312

---

<Title>Next.jsとブラウザバック体験</Title>

---
layout: sub-section
breadcrumb: Next.jsとブラウザバック体験
---

# Next.jsのスクロール復元

experimental(実験的機能)で提供されてるappディレクトリはおそらく未対応？

```ts
// next.config.js
const nextConfig = {
  // ...
  experimental: {
    scrollRestoration: true,
  },
}

module.exports = nextConfig
```

---
layout: sub-section
breadcrumb: Next.jsとブラウザバック体験
---

# Next.jsのスクロール復元

TBW: バグ修正の話書きたい

---
layout: sub-section
breadcrumb: Next.jsとブラウザバック体験
---

# Next.jsのスクロール復元

詳細な解説はzennにまとめてあるので、そちらをご参照ください。

https://zenn.dev/akfm/articles/next-js-scroll-restore

<div class="flex justify-center">
  <img src="/assets/zenn-scroll.png" class="h-60">
</div>

---
layout: sub-section
breadcrumb: Next.jsとブラウザバック体験
---

# Next.jsの状態復元

TBW

---

# 構成

- 自己紹介(1m)
- ブラウザバック体験とSPA(2m)
- Next.jsはどうなのか(5m)
- スクロール周りのバグ修正(1m)
- 今後Next.jsに求めたいこと(1m)
