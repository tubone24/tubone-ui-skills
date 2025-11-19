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
