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
 OpenGL'in harika bir özelliği, uzantıların desteklenmesidir. Bir grafik şirketi yeni bir teknik veya sahneleme için yeni bir optimizasyon sunduğunda bu genellikle sürücülerde uygulanan bir uzantıda bulunur. Uygulamanın çalıştığı donanım böyle bir uzantıyı destekliyorsa, geliştirici daha gelişmiş veya verimli grafikler için uzantı tarafından sağlanan işlevselliği kullanabilir. Bu şekilde, bir grafik uygulama geliştiricisi, OpenGL'nin gelecekteki sürümlerinin işlevselliği içermesini beklemeden, yalnızca uzantının grafik kartı tarafından desteklenip desteklenmediğini kontrol ederek bu yeni sahneleme tekniklerini kullanabilir. Genellikle bir uzantı popüler veya çok yararlı olduğunda, gelecekteki OpenGL sürümlerinin bir parçası haline gelmektedir.

Geliştirici daha sonra bu uzantıların herhangi birinin mevcut olup olmadığını sorgulamak zorundadır (veya bir OpenGL eklenti kütüphanesi kullanmalı). Bu, uzantının olup olmadığına bağlı olarak işleri daha iyi veya daha verimli yapmasını sağlar: 

```cpp
if(GL_ARB_extension_name)
{
    // Donanım tarafından desteklenen yeni ve modern şeyler yapın
}
else
{
    // Uzantı desteklenmiyor: Eski yöntemden yapın
}
```
 OpenGL sürüm 3.3 ile çoğu teknik için nadiren bir uzantıya ihtiyaç duyarız, ancak gerekli olan her yerde uygun talimatlar verilir.
### Durum Makinesi

OpenGL başlı başına büyük bir durum makinesidir(ing. state machine): Bu durum makinesi, OpenGL'nin şu anda nasıl çalışması gerektiğini tanımlayan bir değişkenler topluluğudur. OpenGL durumu genellikle OpenGL bağlamı(ing. context) olarak adlandırılır. OpenGL kullanırken, genellikle bazı seçenekleri ayarlayarak, bazı tamponları değiştirerek ve ardından mevcut içeriği kullanarak, sahneleyerek durumunu değiştiririz.

OpenGL'e üçgen yerine çizgiler çizmek istediğimizi söylediğimizde, OpenGL çizimini ayarlayan bazı bağlam değişkenlerini değiştirerek aslında OpenGL durumunu değiştiririz. Opengl'e çizgiler çizmesi gerektiğini söyleyerek durumu değiştirdiğimizde, bir sonraki çizim komutları şimdi üçgen yerine çizgiler çizecektir.

When working in OpenGL we will come across several state-changing functions that change the context and several state-using functions that perform some operations based on the current state of OpenGL. As long as you keep in mind that OpenGL is basically one large state machine, most of its functionality will make more sense.
### Nesneler

OpenGL kütüphaneleri C dilinde yazılmıştır ve diğer dillerde birçok türetim için izin verir, fakat özünde bir C kütüphanesi olarak kalır. C'nin dil yapılarının çoğu, bunu diğer üst düzey dillere pek iyi çevirmediğinden, OpenGL birçok soyutlama düşünülerek geliştirilmiştir. Bu soyutlamalardan biri OpenGL'deki nesnelerdir (ing. objects).

OpenGL'deki bir nesne, OpenGL'in durumunun bir alt kümesini temsil eden seçenekler topluluğudur. Örneğin, çizim penceresinin ayarlarını temsil eden bir nesneye sahip olabiliriz. Daha sonra boyutunu, kaç rengi desteklediğini vb. belirleyebiliriz. Bir nesne C-dili benzeri bir yapı(ing. struct) olarak görselleştirebilir:


