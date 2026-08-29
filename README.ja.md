<p align="center">
  <img src="docs/app-icon.png" alt="CarCanvas" width="128" height="128" />
</p>

<h1 align="center">CarCanvas</h1>

<p align="center">
  <a href="README.md">English</a>
</p>

<p align="center">CarPlay の壁紙キャッシュを上書きして、自分の画像を CarPlay 壁紙として使うためのアプリです。</p>

CarPlayは起動時にあらかじめ作成したキャッシュファイルを読み込みます。このアプリは、そのファイルを自作のCPBitmapで置き換える仕組みです。

## できること

- 写真からローカル CPBitmap 壁紙を作成
- プレビュー付きの書き出し履歴
- CarPlay 壁紙キャッシュの上書き（Dark / Light を個別選択）
- アプリ内でキャッシュファイルの閲覧・管理
- 初回向けセットアップガイド

## インストール

IPAファイルは [Releases](https://github.com/Dcsyhi1998/CarCanvas/releases) からダウンロードしてください。

CarCanvas は **通常の方法ではインストールできません**。

このアプリは `com.apple.mobile.MobileHouseArrest` の identityを前提にしていて、他アプリのコンテナ（CarPlayWallpaper）にアクセスします。以下のようなインストール方法では動作しません。

- App Storeからのインストール
- Xcodeの個人／開発用証明書でのインストール
- **AltStore**（無料枠・有料枠問わず）でのサイドロード

**企業向け配布証明書（Enterprise / In-House）で IPAに署名してからインストールする必要があります。**
未署名のまま、または AltStore用に再署名しただけでは、必要な権限・identity が得られず、壁紙キャッシュへのアクセスはできません。

企業証明での署名・インストールには、**ESignな**どの無料アプリもあります。具体的な導入手順はツールや環境によって異なるので、**各自で調べてください**。このREADMEでは詳しいインストール手順には触れません。

一度CarCanvasを導入できれば、あとは自由に CarPlay の壁紙を変更できます。

## クレジット / 謝辞

このプロジェクトは、[0xjohnnydev/FilzaSlop](https://github.com/0xjohnnydev/FilzaSlop) のソースコードと調査に基づいて実現されました。FilzaSlopが示したMobileHouseArrest経由のコンテナアクセスや関連実装がなければ、CarCanvasのCarPlayWallpaperキャッシュ操作は成立しませんでした。感謝申し上げます。

> Special thanks to 0xjohnny for FilzaSlop and related research:
> https://github.com/0xjohnnydev/FilzaSlop

FilzaSlopは、FilzaJailedDS系のフォークで、アプリデータコンテナへのアクセスなどを提供しています。CarCanvasのIPAも同様に、`com.apple.mobile.MobileHouseArrest` の identity を前提にしています。

## 仕組み

CarPlayは壁紙を毎回その都度生成しているわけではありません。起動時に、あらかじめ作成した**キャッシュファイル**を読み込んでいます。

流れはこんな感じです。

1. 先にCarPlayで壁紙を選び、キャッシュファイルを作成します。
2. CarCanvasで自分の壁紙をCPBitmapとして保存します。
3. 履歴から、作成済みキャッシュへ同じファイル名で上書きします。
4. **iPhoneの電源を切って再起動します**（1回で大丈夫）。
5. 再起動後、CarPlayがキャッシュを読み直し、上書きした壁紙が表示されます。

キャッシュが無い状態では、上書き先がありません。**最初にCarPlayでキャッシュを作ることが必須**です。

上書きしただけではすぐには反映されません。CarPlayのプロセスがバックグラウンドで動き続け、古いキャッシュを持ったままになってしまいます。一定時間待てばプロセスは終了することもありますが、待ち時間は不明です。確実な方法は、iPhoneの電源を切って再起動することです。この方が確実なので、こちらの手順にしています。この再起動は、キャッシュをすべて上書きした後1回だけで十分です。

## 事前準備: CarPlayでキャッシュを作る

CarPlayの設定から**壁紙**を開き、上段・中段の**模様付き壁紙6枚**を左上から順に選びます。選ぶたびに、対応するDark / Lightのキャッシュファイルが作られます。

![CarPlay の壁紙選択画面](docs/carplay-wallpaper-picker.png)

| 順番 | 位置                   | 色           | キャッシュファイル                                                              |
| ---- | ---------------------- | ------------ | ------------------------------------------------------------------------------- |
| 1    | 左上                   | 青           | `CARWallpaperBlue-Dark-14.cpbitmap`<br>`CARWallpaperBlue-Light-14.cpbitmap`     |
| 2    | 上段中央               | 紫           | `CARWallpaperPurple-Dark-14.cpbitmap`<br>`CARWallpaperPurple-Light-14.cpbitmap` |
| 3    | 右上                   | グレイ       | `CARWallpaperGray-Dark-14.cpbitmap`<br>`CARWallpaperGray-Light-14.cpbitmap`     |
| 4    | 中段左                 | 緑           | `CARWallpaperGreen-Dark-14.cpbitmap`<br>`CARWallpaperGreen-Light-14.cpbitmap`   |
| 5    | 中段中央               | ブラウン     | `CARWallpaperBrown-Dark-14.cpbitmap`<br>`CARWallpaperBrown-Light-14.cpbitmap`   |
| 6    | 中段右                 | 赤           | `CARWallpaperRed-Dark-14.cpbitmap`<br>`CARWallpaperRed-Light-14.cpbitmap`       |
| —    | （追加で作られる場合） | 黒（模様）   | `CARWallpaperBlack-Dark-14.cpbitmap`<br>`CARWallpaperBlack-Light-14.cpbitmap`   |
| —    | 下段左                 | 黒（単色）   | **作られない**                                                                  |
| —    | 下段中央               | ダークグレイ | **作られない**                                                                  |
| —    | 下段右                 | 茶色         | **作られない**                                                                  |

下段の単色3枚（黒・ダークグレイ・茶色）は画像ではなく色として扱われます。選んでもキャッシュファイルは作られないので、上書き対象にはなりません。

模様付き6枚を一度ずつ選べば準備は完了です。この作業は、キャッシュが消えない限り繰り返す必要はありません。

ファイル名の末尾の`-14`は環境によって異なる場合があります。CarCanvasの書き出し画面では、実機にある名前がそのまま使われます。

## Dark と Light

模様付き壁紙は、どれも**Dark**と**Light**の2枚組です。

| ファイル              | 使われるとき                               |
| --------------------- | ------------------------------------------ |
| `*-Dark-14.cpbitmap`  | 夜、または外見がダークモードのとき         |
| `*-Light-14.cpbitmap` | 朝・明るいとき（外見がライトモードのとき） |

この切り替えのタイミングは、CarPlayの外見モードが**自動**のときだけです。常にダークにしている場合はDark側だけが使われ、Lightは読み込まれません。

同じ壁紙をDarkとLightの両方に設定すると、時間帯が変わっても同じ絵のままです。別々の画像を設定すると、**朝は壁紙A、夜は壁紙B**のように自動で切り替わります。

## CarCanvas での使い方

### 1. 自分の壁紙をローカルに保存する

1. CarCanvasを開く
2. **壁紙**を開く
3. 使いたい画像を選ぶ
4. **ローカルにCPBitmapを作成** を押します

画像はアプリ内にCPBitmapとして保存され、**履歴**に残ります。この時点ではまだCarPlayの表示は変わりません。

### 2. CarPlay のキャッシュへ書き出す

1. **履歴**を開く
2. 先ほど保存した項目をタップする
3. **CarPlay Wallpaperに書き出し**を選択
4. CarPlay側のキャッシュ一覧が出るので、上書きしたい Dark / Lightを選択
5. **上書き**を実行

履歴側のDark / Lightは、キャッシュ側のDark / Lightに対応づけて書き出されます。同じファイル名は常に上書きです。

同じ絵で良いなら、使いたい模様（青・紫・グレイなど）のDarkとLightの両方に、同じ履歴を書き出してください。朝と夜で絵を変えたいなら、Lightには壁紙A、Darkには壁紙Bを別々に書き出します。CarPlayの外見が自動なら、明るさに合わせて切り替わります。

### 3. iPhoneを再起動する（1回）

キャッシュを自分の壁紙で上書きし終わったら、**iPhoneの電源を切って、再起動**する必要があります。これを1度行えば、CarPlayのプロセスは確実に終了し、起動後に新しいキャッシュを読み込むはずです。

待っていればプロセスはいつか終了しますが、何分、何時間待てば良いかは分かっていません。
なので、電源を切る方が確実です。

## 補足

CarCanvas の **CarPlay Wallpaper** 画面では、上書き対象のキャッシュフォルダを直接確認できます。ファイルのインポート、名前変更、複製、削除なども修正できます。

## 関連

| 名前                                                      | 内容                                                         |
| --------------------------------------------------------- | ------------------------------------------------------------ |
| **CarCanvas**（このアプリ）                               | CarPlay 壁紙キャッシュの閲覧・上書き                         |
| **[FilzaSlop](https://github.com/0xjohnnydev/FilzaSlop)** | コンテナアクセス等のベースとなったオープンソース（0xjohnny） |

## 免責

CarCanvasは非公開のシステム挙動と特殊なidentityに依存しています。iOSやCarPlayの更新で動かなくなる可能性があります。自己責任でご利用ください。企業証明やサイドロードはAppleの規約や各環境のポリシーに抵触する場合があります。その判断と結果は利用者にあります。
