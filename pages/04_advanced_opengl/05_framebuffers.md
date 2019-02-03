---
title: Framebuffer
keywords: 
last_updated: 
tags: []
permalink: /advanced_opengl/framebuffers.html
sidebar: main_sidebar
---
# Framebuffer

> **Çevirmen Notu:** "Framebuffer" sözcüğü tek kelimeden oluşmaktadır ve "çerçeve tamponu" olarak çevirisi uygun olmamaktadır. Framebuffer, RAM'in bir parçasıdır. Kendisi bir tampon değil, aslında tamponların birleşimine verilen isimdir. Türkçe karşılığı olmamakla birlikte, eğitsellerin çevirisi dahilinde "framebuffer" sözcüğü orijinal biçimi ile bırakılmıştır.
 
Şimdiye kadar birkaç çeşit ekran tamponu kullandık: Renk değerleri yazmak için bir renk tamponu (ing. collor buffer), derinlik bilgileri yazmak için bir derinlik tamponu(ing. depth buffer) ve nihayet bazı koşullara dayanarak belirli parçaları atmamıza izin veren bir şablon tamponu (ing. stencil buffer). Bu tamponların birleşimine <span style="color:green">framebuffer </span>  denir ve bellekte bir yere depolanır. OpenGL bize kendi framebuffer'ımızı tanımlama ve böylece kendi rengimizi, isteğe bağlı olarak bir derinlik ve şablon tamponu tanımlama esnekliği sağlar.

Şimdiye kadar yaptığımız sahneleme işlemlerinin tümü, varsayılan framebuffer'a bağlı olan sahneleme tamponlarının (ing. render buffers) üzerinde yapıldı. <span style="color:green">Varsayılan framebuffer </span> pencere oluşturulduğunda yaratılır ve yapılandırılır (GLFW bunu bizim için yapmaktadır). Kendimiz framebuffer oluşturarak, sahnelemek için ek bir yol bulabiliriz.

Framebuffer uygulamaları hemen bir anlam ifade etmeyebilir,ancak sahnenizi farklı bir framebuffer'a yansıtmak, bir sahnede aynalar oluşturmamızı ya da örneğin etkili son-işlem (ing. post-processing) efektleri yapmamızı sağlar.İlk önce gerçekte nasıl çalıştıklarını tartışacağız ve sonra bu harika son-işlem efektlerini uygulayarak kullanacağız.

## Framebuffer Oluşturma

OpenGL'deki diğer nesneler gibi, **glGenFramebuffers** adlı bir işlevi kullanarak bir framebuffer nesnesi (kısaca FBO) oluşturabiliriz:
```cpp
unsigned int fbo;
glGenFramebuffers(1, &fbo);
```
Bu nesne yaratma ve kullanma kalıbı, şimdiye kadar onlarca kez gördüğümüz bir şeydir. Bu yüzden kullanım işlevleri, gördüğümüz diğer tüm nesnelere benzer; ilk önce bir framebuffer nesnesi yaratır, onu aktif framebuffer olarak bağlar, bazı işlemleri yapar ve framebuffer'ı serbest bırakırız. Bağlama işlemi için glBindFramebuffer fonksiyonu kullanılır: 
```cpp
glBindFramebuffer(GL_FRAMEBUFFER, fbo);
```
Framebuffer nesnesi, GL_FRAMEBUFFER hedefi ile bağlandığında, işlemler o andaki bağlı framebuffer'ı etkileyecektir. Ayrıca, bir framebuffer'ı GL_READ_FRAMEBUFFER veya GL_DRAW_FRAMEBUFFER ile sırasıyla okuma veya yazma hedeflerine bağlamak da mümkündür. GL_READ_FRAMEBUFFER öğesine bağlanan framebuffer, glReadPixels gibi tüm okuma işlemleri için kullanılırken, GL_DRAW_FRAMEBUFFER'a bağlanan framebuffer sahenleme, temizleme ve diğer yazma işlemlerinde hedef olarak kullanılır.
Çoğu zaman bu ayrımı yapmanıza gerek kalmayacak ve genellikle her ikisine de GL_FRAMEBUFFER ile bağlanacaksınız.

