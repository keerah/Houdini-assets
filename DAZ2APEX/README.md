# DAZ to APEX character rig setup

![DAZ to APEX](https://github.com/user-attachments/assets/f0d1771e-8d74-4f10-a655-12adf9e1131b)


Although it went through a lot of iterations already, it's not perfect.

I left comments and notes inside for most of the parts of it. 

This setup is for bipedal human characters.

It has no blendshapes support by now and was never tested on an animated DAZ export.

This setup is also suitable for Genesis 8, but you will have to rename all the joints accordingly to the G8 naming in all parts of it.

Basically this setup allows to inject a new character by simply loading a new fbx (and materials).

The most of the setup is auto, and the APEX part is 100% auto.


## What's included:

- Full-featured APEX rig based on Electra featuring fk/ik, spine spline, reverse feet, full facial rig and head/eyes aim
- DAZ fbx character import (export character without hairs for it)
- DAZ obj hair/stache import, fiber style or use 2 included hair cards to splines resamplers (export hairs to obj for this)
- Genesis joints renamer (to sanitize names and bring them to root based naming)
- Props/cloths grouping to isolate materials (optional for cases when the material names are not unique)
- Joint transform reset (to 100% scale for DAZ and alternative for other characters)
- Joint orientation correction
- 2 versions of hair/stache/props binding (for simple non-simulated attachment)
- Capture layer correction setup
- Material assignment
- Transparent zones setup (to hide body-clothes intersections)
- Basic APEX character animation setup
- Karma LOP with auto SOP->LOP material assignment (copy over the materials from the character_init matnet1 into the LOPs matlib, do not rename matnet1, this name is used for auto-material assignment, or if needed rename the LOPs primitive path accordingly)
- Demo character (included for your convenience only, please buy the assets from DAZ)
- Default DAZ textures wired to the DAZ library using $LIB variable (change it to your path in Assets & Variables)


## The manual parts

1. Since DAZ structures are quite diverse, after loading an fbx you will have to care of the assets material separation. This is optional for cases when material (surface) names are not unique. `Isolate_materials_into_groups` subnet cares about this. This subnet has a number of renaming nodes for eatch clothing geo. They simply add a prefix to the original material names, that you can also specify. Just select a named geo group for each asset and change the prefix to your preference, then update these in Material assignment.

2. Then you will have to care about the materials yourself, while I am looking into how to automate it. You will find the template materials inside, replace the textures and create new ones for the clothings.

3. DAZ clothes/props have their own skeletons, which my setup takes care of in the `Joints_cleanup` subnet. It's not a universal solution, but I never had to change anything in it. Altho if something breaks, it might be a good idea to inspect the skeletons for possible duplicates.

4. This setup handles twist joints in 99% of cases, but if it doesn't you can get rid of them in DAZ befor expoting, as an option.

5. Use the included `Sculpt` and `Capture Layer Paint` nodes to fix any problems with skinning/geometry (they stash the geo, so you'll need to reset them for each new character).

6. Select one of the hair injection methods. If you exported fiber (curve) hair from DAZ, you connect them directly. Or if your character has hair cards, then use one of the two resamplers included in the setup: `Polyhair_to_Curves` for single-poly hairs or the more complex and slower "Hair_polycards_to_Curves". These will resample the geo into curves for you. Adjust hair render propreties using `Hair_properties` subnet. Then the head hair can be simply attached to the head joint using "Bind_To_Head_Joint". But for the beard or any other deforming hair use `Bind_Capture_Transfer_from_Skin`, it transfers the Capture weights from the character's skin. It might need some adjustment from you as well.


## Exporting from DAZ

Use pretty standart fbx export settings excluding lights and cameras. DAZ does not export proper joint orientations, my setup cares about it in the `Joints_orient` subnet. DAZ insists that Degraded Scaling must be on for FBX, but both states are working well.

Export the head and facial/body hair (except the eyelashes and eyebrows) separately into Obj file. Enable UVs, Normals, Faces, Polylines, Separate Object and Write Groups checkmarks. If the hair scalp is required then it's more convenient to export it together with hair instead of character geo in my experience.


## Changelog:

v 1.92

- Small fixes and reorganizing
- Remove class attribute after `Polyhair_to_Curves`

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
