# 小魚乾點餐系統 · 共用核心（core）

這個 repo 只放**兩個站台共用的程式碼**，用 git submodule 掛在各站的 `core/`：

| 檔案 | 用途 |
|------|------|
| `shared.js` | 菜單預設值、共用樣式、Firestore 同步層、營運設定、測試層判斷 |
| `neworder-sound.js` | 新訂單提示音 |

## 誰在用

- [charliersa/Dried-Fish-Brunch](https://github.com/charliersa/Dried-Fish-Brunch) — 店內系統（顧客點餐／廚房／收銀／叫號／管理後台）
- [charliersa/dried-fish-brunch-order-app](https://github.com/charliersa/dried-fish-brunch-order-app) — 顧客手機點餐（QR 掃這個）

## 為什麼用 submodule

submodule 是**釘住某一個 commit**的。改這裡不會自動影響任何一站，
哪一站要升級就單獨把指標往前推，兩站與測試層互不干擾。

```bash
# 第一次抓下來
git clone --recursive https://github.com/charliersa/Dried-Fish-Brunch.git

# 已經 clone 過的
git submodule update --init --recursive

# 某一站要升級到最新 core
cd core && git pull origin main && cd ..
git add core && git commit -m "升級 core"
```

> GitHub 網頁上的「Download ZIP」**不含 submodule 內容**，一定要用 `git clone --recursive`。

## 各站可覆寫的設定

在各站自己的 `firebase-config.js` 裡（載入順序在 `core/shared.js` 之前）定義：

```js
// 覆寫餐點備註快捷選項（不定義就用 core 的預設）
const SITE_NOTE_OPTIONS = { drink: ['去冰', '少冰', '微糖'] /* … */ };
```

## 測試層

`shared.js` 偵測網址含 `/test/` 就自動切測試模式：Firestore 集合加 `test_` 前綴、
localStorage key 加前綴、推播端點清空、頁面頂端顯示「🧪 測試站」橫幅。
改版先在各站的 `test/` 驗證，打烊後再同步到正式站。