```cpp
struct object_name {
    float  option1;
    int    option2;
    char[] name;
};
```
Bir nesne kullanmak istediğimizde genellikle şöyle görünür(OpenGL bağlamı büyük bir yapı olarak görselleştirilmiştir): 
```cpp
// OpenGL'in durumu
struct OpenGL_Context {
  	...
  	object* object_Window_Target;
  	...  	
};
```
```cpp
// nesne oluştur
unsigned int objectId = 0;
glGenObject(1, &objectId);
// nesneyi bağla
glBindObject(GL_WINDOW_TARGET, objectId);
// set options of object currently bound to GL_WINDOW_TARGET
glSetObjectOption(GL_WINDOW_TARGET, GL_OPTION_WINDOW_WIDTH, 800);
glSetObjectOption(GL_WINDOW_TARGET, GL_OPTION_WINDOW_HEIGHT, 600);
// set context target back to default
glBindObject(GL_WINDOW_TARGET, 0);
```

Bu küçük kod parçası, OpenGL'de çalışırken sık sık göreceğiniz bir iş akışıdır. Önce bir nesne yaratır ve nesnenin referansını Id olarak saklarız (gerçek nesne verileri sahne arkasında saklanır). Daha sonra nesneyi bağlamın hedef konumuna bağlarız (örnek pencere nesnesi hedefinin konumu GL_WINDOW_TARGET olarak tanımlanır). Daha sonra pencere seçeneklerini belirler ve nihayet pencere hedefinin geçerli nesne kimliğini 0 olarak ayarlayarak nesneyi serbest bırakırız. Belirlediğimiz seçenekler objectId tarafından başvurulan nesnede saklanır ve nesneyi tekrar GL_WINDOW_TARGET'a bağladığımız anda geri yüklenir.

>Şimdiye kadar sağlanan kod örnekleri, OpenGL'in nasıl işlediğine dair sadece yaklaşımlar sunmaktadır. Eğitsel boyunca yeterli gerçek örneklerle karşılaşacaksınız. 

Bu nesneleri kullanmanın en iyi yanı, uygulamamızdaki birden fazla nesneyi tanımlayabilmemiz, seçeneklerini belirleyebilmemiz ve OpenGL'nin durumunu kullanan bir işleme başladığımızda, nesneyi tercih edilen ayarlarımıza bağlayabilmemizdir. Örneğin 3B model verileri için konteyner nesneleri görevi gören nesneler vardır (bir ev veya karakter gibi) ve bunlardan birini çizmek istediğimizde, çizmek istediğimiz model verilerini içeren nesneyi bağlarız (önce oluştururuz ve nesne seçeneklerini ayarlarız). Birkaç nesneye sahip olmak, birçok model belirlememize izin verir ve ne zaman belirli bir model çizmek istersek, çizimden önce tüm seçeneklerini tekrar ayarlamadan ilgili nesneyi basitçe bağlarız.

### Hadi Başlayalım

Bir spesifikasyon ve kütüphane olarak OpenGL hakkında biraz şey öğrendiniz. OpenGL'in yaklaşık olarak  nasıl çalıştığını ve OpenGL'in kullandığı birkaç özel püf noktayı öğrendiniz. Hepsini anlamadıysanız endişelenmeyin; eğitsel boyunca adım adım işleyeceğiz ve OpenGL'i gerçekten kavramak için yeterli örnekler göreceğiz. Bir sonraki adıma hazırsanız, OpenGL bağlamını ve ilk penceremizi oluşturmaya başlayabiliriz [here](https://cg-translators.github.io/learnopengl-tr/getting_started/creating_a_window.html). 

### Ek Kaynaklar

[opengl.org](https://www.opengl.org/): OpenGL'in resmi web sitesi

[OpenGL registry](https://www.khronos.org/registry/OpenGL/): tüm OpenGL sürümleri için OpenGL özellikleri ve uzantıları barındırır.


##### Orijinal Kaynak
https://learnopengl.com/Getting-started/OpenGL

##### Çeviri: [Nezihe Sözen](https://github.com/NeziheSozen)
