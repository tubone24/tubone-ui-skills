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

### カラーパレット（FinBroスタイル）

```typescript
// ダークモードダッシュボードの推奨配色（FinBro参考）
const darkColors = {
  background: {
    primary: 'bg-black',          // #000000 - メイン背景
    card: 'bg-[#0D0D0D]',         // #0D0D0D - カード背景
    secondary: 'bg-[#1A1A1A]',    // #1A1A1A - ホバー/セカンダリ
    tertiary: 'bg-[#2A2A2A]',     // #2A2A2A - アクティブ状態
  },
  text: {
    primary: 'text-white',        // #FFFFFF - メインテキスト (21:1)
    secondary: 'text-[#E7E7E7]',  // #E7E7E7 - プライマリ (13.5:1)
    muted: 'text-[#919191]',      // #919191 - セカンダリ/ミュート (5.8:1)
    disabled: 'text-gray-400',    // #9CA3AF - 無効状態
  },
  border: {
    primary: 'border-[#1F1F1F]',  // #1F1F1F - メインボーダー
    secondary: 'border-[#333]',   // #333333 - セカンダリボーダー
  },
  accent: {
    primary: 'bg-[#86efac]',      // #86efac - メインアクセント（緑）
    success: 'text-[#4ADE80]',    // #4ADE80 - 成功/ポジティブ
    danger: 'text-[#F87171]',     // #F87171 - 警告/エラー
  }
};

// コントラスト比（WCAG基準）
// #0D0D0D vs #E7E7E7: 13.5:1 ✅ AAA
// #0D0D0D vs #919191: 5.8:1  ✅ AA
// #0D0D0D vs #86efac: 9.5:1  ✅ AAA
// #1A1A1A vs #E7E7E7: 11.8:1 ✅ AAA
// #1A1A1A vs #919191: 5.1:1  ✅ AA
```

### CSS変数ベースのテーマ管理（推奨）

OKLCHカラースペースを使用した柔軟なテーマ設定：

```css
/* globals.css */
@import 'tailwindcss';

:root {
  --background: oklch(1 0 0);
  --foreground: oklch(0.145 0 0);
  --card: oklch(1 0 0);
  --card-foreground: oklch(0.145 0 0);
  --primary: oklch(0.205 0 0);
  --primary-foreground: oklch(0.985 0 0);
  --muted: oklch(0.97 0 0);
  --muted-foreground: oklch(0.556 0 0);
  --accent: oklch(0.97 0 0);
  --border: oklch(0.922 0 0);
}

.dark {
  --background: oklch(0.145 0 0);      /* #0D0D0D相当 */
  --foreground: oklch(0.985 0 0);      /* #FFFFFF相当 */
  --card: oklch(0.145 0 0);
  --card-foreground: oklch(0.985 0 0);
  --primary: oklch(0.985 0 0);
  --primary-foreground: oklch(0.205 0 0);
  --muted: oklch(0.269 0 0);           /* #1A1A1A相当 */
  --muted-foreground: oklch(0.708 0 0); /* #919191相当 */
  --accent: oklch(0.269 0 0);
  --border: oklch(0.269 0 0);          /* #1F1F1F相当 */
}

@theme inline {
  --color-background: var(--background);
  --color-foreground: var(--foreground);
  --color-card: var(--card);
  --color-card-foreground: var(--card-foreground);
  /* ... */
}

@layer base {
  * {
    @apply border-border outline-ring/50;
  }
  body {
    @apply bg-background text-foreground;
  }
}
```

### ダッシュボードコンポーネントパターン

