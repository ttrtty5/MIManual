---
title: "目录"
date: 2021-10-30T22:32:58+08:00
draft: true
---
### Mine_imator Readme

==**此文档为赶工编写，写作过程中难免有疏漏，如有发现，请及时与笔者取得联系。**==

#### 〇、基本数据类型

​	在 GameMaker Studio 2中，存在多个数据类型，详细情况见下表。

|          变量类型           |        声明方法        | 文本编辑器中的颜色 |       注意事项       |
| :-------------------------: | :--------------------: | :----------------: | :------------------: |
|  全局变量`globle variable`  |  `globalvar kangti;`   |        粉色        |    后面得跟个分号    |
|     实例变量`variable`        |        `kangti`        |       蓝紫色       |                      |
|      临时变量/局部变量      |      `var kangti`      |       浅黄色       |   只在当前脚本有效   |
|       常量`constant`        |         我不会         |       浅红色       |    `noone == -4`     |
|          宏`Macro`          |  `#macro kangti 250 `  |       正红色       |                      |
| 内置变量`Built-in Variable` |        无法声明        |       亮绿色       |        没啥用        |
|        文本`string`         | `kangti = "da sha bi"` |       亮黄色       |                      |
|        脚本`script`         |   在右边文件树里新建   |       浅橙色       |  要在文件里写参数名  |
|         枚举`enum`          | `enum e_kangti {A,B}`  |   浅红色(同常量)   | 调用方法`e_kangti.A` |

##### 0.1 在GameMake Studio 2中创建脚本

​	在GMS2中，可以通过在最右侧文件树中空白处单击鼠标右键-Create Script来创建一个新的脚本。脚本的内容和注意事项见下面的样例程序。假设这里创建了一个名为`script_name`的脚本。可以通过对函数名称**单击鼠标中键**的方式来跳转到定义。

```javascript
/// script_name(kangti, linzi, ttr)       // 这里是ide注释, 可以不写, 但是写了注释的人颜值往往更高, 形参是根据使用的argument的数量决定的
/// @desc this is an example script.      // 这里是脚本的描述
/// @arg kangti                           // 
/// @arg linzi                            //
/// @arg ttr                              //
                                          //
var hao_ren, huai_ren, da_lao;            // 声明临时变量，仅在这个脚本中生效
hao_ren = argument1                       // 这里要用argument0这样的格式来调用，在这个脚本中，argument1是linzi
huai_ren = argument0                      // argument0 == kangti
da_lao = argument2                        // argument2 == ttr
                                          // 后面就是主体内容了
print(haoren)                             //
```



#### 一、Mine-imator中的UI系统

​	Mine-imator的作者设计了多种UI组件，详细情况见下表，若要调用，只需要在**名称前面加上**`draw_`。这里没完全用过，**待补充**。

| 组件名称               | 实现效果                       | 注意事项 |
| ---------------------- | ------------------------------ | -------- |
| `alert`                |                                |          |
| `button_color`         |                                |          |
| `button_menu`          |                                |          |
| `button_normal`        |                                |          |
| `button_checkbox`      |                                |          |
| `checkbox`             | 复选框                         |          |
| `checkbox_expand`      | 带扩展菜单的复选框(如颜色校正) |          |
| `dragger`              |                                |          |
| `drop_shadow`          |                                |          |
| `inputbox`             |                                |          |
| `keycontrol`           |                                |          |
| `label`                | 文字标签(组件前面的字)         |          |
| `link`                 |                                |          |
| `loading_bar`          |                                |          |
| `meter`                | 能拖动交互的数值调整条         |          |
| `radiobutton`          |                                |          |
| `recent`               |                                |          |
| `separator_horizontal` |                                |          |
| `separator_vertical`   |                                |          |
| `texture_picker`       |                                |          |
| `texture_slot`         |                                |          |
| `watermark_preview`    |                                |          |
| `wheel`                |                                |          |

##### 1.1 UI组件的调用与常见参数的说明

​	以`draw_meter()`为例，对各个UI组件的绘制函数进行说明。下面是`tab_frame_editor_camera()`的一段节选：

```javascript
/// tab_frame_editor_camera()

var capwid, text;
capwid = text_caption_width("frameeditorcamerafov");

// FOV
tab_control_meter()
draw_meter("frameeditorcamerafov", dx, dy, dw, tl_edit.value[e_value.CAM_FOV], 50, 1, 170, 45, 1, tab.camera.tbx_fov, action_tl_frame_cam_fov, capwid)
tab_next()
```

