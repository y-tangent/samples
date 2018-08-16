# autoware

## install
https://github.com/CPFL/Autoware/tree/1.7.0

readmeの手順に従う

```
error: RPC failed; curl 56 ...
```

```
git config --global http.postBuffer 5M
```

```
git checkout refs/tags/1.7.0
```

## debug build

```
cp catkin_make_release catkin_make_debug
```
-DCMAKE_BUILD_TYPE=Release -> Debug

### attach

```
rosnode info /node_name | grep id
sudo gdb
attach pid
b filename:line
c
set logging on
p value_name
set logging off
```

https://askubuntu.com/questions/41629/after-upgrade-gdb-wont-attach-to-process

## node

- Setup
  - TF
  - Vehicle Model
- Map
  - Point Cloud
  - Vector Map
  - TF
- Sensing
  - Velodyne VLP-16
- Computing
  - Localization
    - vel_pose_connect
  - Detection
    - lidar_euclidean_cluster_detect
  - Mission Planning
    - way_planner
  - Motion Planning
    - dp_planner
    - ff_waypoint_follower

### vel_pose_connect
- input
  - /sim_pose
  - /sim_velocity
- output
  - /pose_relay(to /current_pose)
  - /vel_relay(to /current_velocity)

### lidar_euclidean_cluster_detect
- input
  - /points_raw
- output
  - cloud_clusters
- param
  - pose_estimation
  - clustering_thresholds
  - clustering_distances

```
leaf_size : ダウンサンプリングの具合。downsample_cloud フラグがONの場合に利用。
cluster_size_min : clusterの点数の下限
cluster_size_max : clusterの点数の上限
clip_min_height:クラスタリング範囲（センサからの最小の高さ）
clip_max_height:クラスタリング範囲（センサからの最大の高さ）
remove_points_upto: 点群データを無視する範囲（センサからの距離）
keep_only_lane_points: 左右のクラスタリングする範囲
clustering_distances: しきい値変更距離（5段階のため区切りとなる4つの距離を指定）
clustering_thresholds: 距離に応じた5段階のしきい値設定。距離が近いほど閾値をゆるくする
```

  - downsample_cloud
  - output_frame
    - frameを持つアウトプットのframeを指定する
      - /bounding_boxes, /cluster_centroids, /cloud_clusters
    - デフォルト値は`velodyne`
      - `map`指定するのと何が異なるのか？使う側でTransformするから同じでは？
    - 指定されたframeにtransformしてメッセージに格納する
  - vectormap_frame
    - vector_map_server/is_way_area
- method
  - segmentByDistance
    - clusterAndColor
      - 2次元x,yによるクラスタリングを行う
```
    //perform clustering on 2d cloud
  	pcl::EuclideanClusterExtraction<pcl::PointXYZ> ec;
  	ec.setClusterTolerance (in_max_cluster_distance); //
  	ec.setMinClusterSize (_cluster_size_min);
  	ec.setMaxClusterSize (_cluster_size_max);
  	ec.setSearchMethod(tree);
  	ec.setInputCloud (cloud_2d);
  	ec.extract (cluster_indices);
```
  - velodyne_callback
    - mainループでは何もせず、velodyneコールバックで処理が閉じている
    - コールバック処理中にサブスクライブがきた場合(アーキテクチャ把握が必要)は無視する
    - behaver
      - velodyneからの距離による点郡削除(初期値: 0.0)
      - ダウンサンプリング(初期値: OFF)
      - 高さによるクリッピング(初期値 min: -1.3, max: 0.5)
      - 左右のクリッピング(初期値 flag: OFF, left: 5.0, right: 5.0)
      - 地面削除(初期値 ON)
      - DoN(Difference of Normals)セグメンテーション(初期値: OFF)
      - 距離で区切って定義した閾値を使ったセグメンテーション(距離:{15, 30, 45, 60}, 閾値:{0.5, 1.1, 1.6, 2.1, 2.6})
      - publish

### way_planner
- input
- output
  - /lane_waypoints_array

- behaver
  - callback
    - callbackGetStartPose(/initialpose)
      - m_GoalsPosに設定
    - callbackGetGoalPose(move_base_simple/goal)
      - m_CurrentPoseに設定、m_GoalsPosをm_CurrentPoseにクリア
  - mainloop
    - HMIメッセージ通知処理を行う(初期値:ON, 設定値:OFF)
    - rViz用mapマーカーpublish
    - グローバルパス生成・publish(/lane_waypoints_array)

### dp_planner
- input
  - /cloud_clusters
- output
  - topic
    - /current_behavior: 直値
    - /final_waypoint
    - behavior_state: rViz用
- param
- behaver
  - callback
    - PlannerX::callbackGetVehicleStatus(/current_velocity)
      - 速度、ステア、タイムスタンプ更新
    - PlannerX::callbackGetEmergencyStop(/emergency_stop_signal)
      - EmergencyStopフラグ設定
    - PlannerX::callbackGetTrafficLight(/traffic_signal_info)
      - GreenLightフラグ設定
    - PlannerX::callbackGetAStarPath(/astar_path)
      - AStarPath設定(未使用)
    - PlannerX::callbackGetWayPlannerPath(/lane_waypoints_array)
      - LaneArrayを受け取る(waypoint_planner:複数レーン, way_planner:単一レーン)
      - WayPlannerPathsに設定
    - PlannerX::callbackGetInitPose(/initialpose)
    - PlannerX::callbackGetCurrentPose(/current_pose)
      - m_CurrentPos更新
    - PlannerX::callbackGetCloudClusters(/cloud_clusters)
      - m_TrackedClusters更新
    - PlannerX::callbackGetBoundingBoxes(/bounding_boxes)
      - 未使用
  - mainloop
    - vectorMapファイルからロードネットワーク構築(Map Source 設定値:フォルダ)
    - ローカルプランニング実行
      - 軌道コスト計算してベストな軌道を算出
      - PreCalculatedConditions算出
      - BehaviorState生成
      - 新しい軌道が必要な場合はrollOuts生成
        - ロールアウトがない場合
        - 現在のwaypointがパスの半分より大きくなった場合
        - bRePlan/bNewGlobalPathフラグが立っていた場合
        - behaviorの状態が`障害物回避`の場合
      - 速度更新
    - behavior publish

