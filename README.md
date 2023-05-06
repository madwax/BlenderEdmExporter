﻿**BLENDER EDM EXPORTER**
========================

I have used much of the knowledge provided by NickD. ( https://ndevenish.github.io/Blender_ioEDM/ )He made the difficult start decrypting the EDM files and thus laid the foundation and understanding for developing such addons. Also madwax (https://github.com/madwax/Blender_ioEDM ) who continued the work and kept NickD’s addon running with the DCS updates. I decided to begin from scratch for an exporter for blender 2.8 because things have changed in blender and I wanted to take a slightly different approach to some things. (use of armature) 

Releases:
---------

https://github.com/tobi-be/BlenderEdmExporter/releases

Be aware that this exporter is not thought to export downloaded models and have a working model with 2 or 3 clicks. 

Install:
--------

- Download io_BlenderEdmExporter.zip from asserts of the latest release. 
- Blender: Go to Edit->Preferences->Add-Ons, press install and choose downloaded zip file 
- Activate the add-on

Usefull links:
----------------

Tutorialplaylist: https://www.youtube.com/playlist?list=PLj_mUHDsU9x7hARVZsA1JilXWnTxJ4PpD  thanks Grajo!

DCS Forum about the exporter: https://forums.eagle.ru/showthread.php?t=268003 

DCS Forum about ingame integration: https://forums.eagle.ru/showthread.php?t=277572

What should work:
------------------

- Mesh export 
- Collision boxes
- Collision lines
- Argument based animations of Visibility, Rotation, Location, Scale and Bone deformation
- A selection of material types including diffuse, normal and specular maps. roughmet textures can be added later
- Damage texture layer
- Lights  
- Connectors
- Animation of materialparameters like diffuse shift, selfillumination
- Baking Animations EDM Export-ready

What doesn‘t work:
------------------

- Import EDM models
- LOD Nodes
- Export of modifiers (exception armature modifier which are used with skinnodes)

Known issues
------------

- Renaming bones after creating animation actions leads to errors. It is best to give all the final bone-names before you start animating
- If you add animations or additional skinnodes the modelviewer has to be restarted to load the model properly.
- In pose mode at bone properties at relations is an option named Relative Parenting. You should enable it. Otherwise objects will be exported at wrong positions.
- Make shure the root bone is not deforming. Otherwise it will lead to an error when using skin nodes  (Edit mode-> Bone Properties-> Deform no)
- Don't use fake user armatures.

General Rules:
==============

Export script could be buggy please report bugs
-----------------------------------------------

Always use an armature
----------------------

Use always an armature even if you don't want to animate anything. This addon uses an armature as the root of the edm model. In the highest hierarchical level must and may only be one bone. This bone then can have children with children of children as you like. Consider this bone as root of the whole edm model. Every (rigid) object you want to be rendered in DCS has to be parented to the armature relative to one bone. The only exception is a bone deformed mesh, which has to be parented to the armature also, but of cause not relative to only one bone. (See SkinNode)
The reason for the need of an armature goes back to the way of animations in blender:
In Blender an armature offers the possibility to handle animations, which contains several bones in one action in the Action Editor. Each bone can be a parent for one or more objects. If you animate the objects directly instead, only one body could be animated in one action. Therefore it was decided that geometric animations (rotation and translation) are only considered and exported using bones. So do not animate objects directly! Always use an Armature. For simplicity in programming, an armature is also required for models without animations

Auto rig objects
-------------------

To make some work easier there is a button "Auto Rig Object" in the EDM panel of an object. To use this function, there must already be an armature that is to be used for the export. 
If you press the button, a bone is automatically created in the origin of the object and the object is parented to the bone. The bone gets the same orientation as the object. The bone gets the name of the object and becomes a child of the root bone of the armature. Of course you can parent the bone to any other bone afterwards. 
If there is already a bone with the corresponding name, it will be repositioned to the object, keeping animations and former parent. So if you need to move objects you can use the button to update the bones. But you have to be careful never to apply the rotation and translation of the objects otherwise all bones will be set to (0,0,0) if you should press the button again.

Use quaternions always
----------------------

Other rotation representations are not supported

Read messageboxes and the system console
-----------------------

If there is a problem with your model the exporter complains about it with messageboxes. The messageboxes describe the problem and should help you fix the problem. Additionally the warnings/errors are printed to the system console. There you can read them again later: 
Window→Toggle System Console. 

Bone Layers
-----------

Every bone which should be used in the EDM model has to be on bone layer 1. Bones on other bone layers are not exported. This allows you to use IK-bones or pole bones on other layers. Make sure every model-relevant bone is on layer 1. 

First steps to create a simple model:
=====================================

- Create an armature
- Create a mesh object
- Parent mesh object relative to one bone oft the armature
In object mode: select mesh object first, add armature to selection, go to pose mode, click on 	the bone you wish, press (strg+p) choose “Bone Relative”
- assign a material to the mesh object. (this is just a dummy right now but it has to be done)
- check UV-Map (a mesh object has to have a UV-Map)
- Select mesh object. In Object Properties should be a EDM-Panel choose RenderNode as rendertype. 
- Adjust the material settings in the EDM-Panel for this mesh. (See Materials)
- export: File->export->Export .EDM to export the model 

Supported Features
==================

Bounding Box and User Box
-------------------------

By default user box and Bounding Box are calculated by the overall dimensions of your model. In some cases you need to adjust them. For example for cargo objects or if an animation leads to greater dimensions. You can adjust them by selecting the armature. In the object properties -> EDM Panel you can choos if you want to uses calculated boxes or if you want to define them directly. In the second case you can the coordinates of the low, left, rear corner(min) and he right, upper, front corner (max)

Materials
---------

As a first approach, the EDM panel offers material properties that are present in the existing EDM files. The settings are independent of the Blender materials and have no influence on the appearance in Blender. Also, the Blender internal material properties have no influence on the exported EDM model. First you have to select a general material type. The type names should be almost self-explanatory. The most commonly used material type should be Solid (def_material in  EDM files). I have not spent much time with the different types and their appearance. Therefore I would not write much here, just a short description of the application and wish every user a lot of fun while experimenting.
Each material has a diffuse color map. The name of the texture to be used must be entered in the filename field. In general, textures are always entered without file names. If you want to use "texture.png" there must be "texture" there. Using Normalmaps and specular maps is optional. If you want to use _roughmet textures you have to enable a specular map. (See: https://forums.eagle.ru/showthread.php?t=193596 )

There is a new experimental feature of the self-illuminated and diffuseshift animation. You can add animated illumination and diffuseshift values. You have to choose the animation argument in the EDM Panel. There you can keyframe the illumination or diffuseshift value by pressing I when the mouse is over the field of the value. The curves of the added keyframes won't appear at the action editor but when you choose dopesheet in the dropdownmenu of the dopesheet-viewer. You also don't have to create an action for that like you have to when you want to animate bones.

RenderNode
----------

A RenderNode is a non deformable mesh object. Like chassis, wheels, planes, etc. RenderNodes can be animated (rotation and translation) by animating the bone to which it is parented. You have to assign a material to the mesh object. (this is just a dummy right now but it has to be done)

ShellNode
---------

A ShellNode are the collision boxes in EDM files mesh object. ShellNodes have to be parented to a bone and can be animated (rotation and translation) by animating its bone. 

Collision lines
---------------

Collision lines are modelled with SegmentsNodes as the rendertype. Segmentsnodes have to be parented to a bone and can be animated by animating its bone. To create collision lines create a mesh object and parent it to a bone. Every edge of the mesh is exported as a line segment of the collision line.

SkinNode
--------
A SkinNode is a bone deformed Mesh used for infantry, pilots, driver or cows. 
Workflow to create a SkinNode would be: 
- Make shure the root bone is not deforming.
- Create the mesh and choose SkinNode at EDM-Panel, setup material parameters and UV-map
- Apply tranformation of the mesh object.
- Prepare the armature. In Blender every bone has the property „Deform“ in Bone Properties. If you want to use a SkinNode you should disable this option on every bone which should not deform the mesh. 
- Parent the mesh to the armature with automatic weights and correct weight painting.  

SkinNodes in DCS uses up to 4 bone weights per vertex. In Blender you have to limit the number of weights to 4 to get a proper export. This can be done in weight paint mode at weights→limit total. You have to assign a material to the mesh object. (this is just a dummy right now but it has to be done)
There might be some problems with exporting Skinnodes. I would be happy about every  description of problems.
If something is not working properly and you want to begin from scratch you should should delete the vertexgroups, which are created by using "parent with automatic weights". 

If you don't want to delete the vertex groups because you already put a lot of effort to weight painting you should make shure that there is a vertexgroup for each deforming bone (same name) and NO vertex groups for none deforming bones.

Connectors
----------

To place a connector in the EDM model you have to place an empty-object in Blender. The name of the connector will be the name of the empty Object. In the EDM-Panel in Object Properties you have to choose Connector at EmptyType.


Light
-----

To place a light in the edm model, you have to place an empty-object and choose Light as Emptytype. The color of the emitted light is specified in RGB. You can also adjust brightness and distance. If you enable spotlight you can adjust the opening angle of the spot with theta. 

The brightness of the light can be animated. You have to choose the animation argument in the EDM Panel. There you can keyframe the brightness value by pressing I when the mouse is over the field of the value. The curves of the added keyframes won't appear at the action editor but when you choose dopesheet in the dropdownmenu of the dopesheet-viewer. You also don't have to create an action for that like you have to when you want to animate bones.

Animation
---------

The way to animate the model is to animate the armature of the model. Every thing which is supposed to show up in DCS has to be parented to the armature. 
The general concept is to use actions of the armature to define argument based animations. Open the Dopesheet Editor and switch to Action Editor. Create a new action in pose mode of the armature  by clicking on new action in Action Editor. You can select a bone and insert keyframes by hitting I. You can use Rotation, Location and Scale. Use only full sets of rotation, locations or scales in the meaning of keyframing all 4 quaternion properties or all 3 location/scale coordinates. If you use Location, Rotation or scale make sure to insert keyframes for each of them at the start and at the end of the action. The start and the end of the action is defined by the first and the last keyframes respectively. 
The Action Editor contains an additional panel. This is a bit hidden on the right side of the window. When you expand it you will find the Export to EDM option and the animation argument. You should make every action a fake user. Otherwise the action could be deleted when saving and loading

VisibilityNode
--------------

A VisibilityNode is the way to let an object appear oder disappear with an argument. This is the only thing which has to be animated on an object directly. Select the Object which should get a VisibilityNode in Object Mode. Open dopesheet editor and switch to Action Editor. Create a new Action. Setup Export to EDM and argument. In Object Properties at Visibility there is a property named „Show in Renders“.  Move mouse over it, change the value as you need and press „I“ to insert a keyframe. Change frame and the value and repeat. One can add multiple visibilty animation actions for one object.
There is a list at the edm property panel. A click on + adds the active object animation action to the list. You can create a new action and add it also to the list. Every action in the list will be exported as a visibilty track although it may not be the active animation data of the action. export the VisibilityNode the action has to kept in the animaton data of the object. The animation data of the object is independent of the animation data of the armature. 

Baking Animations
-----------------
Actions can be baked by pressing the Bake Action Button at the EDM Action Panel. You can adjust the frame range. Using IK-Chains and other bone contraints becomes very handy with that.
