---
theme: default
highlighter: shiki
colorSchema: light
fonts:
  mono: Fira Mono
lineNumbers: false
drawings:
  persist: true
title: Browser back experience and Next.js
remoteAssets: false
---

# Browser back experience <br>and Next.js

ブラウザバック体験で見るNext.js

---

# About

- name: Akifumi Sato
  - twitter: akfm_sato
  - github: AkifumiSato
  - zenn.dev: akfm
  - Frontend/Backend Engineer
  - book: 「Webアプリ開発で学ぶ Rust言語入門」
- Using Next.js
  - work
  - my site: https://akfm.dev/
    - on vercel

---
layout: message
---

Theme:

Browser back experience<br> and Next.js

---

<Title>Why browser back experience?</Title>

---
layout: sub-section
breadcrumb: Why browser back experience?
---

# Importance of the browser back experience

Browser back is one of the most important web browsing experiences for users.

- [7 Principles of Rich Web Applications](https://rauchg.com/2014/7-principles-of-rich-web-applications)
  - Translation: [リッチなWebアプリケーションのための7つの原則](https://yosuke-furukawa.hatenablog.com/entry/2014/11/14/141415)
  - Written by Mr. Rauch.
- **Don't break history, enhance it**
  - `Back should be quick; users don't expect data to have changed much.`
  - Scroll position and UI state would be better restored by browser back / forward.

<!--
ブラウザバックはWebブラウジングにおいて、非常に重要な機能の1つです。
このことは、以下の7つの原則においても言及されています。

- 7つの原則
  - Rauch氏著
- historyを壊すべきじゃない、historyを拡張しよう
  - `"戻る"のは素早く行われるべきだ。ユーザーは"戻る"事によってデータが変更されるような事は期待していない。`
  - スクロール位置やUI Stateはブラウザバック・フォワード時に復元されることが望ましい。
-->

---
layout: sub-section
breadcrumb: Why browser back experience?
---

# Importance of the browser back experience

However, most applications don't care about browser back / forward restoration.

- Scroll position and UI state is missed on some sites.
  - e.g. Accordion, tab ui, etc.
- Some users complained about these situations.
  - https://rentwi.hyuki.net/?1576010373357965312

<!--
しかしながら、多くのサイトではブラウザバック体験はそもそも気にされてない。

- スクロール位置やUI Stateが失われるサイトは多い。
- 実際、ユーザーからの不満も上がったりしてる
-->

---

<Title>Browser back and Next.js</Title>

<!--
Next.jsをブラウザバック観点から見てみます。
-->

---
layout: sub-section
breadcrumb: Browser back and Next.js<
---

# Important features in the browser back experience

IMO, Next.js would be better to implement the below features for the browser back experience.

- **Restoration of scroll position**
- **Restoration of UI state**
  - UI state sync when next.js router is changed
    - It would be better `next/router` is easy to integrate with a global state management system such as Redux, Recoil, etc.

<!--
Next.jsアプリでの快適なブラウザバック体験のためには、以下のような機能を実装されてることが望ましいと考えられます。

- スクロール位置復元
- Ui Stateの復元
  - Next.jsのルーティングとUI Stateの同期
    - `next/router`がRecoilやReduxなどの状態管理ライブラリと統合しやすい状態が望ましいと考えられます
-->

---
layout: sub-section
breadcrumb: Browser back and Next.js<
---

# Restoration of scroll position

Scroll restoration has been implemented already behind the experimental flag.

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

<!--
スクロール復元についてはすでに、experimentalで実装されています。
beta機能のappディレクトリでは未対応のため現状pagesでのみ対応されてます。
-->

---
layout: sub-section
breadcrumb: Browser back and Next.js<
---

# Restoration of scroll position

fix: Scroll restoration bug caused by idx reset to 0 on reload.

https://github.com/vercel/next.js/pull/36861

<div class="flex justify-center">
  <img src="/assets/nextjs-pr.png" class="h-60">
</div>

<!--
スクロール位置復元に関連して、内部的な履歴管理がインクリメンタルになってることに由来するバグの修正を行いました。
これにより、内部的な履歴管理はindexではなくhash値で管理されるようになりました。
-->

---
layout: sub-section
breadcrumb: Browser back and Next.js<
---

# Restoration of scroll position

詳細な解説はzennにまとめてあるので、そちらをご参照ください。

https://zenn.dev/akfm/articles/next-js-scroll-restore

<div class="flex justify-center">
  <img src="/assets/zenn-scroll.png" class="h-60">
</div>

---
layout: sub-section
breadcrumb: Browser back and Next.js<
---

# Restoration of UI state

Unfortunately, we cannot sync our UI state with `next/router` changed using public API.

- Next.js encapsulates `history.state`.
- In `next/router`, the history key is included in `history.state` but it is not opened.
  - Depends on internal implementation, but history state can be realized by referring to this.

<!--
Ui Stateについては、残念ながら現状`next/router`の変更と同期するための公開されたAPIはありません。

- Next.jsは`history.state`を完全に置き換えてしまいます
  - なので、基本自前での管理は不可能
  - 履歴と同期した状態を作るには`next/router`とうまく統合したいが...
- `next/router`は履歴のkeyを`history.state`に格納するが、これを公開してない
  - 内部実装に依存することになるが、これを参照することで一応ブラウザバック時の復元を実現できる
-->

---
layout: sub-section
breadcrumb: Browser back and Next.js<
---

# Restoration of UI state

We have created a new library, [recoil-sync-next](https://github.com/recruit-tech/recoil-sync-next)!(and I helped a little with that.)<br>This lib could be better to improve the browser back experience.

```tsx{|4-9}
export const counter = atom<number>({
  key: 'counterState',
  default: 0,
  effects: [
    syncEffect({
      storeKey: 'ui-state',
      refine: number(),
    }),
  ],
})
```

<!--
こうして作られたのが`recoil-sync-next`です。本当に微力ですが、開発に携わらせていただきました。このライブラリを使うと、簡単にブラウザバック時にUI Stateを復元することができます。
-->

---
layout: sub-section
breadcrumb: Browser back and Next.js<
---

# Restoration of UI state

これも、詳細な解説はzennにまとめてあるのでそちらをご参照ください。

https://zenn.dev/akfm/articles/recoi-sync-next

<div class="flex justify-center">
  <img src="/assets/zenn-history.png" class="h-60">
</div>

---

<Title>What we would like to Next.js<br> in the future</Title>

<!--
最後に、ブラウザバック体験から見てNext.jsに今後望むこと、についてです。
-->

---
layout: sub-section
breadcrumb: What we would like to Next.js in the future
---

# What we would like to from the browser back experience for Next.js

First of all, thanks for having `scrollRestoration`.

- It would be nice!
  - if the beta feature `app` directory could also support `scrollRestoration`.
  - `next/router` would expose the key as with the [Navigation API](https://developer.mozilla.org/en-US/docs/Web/API/Navigation_API).
  - History state management (`useNextState`?) implemented.

<!--
欲を言えば、以下があると嬉しいです。

- 現在ベータ機能の`app`ディレクトリでも同様の対応がされること
- NavigationAPI同様、`next/router`もkeyを公開すること
- 可能なら、`useNextState`のような`next/router`の変更と同期するような状態管理hooksを実装すること
-->
