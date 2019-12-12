---
title: Renkler
keywords: 
last_updated: 
tags: []
permalink: /lighting/colors.html
sidebar: main_sidebar
---
Önceki eğitsellerde OpenGL'de renklerle nasıl çalışılacağını kısaca açıkladık, ancak şimdiye kadar sadece renklerin yüzeyine dokunduk. Bu bölümde, renklerin ne olduğunu yoğun bir şekilde tartışacağız ve yaklaşmakta olan aydınlatma dersleri için sahne oluşturmaya başlayacağız.

Gerçek dünyada renkler, her nesnenin kendi rengine / renklerine sahipken pratikte bilinen herhangi bir renk değerini alabilir. Dijital dünyada (sonsuz) gerçek renkleri (sınırlı) dijital değerlerle eşlememiz gerekir ve bu nedenle tüm gerçek dünya renkleri dijital olarak gösterilemez. Ancak o kadar çok rengi temsil edebiliriz ki muhtemelen farkı asla farketmezsiniz. Renkler, genel olarak RGB olarak kısaltılan kırmızı, yeşil ve mavi bir bileşen kullanılarak dijital olarak temsil edilir. Sadece bu 3 değerin farklı kombinasyonlarını kullanarak, hemen hemen her rengi temsil edebiliriz. Örneğin, mercan rengi elde etmek için bir renk vektörünü şöyle tanımlarız:

```cpp
glm::vec3 coral(1.0f, 0.5f, 0.31f); 
```

Gerçek hayatta gördüğümüz renkler, nesnelerin gerçekte sahip olduğu renkler değil, nesneden yansıyan renklerdir; nesneler tarafından emilmeyen (reddedilen) renkler, onları algıladığımız renklerdir. Örneğin, güneşin ışığı (resimde gördüğünüz gibi) birçok farklı rengin birleştirilmiş toplamı olan beyaz bir ışık olarak algılanır. Dolayısıyla, beyaz ışığı mavi bir oyuncak üzerinde parlatırsak, mavi rengin dışındaki tüm beyaz rengin alt renklerini emer. Oyuncak mavi değeri absorbe etmediğinden yansıtılır ve bu yansıyan ışık gözümüze girerek oyuncağın mavi renge sahip gibi görünmesini sağlar. Aşağıdaki görüntü mercan renginde bir oyuncak için bunu gösterir.

<img src="https://learnopengl.com/img/lighting/light_reflection.png">

Beyaz güneş ışığının aslında tüm görünür renklerin bir birleşimi olduğunu ve nesnenin bu renklerin büyük bir kısmını emdiğini görebilirsiniz. Sadece nesnenin rengini temsil eden renkleri yansıtır ve onların birleşimi bizim algıladığımız şeydir (bu durumda mercan rengi).

Bu renk yansıma kuralları doğrudan grafik alanı ile ilişkilidir. OpenGL'de bir ışık kaynağı tanımladığımızda, bu ışık kaynağına bir renk vermek istiyoruz. Önceki paragrafta beyaz bir renge sahiptik, bu ışık kaynağına da beyaz renk vereceğiz. Işık kaynağının rengini bir nesnenin renk değeri ile çarparsak, ortaya çıkan renk nesnenin yansıtılan rengidir (ve dolayısıyla algılanan rengi). Oyuncağımızı tekrar gözden geçirelim (mercan renk değeri olan) ve grafik dünyasında algılanabilir rengini nasıl hesaplayacağımızı görelim. Her iki renk vektöründe bileşen tabanlı bir çarpma işlemi yaptığımızda elde edilen renk vektörü:

```cpp
glm::vec3 lightColor(1.0f, 1.0f, 1.0f);
glm::vec3 toyColor(1.0f, 0.5f, 0.31f);
glm::vec3 result = lightColor * toyColor; // = (1.0f, 0.5f, 0.31f);
```

Cismin renginin beyaz ışığın büyük bir kısmını emdiğini, ancak kendi renk değerine bağlı olarak birkaç kırmızı, yeşil ve mavi değeri yansıttığını görebiliriz. Bu, renklerin gerçek hayatta nasıl işlediğini göstermektedir. Böylece bir nesnenin rengini, ışık kaynağından yansıttığı her renk bileşeninin miktarı olarak tanımlayabiliriz. Şimdi yeşil bir ışık kullanırsak ne olur?

```cpp
glm::vec3 lightColor(0.0f, 1.0f, 0.0f);
glm::vec3 toyColor(1.0f, 0.5f, 0.31f);
glm::vec3 result = lightColor * toyColor; // = (0.0f, 0.5f, 0.0f);
```

Gördüğümüz gibi, cisim emebilecek ve/veya yansıtabilecek kırmızı ve mavi ışığa sahip değildir. Ayrıca cisim, ışığın yeşil değerinin yarısını emer, ancak ışığın yeşil değerinin yarısını da yansıtır. Cismin algıladığımız rengi koyu yeşilimsi bir renk olur. Yeşil bir ışık kullanırsak, sadece yeşil renk bileşenlerinin yansıtılabileceğini ve bu sayede algılanabileceğini görebiliriz ki kırmızı ve mavi renkler algılanmaz. Sonuç, mercan rengi nesne aniden koyu yeşilimsi bir nesne haline gelmesidir. Koyu zeytin yeşili ışıkla bir örnek daha deneyelim:

