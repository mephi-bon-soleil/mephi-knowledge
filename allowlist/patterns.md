# pre-push hook パターン解説

グローバル pre-push hook（`hooks/pre-push`）で検知するパターンの意味・理由・運用知見。

---

## 検知パターン一覧

| パターン | 対象 | 備考 |
|---|---|---|
| `PRIVATE KEY` | 秘密鍵ファイルの内容 | PEM形式の秘密鍵 |
| `aws_access_key_id` | AWSアクセスキー | |
| `aws_secret_access_key` | AWSシークレットキー | |
| `ANTHROPIC_API_KEY` | Anthropic APIキー | |
| `OPENAI_API_KEY` | OpenAI APIキー | |
| `API_KEY\s*=` | 汎用APIキー代入 | 誤検知多め。要注意 |
| `SECRET_KEY\s*=` | 汎用シークレット代入 | |
| `password\s*=` | パスワード代入 | 誤検知多め。要注意 |
| `token\s*=` | トークン代入 | 誤検知多め。要注意 |
| `Bearer ...` | Bearerトークン | |
| `sk-[A-Za-z0-9]{20,}` | OpenAI形式のキー | |
| `ghp_[A-Za-z0-9]{36}` | GitHub Personal Access Token | |
| `gho_[A-Za-z0-9]{36}` | GitHub OAuth Token | |

---

## ファイル名検知パターン

`.env`, `.pem`, `.key`, `.p12`, `.pfx`, `.keystore`, `.credentials`, `.secret`

---

## 誤検知が多いパターンと対処

### `token\s*=`, `password\s*=`, `API_KEY\s*=`

設定ファイルや環境変数のプレースホルダーで頻繁に誤検知が出る。
実際のシークレット値ではなく変数参照・ダミー値の場合は `.gitallowed` に追加する。

```
# 例: .gitallowed への追記
token = {TOKEN}
password = {PASSWORD}
```

---

## hookを書く際の障壁と対処

### 「hookが自分自身を検知する」問題

hookのパターン定義・解説を含むファイルをコミットしようとすると、
hookが自分自身のパターン文字列に反応してブロックされる。

これが過去に `--no-verify` で逃げる習慣につながっていた。

**正しい対処：**
- hookのパターン定義・解説を含むリポジトリには、最初から包括的な `.gitallowed` を用意する
- `common.gitallowed` をコピーするだけでは足りない——そのリポジトリ固有の自己言及エントリが必要

**このリポジトリの `.gitallowed` がその実例。**

---

## 運用ルール

1. 誤検知が出たら `.gitallowed` に追加してコミット
2. `--no-verify` は**絶対に使わない**
3. 新リポジトリ作成時は `common.gitallowed` をコピーして `.gitallowed` として配置
4. hookスクリプトの正本はこのリポジトリ（`hooks/pre-push`）で管理
5. `~/.config/git/hooks/pre-push` はここへのシンボリックリンクにする（→セットアップ参照）

---

## セットアップ手順

```bash
# hookの正本をシンボリックリンクに切り替える
ln -sf ~/workspace/projects/mephi-knowledge/hooks/pre-push ~/.config/git/hooks/pre-push

# 新リポジトリへの .gitallowed 配置
cp ~/workspace/projects/mephi-knowledge/allowlist/common.gitallowed {repo}/.gitallowed
```
