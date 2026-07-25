# hbd — 誕生日AR カード 🎂

QRを読む → ブラウザでAR起動 → メッセージカードが開いて3段ケーキが飛び出し、キャラがぷかぷか揺れてウインクする、Webベースの誕生日ARです。

- **技術**: [A-Frame](https://aframe.io/) + [AR.js](https://ar-js-org.github.io/AR.js-Docs/)（アプリ不要・ブラウザだけで動く）
- **公開**: GitHub Pages（無料・HTTPS。カメラ利用にHTTPSが必須なので相性◎）

---

## 全体の流れ

```
[QRコード] --読む--> [GitHub PagesのURL] --開く--> [カメラ起動 & AR.js]
                                                        |
                                          カメラを「マーカー」に向ける
                                                        |
                                        カード開く → ケーキ飛び出す → キャラ登場&ウインク
```

> ⚠️ **重要**: QRコードは「サイトのURL」を持っているだけで、マーカーではありません。
> 3Dを表示する“目印”は別に必要です。まずは AR.js 標準の **Hiroマーカー** で全演出を確認し、
> 完成形（Phase 2）で好きな画像マーカーに差し替えます。

---

## Phase 1 — まず公開して実機で動かす（最重要）

### 1. GitHubにリポジトリを作って push

```bash
cd C:/local/hbd
git init
git add .
git commit -m "Initial birthday AR"
git branch -M main
# 事前に GitHub 上で hbd リポジトリを作成しておく（または gh を使用）
git remote add origin https://github.com/<あなたのユーザー名>/hbd.git
git push -u origin main
```

`gh` CLI が入っていれば、リポジトリ作成＋公開まで一気に:

```bash
cd C:/local/hbd
git init && git add . && git commit -m "Initial birthday AR"
gh repo create hbd --public --source=. --remote=origin --push
```

### 2. GitHub Pages を有効化

GitHubのリポジトリ → **Settings → Pages** →
Source を **Deploy from a branch**、Branch を **main / (root)** にして Save。

数十秒後、次のURLで公開されます:

```
https://<あなたのユーザー名>.github.io/hbd/
```

### 3. Hiroマーカーを用意して実機テスト

- このマーカー画像を印刷（または別画面に表示）: <https://raw.githubusercontent.com/AR-js-org/AR.js/master/data/images/hiro.png>
- スマホで上記URLを開く → カメラ許可 → **マーカーにカメラを向ける**
- カードが開いてケーキが飛び出せば成功 🎉

> うまく映らないときのチェック: HTTPSで開けているか / カメラ許可したか / 明るい場所か / マーカー全体が画面に入っているか。

### 4. QRコードを作る

公開URL（`https://<ユーザー名>.github.io/hbd/`）を、無料のQR生成サイト等でQR画像にするだけ。
カードにQRを印刷しておけば「読む→ARが開く」が完成します。

---

## Phase 2 — 好きな画像をマーカーにする（完成形）

Hiroの黒枠マーカーを、カードのイラストや写真に変えます。方法は2つ:

### 方法A: MindAR（おすすめ・きれいな画像トラッキング）
- <https://hiukim.github.io/mind-ar-js-doc/> の Image Tracking を使うと、任意の画像を高精度で目印にできます。
- A-Frame と併用でき、`<a-scene mindar-image=...>` に置き換える形。3Dコンテンツ（このリポの中身）はほぼ流用できます。

### 方法B: AR.js の NFT（自然特徴点トラッキング）
- <https://ar-js-org.github.io/AR.js-Docs/image-tracking/> 参照。
- 画像から `.fset / .fset3 / .iset` を生成し、`<a-nft url="...">` で読み込みます。

> どちらも「カードのデザイン＝マーカー」にできるので、QRとマーカーを1枚のカードに同居させられます。

---

## キャラクターの差し替え

`index.html` の `<a-entity id="character">` の中身が仮のキャラ（黄色い顔）です。差し替え方:

- **自作イラスト（2D）**: 透過PNGを `<a-image>` で貼る。ウインクは目パーツ差し替え or スプライトで表現。
- **3Dモデル（GLTF/GLB）**: `<a-gltf-model src="...">` に置き換え。モデルにウインク/動きのアニメclipが入っていれば `animation-mixer` で再生。

> ⚠️ **著作権に注意**: GitHub Pages は**公開サイト**です。有名キャラの流用は権利侵害のリスクがあります。
> 自作イラスト、または商用/再配布OKなライセンス素材を使ってください。

---

## ローカルで確認する

AR.js はカメラ＝HTTPS/localhost が必要です。ローカル確認は簡易サーバ経由で:

```bash
cd C:/local/hbd
python -m http.server 8000
# ブラウザで http://localhost:8000 を開く（PCのWebカメラでテスト可）
```

## 演出のタイミング調整

`index.html` の各 `animation__*` の `delay` / `dur` を変えると、
「カードが開く→ケーキ→キャラ→テキスト」の間合いを調整できます。
