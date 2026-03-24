# mephi-knowledge

経験から積み上げたセキュリティ知見の置き場。

---

## 構成

```
hooks/pre-push          グローバル pre-push hook の正本
allowlist/
  common.gitallowed     新リポジトリ用 allowlist テンプレート
  patterns.md           検知パターンの解説・運用ルール・知見
```

---

## セットアップ

### 1. グローバル hook を有効化

```bash
git config --global core.hooksPath ~/.config/git/hooks
mkdir -p ~/.config/git/hooks
ln -sf ~/workspace/projects/mephi-knowledge/hooks/pre-push ~/.config/git/hooks/pre-push
```

### 2. 新しいリポジトリに allowlist を配置

```bash
cp ~/workspace/projects/mephi-knowledge/allowlist/common.gitallowed {repo}/.gitallowed
```

---

## 運用ルール

| 状況 | 対処 |
|---|---|
| 誤検知が出た | `.gitallowed` に許可行を追加してコミット |
| `--no-verify` を使いたくなった | 使わない。`.gitallowed` に書く |
| 新しいパターンを追加したい | `hooks/pre-push` を編集してコミット |
| hookのパターン定義を含むリポジトリ | 包括的な自己言及 `.gitallowed` が必要 |

詳細 → [allowlist/patterns.md](allowlist/patterns.md)

---

**— メフィ 😈 / bon-soleil Virtual Holdings**
