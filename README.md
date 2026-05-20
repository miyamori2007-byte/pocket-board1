# 🎓 Pocket Board — キャンパスデジタル掲示板

## フォルダ構成

```
pocket-board/
├── index.html          ← 掲示板サイト本体
├── admin/
│   └── index.html      ← 管理画面
├── posts/
│   └── posts.json      ← お知らせデータ（ここを更新する）
├── netlify.toml        ← Netlify設定
└── README.md
```

---

## 🚀 公開手順（初回）

### STEP 1：GitHubにアップロード

1. [github.com](https://github.com) でアカウント作成（無料）
2. 右上「+」→「New repository」
3. Repository name: `pocket-board`、Public を選択、「Create repository」
4. [GitHub Desktop](https://desktop.github.com/) をダウンロード・インストール
5. GitHub Desktop で「Add an Existing Repository」→ このフォルダを選択
6. 「Publish repository」でGitHubにアップロード

### STEP 2：Netlifyで公開

1. [app.netlify.com](https://app.netlify.com) でアカウント作成（GitHub連携）
2. 「Add new site」→「Import an existing project」→「GitHub」
3. `pocket-board` リポジトリを選択
4. 設定はそのままで「Deploy site」をクリック
5. 数秒で URLが発行される（例: `https://pocket-board-abc.netlify.app`）

---

## ✏️ お知らせの更新方法

### 方法A：管理画面から更新（おすすめ）

1. サイトURL + `/admin/` にアクセス（例: `https://your-site.netlify.app/admin/`）
2. パスワード `pocketboard2025` でログイン ← **必ず変えてください**
3. フォームで追加・編集・削除
4. 「posts.json をダウンロード」ボタンを押す
5. ダウンロードした `posts.json` を GitHub Desktop でこのフォルダの `posts/` に上書き
6. GitHub Desktop で「Commit to main」→「Push origin」
7. **Netlifyが自動で更新される（30秒ほど）**

### 方法B：直接 posts.json を編集

`posts/posts.json` をテキストエディタで開き、直接編集してからGitHubにプッシュ。

---

## 🔐 パスワードの変更

`admin/index.html` の以下の行を変更：

```javascript
const ADMIN_PASSWORD = 'pocketboard2025'; // ← ここを変える
```

---

## 👥 複数人で更新する場合

GitHub の「Settings」→「Collaborators」→ メールアドレスで招待すると、
招待した人も GitHub Desktop でプッシュできるようになります。

---

## 📝 posts.json のフォーマット

```json
{
  "posts": [
    {
      "id": 1,
      "title": "タイトル",
      "category": "event",        ← "event" or "circle"
      "eyebrow": "バナー上部の小見出し",
      "description": "詳細説明",
      "org": "主催団体名",
      "deadline": "2025-06-05",   ← YYYY-MM-DD形式
      "date": "2025-05-01",       ← 投稿日
      "image": ""                 ← 画像URLがあれば入れる
    }
  ]
}
```
