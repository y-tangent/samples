# uml tool

結論として astah か PlantUML が有力。
中規模以上のモデリングは astah 、小規模のモデリングは PlantUML を使用する

## UMLツールの不満点

umlで作ったダイアグラムを画像としてドキュメントに使う。その際にumlファイルと
画像ファイルを両方管理しないといけない。そして画像ファイルからはumlダイアグラムに
インポートできない。

## Inkscape

純粋なsvg記述ツールのためダイアグラムを記述するには向いていない。

## Dia

本ツールで出力したsvgを、umlオブジェクトとしてインポートができない。umlオブジェクト
情報が欠落してしまうか。svgにメタ情報を埋め込んでインポートできるようにできないのか？

## argouml

操作性がよくない、undoできない。なぜかファイルが壊れたことがある。

## PlantUML
ドメイン固有言語。暫定でマークダウンの画像の近くにコメントアウトで仕込んでおくか。

<!---
コメントアウト
-->

http://plantuml.com

- 理想は github, gitlab, Atom の Markdown で plantuml が画像で表示されること
- 現状はそれができないので plantuml記述をコメントアウトして画像に差し替える
- plantuml-viewer は 一番上に出てくるumlブロックの表示のみに対応している
  - 複数のumlブロックがある場合は、わざわざ別のファイルにコピペしてpreviewしなけばならない

### install

http://pierre3.hatenablog.com/entry/2015/08/23/220217

Atom上で記述する。JRE, GraphViz, plantuml-viewer(Atom plugin) を入れる。

### syntax

リファレンスガイドを参照
http://plantuml.com/PlantUML_Language_Reference_Guide.pdf

## StackEdit (raphael)
Markdownに sequence, flowchart を埋め込むことが可能。ただし本エディタに閉じた機能。
