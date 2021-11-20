---
title: "添加及使用shader"
draft: true
---
首先是给2d用的shader流程
### 新建及使用2d shader
在Resources窗口中的shader一栏, 新建一个shader, 这里我命名为shader0

给物体添加一个Draw事件, 在其中写入

    shader_set(shader0); // 设置物体的shader为shader0
    draw_self(); // 绘制用了shader的sprite
    shader_reset(); // 如果没这一行, 下次绘制将不更新shader

### 传参给shader
比如我们定义一个color1参数, 由键盘按下→键事件改变color1参数, 传入shader

在物体的create事件中写入

    color1_var = [0,1,0,1]; // 在脚本中的变量
    uni_color1 = shader_get_uniform(shader0,"color1"); // 获取在shader里的color1变量指针

变量修改, 在其他脚本修改color1_var即可, 比如在键盘按下→键事件中写
color1_var[1] -=0.1;

在draw事件中写

    shader_set(shader0); // 设置物体的shader为shader0
    shader_set_uniform_f_array(uni_color1, color1_var); // 通过shader里的变量的地址指针传入在脚本里的变量
    draw_self(); // 绘制用了shader的sprite
    shader_reset(); // 如果没这一行, 下一个object也将使用这个shader, 直到shader_reset为止


在shader0的fsh文件中, 最顶上的声明变量中添加uniform vec4 color1;

    varying vec2 v_vTexcoord;
    varying vec4 v_vColour;
    uniform vec4 u_color;
    uniform vec4 color1;

传递纹理则是用
    glassshader_bgsampler = shader_get_sampler_index(GlassShader, "s_BackgroundSampler")
    spr = sprite_get_texture(sprite_index, 0);
    shader_set(GlassShader);
    texture_set_stage(glassshader_bgsampler, spr);
    shader_reset();



注1: 在draw中传给shader参数时, 是float类型用shader_set_uniform_f(shader0,1)

是float数组则用shader_set_uniform_f(shader0, 0.1, 0.8, 0.25)或shader_set_uniform_f_array

是int则是shader_set_uniform_i


注2:
Attribute：OpenGL传递给vertex shader的变量。 它们可以更改每一个顶点，而且仅可读取。 这些信息包括顶点坐标，材质坐标，顶点颜色和顶点法线等信息。

Varying：用于在vertex shader和fragment shader之间传递数据的变量。 它们可以在vertex shader中编写，在fragment shader中读取。

Uniform：由用户传递给shader的变量。它们都可以在vertex shader和fragment shader中获取

### 给3d用的shader
在顶点绘制vertex_submit前后设置shader即可

    shader_set(shader_prim);
    vertex_submit(buff, pr_trianglelist, sprite_get_texture(sprite_index));
    shader_reset();
