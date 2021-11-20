---
title: "添加UI"
draft: true
---
比如需要添加一个bezier曲线参数的滑动条

先定义ui控制的变量, 在enums.gml文件第103行

    enum e_value
    {
    	POS_X,
    	POS_Y,
    	POS_Z,
    	ROT_X,
    	.......
    	amount
    }
在amount的前面添加你要添加的参数
如BEZIERY

    enum e_value
    {
    	POS_X,
    	POS_Y,
    	POS_Z,
    	ROT_X,
    	.......
		BEZIERY.
    	amount
    }

然后在app_startup_lists.gml的第14行内value_name_list列表添加你想添加的参数的命名, 尽量对应

    ds_list_add(value_name_list,
    	"POS_X",
    	"POS_Y",
    	"POS_Z",
    .....
    	"BEZIERY"
    }

在tl_value_defaul.gml中设置参数的默认值

然后找到你要添加ui的地方, 比如在tab_frame_editor_keyframe.gml里


此时只完成了对参数的声明, 以及ui填入参数的方式, 还需要在tl_update_values中选择对参数的插值方式

比如

    tl_update_values_ease(e_value.BEZIER, "instant", 0)

此时只需要在对应的更新事件脚本中调用tl_edit.value[e_value.BEZIER]获取参数并做你想做的事就行了




注: value_name_list和e_value的长度一定要对应, 不然会报错