# Next.js i18n Template

Next.js、next-intl、next-themesを使用した多言語・ダークモード対応のサーバーサイドレンダリング（SSR）テンプレート。

## 🚀 特徴

- **🌍 多言語対応**: next-intlによる完全な国際化サポート
- **🌓 ダークモード**: next-themesによるシステム連動のテーマ切り替え
- **⚡ サーバーサイドレンダリング**: Next.js 14のApp Routerを使用したSSR
- **📱 レスポンシブ**: Tailwind CSSによる完全レスポンシブデザイン
- **🎨 カスタマイズ可能**: 再利用可能なコンポーネントと設定

## 📦 セットアップ

### 1. インストール

```bash
# テンプレートをクローン
git clone [your-repo-url] my-project
cd my-project

# 依存関係をインストール
npm install
```

### 2. 環境変数の設定

`.env.example`をコピーして`.env.local`を作成：

```bash
cp .env.example .env.local
```

必要に応じて環境変数を編集：

```env
NEXT_PUBLIC_SITE_URL=https://your-domain.com
```

### 3. 開発サーバーの起動

```bash
npm run dev
```

[http://localhost:3000](http://localhost:3000)でアプリケーションが起動します。

## 🏗️ プロジェクト構造

```
src/
├── app/
│   └── [locale]/
│       ├── layout.tsx      # ルートレイアウト
│       ├── page.tsx        # ホームページ
│       ├── about/          # Aboutページ
│       └── example/        # サンプルページ
├── components/
│   ├── layout/            # レイアウトコンポーネント
│   └── ui/                # UIコンポーネント
├── constants/
│   ├── metadata.ts        # メタデータ設定
│   └── styles.ts          # 共通スタイル定数
├── i18n/
│   └── routing.ts         # 言語ルーティング設定
├── messages/              # 翻訳ファイル
│   ├── en.json
│   └── ja.json
└── hooks/                 # カスタムフック
```

## 🌍 多言語対応

### 新しい言語の追加

1. `src/i18n/routing.ts`に言語コードを追加：

```typescript
export const routing = defineRouting({
  locales: ['en', 'ja', 'ko'], // 韓国語を追加
  defaultLocale: 'ja',
});
```

2. `src/messages/`に翻訳ファイルを追加（例：`ko.json`）

3. `src/constants/metadata.ts`に言語別のメタデータを追加

### 翻訳の使用方法

```tsx
import { useTranslations } from 'next-intl';

export default function Component() {
  const t = useTranslations('HomePage');
  return <h1>{t('title')}</h1>;
}
```

## 🎨 スタイルのカスタマイズ

### 共通スタイルの使用

`src/constants/styles.ts`から共通スタイルをインポート：

```tsx
import { PROSE_STYLES, CONTAINER_STYLES } from '@/constants/styles';

// 使用例
<div className={CONTAINER_STYLES.withPadding}>
  <article className={`prose ${PROSE_STYLES}`}>
    {/* コンテンツ */}
  </article>
</div>
```

### ダークモードの対応

Tailwind CSSのダークモードクラスを使用：

```tsx
<div className="bg-white dark:bg-gray-900 text-gray-900 dark:text-gray-100">
  {/* コンテンツ */}
</div>
```

## 📄 新しいページの追加

1. `src/app/[locale]/`に新しいフォルダを作成
2. `page.tsx`を作成：

```tsx
import { routing } from '@/i18n/routing';
import { setRequestLocale } from 'next-intl/server';
import PageLayout from '@/components/layout/PageLayout';
import { getPageMetadata } from '@/constants/metadata';
import type { Metadata } from 'next';

// SSR対応
export function generateStaticParams() {
  return routing.locales.map((locale) => ({ locale }));
}

// メタデータ生成
export async function generateMetadata({ params }: { params: Promise<{ locale: string }> }): Promise<Metadata> {
  const { locale } = await params;
  return getPageMetadata(locale as 'ja' | 'en', {
    title: 'ページタイトル',
    description: 'ページの説明',
  });
}

export default async function NewPage({ params }: { params: Promise<{ locale: string }> }) {
  const { locale } = await params;
  setRequestLocale(locale);
  
  return (
    <PageLayout title="ページタイトル">
      {/* ページコンテンツ */}
    </PageLayout>
  );
}
```

## 🚀 ビルドとデプロイ

### ビルド

```bash
npm run build
```

### プレビュー

```bash
npm run start
```

### デプロイ

Vercel、Netlify、またはその他のホスティングサービスにデプロイ可能です。

## 🛠️ カスタマイズのヒント

1. **メタデータ**: `src/constants/metadata.ts`でサイト全体の設定を管理
2. **フッター**: `src/components/layout/Footer.tsx`でリンクや情報を更新
3. **ヘッダー**: `src/components/layout/Header.tsx`でナビゲーションをカスタマイズ
4. **テーマ**: `tailwind.config.js`でカラーパレットをカスタマイズ

## 📝 ライセンス

MIT License

## 🤝 貢献

プルリクエストを歓迎します！
