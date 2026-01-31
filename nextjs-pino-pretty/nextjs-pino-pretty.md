# Next.js (App Router) で Pino-Pretty を使う方法

## TL;DR

Next.jsでpino-prettyの`transport`オプションを使うとworker threadsの問題でエラーになる。`transport`の代わりに`stream`を使えば解決する。

## 問題

Next.js 13以降のApp Routerで、pinoのtransportオプションを使ってpino-prettyを設定すると、以下のようなエラーが発生する：

```
Error: the worker thread exited
```

### なぜ起きるのか

pino-prettyの`transport`オプションは内部でworker threads`thread-stream`を使用する。Next.jsのバンドラー（SWC/webpack）がこれらのモジュールを正しく処理できず、ランタイムでエラーになる。

```javascript
// ❌ これはNext.jsで動かない
const logger = pino({
  transport: {
    target: 'pino-pretty',
    options: { colorize: true }
  }
})
```

next.config.jsの`serverExternalPackages`に追加する方法も提案されているが、ビルドは通っても実行時にモジュールが見つからないケースがある。

## 解決策

**transportではなくstreamを使う。** streamはworker threadsを使わないため、Next.jsでも問題なく動作する。

```javascript
import pino from "pino";
import pretty from "pino-pretty";

// ✅ streamを使えばNext.jsでも動く
const stream = pretty({
  colorize: true,
  ignore: "pid,hostname",
  translateTime: "SYS:HH:MM:ss.l",
});

const logger = pino({ level: "debug" }, stream);
```

## 参考

- [pino-pretty logger doesn't work with Next 13 app directory](https://github.com/vercel/next.js/discussions/46987)
- [Pino Bundling Documentation](https://getpino.io/#/docs/bundling)
