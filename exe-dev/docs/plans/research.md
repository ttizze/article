# exe.dev 調査レポート

## exe.devとは

- 2025年12月にパブリックデベロッパープレビュー公開
- **David Crawshaw**（Tailscale元CTO・共同創業者）が開発
- 「AI時代に最適化されたVMホスティングサービス」

## 主な特徴

| 特徴 | 詳細 |
|------|------|
| 超高速起動 | 約2秒でVM起動 |
| 永続ディスク | SQLiteも使える（サーバーレスと違う） |
| 専用ドメイン | `vmname.exe.xyz`が自動付与 |
| HTTPS自動設定 | TLS証明書の自動発行・管理 |
| SSHベースAPI | `ssh exe.dev new`でVM作成 |
| AIエージェント内蔵 | Shelley、Claude、Codexがプリインストール |

## AIエージェント「Shelley」

- ポート9999で実行
- Webベース・モバイル対応
- exe.devのインフラを理解しており、サービス公開設定を自動実行
- Systemdでのデーモン化、ログ管理を自動処理
- Chromium Headless搭載でスクリーンショット確認可能

## 料金

| プラン | 料金 | VM数 | CPU | RAM | ディスク |
|--------|------|------|-----|-----|----------|
| Individual | $20/月 | 25 | 2 | 8GB | 25GB |
| Team | $25/月/ユーザー | 25 | 2 | 8GB | 25GB |
| Enterprise | $30/月/ユーザー | 30 | 2 | 16GB | 25GB |

現在アルファ段階で**無料トライアル**可能。

## 競合との比較

| 項目 | exe.dev | E2B | GitHub Codespaces |
|------|---------|-----|-------------------|
| 主な用途 | 永続VM + AIエージェント | AIサンドボックス | 開発環境 |
| 起動時間 | 約2秒 | 200ms未満 | 数秒 |
| 永続性 | あり | なし（最大24時間） | あり |
| 料金 | 月額定額 | 従量課金 | 従量課金 |
| AIエージェント | Shelley内蔵 | なし | Copilot連携 |

## ユースケース

1. AIエージェントの実行環境（サンドボックス）
2. プロトタイプ開発
3. 個人レベルの自動化ツール（cronジョブ）
4. Webhook処理
5. セルフホストGitHub Actions Runner
6. 隙間時間でのモバイル開発

## メリット

- `ssh exe.dev new`だけでVM起動
- 2秒で起動
- 永続ディスク（SQLite使える）
- AIエージェント内蔵
- HTTPS自動設定
- 定額制（月$20で25VM）
- モバイル対応

## デメリット・注意点

- まだアルファ版
- $20/月は試験用途には高いという声も
- 月100GB帯域制限
- パブリックIP未実装（本番環境には制限）

## 技術的背景

- Kata Containersベース
- crosvm派生の仮想マシン技術
- 実際のVM（コンテナではない）
- TUNデバイス作成可能
- Docker Compose対応

## 参考リンク

- [exe.dev 公式](https://exe.dev/)
- [公式ドキュメント](https://exe.dev/docs/what-is-exe)
- [公式ブログ](https://blog.exe.dev/meet-exe.dev)
- [Hacker News](https://news.ycombinator.com/item?id=46397609)
- [日本語解説（deeeet）](https://www.deeeet.com/writing/exe-dev)
