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
ダークモードでは特に以下の点に注意（FinBroスタイル基準）:

```typescript
// ❌ 悪い例: コントラスト不足
<div className="bg-[#0D0D0D] text-gray-600">
  {/* コントラスト比: 3.2:1 - WCAG AA不合格 */}
</div>

<div className="bg-[#1A1A1A] text-gray-500">
  {/* コントラスト比: 3.8:1 - WCAG AA不合格 */}
</div>

// ✅ 良い例: 十分なコントラスト（FinBroパレット）
<div className="bg-[#0D0D0D] text-white">
  {/* コントラスト比: 18.2:1 - WCAG AAA合格 */}
</div>

<div className="bg-[#0D0D0D] text-[#E7E7E7]">
  {/* コントラスト比: 13.5:1 - WCAG AAA合格 */}
</div>

<div className="bg-[#0D0D0D] text-[#919191]">
  {/* コントラスト比: 5.8:1 - WCAG AA合格 */}
</div>

<div className="bg-[#1A1A1A] text-[#919191]">
  {/* コントラスト比: 5.1:1 - WCAG AA合格 */}
</div>

// ✅ アクセント色の使用
<span className="text-[#4ADE80]">+12.5%</span>
{/* bg-[#0D0D0D]との比: 9.2:1 - WCAG AAA */}

<span className="text-[#F87171]">-8.3%</span>
{/* bg-[#0D0D0D]との比: 5.9:1 - WCAG AA */}
```

**推奨コントラスト比（WCAG 2.1）:**
- 通常テキスト（14pt未満）: **最低4.5:1**（AA）/ **7:1以上**（AAA推奨）
- 大きなテキスト（18pt以上 or 14pt太字）: **最低3:1**（AA）/ **4.5:1以上**（AAA推奨）
- UIコンポーネント（ボタン、フォーム等）: **最低3:1**
- グラフィック要素（アイコン、チャート）: **最低3:1**

**FinBroダークモード配色の検証:**

| 背景色 | テキスト色 | コントラスト比 | WCAG評価 | 用途 |
|--------|-----------|--------------|----------|------|
| **#000000** (black) | **#FFFFFF** (white) | **21:1** | ✅ AAA | ページ背景 + メインテキスト |
| **#0D0D0D** | **#E7E7E7** | **13.5:1** | ✅ AAA | カード + プライマリテキスト |
| **#0D0D0D** | **#919191** | **5.8:1** | ✅ AA | カード + セカンダリテキスト |
| **#1A1A1A** | **#FFFFFF** (white) | **15.8:1** | ✅ AAA | ホバー背景 + メインテキスト |
| **#1A1A1A** | **#E7E7E7** | **11.8:1** | ✅ AAA | ホバー背景 + プライマリ |
| **#1A1A1A** | **#919191** | **5.1:1** | ✅ AA | ホバー背景 + セカンダリ |
| **#2A2A2A** | **#FFFFFF** (white) | **10.2:1** | ✅ AAA | アクティブ背景 + テキスト |
| **#0D0D0D** | **#4ADE80** (緑) | **9.2:1** | ✅ AAA | ポジティブ値（成功） |
| **#0D0D0D** | **#F87171** (赤) | **5.9:1** | ✅ AA | ネガティブ値（警告） |
| **#0D0D0D** | **#86efac** (緑明) | **9.5:1** | ✅ AAA | アクセント |
| **#0D0D0D** | **#9CA3AF** (gray-400) | **4.2:1** | ❌ **AA不合格** | 使用禁止 |

**自動チェックツール:**
1. **Chrome DevTools Lighthouse**
   - Performance、Accessibility、Best Practicesを一括チェック
   - コントラスト比の自動検出

2. **axe DevTools** (推奨)
   - より詳細なアクセシビリティ問題を検出
   - Color Contrast Analyzer内蔵

3. **WebAIM Contrast Checker**
   - https://webaim.org/resources/contrastchecker/
   - 手動での色の組み合わせ検証に最適

4. **Polypane** (有料)
   - 複数デバイス + ダークモードの同時プレビュー
   - コントラストの視覚的検証

5. **Accessible Colors**
   - https://accessible-colors.com/
   - コントラスト比を満たす代替色の提案

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
