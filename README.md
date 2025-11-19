# tubone-ui-skills

Claude Code用のUI開発カスタムスキル集

## スキル一覧

### ui-component
UIコンポーネントの生成。既存パターンを踏襲し、実用的なコードを出力します。

### ui-review
UIコードのレビュー。品質、UX、アクセシビリティの観点から具体的な改善案を提示します。

### a11y-check
アクセシビリティチェック（WCAG 2.1 AA準拠）。Critical/High/Mediumの優先度で問題を指摘します。

## 使い方

### Claude Code上で
```
/skills ui-component
```

### インストール

1. `.claude/skills`ディレクトリにスキルをコピー
2. Claude Codeを再起動

または

```bash
git clone https://github.com/tubone24/tubone-ui-skills.git
cp -r tubone-ui-skills/.claude/skills/* /path/to/your/project/.claude/skills/
```

## 設計思想

「AIが作成したっぽくない」スキル設計：

- **簡潔さ**: 冗長な説明を避け、要点のみ
- **実用性**: 理論よりも具体的な実装パターン
- **率直さ**: 過度に丁寧すぎない自然な言葉遣い
- **具体性**: 抽象的な提案ではなく、コード例を提示

## ライセンス

MIT

## 参考

- [カスタムスキルの作成方法 - Claude](https://support.claude.com/ja/articles/12512198)
- [Anthropic Claude Skills Design - Zenn](https://zenn.dev/tmasuyama1114/articles/anthropic_claude_skills_design)
