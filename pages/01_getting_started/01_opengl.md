---
title:Başlarken: OpenGL
permalink: /getting_started/opengl.html
sidebar: main_sidebar
---
## OpenGL
Yolculuğumuza başlamadan önce, öncelikle OpenGL'nin gerçekte ne olduğunu tanımlamalıyız. OpenGL temel olarak grafik ve görüntüleri işlemek için kullanabileceğimiz büyük bir fonksiyon seti sağlayan bir **API (Uygulama Programlama Arayüzü)** olarak kabul edilir. Bununla birlikte, OpenGL kendi başına sadece bir API değildir,  Khronos Group tarafından geliştirilmiş ve sürdürülmüş bir spesifikasyondur.

<img src="https://learnopengl.com/img/getting-started/opengl.jpg">

OpenGL tanımlaması, her bir işlevin sonucunun/çıktısının ne olması gerektiğini ve nasıl uygulaması gerektiğini belirtir.Bu işlevin nasıl çalışması gerektiğine dair bir çözüm bulmak bu tanımlamayı uygulayan geliştiriciye bağlıdır.OpenGL tanımlaması bize uygulama ayrıntılarını vermediğinden, OpenGL'nin gerçek geliştirilmiş sürümleri, sonuçları tanımlamaya uygun olduğu sürece (ve dolayısıyla kullanıcıyla aynı olduğu sürece) farklı uygulamalara izin verir.

Gerçek OpenGL kütüphanelerini geliştiren kişiler genellikle grafik kartı üreticileridir. Satın aldığınız her bir grafik kartı, OpenGL'in, bu kart için özel olarak geliştirilmiş OpenGL sürümlerini destekler.

>Çoğu uygulama, grafik kartı üreticileri tarafından oluşturulduğundan, uygulamada bir hata olduğunda, bu genellikle grafik kartı sürücülerini güncelleyerek çözülür. Bu sürücüler, kartın desteklediği OpenGL'in en yeni sürümlerini içerir. Bu, grafik sürücülerinizi sık sık güncellemenin tavsiye edilmesinin sebeplerinden biridir.

