---
description: 此页面不属于【日本语教学】内容
---

# 《Payday2》如何查看报错

在《Payday2》（下称“游戏”）中，多个模组（.lua）脚本很可能因为冲突而导致崩溃（crash）。\
游戏崩溃后，将会生成一个崩溃日志（crashlog.txt）；此文章的主题意在教学如何分析日志。

## 崩溃日志结构

崩溃日志的结构是固定的，每次生成的崩溃日志结构如下：

```
------------------------------------长虚线表示报错日志开头-------------------------------
//（此小节主要说明崩溃时间和主要原因）
Wed Feb 09 20:33:56 2022


Application has crashed: C++ exception
bad allocation

-------------------------------
//（调用栈）
Callstack:

         payday2_win32_release  (???)     zip_get_name                                        
         payday2_win32_release  (???)     ???                                                 
         payday2_win32_release  (???)     ???                                                 
         payday2_win32_release  (???)     ???                                                 
         payday2_win32_release  (???)     ???                                                 
                           ???  (???)     ???                                                 
                           ???  (???)     ???                                                 
                           ???  (???)     ???                                                 
                           ???  (???)     ???                                                 


-------------------------------
//（错误详细原因）
Current thread: Main
Script stack:
                 GetModImage()  @mods/base/req/BLTMod.lua:289                       
                        init()  @mods/base/req/ui/BLTModItem.lua:112                
                         new()  @mods/base/req/utils/UtilsClass.lua:26              
         update_visible_mods()  @mods/base/req/ui/BLTModsGui.lua:280                
                      _setup()  @mods/base/req/ui/BLTModsGui.lua:229                
                        init()  @mods/base/req/ui/BLTModsGui.lua:46                 
                         new()  @mods/base/req/utils/UtilsClass.lua:26              
                      create()  @mods/base/req/core/MenuHelper.lua:669              
       set_active_components()  lib/managers/menu/menucomponentmanager.lua:490      
       set_active_components()  @mods/base/lua/MenuComponentManager.lua:18          
                                core/lib/managers/menu/coremenulogic.lua:89         
       _execute_action_queue()  core/lib/managers/menu/coremenulogic.lua:54  
                      update()  core/lib/managers/menu/coremenulogic.lua:64         
                      update()  core/lib/managers/menu/coremenumanager.lua:155  
                      update()  lib/managers/menumanagerpd2.lua:20                  
                      update()  lib/setups/setup.lua:879  
                      update()  lib/setups/menusetup.lua:358                        
                      update()  @mods/base/lua/MenuSetup.lua:5  
                      		core/lib/setups/coresetup.lua:557                   

-------------------------------
//（系统信息）
System information:
	Application version : 1.116.69
	CPU : AMD Ryzen 7 4800H with Radeon Graphics (8 cores); SSE; SSE2; SSE3; SSSE3; SSE4.1
	DirectX : 12.0 
	GPU : NVIDIA GeForce RTX 2060 / nvldumd.dll[27.21.14.5749]
	Language : schinese
	Memory :  16253MB 324KB
	OS : 6.2.9200 () 0x300-0x1 (64 bits)
	Physics : threaded
	Renderer : DX9 threaded
	Sound : Realtek Semiconductor Corp. (WH-xm3000 (Realtek(R) Audio))



----------------------------------------------------------------------

```

由此可见，日志被分为4个结构

* 崩溃时间和主要原因
* 调用栈
* 错误详细原因
* 系统信息

日志的重点，当然是**错误详细原因**；当然崩溃时间和主要原因也可以帮你找出问题。因此，下文将会用两个小节说明如何解析这两个问题。

## 错误详细原因 <a href="#why_error" id="why_error"></a>

在【Script stack】（【脚本栈】）后，会详细地将报错瞬间调用的脚本写出。\
它们分为两个部分，并且按照以下结构：

```
调用函数()    调用文件路径:行
```

举个简单的例子，像是\
【weapon factory manager】与【dynamic resource manager】冲突载入引起的错误\
是这个样子的：

