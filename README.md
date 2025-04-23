# 🎬 映画情報アプリ（Vue.js + TMDB API）

## 📌 概要

Vue 3（Composition API）とTMDB APIを用いて構築したSPAです。  
人気映画の情報を一覧表示し、検索・ジャンル絞り込み・お気に入り登録・詳細表示などの機能を実装。  
保守性を意識したコンポーネント設計と、APIデータの加工・表示処理を組み合わせ、ユーザー体験にも配慮しています。

## 🛠 使用技術

- Vue.js 3 / Composition API
- Axios
- TMDB API
- SCSS（Sass）
- LocalStorage
- Git / GitHub

## 🔧 主な機能

- 映画の日本語タイトル・画像表示
- タイトル検索（リアルタイム対応）
- ジャンルによる絞り込み
- モーダルによる詳細表示
- お気に入りの登録・解除（ローカル保存）
- スマホ対応のレスポンシブUI
- コンポーネント分割による保守性向上

---

## ⚙️ Project setup

```bash
npm install

# movie-app

## Project setup
```
npm install
```

### Compiles and hot-reloads for development
```
npm run serve
```

### Compiles and minifies for production
```
npm run build
```

### Lints and fixes files
```
npm run lint
```

### Customize configuration
See [Configuration Reference](https://cli.vuejs.org/config/).