Ne yazık ki, framebuffer'ımızı henüz kullanamıyoruz çünkü <span style="color:green">tamamlanmadı. </span> Bir framebuffer'ın tamamlanabilmesi için aşağıdaki gereksinimler gerçekleşmelidir:

* En az bir tampon ile ilişkilendirmek zorundayız (renk, derinlik ya da şablon tamponu).
* En az bir renk ilişkilendirilmiş olmalıdır.
* Tüm ekler tamamlanmış olmalıdır (ayrılmış bellek).
* Her tamponun aynı sayıda **örneği** olmalıdır.

Eğer örneklerin ne olduğunu bilmiyorsanız endişelenmeyin,çünkü bir sonraki eğitselde bunu öğreneceğiz.

Gereksinimlerden, framebuffer için bir tür bağlantı oluşturmamız ve bu bağlantıyı framebuffer ile ilişkilendirmemiz gerektiği açıktır. Tüm gereklilikleri tamamladıktan sonra glCheckFramebufferStatus'u GL_FRAMEBUFFER ile çağırarak framebuffer'ı başarıyla tamamlayıp tamamlamadığımızı kontrol edebiliriz. "GL_FRAMEBUFFER_COMPLETE" döndürülürse devam etmeye hazırız: 

```cpp
if(glCheckFramebufferStatus(GL_FRAMEBUFFER) == GL_FRAMEBUFFER_COMPLETE)
  // zafer dansı başlasın
```
Sonraki tüm sahneleme işlemleri, şu anda bağlı olan framebuffer eklerine işlenecektir. Framebuffer, bizim varsayılan frameebuffer'ımız olmadığı için sahneleme komutlarının pencerenin görsel çıktısı üzerinde bir etkisi olmaz. Bu sebeple, farklı bir framebuffer sahnelenmesi <span style="color:green">ekran dışı sahneleme (ing. off-screen rendering) </span> olarak adlandırılır. Tüm sahneleme işlemlerinin ana pencere üzerinde görsel bir etkisi olmasını sağlamak için, varsayılan framebuffer 0'a bağlanarak tekrar etkin hale getirilmelidir:

```cpp
glBindFramebuffer(GL_FRAMEBUFFER, 0);
```
Tüm framebuffer işlemleriyle işimiz bittiğinde, framebuffer nesnesini silmeyi unutmayın:
```cpp
glDeleteFramebuffers(1, &fbo);
```
Şimdi bütünlük kontrolü yapılmadan önce, framebuffer ile bir veya daha fazla <span style="color:green">bağ (ing. attachment) </span> kurmamız gerekir. Bir bağ, framebuffer için tampon görevi görebilen, onu imge olarak düşünebilen bir hafıza alanıdır. Bir bağ oluştururken iki seçeneğimiz var: Dokular veya <span style="color:green">renderbuffer </span>  nesneler.

## Doku Bağlantısı

Bir dokuyu framebuffer'a bağlarken, tüm sahneleme komutları dokuya normal bir renk/derinlik ya da şablon tamponu gibi uygulanacaktır. Doku kullanmanın avantajı, tüm sahneleme işlemlerinin sonucunun gölgelendiricilerimizde kolayca kullanabileceğimiz bir doku imgesi olarak saklanmasıdır.

Bir framebuffer için doku oluşturmak, normal bir doku oluşturmayla kabaca aynıdır:
```cpp
unsigned int texture;
glGenTextures(1, &texture);
glBindTexture(GL_TEXTURE_2D, texture);
  
glTexImage2D(GL_TEXTURE_2D, 0, GL_RGB, 800, 600, 0, GL_RGB, GL_UNSIGNED_BYTE, NULL);

glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_MIN_FILTER, GL_LINEAR);
glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_MAG_FILTER, GL_LINEAR); 
```
Buradaki ana fark, boyutları ekran boyutuna eşit ayarlamamız(nu gerekli olmasa da) ve NULL değerini dokunun veri parametresi olarak iletmemizdir. Bu doku için, sadece bellekte yer ayırıyoruz ve aslında doldurmuyoruz. Dokuyu doldurmak, framebuffer'e gönderdiğimiz anda gerçekleşir. Ayrıca, çoğu durumda bunlara ihtiyaç duymayacağımızdan, paketleme (ing. wrapping) yöntemlerinin veya mipmaplerin hiçbirini umursamadığımızı da unutmayın.

