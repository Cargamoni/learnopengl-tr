---
title: Gölgelendiriciler
keywords: 
tags: [shader,glsl,vertex shader,fragment shader,vektörler]
permalink: /getting_started/shaders.html
sidebar: main_sidebar
---
[Merhaba Üçgen](/getting_started/hello_triangle.html) dersinde belirtildiği gibi, shaderlar GPU üzerinde saklanan küçük programlardır. Bu programlar grafik iletişim hattının her bir bölümü için çalıştırılır. Temel olarak shaderlar, girdileri çıktılara dönüştüren programlardan fazlası değildir. Ayrıca, shaderlar birbirleriyle iletişim kurmalarına izin verilmeyen yalıtılmış programlardır; sahip oldukları tek iletişim giriş ve çıkışları üzerinden gerçekleşir.

Önceki derste, shaderlara ve nasıl doğru kullanılacağına kısaca değindik. Şimdi shaderları -özellikle OpenGL Shading Language- daha genel bir şekilde açıklayacağız.

# GLSL
Shaderlar C benzeri bir dil olan GLSL'de yazılmıştır. GLSL, grafiklerle kullanmak için uygun hâle getirilmiştir ve özellikle vektör ve matris manipülasyonunu hedefleyen kullanışlı özellikler içerir.

Shaderlar her zaman bir versiyon bildirimi ile başlar. Bunu bir dizi giriş-çıkış değişkenleri, uniformlar ve kendi ana fonksiyonu takip eder. Her shaderin giriş noktası, herhangi bir giriş değişkenini işlediğimiz ve çıkış değişkenlerine sonuçları yazdırdığımız kendi `main` fonksiyonundadır. Uniformların ne olduğunu bilmiyorsanız endişelenmeyin, onları birazdan öğreneceğiz.

Bir shader genel anlamda aşağıdaki yapıya sahiptir:

```glsl
#version version_number
in type in_variable_name;
in type in_variable_name;

out type out_variable_name;

uniform type uniform_name;

void main()
{
  // girdileri işle ve bazı garip grafikler yap
  ...
  // işlenmiş bölümü çıktı değişkenine ata
  out_variable_name = weird_stuff_we_processed;
}
```

Özellikle vertex shader hakkında konuşurken, her giriş değişkeni verteks özniteliği olarak da bilinir. Donanım tarafından sınırlanmış, tanımlamaya izin verilen bir maksimum vertex sayısı vardır. OpenGL, her zaman en az 16 adet 4-bileşenli vertex özelliğinin bulunduğunu garanti eder; fakat bazı donanımlar daha fazlasına izin verebilir. Bunu `GL_MAX_VERTEX_ATTRIBS` değerini sorgulayarak öğrenebilirsiniz:

```cpp
int nrAttributes;
glGetIntegerv(GL_MAX_VERTEX_ATTRIBS, &nrAttributes);
std::cout << "Desteklenen maksimum vertex özniteliği sayısı: " << nrAttributes << std::endl;
```

Genelde çoğu amaç için yeterli olabilecek 16 değerini döndürür.

Tipler
-----

GLSL, hangi tipte bir değişkenle çalışmak istediğimizi belirlemek için diğer programlama dillerine benzer veri tiplerine sahiptir. GLSL, C gibi dillerden bildiğimiz temel tiplerin çoğuna sahiptir: `int`, `float`, `double`, `uint`, `bool`. Ayrıca, GLSL dersler boyunca `vektörler` ve `matrisler` olarak sık sık kullanacağımız iki kapsayıcı tipe de sahiptir. Matrisleri daha sonraki bir derste konuşacağız.

### Vektörler

GLSL'de vektör, yukarıda bahsedilen temel tiplerin herhangi biri için 1,2,3 veya 4 bileşenli bir kapsayıcıdır. Aşağıdaki şekilleri alabilirler (`n` bileşen sayısını temsil eder):

*   `vecn`: `n` float'tan oluşan başlangıç vektörü.
*   `bvecn`: `n` boolean içeren vektör.
*   `ivecn`: `n` integer içeren vektör.
*   `uvecn`: `n` unsigned tamsayı içeren vektör.
*   `dvecn`: `n` double sayı içeren vektör.

