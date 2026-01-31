# RemixからNext.jsに移行した話

このWebサイト『記事もコメントも自動で翻訳される多言語コミュニティ**Evame**』のメタフレームワークを**Remix**から**Next.js**に移行し、インフラも**Render**から**Vercel**へ移行しました。

2週間ほどかけての大掛かりな移行でしたが、結果には満足していて、「小〜中規模なサイトなら**Remix / React Router**、大規模なサイトなら**Next.js**を選ぶ価値が大いにある」という結論に至りました。

この記事では、**Remix**から**Next.js**への移行についてや、**Remix**と**Next.js**の個人的な比較について書きます。どっちを使うか悩んでいる人や、移行を考えている人はぜひ読んでください!

## はじめに：移行を考えた3つの理由

### 1. サイト方針の変更と規模の拡大

当初Evameは、青空文庫やプロジェクト・グーテンベルク、過去の仏典といったpublic domainのテキストに対訳を付与したくて始めました。しかし開発を進めるうちに、public domainのテキストだけではなくユーザが投稿した記事にも対訳を付与できるようにしたいと考えるようになりました。そもそもEvameを作り始めた目的は知識と物語の循環であり、そのためにはユーザーが記事を投稿できるシステムが必要不可欠だと考えたからです。この方針の変更によってサイトの規模が拡大し、必要な機能が増え、大きなエコシステムを持つNext.jsに惹かれるようになりました。

### 2. Remixの今後の不明瞭さ

2024/11/22、Remix v2はReact Router v7とマージされました。Remixは消え、React Routerとして続いていくのかと思っていましたが、Co-FounderがTwitterで、次はRemix v3になるという発言をしています。

名前の変更はエコシステム全体に変化を余儀なくさせる大きなものです。実際、別のプロジェクトでRemix v2からReact Router v7へアップデートしたのですが、Remix関連の各種ライブラリが使えなくなり、多くの対応が必要で結構な工数がかかりました。再度名前の変更で同様の対応を迫られる可能性もあり、そうすると今回アップデートしてもまた使えなくなるライブラリが多く出るかも、といった不安がありました。

### 3. React と Next.js の緊密化

ReactとNext.jsは、どんどん連携を深めており、Next.jsは他のフレームワークよりも先行して多くの技術を使用しています。

RSCがNext.jsでリリースされたのは2023年でもう2年近く経っているのに、まだRSCを使えるフレームワークがほとんどないことがその証明です。

今後も優先的に新機能を実装できるだろう点は大きなアドバンテージだと感じました。

## Remix / React Router と Next.js の比較

### Remixと比較したNext.jsのよいところ

#### 1. React 19がフルに活用できる

Remix / React Router v7では、route modulesのloader / actionでデータを扱うため、コンポーネントとデータ部分が分離しがちでした。Next.js（App Router）ではRSC (React Server Components)やServer Actions、useActionState等を活用することで、**コンポーネントとデータの距離が縮まり、開発生産性が向上**しました。また、**コロケーションに基づいたディレクトリ構成**がRemixより行いやすくなった点も開発生産性の向上に繋がりました。

#### 2. 豊富なエコシステム

Next.jsの場合、Sentry、Supabase、Stripleなど多くのサービスの公式例があり、周辺ライブラリも充実しています。next/fontやnext/imageも素晴らしく、これによってLight HouseのPerformanceが100点になり、Best Practicesも100点になりました。

移行前は異なるスコアだったので、エコシステムの力を大いに感じています。

#### 3. AIフレンドリー

ClaudeやGPTに、Remixで書いてと指示を出してもNext.jsのコードを吐いてくることがよくありました。ユーザが多い分、Next.jsの場合最初から使えるコードを出してくれる割合が増えたと感じます。これもAI時代の新たなエコシステムと呼べるかもしれません。

### Remixと比較したNext.jsのよくないところ

#### 1. ビルドが遅い

Remixはリアルタイム更新が速く、ローカル開発が非常に快適でした。Next.jsはファイルによってはビルドに数秒かかり、時にはリロード必須になるなど、**開発体験としてはストレス**があります。

#### 2. メンタルモデルの複雑さ

Remixのメンタルモデルは、loader→component→actionというシンプルでわかりやすい流れでした。

Next.jsの場合、RSC/Server Actions/revalidatePath/revalidateTag/next/dynamic/Request Memoizationなど様々な概念が登場します。最適化されたことでLighthouseのスコアは上がりましたが、体感ではRemixの時と速度に差はありません。ユーザとしては体感速ければどうでもいいので、勝手に速くなるようにしてほしいという感想です。

