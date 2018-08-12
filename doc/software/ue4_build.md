# ue4 build

## VS Project
http://unrealengine.hatenablog.com/entry/2015/05/20/220000
http://api.unrealengine.com/JPN/Programming/Plugins/
http://api.unrealengine.com/JPN/Programming/UnrealBuildSystem/

- uprojectからVSプロジェクト生成(c++ファイルがなければuprojectにc++クラスを追加してから)
- C:/Program Files/Epic Games/UE_4.17/Engine/Binaries/DotNET/UnrealBuildTool.exe
- Plugins内のサブディレクトリにupluginファイルがあれば自動認識
- バイナリがなければ自動でビルドしにいく。失敗したらソリューションからマニュアルでビルド。

## Porting
ポーティング対象(4.19 -> 4.17)
https://github.com/getnamo/udp-ue4

http://api.unrealengine.com/JPN/Programming/UnrealArchitecture/StringHandling/

- ４.17 -> 4.18
  - https://usagi.hatenablog.jp/entry/2017/11/16/040755
  - FPaths::GameDir -> FPath::ProjectDir
  - FPaths::GameContentDir -> FPaths::ProjectContentDir
  - FPaths::GameSavedDir -> FPaths::ProjectSavedDir
  - 文字列のコンバート
    - https://docs.unrealengine.com/en-us/Programming/UnrealArchitecture/StringHandling/FName

|From|To|Example|
|---|---|---|
|FName|FString|TestHUDString = TestHUDName.ToString();|
|FName|FText|TestHUDText = FText::FromName(TestHUDName);|
|FString|FName|TestHUDName = FName(*TestHUDString);|
|FText|FName|FText -> FString -> FName|

- 4.18 -> 4.19
  - https://usagi.hatenablog.jp/entry/2018/03/31/021300

## Frame
https://api.unrealengine.com/udk/Three/ChangingUnitsJP.html
1 Unreal Unit = １cm