>Tüm ekranınızı daha küçük veya daha büyük bir boyuta sahip bir dokuya dönüştürmek istiyorsanız, dokunuzdaki yeni boyutlarla glViewport'u tekrar çağırmanız gerekir (famebuffer'da oluşturmadan önce), aksi takdirde, doku veya ekranın sadece küçük bir kısmı doku üzerine çizilir.

Şimdi bir doku oluşturduğumuza göre, yapmamız gereken son şey onu framebuffer'a bağlamak.

```cpp
glFramebufferTexture2D(GL_FRAMEBUFFER, GL_COLOR_ATTACHMENT0, GL_TEXTURE_2D, texture, 0);
```
The glFrameBufferTexture2D has the following parameters:

* target: hedeflediğimiz framebuffer tipi (çizme, okuma ya da her ikisi).
* attachment: bağ tipi. Şu anda bir renk bağı ile bağlantı sağlıyoruz. Sonundaki 0'ın, daha fazla renk eki ekleyebileceğimizi ifade ettiğini unutmayın. Daha sonraki bir derste buna başlayacağız.
* textarget: bağlamak istediğimiz dokunun tipi
* texture: bağlantı sağlanacak doku
* level: mipmap seviyesi. Bunu 0'da tutuyoruz.
 
Renk bağlantılarının yanı sıra, framebuffer nesnesine bir derinlik ve şablon dokusu de ekleyebiliriz. Derinlik eklemek için, bağlantı türünü GL_DEPTH_ATTACHMENT olarak belirtiyoruz.Doku formatının ve **dahili biçim (ing. internalformat)** türünün, derinlik tamponunun depolama formatını yansıtması için GL_DEPTH_COMPONENT olması gerektiğini unutmayın. Bir şablon tamponu eklemek için ikinci argüman olarak GL_STENCIL_ATTACHMENT kullanır ve doku formatlarını GL_STENCIL_INDEX olarak belirtirsiniz.

Tek bir doku olarak hem bir derinlik tamponu hem de bir şablon tamponu eklemek de mümkündür. Dokunun her 32-bitlik değeri daha sonra 24-bit derinlik bilgisi ve 8-bit şablon bilgisi için oluşur. Bir doku olarak derinlik ve şablon tamponu eklemek için GL_DEPTH_STENCIL_ATTACHMENT tipini kullanırız ve dokunun formatlarını derinlik ve şablon değerleri içerecek şekilde yapılandırırız. Altyapıya bir doku olarak bir derinlik ve şablon tamponu bağlama örneği aşağıda verilmiştir:

```cpp
glTexImage2D(
  GL_TEXTURE_2D, 0, GL_DEPTH24_STENCIL8, 800, 600, 0, 
  GL_DEPTH_STENCIL, GL_UNSIGNED_INT_24_8, NULL
);

glFramebufferTexture2D(GL_FRAMEBUFFER, GL_DEPTH_STENCIL_ATTACHMENT, GL_TEXTURE_2D, texture, 0);
```
## Renderbuffer Nesne Bağlantısı

**Renderbuffer nesneler** OpenGL'e dokulardan sonra olası bir framebuffer bağlantı türü olarak getirildi. Tıpkı bir doku imgesi gibi, bir renderbuffer nesnesi de gerçek bir arabellektir. Örneğin; bayt dizisi, tamsayılar, pikseller vb. Bir renderbuffer nesnesi, verilerini OpenGL'in yerel sahneleme biçiminde saklamasına rağmen ek bir avantaja sahiptir, bu da ekran dışı sahneleme framebuffer'ı için optimize edilmesini sağlar.

