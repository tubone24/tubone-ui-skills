---
description: アクセシビリティの実践的なチェック
---

# アクセシビリティチェック

WCAG 2.1 AA準拠を目指した実用的なチェックを行います。

## チェック対象

### キーボード操作（Level A）
```javascript
// ❌ 悪い例
<div onClick={handleClick}>クリック</div>

// ✅ 良い例
<button onClick={handleClick}>クリック</button>
```

- すべてのインタラクティブ要素にキーボードでアクセス可能か
- Tab順序は論理的か
- フォーカスインジケーターは視認可能か
- キーボードトラップはないか

### セマンティックHTML（Level A）
- 見出しレベルは適切か（h1→h2→h3の順）
- リストにはul/ol/dlを使用しているか
- フォームのlabelは適切に関連付けられているか
- ランドマークロール（main、nav、asideなど）は使用されているか

### ARIA（使いすぎない）
```javascript
// ❌ 不要なARIA
<button aria-label="送信">送信</button>

// ✅ 必要な場合のみ
<button aria-label="メニューを開く">
  <MenuIcon />
</button>
```

- ネイティブHTMLで代替できる場合はARIAを使わない
- aria-labelは視覚的なテキストがない場合のみ
- role属性は本当に必要か再考する

### 色とコントラスト（Level AA）
- テキストコントラスト比: 4.5:1以上（大きなテキストは3:1）
- 色だけで情報を伝えていないか
- エラー表示は色以外でも識別可能か

#### ダークモードでのコントラスト
ダークモードでは特に以下の点に注意:

```typescript
// ❌ 悪い例: コントラスト不足
<div className="dark:bg-slate-900 dark:text-slate-700">
  {/* コントラスト比: 2.8:1 - WCAG AA不合格 */}
</div>

// ✅ 良い例: 十分なコントラスト
<div className="dark:bg-slate-900 dark:text-slate-100">
  {/* コントラスト比: 12.6:1 - WCAG AAA合格 */}
</div>
```

**推奨コントラスト比（ダークモード）:**
- 通常テキスト: 最低4.5:1（推奨7:1以上）
- 大きなテキスト（18pt以上 or 14pt太字）: 最低3:1（推奨4.5:1以上）
- UIコンポーネント: 最低3:1
- グラフィック要素: 最低3:1

**ダークモード配色の検証:**
| 背景色 | テキスト色 | コントラスト比 | 評価 |
|--------|-----------|---------------|------|
| #020617 (slate-950) | #f8fafc (slate-50) | 19.7:1 | ✅ AAA |
| #0f172a (slate-900) | #e2e8f0 (slate-200) | 14.1:1 | ✅ AAA |
| #1e293b (slate-800) | #cbd5e1 (slate-300) | 9.8:1 | ✅ AAA |
| #334155 (slate-700) | #94a3b8 (slate-400) | 3.2:1 | ❌ AA不合格 |

**自動チェックツール:**
- Chrome DevTools の Lighthouse
- axe DevTools の Color Contrast Analyzer
- WebAIM の Contrast Checker
- Polypane の Visual Testing Tools

### 動的コンテンツ
- ローディング状態はスクリーンリーダーに通知されるか（aria-live）
- フォーカス管理は適切か（モーダル、ページ遷移）
- エラーメッセージはフォーカスされるか、aria-liveで通知されるか

## 実装パターン

### モーダル
```typescript
const Modal = ({ isOpen, onClose, children }) => {
  useEffect(() => {
    if (isOpen) {
      // フォーカストラップの実装
      // Escキーでクローズ
    }
  }, [isOpen]);

  return (
    <div
      role="dialog"
      aria-modal="true"
      aria-labelledby="modal-title"
    >
      {children}
    </div>
  );
};
```

### ライブリージョン
```typescript
<div aria-live="polite" aria-atomic="true">
  {/* 動的に更新されるステータスメッセージ */}
</div>
```

## チェック方法

1. **キーボードのみでの操作テスト**
2. **スクリーンリーダーでのテスト**（NVDA、VoiceOver）
3. **自動ツール**（axe DevTools、Lighthouse）
   - ただし自動ツールは30%程度しか検出できない
4. **実際のユーザーテスト**（可能であれば）

## 出力形式

### 問題の優先度
1. **Critical**: 機能が使えない（キーボードアクセス不可など）
2. **High**: WCAG AA違反（コントラスト不足など）
3. **Medium**: ベストプラクティス違反

具体的な修正コードを提示します。理論的な説明は最小限に。

---

チェックしたいコンポーネントやページを指定してください。