Float tipi çoğu amaç için yeterli olacağından çoğunlukla `vecn` vektörünü kullanacağız.


Bir vektörün bileşenlerine `vec.x` ile erişilebilir, burada `x`, vektörün ilk bileşenidir. Birinci, ikinci, üçüncü ve dördüncü bileşene erişmek için sırasıyla `.x`, `.y`, `.z` ve `.w` üyelerini kullanabilirsiniz. Ayrıca GLSL, aynı bileşenlere erişerek, renkler için `rgba` veya doku koordinatları için `stpq` kullanmanıza izin verir.

Vektör veri tipi, swizzling denilen biraz ilginç ve esnek bileşen seçimine izin verir. Swizzling aşağıdaki sözdizimine olanak tanır:

```glsl
vec2 someVec;
vec4 differentVec = someVec.xyxx;
vec3 anotherVec = differentVec.zyw;
vec4 otherVec = someVec.xxxx + anotherVec.yxzy;
```

Orijinal vektör bu bileşenlere sahip olduğu sürece (aynı türde) yeni bir vektör oluşturmak için en fazla 4 harften oluşan herhangi bir kombinasyonu kullanabilirsiniz; örneğin bir `vec2`'nin `.z` bileşenine erişimi mümkün değildir. Ayrıca, vektörleri farklı vektör yapıcı çağrılarına argüman olarak iletebiliriz, böylece gerekli argüman sayısını azaltabiliriz:

```glsl
vec2 vect = vec2(0.5, 0.7);
vec4 result = vec4(vect, 0.0, 0.0);
vec4 otherResult = vec4(result.xyz, 1.0);
```

Vektör, her tür giriş ve çıkış için kullanabileceğimiz esnek veri tipidir. Eğitim boyunca, vektörleri nasıl yaratıcı bir şekilde yönetebileceğimize dair bolca örnek göreceksiniz.

Girdiler ve Çıktılar
------------

Shaderlar kendi başlarına küçük hoş programlardır, ama bir bütünün parçasıdırlar ve bu nedendenle her bir shader üzerinde giriş ve çıkışlara sahip olmak isteriz ki böylece birşeyleri etrafta hareket ettirebiliriz. GLSL, bu amaç için özelllikle `in` ve `out` anahtar kelimelerini tanımlamıştır. Her bir shader, bu anahtar kelimeleri kullanarak giriş ve çıkışlar tanımlayabilir ve bir çıkış değişkeni, bir sonraki shader bölümünün bir giriş değişkeni ile eşleştiğinde aktarılır. Vertex ve fragment shader biraz farklıdır.

Vertex shader bir tür girdi almalıdır, aksi taktirde oldukça etkisiz olur. Vertex shader girdi bakımından ayrılır, çünkü  girdisini vertex data üzerinden alır. Vertex data'nın nasıl düzenlendiğini tanımlamak için, girdi değişkenlerini konum (location) meta verisi ile belirtiriz. Böylece CPU üzerinde vertex özelliklerini ayarlayabiliriz. Bunu bir önceki derste `layout (location = 0)` olarak görmüştük. Vertex shader, bu nedenle, girdileri için fazladan düzen tanımı gerektirir, böylece onu vertex data ile bağlayabiliriz.

