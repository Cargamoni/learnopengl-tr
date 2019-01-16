---
title:Başlarken: OpenGL
permalink: /getting_started/opengl.html
sidebar: main_sidebar
---
## OpenGL
Yolculuğumuza başlamadan önce, öncelikle OpenGL'nin gerçekte ne olduğunu tanımlamalıyız. OpenGL temel olarak grafik ve görüntüleri işlemek için kullanabileceğimiz büyük bir fonksiyon seti sağlayan bir API (Uygulama Programlama Arayüzü) olarak kabul edilir. Bununla birlikte, OpenGL kendi başına sadece bir API değildir,  Khronos Group tarafından geliştirilmiş ve sürdürülmüş bir spesifikasyondur.

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






#### Kaynak
https://learnopengl.com/Getting-started/OpenGL