#### 3. 黒魔術

Remixの吐くhtmlは非常にシンプルでした。書いたものがそのまま出力されているという感じで、把握しやすかったです。

Next.jsはフレームワーク独自のロジックでclassNameやcanvasタグが差し込まれていたり、デバッグが複雑です。また、Edge Runtimeと絡んで、ローカルでは動くのに本番だと動かない問題にも遭遇しがちです。

## 移行ステップ

### 1. ライブラリ移行とDBスキーマ変更

最初にブランチを切ってcreate next し、ライブラリの移行から始めました。具体的には以下のライブラリを移行する必要がありました。

- remix-auth → auth.js
- react-i18next → next-intl

remix-auth → auth.jsの変更には、DBスキーマの対応とORMとして使っているPrismaのEdge Runtime対応が必要で、移行全体の中でもここが一番大変でした。

DBスキーマはauth.js側に合わせたスキーマを用意し、既存データをマイグレーションし、カスタマイズを減らすことでアップデート時のリスクを抑えました。

Edge Runtimeは、Prisma Accelerateはまだ事例が少なく、ローカルと本番の差異が大きそうで懸念があり、Drizleへの移行は自由度が高い一方で工数が大きいので、結果、**Neon + Prisma**を選択して最小限の工数に留めました。

### 2. Remix 向けコードを Next.js に書き換え

次に、既存のRemixのルーティングやloader / action部分を、Next.js App Routerでのサーバーコンポーネント/Server Actionsに置き換えました。

ここではAIが非常に役に立ちました。ただしuseActionStateやServer Actionはまだ完全には任せられないので自力で頑張る必要があります。

## RSC / Server Actions のTips

### 1. Server Actions に共通ロジックを持たせない工夫

すべてのServer Actionsが外部公開されるため、認証コードをいちいち書く必要があります。できるだけ認証ロジックをまとめた関数や、Middlewareで対応できないか検討しましょう。

### 2. RSC コンポーネントのテスト

RSCは現状テスト手法が確立されていないので、**ビジネスロジックや認証ロジックを分け**、Unitテスト可能にするのがおすすめです。

### 3. useActionState の型付け

useActionStateの返り値に型情報を付与する仕組みはまだ確立されていません。自前で定義するのが現状ベターです。

```typescript
export type ActionResponse<T = void, U = Record<string, unknown>> = {
	success: boolean;
	message?: string;
	data?: T;
	zodErrors?: typeToFlattenedError<U>["fieldErrors"];
};
```

### 4. form の型検証

Server Action内でバリデーションするといいです。個人的にはzodで以下のようにしています。

```typescript
const editPageContentSchema = z.object({
	slug: z.string().min(1),
	title: z.string().min(1).max(100),
	pageContent: z.string().min(1),
});

const parsedFormData = editPageContentSchema.safeParse({
	slug: formData.get("slug"),
	title: formData.get("title"),
	pageContent: formData.get("pageContent"),
});
```

## 結論：規模やチーム体制で選ぶ

### 小〜中規模のアプリ

- そこまで最新のReact機能を活用しなくても良ければ、**Remix / React Router**はシンプルで高速な開発体験を得やすい。メンタルモデルも明快。
- toBやSPAなら個人的には**Remix / React Router**。

### 中〜大規模 or エコシステムを活かしたい場合

- **Next.js**を選べばサービス・ライブラリとの連携がしやすく、将来的なReactのアップデートにも先行して対応できる。
- toCやSSRをしたいなら**Next.js**。

個人的には「リリースして伸びてきたらNext.jsに移行」というアプローチも十分アリだと思います。Remix/React Routerならcloudflareを使えばコストもかなり抑えられます。ただし、移行時はやはり工数も掛かるので、**将来的な拡張を見据えている場合**は最初からNext.jsを使う選択肢も強いです。

## おわりに

フレームワーク、DB、インフラの移行と大掛かりな変更だったのですが、重要な部分にテストコードを書いていたことや、DB周りのコードをCQRSで分離していたことで、認証周り以外は比較的スムーズでした。改めて、**コードの品質やアーキテクチャに投資することが大事**だと実感しています。

このサイト「Evame」は、書いた記事やコメントをAIが自動翻訳してくれる多言語コミュニティです。僕もこの移行記を日本語で書き、英語圏にシェアできるようになりました。

もし興味があれば、ぜひ好きな言語で記事を書いて、世界中に発信してみてください！

以上が、Remix / React Router v7からNext.jsへ移行した感想と比較、そして移行手順です。何か参考になる部分があれば嬉しいです！
