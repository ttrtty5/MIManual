---
title: "如何为mi添加后处理着色器"
draft: true
---
### 如何为mi添加后处理着色器

###### 渲染流程

0. mine-imator的渲染流程 (预览视图)

   ```javascript
   app_event_draw()
         ↓
   window_draw()
   	  ↓
   panel_area_draw()
   	  ↓
   view_area_draw()
   	  ↓
   view_draw(view_main) // view_main = new(obj_view) in app_startup_views()
   	  ↓
   view_update()
   	  ↓
   view_update_surface()
   	  ↓
   render_high() ← action_toolbar_export
   	  ↓
   render_high_xxx()
   ```

###### 手把手教学

1. 创建一个着色器, e,g:`shader_test`. 进入`render_startup.gml`

   1.1. (**可选**)这里有个ds_map()，用来初始化着色器列表.

      ```javascript
      ··· ···
      render_mode_shader_map[?e_render_mode.HIGH_LIGHT_NIGHT] = shader_high_light_night
      render_mode_shader_map[?e_render_mode.HIGH_FOG] = shader_high_fog
      render_mode_shader_map[?e_render_mode.COLOR_GLOW] = shader_color_glow
      render_mode_shader_map[?e_render_mode.TEST] = shader_test // ← look here!!!
      ```

   1.2. (**可选**) 有一堆  `e_render_mode.XXX`在`enums.gml`里，加上你自己的。我加了个 `e_render_mode_TEST`.

   1.3. 进入`shader_startup.gml`,加上你刚创建的着色器.

      ```javascript
      // Compiled?
      if (!err)
      {
      	shader_map = ds_map_create()
      	new_shader("shader_alpha_fix")
      	new_shader("shader_alpha_test")
      	new_shader("shader_blend")
      	new_shader("shader_border")
      	new_shader("shader_color_camera")
      	··· ···
      	new_shader("shader_ca")
      	new_shader("shader_distort")
      	new_shader("shader_test") // ← look here!!!
      ```

      

2. 把你的着色器加入渲染管线. 

   2.1. 在 `render_startup.gml` 声明了一些用于控制着色器开关的全局变量. 你可以把它初始化成`false`. 我加了个`render_test_shader`在这里.

      ```javascript
      ··· ···
      globalvar render_effects_done, render_effects_list, render_effects_progress, render_camera_bloom, render_camera_dof, render_glow,
      		  render_glow_falloff, render_camera_ca, render_camera_distort, render_camera_color_correction, render_camera_grain,
      		  render_camera_vignette, render_aa, render_overlay, render_camera_lens_dirt, render_camera_lens_dirt_bloom, render_camera_lens_dirt_glow, 
      		  render_camera_camera_length, render_test_shader; // ← look here !!!
      ··· ···
      render_overlay = false
      render_camera_lens_dirt = true
      render_camera_lens_dirt_bloom = true
      render_camera_lens_dirt_glow = true
      render_camera_camera_length = false
      render_test_shader = false // ← look here !!!
      ```

      

   2.2. (**可选**) 如果你那个变量是`effect`, 得放进 `render_effects_list`里，在 `render_start.gml`，第78行.

   2.3. 去 `render_high().gml`. 把你的着色器和变量插进来(我的叫`render_test_shader`) 。

      ```javascript
      ··· ···
      // Vignette
      if (render_camera_vignette)
      	finalsurf = render_high_vignette(finalsurf)
      render_update_effects()
      
      // TEST Shader
      if (render_test_shader)
      	finalsurf = render_high_test_shader(finalsurf) // ← look here!!!
      	
      // 2D overlay (camera colors/watermark)
      if (render_overlay)
      	render_high_overlay(finalsurf)
      ```

   2.4. 我在这加了个脚本`render_high_test_shader()`. 这个是用来把着色器应用到平面上的. 内容如下:

      ```javascript
      /// render_high_test_shader(basesurf)
      /// @arg basesurf
      
      var prevsurf, resultsurf;
      
      prevsurf = argument0;
      resultsurf = render_high_get_apply_surf();
      
      gpu_set_texrepeat(false)
      surface_set_target(resultsurf)
      {
      	draw_clear_alpha(c_black, 0)
      		
      	render_shader_obj = shader_map[?shader_test] // this is your shader's name
      	with (render_shader_obj)
      		shader_use()
      	draw_surface_exists(prevsurf, 0, 0)
      	with (render_shader_obj)
      		shader_clear()
      }
      surface_reset_target()
      gpu_set_texrepeat(true)
      
      return resultsurf
      ```

3. 加控件. 具体内容看这里→:

###### FAQ

1. Q: 啥是 `surface`?

   A: surface是view的一个成员变量, 初始化为`null`. view有这些成员.

   ```javascript
   show = true
   location = "full"
   surface = null
   surface_select = null
   surface_camera = null
   mouseon = false
   control_mouseon = null
   control_mouseon_last = null
   render = false
   width = 440
   height = 280
   ```

   

2. ()