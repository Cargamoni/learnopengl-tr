---
title: Teori
keywords: 
last_updated: 
tags: []
permalink: /pbr/theory.html
sidebar: main_sidebar
---

 PBR, or more commonly known as physically based rendering is a collection of render techniques that are more or less based on the same underlying theory which more closely matches that of the physical world. As physically based rendering aims to mimic light in a physically plausible way it generally looks more realistic compared to our original lighting algorithms like Phong and Blinn-Phong. Not only does it look better, as it closely approximates actual physics, we (and especially the artists) can author surface materials based on physical parameters without having to resort to cheap hacks and tweaks to make the lighting look right. One of the bigger advantages of authoring materials based on physical parameters is that these materials will look correct regardless of lighting conditions; something that is not true in non-PBR pipelines.

Physically based rendering is still nonetheless an approximation of reality (based on the principles of physics) which is why it's not called physical shading, but physically based shading. For a PBR lighting model to be considered physically based it has to satisfy the following 3 conditions (don't worry, we'll get to them soon enough):

1. Be based on the microfacet surface model.
2. Be energy conserving.
3. Use a physically based BRDF.

In this PBR tutorial series/guide we'll be focusing on the PBR approach as originally explored by Disney and adopted for real-time display by Epic Games. Their approach based on the metallic workflow is decently documented, widely adopted on most popular engines and looks visually amazing. By the end of the series we'll have something that looks like this: 

<img src ="https://learnopengl.com/img/pbr/ibl_specular_result_textured.png">

Keep in mind the topics in this tutorial series are rather advanced so it is advised to have a good understanding of OpenGL and shader lighting. Some of the more advanced knowledge you'll need for this series are: framebuffers, cubemaps, gamma correction, HDR and normal mapping. We'll also delve into some advanced mathematics, but I'll do my best to explain the concepts as clear as possible. 

## The microfacet model

 All the PBR techniques are based on the theory of microfacets. The theory describes that any surface at a microscopic scale can be described by tiny little perfectly reflective mirrors called microfacets. According to the roughness of a surface the alignment of these tiny little mirrors can differ quite a lot: 
 
<img src ="https://learnopengl.com/img/pbr/microfacets.png">

 The rougher a surface is, the more chaotically aligned each microfacet will be along the surface. The effect of these tiny-like mirror alignments is that when specifically talking about specular lighting/reflection the incoming light rays are more likely to scatter along completely different directions on rougher surfaces, resulting in a more widespread specular reflection. In contrast, on a smooth surface the light rays are more likely to reflect in roughly the same direction, giving us smaller and sharper reflections: 
 
 <img src="https://learnopengl.com/img/pbr/microfacets_light_rays.png">
 
 No surface is completely smooth on a microscopic level, but seeing as these microfacets are small enough that we can't make a distinction between them on a per-pixel basis we statistically approximate the surface's microfacet roughness given a roughness parameter. Based on the roughness of a surface we can calculate the ratio of microfacets roughly aligned to some vector h. This vector h is the halfway vector that sits halfway between the light l and view v vector. We've discussed the halfway vector before in the advanced lighting tutorial which is calculated as the sum of l and v divided by its length: 
 
 