Renderbuffer nesneleri, tüm sahneleme verilerini, dokuya özgü biçimlerde herhangi bir dönüşüm yapmadan doğrudan arabelleğine depolar, böylece, onları yazılabilir bir depolama ortamı olarak daha hızlı hale getirir. Bununla birlikte, renderbuffer nesneleri genellikle salt-yazılırdır. Bu nedenle onlardan okuma yapılamaz (doku erişiminde olduğu gibi).Onları glReadPixels işlevi ile okumak mümkündür, ancak doğrudan bağlantının kendisinden değil.

Verileri zaten kendi biçiminde olduğundan, veri yazarken veya basitçe verilerini diğer tamponlara kopyalarken oldukça hızlıdırlar. Geçiş(ing. switching) arabellekleri gibi işlemler, renderbuffer nesneleri kullanırken bu nedenle oldukça hızlıdır. Her sahneleme yinelemesinin sonunda kullandığımız glfwSwapBuffers işlevi, renderbuffer nesneleri ile de uygulanabilir:Biz sadece bir renderbuffer imgeye yazıyoruz ve sonunda diğerine takas yapıyoruz. Renderbuffer nesneleri bu tür işlemler için mükemmeldir.

Renderbuffer nesnesi oluşturma, framebuffer koduna benzerdir:
```cpp
unsigned int rbo;
glGenRenderbuffers(1, &rbo);
```
Ve benzer şekilde tüm sonraki renderbuffer işlemleri, geçerli rbo'yu etkileyecek şekilde renderbuffer nesnesini bağlıyoruz
```cpp
glBindRenderbuffer(GL_RENDERBUFFER, rbo);
```
Renderbuffer nesneleri genellikle salt-yazılır olduklarından, derinlik ve şablon bağlantıları olarak kullanılırlar. Çünkü çoğu zaman derinlik ve şablon tamponlarından değerleri okumamıza gerek kalmaz, ancak yine de derinlik testi ve şablon testini önemsiyoruz. Test için derinlik ve şablon değerlerine ihtiyacımız var, ancak bu değerleri örneklememiz gerekmiyor. Bu tamponlardan örnekleme yapmadığımız zaman, renderbuffer nesnesi genellikle daha optimize olduğu için tercih edilmektedir.

Derinlik ve şablon renderbuffer nesnesi oluşturma, glRenderbufferStorage işlevinin çağrılması ile gerçekleştirilir: 
```cpp
glRenderbufferStorage(GL_RENDERBUFFER, GL_DEPTH24_STENCIL8, 800, 600);
```
Bir renderbuffer nesnesi oluşturma işlemi doku nesnelerininkine benzerdir. Aradaki fark, bu nesnenin, doku gibi genel amaçlı bir veri tamponu yerine, bir imge olarak kullanılmak üzere özel olarak tasarlanmasıdır. Burada, hem derinlik hem de şablon tamponunu sırasıyla 24 ve 8 bit ile tutan dahili biçim olarak GL_DEPTH24_STENCIL8'i seçtik.

Yapılması gereken son şey aslında renderbuffer nesnesini bağlamaktır:
```cpp
glFramebufferRenderbuffer(GL_FRAMEBUFFER, GL_DEPTH_STENCIL_ATTACHMENT, GL_RENDERBUFFER, rbo);
```
Renderbuffer nesneleri, framebuffer projelerinizde bazı iyileştirmeler sağlayabilir, ancak ne zaman renderbuffer nesnei ve ne zaman doku olarak kullanılacağının bilinmesi önemlidir. Verileri belirli bir tampondan hiçbir zaman örneklemeniz gerekmiyorsa, o belirli tampon için bir renderbuffer nesnesini kullanmak akıllıca olandır. Bir gün veriyi renk veya derinlik değerleri gibi belirli bir tampondan örneklemeniz gerekirse, bunun yerine bir doku kullanmanız gerekir. Performans açısından olsa da, muazzam derecede etkisi olmaz.

