# ensave-web

家計簿アプリ **ensave** の公開用 Web サイト（静的サイト）。
App Store / Google Play の審査で必要になる、プライバシーポリシー・利用規約・
アカウント削除案内・サポート情報を公開するためのリポジトリ。

- ビルド不要（HTML / CSS のみ。npm も JavaScript も使っていない）
- GitHub Pages にそのまま置ける
- アプリ本体のリポジトリとは分離して運用する

---

## サイト構成

| URL | ファイル | 内容 |
|---|---|---|
| `/` | `index.html` | サービス名・コンセプト・各ページへの目次 |
| `/guide/` | `guide/index.html` | 使い方（予算・支出の記録・残高・カレンダー・自分と共有・設定） |
| `/privacy/` | `privacy/index.html` | プライバシーポリシー |
| `/terms/` | `terms/index.html` | 利用規約 |
| `/account-deletion/` | `account-deletion/index.html` | アカウント削除について |
| `/support/` | `support/index.html` | よくある質問・ペア共有・招待コード・不具合報告・お問い合わせ |

公開 URL：**https://ensave.github.io/**（GitHub Organization `ensave` のリポジトリ `ensave.github.io`）

> 公開文書の運営者表記は **「ensave運営者」**、連絡先は **ensave.support@gmail.com** に統一する（個人の実名・個人のメールアドレスは載せない）。

```
ensave-web/
  index.html
  guide/index.html
  privacy/index.html
  terms/index.html
  account-deletion/index.html
  support/index.html
  assets/
    css/style.css        ← 全ページ共通のスタイル
    images/              ← 画像を置く場所（現時点では空）
  .nojekyll              ← GitHub Pages の Jekyll 処理を無効化
  README.md
```

### リンクの書き方（重要）

このサイトはすべて**相対パス**で書いてある。ページを追加するときも同じルールを守ること。
（Organization のサイト `https://ensave.github.io/` ではルートが `/` だが、独自ドメインや
Project Pages へ移しても壊れないよう、絶対パス `/privacy/` などは使わない。）

| 置き場所 | CSS | 他ページへのリンク |
|---|---|---|
| ルート（`index.html`） | `assets/css/style.css` | `privacy/` |
| 1 階層下（`privacy/index.html` など） | `../assets/css/style.css` | `../terms/` / ホームは `../` |

---

## ローカルで確認する

ファイルをブラウザで直接開くだけでも確認できる（相対パスなので `file://` で動く）。
ディレクトリ URL（`/privacy/`）の挙動まで含めて確認したい場合は簡易サーバーを使う。

```bash
python -m http.server 8000
```

→ 表示されたアドレス（ポート 8000）をブラウザで開く。

---

## GitHub Pages で公開する手順

1. GitHub Organization `ensave` に **Public** リポジトリ `ensave.github.io` を作成する（README は追加しない）。
2. このリポジトリの commit の作成者を、個人名ではなく ensave の表記にする（Public なので作成者名・メールは公開される）。
   メールは ensave 専用 GitHub アカウントの noreply アドレスを使い、push も専用アカウントで行う
   （個人アカウントで push すると、リポジトリの Activity などに個人アカウント名が出ることがある）。

   ```bash
   git init
   git config user.name "ensave"
   git config user.email "<ensave 専用アカウントの noreply アドレス>"
   git add .
   git commit -m "feat: ensave 公開用サイトを作成"
   git branch -M main
   git remote add origin https://github.com/ensave/ensave.github.io.git
   git push -u origin main
   ```

3. GitHub のリポジトリページで **Settings** → 左メニューの **Pages** を開く。
4. **Build and deployment** の **Source** を **Deploy from a branch** にする。
5. **Branch** で `main` / `/ (root)` を選び **Save**。
6. 1〜2 分待つと Pages の画面上部に公開 URL が表示される：`https://ensave.github.io/`
7. 各ページが開けることを確認する。
   - `https://ensave.github.io/privacy/`
   - `https://ensave.github.io/terms/`
   - `https://ensave.github.io/account-deletion/`
   - `https://ensave.github.io/support/`
   - **ログアウト状態（シークレットウィンドウ）でも開けること**を必ず確認する。
     ストア審査ではログイン不要で閲覧できる必要がある。

以後は `main` に push するたびに自動で再デプロイされる。

---

## 本文を更新するとき

- 章タイトルは `<h2>`、節タイトルは `<h3>`（見出し階層を飛ばさない）
- 段落は `<p>`、箇条書きは `<ul><li>` / `<ol><li>`
- 値が決まっていない箇所を、仮の値で埋めて公開しない。決まるまでは手元で作業し、push しない
- 本文を変えたら、各ページ冒頭の「最終更新日」も更新する
- 削除・ペア解除・招待コード・Apple/Google 連携・Analytics・Crashlytics の説明は、4ページで同じ事実関係にそろえる

### 公開前に置き換える値（2nd Release）

アプリの新しいバージョンの公開に合わせて、次の2つの値を**公開する直前に**置き換える。
置き換え忘れは画面にそのまま表示されるので、公開前に必ず検索して 0 件にする。

| 値 | 置き換える内容 | 例 |
|---|---|---|
| `{{RELEASE_VERSION}}` | アカウント削除の新しい道順や「新しいバージョン」の機能が使えるアプリのバージョン（App Store に出す正式な番号） | `1.1.0` |
| `{{PUBLISH_DATE}}` | そのページを公開する日（PR を作った日ではない） | `2026年11月10日` |

```bash
grep -rn "{{" --include=index.html .
```

→ 何も表示されなければ置き換え完了。

### push 前の最終チェック

作業用の印や下書きの文言、手元の確認用アドレスが残っていないことを、全ファイルを検索して確認する。
検索に使う語の一覧は、このリポジトリには書かない（手元の作業メモで管理する）。
あわせて、個人の実名・個人のメールアドレスが含まれていないことを目視で確認する
（このリポジトリは Public なので、確認用の検索語にも個人名を書かない）。

---

## 公開してはいけないもの

このリポジトリは Public 前提。以下は**絶対に置かない**。

- Firebase の秘密鍵・サービスアカウント・Admin SDK の情報
- API キー、アクセストークン、GitHub Token、パスワード
- `.env` の内容、開発環境の設定ファイル
- アプリ本体（`ensave`）の `GoogleService-Info.plist` / `google-services.json` などの設定ファイル

---

## 今後の拡張

- トップページを LP に拡張する（`index.html` のヒーロー下にセクションを追加）
- App Store / Google Play へのバッジリンク
- FAQ・お知らせページの追加（ディレクトリ + `index.html` を足すだけ）
- 独自ドメイン（Pages の設定 + `CNAME` ファイル。URL が変わるのでストア側の URL も更新する）
- お問い合わせフォーム（外部サービスを使う場合はプライバシーポリシーへの追記が必要）
