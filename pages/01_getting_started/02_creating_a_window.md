---
title: Pencere Oluşturma
keywords: 
last_updated: 
tags: []
permalink: /getting_started/creating_a_window.html
sidebar: main_sidebar
---

# Bir Pencere Oluşturma

Çarpıcı grafikler oluşturmak için yapmamız gereken ilk şey, bir OpenGL bağlamı ve onu çizmek için bir uygulama penceresi oluşturmaktır. Ancak, bu işlemler her bir işletim sistemi için özeldir ve OpenGL kasıtlı olarak bu işlemlerden soyutlanmaya çalışır. Bunun anlamı bir pencere oluşturmamız, bir bağlam tanımlamamız ve kullanıcı girdisi ile kendimizin ilgilenmesi gerektiğidir.

Neyse ki, aradığımız işlevselliği sağlayan epeyce kütüphane var ve bazıları özellikle OpenGL'i hedefliyor. Bu kütüphaneler, işletim sistemine özgü tüm çalışmalarla ilgileniyor ve bize oluşturulacak bir pencere ve bir OpenGL bağlamı veriyor. Bu popüler kütüphanelerden bazıları GLUT, SDL, SFML ve GLFW'dir. Bizim eğitsellerimiz için GLFW kullanacağız.

## GLFW

GLFW, özellikle ekrana görüntülenmesi için gereken yalın gereklilikleri sağlayan ve OpenGL'i hedef alan C ile yazılmış bir kütüphanedir. Bir OpenGL bağlamı oluşturmamıza, pencere parametrelerini tanımlamamıza ve ihtiyaç duyduğumuz kullanıcı girdisini kullanmamıza olanak tanır.

<img src="https://learnopengl.com/img/getting-started/glfw.png">

Bunun ve bir sonraki eğitselin odağı, bir OpenGL bağlamı oluşturduğundan ve  bir pencere gösterdiğinden emin olarak GLFW'yi çalıştırmaktır. Eğitsel, GLFW kütüphanesini alma, inşa etme (ing building) ve  bağlama (ing. linking) için adım adım bir yaklaşım izleyecektir. Bu ders için Microsoft Visual Studio 2015 IDE'yi kullanacağız (işlemin daha yeni Visual Studio sürümlerinde aynı olduğunu unutmayın). Visual Studio'yu (ya da daha eski bir sürümü) kullanmıyorsanız endişelenmeyin, işlem diğer çoğu IDE'de benzer olacaktır.

### Building GLFW

