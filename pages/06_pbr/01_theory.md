---
title: Teori
keywords: 
last_updated: 
tags: []
permalink: /pbr/theory.html
sidebar: main_sidebar
---

 PBR ya da daha yaygın olarak bilinen ismiyle Fiziksel Temelli Sahneleme (ing. Physically Based Rendering) fiziksel dünyadaki teoriyle aynı temelde birleşen sahneleme tekniğidir. Fiziksel Temelli Sahneleme, ışığı fiziksel olarak makul bir şekilde taklit etmeyi amaçlar. Phong ve Blinn-Phong gibi orijinal aydınlatma algoritmalarına kıyasla genellikle daha gerçekçi görünür. Sadece daha iyi görünmekle kalmaz, aynı zamanda gerçek fiziğe yaklaştığı için, aydınlatmanın doğru sağlanması için bayağı ve ince ayarlara başvurmaya gerek olmaksızın yüzey materyalleri oluşturabiliriz. Fiziksel parametrelere dayalı materyallerin en büyük avantajlarından biri, aydınlatma koşullarından bağımsız olarak bu materyallerin doğru görünmesidir. Bu, PBR olmaksızın iş hatlarında sağlanamayan bir şeydir.
 
Fiziksel temelli renderleme gerçekliğin bir yaklaşımıdır (fizik prensiplerine dayandırılarak), bu yüzden fiziksel gölgeleme değil, fiziksel temelli gölgeleme olarak adlandırılır. Bir PBR aydınlatma modelinin fiziksel olarak temel alınması için aşağıdaki 3 şartı yerine getirmesi gerekir:

1. Mikro-yüzey yüzey modeline sahip olmak
2. Enerji tasarruflu olmak
3. Fiziksel tabanlı BRDF kullanmak

Bu PBR eğitselinde başlangıçta Disney tarafından keşfedilen ve Epic Games tarafından gerçek zamanlı gösterim için kabul edilen PBR yaklaşımına odaklanacağız. Metalik iş akışına dayanan yaklaşımları oldukça belgeleştirilmiştir, çoğu popüler motorda yaygın olarak kullanılmaktadır ve görsel olarak harika görünmektedir. Serinin sonunda şuna benzeyen bir şeye sahip olacağız:

<img src ="https://learnopengl.com/img/pbr/ibl_specular_result_textured.png">

Bu eğitseldeki kısımların oldukça ileri seviye olduğunu unutmayın, bu yüzden OpenGL ve gölgelendirici aydınlatma konularını iyi anlayabilmeniz önerilir. Bu seri için ihtiyaç duyacağınız ileri seviye bilgilerden bazıları şunlardır: framebuffer, cubemap, gama düzeltmesi, HDR ve normal haritalama. Ayrıca ileri seviye matematiğe de gireceğiz, ancak kavramları mümkün olduğunca net bir şekilde açıklamak için elimden geleni yapacağım.

## Mikro-yüzey Model

 All the PBR techniques are based on the theory of microfacets. The theory describes that any surface at a microscopic scale can be described by tiny little perfectly reflective mirrors called microfacets. According to the roughness of a surface the alignment of these tiny little mirrors can differ quite a lot: 
 
<img src ="https://learnopengl.com/img/pbr/microfacets.png">

 The rougher a surface is, the more chaotically aligned each microfacet will be along the surface. The effect of these tiny-like mirror alignments is that when specifically talking about specular lighting/reflection the incoming light rays are more likely to scatter along completely different directions on rougher surfaces, resulting in a more widespread specular reflection. In contrast, on a smooth surface the light rays are more likely to reflect in roughly the same direction, giving us smaller and sharper reflections: 
 
 <img src="https://learnopengl.com/img/pbr/microfacets_light_rays.png">
 
 No surface is completely smooth on a microscopic level, but seeing as these microfacets are small enough that we can't make a distinction between them on a per-pixel basis we statistically approximate the surface's microfacet roughness given a roughness parameter. Based on the roughness of a surface we can calculate the ratio of microfacets roughly aligned to some vector h. This vector h is the halfway vector that sits halfway between the light l and view v vector. We've discussed the halfway vector before in the advanced lighting tutorial which is calculated as the sum of l and v divided by its length: 
 
 
