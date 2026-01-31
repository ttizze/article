# Remixで動的サイトマップを作成する方法

Remixでは以下のパッケージを使ってサイトマップを作るのがメジャーなようだ。

[nasa-gcn/remix-seo: Collection of SEO utilities like sitemap, robots.txt, etc. for a Remix application. Forked from](https://github.com/nasa-gcn/remix-seo) [https://github.com/balavishnuvj/remix-seo](https://github.com/balavishnuvj/remix-seo)

しかし以下のissueのように、usageのままだとViteでエラーが出る。

[Can't use server-side code to get sitemap entries · Issue #17 · nasa-gcn/remix-seo](https://github.com/nasa-gcn/remix-seo/issues/17)

vite-env-onlyを使うと良いと書かれているが、実際にはこのままだとだめで、vite.config.tsを以下のように変更してから

```typescript
// vite.config.ts
import { defineConfig } from "vite"
import { envOnlyMacros } from "vite-env-only"
export default defineConfig({
  plugins: [envOnlyMacros()],
})
```

使用箇所で以下のようにすると良い

```typescript
import { serverOnly$ } from "vite-env-only/macros";
```