```cpp
glm::vec3 lightColor(0.33f, 0.42f, 0.18f);
glm::vec3 toyColor(1.0f, 0.5f, 0.31f);
glm::vec3 result = lightColor * toyColor; // = (0.33f, 0.21f, 0.06f);
```

Gördüğünüz gibi, farklı ışık renkleri kullanarak nesnelerden farklı renkler elde edebiliriz. Renklerle çalışırken yaratıcı olmak zor değil.

Ama renkler hakkında bu kadar bilgi yeterli, haydi deney yapabileceğimiz bir sahne kurmaya başlayalım.

## Bir Işıklandırma Sahnesi
Gelecek eğitsellerde gerçek dünya ışıklandırmasını renklerin yoğun şekilde kullanılmasını benzetimleyerek ilginç görseller yaratacağız. Sahnede görsel nesneler olarak ifade etmek istediğimiz ışık kaynaklarını kullanacağız ve aydınlatmayı benzetimlemek için en az bir nesne ekleyeceğiz.

İhtiyacımız olan ilk şey, ışığı açmak için bir nesne. Bunun için önceki derslerden gelen meşhur konteyner küpünü kullanacağız. Işık kaynağının 3B sahnede nerede olduğunu göstermek için bir ışık nesnesine de ihtiyacımız olacak. Sadelik için, ışık kaynağını bir küp ile temsil edeceğiz (zaten [köşe verilerine](https://learnopengl.com/code_viewer.php?code=getting-started/cube_vertices) sahip değil miyiz?).

Bir köşe tampon nesnesini (ing. vertex buffer object) doldurmak, köşe nitelik işaretçileri(ing. vertex attribute pointers) ve tüm bu garip şeyleri ayarlamak artık sizin için kolay olmalı. Bu nedenle sizi bu adımlardan geçirmeyeceğiz. Bu konularla ilgili hala zorluk yaşıyorsanız, [önceki eğitselleri](https://cg-translators.github.io/learnopengl-tr/getting_started/hello_triangle.html) incelemenizi ve devam etmeden önce mümkünse alıştırmaları gözden geçirmenizi öneririm.

Gerçekte ihtiyacımız olacak ilk şey, konteyneri çizmek için bir köşe gölgelendiricisi (ing. vertex shader). Konteynerin köşe konumları aynı kalır (bu sefer doku koordinatlarına ihtiyacımız olmamasına rağmen), böylece kod aynı kalır. Son eğitsellerdeki köşe gölgelendiricisinin sadeleşmiş bir sürümünü kullanacağız:

```glsl
#version 330 core
layout (location = 0) in vec3 aPos;

uniform mat4 model;
uniform mat4 view;
uniform mat4 projection;

void main()
{
    gl_Position = projection * view * model * vec4(aPos, 1.0);
} 
```
Köşe verilerinizi güncellediğinizden ve işaretçilerinizi yeni köşe gölgelendiricisine uygun olacak şekilde güncellediğinizden emin olun.

Ayrıca bir lamba küpü yaratacağımızdan, özellikle lamba için yeni bir VAO üretmek istiyoruz. Aynı VAO'yu kullanan bir lambayı da temsil edebiliriz ve sonra sadece model matrisinde bazı dönüşümler yapabiliriz, ancak gelecek derslerde köşe verilerini değiştireceğiz ve konteyner nesnesinin işaretçilerine çok sık atıfta bulunacağız ve bu durumla karşılaşmak istemiyoruz. Yalnızca lambanın köşe noktalarını önemsiyoruz, bu yüzden yeni bir VAO oluşturuyoruz:

```cpp
unsigned int lightVAO;
glGenVertexArrays(1, &lightVAO);
glBindVertexArray(lightVAO);
// we only need to bind to the VBO, the container's VBO's data already contains the correct data.
glBindBuffer(GL_ARRAY_BUFFER, VBO);
// set the vertex attributes (only position data for our lamp)
glVertexAttribPointer(0, 3, GL_FLOAT, GL_FALSE, 3 * sizeof(float), (void*)0);
glEnableVertexAttribArray(0);
```


```glsl
#version 330 core
out vec4 FragColor;
  
uniform vec3 objectColor;
uniform vec3 lightColor;

void main()
{
    FragColor = vec4(lightColor * objectColor, 1.0);
}
```
Kod nispeten basit olmalıdır. Artık hem konteyneri hem de lamba küpünü yarattığımıza göre, tanımlanacak bir şey kaldı ve bu da fragment shader:

```glsl
#version 330 core
out vec4 FragColor;
  
uniform vec3 objectColor;
uniform vec3 lightColor;

void main()
{
    FragColor = vec4(lightColor * objectColor, 1.0);
}
```

Fragment shader, uniform bir değişkenden hem nesne rengini hem de ışık rengini alır. Burada ışığın rengini, bu eğitselin başında tartıştığımız gibi nesnenin (yansıyan) rengiyle çarpıyoruz.Anlaşılması oldukça kolay olmalı. Nesnenin rengini en son bölümün mercan rengine beyaz bir ışıkla ayarlayalım:

```cpp
// ilgili shader programın ayarlanması için "use" fonksiyonunu kullanıyoruz
lightingShader.use();
lightingShader.setVec3("objectColor", 1.0f, 0.5f, 0.31f);
lightingShader.setVec3("lightColor",  1.0f, 1.0f, 1.0f);
```

Unutulmaması gereken bir şey, vertex ve fragment gölgelendiricileri değiştirmeye başladığımızda, lamba küpünün de değişeceği ve bu durum istediğimiz şey olmadığıdır. Lamba nesnesinin renginin gelecek derslerde ışıklandırma hesaplamalarından etkilenmesini istemiyoruz, aksine lambayı diğerlerinden uzak tutuyoruz. Lambanın diğer renk değişikliklerinden etkilenmeyen, sürekli parlak bir renge sahip olmasını istiyoruz (bu, lambanın gerçekten ışık kaynağı gibi görünmesini sağlar).

Bunu başarmak için, lambayı çizdirmekte kullanacağımız ikinci bir gölgelendirici seti oluşturmamız gerekir, böylece aydınlatma gölgelendiricilerindeki değişikliklerden korunmuş oluruz. Köşe gölgelendiricisi, mevcut köşe gölgelendiricisi ile aynıdır, bu nedenle lambanın köşe gölgelendiricisi için kaynak kodunu kolayca kopyalayabilirsiniz. Lambanın fragment gölgelendiricisi, lambadaki sabit beyaz renk tanımlayarak lambanın renginin parlak kalmasını sağlar:

```glsl
#version 330 core
out vec4 FragColor;

void main()
{
    FragColor = vec4(1.0); // set all 4 vector values to 1.0
}
```

Nesnelerimizi çizmek istediğimizde, henüz tanımladığımız ışıklandırma gölgelendiricisini kullanarak konteyner nesnesini (veya muhtemelen diğer pek çok nesneyi) çizmek istiyoruz ve lambayı çizmek istediğimizde, lambanın gölgelendiricilerini kullanıyoruz. Eğitseller sırasında, daha gerçekçi sonuçlar elde etmek için ışıklandırma gölgelendiricilerini kademeli olarak güncelleyeceğiz.

Lamba küpünün asıl amacı ışığın nereden geldiğini göstermektir. Genelde bir ışık kaynağının konumunu sahnenin herhangi bir yerinde tanımlarız, ancak bu sadece görsel anlamı olmayan bir konumdur. Gerçek lambayı göstermek için, lamba küpünü ışık kaynağının aynı yerine çekiyoruz. Bu, lamba nesnesini gölgelendirici ile çizerek gerçekleştirilir ve bu sahnenin ışık koşullarından bağımsız olarak lamba küpünün her zaman beyaz kalmasını sağlar.

Öyleyse, ışık kaynağının dünya-uzay koordinatlarındaki yerini temsil eden genel bir vec3 değişkeni bildirelim:

```cpp
glm::vec3 lightPos(1.2f, 1.0f, 2.0f);
```

Daha sonra, lamba küpünü çizdirmeden önce ışık kaynağının konumuna çevirmek istiyoruz ve lambanın çok baskın olmadığından emin olmak için biraz da küçülteceğiz:

```cpp
model = glm::mat4(1.0f);
model = glm::translate(model, lightPos);
model = glm::scale(model, glm::vec3(0.2f)); 
```

Lambanın son çizim kodu aşağıdaki gibi görünmelidir:
```cpp
lampShader.use();
// set the model, view and projection matrix uniforms
...
// draw the lamp object
glBindVertexArray(lightVAO);
glDrawArrays(GL_TRIANGLES, 0, 36);	
```

Tüm kod parçalarını uygun yerlerine yerleştirmek, ışıklandırma ile uygun şekilde yapılandırılmış temiz bir OpenGL uygulamasıyla sonuçlanacaktır. Her şey derlenirse şöyle görünmesi gerekir:

<img src ="https://learnopengl.com/img/lighting/colors_scene.png">

Şu anda gözden geçirilecek pek bir şey yok, ama gelecek derslerde daha ilginç olacağına söz veriyorum.

Tüm kod parçalarının uygulamada bir bütün olarak nereye uyduğunu bulmakta güçlük çekiyorsanız, [buradaki](https://learnopengl.com/code_viewer_gh.php?code=src/2.lighting/1.colors/colors.cpp) kaynak kodunu kontrol edin ve kod / yorumlar arasında dikkatli bir şekilde çalışın.

Şimdi renkler hakkında oldukça fazla bilgiye sahibiz ve bazı aydınlatma eşyaları için temel bir sahne yarattık, gerçek büyünün başladığı [bir sonraki](https://cg-translators.github.io/learnopengl-tr/lighting/basic_lighting.html) eğitsele geçebiliriz.
