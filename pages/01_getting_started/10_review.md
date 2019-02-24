---
title: Gözden Geçirme
keywords: 
last_updated: 
tags: []
permalink: /getting_started/review.html
sidebar: main_sidebar
---

**Başlarken** bölümünün sonuna ulaştığınız için tebrikler. Şimdiye kadar bir pencere oluşturabilmeli, gölgelendiriciler oluşturabilmeli ve derleyebilmeli, gölgelendiricinize tampon nesneler veya uniform yoluyla köşe verisi gönderebilmeli, nesneleri çizebilmeli, dokuları kullanabilmeli, vektörleri ve matrisleri anlayabilmeli ve tüm bu bilgileri tam bir 3-boyutlu sahne oluşturmak için birleştirebilmelisiniz.

Eğitsellerle oynamaya çalışın, biraz deneme yapın veya bazı sorunlara ilişkin kendi fikir ve çözümlerinizi bulun. Tartıştığımız tüm materyalleri anladığınızı hissettiğiniz anda bir sonraki derse geçme zamanı.

## Glossary

* **OpenGL**: her işlevin düzenini ve çıktısını tanımlayan  grafik API'sinin resmi bir spesifikasyonu.
* **GLAD**: Tüm OpenGL işlev işaretçilerini yükleyen ve ayarlayan bir eklenti yükleme kütüphanesi, böylece tüm OpenGL işlevlerini kullanabiliyoruz.
* **Viewport**: sahnelediğimiz pencere
* **Grafik İş Hattı**: bir köşe verisinin ekranda bir piksel olarak görüntülenebilmesini sağlayan tüm süreçtir. 
* **Shader**: grafik kartında çalışan küçük bir programdır. Grafik iş hattının birkaç aşamasında, mevcut işlevselliğin yerine kullanıcı yapımı gölgelendiriciler kullanabilir.
* **Vertex**: Tek bir noktayı temsil eden bir veri topluluğudur.
* **Normalize Edilmiş Koordinatlar**: Tepe noktalarının kırpma sonrasında sona erdiği koordinat sistemi ve kırpılmış koordinatlarda perspektif bölünmesi gerçekleştirilir. NDC'de -1.0 ile 1.0 arasındaki hiç bir tepe noktası konumu atılmaz ve görünür hale gelir.
* **Vertex Buffer Object**: bellekte yer ayıran ve kullanılacak tüm grafik verilerini depolayan bir tampon nesnesi.
* **Vertex Array Object**: tampon ve tepe noktası nitelik (vertex attribute) durumu bilgilerini depolar.
* **Element Buffer Object**: Dizine alınmış çizimler için indisleri saklayan bir ara bellek nesnesi.
* **Uniform**: global olan (bir gölgelendirici programındaki her gölgelendirici bu tek biçimli değişkene erişebilir) ve yalnızca bir kez ayarlanması gereken özel bir GLSL değişkeni türü.
* **Texture**:nesnelerin etrafına sarılmış özel bir görüntü
* **Texture Wrapping**: Doku koordinatları aralıkları (0, 1) dışındayken OpenGL'in dokuları nasıl örneklemesi gerektiğini belirleyen modu tanımlar.
* **Texture Filtering**: Seçilebilecek çeşitli dokular (doku pikselleri) olduğunda OpenGL'in dokuyu nasıl örneklemesi gerektiğini belirleyen modu tanımlar. Bu genellikle bir doku büyütüldüğünde meydana gelir.
* **Mipmaps**: izleyiciye olan mesafeye bağlı olarak uygun boyuttaki sürümün seçildiği dokunun daha küçük sürümlerini saklar.
* **stb_image**: imge yükleme kütüphanesi
* **Texture Units**: her biri farklı bir doku birimine birden fazla doku bağlayarak tek bir nesne üzerinde birden fazla dokuya izin verir.
* **Vector**: Herhangi bir boyuttaki yönleri ve/veya konumları tanımlayan matematiksel bir varlık.
* **Matrix**: matematiksel ifadelerin dikdörtgensel dizisi
* **GLM**: OpenGL için uyarlanmış bir matematik kütüphanesi.
* **Local Space**: Bir nesnenin başladığı alan. Bir nesnenin orijinine göre tüm koordinatlar.
* **World Space**: küresel bir orijine göre tüm koordinatlar.
* **View Space**: kamera açısından bakıldığında tüm koordinatlar.
* **Clip Space**: kamera açısından bakıldığında ancak yansıtma uygulanmış tüm koordinatlardır. Bu, köşe koordinatlarının, köşe gölgelendiricisinin çıktısı olarak girmesi gereken alandır. OpenGL gerisini halleder (kırpma /perspektif bölümleme).
* **Screen Space**: Ekrandan bakıldığındaki tüm koordinatlardır. Koordinatlar 0 ile ekran genişliği/yüksekliği arasında değişmektedir.
* **LookAt**: tüm koordinatların, belirli bir pozisyondan kullanıcının belirli bir hedefe bakacağı şekilde döndürüldüğü ve çevrildiği bir koordinat sistemi oluşturan özel bir görünüm matrisi.
* **Euler Açıları**: Yaw, pitch ve roll'dan 3-boyutlu yön vektörü oluşturmamızı sağlar.
