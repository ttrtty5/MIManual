---
title: "how to add a shader in miv"
draft: true
---

### how to add a post-progress shader in mine-imator

###### Pipeline

0. the rendering pipeline in mine_imator (preview)

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

###### Step by step

1. create your own shader, e,g:`shader_test`. find a file called`render_startup.gml`

   1.1. in this file, there is a ds_map() that is used to initialize shaders' name.

      ```javascript
      ··· ···
      render_mode_shader_map[?e_render_mode.HIGH_LIGHT_NIGHT] = shader_high_light_night
      render_mode_shader_map[?e_render_mode.HIGH_FOG] = shader_high_fog
      render_mode_shader_map[?e_render_mode.COLOR_GLOW] = shader_color_glow
      render_mode_shader_map[?e_render_mode.TEST] = shader_test // ← look here!!!
      ```

   1.2. (**optional**) you may find that there are some `enums` called `e_render_mode.XXX`. In order to add your own shader, you could choose to add one in `enums.gml`. For example, I add one named `e_render_mode_TEST`.

   1.3. then go to `shader_startup.gml`, add your shader.

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

      

2. add your shader into render pipeline. 

   2.1. there are some global variables at `render_startup.gml` that is used to control if the shader is enabled. you could initialize it as `false`. for example, I add a var called `render_test_shader`.

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

      

   2.2. (**optional**) if your variable is an `effect`, it should be added in the `render_effects_list` in `render_start.gml`, line 78.

   2.3. go to `render_high().gml`. add your var(mine's name is `render_test_shader`, do you remember it?) and plug it into the queue.

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

   2.4. you may notice that I used a script named`render_high_test_shader()`. this is created to apply the shader on the surface that you chose. Including are these:

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

3. add user interface component to control your shader. see another link:

###### FAQ

1. Q: What is a `surface`?

   A: surface belongs to OBJECT view, initialized as a `null`. The list of members that belong to a view object is as followed.

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