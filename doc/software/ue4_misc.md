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
