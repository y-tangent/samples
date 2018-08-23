# ue4_ui

## HUD
- Widget Blueprintを作成
- Designerで部品を配置、プロパティ設定
  - 値の取得メソッドをbind
- レベルブループリント
  - CreateWidget
  - Add to Viewport
- Actorブループリント
  - Get All Widgets Of Class でクラス指定で取得し、UIに値を設定
    - UIではない場合はアクターにタグをつけて、Get All Actors with Tag で取得は可能