Khronos, tüm OpenGL sürümleri için spesifikasyon belgelerini herkese açık olarak barındırmaktadır. İlgili okuyucu [burada](https://www.khronos.org/registry/OpenGL/specs/gl/glspec33.core.pdf) OpenGL spesifikasyonunun 3.3 versiyonunu (bizim kullanacağımız versiyon) bulabilir ki OpenGL'in ayrıntılarıyla incelemekisteyenler için iyi bir okumadır. Spesifikasyon ayrıca, işlevlerin tam olarak işleyişinin bulunması için harika bir referans sağlamaktadır.

### Core-profile ile Immediate Mode Karşılaştırması
Eski günlerde OpenGL kullanımı, grafik çizimi için kolay bir yol olan anlık mod (ing. immediate mode) ile geliştirme anlamına geliyordu. OpenGL işlevselliğinin çoğu kütüphanede gizlenmişti ve geliştiricilerin OpenGL'in hesaplamalarına dair fazla özgürlüğü yoktu. Geliştiriciler nihayetinde daha fazla esneklik istedi ve zamanla spesifikasyonlar daha esnek hale geldi; böylece geliştiriciler grafikleri üzerinde daha fazla kontrol sahibi oldu. Anlık modun kullanımı ve anlaşılması gerçekten kolaydır, fakat aynı zamanda son derece verimsizdir. Bu nedenle spesifikasyon, sürüm 3'ten itibaren anlık mod işlevselliğini kaldırmaya başladı ve OpenGL'in tüm eski kullanım dışı işlevselliklerini kaldıran bir özelliği olan çekirdek profili (ing. core-profile) modunda geliştiricileri motive etti.

Çekirdek profili kullanırken OpenGL, bizi modern uygulamaları kullanmaya zorlar. OpenGL'in kullanım dışı işlevlerinden birini kullanmaya çalıştığımızda, OpenGL bir hataya neden olur ve çizim işlemini durdurur. Modern yaklaşım çok esnek ve verimlidir, ancak ne yazık ki öğrenmesi de zordur. Anlık mod, OpenGL'in gerçekleştirdiği işlemlerden oldukça soyutlanmıştı. Bu yüzden OpenGL'in nasıl çalıştığını anlamak zordu. Modern yaklaşım, geliştiricinin OpenGL ve grafik programlamayı gerçekten anlamasını gerektirir ve biraz zor olsa da, daha fazla esneklik, daha fazla verimlilik ve en önemlisi de grafik programlamanın daha iyi anlaşılmasını sağlar.

Bu aynı zamanda eğitsellerimizde çekirdek profil OpenGL sürüm 3.3 kullanılmasının sebebidir. Daha zor olmasına rağmen, çabaya değer.

Bugünden itibaren, OpenGL'in çok daha yüksek sürümleri yayınlanırken (eğitseli yazarken 4.5 yayınlandı), neden OpenGL 4.5 yerine OpenGL 3.3'ü öğrendiğimizi sorabilirsiniz. Bu sorunun cevabı nispeten basit. Gelecekteki tüm OpenGL sürümleri 3.3'ten başlayarak, OpenGL'in çekirdek mekaniğini değiştirmeden, temel olarak OpenGL'e harici kullanışlı özellikler ekliyor; daha yeni sürümler, aynı görevleri yerine getirmek için biraz daha verimli veya daha kullanışlı yöntemler sunuyor. Sonuç olarak, tüm kavramlar ve teknikler modern OpenGL versiyonlarında aynı kalır.Bu nedenle OpenGL 3.3'ü öğrenmek mükemmel bir şekilde geçerlidir. Ne zaman hazır ve / veya daha deneyimli olursanız, daha yeni OpenGL sürümlerinden belirli işlevleri kolayca kullanabilirsiniz.

>OpenGL'in en son sürümünden gelen işlevselliği kullanırken, uygulamanızı yalnızca en modern grafik kartları çalıştırabilirsiniz. Çoğu geliştiricinin genellikle OpenGL'in daha düşük sürümlerini hedef almasının ve isteğe bağlı olarak daha yüksek sürüm işlevselliğini etkinleştirmesinin nedeni budur.

Bazı derslerde bazen daha modern özellikler bulacaksınız.

### Uzantılar
 A great feature of OpenGL is its support of extensions. Whenever a graphics company comes up with a new technique or a new large optimization for rendering this is often found in an extension implemented in the drivers. If the hardware an application runs on supports such an extension the developer can use the functionality provided by the extension for more advanced or efficient graphics. This way, a graphics developer can still use these new rendering techniques without having to wait for OpenGL to include the functionality in its future versions, simply by checking if the extension is supported by the graphics card. Often, when an extension is popular or very useful it eventually becomes part of future OpenGL versions.

The developer then has to query whether any of these extensions are available (or use an OpenGL extension library). This allows the developer to do things better or more efficient, based on whether an extension is available: 

```cpp
if(GL_ARB_extension_name)
{
    // Do cool new and modern stuff supported by hardware
}
else
{
    // Extension not supported: do it the old way
}
```
 With OpenGL version 3.3 we rarely need an extension for most techniques, but wherever it is necessary proper instructions are provided.
###State machine

OpenGL is by itself a large state machine: a collection of variables that define how OpenGL should currently operate. The state of OpenGL is commonly referred to as the OpenGL context. When using OpenGL, we often change its state by setting some options, manipulating some buffers and then render using the current context.

Whenever we tell OpenGL that we now want to draw lines instead of triangles for example, we change the state of OpenGL by changing some context variable that sets how OpenGL should draw. As soon as we changed the state by telling OpenGL it should draw lines, the next drawing commands will now draw lines instead of triangles.

When working in OpenGL we will come across several state-changing functions that change the context and several state-using functions that perform some operations based on the current state of OpenGL. As long as you keep in mind that OpenGL is basically one large state machine, most of its functionality will make more sense.
###Objects

The OpenGL libraries are written in C and allows for many derivations in other languages, but in its core it remains a C-library. Since many of C's language-constructs do not translate that well to other higher-level languages, OpenGL was developed with several abstractions in mind. One of those abstractions are objects in OpenGL.

An object in OpenGL is a collection of options that represents a subset of OpenGL's state. For example, we could have an object that represents the settings of the drawing window; we could then set its size, how many colors it supports and so on. One could visualize an object as a C-like struct: 


```cpp
struct object_name {
    float  option1;
    int    option2;
    char[] name;
};
```
Whenever we want to use objects it generally looks something like this (with OpenGL's context visualized as a large struct): 
```cpp
// The State of OpenGL
struct OpenGL_Context {
  	...
  	object* object_Window_Target;
  	...  	
};
```
```cpp
// create object
unsigned int objectId = 0;
glGenObject(1, &objectId);
// bind object to context
glBindObject(GL_WINDOW_TARGET, objectId);
// set options of object currently bound to GL_WINDOW_TARGET
glSetObjectOption(GL_WINDOW_TARGET, GL_OPTION_WINDOW_WIDTH, 800);
glSetObjectOption(GL_WINDOW_TARGET, GL_OPTION_WINDOW_HEIGHT, 600);
// set context target back to default
glBindObject(GL_WINDOW_TARGET, 0);
```

This little piece of code is a workflow you'll frequently see when working in OpenGL. We first create an object and store a reference to it as an id (the real object data is stored behind the scenes). Then we bind the object to the target location of the context (the location of the example window object target is defined as GL_WINDOW_TARGET). Next we set the window options and finally we un-bind the object by setting the current object id of the window target to 0. The options we set are stored in the object referenced by objectId and restored as soon as we bind the object back to GL_WINDOW_TARGET. 

>The code samples provided so far are only approximations of how OpenGL operates; throughout the tutorial you will come across enough actual examples. 

The great thing about using these objects is that we can define more than one object in our application, set their options and whenever we start an operation that uses OpenGL's state, we bind the object with our preferred settings. There are objects for example that act as container objects for 3D model data (a house or a character) and whenever we want to draw one of them, we bind the object containing the model data that we want to draw (we first created and set options for these objects). Having several objects allows us to specify many models and whenever we want to draw a specific model, we simply bind the corresponding object before drawing without setting all their options again. 

###Let's get started

You now learned a bit about OpenGL as a specification and a library, how OpenGL approximately operates under the hood and a few custom tricks that OpenGL uses. Don't worry if you didn't get all of it; throughout the tutorial we'll walk through each step and you'll see enough examples to really get a grasp of OpenGL. If you're ready for the next step we can start creating an OpenGL context and our first window [here](https://cg-translators.github.io/learnopengl-tr/getting_started/creating_a_window.html). 

### Ek Kaynaklar

    [opengl.org](https://www.opengl.org/): OpenGL'in resmi web sitesi
    [OpenGL registry](https://www.khronos.org/registry/OpenGL/index_gl.php): tüm OpenGL sürümleri için OpenGL özellikleri ve uzantıları barındırır.


#### Kaynak
https://learnopengl.com/Getting-started/OpenGL
