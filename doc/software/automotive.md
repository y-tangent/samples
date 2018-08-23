# automotive

https://vires.com/
https://ipg-automotive.com/jp/
https://www.pegasusprojekt.de/en/about-PEGASUS

http://www.meti.go.jp/committee/kenkyukai/mono_info_service.html
自動走行ビジネス検討会

## Virtual Test Drive
https://vires.com/docs/
https://www.asam.net/index.php?eID=dumpFile&t=f&f=1789&token=cd657a37f15f841d62e4d3362c91c4decd05d991

## OpenSCENARIO



## OpenDrive

```
sudo apt-get install freeglut3
```

```
./odrViewer64.1.9.1
```

## pegasus

https://www.pegasusprojekt.de/en/lectures-publications

AVT Symposium 2017 Test Specifications for HAD_Folien .pdf

- ISO 26262: Standard `Road Vehicles – Functional Safety` for developing systems with electronic elements
  - Risk-based approach to safety
  - Safety requirement
  - Proving safety of an implementation of the Highway Pilot

- List of functional scenarios
  - Free driving
  - Following
  - Lane change
  - Overtaking
  - Cut-in
  - Leave lane
  - Cut-through
  - Slow traffic
  - Stop & Go
  - Jam
  - Lane violation
  - Incident traffic
  - Wrong-way driver
  - Obstacle
  - Incident environment

|Functional Scenario|Logical Scenario|Concrete Scenario|
|---|---|---|
|Cut-in|left, from behind|step1:A(V,P),B(V,P)<br>step2:A(V,P),B(V,P)<br>step3:A(V,P),B(V,P)|
|||step1:A(V,P),B(V,P)<br>step2:A(V,P),B(V,P)<br>step3:A(V,P),B(V,P)|
|||step1:A(V,P),B(V,P)<br>step2:A(V,P),B(V,P)<br>step3:A(V,P),B(V,P)|
||left, front||
||left, fall-back||
||right, from behind||

## Simple 2D Scenario Generator

コンクリートシナリオからue4へのデータ生成を段階的に行っていきたい。

http://shinkokyo.or.jp/casest1.html
フラッシュはタイムラインで移動を制御している

ITARDA
CATIA: Dassault Systemes

## Simple 2D simulator
センサ・判断・制御の簡易シミュレータ
- 世に出ているものの調査

迷子にならないように当初の目的も含め地図をかく

- Pegasusは実走行を基にした検証用システム
- 仕様からシナリオを用意したい場合
  - Logical Scenario / Concrete Scenario を記述
  - シミュレート機能あり

ue4 2D: Paper2D
http://katze.hatenablog.jp/entry/2016/03/04/223927

https://qiita.com/kanehama/items/972b16b2e1b0df8e1da8
http://sutara79.hatenablog.com/entry/2016/09/06/111411
