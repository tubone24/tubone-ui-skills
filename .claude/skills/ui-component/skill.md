---
description: UIコンポーネントを生成する実用的なスキル
---

# UIコンポーネント生成

指定された要件に基づいてReact/Vue/Svelteコンポーネントを生成します。

## アプローチ

1. **プロジェクトの確認**
   - package.jsonでフレームワークとバージョンを確認
   - 既存のコンポーネントパターンを調査
   - スタイリング手法（CSS Modules、Tailwind、styled-componentsなど）を特定

2. **コンポーネント設計**
   - 単一責任の原則を守る
   - propsは最小限に
   - 型定義は明確に

3. **実装の原則**
   - 過度な抽象化を避ける
   - 必要最小限のpropsで開始
   - パフォーマンスは測定してから最適化
   - コメントは「なぜ」を説明（「何を」は書かない）

4. **スタイル**
   - プロジェクトの既存パターンに従う
   - レスポンシブ対応が必要な場合は明示的に確認
   - アクセシビリティは基本的なWCAG 2.1 AA準拠

## ダークモード対応

### カラーパレット（Tailwind CSS使用時）

```typescript
// ダークモードダッシュボードの推奨配色
const darkColors = {
  background: {
    primary: 'bg-slate-950',      // #020617 - メイン背景
    secondary: 'bg-slate-900',    // #0f172a - カード背景
    tertiary: 'bg-slate-800',     // #1e293b - ホバー/アクティブ
  },
  text: {
    primary: 'text-slate-50',     // #f8fafc - メインテキスト
    secondary: 'text-slate-400',  // #94a3b8 - サブテキスト
    muted: 'text-slate-500',      // #64748b - 補助テキスト
  },
  border: 'border-slate-800',     // #1e293b - 境界線
  accent: {
    blue: 'bg-blue-600',          // #2563eb - プライマリアクション
    green: 'bg-emerald-600',      // #059669 - 成功/ポジティブ
    red: 'bg-red-600',            // #dc2626 - 警告/エラー
    purple: 'bg-purple-600',      // #9333ea - セカンダリアクション
  }
};
```

### ダッシュボードコンポーネントパターン

#### メトリクスカード
```typescript
interface MetricCardProps {
  title: string;
  value: string | number;
  change?: number;
  icon?: React.ReactNode;
  trend?: 'up' | 'down';
}

const MetricCard: React.FC<MetricCardProps> = ({ title, value, change, icon, trend }) => (
  <div className="bg-slate-900 border border-slate-800 rounded-lg p-6">
    <div className="flex items-center justify-between">
      <div>
        <p className="text-slate-400 text-sm font-medium">{title}</p>
        <p className="text-slate-50 text-3xl font-bold mt-2">{value}</p>
        {change !== undefined && (
          <p className={`text-sm mt-2 ${trend === 'up' ? 'text-emerald-500' : 'text-red-500'}`}>
            {change > 0 ? '+' : ''}{change}%
          </p>
        )}
      </div>
      {icon && <div className="text-slate-400">{icon}</div>}
    </div>
  </div>
);
```

#### チャートコンテナ
```typescript
const ChartContainer: React.FC<{ title: string; children: React.ReactNode }> = ({ title, children }) => (
  <div className="bg-slate-900 border border-slate-800 rounded-lg p-6">
    <h3 className="text-slate-50 text-lg font-semibold mb-4">{title}</h3>
    <div className="bg-slate-950 rounded p-4">
      {children}
    </div>
  </div>
);
```

### ダークモード切り替え実装

```typescript
// Tailwind CSS のダークモード設定（tailwind.config.js）
module.exports = {
  darkMode: 'class', // または 'media'
  // ...
};

// Next.js + next-themes の例
import { ThemeProvider } from 'next-themes';

function App({ Component, pageProps }) {
  return (
    <ThemeProvider attribute="class" defaultTheme="dark">
      <Component {...pageProps} />
    </ThemeProvider>
  );
}

// ダークモード切り替えボタン
const ThemeToggle = () => {
  const { theme, setTheme } = useTheme();

  return (
    <button
      onClick={() => setTheme(theme === 'dark' ? 'light' : 'dark')}
      className="p-2 rounded-lg bg-slate-800 hover:bg-slate-700 text-slate-50"
      aria-label="テーマ切り替え"
    >
      {theme === 'dark' ? '🌙' : '☀️'}
    </button>
  );
};
```

### レスポンシブ対応

```typescript
// ダッシュボードグリッドレイアウト
<div className="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-4 gap-4">
  <MetricCard title="売上" value="¥1,234,567" change={12.5} trend="up" />
  <MetricCard title="訪問者" value="45,678" change={-3.2} trend="down" />
  {/* ... */}
</div>
```

### アクセシビリティ配慮

- テキストコントラスト比: 最低4.5:1（WCAG AA）
- フォーカスインジケーター: `focus:ring-2 focus:ring-blue-500 focus:outline-none`
- アイコンのみのボタン: aria-labelを必ず追加

## 出力形式

```typescript
// コンポーネントファイル
// テストファイル（必要に応じて）
// ストーリーファイル（Storybookがある場合）
```

## やらないこと

- 過度なエラーハンドリング（必要になってから追加）
- 憶測でのパフォーマンス最適化
- 使われないかもしれないpropsの先行実装
- 過剰なコメント

---

それでは、生成したいコンポーネントの詳細を教えてください。
