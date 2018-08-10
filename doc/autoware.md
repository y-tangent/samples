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
    - ndt_matching
    - vel_pose_connect
  - Detection
    - lidar_euclidean_cluster_detect
  - Mission Planning
    - lane_rule
    - lane_stop
    - lane_select
    - way_planner
    - dp_planner
    - ff_waypoint_follower
    - pure_pursit
    - twist_filter

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
  - output_frame
    - frameを持つアウトプットのframeを指定する
      - /bounding_boxes, /cluster_centroids, /cloud_clusters
    - デフォルト値は`velodyne`
    - 指定されたframeにtransformしてメッセージに格納する
  - vectormap_frame
    - vector_map_server/is_way_area
- method
  - segmentByDistance

- worldとmap

### way_planner
- input
- output
  - /lane_waypoints_array

### dp_planner
- input
  - /cloud_clusters
- output
  - topic
    - /current_behavior: 直値
    - /final_waypoint
    - behavior_state: rViz用
- param
- method
  - open planner
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
      - frame_idが`world`にも関わらず`map`の座標を渡している
      - ただしframe_idをみておらず、`map`として扱われている
    - ポイントは形状を持ったCloudClusterArrayとして扱い、m_TrackedClustersに反映する
    - `/dp_planner_tracked_boxes`にパブリッシュする
  - メインループ
    - m_TrackedClustersを`detected_polygons`にパブリッシュする
      - `detected_polygons`は`visualization_msgs/Marker`メッセージなのでrVizビジュアライズ用
  - Forward -> Follow の遷移確認
  - behavior_state

  障害物があったときの振る舞い
  https://www.youtube.com/watch?v=FKM8v79X3_s
  https://www.youtube.com/watch?v=7YQFbqOJOXM
  https://www.youtube.com/watch?v=afxaxOHCuVE

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

- behaver
  - Localization and Status Reading Part
  - Path Following Part

## UE4 連携
UDPSender/Receiver
Camera
Scenario

### UE4 -> Autoware
- frame: velodyne

### Autoware -> UE4
- topic
  - current_pose
  - sim_velocity
- frame: map

## 3D map
world座標系(平面直角座標)で座標が格納されている。

## vector map