## Bir Dokuyu Sahneleme
Framebuffer'ın nasıl çalıştığını bildiğimize göre artık onları kullanmanın vakti geldi. Yarattığımız bir framebuffer nesnesine bağlı renkli bir dokuyu sahneleyeceğiz ve sonra tüm ekrana yayılan basit bir dörtlü (ing. quad) üzerinde bu dokuyu çizeceğiz. Görsel çıktı, çerçevesiz olarak aynıdır, ancak bu kez hepsi tek bir dörtlünün üstüne basılmıştır. Bu neden yararlıdır? Bir sonraki bölümde nedenini göreceğiz.

Yapılması gereken ilk şey, gerçek bir framebuffer nesnesi oluşturmak ve bağlamaktır, bu nispeten basittir:
```cpp
unsigned int framebuffer;
glGenFramebuffers(1, &framebuffer);
glBindFramebuffer(GL_FRAMEBUFFER, framebuffer);
```
Sonra,framebuffer'a renk bağlantısı olarak eklediğimiz doku imgesi oluştururuz. Dokunun boyutlarını pencerenin genişliğine ve yüksekliğine eşit olarak belirliyor ve verilerini ilklendirmemiş olarak tutuyoruz:
```cpp
// doku üret
unsigned int texColorBuffer;
glGenTextures(1, &texColorBuffer);
glBindTexture(GL_TEXTURE_2D, texColorBuffer);
glTexImage2D(GL_TEXTURE_2D, 0, GL_RGB, 800, 600, 0, GL_RGB, GL_UNSIGNED_BYTE, NULL);
glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_MIN_FILTER, GL_LINEAR );
glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_MAG_FILTER, GL_LINEAR);
glBindTexture(GL_TEXTURE_2D, 0);

// mevcur framebuffer nesnesine bağla
glFramebufferTexture2D(GL_FRAMEBUFFER, GL_COLOR_ATTACHMENT0, GL_TEXTURE_2D, texColorBuffer, 0);
```
Ayrıca OpenGL'in derinlik testi yapabildiğinden emin olmak istiyoruz (ve eğer isterseniz şablon testi), bu nedenle framebuffer'a derinlik (ve şablon) bağlantısı eklemeyi de sağlamalıyız. Sadece renk tamponu örnekleyeceğimizden ve diğer tamponları örneklemediğimizden, bu amaç için bir renderbuffer nesnesi oluşturabiliriz. Belirli tampondan örnekleme yapmayacağınız zaman iyi bir seçim olduklarını unutmayın.

Bir renderbuffer nesnesi oluşturmak çok zor değil. Hatırlamamız gereken tek şey, onu derinlik ve şablon eki oluşturma nesnesi olarak oluşturmamızdır. Dahili formatını, amaçlarımız için yeterince hassas olan GL_DEPTH24_STENCIL8 olarak ayarladık.

```cpp
unsigned int rbo;
glGenRenderbuffers(1, &rbo);
glBindRenderbuffer(GL_RENDERBUFFER, rbo); 
glRenderbufferStorage(GL_RENDERBUFFER, GL_DEPTH24_STENCIL8, 800, 600);  
glBindRenderbuffer(GL_RENDERBUFFER, 0);
```

Renderbuffer nesnesi için yeterli bellek ayırdıktan sonra, renderbuffer bağını serbest bırakabiliriz.

Sonra, framebuffer'ı tamamlayabilmemiz için son bir adım olarak renderbuffer nesnesini, framebuffer'ın derinlik ve şablon bağına bağlarız:

```cpp
glFramebufferRenderbuffer(GL_FRAMEBUFFER, GL_DEPTH_STENCIL_ATTACHMENT, GL_RENDERBUFFER, rbo);
```

Son bir önlem olarak, framebuffer'ın gerçekten tamamlanıp tamamlanmadığını kontrol etmek istiyoruz ve değilse, bir hata mesajı yazdırıyoruz.

```cpp
if(glCheckFramebufferStatus(GL_FRAMEBUFFER) != GL_FRAMEBUFFER_COMPLETE)
	std::cout << "ERROR::FRAMEBUFFER:: Framebuffer is not complete!" << std::endl;
glBindFramebuffer(GL_FRAMEBUFFER, 0);
```
Sonra da yanlış framebuffer'ın sahnelenmediğinden emin olabilmek için bağın çözüldüğünden emin olun.

