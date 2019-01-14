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
Eski günlerde OpenGL kullanımı, grafik çizimi için kolay bir yol olan anlık mod (ing. immediate mode) ile geliştirme anlamına geliyordu. OpenGL işlevselliğinin çoğu kütüphanede gizlenmişti ve geliştiricilerin OpenGL'in hesaplamalarına dair fazla özgürlüğü yoktu.





#### Kaynak
https://learnopengl.com/Getting-started/OpenGL
