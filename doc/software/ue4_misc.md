# ue4_misc

## Blueprintの位置づけ

Unreal Editor

## alwei's blog
http://unrealengine.hatenablog.com/
https://www.slideshare.net/masahikonakamura50/presentations

### 第5回ue4ハンズオンセミナー

- ShotActor
  - Actor
    - initial Life Span
      - 初期値が`0`(forever)にも関わらず、アクターがエフェクト終了時に削除される。ParticleSystemが制御している？
      - ParticleSystemにAutoDestroyフラグがあるっぽい？
  - Component
    - ParticleSystem
      - Particles Template: P_Fire(StarterContent)
    - ProjectileMovement
      -  http://api.unrealengine.com/JPN/Engine/Components/Movement/
      - > ティック中の別のコンポーネントの位置を更新します
      - Initial Speed: 0 -> 2000
      - Max Speed: 0 -> 2000
    - Audio
      Sound: Explosion_Cue(StarterContent)
    - Sphere Collision
      - Collison Presets `OverlapAll`
- Input
  - Shot: Left Mouse Button
- PersonBP
  - SpawnActor ShotActor
    - SpawnTransform: GetActorTransform(self)
- EnemyCharactor
  - Skeletal Mesh
    - Skeletal Mesh: SK_Mannequin
    - Transform
      - Location Z: `-90`
      - Rotation Z: `-90`
    - Anim Class: ThirdPerson_AnimBP_C
  - CapsuleComponent
    - OnComponentBeginOverlap
      - Other Actor: cast to ShotActor
        - このキャストの意味がわからない、オーバーラップ対象のフィルタリングを目的としている？もちろんこれがなくても動く
    - SpawnEmitterAtLocation
      - Location: GetActorTransform(split)
      - Rotation: GetActorTransform(split)
    - SetLifeSpan
      - In Lifespan: 0.2
- AIController
  - NavMeshBoundsVolume 配置
  - BehaviorTreeアセット作成
    - New Blackboard(Asset)
      - Vector TargetPoint
    - New Task(Asset)
      - Variables
        - Blackboad Key Selector KeySelector
          - タスクが提供するプロパティ、ビヘイビアツリーからブラックボードキーを指定するため
      - EventGraph
        - Event Receive Execute
          - Set Blackboard Value as Vector
            - Key: KeySelector(Variables)
            - Value: GetActorLocation(GetPlayerCharacter())
          - Finish Execute
            - Success: True
    - Root
      - Blackboard Asset: MyBlackboard
      - D&D Sequence
        - BTTask_FindTarget
          - KeySelector: TargetPoint
        - Move To
          - MoveTo: TargetPoint
        - Wait
          - Wait: 5.0s
    - flow
      - Characterゲーム開始時にビヘイビアキック
      - ビヘイビアはプレイヤーの位置をブラックボードに記憶 -> 移動 -> Wait を行う
  - EventBeginPlayからRunBehaviorTreeコール
  - EnemyCharactorに本AIControllerを設定

## Scenario to ue4 data
- 1 concrete scenario 1 umap
- n concrete scenario

- 外部ツール
  - CUIでバッチ処理を行えるようにしたい
- コンストラクションスクリプト
  - https://togetter.com/li/868931
  - レベル配置のブループリント化をしたいだけ
- ゲーム開始時

### Construction Script
In Game ではなくコンストラクション中に動作するスクリプト

インスタンスをどのように配置するのか

## csv出力
Rama's Victory Blueprint Library
https://github.com/EverNewJoy/VictoryPlugin
リリースバイナリは以下の公式フォーラムのリンクに存在する

https://forums.unrealengine.com/development-discussion/blueprint-visual-scripting/4014-39-rama-s-extra-blueprint-nodes-for-you-as-a-plugin-no-c-required

- FileIO__SaveStringTextToFile
- FileIO__SaveStringArrayToFile

単発のcsv出力は可能だが、連続した追加書き込みには対応していなさそう。
プラグインのWriteFlagsを修正する必要がある。
https://forums.unrealengine.com/development-discussion/c-gameplay-programming/61058-append-when-writing-to-a-file
UnrealEngine-4.17.2-release\Engine\Source\Runtime\Core\Public\Misc\FileHelper.h

```
/**
 * Save a binary array to a file.
 */
static bool SaveArrayToFile(TArrayView<const uint8> Array, const TCHAR* Filename, IFileManager* FileManager=&IFileManager::Get(), uint32 WriteFlags = 0);

/**
 * Write the FString to a file.
 * Supports all combination of ANSI/Unicode files and platforms.
 */
static bool SaveStringToFile( const FString& String, const TCHAR* Filename, EEncodingOptions::Type EncodingOptions=EEncodingOptions::AutoDetect, IFileManager* FileManager=&IFileManager::Get(), uint32 WriteFlags = 0 );
```

UE4でファイルIOは面倒なのでUDP送信して受信側でファイル保存を行う
正確な加速度を得るにはAutowareでメッセージのタイムスタンプから計算する
csvファイル出力もAutowareのノード出力のリダイレクトでよいのでは。

## コリジョンの持ち方
スタティックメッシュ
スケルタルメッシュ
自動生成(詳細度)
カスタム設定

## Lighting

### DirectionalLight

> [Atmosphere Sun Light] にチェックを入れると、 Rotation モード (E) を使ってライトを回転させながら、太陽の位置を制御することができます。
方向でライトの位置が決定する
