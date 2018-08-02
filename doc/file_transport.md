# file_transport

## split - join

http://ser1zw.hatenablog.com/entry/2014/10/19/051134

- split

```
ツール：7-Zip
書庫形式：zip
暗号化方式：AES-256
書庫をサイズで分割
http://www.graviness.com/temp/pw_creator/
```

zipcrypto ならWindows標準で解凍が可能。
zipファイルをエクスプローラで開き(右クリック->プログラムから開く->エクスプローラ)、
ディレクトリをコピー->解凍場所にペースト->パスワード入力

- join

```
COPY /B split_1+split_2+…split_n join_file_name
```

```
COPY /B split_* join_file_name
```

## timestamp
Neo FileTimeChange
画像のメタデータである撮影日時の変更も可能
