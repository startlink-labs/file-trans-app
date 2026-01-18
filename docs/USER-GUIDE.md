# StartConnect ユーザーガイド

## 目次

1. [はじめに](#はじめに)
2. [初期設定](#初期設定)
3. [ファイル移行](#ファイル移行)
4. [Chatter移行](#chatter移行)
5. [トラブルシューティング](#トラブルシューティング)

---

## はじめに

StartConnectは、SalesforceからHubSpotへデータを移行するためのデスクトップアプリケーションです。

### 対応機能

- **ファイル移行**: ContentVersionとContentDocumentLinkを使用したファイルのアップロードとノート作成
- **Chatter移行**: FeedItemとFeedCommentをHubSpotノートとして移行

---

## 初期設定

### 1. HubSpot認証

1. アプリを起動
2. 「HubSpotでログイン」ボタンをクリック
3. ブラウザが開き、HubSpot認証ページが表示されます
4. HubSpotアカウントでログイン
5. アクセス許可を承認
6. 自動的にアプリに戻ります

**注意**: 認証情報は安全にキーチェーン（macOS）またはWindows資格情報マネージャーに保存されます。

### 2. HubSpotオブジェクトの確認

- ヘッダー右上の「ℹ️」アイコンをクリックすると、利用可能なHubSpotオブジェクト一覧が表示されます
- 標準オブジェクト（コンタクト、会社、取引、チケット）とカスタムオブジェクトが表示されます

---

## ファイル移行

### 必要なファイル

#### 必須
- **ContentVersion.csv**: ファイル本体の情報
- **ContentDocumentLink.csv**: ファイルとレコードの関連付け情報

#### オプション
- **ContentVersionフォルダ**: VersionDataが空の場合にファイルを読み込むフォルダ

### ContentVersion.csv の必須カラム

```csv
Id,ContentDocumentId,PathOnClient,VersionData
068xxx,069xxx,sample.pdf,JVBERi0xLjQK...
```

- `Id`: ContentVersionのID
- `ContentDocumentId`: ContentDocumentのID
- `PathOnClient`: ファイル名（パス含む場合は最後のファイル名のみ使用）
- `VersionData`: Base64エンコードされたファイルデータ（オプション）

### ContentDocumentLink.csv の必須カラム

```csv
LinkedEntityId,ContentDocumentId
001xxx,069xxx
003xxx,069xxx
```

- `LinkedEntityId`: Salesforceレコード ID（先頭3文字でオブジェクトタイプを判定）
- `ContentDocumentId`: ContentDocumentのID

### 移行手順

#### ステップ1: ファイル選択

1. ダッシュボードから「ファイル移行」を選択
2. 「ContentVersion.csv」を選択
3. 「ContentDocumentLink.csv」を選択
4. （オプション）「ContentVersionフォルダ」を選択
5. 「オブジェクト分析」ボタンをクリック

#### ステップ2: マッピング設定

1. 検出されたSalesforceオブジェクトが表示されます
2. 各オブジェクトに対して以下を設定：
   - **HubSpotオブジェクト**: マッピング先のHubSpotオブジェクトを選択
   - **HubSpotプロパティ内部値**: SalesforceのIDが格納されているHubSpotプロパティ名

**デフォルトマッピング例**:
- `001` (Account) → `companies` / `salesforceaccountid`
- `003` (Contact) → `contacts` / `salesforcecontactid`
- `006` (Opportunity) → `deals` / `hs_salesforceopportunityid`
- `500` (Case) → `tickets` / `salesforcecaseid`

3. 「マッピング済のみ」トグルで表示を絞り込み可能
4. 「ファイル移行実行」ボタンをクリック

#### ステップ3: 実行確認

1. 確認ダイアログが表示されます
2. HubSpotポータルIDを入力（誤操作防止）
3. 「実行する」ボタンをクリック

#### ステップ4: 結果確認

1. 処理が完了すると結果サマリーが表示されます
2. オブジェクトごとに以下が表示：
   - 成功数
   - スキップ数（HubSpotにレコードが存在しない）
   - エラー数
   - アップロードされたファイル数
3. 「CSVダウンロード」ボタンで詳細結果をダウンロード

### 処理内容

1. CSVからマッピング対象レコードを抽出
2. HubSpotプロパティでレコード存在確認
3. VersionData（Base64）からファイルをアップロード
   - 既に同名ファイルが存在する場合はスキップ
   - ファイル名は `{元のファイル名}_{VersionId}.{拡張子}` 形式
4. HubSpotレコードにノートを作成し、ファイルを添付
5. 処理結果CSVを出力

---

## Chatter移行

### 必要なファイル

#### 必須
- **FeedItem.csv**: Chatter投稿の情報
- **FeedComment.csv**: Chatterコメントの情報

#### オプション
- **User.csv**: ユーザー情報（指定しない場合、投稿者はIDで表示）
- **ContentVersion.csv**: 添付ファイル情報
- **ContentDocumentLink.csv**: ファイルリンク情報
- **FeedAttachment.csv**: 古い形式の添付ファイル情報（FeedItemのみ）

### FeedItem.csv の必須カラム

```csv
Id,ParentId,Body,CreatedById,CreatedDate
0D5xxx,001xxx,投稿内容,005xxx,2024-01-01T00:00:00.000Z
```

- `Id`: FeedItemのID
- `ParentId`: 親レコードのID（先頭3文字でオブジェクトタイプを判定）
- `Body`: 投稿本文
- `CreatedById`: 投稿者のユーザーID
- `CreatedDate`: 投稿日時

### FeedComment.csv の必須カラム

```csv
Id,FeedItemId,CommentBody,CreatedById,CreatedDate,RelatedRecordId
0D7xxx,0D5xxx,コメント内容,005xxx,2024-01-01T00:00:00.000Z,069xxx
```

- `Id`: FeedCommentのID
- `FeedItemId`: 親FeedItemのID
- `CommentBody`: コメント本文
- `CreatedById`: コメント投稿者のユーザーID
- `CreatedDate`: コメント日時
- `RelatedRecordId`: 添付ファイルのContentDocumentId（オプション）

### User.csv の必須カラム（オプション）

```csv
Id,Name,Username
005xxx,山田太郎,yamada@example.com
```

### 移行手順

#### ステップ1: ファイル選択

1. ダッシュボードから「Chatter移行」を選択
2. 「FeedItem.csv」を選択
3. 「FeedComment.csv」を選択
4. （オプション）「User.csv」を選択
5. （オプション）「ContentVersion.csv」を選択
6. （オプション）「ContentDocumentLink.csv」を選択
7. （オプション）「FeedAttachment.csv」を選択
8. 「オブジェクト分析」ボタンをクリック

#### ステップ2: マッピング設定

1. FeedItemのParentIdから検出されたオブジェクトが表示されます
2. 各オブジェクトに対してマッピングを設定（ファイル移行と同様）
3. 「Chatter移行実行」ボタンをクリック

#### ステップ3: 実行確認

1. 確認ダイアログが表示されます
2. HubSpotポータルIDを入力
3. 「実行する」ボタンをクリック

#### ステップ4: 結果確認

1. 処理が完了すると結果サマリーが表示されます
2. オブジェクトごとに以下が表示：
   - 成功数
   - スキップ数
   - エラー数
   - ノート作成数
3. 「CSVダウンロード」ボタンで詳細結果をダウンロード

### 処理内容

1. FeedItemとFeedCommentをCSVから読み込み
2. HubSpotプロパティでレコード存在確認
3. FeedItemとコメントをHTML形式に整形
   - 投稿日時と投稿者を表示
   - コメントは古い順に表示
   - 添付ファイルがある場合はリンクを追加
4. 添付ファイルをHubSpotにアップロード
5. HubSpotレコードにノートを作成
6. 処理結果CSVを出力

### ノート形式

```html
Chatter投稿

[投稿日時] - [投稿者名]
[投稿本文]
📎 [添付ファイル名]

コメント (2件)

[コメント日時] - [コメント投稿者名]
[コメント本文]
📎 [添付ファイル名]

---
Salesforce FeedItem ID: 0D5xxx
```

---

## トラブルシューティング

### 認証エラー

**症状**: 「認証情報が見つかりません」エラー

**対処法**:
1. ヘッダー右上の「ログアウト」をクリック
2. 再度「HubSpotでログイン」を実行

### ファイルアップロードエラー

**症状**: 「ファイルアップロードに失敗しました」エラー

**原因**:
- VersionDataが空でContentVersionフォルダも指定されていない
- Base64デコードエラー
- ファイルサイズが大きすぎる

**対処法**:
1. ContentVersion.csvのVersionDataカラムを確認
2. VersionDataが空の場合、ContentVersionフォルダを指定
3. ファイルサイズを確認（HubSpotの制限: 最大512MB）

### レコードが見つからない

**症状**: 「HubSpotにレコードが存在しません」でスキップされる

**原因**:
- HubSpotプロパティ名が間違っている
- SalesforceのIDがHubSpotに存在しない

**対処法**:
1. マッピング設定でHubSpotプロパティ内部値を確認
2. HubSpotでプロパティ名を確認（設定 → プロパティ）
3. SalesforceのIDがHubSpotに正しく同期されているか確認

### CSVファイルエラー

**症状**: 「必須カラムがありません」エラー

**対処法**:
1. CSVファイルのヘッダー行を確認
2. 必須カラムが存在するか確認
3. カラム名のスペルを確認（大文字小文字を区別）

### 大きなファイルの処理

**推奨事項**:
- CSVファイルは最初の2~3行で構造を確認してから実行
- 大量のレコードがある場合は、オブジェクトごとに分割して実行
- 処理中はアプリを閉じないでください

### ログの確認

**macOS**:
```bash
~/Library/Logs/com.startlink.start-connect/
```

**Windows**:
```
%APPDATA%\com.startlink.start-connect\logs\
```

**Linux**:
```bash
~/.local/share/com.startlink.start-connect/logs/
```

---

## よくある質問

### Q: 同じファイルを再度アップロードするとどうなりますか？

A: 既に同名ファイルが存在する場合はスキップされ、既存のファイルIDを使用してノートが作成されます。

### Q: カスタムオブジェクトに対応していますか？

A: はい。HubSpotのカスタムオブジェクトにも対応しています。マッピング設定で選択可能です。

### Q: 処理を途中でキャンセルできますか？

A: いいえ。処理開始後はキャンセルできません。処理が完了するまでお待ちください。

### Q: 結果CSVには何が含まれますか？

A: 各レコードの処理結果（成功/スキップ/エラー）、HubSpotレコードID、HubSpotレコードURL、ファイル数などが含まれます。

### Q: トークンの有効期限はありますか？

A: はい。トークンは自動的にリフレッシュされますが、長期間使用しない場合は再認証が必要になることがあります。

---

## サポート

問題が発生した場合は、以下の情報を添えてお問い合わせください：

1. エラーメッセージ
2. 実行した操作
3. CSVファイルの最初の2~3行（個人情報を除く）
4. ログファイル

**お問い合わせ先**: [GitHub Issues](https://github.com/startlink-labs/start-connect/issues)
