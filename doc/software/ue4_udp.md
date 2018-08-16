# ue4_udp

## Method
Engine\Source\Runtime\Networking
- FUdpSocketReceiver
- FUdpSocketSender

http://www.kirurobo.com/2015/12/ue4-udp.html
UGameInstance を継承してUDP受信処理を実装

システムとしての実装でなければコンポーネントでソケットをもった方が使いやすいか
https://github.com/getnamo/socketio-client-ue4
jsonも使える便利なやつだが、名前のとおりsocketioのクライアント用

https://github.com/getnamo/udp-ue4
あまり使われていないようだが、簡単に送受信ができるようになるudp wrapper。リリースは4.19のみ。

## Sample
ue4で座標(x,y)を受信する。フォーマットはcsv(またはjson)文字列でクライアントは任意。

### client
https://wiki.python.org/moin/UdpCommunication

``` py:udp_client.py
import socket

UDP_IP = "127.0.0.1"
UDP_PORT = 5005
MESSAGE = "1.23, 4.56"

print("UDP target IP: {0}, Port: {1}".format(UDP_IP, UDP_PORT))
print("message: {0}".format(MESSAGE))

sock = socket.socket(socket.AF_INET, socket.SOCK_DGRAM)
sock.sendto(MESSAGE.encode('utf-8'), (UDP_IP, UDP_PORT))
```

``` py:udp_server.py
import socket

UDP_IP = "127.0.0.1"
UDP_PORT = 5005

sock = socket.socket(socket.AF_INET, socket.SOCK_DGRAM)
sock.bind((UDP_IP, UDP_PORT))

while True:
    data, addr = sock.recvfrom(256)
    print("received message: {0}".format(data.decode('utf-8')))
```

### server

Copy Plugins folder into Project root
BPアクターにUDPコンポーネントを持たせて、IP/Port設定、受信イベントのグラフを作成する

## Debug

### 終了時にソケットがクローズされない問題がある

よくわからないが一つのアクターに2つのUDPコンポーネントを持たせ、独自でイベントを定義してオープンクローズを制御すると、クローズできていなかった。
コンポーネントの実装におまかせして、独自のオープンクローズを削除したら問題なく動作した。

LISTENING状態のポート確認

```
netstat -a -p UDP
```

アクターのイベントグラフは、コンポーネントにどう作用するか
アクターが受信してブロードキャストすると、各コンポーネントにつながる？
