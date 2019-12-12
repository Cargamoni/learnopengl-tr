---
title: Basit Işıklandırma
keywords: 
last_updated: 
tags: []
permalink: /lighting/basic_lighting.html
sidebar: main_sidebar
---

# Basit Işıklandırma
Gerçek dünyada ışıklandırma son derece karmaşıktır ve sahip olduğumuz sınırlı işlem gücüne göre hesaplayamayacağımız çok fazla faktöre bağlıdır. Bu nedenle OpenGL'deki ışıklandırma, işlenmesi ve nispeten benzer görünmesi daha kolay olan basitleştirilmiş modeller kullanılarak gerçeğe yakınlığa dayanmaktadır. Bu ışıklandırma modelleri, anladığımız gibi ışığın fiziğine dayanır. Bu modellerden birine Phong aydınlatma modeli denir. Phong modelinin ana yapı taşları 3 bileşenden oluşur: ambient, diffuse ve specular ışıklandırma. Aşağıda bu ışıklandırma bileşenlerinin gerçekte nasıl göründüğünü görebilirsiniz:

<img src="https://learnopengl.com/img/lighting/basic_lighting_phong.png">

* Ambient Işıklandırma: Karanlık olsa bile, genellikle dünyanın herhangi bir yerinde bir miktar ışık vardır (ay, uzak bir ışık), yani nesneler neredeyse hiç tamamen karanlık olmazlar. Bunu benzetimlemek için nesneye daima renk veren bir ambient ışık sabiti kullanırız.

* Diffuse Işıklandırma: Bir ışık nesnesinin başka bir nesne üzerindeki yönsel etkisini benzetimler. Bu, ışıklandırma modelinin görsel olarak en önemli bileşenidir. Bir nesnenin ışık alan kısmı ne kadar çoksa o kadar parlak olur.

* Specular Işıklandırma: Parlak nesnelerde görünen ışığın parlak noktasını benzetimler. Specular parlak noktalar, nesnenin renginden çok, ışığın rengine meyillidir.

Görsel olarak ilginç sahneler oluşturmak için en azından bu 3 ışıklandırma bileşenini benzetimlemek istiyoruz. En basitiyle başlayacağız: Ambient ışıklandırma.

## Ambient Işıklandırma
