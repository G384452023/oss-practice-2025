# 第5回：Gitの基本操作（add と commit）

## この回で学ぶこと
この回では，Gitの基本的な操作である `git add` と `git commit` について学びます．
これらのコマンドを使うことで，自分の作業を記録し，バージョン管理ができるようになります．

## リポジトリの取得（git clone）
GitHub上の「Download ZIP」などでファイルをダウンロードすると，リポジトリの履歴やメタデータが入っている `.git` フォルダは含まれません．
そのため，履歴を取得したり他の人に変更を送ったり（push）するには，`git clone` でリポジトリをクローンしてください．

- HTTPSでクローンする場合（推奨）：
```bash
git clone https://github.com/tomohitoy/oss-practice-2025.git
```

クローンの後にこの章のファイルに移動する例：
```bash
cd oss-practice-2025/chap05
```

履歴を全部取らずに最新だけを取得する（帯域を節約したい場合）：
```bash
git clone --depth 1 https://github.com/tomohitoy/oss-practice-2025.git
```

注：Download ZIP は編集して戻す（push）ことができないため，変更をリポジトリに反映させたい場合は必ず `git clone` してください．

## Gitとは？
Gitは，ファイルの変更履歴を記録し，管理するためのツールです．
まるでゲームのセーブポイントのように，作業の節目でファイルの状態を保存できます．

## 基本的なワークフロー

### 1. 変更を確認する（`git status`）
まず，どのファイルが変更されているかを確認します．

```bash
git status
```

このコマンドを実行すると，以下のような情報が表示されます：
- **変更されたファイル**：どのファイルを編集したか
- **ステージングされていないファイル**：まだ `git add` していないファイル
- **コミット準備ができているファイル**：`git add` 済みでコミット待ちのファイル

### 2. 変更をステージングする（`git add`）
ファイルの変更をコミットする準備をします．これを「ステージング」と言います．

```bash
git add ファイル名
```

例：`PROFILE.md` をステージングする場合
```bash
git add PROFILE.md
```

すべての変更をまとめてステージングする場合：
```bash
git add .
```

**ステージングとは？**
ステージングは，写真を撮る前に「どの人を写真に含めるか」を決めるようなものです．
変更したファイルの中から，次のコミットに含めたいものだけを選びます．

### 3. 変更を記録する（`git commit`）
ステージングした変更を，履歴として記録します．

```bash
git commit -m "コミットメッセージ"
```

**`-m` オプション**について：
- `-m` は "message"（メッセージ）の略です
- このオプションを使うと，コマンドライン上で直接コミットメッセージを書けます
- メッセージには，「何を変更したか」を簡潔に書きます

例：
```bash
git commit -m "自己紹介を追加"
```

```bash
git commit -m "PROFILE.mdに趣味の情報を追加"
```

**良いコミットメッセージの書き方**：
- 何をしたかを明確に書く
- 日本語でも英語でもOK
- 短く，わかりやすく（1行程度）

### 次の警告が出たときには．．．

しばしば，以下の警告が出ることがあります．これは，`git` の **初期設定が完了していない** ということを意味していますので，指示の通りに初期設定を行いましょう．

```
Author identity unknown

*** Please tell me who you are.

Run

  git config --global user.email "you@example.com"
  git config --global user.name "Your Name"

to set your account's default identity.
Omit --global to set the identity only in this repository.
```

メールアドレスを設定する
```bash
git config --global user.email "あなたのメールアドレス"
```

名前を設定する
```bash
git config --global user.name "あなたの名前"
```

**登録を終えたらもう一度コミットにチャレンジしてください．**

## コミットの仕組みを理解しよう

Gitは，すべての変更履歴を `.git/objects` というフォルダに保存しています．
以下のコマンドを使って，Gitの内部の仕組みを見てみましょう．

### コミット履歴を見る

#### `git log --oneline`
コミット履歴を1行ずつ，簡潔に表示します．

```bash
git log --oneline
```

表示例：
```
7f9a8b2 自己紹介を追加
e4f5g6h 初回コミット
```

- 左側の文字列（`7f9a8b2`）は「コミットハッシュ」と呼ばれる，コミットを識別するIDです
- 右側はコミットメッセージです

#### `git log -1`
最新のコミット1件だけを詳しく表示します．

```bash
git log -1
```

表示例：
```
commit 7f9a8b2c3d4e5f6a7b8c9d0e1f2a3b4c5d6e7f8
Author: Your Name <your.email@example.com>
Date:   Mon Oct 26 10:00:00 2025 +0900

    自己紹介を追加
```

### Gitオブジェクトの中身を見る

#### `.git/objects` フォルダを確認する
Gitは，すべてのデータを `.git/objects` フォルダに保存しています．

```bash
ls .git/objects
```

このフォルダには，2文字のフォルダがたくさん並んでいます（例：`a1`, `b2`, `c3`など）．
これらのフォルダの中に，実際のコミットやファイルのデータが保存されています．

特定のフォルダの中身を見るには：
```bash
ls .git/objects/a1
```

#### `git cat-file` コマンド
コミットやファイルの中身を確認できます．

コミットの内容を表示：
```bash
git cat-file -p コミットハッシュ
```

例：
```bash
git cat-file -p 7f9a8b2
```

これを実行すると，以下のような情報が見られます：
- **tree**：そのコミット時点でのファイルツリー（フォルダ構造）
- **parent**：1つ前のコミット
- **author**：誰が変更したか
- **committer**：誰がコミットしたか
- **コミットメッセージ**：なぜ変更したか

オブジェクトの種類を確認：
```bash
git cat-file -t コミットハッシュ
```

結果は `commit`，`tree`，`blob`（ファイルの内容）のいずれかになります．

### 実習の流れ

1. `PROFILE.md` ファイルに自己紹介を書く
2. `git status` で変更を確認
3. `git add PROFILE.md` でステージング
4. `git status` で再度確認（ステージングされたことを確認）
5. `git commit -m "自己紹介を追加"` でコミット
6. `git log --oneline` で履歴を確認
7. `.git/objects` の中身を見て，Gitの仕組みを理解する

## まとめ

- **`git status`**：変更されたファイルを確認
- **`git add`**：変更をステージング（コミット準備）
- **`git commit -m "メッセージ"`**：変更を記録
- **`git log --oneline`**：コミット履歴を簡潔に表示
- **`git log -1`**：最新のコミットを詳しく表示
- **`ls .git/objects`**：Gitのデータ保存場所を確認
- **`git cat-file`**：コミットやファイルの中身を表示

これらのコマンドを使いこなせば，Gitの基本操作はバッチリです！
