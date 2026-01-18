# StartConnect

SalesforceからHubSpotへデータを移行するデスクトップアプリケーション

## 主な機能

- **ファイル移行**: ContentVersion/ContentDocumentLinkからファイルをアップロード
- **Chatter移行**: FeedItem/FeedCommentをHubSpotノートとして移行
- **安全な認証**: HubSpot OAuth 2.0（トークン自動更新）
- **クロスプラットフォーム**: macOS、Windows、Linux対応

## クイックスタート

### 1. インストール

[Releases](https://github.com/startlink-labs/start-connect/releases/latest)から、お使いのOSに対応したインストーラーをダウンロード：

- **macOS**: `.dmg` ファイル
- **Windows**: `.msi` または `.exe` ファイル
- **Linux**: `.AppImage` または `.deb` ファイル

### 2. HubSpot認証

1. アプリを起動
2. 「HubSpotでログイン」をクリック
3. ブラウザで認証を完了

### 3. データ移行

1. ダッシュボードから「ファイル移行」または「Chatter移行」を選択
2. 必要なCSVファイルを選択
3. オブジェクトマッピングを設定
4. 実行して結果を確認

## ドキュメント

- **[ユーザーガイド](./docs/USER-GUIDE.md)**: 詳細な使い方、トラブルシューティング
- **[開発者ガイド](./DEVELOPMENT.md)**: 開発環境のセットアップ、ビルド方法

## サポート

問題が発生した場合は、[Issues](https://github.com/startlink-labs/start-connect/issues)からお問い合わせください。

## ライセンス

Private - StartLink Inc.
