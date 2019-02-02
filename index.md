---
title: "OpenGL'e Hoşgeldiniz"
keywords: sample homepage
tags: [OpenGL'e Hoşgeldiniz]
sidebar: main_sidebar
permalink: index.html
---
OpenGL grafik API'si için iyi geliştiirlmiş bu mütevazi öğretim platformuma hoşgeldiniz. OpenGL'i akademik amaçlar için öğrenmeye, kariyerine devam etmeye ya da sadece bir hobi bulmaya çalışıyor olsanız da, bu site size Modern (çekirdek-profili) OpenGL kullanarak temel, orta ve tüm gelişmiş bilgileri öğretecektir. LearnOpenGL’in amacı, Modern OpenGL’i anlaşılması kolay  açık örnekler ile göstermek ve aynı zamanda daha sonraki çalışmalar için faydalı bir referans sunmaktır.

## Bu Eğitselleri Neden Okumalıyız?

İnternette OpenGL öğrenmeye dair binlerce belge ve kaynak var, ancak bu kaynakların çoğu yalnızca OpenGL'in anlık durum moduna odaklanır (genellikle eski OpenGL olarak adlandırılır) veya eksiktir, uygun belgeler içermez, öğrenim tercihleriniz için uygun değildir. Bu nedenle amacım hem tam hem de anlaşılması kolay bir platform sağlamak.

<img src="https://learnopengl.com/img/index_image2.png">

Eğer adım adım talimatlar veren, açık örnekler sunan ve sizi milyonlarca ayrıntı ile derinlere atmayacak olan eğitseller okumaktan hoşlanıyorsanız, bu eğitseller muhtemelen sizin içindir. Eğitseller, grafik programlama deneyimi olmayan insanlar için anlaşılabilir olmayı amaçlamaktadır, ancak daha deneyimli kullanıcılar için hâlâ önemlidir. Eğitseller ayrıca, fikirlerinizi gerçek 3B uygulamalara dönüştürebilecek pratik kavramları da tartışıyor olacak.


## Neler Öğreneceksiniz?

Bu eğitsellerin odağında Modern OpenGL vardır. Modern OpenGL'i öğrenmek (ve kullanmak), güçlü bir grafik programlama bilgisi ve OpenGL'in nasıl çalıştığını en iyi şekilde anlamanızı gerektirir. Bu yüzden temel grafik özelliklerini tartışarak başlayacağız, OpenGL'in ekranınıza gerçekte pikselleri nasıl çizdiği ve bazı efektler oluşturmak için bu bilgiyi nasıl kullanabileceğimizi tartışacağız.

Temel bilgilerin yanı sıra, uygulamalarınız için kullanabileceğiniz bir çok faydalı tekniği tartışacağız: Bir sahneyi bir ucundan bir ucuna çizmek, güzel bir aydınlatma oluşturmak, bir modelleme programından özel yapılmış nesneler yüklemek, harika son-işlem yöntemler gerçekleştirmek ve çok daha fazlası. Ayrıca, OpenGL bilgisine dayanarak küçük bir oyun oluşturduğumuz adım adım eğitsel özelliğine de sahibiz. Bu yüzden gerçekten grafik programlamanın nasıl bir şey olduğunu hissedeceksiniz.

Sitenin ilerleyişi ve / veya diğer LearnOpenGL haberleri hakkında güncel bilgi almak istiyorsanız, lütfen beni [Twitter](https://twitter.com/JoeyDeVriez)'da takip edin.


# Giriş

Buraya geldiğinize göre muhtemelen bilgisayar grafiklerinin iç çalışmasını öğrenmek istiyorsunuz. Bir şeyleri kendiniz yapmanız son derece eğlenceli ve sizi her işin altından kalkmanızı sağlayarak grafik programlamayı harika bir şekilde anlamanızı sağlar. Ancak, yolculuk başlamadan önce dikkate alınması gereken birkaç öğe var.

## Önkoşullar

OpenGL bir grafik API olduğundan ve kendi başına bir platform olmadığından, çalışması için bir programlama dili gerektirir ve tercih edilen dil C++'dır. Bu nedenle bu dersler için C++ programlama dilinin iyi bilinmesi gerekir. Bununla birlikte, kullanılan ileri düzey C++ konuları da dahil olmak üzere, kavramların çoğunu açıklamaya çalışacağım. Bu yüzden C++ 'da bir uzman olmanız gerekmiyor, ancak bir "Merhaba Dünya" programından daha fazlasını yazabilmelisiniz. C++ ile ilgili fazla tecrübeniz yoksa,  www.learncpp.com adresindeki dersleri önerebilirim.

Ayrıca, bir miktar matematiği (doğrusal cebir, geometri ve trigonometri) kullanacağız ve gerekli olan matematiğin gerekli tüm kavramlarını açıklamaya çalışacağım.Ancak, bir matematikçi değilim ve bu yüzden açıklamalarımın anlaşılması kolay olsa da, büyük olasılıkla eksik kalacaktır. Bu nedenle gerektiğinde materyali daha eksiksiz bir şekilde açıklayan iyi kaynaklara işaretçiler sunacağım. OpenGL'e yolculuğunuza başlamadan önce gerekli olan matematiksel bilgilerden korkmayın.Hemen hemen tüm kavramlar temel bir matematiksel arka planla anlaşılabilir ve matematiği mümkün olan en düşük seviyede tutmaya çalışacağım. İşlevselliklerin çoğu, nasıl kullanılacağını bildiğiniz sürece tüm matematiği anlamanızı gerektirmez.

## Yapı

LearnOpenGL bir dizi genel konuya bölünmüştür.Her konu, her biri farklı kavramları ayrıntılı olarak açıklayan birkaç bölüm içerir. Konuların her biri soldaki menüde bulunabilir. Konular, arka plandaki teori ve pratik yönlerinin açıklandığı doğrusal bir şekilde(bu nedenle, aksi belirtilmedikçe, yukarıdan aşağıya doğru başlatılması önerilir) öğretilir.

Eğitsellerin takibini kolaylaştırmak ve onlara bir yapı kazandırmak için  kutular, kod blokları, renk ipuçları ve işlev referanslarından yararlanılmıştır.


### Kutular
>Yeşil kutular, OpenGL veya eldeki konu hakkında bazı notları veya faydalı özellikler / ipuçları içerir.

>Kırmızı kutular, dikkatli olmanız gereken uyarıları veya diğer özellikleri içerecektir.

### Kod

Aşağıda gördüğünüz gibi, sözdizimi ile vurgulanmış kod içeren gri kutularda yer alan web sitesinde bol miktarda küçük kod parçaları bulacaksınız:

```cpp
// This box contains code    
```
Bunlar yalnızca kod parçacıklarını sağladığından, gerektiğinde belirli bir konu için gereken tüm kaynak koduna bir link vereceğim.

### Renk İpuçları

Bazı kelimeler, bu kelimelerin özel bir anlam ifade ettiğini açıklığa kavuşturmak için farklı renklerle görüntülenir:

Tanım:  Yeşil kelimeler bir tanımı yani, daha sık duyabileceğiniz bir şeyin önemli bir yönünü / adını belirtir.
Program Mantığı: kırmızı kelimeler, işlev isimlerini veya sınıf isimlerini belirtir.
Değişkenler: mavi kelimeler tüm OpenGL sabitlerini içeren değişkenleri belirtir.

#### OpenGL İşlev Referansları

LearnOpenGL'nin bilhassa takdir edilen bir özelliği, OpenGL'in fonksiyonlarının çoğunu, içeriğinde gösterdikleri yerde gözden geçirme yeteneğidir. Web sitesinde belgelenen içerikte bir işlev bulunduğunda, işlev biraz belirgin bir alt çizgi ile gösterilir. Fareyi fonksiyonun üzerine getirebilir ve sonra açılır pencere, fonksiyonun gerçekte ne yaptığına dair güzel bir genel bakış içeren ilgili bilgileri gösterecektir. Görmek için, farenizi hareket halindeyken  glEnable üzerine getirin.

Artık sitenin yapısı hakkında biraz fikir sahibi olduğunuza göre, [Başlarken](https://cg-translators.github.io/learnopengl-tr/getting_started/opengl.html) bölümüne geçebilirsiniz.




