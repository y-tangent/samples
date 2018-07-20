# numerical_analysis

## MATLAB

- MATLABコマンド
	- マトリックス演算
	- 微分方程式の解放
	- グラフ表示
- ツールボックス
- Simulink
	- ブロックセット

データ型
https://jp.mathworks.com/help/matlab/data-types_data-types.html

- table
	- 異なる型をもつことができる名前付きの列で構成された、表形式の配列
- 構造体
	- さまざまな型とサイズのデータを含む名前付きフィールドをもつ配列
	- 定義は必要とせず動的にメンバが追加できる
- セル配列
	- セルと呼ばれるインデックス付きのデータコンテナをもつデータ型

### syntax

- cかpythonとの比較表がほしい
- static variable & scope
- pack/unpack

## Octave

MATLABと互換性がありMATLABコマンドが使用できる。
しかし以下のように未実装のものもある。

```
>> height
warning: the 'height' function is not yet implemented in Octave
>> width
warning: the 'width' function is not yet implemented in Octave
>> readtable
warning: the 'readtable' function is not yet implemented in Octave
>> xmlread
warning: the 'xmlread' function is not yet implemented in Octave
```
