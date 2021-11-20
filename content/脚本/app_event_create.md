---
title: "app_event_create()"
draft: true
---
## 描述
应用程序的入口点。 

## 内容流程
声明全局变量 debug_indent, debug_timer;

声明枚举

随机化种子

启动程序 [app_startup()](../app_startup "")

若启动失败则触发game_end()游戏结束事件