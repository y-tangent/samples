# pcl

## pcd_viewer

install
http://www.pointclouds.org/

```
pcd_viewer_release bin_Laser-00147_-00846.pcd
```

`h`でコンソールにヘルプ表示。c, g, r(ALT + r) は主に利用。

```
          p, P   : switch to a point-based representation
          w, W   : switch to a wireframe-based representation (where available)
          s, S   : switch to a surface-based representation (where available)

          j, J   : take a .PNG snapshot of the current window view
          c, C   : display current camera/window parameters
          f, F   : fly to point mode

          e, E   : exit the interactor
          q, Q   : stop and call VTK's TerminateApp

           +/-   : increment/decrement overall point size
     +/- [+ ALT] : zoom in/out

          g, G   : display scale grid (on/off)
          u, U   : display lookup table (on/off)

    r, R [+ ALT] : reset camera [to viewpoint = {0, 0, 0} -> center_{x, y, z}]

    ALT + s, S   : turn stereo mode on/off
    ALT + f, F   : switch between maximized window mode and original size

          l, L           : list all available geometric and color handlers for the current actor map
    ALT + 0..9 [+ CTRL]  : switch between different geometric handlers (where available)
          0..9 [+ CTRL]  : switch between different color handlers (where available)

    SHIFT + left click   : select a point
```

## 座標系
https://www.n-survey.com/online/gmap2.htm
緯度経度 - 平面直角座標 変換

名古屋市守山区 守山区役所

|緯度|経度|X座標(m)|Y座標(m)|縮尺係数|真北方向角|
|---|---|---|---|---|---|
|351212.27566|1365834.50577|-88357.531|-17337.794|0.999904|+00635.17535|

```launch:tf.launch
<!-- worldからmapへのtf -->
<node pkg="tf"  type="static_transform_publisher" name="world_to_map" args="14771 84757 -39 0 0 0 /world /map 10" />
```