#### メトリクスカード（FinBroスタイル）
```typescript
"use client"

import { Wallet } from 'lucide-react'

interface MetricCardProps {
  title: string;
  value: string | number;
  change?: number;
  icon?: React.ReactNode;
  trend?: 'up' | 'down';
}

// シンプルなメトリクスカード
const MetricCard: React.FC<MetricCardProps> = ({ title, value, change, trend }) => (
  <div className="flex flex-col gap-1">
    <span className="text-[#919191] text-sm">{title}</span>
    <span className="text-2xl md:text-xl lg:text-2xl font-semibold text-white">
      {value}
    </span>
    {change !== undefined && (
      <span className={`text-sm ${trend === 'up' ? 'text-[#4ADE80]' : 'text-[#F87171]'}`}>
        {change > 0 ? '+' : ''}{change}%
      </span>
    )}
  </div>
);

// ダッシュボードメトリクスコンテナ
export function DashboardMetrics() {
  return (
    <div className="flex flex-col xl:flex-row gap-8 xl:items-center justify-between p-6 bg-[#0D0D0D] rounded-2xl">
      <div className="flex flex-col gap-2">
        <div className="flex items-center gap-2 text-[#919191]">
          <Wallet className="h-5 w-5" />
          <span className="text-lg">Current</span>
        </div>
        <div className="text-5xl md:text-4xl lg:text-5xl font-bold text-white">
          $6,810
        </div>
      </div>

      <div className="grid grid-cols-2 md:grid-cols-4 gap-8 xl:gap-16">
        <MetricCard title="Invested" value="$5,220" />
        <MetricCard title="Total Returns" value="+$1,590" trend="up" />
        <MetricCard title="Net Returns" value="+30.46%" trend="up" />
        <MetricCard title="1 Day Returns" value="+$142.50" trend="up" />
      </div>
    </div>
  );
}
```

#### チャートコンテナ（FinBroスタイル）
```typescript
"use client"

import { Calendar, Download } from 'lucide-react'

interface ChartContainerProps {
  title: string;
  ticker?: string;
  children: React.ReactNode;
}

export function ChartContainer({ title, ticker, children }: ChartContainerProps) {
  return (
    <div className="flex flex-col gap-6 p-6 bg-[#0D0D0D] rounded-2xl">
      {/* ヘッダー */}
      <div className="flex flex-col sm:flex-row items-start sm:items-center justify-between gap-4">
        <div className="flex items-center gap-3">
          <h2 className="text-xl font-medium text-white">{title}</h2>
          {ticker && (
            <div className="flex items-center gap-2 px-3 py-1 bg-[#1A1A1A] rounded-full border border-[#333]">
              <div className="w-4 h-4 rounded-full bg-red-600 flex items-center justify-center">
                <span className="text-[10px] font-bold text-white">
                  {ticker[0]}
                </span>
              </div>
              <span className="text-sm font-medium text-white">{ticker}</span>
            </div>
          )}
        </div>

        {/* 時間範囲セレクター */}
        <div className="flex items-center gap-4">
          <div className="flex items-center bg-[#1A1A1A] rounded-lg p-1">
            {['1D', '1M', '3M', '6M', '1Y'].map((period) => (
              <button
                key={period}
                className={`px-3 py-1 text-sm rounded-md transition-colors ${
                  period === '6M'
                    ? 'bg-[#2A2A2A] text-white shadow-sm'
                    : 'text-[#919191] hover:text-white'
                }`}
                aria-label={`${period}のデータを表示`}
              >
                {period}
              </button>
            ))}
          </div>

          <div className="flex items-center gap-2">
            <button
              className="p-2 text-[#919191] hover:text-white bg-[#1A1A1A] rounded-lg transition-colors"
              aria-label="カレンダーを開く"
            >
              <Calendar className="h-5 w-5" />
            </button>
            <button
              className="p-2 text-[#919191] hover:text-white bg-[#1A1A1A] rounded-lg transition-colors"
              aria-label="データをダウンロード"
            >
              <Download className="h-5 w-5" />
            </button>
          </div>
        </div>
      </div>

      {/* チャートエリア */}
      <div className="h-[400px] w-full">
        {children}
      </div>
    </div>
  );
}
```