Madem ki framebuffer tamamlandı, framebuffer sahnelemek için yapmamız gereken şey, varsayılan framebuffer yerine framebuffer nesnelerine bağlamaktır. Sonraki tüm sahneleme komutları şu anda bağlı olan framebuffer'ı etkileyecektir. Tüm derinlik ve şablon işlemleri, eğer varsa, bağlı framebuffer derinlik ve şablon bağlantılarından da okunacaktır. Örneğin bir derinlik tamponunu unutacak olursanız, derinlik testi işlemleri artık çalışmayacaktır, çünkü şu anda bağlı framebuffer'da bir derinlik tamponu mevcut değildir.

Dolayısıyla sahneyi tek bir doku ile çizmek için aşağıdaki adımları izlememiz gerekir:

 1. Sahneyi, etkin framebuffer olarak bağlı yeni framebuffer ile oluşturun.
 2. Varsayılan framebuffer'a bağlanın
 3. Tüm ekranı, yeni framebuffer'ın renk tamponunu kullanarak doku olarak kaplayan bir dörtlü çizin.

Derinlik testi eğitselindeki aynı sahneyi çizeceğiz, ancak bu sefer eski okul konteyner dokusunu kullanarak.

Dörtlüyü çizmek için yeni bir basit gölgelendirici seti oluşturacağız. Herhangi bir süslü püslü matris dönüşümünü dahil etmeyeceğiz. Çünkü yalnızca köşe koordinatlarını normalize cihaz koordinatları olarak vereceğiz, böylece bunları doğrudan köşe gölgelendiricisinin çıktısı olarak belirleyebiliriz. Kçşe gölgelendiricisi şöyle görünüyor:

```cpp
#version 330 core
layout (location = 0) in vec2 aPos;
layout (location = 1) in vec2 aTexCoords;

out vec2 TexCoords;

void main()
{
    gl_Position = vec4(aPos.x, aPos.y, 0.0, 1.0); 
    TexCoords = aTexCoords;
}
```
Çok süslü bir şey yok. Yapmamız gereken tek şey dokudan bir örnek almak olduğu için parça gölgelendiricisi daha da basit olacaktır: 

```cpp
#version 330 core
out vec4 FragColor;
  
in vec2 TexCoords;

uniform sampler2D screenTexture;

void main()
{ 
    FragColor = texture(screenTexture, TexCoords);
} 
```
Ekran için bir VAO oluşturmak ve yapılandırmak size kalmıştır. Framebuffer işleminin bir sahneleme yinelemesi aşağıdaki yapıya sahiptir:
```cpp
// ilk geçiş
glBindFramebuffer(GL_FRAMEBUFFER, framebuffer);
glClearColor(0.1f, 0.1f, 0.1f, 1.0f);
glClear(GL_COLOR_BUFFER_BIT | GL_DEPTH_BUFFER_BIT); // şablon tamponunu şimdi kullanmıyoruz.
glEnable(GL_DEPTH_TEST);
DrawScene();	
  
// ikinci geçiş
glBindFramebuffer(GL_FRAMEBUFFER, 0); // varsayılana geri dön
glClearColor(1.0f, 1.0f, 1.0f, 1.0f); 
glClear(GL_COLOR_BUFFER_BIT);
  
screenShader.use();  
glBindVertexArray(quadVAO);
glDisable(GL_DEPTH_TEST);
glBindTexture(GL_TEXTURE_2D, textureColorbuffer);
glDrawArrays(GL_TRIANGLES, 0, 6);
```
Dikkat edilmesi gereken birkaç şey var. Öncelikle, kullandığımız her framebuffer kendi arabellek kümesine sahip olduğundan, bu arabelleklerin her birini glClear işlevini çağırarak ayarlanan uygun bitlerle siliyoruz. İkincisi, dörtlü çizerken, derinlik testini devre dışı bırakıyoruz çünkü derinlik testini gerçekten umursamıyoruz çünkü basit bir dörtlü çiziyoruz; normal sahneyi çizerken yine derinlik testini etkinleştirmek zorunda kalacağız.

