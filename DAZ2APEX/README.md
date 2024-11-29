# DAZ to APEX character rigging setup v1.5

![daz2apex_demo](https://github.com/user-attachments/assets/34fe8b71-e561-47ef-ad48-e1746e361663)


Although it went through a lot of iterations already, it's not perfect.
I left some comment notes and node comments inside. 
This setup is intended for bipedal human characters and never tested on an animated DAZ export.
Basically this setup allows to inject a new character simply by loading a new fbx.


## The most of the setup is auto, and the APEX part is 100% auto

What's included:
- DAZ fbx character import (export character without hairs for it)
- DAZ obj hair/stache import, fiber style or use 2 included hair cards to splines resamplers (export hairs to obj for this)
- Genesis joints/paths renamer (to sanitize names and bring them to root based naming)
- Props/cloths grouping to isolate materials
- Joint orientation setup
- Hair/stache/props to character binding (for simple non-simulated attachment)
- Capture layer correction setup
- Material assignment
- Full-featured APEX rig based on Electra featuring fk/ik, spine spline, reverse feet, full facial rig and head/eyes aim
- Basic APEX character animation setup
- Karma LOP with auto SOP->LOP material assignment (copy over the materials from the character_init matnet1 into the LOPs matlib, do not rename matnet1, this name is used for auto-material assignment)
- Demo character

Please do not use the assets of this setup without buying from DAZ, they included for your convenience only.


## The manual parts

1. Since DAZ structures are quite diverse, after loading an fbx you will have to care of the assets material separation. `Isolate_materials_into_groups` subnet cares about this. This is mostly required because assets can share same material names. This subnet has a number of renaming nodes, you will need one for eatch clothing. Each one adds a prefix to the original material names, that you can also specify. Just select a group for each asset and change the prefix to your preference.

2. Then you will have to care about the materials yourself, while I am looking into how to automate it. You will find the template materials inside, just replace the textures and create new materials for the clothings.

3. DAZ clothes/props have their own skeletons, which my setup takes care of in the `Joints_cleanup` subnet. I never had to change anything in it, but if something breaks it might be a good idea to inspect the incoming skeletons for possible duplicates.

4. This setup handles twist joints in 99% of cases, but if it doesn't you can get rid of them in DAZ as an option.

5. Use the included `Sculpt` and `Capture Layer Paint` nodes to fix any problems with skinning/geometry (they stash the geo, so you'll need to reset them for each new character).

6. Select one of the hair injection methods. If you exported fiber (curve) hair from DAZ, you connect them directly. Or if your character has hair cards, then use one of the two resamplers included in the setup: `Polyhair_to_Curves` for single-poly hairs or the more complex and slower `Hair_polycards_to_Curves`. These will resample the geo into curves for you. Adjust hair render propreties inside `Hair_render_properties` subnet. Then the head hair simply gets attached to the head joint, but for the beard I use `Atribute Transfer` node to transfer Capture weights from the character's face. It might need some adjustment from you as well.


## Exporting from DAZ

Use pretty standart fbx export settings excluding lights and cameras. DAZ does not export proper joint orientations, my setup cares about it in the "Orient_joints" subnet. It works pretty well, but since I am not an expert, it might need your attention. DAZ insists that Degraded Scaling must be on for FBX, and I am yet not sure about Degraded Skinning, both states are working.

Exportt the head and facial/body hair (except the eyelashes and eyebrows) separately into Obj. Enable UVs, Normals, Faces, Polylines and Separate Object and Write Groups checkmarks.


## Useful links

[DAZ Studio Alembic Exporter](https://www.daz3d.com/forums/discussion/428856/sagan-a-daz-studio-to-blender-alembic-exporter/p1)
[DAZ js script to collect textures](https://www.daz3d.com/forums/discussion/386421/help-solution-for-collecting-all-maps-from-a-ds4-scene/p1)
