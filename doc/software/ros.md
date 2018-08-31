# ros

## debug

```
rosrun rqt_graph rqt_graph
rosrun tf view_frames
```

## message addition
http://wiki.ros.org/ja/ROS/Tutorials/CreatingMsgAndSrv
http://www.hotblackrobotics.com/en/blog/2018/02/26/ros-custom-messages/

https://answers.ros.org/question/286263/how-to-echo-custom-defined-message-remotely/
catkin work space で以下を打つと見えるようになる。
環境変数のライブラリパスやROSパッケージパスに追加されるため。

```
source devel/setup.sh
```

### MATLAB
https://jp.mathworks.com/help/robotics/ref/rosgenmsg.html

```
roboticsAddons
rosgenmsg(dir_path)
```

jsk_recognition_msgs が存在しないというエラーがでる
/opt/ros/kinetic/share にある上記メッセージをコピーしてautoware_msgs,jsk_recognition_msgsの親ディレクトリを指定し生成したら通った。
手順に従いpath設定を行い、カスタムメッセージが使用できるようになり rosmsg list で表示されるようになった。

## MATLAB connection
https://jp.mathworks.com/matlabcentral/answers/119559-why-is-the-ros-subscriber-callback-in-matlab-not-triggered-when-messages-are-published-from-an-exter

- Ubuntu

```
export ROS_IP=192.168.198.128
export ROS_MASTER_URI=http://192.168.198.128:11311
```

- Matlab on Windows

```
setenv('ROS_MASTER_URI','http://192.168.198.128:11311')
setenv('ROS_IP','192.168.198.1')
```

WindowsファイアウォールでMATLABを許可する