#### テーブルコンポーネント（FinBroスタイル）
```typescript
"use client"

import { ChevronsUpDown, ArrowUp, ArrowDown } from 'lucide-react'

interface TableRowData {
  id: string;
  name: string;
  qty: number;
  price: number;
  current: number;
  returns: number;
  trend: 'up' | 'down';
}

export function DataTable({ data }: { data: TableRowData[] }) {
  return (
    <div className="bg-[#0D0D0D] rounded-2xl p-6">
      <table className="w-full">
        <thead>
          <tr className="text-[#919191] text-sm border-b border-transparent">
            <th className="pb-4 text-left font-medium pl-2">
              <div className="flex items-center gap-1 cursor-pointer hover:text-white transition-colors">
                Company
                <ChevronsUpDown className="h-4 w-4" />
              </div>
            </th>
            <th className="pb-4 text-right font-medium">Qty.</th>
            <th className="pb-4 text-right font-medium">Mkt. Price</th>
            <th className="pb-4 text-right font-medium">Current</th>
            <th className="pb-4 text-right font-medium pr-2">Returns</th>
          </tr>
        </thead>
        <tbody>
          {data.map((item) => (
            <tr
              key={item.id}
              className="group hover:bg-[#1A1A1A] transition-colors border-b border-transparent last:border-0"
            >
              <td className="py-4 pl-2 rounded-l-xl">
                <span className="font-bold text-white">{item.name}</span>
              </td>
              <td className="py-4 text-right text-white font-medium">{item.qty}</td>
              <td className="py-4 text-right text-white font-medium">${item.price}</td>
              <td className={`py-4 text-right font-medium ${
                item.trend === 'up' ? 'text-[#4ADE80]' : 'text-[#F87171]'
              }`}>
                ${item.current}
              </td>
              <td className={`py-4 text-right font-medium pr-2 rounded-r-xl ${
                item.trend === 'up' ? 'text-[#4ADE80]' : 'text-[#F87171]'
              }`}>
                <div className="flex items-center justify-end gap-1">
                  {item.trend === 'up' ? (
                    <ArrowUp className="h-4 w-4" />
                  ) : (
                    <ArrowDown className="h-4 w-4" />
                  )}
                  {item.returns > 0 ? '+' : ''}{item.returns}%
                </div>
              </td>
            </tr>
          ))}
        </tbody>
      </table>
    </div>
  );
}
```

### ダークモード切り替え実装

```typescript
// tailwind.config.js - FinBroスタイルの設定
import type { Config } from "tailwindcss";

const config: Config = {
  darkMode: ["class"],
  content: [
    "./pages/**/*.{js,ts,jsx,tsx,mdx}",
    "./components/**/*.{js,ts,jsx,tsx,mdx}",
    "./app/**/*.{js,ts,jsx,tsx,mdx}",
  ],
  theme: {
    extend: {
      colors: {
        background: "hsl(var(--background))",
        foreground: "hsl(var(--foreground))",
        card: "hsl(var(--card))",
        // ...
      },
      borderRadius: {
        lg: "var(--radius)",
        md: "calc(var(--radius) - 2px)",
        sm: "calc(var(--radius) - 4px)",
      },
    },
  },
  plugins: [],
};

// app/layout.tsx - Next.js 14+ App Router
import type { Metadata } from 'next'
import { Space_Grotesk } from 'next/font/google'
import './globals.css'

const spaceGrotesk = Space_Grotesk({ subsets: ["latin"] });

export const metadata: Metadata = {
  title: 'Dashboard App',
  description: 'FinBro style dashboard',
  icons: {
    icon: [
      {
        url: '/icon-light.png',
        media: '(prefers-color-scheme: light)',
      },
      {
        url: '/icon-dark.png',
        media: '(prefers-color-scheme: dark)',
      },
    ],
  },
}

export default function RootLayout({
  children,
}: {
  children: React.ReactNode
}) {
  return (
    <html lang="ja" className="dark">
      <body className={`${spaceGrotesk.className} antialiased`}>
        {children}
      </body>
    </html>
  )
}
```

