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

GLAD is an open source library that manages all that cumbersome work we talked about. GLAD has a slightly different configuration setup than most common open source libraries. GLAD uses a web service where we can tell GLAD for which version of OpenGL we'd like to define and load all relevant OpenGL functions according to that version.

GLAD, konuştuğumuz tüm bu hantal işleri yöneten açık kaynaklı bir kütüphanedir. GLAD, yaygın açık kaynaklı kütüphanelerden biraz farklı olarak bir yapılandırma kurulumuna sahiptir. GLAD, OpenGL'in kullanacağımız sürümü için ilgili tüm OpenGL işlevlerini bu sürüme göre tanımlamak ve yüklemek istediğimizi söyleyebileceğimiz bir Web hizmeti kullanır.

Go to the web service, make sure the language is set to C++ and in the API section, select an OpenGL version of at least 3.3 (which is what we'll be using for these tutorials; higher versions are fine as well). Also make sure the profile is set to Core and that the Generate a loader option is ticked. Ignore the extensions (for now) and click Generate to produce the resulting library files.

GLAD by now should have provided you a zip file containing two include folders, and a single glad.c file. Copy both include folders (glad and KHR) into your include(s) directoy (or add an extra item pointing to these folders), and add the glad.c file to your project.

After the previous steps, you should be able to add the following include directive above your file:

```cpp
#include <glad/glad.h>
```
 

Hitting the compile button shouldn't give you any errors, at which point we're set to go for the next tutorial where we'll discuss how we can actually use GLFW and GLAD to configure an OpenGL context and spawn a window. Be sure to check that all your include and library directories are correct and that the library names in the linker settings match with the corresponding libraries. If you're still stuck, check the comments, check any of the additional resources or ask your question below.

### Ek Kaynaklar
* GLFW: Window Guide: official GLFW guide on setting up and configuring a GLFW window.
* Building applications: provides great info about the compilation/linking process of your application and a large list of possible errors (plus solutions) that might come up.
* GLFW with Code::Blocks: building GLFW in Code::Blocks IDE.
* Running CMake: short overview of how to run CMake on both Windows and Linux.
* Writing a build system under Linux: an autotools tutorial by Wouter Verholst on how to write a build system in Linux, specifically targeted for these tutorials.
* Polytonic/Glitter: a simple boilerplate project that comes pre-configured with all relevant libraries; great for if you want a sample project for the LearnOpenGL tutorials without the hassle of having to compile all the libraries yourself.

Çeviri: [Nezihe Sözen](https://github.com/NeziheSozen)
