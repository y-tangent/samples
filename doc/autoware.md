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

## UE4 連携
UDPSender/Receiver
Camera
Scenario
