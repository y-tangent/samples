# rendering engine

## Selecting the Best Open Source 3D Games Engines
http://citeseerx.ist.psu.edu/viewdoc/download;jsessionid=F7B6E5C280230E1732777A67414CFF06?doi=10.1.1.654.6783&rep=rep1&type=pdf

### Ogre3D
rVizで使用している。Publish Pointをしようとしてマウスオーバーすると以下のセグフォで落ちる。

```
#3  0x00007f84c623a295 in ?? ()
   from /usr/lib/nvidia-387/libnvidia-glcore.so.387.26
#4  0x00007f84c41fd9c5 in Ogre::GLRenderSystem::_render(Ogre::RenderOperation const&) () from /usr/lib/x86_64-linux-gnu/OGRE-1.9.0/RenderSystem_GL.so.1.9.0
#5  0x00007f8500b837f7 in Ogre::SceneManager::renderSingleObject(Ogre::Renderable*, Ogre::Pass const*, bool, bool, Ogre::HashedVector<Ogre::Light*> const*) ()
   from /usr/lib/x86_64-linux-gnu/libOgreMain.so.1.9.0
```

http://wiki.ros.org/rviz/Troubleshooting
https://github.com/CPFL/Autoware/issues/1076

Autowareが用意しているrviz Pluginで本問題の回避が可能。
- rViz Menu `+`
- OpFlag をロード
- OpFlag ボタンからパブリッシュを行う