###### 1.1.1 调用方法

​	在这个tab中，调用`draw_meter()`之前，调用了一次`tab_control_meter()`，这是对组件的高度进行控制，无需理解其内容，只要记得**在绘制前调用**即可。

​	调用`draw_meter()`之后，调用了一次`tab_next()`，该函数会对`dy`进行更新，相当于指定下一个组件的垂直起始位置。

###### 1.1.2 绘制函数中参数的说明

​	`draw_meter()`的声明如下，要想查看具体内容，可以在GMS2中使用鼠标中键跳转定义：

```javascript
draw_meter(name, x, y, width, value, valuewidth, min, max, default, snap, textbox, script, [captionwidth, [tip]])
```

​	大部分参数的意义是显而易见的，这里不再赘述，只对容易出错的几项进行说明。如有描述不清或认为需要补充，请及时联系本文档的笔者。

​	首先是`name`，第一段引用中传入的值为`"frameeditorcamerafov"`，这个字符串要与语言文件中的键名进行对应。例如，这个组件在语言文件中的map是这样的：

```json
{
	··· ···
	"frameeditor/": {
		··· ···
		"camera/": {
			"fov": "Field of view",
			··· ···
		}
    	··· ···
	},
	··· ···
}
```

​	**将键名由根及顶进行连接，去除斜杠，即为需要传入的`name`值。**	

------

​	其次是`value`，从第一段引用代码中，可以看到程序向`draw_meter`中传入了一个名为`tl_edit.value[e_value.CAM_FOV]`的值。在Mine-imator的代码中，`tl`是`timeline`的缩写。`tl_edit`是一个全局变量，在`project_startup()`中被声明和初始化，初始值为`null`, 由其他函数填入物体id。通过物体id直接访问obj_timeline对象的value属性, 也就是`tl_edit.value`

​	`[]`中的数值为`e_value.CAM_FOV`，这是一个枚举量。在Mine-imator的代码中，凡是以`e_`作为开头的量都代表着枚举。当我们进行更改的时候，尽量遵循原有的规范。容易注意到，枚举量的颜色为浅红色，与常量的颜色相同。这说明枚举量就是一个常量，这与枚举的定义相符合。假定`e_value.CAM_FOV == 123`，则`tl_edit.value[e_value.CAM_FOV] == tl_edit.value[123]`。这样使用枚举来代替常量的方法增加了代码的可读性，相当于为数组中的元素进行了命名，是常见的写法。

​	如果要修改程序中的枚举量，最好打开`\scripts\enums`再进行相应的增删。在程序启动时，会调用一次`enums`来初始化各枚举量。增加后，要到对应的`default`中进行初始化。例如，在`timeline`中的更改，需要打开`tl_value_default()`。

------

​	接下来介绍`textbox`，这是一个容纳文字的容器，可以指定一段文本的数据类型和后缀。为保证代码规范，传入的值要在`app_startup_interface_tabs()`中进行初始化。样例如下：

```javascript
frame_editor = new_tab(setting_frame_editor_location, false)
with (frame_editor)
{
		// Camera
	camera = tab_add_category("frameeditorcamera", tab_frame_editor_camera, false)
	with (camera)
	{
		video_template = null
		look_at_rotate = true
		tbx_fov = new_textbox_integer()   // 这个就是传进去的那个值
		tbx_fov.suffix = "°"
		tbx_blade_amount = new_textbox_integer()
		tbx_blade_angle = new_textbox_integer()
		tbx_blade_angle.suffix = "°"
		tbx_ratio = new_textbox_decimals()

	}
}
```

------

​	最后要对`capwid`进行说明，它是`caption width`的简写，这个参数通常要取一系列字符串的长度的最大值，是在ui中对齐使用的。如果增加了很长的文字说明，记得改一下对应文件的这个值。

##### 1.2 使用UI控制值

​	别人咋写我咋写，全靠抄，复制粘贴，没啥好说的，也没细看

##### 1.3 对变量增加缓动曲线

​	在`tl_update_values()`加上就行，格式为`tl_update_values_ease(e_value.CAM_FOV, trans, p)`
