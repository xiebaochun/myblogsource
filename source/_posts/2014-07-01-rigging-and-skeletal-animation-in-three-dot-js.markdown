---
layout: post
title: "rigging_and_skeletal_animation_in_three.js"
date: 2014-07-01 11:16
comments: true
categories: three.js
---
JavaScript
and stuff
github.com/roman01la
Rigging and skeletal animation in Three.js
Update: Do not move/rotate the armature, this will cause geometry stretching. Align the first bone using its tail/head position
Three.js supports some basic rigging and skeletal animation. The simplest way to achieve a nice character animation is to make it in appropriate software first. This article is going to be about Blender rigging workflow and how to do the best for a clean Three.js export.
Here’s a very nice and detailed article about rigging and animating in Blender with further exporting and scene setup in Three.js. Actually I got this thing after reading the article. Here’s my, actually same, workflow, but with some tips and additions.
That’s what we’ll get when all thing will be done.

Lets create a simple shape. You’ll need Blender itself and exporter script, check the article about external models to find out.
Create a mesh
Remove camera and light from the scene and extrude the box. Select it with RMB, go into Edit mode with Tab, hit A to unselect the object, hit Ctrl+Tab and select Face select mode. Select one face with RMB, hit E, hold Ctrl and drag the mouse to extrude the face. Drag it until you get another cube in same size. Click LMB and release Ctrl key. Do the process for one more time to make another cube.

Create an armature
Now, add an armature. Hit Z to go to Wireframe view mode. Hit Shift+A, select Armature -> Single Bone, this will add a bone. Rotate and position the bone as shown below. Position the bone using its tail/head position. Select armature, go to Edit mode, select bone, hit E, hold Ctrl and drag to create a new bone. Create another two bones, so you’ll get four bones armature.

Create and assign vertex groups
So here we have a mesh and an armature, the next step is to assign the geometry to the appropriate bones. It can be done in two ways: automatic assignment and manual. I prefer manual, you can choose assignment polygons more precisely.
Each set of polys is called a vertex group, and the name of the group should be exact same like the name of the bone we want assign to. Usually, in Blender, the first bone name is Bone, the second one is Bone.001, the next is Bone.002 and so on.
Select the mesh and go to Edit mode. Go to Object Data tab in the Properties panel. Hit + under the Vertex Groups list to add a new group, call it Bone.001, this is the second bone of the armature. Choose Face select mode and select all five faces of the first cube with Shift+RMB. Once you’ve done make sure that the right vertex group is chosen, hit Assign button under Vertex Groups. Now the first cube geometry will be transformed with its bone. You can check the assignment by selecting a vertex group and hit Select button, if there are extra faces, select only them and hit Remove button. Repeat the process for the rest of the mesh.
Tip: For more complex geometry to create nearby vertex groups and make sure that there are no same faces assigned to both, create the first vertex group, select it in the list, hit select to select its faces, select all faces of the second vertex group which are the nearest to first group faces, make sure the first vertex group is selected, hit Deselect and you’ll get edge selection which is a boundary between two groups. Now you create the second vertex group based on that line.
Tip: When the armature is about to have a static geometry, read further about it, it also must be assigned to its bone, otherwise it won’t be visible. So, we need to select all the faces that are not belong to any other vertex groups. The trick here is to select all groups (choose group in the list and hit Select) and then hit Ctrl+I which states for Inverse Selection, selected faces can be assigned to static vertex group.

There’s one more action required to finish with assignment. We need to parent the mesh to the armature. Go to Object mode, select mesh, go to Object tab in the Properties panel and hit Parent box under relations section, select Armature object. Hit the box below and select Armature from the list. If the mesh has changed its position, align it back to the bones.
Animate the armature
The armature is ready for animation. Make sure the mark is in the first frame position on the timeline. Select the armature and go to Pose mode (Ctrl+Tab), select the bone, hit I to add a keyframe and choose LocRotScale. Move the mark to frame 10 and move the bone, add another keyframe. Do it with all bones and play with position and time, except the first one. The first bone is the support bone and it should be static. Do not change its position, just add a keyframe in the first frame. This is useful when you are doing a hand animation, for example, where only fingers are moving and the hand itself should be static. Select the last keyframe of the animation and hit E to set the end of the anim. Three.js will play the animation from the first to the last defined keyframes. That means if you have a gap before the first keyframe, it won’t be played. Just add another keyframe in the first frame position to make that gap playable in Three.js.

In the article I’ve mentioned at the beginning there’s a section about removing unused actions of the animation, to make sure Three.js will play the right one. This is a good point, but is not required if you are making all stuff carefully. But still, read about it.
Export the model
If everything is ok and you are happy with your skeleton and anim, it’s time to export things. Make sure you have installed exporter script. Select both mesh and armature. Go to File -> Export -> Three.js. Enable the next options in the Export Three.js section: skinning, bones and skeletal animation. Export the model.
Setup Three.js scene
Use a scene setup from one of the previous articles, adjust light and camera position.
We need a loader to load the model, create SkinnedMesh instance using model geometry and materials and enable skinning on its materials.
var loader = new THREE.JSONLoader();
var animation;

// load the model and create everything
loader.load('model.js', function (geometry, materials) {
  var mesh, material;

  // create a mesh
  mesh = new THREE.SkinnedMesh(
    geometry,
    new THREE.MeshFaceMaterial(materials)
  );

  // define materials collection
  material = mesh.material.materials;

  // enable skinning
  for (var i = 0; i < materials.length; i++) {
    var mat = materials[i];

    mat.skinning = true;
  }

  scene.add(mesh);

  // add animation data to the animation handler
  THREE.AnimationHandler.add(mesh.geometry.animation);

  // create animation
  animation = new THREE.Animation(
    mesh,
    'ArmatureAction',
    THREE.AnimationHandler.CATMULLROM
  );

  // play the anim
  animation.play();

  render();
});

function render() {
  animation.update(.01);
  renderer.render(scene, camera);
  requestAnimationFrame(render);
}
9 months ago
#webgl  #threejs  #blender  #rigging  #skeletal animation
8 notes

mashabatsea likes this

jhclaura likes this

laugharne-me reblogged this from roman01la

recto-ver-so likes this

oivoodoo likes this

paraknight likes this

prostheticknowledge likes this

datahacker likes this

roman01la posted this
Ashley theme by Jxnblk
