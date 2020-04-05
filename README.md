# Fucking-Morph-Target-Animations
Showcase of current GLTF export bug with morph target animations that don't target the root node being exported.


There is a bug with the GLTF Exporter in threejs. When merging morphtarget tracks, the code in the GLTF Exporter currently hardcodes the track name to be '.morphTargetInfluences'.


To refresh your memory on the purpose of Animation track names, they essentially serve as map to determine which property should be influenced by the animation. Checking out this doc on parsing track names is a good way to see how much variety there is:


https://threejs.org/docs/#api/en/animation/PropertyBinding.parseTrackName


# The Problem

So the problem with hardcoding the track name as '.morphTargetInfluences' is that it ignorantly assumes the animation tracks being merged are targeting the root node of the export. However, in cases where the unmerged morph target animation tracks are targeting children nodes of the root export node, those targets are lost and the exporter will fail to create any .gltf file.


This repo is an example which demonstrates this bug. You can check out a live version here:
https://threejs-demos.s3.us-east-2.amazonaws.com/bug.html


The demo has two buttons: "Good Export" and "Bad Export".

Good export simply exports an object3d with morph targets and an animation as a .gltf file.

Bad export will attempt to do the same thing, except it exports a group containing the object3d that is being targeted by the morphtarget animation. This fails and you can see the error in console.

# The Solution

If you replace the unnmodified `GLTFExporter.js` with my `GLTFExporterFixed.js` which contains my proposed changes, you'll then be able to run the "Bad Export" without error. I even tested the file output in Dom's GLTF2Viewer and it came in clean with no errors or warnings.




It is an easy fix simply changing:

```				mergedTrack.name = '.morphTargetInfluences';```



To:




```				mergedTrack.name = sourceTrack.name;```






This way the mergedTrack animation inherits the correct target from the tracks it's combining.