```
geometry_msgs::Twist t;
geometry_msgs::TwistStamped behavior;
t.linear.x = m_CurrentBehavior.followDistance;
t.linear.y = m_CurrentBehavior.stopDistance;
t.linear.z = (int)m_CurrentBehavior.indicator;

t.angular.x = m_CurrentBehavior.followVelocity;
t.angular.y = m_CurrentBehavior.maxVelocity;
t.angular.z = (int)m_CurrentBehavior.state;

behavior.twist = t;
behavior.header.stamp = ros::Time::now();

pub_BehaviorState.publish(behavior);
```

  - trajectory publish

- method
  - open planner
    - autoware Contributorの名古屋大学 博士課程学生`Hatem Darweesh`が開発している
    - LocalPlannerH::DoOneStep
    - LocalPlannerH::GenerateBehaviorState
      - current_behaviorに反映する値を生成
      - ForwardState::GetNextState()
        - nh.getParam("/dp_planner/enableFollowing", params.enableFollowing);
        - bFullyBlockは、CalculateImportantParameterForDecisionMaking で設定

  ```cpp:BehaviorStateMachine.cpp
    else if(m_pParams->enableFollowing
        && pCParams->bFullyBlock)
        return FindBehaviorState(FOLLOW_STATE);
  ```

    - TrajectoryCosts::DoOneStep
    - LocalPlannerH::CalculateImportantParameterForDecisionMaking

- frameは何を期待しているか
  - RvizPointコールバックでパブリッシュするフレームは2つとも`map`を指定している
- https://github.com/CPFL/Autoware/issues/1250
- dp_plannerの機能でrVizのPublish Pointを使って障害物を置く
  - rVizコールバック
    - rVizがパブリッシュするトピックは`/clicked_point`
    - ポイントは形状を持ったCloudClusterArrayとして扱い、m_TrackedClustersに反映する
    - `/dp_planner_tracked_boxes`にパブリッシュする
  - メインループ
    - m_TrackedClustersを`detected_polygons`にパブリッシュする
      - `detected_polygons`は`visualization_msgs/Marker`メッセージなのでrVizビジュアライズ用
        - rVizの項目名は`Tracked Contours`。Namespacesでもフィルタしたい
  - Forward -> Follow の遷移確認
  - behavior_state

  障害物があったときの振る舞い
  https://www.youtube.com/watch?v=FKM8v79X3_s
  https://www.youtube.com/watch?v=7YQFbqOJOXM
  https://www.youtube.com/watch?v=afxaxOHCuVE

- 疑問点
  - レーンを誰がどう意識しているかがわからない
  - 蛇行運転する問題の対処
    - <arg name="enabTrajectoryVelocities"	default="true" /> <!-- enable when using autoware's pure pursuit node-->

### ff_waypoint_follower
- input
  - /current_behavior
  - /final_waypoint
  - /current_velocity
- output
  - sim_pose
  - sim_velocity
- param
- method
  - open planner
    - TrajectoryFollower::VeclocityControllerUpdate

- behavior
- behaver
  - callback
    - FFSteerControl::callbackGetCurrentVelocity(/current_velocity)
      - 速度・ステアを保存
    - FFSteerControl::callbackGetBehaviorState(/current_behavior)
      - CurrentBehaviorに保存
    - FFSteerControl::callbackGetCurrentTrajectory(/final_waypoints)
      - ローカルパスに保存
  - mainloop
    - Localization and Status Reading Part
      - CurrVehicleStatus更新(delta tでシミュレート)
      - シミュレートした位置・速度をpublish
    - Path Following Part
      - TrajectoryFollowerで要求速度・ステアを計算(結果は次回メインループで利用)

## UE4 連携
UDPSender/Receiver
Camera
Scenario

### input
`current_pose`

PoseStamped.msg
```msg:PoseStamped.msg
Header header
Pose pose
```

Pose.msg
```msg:Pose.msg
Point position
Quaternion orientation
```

### method
- SetWorldLocation
  - frame map -> world
- SetWorldRotation
  - z,w -> yaw

### UE4 -> Autoware
- frame: velodyne

### Autoware -> UE4
- topic
  - current_pose
  - sim_velocity
- frame: map

### Scenario
- 自車の制御はAutowareのパラメータ依存になる
  - シナリオからlaunchファイルを生成するイメージ
- TrafficはUE4のアクターのパラメータとなる
  - 1 Concrete Scenario 1 umap
    - map切り替え時に保持したい情報はGameInstanceに保持
  - or 実行時にシナリオを読み込んで動的にアクターを生成した方が、umap配置するよりやりやすいか？
    - コンポーネントによってはBeginPlay初期化を
- 分散処理

## 3D map
map frame(平面直角座標系)で座標が格納されている。

## vector map
