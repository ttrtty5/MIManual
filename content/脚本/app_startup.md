---
title: "app_startup()"
draft: true
---
## 内容流程
启动日志系统 log_startup()

加载外部程序dll lib_startup()

文件系统检测是否可用 file_lib_startup()

检测是否存在 地图导入exe, 配置文件"legacy.midata", 语言文件"english.milanguage"

3d系统 顶点初始化 vertex_format_startup()

内部数据列表初始化 [app_startup_lists()](../app_startup_lists "")

声明app实例的一堆属性 [app_startup_window()](../app_startup_window "")

声明app实例的关于alert的属性 alert_startup

声明json解析会用到的变量 json_startup()

做好加载settings的准备并加载settings settings_startup()

project_startup()

render_startup()

camera_startup()

做好加载mc资产包的准备并加载mc资产包 minecraft_assets_startup 里面的minecraft_assets_load_startup_version()才是加载