`layout (location = 0)` belirtecini çıkartmak ve OpenGL kodunuzda glGetAttribLocation ile özellik konumlarını sorgulamak da mümkündür, ancak ben bunları vertex shader içinde atamayı tercih ederim. Bunu anlaması daha kolaydır ve sizi (ve OpenGL'i) biraz işten kurtarır.

Diğer istisna ise fragment shader'ın bir `vec4` renk çıkışı değişkeni gerektirmesidir, çünkü fragment shaderlar bir son çıktı rengi üretmesi gerekir. Fragment shaderda çıktı rengi belirtmeyi başaramazsanız OpenGL nesnenizi siyah (veya beyaz) yapar.

Dolayısıyla bir shaderdan veri göndermek istiyorsak, gönderen shaderdan bir çıktı ve alıcı shadera, benzer bir girdi bildirmek zorunda kalırız. Tipler ve isimler her iki tarafta da aynı olduğunda, OpenGL bu değişkenleri birbirleriyle bağlar ve shaderlar arası veri göndermek mümkün olur(bu bir program nesnesi bağlanırken yapılır). Bunun pratikte nasıl çalıştığını size göstermek için geçen dersteki shaderları değiştireceğiz, vertex shaderın, fragment shaderın rengine karar vermesine izin vereceğiz.

**Vertex shader**

```glsl
#version 330 core
layout (location = 0) in vec3 aPos; // konum özelliği 0 olan pozisyon değişkeni 

out vec4 vertexColor; // fragment shadera bir renk çıktısı belirle

void main()
{
    gl_Position = vec4(aPos, 1.0); // vec4'ün yapıcısına nasıl doğrudan vec3 verdiğimizi görün
    vertexColor = vec4(0.5, 0.0, 0.0, 1.0); // çıktı değişkenine koyu-kırmızı bir renk ata
}
```

**Fragment shader**

```glsl
#version 330 core
out vec4 FragColor;
  
in vec4 vertexColor; // vertex shaderın girdi değişkeni (aynı ad ve aynı tip)  

void main()
{
    FragColor = vertexColor;
}
```
Bir vertexColor değişkenini, vertex shader'da atadığımız çıkışı `vec4` olarak belirttiğimizi ve fragment shader'da benzer bir vertexColor girişi tanımladığımızı görebilirsiniz. Bunların ismi ve tipi aynı olduğu için, fragment shader'daki vertexColor, vertex shader içindeki vertexColor'a bağlanır. Vertex shader'da koyu kırmızı bir renk atadığımız için sonuç fragmentleri de koyu kırmızı olmalıdır. Aşağıdaki görseller çıktıyı gösteriyor.

![](https://learnopengl.com/img/getting-started/shaders.png)

İşte başlıyoruz! Biz sadece vertex shader'dan fragment shader'a bir değer göndermeyi başardık. Haydi biraz baharatlandıralım ve uygulamamızdan fragment shader'a bir renk gönderebilecek miyiz görelim!

Uniformlar
--------

Uniformlar, CPU üzerindeki uygulamamızdan GPU üzerindeki shaderlara veri aktarmanın bir başka yoludur. Ancak uniformlar vertex özellikleriyle karşılaştırılınca biraz farklıdır. Öncelikle, uniformlar globaldir. Global, bir uniform değişkenin, her bir shader program nesnesine özgün olması ve shader programının herhangi bir bölümündeki herhangi bir shaderdan erişilebilir olması anlamına gelir. İkinci olarak, uniform değeri neye ayarladığınıza göre, uniformlar sıfırlanana veya güncellenene kadar değerlerini koruyacaktır.

GLSL'de bir uniform tanımlamak için basitçe, bir shadera, bir tip ve isimle birlikte `uniform` anahtar kelimesini ekleriz.
Bu noktadan itibaren, shaderda yeni tanımlanan uniformu kullanabiliriz. Bakalım bu sefer üçgenin rengini üniform ile ayarlayabilir miyiz?:

```glsl
#version 330 core
out vec4 FragColor;
  
uniform vec4 ourColor; // bu değişkeni OpenGL kodunda atıyoruz

void main()
{
    FragColor = ourColor;
}   
```

Fragment shaderda bir uniform `vec4` tanımladık ve fragmentin çıktı rengini bu üniformun değerine atadık. Üniformlar global değişkenler olduğundan dolayı, onları istediğimiz bir shaderın içinde tanımlayabiliriz ???? Bu üniformu vertex shaderda kullanmıyoruz, böylece onu burada tanımlamamıza ihtiyaç yok.

GLSL kodu içinde, hiçbir yerde kullanmadığınız bir üniform tanımlarsanız, derleyiciniz bu değişkeni birkaç sinir bozucu hataya neden oluşturan derlenmiş hâlinden sessizce kaldıracaktır; aklınızda bulunsun.

Üniform şuan boş; henüz üniforma bir veri eklemedik, hadi deneyelim. Öncelikle, shaderımızda üniformun konum (location) özelliğini bulmamız gerek. Üniformun konumunu bildiğimizde, değerini gücelleyebiliriz. Fragment shader'a tek renk geçmek yerine, rengi zamanla kademeli bir şekilde değiştirerek canlandıralım:

```cpp
float timeValue = glfwGetTime();
float greenValue = (sin(timeValue) / 2.0f) + 0.5f;
int vertexColorLocation = glGetUniformLocation(shaderProgram, "ourColor");
glUseProgram(shaderProgram);
glUniform4f(vertexColorLocation, 0.0f, greenValue, 0.0f, 1.0f);
```

Önce, çalışma zamanını glfwGetTime() ile alırız. Sonra, sin fonksiyonunu kullanarak rengi `0.0` - `1.0` aralığında çeşitlendirir ve sonucu greenValue'da saklarız.

Sonra ourColor üniformunun konumunu glGetUniformLocation kullanarak sorgularız. Sorgu fonksiyonuna, shader programını ve üniformun (konumdan almak istediğimiz) adını sağladık.

glGetUniformLocation `-1` döndürürse, konumu bulamamıştır. Son olarak glUniform4f fonksiyonunu kullanarak üniformun değerini ayarlayabiliriz. Üniform konumunu bulmanın, önce shader programını kullanmanızı gerektirmediğini unutmayın., ancak bir üniformu güncellemek şu anda aktif olan shader programında üniformu ayarladığı için önce programı (glUseProgram'ı çağırarak) kullanmanızı gerektirir.

{% include callout.html content="
OpenGL, çekirdeğinde bir C kütüphanesi olduğundan aşırı yükleme için (overloading) native (doğal) desteğe sahip değildir. Bir fonksiyonun farklı tiplerle çağrılabildiği her yerde OpenGL gereken her tip için yeni fonksiyonlar tanımlar; glUniform bunun mükemmel bir örneğidir. Fonksiyon, belirlemek istediğiniz üniformun tipi için özel bir son ek gerektirir. Birkaç olası son ek:
<br/><br/>
`f`: fonsiyon bir `float` bekliyor<br/>
`i`: fonsiyon bir `int` bekliyor<br/>
`ui`: fonsiyon bir `unsigned int` bekliyor<br/>
`3f`: fonsiyon 3 `float` bekliyor<br/>
`fv`: fonsiyon bir `float` vektörü/dizisi bekliyor<br/>
<br/>
OpenGL'de bir seçeneği ayarlamak istediğiniz zaman, basitçe, tipinize uygun olan aşırı yüklenmiş fonksiyonu seçin. Bizim durumumuzda, her bir üniforma 4 float atamak istiyoruz, bu yüzden verimizi `glUniform4f` fonksiyonuyla aktaracağız(ayrıca `fv` hâlini kullanabileceğimize de dikkat edin).
<br/>
" type="primary" %} 

Şuan uniform değişkenlerinin değerlerini nasıl atayacağımızı biliyoruz, bunları render için kullanabiliriz. Rengi kademeli olarak değiştirmek istiyorsak, bu uniformu her oyun döngüsü yinelemesini (kareye göre değişir) güncellemek isteriz aksi hâlde eğer bir kere atarsak üçgen tek bir düz rengi korur. Bu yüzden greenValue yi hesaplarız ve her render yinelemesinde uniformu güncelleriz:

```cpp
while(!glfwWindowShouldClose(window))
{
    // girdi
    processInput(window);

    // render
    // renk tamponunu temizle
    glClearColor(0.2f, 0.3f, 0.3f, 1.0f);
    glClear(GL_COLOR_BUFFER_BIT);

    //  shaderın aktif edildiğinden emin ol
    glUseProgram(shaderProgram);
  
    // uniform rengini güncelle
    float timeValue = glfwGetTime();
    float greenValue = sin(timeValue) / 2.0f + 0.5f;
    int vertexColorLocation = glGetUniformLocation(shaderProgram, "ourColor");
    glUniform4f(vertexColorLocation, 0.0f, greenValue, 0.0f, 1.0f);

    // üçgeni çiz
    glBindVertexArray(VAO);
    glDrawArrays(GL_TRIANGLES, 0, 3);
  
    // tamponları takas et ve G/Ç olaylarını yokla
    glfwSwapBuffers(window);
    glfwPollEvents();
}
```

Kod, önceki kodun nispeten basit bir uyarlamasıdır. Bu kez, üçgeni çizmeden önce her bir yineleme için tek biçimli bir değeri güncelledik. Uniformu doğru bir şekilde güncellerseniz, üçgeninizin renginin kademeli olarak yeşilden siyaha ve yeşile döndüğünü görmelisiniz.

 ![](https://learnopengl.com/img/getting-started/shaders2.png)

Takıldıysanız [buradan](https://learnopengl.com/code_viewer_gh.php?code=src/1.getting_started/3.1.shaders_uniform/shaders_uniform.cpp) kaynak kodunu inceleyin.


Gördüğünüz gibi uniformlar, render yinelemelerinde değişebilecek nitelikleri ayarlamak veya uygulamanız ile shaderlarınız arasında veri alışverişi yapmak için yararlı bir araçtır, ancak her vertex için bir renk belirlemek istiyorsak ne olur? Bu durumda, köşelerimiz kadar uniform tanımlamamız etmemiz gerekirdi. Daha iyi bir çözüm, yapacağımız şey olan vertex özelliklerine daha fazla veri eklemek olacaktır.

Daha fazla özellik!
----------------

Önceki derste, bir VBO'yu nasıl doldurabileceğimizi, vertex özellik pointerlarını nasıl yapılandırabileceğimizi ve hepsini bir VAO'da nasıl saklayabileceğimizi gördük. Bu kez, vertex verisine renk verileri de eklemek istiyoruz. Renk vertex dizisine 3 `float` şeklinde renk verilerini ekleyeceğiz. Üçgenin her bir köşesine sırasıyla kırmızı, yeşil ve mavi renk atarız:

```cpp
float vertices[] = {
    // pozisyonlar         // renkler
     0.5f, -0.5f, 0.0f,  1.0f, 0.0f, 0.0f,   // alt sağ
    -0.5f, -0.5f, 0.0f,  0.0f, 1.0f, 0.0f,   // alt sol
     0.0f,  0.5f, 0.0f,  0.0f, 0.0f, 1.0f    // üst
};    
```

Artık vertex shadera gönderilecek daha fazla veriye sahip olduğumuzdan, renk değerimizi bir vertex özelliği girişi olarak alacak şekilde vertex shaderı ayarlamak gerekir. `aColor` özelliğinin konumunu düzen belirleyici ile `1`olarak ayarladığımızı unutmayın:

```glsl
#version 330 core
layout (location = 0) in vec3 aPos;   // konum özelliği 0 olan pozisyon değişkeni
layout (location = 1) in vec3 aColor; // konum özelliği 1 olan renk değişkeni
  
out vec3 ourColor; // fragment shadera bir renk gönder

void main()
{
    gl_Position = vec4(aPos, 1.0);
    ourColor = aColor; // vertex verisinden aldığımız değeri ourColor'a ata
}       
```

Artık, fragment'in rengi için bir uniform kullanmayacağımızdan, şimdi ourColor çıktı değişkenini kullandığımız için, fragment shaderı da değiştirmek zorunda kalacağız:

```glsl
#version 330 core
out vec4 FragColor;  
in vec3 ourColor;
  
void main()
{
    FragColor = vec4(ourColor, 1.0);
}
```

Başka bir vertex niteliği eklediğimiz ve VBO hafızasını güncellediğimiz için vertex niteliği işaretçilerini yeniden yapılandırmamız gerekiyor. VBO’nun belleğindeki güncellenmiş veriler şimdi biraz şuna benziyor:

![glVertexAttribPointer ile yapılandırılacak VBO içindeki pozisyon ve rengin iç içe geçmiş verileri](https://learnopengl.com/img/getting-started/vertex_attribute_pointer_interleaved.png)

Mevcut düzeni bilerek, vertex formatını `glVertexAttribPointer` ile güncelleyebiliriz:

```cpp
// pozisyon özelliği
glVertexAttribPointer(0, 3, GL_FLOAT, GL_FALSE, 6 * sizeof(float), (void*)0);
glEnableVertexAttribArray(0);
// renk özelliği
glVertexAttribPointer(1, 3, GL_FLOAT, GL_FALSE, 6 * sizeof(float), (void*)(3* sizeof(float)));
glEnableVertexAttribArray(1);
```
GlVertexAttribPointer'ın ilk birkaç argümanı nispeten basittir. Bu kez vertex niteliğini `1` özellik konumunda yapılandırıyoruz. Renk değerleri 3 `float` büyüklüğüne sahiptir ve değerleri normalleştirmiyoruz.

Şimdi iki vertex niteliğine sahip olduğumuz için _stride_ değerini yeniden hesaplamamız gerekiyor. Veri dizisindeki bir sonraki öznitelik değerini (örneğin, pozisyon vektörünün bir sonraki `x` bileşeni) elde etmek için, üç pozisyon değeri ve üç renk değeri için `6` `float`ı sağa hareket ettirmeliyiz. Bu bize bayt cinsinden bir `float` boyutunun 6 katı adım değeri verir (= `24` bayt).

Ayrıca, bu sefer bir ofset belirtmeliyiz. Her vertex için, konum vertex özniteliği öncedir, bu nedenle `0` ofsetini tanımlarız. Renk özniteliği, konum verilerinden sonra başlar, bu nedenle ofset, bayt cinsinden `3 * sizeof (float)` dır (=`12` bayt).

Uygulamayı çalıştırmak aşağıdaki görüntüyle sonuçlanmalıdır:

![](https://learnopengl.com/img/getting-started/shaders3.png)

Kaynak kodunu [buradan](https://learnopengl.com/code_viewer_gh.php?code=src/1.getting_started/3.2.shaders_interpolation/shaders_interpolation.cpp)inceleyin

Resim tam olarak beklediğiniz gibi olmayabilir, çünkü şu anda gördüğümüz büyük renk paleti değil, sadece 3 renk sağladık. Tüm bunlar, fragment shaderda fragment enterpolasyonu denilen bir şeyin sonucu. Bir üçgen oluştururken, rasterleştirme aşaması genellikle başlangıçta belirtilen vertexlerden çok daha fazla fragmente neden olur. Rasterizer, daha sonra bu fragmentlerin her birinin pozisyonlarını, üçgen şeklinde bulundukları yere göre belirler.
Bu konumlara dayanarak, tüm parça gölgelendiricinin giriş değişkenlerini enterpolasyon yapar. Örneğin, üst noktanın yeşil ve alt noktanın mavi renkte olduğu bir çizgimiz var. Fragment shader, çizginin `%70` pozisyonundaki bir pozisyon etrafında kalan bir parçada çalıştırılırsa, sonuçtaki renk girişi özelliği, yeşil ve mavinin doğrusal bir birleşimi olur; Daha kesin olmak gerekirse: `%30` mavi ve `%70` yeşil.


Bu tam olarak üçgende olan şey. 3 vertexe ve dolayısıyla 3 renge sahibiz ve üçgenin piksellerine bakılırsa, muhtemelen fragment shaderın renkleri bu pikseller arasına yerleştirdiği yaklaşık 50000 fragmentten oluşuyor. Renklere iyi bakarsanız, her şeyin mantıklı olduğunu göreceksiniz: önce kırmızıdan maviye, mor sonra maviye. Parça enterpolasyonu, tüm fragment shaderın giriş niteliklerine uygulanır.

Kendi shader sınıfımız
====================

Shaderların yazılması, derlenmesi ve yönetilmesi oldukça zahmetli olabilir. Shader konusuna son bir dokunuş olarak, shaderları diskten okuyan, derleyen ve bağlayan, hataları kontrol eden ve kullanımı kolay bir shader sınıfı oluşturarak hayatımızı biraz daha kolaylaştıracağız. Bu aynı zamanda size şimdiye kadar öğrendiğimiz bilgilerin bir kısmını yararlı soyut nesnelere nasıl yerleştirebileceğimize dair bir fikir verir.

Gölgelendirici sınıfını, esas olarak öğrenme amaçları ve taşınabilirlik için tamamen bir header dosyasında oluşturacağız. Gerekli olanları ekleyerek ve sınıf yapısını tanımlayarak başlayalım:

```cpp
#ifndef SHADER_H
#define SHADER_H

#include <glad/glad.h> // gerekli tüm OpenGL headarlarını almak için glad
  
#include <string>
#include <fstream>
#include <sstream>
#include <iostream>
  

class Shader
{
public:
    // program ID
    unsigned int ID;
  
    // yapıcı shaderı okur ve derler
    Shader(const GLchar* vertexPath, const GLchar* fragmentPath);
    // shaderı kullan/aktifleştir
    void use();
    // uniform fonksiyonları
    void setBool(const std::string &name, bool value) const;  
    void setInt(const std::string &name, int value) const;   
    void setFloat(const std::string &name, float value) const;
};
  
#endif
```

Header dosyasının en üstünde birkaç önişlemci direktifi kullandık. Bu küçük kod satırlarını kullanmak, derleyicinize yalnızca bu header dosyasını eklememiş ve henüz eklenmemişse, birden fazla dosya shader headerını dahil etse bile derler. Bu bağlantı çakışmalarını önler.

Shader sınıfı, shader programının ID'sini tutar. Yapıcısı, diskte basit metin dosyaları olarak saklayabileceğimiz sırasıyla vertex ve fragment shaderın kaynak kodunun dosya yollarını gerektirir. Biraz fazlalık eklemek, yaşamımızı biraz kolaylaştırmak için çeşitli işe yarar fonksiyonlar da ekliyoruz: `use`, shader programını etkinleştirir ve tüm `set...` fonksiyonları bir uniform konum sorgusu yapar ve değerini ayarlar.

Dosyadan okuma
-----------------

Dosyanın içeriğini birkaç `string` değişkeninin içine aktarmak için C++ filestreamlarını kullanıyoruz:

```cpp
Shader(const char* vertexPath, const char* fragmentPath)
{
    // 1. dosya yolundan vertex/fragment kaynak kodunu al
    std::string vertexCode;
    std::string fragmentCode;
    std::ifstream vShaderFile;
    std::ifstream fShaderFile;
    // ifstream nesnelerinin exception verebildiğinden emin ol:
    vShaderFile.exceptions (std::ifstream::failbit | std::ifstream::badbit);
    fShaderFile.exceptions (std::ifstream::failbit | std::ifstream::badbit);
    try 
    {
        // dosyaları aç
        vShaderFile.open(vertexPath);
        fShaderFile.open(fragmentPath);
        std::stringstream vShaderStream, fShaderStream;
        // dosyanın tampon içeriklerini akışlara aktar
        vShaderStream << vShaderFile.rdbuf();
        fShaderStream << fShaderFile.rdbuf();		
        // dosya işleyicileri kapat
        vShaderFile.close();
        fShaderFile.close();
        // akışı stringe çevir
        vertexCode   = vShaderStream.str();
        fragmentCode = fShaderStream.str();		
    }
    catch(std::ifstream::failure e)
    {
        std::cout << "ERROR::SHADER::FILE_NOT_SUCCESFULLY_READ" << std::endl;
    }
    const char* vShaderCode = vertexCode.c_str();
    const char* fShaderCode = fragmentCode.c_str();
    [...]
```

Daha sonra, shaderları derlememiz ve birbirine bağlamamız gerekir. Derleme / bağlamanın başarısız olup olmadığını da incelediğimize dikkat edin ve öyleyse, hata ayıklama işleminde son derece yararlı olan derleme zamanı hatalarını yazdırın (sonunda bu hata günlüklerine ihtiyacınız olacak):

```cpp
// 2. shaderları derle
unsigned int vertex, fragment;
int success;
char infoLog[512];
   
// vertex Shader
vertex = glCreateShader(GL_VERTEX_SHADER);
glShaderSource(vertex, 1, &vShaderCode, NULL);
glCompileShader(vertex);
// derleme hatası varsa yazdır
glGetShaderiv(vertex, GL_COMPILE_STATUS, &success);
if(!success)
{
    glGetShaderInfoLog(vertex, 512, NULL, infoLog);
    std::cout << "ERROR::SHADER::VERTEX::COMPILATION_FAILED\n" << infoLog << std::endl;
};
  
// Fragment Shader için benzeri
[...]
  
// shader Program
ID = glCreateProgram();
glAttachShader(ID, vertex);
glAttachShader(ID, fragment);
glLinkProgram(ID);
// linkleme hatası varsa yazdır
glGetProgramiv(ID, GL_LINK_STATUS, &success);
if(!success)
{
    glGetProgramInfoLog(ID, 512, NULL, infoLog);
    std::cout << "ERROR::SHADER::PROGRAM::LINKING_FAILED\n" << infoLog << std::endl;
}
  
// programımızda bağlanmış ve artık gerekmeyen shaderları sil
glDeleteShader(vertex);
glDeleteShader(fragment);
```

`use` fonksiyonu basittir:

```cpp
void use() 
{ 
    glUseProgram(ID);
}  
```

Herhangi bir uniform setter fonksiyonu için benzer şekilde:

```cpp
void setBool(const std::string &name, bool value) const
{
    glUniform1i(glGetUniformLocation(ID, name.c_str()), (int)value);
}
void setInt(const std::string &name, int value) const
{
    glUniform1i(glGetUniformLocation(ID, name.c_str()), value);
}
void setFloat(const std::string &name, float value) const
{
    glUniform1f(glGetUniformLocation(ID, name.c_str()), value);
}
```

Ve tamamlanmış bir [shader sınıfı](/code_viewer_gh.php?code=includes/learnopengl/shader_s.h)mız var.
Shader sınıfını kullanmak oldukça kolaydır; shader nesnesini bir kez oluştururuz ve bu noktadan itibaren kullanmaya başlarız:

```cpp
Shader ourShader("path/to/shaders/shader.vs", "path/to/shaders/shader.fs");
...
while(...)
{
    ourShader.use();
    ourShader.setFloat("someUniform", 1.0f);
    DrawStuff();
}
```

Burada vertex ve fragment shaderın kaynak kodunu `shader.vs` ve `shader.fs` isimli iki dosyada sakladık. Shader dosyalarını dilediğiniz gibi adlandırmakta özgürsünüz; Kişisel olarak `.vs` ve `.fs` uzantıları oldukça sezgisel buluyorum.

Yeni oluşturduğumuz [shader sınıfını](https://learnopengl.com/code_viewer_gh.php?code=includes/learnopengl/shader_s.h) kullanan kaynak kodunu [burada](https://learnopengl.com/code_viewer_gh.php?code=src/1.getting_started/3.3.shaders_class/shaders_class.cpp) bulabilirsiniz. 

Alıştırmalar
=========

1. Vertex shaderı, üçgen baş aşağı olacak şekilde ayarlayın: [solution](https://learnopengl.com/code_viewer.php?code=getting-started/shaders-exercise1).
2. Bir uniform ile yatay bir ofset belirtin ve bu ofset değerini kullanarak vertex shaderda üçgeni ekranın sağ tarafına taşıyın: [solution](/code_viewer.php?code=getting-started/shaders-exercise2).
3. Vertex pozisyonunu `out` anahtar sözcüğünü kullanarak fragment shadera çıkarıp parçanın rengini bu vertex pozisyonuna eşit olarak ayarlayın (vertex pozisyon değerlerinin üçgen boyunca nasıl enterpolasyon yaptığını görün). Bir kere bunu yapmayı başardınız; aşağıdaki soruyu cevaplamaya çalışın: neden üçgenimizin sol-alt tarafı siyah?: [solution](/code_viewer.php?code=getting-started/shaders-exercise3).


![GPL License](http://www.gnu.org/graphics/gplv3-88x31.png)
*[grafik iletişim hattı]: graphics pipeline
*[uniform]: ??

Çeviri: [Barış Çelik](https://github.com/bariscelik)