### レスポンシブ対応

```typescript
// ダッシュボードレイアウト（FinBroスタイル）
export default function Dashboard() {
  return (
    <div className="relative h-screen w-full bg-black text-white overflow-hidden">
      <Header />

      {/* スクロール可能なメインエリア */}
      <div className="h-full overflow-y-auto no-scrollbar">
        <main className="flex gap-6 p-6 pt-24 min-h-full">
          {/* サイドバー: mdサイズ以上で表示 */}
          <Sidebar />

          {/* メインコンテンツ */}
          <div className="flex-1 flex flex-col gap-6 min-w-0">
            <DashboardMetrics />
            <PerformanceChart />
            <TickerList />
          </div>
        </main>
      </div>
    </div>
  );
}

// グリッドレイアウトの例
<div className="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-4 gap-8 xl:gap-16">
  <MetricCard title="Invested" value="$5,220" />
  <MetricCard title="Total Returns" value="+$1,590" trend="up" />
  <MetricCard title="Net Returns" value="+30.46%" trend="up" />
  <MetricCard title="1 Day Returns" value="+$142.50" trend="up" />
</div>
```

### アクセシビリティ配慮（重要）

#### コントラスト比の検証
```typescript
// ✅ WCAG AAA準拠（推奨）
<div className="bg-[#0D0D0D] text-[#E7E7E7]">  {/* 13.5:1 */}
<div className="bg-[#1A1A1A] text-white">       {/* 15.8:1 */}

// ✅ WCAG AA準拠（最低基準）
<div className="bg-[#0D0D0D] text-[#919191]">  {/* 5.8:1 */}
<div className="bg-[#1A1A1A] text-[#919191]">  {/* 5.1:1 */}

// ❌ 不合格（避ける）
<div className="bg-[#0D0D0D] text-gray-600">   {/* 3.2:1 - AA不合格 */}
```

#### フォーカス管理
```typescript
// アウトラインの設定（globals.css）
@layer base {
  * {
    @apply border-border outline-ring/50;
  }
}

// ボタンコンポーネント
<button
  className="p-2 rounded-lg bg-[#1A1A1A] hover:bg-[#2A2A2A]
             text-[#919191] hover:text-white
             focus:outline-none focus:ring-2 focus:ring-white/20
             transition-colors"
  aria-label="設定を開く"
>
  <Settings className="h-5 w-5" />
</button>
```

#### スクリーンリーダー対応
```typescript
// アイコンのみのボタン: aria-labelは必須
<button aria-label="カレンダーを開く">
  <Calendar className="h-5 w-5" />
</button>

// インタラクティブな要素: 適切なセマンティックHTML
<button>クリック</button>  // ✅ Good
<div onClick={...}>クリック</div>  // ❌ Bad

// テーブル: thead/tbody/thを適切に使用
<table className="w-full">
  <thead>
    <tr>
      <th scope="col" className="text-left">Company</th>
      <th scope="col" className="text-right">Price</th>
    </tr>
  </thead>
  <tbody>
    {/* ... */}
  </tbody>
</table>
```

#### キーボードナビゲーション
```typescript
// ドロップダウンメニュー: Escキーで閉じる
import {
  DropdownMenu,
  DropdownMenuContent,
  DropdownMenuItem,
  DropdownMenuTrigger,
} from "@/components/ui/dropdown-menu"

<DropdownMenu>
  <DropdownMenuTrigger asChild>
    <button className="h-10 w-10 rounded-full bg-gradient-to-br from-pink-500 to-orange-400">
      {/* アバター */}
    </button>
  </DropdownMenuTrigger>
  <DropdownMenuContent align="end" className="w-48">
    <DropdownMenuItem>
      <Settings2 className="mr-2 h-4 w-4" />
      <span>Settings</span>
    </DropdownMenuItem>
  </DropdownMenuContent>
</DropdownMenu>
```

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