GLFW [web sayfasından](https://www.glfw.org/download.html) indirilebilir. GLFW zaten Visual Studio 2013/2015 için önceden derlenmiş binary ve başlık dosyalarına sahiptir, ancak eksiksiz olması için GLFW'yi kaynak kodundan kendimiz derleyeceğiz. Öyleyse Kaynak paketini indirelim.

> Önceden derlenmiş binary dosyaları kullanıyorsanız (tam olarak ne yaptığınızı bilmiyorsanız) 64-bit sürümlerini değil de 32-bit sürümlerini indirdiğinizden emin olun. 64-bit sürümlerin garip hatalara neden olduğu çoğu okuyucu tarafından bildiriliyor.


Kaynak paketi indirdikten sonra, çıkartın ve içeriğini açın. Sadece birkaç ürünle ilgileniyoruz:


* Derleme sonucu ortaya çıkan kütüphane.
* **include** klasörü.

Kütüphaneyi kaynak kodundan derlemek, elde edilen kütüphanenin CPU/işletim sisteminize mükemmel şekilde uyarlandığını garanti eder, önceden derlenmiş binary dosyalar her zaman uyum sağlamaz (bazen önceden derlenmiş binary dosyalar sisteminizde kullanılamaz).Bununla birlikte, açık kaynak dünyasında kaynak kodu sağlamadaki sorun, uygulama geliştirmek için herkesin aynı IDE'yi kullanmamasıdır. Bu, sağlanan proje/çözüm dosyalarının diğer kişilerin IDE'leriyle uyumlu olmayabileceği anlamına gelir. Bu yüzden insanlar daha sonra  .c/.cpp ve .h/.hpp dosyalarıyla kendi projelerini/çözümlerini oluşturmalılar. Bu nedenlerden dolayı CMake adında bir araç var.

## CMake

CMake, kullanıcının seçtiği proje/ çözüm dosyalarını (örneğin, Visual Studio, Code::Blocks, Eclipse) önceden tanımlanmış CMake kodları kullanarak kaynak kod dosyaları koleksiyonundan üretebilen bir araçtır. Bu, GLFW'nin kütüphanesini derlemek için kullanabileceğimiz kaynak paketinden bir Visual Studio 2012 proje dosyası oluşturmamızı sağlar. Öncelikle, [indirme](https://cmake.org/download/) sayfalarında bulunan CMake'i indirip yüklememiz gerekir. Win32 yükleyicyi kullandım.

CMake kurulduktan sonra onu komut satırından veya GUI'den çalıştırmayı seçebilirsiniz. İşleri karmaşıklaştırmaya çalışmadığımızdan GUI'yi kullanacağız. CMake, binary dosyalar için bir kaynak kod klasörüne ve bir hedef klasöre ihtiyaç duyar. İndirilen GLFW kaynak paketinin kök klasörünü, kaynak kod klasörü olarak seçeceğiz ve ve build klasörü için yeni bir build dizini yaratıyoruz ve sonra bu dizini seçiyoruz.

<img src="https://learnopengl.com/img/getting-started/cmake.png">

Kaynak ve hedef klasörler ayarlandıktan sonra, CMake'in gerekli ayarları ve kaynak kodunu okuyabilmesi için Configure (tr. Yapılandır) düğmesine tıklayın. Daha sonra proje için üreteç seçmeliyiz ve Visual Studio 2015'i kullandığımızdan Visual Studio 14 seçeneğini seçeceğiz (Visual Studio 2015 ayrıca Visual Studio 14 olarak da bilinir). CMake daha sonra ortaya çıkan kütüphaneyi yapılandırmak için olası seçeneklerini görüntüler. Bunları varsayılan değerlerine bırakıp ayarları saklamak için tekrar Configure'i tıklayabiliriz. Ayarlar yapıldıktan sonra Generate (tr. Üret) seçeneğine tıklarız ve ortaya çıkan proje dosyaları build klasörünüzde oluşturulur.
 
## Derleme

Derleme klasöründe GLFW.sln isimli bir dosya bulunacak ve onu Visual Studio 2015 ile açıyoruz.CMake, zaten uygun yapılandırma ayarlarını içeren bir proje dosyası oluşturduğundan, Build Solution düğmesine basabiliriz ve elde edilen derlenmiş kütüphane, glfw3.lib adlı src/Debug dizininde bulunabilir (Not: sürüm 3'ü kullanıyoruz).

Kütüphane oluşturulduktan sonra, IDE'nin kütüphaneyi ve include dosyalarını nerede bulacağını bildiğinden emin olmalıyız. Bunu yaparken iki yaklaşım vardır:

1. IDE'nin /lib ve /include klasörlerini buluruz ve GLFW'nin include klasörünün içeriğini IDE'nin /include klasörüne ekleriz ve benzer şekilde IDE'nin /lib klasörüne glfw3.lib'i ekleriz. Bu işe yarar, ancak önerilen yaklaşım bu değildir.library/include dosyalarınızı takip etmek zordur ve IDE'nizin yeniden kurulumu dosyaların kaybına neden olacaktır.

2. Önerilen yaklaşım, seçtiğiniz bir yerde, IDE ya da derleyicinizi kullanarak başvurabileceğiniz üçüncü parti tüm başlık dosyalarını ve kütüphanelerini içeren yeni bir dizin seti oluşturmaktır. Kişisel olarak, OpenGL projeleri için sırasıyla tüm kütüphane ve başlık dosyalarımı sakladığım bir **Libs** ve **Include** klasörü içeren tek bir klasör kullanıyorum. Artık tüm üçüncü parti kütüphanelerim tek bir yerde (birden fazla bilgisayar arasında paylaşılabilecek şekilde) düzenleniyor.Bununla birlikte, yeni bir proje oluşturduğumuzda, IDE'ye bu dizinleri nerede bulacağını söylememiz gerekir.

Gereken dosyaları seçtiğiniz bir dizinde saklandıktan sonra, ilk OpenGL projemiz GLFW!

# İlk Projemiz

İlk önce Visual Studio'yu açalım ve yeni bir proje oluşturalım. Birden fazla seçenek verilirse Visual C++'ı ve ardından Empty Project'i seçin (projenize uygun bir ad vermeyi unutmayın). Artık ilk OpenGL uygulamamızı oluşturmak için bir çalışma alanımız var!

## Bağlama (ing. Linking)

Projenin GLFW'yi kullanması için kütüphaneyi projemizle bağlamamız gerekir. Bu, linker ayarlarında glfw3.lib kullanmak istediğimizi belirterek yapılabilir, ancak projemizde üçüncü parti kütüphaneleri farklı dizinlere yapıştırdığımızdan beri glfw3.lib'i nerede bulunacağı bilinmiyor. Bu yüzden önce bu dizinleri projeye eklememiz gerekiyor.

Proje özelliklerine gidip (Solution Explorer'da proje adına sağ tıklayın) bu dizinleri ekleyebilir ve ardından aşağıdaki resimde görüldüğü gibi VC++ dizinlerine gidebiliriz: 

<img src="https://learnopengl.com/img/getting-started/vc_directories.png">


O andan itibaren, projenin nerede arama yapılacağını bildirmek için kendi dizinlerinizi ekleyebilirsiniz. Bu, metne elle ekleme yaparak veya uygun konum dizesini tıklayarak ve **Include Directories** vakası için aşağıdaki resmi göreceğiniz <Edit ..> seçeneğini belirleyerek yapılabilir:

<img src="https://learnopengl.com/img/getting-started/include_directories.png">


Burada istediğiniz kadar fazladan dizin ekleyebilirsiniz ve IDE header dosyalarını ararken bu dizinleri de arayacaktır. Böylece GLFW'nin "include" klasörü dahil edilince GLFW için tüm header dosyalarını <GLFW/..> şeklinde bulabileceksiniz. Aynısı diğer kütüphane dizinleri için de geçerlidir.


VS artık gerekli tüm dosyaları bulabildiği için, Linker sekmesine gidip giriş seçerek GLFW'yi projeye bağlayabiliriz:

<img src="https://learnopengl.com/img/getting-started/linker_input.png">


Daha sonra bir kütüphaneye link vermek için kütüphanenin adını linke belirtmeniz gerekir. Kütüphane adı glfw3.lib olduğu için, Ek Bağımlılıklar alanına (manuel olarak ya da <Düzenle ..> seçeneğini kullanarak) ekliyoruz ve derlediğimizde GLFW'deki o noktadan bağlantı sağlanacak. GLFW dışında, OpenGL kütüphanesine bir link girişi de eklemelisiniz, ancak bu işletim sistemine göre farklılık gösterebilir:

Windows'ta OpenGL Kütüphanesi

Windows kullanıyorsanız, OpenGL kütüphanesi opengl32.lib, Visual Studio'yu yüklediğinizde varsayılan olarak yüklenen Microsoft SDK ile birlikte gelir. Bu eğitimde VS derleyicisini kullandığından ve Windows üzerinde bulunduğundan, Linker ayarlarına opengl32.lib ekliyoruz.

Linux'da OpenGL Kütüphanesi

Linux sistemlerde libGL.so kütüphanesini linker ayarlarına -lGL ekleyerek bağlamanız gerekir. Eğer kütüphaneyi bulamıyorsanız muhtemelen Mesa, NVidia ya da AMD paketlerinden birini yüklemeniz gerekiyor. Ancak platforma özgü bir işlem olduğu için detaylara dalmayacağım. (ek olarak, Linux uzmanı değilim)

Ardından, hem GLFW hem de OpenGL kütüphanesini linker ayarlarına ekledikten sonra, GLFW header dosyalarını aşağıdaki gibi ekleyebilirsiniz:

```cpp
#include <GLFW\glfw3.h>
```


> GCC ile derleme yapan Linux kullanıcıları için aşağıdaki komut satırı seçenekleri -lglfw3 -lGL -lX11 -lpthread -lXrandr -lXi -ldl projesini derlemenize yardımcı olabilir. Karşılık gelen kütüphaneleri doğru bir şekilde ilişkilendirmemek birçok tanımsız referans hatası oluşturacaktır.

Böylece GLFW'nin kurulumunu ve konfigürasyonunu sonlandırırız.

## GLAD

Hala tam olarak sonuna varablmiş değiliz, çünkü yapmamız gereken bir şey daha var. OpenGL bir standart/spesifikasyon olduğu için, belirli grafik kartının desteklediği bir sürücüye teknik özellikleri uygulamak sürücü üreticisine kalmıştır. OpenGL sürücülerinin birçok farklı sürümü bulunduğundan, işlevlerinin çoğunun konumu derleme zamanında bilinmemektedir ve çalışma zamanında sorgulanması gerekir. Ardından, geliştiricinin, ihtiyaç duyduğu işlevlerin yerlerini almak ve daha sonra kullanmak üzere işlev işaretçilerinde (ing. funtion pointers) saklamak görevidir. Bu konumların alınması işletim sistemine özgüdür ve Windows'ta şöyle görünür:

```cpp
// işlev prototipini tanımla
typedef void (*GL_GENBUFFERS) (GLsizei, GLuint*);
// işlevi bul ve bir işlev işaretçisine ata
GL_GENBUFFERS glGenBuffers  = (GL_GENBUFFERS)wglGetProcAddress("glGenBuffers");
// işlev şimdi normal olarak adlandırılabilir
unsigned int buffer;
glGenBuffers(1, &buffer);
```
Gördüğünüz gibi, kod karmaşık görünüyor ve henüz deklare edilmemiş, ihtiyaç duyabileceğiniz her işlev için bunu yapmak zahmetli bir işlem. Neyse ki, GLAD'ın popüler ve güncel bir kütüphane olduğu bu amaç için kütüphaneler var.

### GLAD'ın Ayarlanması

GLAD, konuştuğumuz tüm bu hantal işleri yöneten açık kaynaklı bir kütüphanedir. GLAD, yaygın açık kaynaklı kütüphanelerden biraz farklı bir yapılandırma kurulumuna sahiptir. GLAD, OpenGL'in kullanacağımız sürümü için ilgili tüm OpenGL işlevlerini bu sürüme göre tanımlamak ve yüklemek istediğimizi söyleyebileceğimiz bir web hizmeti kullanır.

Web hizmetine gidin, dilin C++ seçili olduğundan ve API bölümünde en azından 3.3 sürümünü (eğitimler boyunca bu sürümü kullanacağız, üst sürümler de olabilir) seçtiğinizden emin olun. Profil seçeneğini Core olarak seçtiğinizden ve "Generate a loader" seçeneğinin işaretli olduğundan da emin olun. (Şimdilik) Eklentileri yoksayın ve kütüphane dosyalarını üretmek için "Generate" butonuna basın.

GLAD, size içinde iki include klasörü ve bir glad.c dosyası olan bir zip dosyası sağlayacaktır. İki include klasörünü (glad ve KHR) de include dizininize kopyalayın (ya da bu klasörleri işaret eden ekstra bir eleman ekleyin) ve glad.c dosyasını projenize ekleyin.

Yukarıdaki adımlardan sonra dosyanızın üzerine aşağıdaki include yönergesini ekleyebilmeniz gerekir:

```cpp
#include <glad/glad.h>
```

Derleme düğmesine basmak size herhangi bir hata vermemelidir, bu noktada bir OpenGL içeriğini yapılandırmak ve bir pencere oluşturmak için GLFW ve GLAD'i nasıl kullanabileceğimizi tartışacağımız bir sonraki eğitime geçeceğiz. Tüm include ve kütüphane dizinlerinizin doğru olduğundan ve linker ayarlarındaki kitaplık adlarının ilgili kitaplıklarla eşleştiğinden emin olun. Hala takılmışsanız, yorumları inceleyin,ek kaynaklardan herhangi birini kontrol edin veya aşağıdan sorunuzu sorun.


### Ek Kaynaklar
* [GLFW: Pencere Rehberi](https://www.glfw.org/docs/latest/window_guide.html): Bir GLFW penceresini oluşturmak ve yapılandırmak üzerine resmi GLFW rehberi.
* [Uygulama derlemek](http://www.opengl-tutorial.org/miscellaneous/building-your-own-c-application/): uygulamanızın derleme/linkleme süreci ve ortaya çıkabilecek olası hataların geniş bir listesi (çözümleriyle) hakkında önemli bilgiler sağlar.
* [Code::Blocks ile GLFW](http://wiki.codeblocks.org/index.php?title=Using_GLFW_with_Code::Blocks): Code::Blocks IDE üzerinde GLFW derlemek.
* [CMake ile çalışmak](https://cmake.org/runningcmake/): CMake'in hem Windows hem de Linux'ta nasıl çalıştırılacağına kısa bir bakış.
* [Linux üzerinde bir derleme sistemi yazmak](https://learnopengl.com/demo/autotools_tutorial.txt): Wouter Verholst'tan Linux'ta bir derleme sisteminin nasıl yazılacağı üzerine bir autotools eğitimi, özellikle bu eğitimleri hedefliyor.
* [Polytonic/Glitter](https://github.com/Polytonic/Glitter): a simple boilerplate project that comes pre-configured with all relevant libraries; great for if you want a sample project for the LearnOpenGL tutorials without the hassle of having to compile all the libraries yourself.

tüm ilgili kütüphanelerle önceden yapılandırılmış olarak gelen basit bir iskelet projesi; tüm kütüphaneleri kendiniz derlemek zorunda kalmadan LearnOpenGL eğitimleri için örnek bir proje istiyorsanız harika.

Çeviri: [Nezihe Sözen](https://github.com/NeziheSozen), [Barış Çelik](https://github.com/bariscelik)
