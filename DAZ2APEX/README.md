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
- DAZ fbx character import, separate hair import
- Hair resamplers and settings: fiber hair (curves) or use 2 included hair cards-to-splines resamplers
- Genesis joints renamer (to sanitize names and bring them to root-based naming)
- Flexible grouping tool to isolate materials into groups by keywords (optional for cases when the material names are not unique)
- Joint scaling reset (Overwrite to 100% or Re-calculate via polardecomp stretch) (H20.5)
- Joint orientation correction
- 2 versions of hair/stache/props binding for simple non-simulated attachment: Bind to (head) Joint (for static) or Capture transfer (for deforming geo)
- Capture layer correction setup
- Transparent zones setup (to hide body-clothes intersections)
- Karma LOP with auto SOP->LOP material assignment
- Demo character (a default DAZ character included for your convenience only, obtain the assets from DAZ)
- Default DAZ textures wired to the DAZ library using $LIB variable (change it to your path in Assets & Variables)
- KineFX retarget helper with Rig presets, Locomotion extraction, Caching and Transforms
- No blenshapes (yet)


## The manual parts

1. Since DAZ structures are quite diverse, after loading an fbx you will have to care about the materiala. For cases when material (surface) names are not unique use `Groups_by_keywords` to isolate them in groups. This is a new tool, that creates groups from attributes by matching patterns.

2. For materials in Karma/Solaris copy over your materials from the `Character_init` `matnet1` into the LOPs matlib, if you rename the `matnet1` node, you have to rename the `Auto_material` Separator accordingly, since it is used as a delimiter to re-build the LOP material path. It is irrelevant where in the SOPs the material is, `Auto_material` script simply truncates everything before (and with) the separator from the GeomSubset's name to get the actual Material name. GeomSubsets are the result of the Partition Attributes function of the SOP Import LOP. Logically the `shop_materialpath` attribute is used for partitioning, it's a covenient procedural solution for those who still live in-between the LOPs/SOPs worlds.

2. The DAZ's clothes/props have their own skeletons, which my setup takes care of in the `Joints_cleanup` subnet. It's not a 100% universal solution, but I never had to change anything in it. Although if something breaks, it might be a good idea to inspect the skeletons for possible duplicates. This setup handles twist joints in 99% of cases, but if it doesn't you can get rid of them in DAZ before expoting as an option.

3. Use the included `Sculpt` and `Capture Layer Paint` nodes to fix any problems with skinning/geometry (they stash the geo, so you need to reset them for each new character).

4. Choose one of the hair import methods. If you exported fiber (curves) hair from DAZ, you connect them directly. Otherwise if your character has hair cards, then use one of the two resamplers included in the setup: `Polyhair_to_Curves` for single-poly hairs or the more complex/slower/better `Hair_polycards_to_Curves`. These will resample the geo into curves for you. Adjust hair render propreties using `Hair_properties` subnet. Then the head hair can be simply attached to the head joint using `Bind_To_Head_Joint`. But for the beard or any other deforming hair use `Bind_Capture_Transfer_from_Skin`, it transfers the Capture weights from the character's skin. It might need some adjustment from you as well.


## Exporting from DAZ

Use pretty standart fbx export settings excluding lights and cameras. DAZ does not export proper Joint orientations, my setup cares about it in the `Joints_orient` subnet. DAZ insists that Degraded Scaling must be on for FBX, but both options seem to work well for me.

Export the head/facial/body hair (except the eyelashes and eyebrows) separately into Obj file. Daz can export curves/splines into Obj. Enable the following options:
- UVs
- Faces 
- Polylines
- Separate Object
- Write Groups

If the hair scalp is required then in my experience it is more convenient to export it together with hair instead of the character geo.


## Changelog:


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


v 1.94

- NEW: Early version of retarget helper setup (kinefx) added
- `Aim_eyes` merged into a single lookat APEX component for both eyes
- Disabled name hardening on cache nodes by default
- Character pack/cache added


v 1.93

- `Isolate_materials_into_groups` now uses name patterns, should help automate it
- `Invisible_zones` in a separate subnet and has settings now


v 1.92

- Small fixes and reorganizing
- Delete Class attribute after `Polyhair_to_Curves`


v 1.9
- Neck rotation joint added
- Correction to the Thumb orientation, now it is more alinged with other fingers
- Correction to Eyelids orientation
- More unified joint name pattenrs in `Joints_cleanup`, `Joints_orient` and in the APEX setup
- Simple bind now does not create packed geometry, it overrides capture attribute instead (should help with char instancing)
- More shortcuts in `Hair_properties`
- Got rid of unneded path attribute on Rest_skel
- Added alternative transform in `Joints_scale` (can make difference for non-DAZ models)
- More comments everywhere
- APEX nodes refreshed with build 20.5.587


## Useful links

[DAZ Studio Alembic Exporter](https://www.daz3d.com/forums/discussion/428856/sagan-a-daz-studio-to-blender-alembic-exporter/p1)

[DAZ js script to collect textures](https://www.daz3d.com/forums/discussion/386421/help-solution-for-collecting-all-maps-from-a-ds4-scene/p1)
