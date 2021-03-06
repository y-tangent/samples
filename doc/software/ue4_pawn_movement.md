# ue4 pawn Movement

## Method

http://unrealengine.hatenablog.com/entry/2017/06/14/005914
http://pickles-ochazuke.hatenablog.com/entry/2017/01/04/034720
http://katze.hatenablog.jp/entry/2015/07/02/005917
http://api.unrealengine.com/JPN/Gameplay/HowTo/CharacterMovement/Blueprints/

SetActorLocation系はワープ
Move to Location 系が移動
  Controller が必要
Add Input Vector

移動経路
- Target Actor
- Spline Component

## Car

- Pawn BP 作成
  - コンポーネントにSkeletalMesh,FloatingPawnMovementを持たせる
- ターゲットポイント配置
- レベルイベントグラフ
  - Move to Actor は FloatingPawnMovement では利用できない
  - Add Input Vector を利用する
    - ターゲットアクタとポーンから Get Unit Direction Vector でベクトルを生成
  - Event Tick をトリガに設定する
- SetActorRotationで向き設定(自動で行う設定があるはず)

- FloatingPawnMovement Parameter
  - Max Speed
  - Acceleration
  - Deceleration
  - Turning Boost

移動のターゲットポイントはCharacterBPのVariableに持たせる
レベル依存のものを持つ場合はレベルBPがよいが、変数として持たせてやればActorBPに持たせれる

http://katze.hatenablog.jp/entry/2016/10/26/184421
FloatingPawnMovementでSplineに沿って走りたい
- 移動距離を覚えているか？
  - 覚えていない。ただしTickComponentで単純に

```cpp:PawnMovementComponent.cpp
void UPawnMovementComponent::AddInputVector(FVector WorldAccel, bool bForce /*=false*/)
{
	if (PawnOwner)
	{
		PawnOwner->Internal_AddMovementInput(WorldAccel, bForce);
	}
}
```

```cpp:FloatingPawnMovement.cpp
// Move actor
FVector Delta = Velocity * DeltaTime;

if (!Delta.IsNearlyZero(1e-6f))
{
  const FVector OldLocation = UpdatedComponent->GetComponentLocation();
  const FQuat Rotation = UpdatedComponent->GetComponentQuat();

  FHitResult Hit(1.f);
  SafeMoveUpdatedComponent(Delta, Rotation, true, Hit);
```

https://api.unrealengine.com/INT/API/Runtime/Engine/GameFramework/UFloatingPawnMovement/index.html

アニメーションさせる場合は ue4_vehicle.md を参照

https://answers.unrealengine.com/questions/739944/moveto-with-pawn-and-floatingpawnmovement.html

## Person

https://www.slideshare.net/pafuhana/unreal-engine4ue4-2014111

- Character BP 作成
- メッシュ・アニメーション設定
- ターゲットポイント・ナビメッシュ配置(Simple Moveはナビメッシュ必須)
- レベルイベントグラフ
  - Simple Move to Actorを設定
    - ContolollerはキャラをターゲットにしてGetController
  - Move to Actorを設定
    - ContolollerはキャラをターゲットにしてGet AIController
      - AIControllerはゲーム開始時に生成される
    - `Use Pathfinding`はナビメッシュがないならOFFにする
  - GoalはTargetPointを設定
- Character Movement Component
  - Orient Rotation to Movement
- SetActorRotationで向き設定(自動で行う設定があるはず)

## AIController
https://normalanagadfokumento.blogspot.com/2016/01/ue4getcontrollernu.html

## Cinematics

Cinematics with Sequencer: Project Overview | 01 | v4.12 Tutorial Series | Unreal Engine
https://www.youtube.com/watch?v=uEnfMV-4afA

- レベルシーケンサは、配置してあるアクターのパラメータをタイムラインで制御を行う
- transformのキーフレームを配置していく
  - 時間と場所の指定なので速度は計算する必要がある

## Spline
- コンポーネントに以下を持たせる
  - Spline Component
  - Target Point Array
    - Variable Type の右側のマークで配列に切り替える
- コンストラクションスクリプト
  - Clear Spline Points
  - ForEachLoop
    - Add Spline Point

## Simple Spline Movement
- メンバ変数
  - Spline
  - Speed
  - Trigger Actor
  - Trigger Distance
- Initialize
  - 初期位置にTeleport  
- Update
  - 速度と時間から距離を出し、以下のメソッドでLocationとRotationを算出し設定する
    - `GetLocationAtDistanceAlongSpline`
    - `GetRotationAtDistanceAlongSpline`

- Sequence Key
  - Time
  - Speed

ActorとComponentの責務分担
Floating Pawn Movement を持たせて制御できるか？
