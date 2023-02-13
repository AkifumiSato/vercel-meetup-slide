---
theme: default
highlighter: shiki
colorSchema: light
fonts:
  mono: Fira Mono
lineNumbers: false
drawings:
  persist: true
title: Browser-back experience and Next.js
remoteAssets: false
---

# Browser-back experience <br>and Next.js

ブラウザバック体験で見るNext.js

---

# About

- name: Akifumi Sato
  - twitter: akfm_sato
  - github: AkifumiSato
  - zenn.dev: akfm
  - Front-end/Backend Engineer
- Using Next.js
  - work
  - my site

---
layout: message
---

Theme:

Browser-back experience and Next.js

---

<Title>Why browser-back experience?</Title>

---
layout: sub-section
breadcrumb: Why browser-back experience?
---

# Importance of the browser-back Experience

Browser back is one of the most important web browsing experiences for users.

- [7 Principles of Rich Web Applications](https://rauchg.com/2014/7-principles-of-rich-web-applications)
  - Translation: [リッチなWebアプリケーションのための7つの原則](https://yosuke-furukawa.hatenablog.com/entry/2014/11/14/141415)
  - Written by Mr. Rauch.
- **Don't break history, enhance it**
  - `Back should be quick; users don't expect data to have changed much.`
  - Scroll position and state should be associated with history and restored.

---
layout: sub-section
breadcrumb: Why browser-back experience?
---

# Importance of the browser-back experience

But few sites focus on these principles...

- Many sites lose scroll position and state.
  - e.g. Accordion, tab ui, etc.
- Complaints from users also exist.
  - https://rentwi.hyuki.net/?1576010373357965312

---

<Title>Next.js and browser-back</Title>

---
layout: sub-section
breadcrumb: Next.js and browser-back
---

# Important features in the browser-back experience

Let's see what Next.js has to implement about two important features of the aforementioned browser-back experience

- **Restoration of scroll position**
- **Restoration of state**

---
layout: sub-section
breadcrumb: Next.js and browser-back
---

# Restoration of scroll position

Implemented with the experimental flag.

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
breadcrumb: Next.js and browser-back
---

# Restoration of scroll position

fix: Scroll restoration bug caused by idx reset to 0 on reload.

https://github.com/vercel/next.js/pull/36861

<div class="flex justify-center">
  <img src="/assets/nextjs-pr.png" class="h-60">
</div>

---
layout: sub-section
breadcrumb: Next.js and browser-back
---

# Restoration of scroll position

詳細な解説はzennにまとめてあるので、そちらをご参照ください。

https://zenn.dev/akfm/articles/next-js-scroll-restore

<div class="flex justify-center">
  <img src="/assets/zenn-scroll.png" class="h-60">
</div>

---
layout: sub-section
breadcrumb: Next.js and browser-back
---

# Restoration of state

Unfortunately, there is no way yet to save the history state using the public API.

- Next.js completely replaces `history.state`.
- Next.js router does not publish keys.
  - A hitstory key for Next.js's internals exists in `history.state`.
    - Depends on internal implementation, but history state can be realized by referring to this.

---
layout: sub-section
breadcrumb: Next.js and browser-back
---

# Restoration of state

We have created a new library, [recoil-sync-next](https://github.com/recruit-tech/recoil-sync-next)!(and I helped a little with that.)

```tsx{|4-7}
export const counter = atom<number>({
  key: 'counterState',
  default: 0,
  effects: [
    syncEffect({
      storeKey: 'ui-state',
      refine: number(),
    }
  )],
})
```

---
layout: sub-section
breadcrumb: Next.js and browser-back
---

# Restoration of state

これも、詳細な解説はzennにまとめてあるのでそちらをご参照ください。

https://zenn.dev/akfm/articles/recoi-sync-next

<div class="flex justify-center">
  <img src="/assets/zenn-history.png" class="h-60">
</div>

---

<Title>What we want Next.js in the future</Title>

---
layout: sub-section
breadcrumb: What we want Next.js in the future
---

# What we want from the browser-back experience for Next.js

First of all, thanks for having `scrollRestoration`.

- It would be nice!
  - if the beta feature `app` directory could also support `scrollRestoration`.
  - `next/router` would expose the key as with the [Navigation API](https://developer.mozilla.org/en-US/docs/Web/API/Navigation_API).
  - History state management (`useNextState`?) implemented.
