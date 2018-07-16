# ue4 vehicle

http://api.unrealengine.com/JPN/Engine/Physics/Vehicles/VehicleUserGuide/
http://api.unrealengine.com/JPN/Engine/Physics/Vehicles/SimpleWheeledVehicleMovementComponent/

## Wheel Blueprint

- Content Browser -> Miscellaneous -> Data Asset -> TierConfig
- Content Browser -> Blueprint Class -> VehicleWheel (Front, Rear)
- Change properties of Front and Rear

## Animation Blueprint

- Copy vehicle directory (Skeletal Mesh and Physics Asset) in Vehicle template
- Content Browser -> Animation -> Animation Blueprint -> Sedan_Skeleton
- MyAnimBlueprint -> Class Settings -> Parent Class -> VehicleAnimInstance
- In the Anim Graph, Mesh Space Ref Pose - Wheel Handler - Final Animation Pose
- Optionally, Add Look At node

```cpp:VehicleAnimInstance.cpp
void FVehicleAnimInstanceProxy::SetWheeledVehicleMovementComponent(const UWheeledVehicleMovementComponent* InWheeledVehicleMovementComponent)
{
  ...
			// set data
			WheelInstance.BoneName = WheelSetup.BoneName;
			WheelInstance.LocOffset = FVector::ZeroVector;
			WheelInstance.RotOffset = FRotator::ZeroRotator;
  ...
}

void FVehicleAnimInstanceProxy::PreUpdate(UAnimInstance* InAnimInstance, float DeltaSeconds)
{
  ...
				{
					WheelInstance.RotOffset.Pitch = VehicleWheel->GetRotationAngle();
					WheelInstance.RotOffset.Yaw = VehicleWheel->GetSteerAngle();
					WheelInstance.RotOffset.Roll = 0.f;

					WheelInstance.LocOffset.X = 0.f;
					WheelInstance.LocOffset.Y = 0.f;
					WheelInstance.LocOffset.Z = VehicleWheel->GetSuspensionOffset();
				}
  ...
}
```
## Vehicle Blueprint

- Content Browser -> Blueprint Class -> WheeledVehicle
- Blueprint -> Skeletal Mesh Component -> Set Animation and Mesh
- Add Component -> Camera Component, camera position setting
- Disable Use Pawn Control Rotation on camera component
- Set Wheel Setups in Vehicle Setup on Vehicle Movement Component
  - 0: Sedan_FrontWheel, Wheel_Front_Left, (0, -12, 0)
  - 1: Sedan_FrontWheel, Wheel_Front_Right, (0, 12, 0)
  - 2: Sedan_RearWheel, Wheel_Rear_Left, (0, -12, 0)
  - 3: Sedan_RearWheel, Wheel_Rear_Right, (0, 12, 0)

## Input Settings

- Project Settings -> Input
  - Action Mappings
    - Handbrake -> Space Bar
  - Axis Mappings
    - Forward - W 1.0, S -1.0
    - Right - D 1.0, A -1.0
- Event Graph
  - InputAxis Forward - Set Throttle Input
  - InputAxis Right - Set Steering Input
  - InputAction Handbrake
    - Pressed - Set Handbrake Input (New Handbrake ON)
    - Released - Set Handbrake Input (New Handbrake OFF)

## Game Mode

- Content Browser -> Blueprint Class -> Game Mode Base
- Game Mode BP -> Default Pawn Class -> Vehicle Blueprint
- World Settings -> Game Mode -> Game Mode BP

## Conclusion

> - How to configure a TireConfig Data Asset.
- How to configure a Wheel Blueprint for your Font and Rear wheels.
- How to setup an Animation Blueprint specifically for a Vehicle.
- How to configure your Vehicle Blueprint with your Skeletal Mesh, Animation Blueprint, and Wheel Blueprint(s).
- How to configure your Axis Mappings and Bindings to control your Vehicle.
- How to create and assign a new Game Mode to spawn your vehicle.

## Minimum Setup

- Copy Vehicle Template
- Create WheeledVehicle Blueprint
  - Set Mesh and Animation
  - Set Wheel Setup on VehicleMovement
- Set Input Axis and Input Event Graph
- Creates Game Mode BP and Set Default Pawn Class
- Overrides Game Mode in World Settings

## Debug

- DefaultPawn
  - UPawnMovementComponent
  - USphereComponent
  - UStaticMeshComponent

```cpp:DefaultPawn.cpp
void InitializeDefaultPawnInputBindings()
  ...
		UPlayerInput::AddEngineDefinedAxisMapping(FInputAxisKeyMapping("DefaultPawn_MoveForward", EKeys::W, 1.f));
		UPlayerInput::AddEngineDefinedAxisMapping(FInputAxisKeyMapping("DefaultPawn_MoveForward", EKeys::S, -1.f));
  ...

void ADefaultPawn::SetupPlayerInputComponent(UInputComponent* PlayerInputComponent)
  ...
  InitializeDefaultPawnInputBindings();

  PlayerInputComponent->BindAxis("DefaultPawn_MoveForward", this, &ADefaultPawn::MoveForward);
  ...

  void ADefaultPawn::MoveForward(float Val)
  {
  	if (Val != 0.f)
  	{
  		if (Controller)
  		{
  			FRotator const ControlSpaceRot = Controller->GetControlRotation();

  			// transform to world space and add it
  			AddMovementInput( FRotationMatrix(ControlSpaceRot).GetScaledAxis( EAxis::X ), Val );
  		}
  	}
  }
```
```cpp:Pawn.cpp
void APawn::AddMovementInput(FVector WorldDirection, float ScaleValue, bool bForce /*=false*/)
{
	UPawnMovementComponent* MovementComponent = GetMovementComponent();
	if (MovementComponent)
	{
		MovementComponent->AddInputVector(WorldDirection * ScaleValue, bForce);
	}
	else
	{
		Internal_AddMovementInput(WorldDirection * ScaleValue, bForce);
	}
}
```
