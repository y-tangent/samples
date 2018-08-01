# ar_vr

## ar

https://www.asukanet.co.jp/

## vr

HMD種類一覧
https://vron.jp/2016/11/27/post-529/


|カテゴリ|名前|dof|
|---|
|aa|bb|cc|


### Oculus Go

#### setup

- スマフォに Oculus アプリをインストール
- Oculusアカウントでログイン
- ブルートゥースで接続(シリアル番号は左側のバンドの裏側にQRコードの下)
- wifi設定
- アプリで設定完了後、Oculusで初期設定

#### Oculus Rooms

- 起動時は入ってなかったが自動でインストールされた
- ライブラリ -> Oculus Rooms
- アプリで名前検索をしてフレンド登録
- Oculus Rooms からフレンドを招待
- 受けた側は招待を受け Oculus Rooms に入る

#### ミラーリング(Oculus Go -> PC)

- 開発者モードに設定する
- PCでVysorを利用する
- 有線/無線 両方可

#### ストリーミング(PC -> Oculus Go)

- Bigscreen
  - 機能
    - PCのデスクトップ共有
  - インストール方法
    - PCで steam から Bigscreen をインストール
  - 使い方
    - Oculus Go で Room ID を入力する(Bigscreenクライアントは必要？)
- PLEX
  - 機能
    - PCにある音楽・動画をストリーミング再生する
  - インストール方法
    - Oculus Goに PLEX をインストール
    - PCに Plex Media Server をインストール
  - 使い方
    - Oculus Go で PLEXアプリ を起動しメディアファイルを選択する

##  wearable devices
触覚デバイス
fove
NEC 展示会

振動モータ ドライバチップ
数パターン
50 - 100 円
小型の振動モータ
ジグビ	UART

スマフォ装着して振動でよくないか？とりあえずiphoneアプリを作成する
https://qiita.com/FumihikoSHIROYAMA/items/a754f77c41b585c90329
TCP/IPだとLANで接続するのが面倒なのでブルートゥースが一番手っ取り早いか(Webサービスでもよいか)
OculusGo Androidアプリ -> iphone

http://unrealengine.hatenablog.com/entry/2018/05/04/232708
> UE4のバージョンはUE4.19以降が正式にOculus Goに対応したバージョンとなっている

OpenFrameworks

## UE4 Datasmith

UE4
https://area.autodesk.jp/column/tutorial/3dsmax-ue4-viz/01-whats-unrealstudio/
Unreal Studio - Datasmith
https://docs.unrealengine.com/en-US/Studio/Unreal-Datasmith/Datasmith
https://docs.unrealengine.com/en-us/Studio/Unreal-Datasmith/Datasmith-Supported-Software-and-File-Types

https://grabcad.com/library/tata-ace-with-tutorial-video-1
rootノード x軸 270度

Unity
CAD Importer Unity Proのライセンス Windows
pixyz
  https://blogs.unity3d.com/jp/2018/03/06/unity-partners-with-pixyz/

## UE4アプリのOculusGo動作確認方法