Burada yanlış gidebilecek bazı adımlar vardır, bu nedenle çıktınız yoksa, hata ayıklamayı (ing. debug) deneyin ve eğitselin ilgili bölümlerini yeniden okuyun. Her şey başarılı bir şekilde çalıştıysa, şuna benzeyen görsel bir sonuç alırsınız. 

<img src=https://learnopengl.com/img/advanced/framebuffers_screen_texture.png>

Solda, derinlik testi eğitiminde gördüğümüzle aynı olan görsel çıktı gösteriliyor, ancak bu kez basit bir dörtlü oluşturuldu. Sahneyi tel kafes içinde oluşturursak, görünür hale gelir, varsayılan çerçevede yalnızca bir dörtlü çizdik.

[Burada](https://learnopengl.com/code_viewer_gh.php?code=src/4.advanced_opengl/5.1.framebuffers/framebuffers.cpp) uygulamanın kaynak kodunu bulabilirsiniz.

Tamamen sahnelenmiş piksellerinin her birine tek bir doku imgesi olarak serbestçe erişebildiğimiz için, parça gölgelendiricisinde bazı ilginç efektler oluşturabiliriz. Tüm bu ilginç etkilerin kombinasyonuna son-işlem efekti denir.

# Post-processing

Now that the entire scene is rendered to a single texture we can create some interesting effects simply by manipulating the texture data. In this section we'll show you some of the more popular post-processing effects and how you might create your own with some added creativity.

Let's start with one of the simplest post-processing effects.
## Inversion

We have access to each of the colors of the render output so it's not so hard to return the inverse of these colors in the fragment shader. We're taking the color of the screen texture and inverse it by subtracting it from 1.0: 
```cpp
void main()
{
    FragColor = vec4(vec3(1.0 - texture(screenTexture, TexCoords)), 1.0);
}
```
While inversion is a relatively simple post-processing effect it already creates funky results: 

<img src=https://learnopengl.com/img/advanced/framebuffers_inverse.png>

 The entire scene now has all its colors inversed with a single line of code in the fragment shader. Pretty cool huh?
## Grayscale

Another interesting effect is to remove all colors from the scene except the white, gray and black colors effectively grayscaling the entire image. An easy way to do this is simply by taking all the color components and averaging their results: 
```glsl
void main()
{
    FragColor = texture(screenTexture, TexCoords);
    float average = (FragColor.r + FragColor.g + FragColor.b) / 3.0;
    FragColor = vec4(average, average, average, 1.0);
} 
```
This already creates pretty good results, but the human eye tends to be more sensitive to green colors and the least to blue, so to get the most physically accurate results we'll need to use weighted channels: 
```glsl
void main()
{
    FragColor = texture(screenTexture, TexCoords);
    float average = 0.2126 * FragColor.r + 0.7152 * FragColor.g + 0.0722 * FragColor.b;
    FragColor = vec4(average, average, average, 1.0);
}  
```
<img src=https://learnopengl.com/img/advanced/framebuffers_grayscale.png>

 You probably won't notice the difference right away, but with more complicated scenes, such a weighted grayscaling effect tends to be more realistic.
## Kernel effects

Another advantage about doing post-processing on a single texture image is that we can actually sample color values from other parts of the texture. We could for example take a small area around the current texture coordinate and sample multiple texture values around the current texture value. We can then create interesting effects by combining them in creative ways.

A kernel (or convolution matrix) is a small matrix-like array of values centered on the current pixel that multiplies surrounding pixel values by its kernel values and adds them all together to form a single value. So basically we're adding a small offset to the texture coordinates in surrounding directions of the current pixel and combine the results based on the kernel. An example of a kernel is given below: 

\begin{bmatrix}2 & 2 & 2 \\ 2 & -15 & 2 \\ 2 & 2 & 2 \end{bmatrix}

 This kernel takes 8 surrounding pixel values and multiplies them by 2 and the current pixel by -15. This example kernel basically multiplies the surrounding pixels by a weight determined in the kernel and balances the result by multiplying the current pixel by a large negative weight.
Most kernels you'll find over the internet all sum up to 1 if you add all the weights together. If they don't add up to 1 it means that the resulting texture color ends brighter or darker than the original texture value.

Kernels are an extremely useful tool for post-processing since they're quite easy to use, experiment with and a lot of examples can be found online. We do have to slightly adapt the fragment shader a bit to actually support kernels. We make the assumption that each kernel we'll be using is a 3x3 kernel (which most kernels are): 

```glsl
const float offset = 1.0 / 300.0;  

void main()
{
    vec2 offsets[9] = vec2[](
        vec2(-offset,  offset), // top-left
        vec2( 0.0f,    offset), // top-center
        vec2( offset,  offset), // top-right
        vec2(-offset,  0.0f),   // center-left
        vec2( 0.0f,    0.0f),   // center-center
        vec2( offset,  0.0f),   // center-right
        vec2(-offset, -offset), // bottom-left
        vec2( 0.0f,   -offset), // bottom-center
        vec2( offset, -offset)  // bottom-right    
    );

    float kernel[9] = float[](
        -1, -1, -1,
        -1,  9, -1,
        -1, -1, -1
    );
    
    vec3 sampleTex[9];
    for(int i = 0; i < 9; i++)
    {
        sampleTex[i] = vec3(texture(screenTexture, TexCoords.st + offsets[i]));
    }
    vec3 col = vec3(0.0);
    for(int i = 0; i < 9; i++)
        col += sampleTex[i] * kernel[i];
    
    FragColor = vec4(col, 1.0);
}  
```
 In the fragment shader we first create an array of 9 vec2 offsets for each surrounding texture coordinate. The offset is simply a constant value that you could customize to your liking. Then we define the kernel, which in this case is a sharpen kernel that sharpens each color value by sampling all surrounding pixels in an interesting way. Lastly, we add each offset to the current texture coordinate when sampling and then multiply these texture values with the weighted kernel values that we add together.

This particular sharpen kernel looks like this: 

<img src=https://learnopengl.com/img/advanced/framebuffers_sharpen.png>

 This could create some interesting effects of where your player might be on a narcotic adventure.
## Blur

A kernel that creates a blur effect is defined as follows: 

\begin{bmatrix} 1 & 2 & 1 \\ 2 & 4 & 2 \\ 1 & 2 & 1 \end{bmatrix} / 16

Because all values add up to 16, simply returning the combined sampled colors would result in an extremely bright color so we have to divide each value of the kernel by 16. The resulting kernel array would then become:
```glsl
float kernel[9] = float[](
    1.0 / 16, 2.0 / 16, 1.0 / 16,
    2.0 / 16, 4.0 / 16, 2.0 / 16,
    1.0 / 16, 2.0 / 16, 1.0 / 16  
);
```
By changing the kernel float array in the fragment shader we're completely changing the post-processing effect we're after. It now looks something like this: 

<img src= https://learnopengl.com/img/advanced/framebuffers_blur.png>

 Such a blur effect creates interesting possibilities. We could vary the blur amount over time for example to create the effect of someone being drunk, or increase the blur whenever the main character is not wearing glasses. Blurring also give us a useful utility to smooth color values which we will use in later tutorials.

You can see that once we have such a little kernel implementation in place it is quite easy to create cool post-processing effects. Let's show you a last popular effect to finish this discussion.
Edge detection

Below you can find an edge-detection kernel that is similar to the sharpen kernel: 

\begin{bmatrix} 1 & 1 & 1 \\ 1 & -8 & 1 \\ 1 & 1 & 1 \end{bmatrix}

This kernel highlights all edges and darkens the rest, which is quite useful when we only care about edges in an image. 

<img src=https://learnopengl.com/img/advanced/framebuffers_edge_detection.png>

It probably does not come as a surprise that kernels like this are used as image-manipulating tools/filters in tools like Photoshop. Because of a graphic card's ability to process fragments with extreme parallel capabilities, we can manipulate images on a per-pixel basis in real-time with relative ease. Image-editing tools therefore tend to use graphics cards more often for image-processing. 

Çeviri: [Nezihe Sözen](https://github.com/NeziheSozen)
