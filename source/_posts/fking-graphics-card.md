---
title: 装机就是个坑
date: 2019-08-24 16:39:53
update: 2019-08-24 16:39:53
tags: WDNMD
---
刚才拆开机箱装风扇，装完正常开机，看自检过了就把侧板盖上了，结果抬头一看，蓝屏了，`VIDEO_MEMORY_INTERNAL_MANAGEMENT_ERROR`, 看到这个代号以为是显卡内部什么毛病，就没管等他重启，结果启是启动好了，Wallpaper Engine 卡的要死，task manger 打开一看 CPU 占用 100%，GPU占用那一栏直接没了，我还以为是在用核显渲染，想打开 NVIDIA Control Panel 调一调，结果直接“未检测到连接到此计算机的NVIDIA产品。” WDNMD，显卡灯都是亮的，HDMI线也是插到显卡上的，这尼玛闹鬼了？？关机，拔显卡，忘了PCIE插槽还有个卡扣，拔了半天拔不出来，8 pin 电源线也花了好大力气拔出来我淦。放床上冷却一下，再插回去，OK没问题了。  
然后过几天还有个ssd要装，我还要把显卡拔出来，装上ssd，再把显卡插回去，折腾这么几下我有点担心我的显卡。  
然而装上风扇感觉显卡温度并没有太大改善，虽然改善还是有，但是不算太明显。可能之前打一局 ow 开局几分钟就可以 80°，现在打个两三局 80°，我现在有点想上水冷了。而且机箱顶部的风扇转起来有咔咔的声音，把机箱横着放就没这种声音，感觉是风扇的旋转部分和转轴有什么毛病。
淦！