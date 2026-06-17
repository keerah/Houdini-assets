# DAZ G9 to APEX bipedal character tools

![DAZ to APEX](https://github.com/user-attachments/assets/f0d1771e-8d74-4f10-a655-12adf9e1131b)


Although it went through a lot of iterations already, it's not perfect.

I left comments and notes inside for most of the parts of it. 

This setup is for bipedal human characters.

It never tested on an animated DAZ export.

This setup allows to inject a new character by simply loading a new fbx (and materials). The most of the setup is auto, except the materials.


## What's included:

- APEX character setup + KineFX character + Agent setup
- Full-featured APEX rig based on Electra featuring fk/ik, spine spline, reverse feet, full facial rig and head/eyes aim
- DAZ fbx character import and separate hair import
- Hair resamplers and settings
- Genesis joints renamer (to sanitize names and bring them to root-based naming)
- Flexible material assignment using RegEx matching of `shop_materialpath`, `name` or `fbx_material` attributes
- Joint scaling reset (for H20.5, H21 takes care of it)
- Joint orientation correction
- Hair/stache/props binding: `Bind_to_Head_Joint` (for static hair) or `Bind_Capture_Transfer_from_Skin` (for deforming hair) or APEX Groom as a purely APEX option
- Capture layer correction setup
- Transparent zones setup (to hide body-clothes intersections)
- Karma LOP with auto SOP->LOP material assignment
- Demo character (a default DAZ character included for your convenience only, obtain the assets from DAZ)
- KineFX retarget helper with Rig presets, Locomotion extraction, Caching and Transforms
- No blenshapes (yet)


## The manual parts

1. Since DAZ structures are quite diverse, after loading an fbx you will have to care about the materiala. Use `Materials_by_ReGex` to isolate and assign materials by any of the attributes. Default DAZ textures wired to the DAZ library using $LIB variable, change this variable to reconnect to your library.

2. For materials in Karma/Solaris copy over your materials from the `Character_init` `matnet1` into the LOPs matlib, if you rename the `matnet1` node, you have to rename the `Auto_material` Separator accordingly, since it is used as a delimiter to re-build the LOP material paths. It is irrelevant where in the SOPs the material is, `Auto_material` script simply truncates everything before (and with) the separator from the GeomSubset's name to get the actual Material name. GeomSubsets are the result of the Partition Attributes function of the SOP Import LOP. Logically the `shop_materialpath` attribute is used for partitioning, it's a covenient procedural solution for those who still live in-between the LOPs/SOPs worlds.

2. The DAZ's clothes/props have their own skeletons, which my setup takes care of in the `Joints_cleanup` subnet. It's not a 100% universal solution, but I never had to change anything here. Although if something breaks, it might be a good idea to inspect the skeletons for possible duplicates. Also make sure you enable `Merge followers` when exporting the character from DAZ. 

3. Use the included `Sculpt` and `Capture Layer Paint` nodes to fix any problems with skinning/geometry (they stash the geo, so you need to reset them for each new character).

4. Choose one of the hair import methods on `Import_Daz_hair`. If you exported fiber (curves) hair from DAZ, use No Resampling. Otherwise if your character has polyhair or haircards, one of the hair resamples will generate curves from the geo for you. Adjust hair render propreties using `Hair_properties` subnet. Then the head hairs can be attached to the head joint using `Bind_To_Head_Joint` or using `Bind_Capture_Transfer_from_Skin` (for deforning hair). And in case you use APEX animation exclusively you can use APEX Groom option (the checkbox on the `APEX_setup` subnet), which takes care of the groom procedurally.


## Exporting from DAZ

Enable these FBX export settings:
- Merge followers (important to merge all the props' skeletons with the main fugure's skeleton)
- Allow Degraded Skinning (required for FBX export)
- Allow Degraded Scaling (required for FBX export, but seems to work with it off as well)

Everything else by default, but exclude the lights, the cameras and the morphs.
DAZ does not export proper Joint orientations, my setup cares about it in the `Joints_orient` subnet.

Export the head/facial/body hair (except the eyelashes and eyebrows) separately into Obj file. Daz can export curves/splines into Obj. Enable the following options:
- UVs
- Faces 
- Polylines
- Separate Object
- Write Groups

If the hair scalp is required then in my experience it is more convenient to export it together with hair instead of the character geo.


## Changelog:

v 2.2

- More updates to more recent APEX features.
- More `Joints_orient` fixes (correct Thumb axis in particular).
- `Joint_names` root joint name pattern narrowed (for renaming).
- NEW: APEX Twist joints added (uses twist joints from DAZ).
- NEW: Option to use APEX Groom instead of binding hair to the character (on by default, useful when you use only APEX to animate the character).
- NEW: `Groups_by_keywords` is now `Materials_by_ReGex`. Instead of grouping it assigns materials directly. Plus a function to quickly update the materials with a new character name (when you spawn a new character).
- NEW: `Hair_properties` updated with new options to collide Hair with Skin (to detangle) and to calculate stiffness along hairs (for simulation).
- Import hair setup moved into the `Import_Daz_hair` subnet.
- Karma materials updated with MKKT option and it's enabled.
- Alpha maps are reconnected (since Houdini can finally display transparent materials).
- Optimized export from `Char_anim` to Solaris by separating hair from geo, since they have pretty different set of attributes.
- Houdini recently changed the default primary rig axis from Z to X, I think I have converted and fixed mostly everything related to this change (except retargeting presets).


v 2.1 - Start of Houdini 21+ support.

- This is an early version based on the previous setup and is most likely not efficient enough yet.
- NEW: APEX setup udated with the custom tool `eyelids_rotation_from_eyes` that deforms eyelids after the eye movement. You can disable it, or you can adjust the rotatinal multipliers in the first 2 lines of the APEX code.
- Hair properties node updated with output visualizer switch, reorganized and optimized. Removed groups creation, instead it can now split the input hair geometry by groups and merge them back into the first output after processing.
- Updated materials with the new Normal map 2.0 and new Karma-specific parameters.
- Updated LOPs autoMat with more strict primitive matching rules (subsets only).
- Replaced the joint scale fixes with the new H21 built-in joint scale normalization option.


v 2.0 - End of Houdini 20.5 support

- NEW: `Isolate_materials` is replaced with the new automatic wrangle `Groups_by_keywords`. It assigns groups by matching keywords (via RegEx patterns) in any of the _name_, _fbx_material_name_ or _shop_materialpath_ attributes with settings per each pattern, allowing maximum flexibility
- `Joints_names` now has node settings with more options, including L/R renaming
- `Joints_cleanup` now has node settings with joint patterns, they basicall do the same (deleting), except the `Modifier` item removes joints with their childern
- Retarget helper v0.45: added the `Joint scale` setting; added viz output switch for switching visualization display between clip/skeletons; the version in `Char_init` has an option to lower-case all names to minimize name variance for better presetting (considering adding this to all helpers after some proof-testing)
- `Invisible_zones` now also has a visualizing switch for easy Alpha preview
- Disconnected alpha primvar in materials by default to be able to see materials out-of-the-box


v 1.96
- `Joints_names` now has parameters with renaming/removing RegEx patterns
- `Joints_scale` now has its mode toggle: Off, Overwrite (with 100% scale) and Calculate (from polardecomp stretch)
- Retarget helper v0.3 (+Rokoko preset, folders order, more controls)
- Added group transfer to `Haircards_to_Curves` to preserve the source's hair groups


v 1.95

- NEW: Agent setup added
- Root Joint orientation fixed (proper for agents)
- Added timeshift `Freeze` node to get rid of the time dependency on Skel
- Retarget helper setup update to v0.2 (with Rig presets, Locomotion extraction, Caching and Transforms)
- Added Joint config with rotation orders to the `Joints_orient` (beta)
- Rigstash moved into `Joints_orient`
- Materials updated with Invisible zones' Alpha (disconnect Opacity to have materilas in the viewport)
- Some more minor joint patterns updates
- APEX refreshed with build 20.5.684


## Useful links

[DAZ Studio Alembic Exporter](https://www.daz3d.com/forums/discussion/428856/sagan-a-daz-studio-to-blender-alembic-exporter/p1)

[DAZ js script to collect textures](https://www.daz3d.com/forums/discussion/386421/help-solution-for-collecting-all-maps-from-a-ds4-scene/p1)
