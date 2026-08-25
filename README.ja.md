<p align="center">
  <img src="docs/app-icon.png" alt="CarCanvas" width="128" height="128" />
</p>

<h1 align="center">CarCanvas</h1>

<p align="center">
  <a href="README.md">English</a>
</p>

<p align="center">CarPlay の壁紙キャッシュを上書きして、自分の画像を CarPlay 壁紙として使うためのアプリです。</p>

CarPlay は起動時に、あらかじめ作っておいたキャッシュファイルを読み込みます。このアプリはそのファイルを、自分で作った CPBitmap で置き換えます。

## できること

- 写真からローカル CPBitmap 壁紙を作成
- プレビュー付きの書き出し履歴
- CarPlay 壁紙キャッシュの上書き（Dark / Light を個別選択可）
- アプリ内でキャッシュファイルの閲覧・管理
- 初回向けセットアップガイド

## インストール

IPA は [Releases](https://github.com/Dcsyhi1998/CarCanvas/releases) からダウンロードしてください。

CarCanvas は **通常の方法ではインストールできません**。

このアプリは `com.apple.mobile.MobileHouseArrest` の identity を前提に、他アプリ（CarPlayWallpaper）のコンテナへアクセスします。そのため次のようなインストールでは動きません。

- App Store からのインストール
- Xcode の個人／開発用証明書でのインストール
- **AltStore**（無料枠・有料枠を含む AltStore 系の証明書）でのサイドロード

**企業向け配布証明書（Enterprise / In-House）で IPA に署名してからインストールする必要があります。**  
未署名のまま、または AltStore 用に再署名しただけでは、必要な権限・identity が得られず、壁紙キャッシュへのアクセスはできません。

企業証明での署名・インストールには、**ESign** など無料で使えるアプリもあります。具体的な導入手順はツールや環境によって異なるため、**各自で調べてください**。この README では詳細なインストール手順は扱いません。

一度 CarCanvas を導入できれば、あとは自由に CarPlay の壁紙を変更できます。

## クレジット / 謝辞

このプロジェクトは、[0xjohnnydev/FilzaSlop](https://github.com/0xjohnnydev/FilzaSlop) のソースコードと調査をベースに実現できました。FilzaSlop が示した MobileHouseArrest 経由のコンテナアクセスや関連実装がなければ、CarCanvas の CarPlayWallpaper キャッシュ操作は成立しませんでした。感謝します。

> Special thanks to 0xjohnny for FilzaSlop and related research:  
> https://github.com/0xjohnnydev/FilzaSlop

FilzaSlop は FilzaJailedDS 系のフォークで、アプリデータコンテナへのアクセスなどを提供しています。CarCanvas の IPA も同様に、`com.apple.mobile.MobileHouseArrest` の identity を前提にしています。

## 仕組み

CarPlay は壁紙をその場で毎回生成しているわけではありません。起動時に、あらかじめ作っておいた **キャッシュファイル** を読み込みます。

流れはこうです。

1. 先に CarPlay 側で壁紙を選んで、キャッシュファイルを作らせる
2. CarCanvas で自分の壁紙を CPBitmap として保存する
3. 履歴から、作成済みキャッシュへ同じファイル名で上書きする
4. **iPhone の電源を切って再起動する**（1 回でよい）
5. 再起動後、CarPlay がキャッシュを読み直し、上書きした壁紙が表示される

キャッシュが無い状態では、上書き先がありません。**最初に CarPlay でキャッシュを作ることが必須**です。

上書きしただけでは、すぐには反映されません。CarPlay のプロセスがバックグラウンドで生き続け、古いキャッシュを持ったままになるためです。一定時間待てばプロセスは終了することもありますが、待ち時間は不明です。確実な方法は、iPhone の電源を切って起動し直すことです。電源を落とせばプロセスは必ず終わるので、こちらの手順にしています。この再起動は、キャッシュをすべて上書きしたあと **1 回だけ** で十分です。

## 事前準備: CarPlay でキャッシュを作る

CarPlay の設定から **壁紙** を開き、上段・中段の **模様付き壁紙 6 枚** を左上から順に選びます。選ぶたびに、対応する Dark / Light のキャッシュファイルが作られます。

![CarPlay の壁紙選択画面](docs/carplay-wallpaper-picker.png)

| 順番 | 位置 | 色 | キャッシュファイル |
|------|------|----|--------------------|
| 1 | 左上 | 青 | `CARWallpaperBlue-Dark-14.cpbitmap`<br>`CARWallpaperBlue-Light-14.cpbitmap` |
| 2 | 上段中央 | 紫 | `CARWallpaperPurple-Dark-14.cpbitmap`<br>`CARWallpaperPurple-Light-14.cpbitmap` |
| 3 | 右上 | グレイ | `CARWallpaperGray-Dark-14.cpbitmap`<br>`CARWallpaperGray-Light-14.cpbitmap` |
| 4 | 中段左 | 緑 | `CARWallpaperGreen-Dark-14.cpbitmap`<br>`CARWallpaperGreen-Light-14.cpbitmap` |
| 5 | 中段中央 | ブラウン | `CARWallpaperBrown-Dark-14.cpbitmap`<br>`CARWallpaperBrown-Light-14.cpbitmap` |
| 6 | 中段右 | 赤 | `CARWallpaperRed-Dark-14.cpbitmap`<br>`CARWallpaperRed-Light-14.cpbitmap` |
| — | （追加で作られる場合） | 黒（模様） | `CARWallpaperBlack-Dark-14.cpbitmap`<br>`CARWallpaperBlack-Light-14.cpbitmap` |
| — | 下段左 | 黒（単色） | **作られない** |
| — | 下段中央 | ダークグレイ | **作られない** |
| — | 下段右 | 茶色 | **作られない** |

下段の単色 3 枚（黒 / ダークグレイ / 茶色）は画像ではなく **色** として扱われます。選んでもキャッシュファイルは作られないので、上書き対象にはなりません。

模様付き 6 枚を一度ずつ選べば準備は完了です。この作業は、キャッシュが消えない限り繰り返す必要はありません。

ファイル名末尾の `-14` は環境によって違うことがあります。CarCanvas の書き出し画面では、実機にある名前をそのまま使います。

## Dark と Light

模様付き壁紙は、どれも **Dark** と **Light** の 2 枚組です。

| ファイル | 使われるとき |
|----------|----------------|
| `*-Dark-14.cpbitmap` | 夜、または外見がダークのとき |
| `*-Light-14.cpbitmap` | 朝・明るいとき（外見がライトのとき） |

この切り替えが効くのは、CarPlay の外見モードが **自動** のときだけです。常にダークにしている場合は Dark 側だけが使われ、Light は読まれません。常にライトなら、逆に Light だけです。

同じ壁紙を Dark と Light の両方に入れれば、時間帯が変わっても同じ絵のままです。別々の画像を入れれば、**朝は壁紙 A、夜は壁紙 B** のように自動で切り替わります。

## CarCanvas での使い方

### 1. 自分の壁紙をローカルに保存する

1. CarCanvas を開く
2. **壁紙** を開く
3. 使いたい画像を選ぶ
4. **ローカルにCPBitmapを作成** を押す

画像はアプリ内に CPBitmap として保存され、**履歴** に残ります。この時点ではまだ CarPlay の表示は変わりません。

### 2. CarPlay のキャッシュへ書き出す

1. **履歴** を開く
2. 先ほど保存した項目をタップする
3. **CarPlay Wallpaper に書き出し** を選ぶ
4. CarPlay 側のキャッシュ一覧が出るので、上書きしたい Dark / Light を選ぶ
5. **上書き** を押す

履歴側の Dark / Light は、キャッシュ側の Dark / Light に対応づけて書き出されます。同じファイル名は常に上書きです。

同じ絵でよいなら、使いたい模様（青・紫・グレイなど）の Dark と Light の両方に、同じ履歴を書き出してください。朝と夜で絵を変えたいなら、Light には壁紙 A、Dark には壁紙 B を別々に書き出します。CarPlay の外見が自動なら、明るさに合わせて切り替わります。

上書きした直後は、CarPlay にすぐ反映されません。

### 3. iPhone を再起動する（1 回）

キャッシュを自分の壁紙で上書きし終わったら、**iPhone の電源を切って、もう一度起動**してください。これを 1 回やれば、CarPlay のプロセスが確実に終了し、起動後に新しいキャッシュを読みます。

待ってもプロセスはいつか終了しますが、何分待てばよいかは分かっていません。電源を切る方が確実です。

## うまくいかないとき

- **書き出し先が空、または見つからない**  
  模様付き 6 枚をまだ選んでいません。CarPlay の壁紙画面で青 → 紫 → グレイ → 緑 → ブラウン → 赤の順に選び直してください。
- **単色壁紙を選んでいる**  
  下段の黒・ダークグレイ・茶色はキャッシュを作りません。模様付きの 6 枚を使ってください。
- **上書きしたのに見た目が変わらない**  
  まだ iPhone を再起動していません。電源を切って起動し直してください。CarPlay を挿し直しただけ、アプリ内のプロセス再起動だけでは足りないことがあります。

## 補足

CarCanvas の **CarPlay Wallpaper** 画面では、上書き対象のキャッシュフォルダを直接確認できます。ファイルのインポート、名前変更、複製、削除もここから行えます。

## 関連

| 名前 | 内容 |
|------|------|
| **CarCanvas**（このアプリ） | CarPlay 壁紙キャッシュの閲覧・上書き |
| **[FilzaSlop](https://github.com/0xjohnnydev/FilzaSlop)** | コンテナアクセス等のベースとなったオープンソース（0xjohnny） |

## 免責

CarCanvas は非公開のシステム挙動と特殊な identity に依存します。iOS や CarPlay の更新で動かなくなる可能性があります。自己責任でご利用ください。企業証明やサイドロードは、Apple の規約や各環境のポリシーに抵触する場合があります。その判断と結果は利用者にあります。