```log
Script stack:
                   _add_part()  lib/managers/weaponfactorymanager.lua:746           
                   _add_part()  lib/managers/weaponfactorymanager.lua:711           
     assemble_from_blueprint()  lib/managers/weaponfactorymanager.lua:630           
     assemble_from_blueprint()  lib/units/weapons/menuraycastweaponbase.lua:203     
                        clbk()  lib/managers/menu/menuscenemanager.lua:2548         
                                lib/managers/dynamicresourcemanager.lua:222        
```

调用函数中，说明了目前进行到了哪个步骤，如同举例：

> ```
> _add_part()  lib/managers/weaponfactorymanager.lua:746  
> 函数：添加     调用内容：lib/managers/weaponfactorymanager.lua 第746行
> ```

游戏崩溃日志文件的报错，都可以通过这种办法进行翻译。

{% hint style="warning" %}
你会注意到，调用内容路径是报错Mod发生的路径，也就是说，根据路径可以判断出是哪个Mod在什么步骤出了问题。
{% endhint %}

然而，前面四行并不能看出问题的所在，看来是顺利载入了。\
让我们看向最后两行：     &#x20;

> ```log
> clbk()  lib/managers/menu/menuscenemanager.lua:2548           
>         lib/managers/dynamicresourcemanager.lua:222
> 翻译：
> 指针()  
> lib/managers/menu/menuscenemanager.lua 第2548行
> lib/managers/dynamicresourcemanager.lua 第222行
> ```

由此可见，这是两个mod在调用指针的时候冲突导致的游戏崩溃。

以上，此教程已经完成了。\
但是，只有简单例子并不能很好的理解，所以再加一个常见的例子：HUD类报错

{% code title="WolfHUD冲突示范" lineNumbers="true" %}
```
Script stack:
                  _set_image()  @mods/WolfHUD-master/lua/DrivingHUD.lua:511  
                        init()  @mods/HUDWolfHUD-master/lua/DrivingHUD.lua:487  
                         new()  core/lib/utils/coreclass.lua:35                     
                        init()  @mods/HUDWolfHUD-master/lua/DrivingHUD.lua:76   
                         new()  core/lib/utils/coreclass.lua:35                     
          create_driving_hud()  @mods/HUDWolfHUD-master/lua/DrivingHUD.lua:951  
  _setup_player_info_hud_pd2()  @mods/HUDWolfHUD-master/lua/DrivingHUD.lua:946                                                   
                       setup()  lib/managers/hudmanager.lua:330                     
                    load_hud()  lib/managers/hudmanager.lua:317                     
               init_finalize()  lib/managers/hudmanager.lua:219                     
               init_finalize()  lib/setups/gamesetup.lua:714                        
               init_finalize()  lib/setups/networkgamesetup.lua:15                  
                                core/lib/setups/coresetup.lua:442 
```
{% endcode %}

可见，在步骤（第8行）setupplayerinfohudpd2() \
直译是：初始化游戏玩家HUD （此步骤属于WolfHUD创造的函数）\
同时，也载入了另一个HUD\
第9、10、11行： setup | loadHUD | initfinalize\
即分三个步骤初始化了另一个HUD管理Mod：HUD Manager\
之后，游戏在两个Mod初始化后，core/lib/setups/coresetup.lua，游戏核心停止。

根据上述部分可以推断出，是两个Hud Mod导致的游戏崩溃。

## 如何进行处理 <a href="#how-solve-error" id="how-solve-error"></a>

在找出是何种原因导致的游戏崩溃后，你可以进行处理：

1. 简单处理：删除冲突Mod
2. 兼容处理：修复不兼容部分（需要一定的C++/C#/lua知识）

一般地，如果你不希望进行兼容性处理，在删除Mod后，尝试联系Mod作者，询问作者是否可以作出兼容性修复。

## 结语 <a href="#final" id="final"></a>

教程已经完成。\
感谢您的阅读，转载请注明出处（本网站即可）。

{% hint style="success" %}
广告：如果您正在寻求一个友好的游戏社区，请加入QQ群：479903937。
{% endhint %}
