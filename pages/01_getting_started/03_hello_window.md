---
title: Merhaba Pencere
keywords: 
last_updated: 
tags: []
permalink: /getting_started/hello_window.html
sidebar: main_sidebar
---

## Merhaba Pencere

GLFW'yi kurup çalıştırabilir miyiz bir bakalım. İlk önce bir .cpp dosyası oluşturun ve aşağıdakileri yeni oluşturulan dosyanızın üstüne ekleyin.

```cpp
#include <glad/glad.h>
#include <GLFW/glfw3.h>
````
> GLFW'den önce GLAD'ı eklediğinizden emin olun. GLAD için include dosyası, doğru OpenGL başlıklarını içerir (GL / gl.h gibi), böylece OpenGL gerektiren diğer başlık dosyalarından önce GLAD dahil etmek bir ipucudur.

Daha sonra, GLFW penceresini başlatacağımız main işlevini oluşturuyoruz:

```cpp

int main()
{
    glfwInit();
    glfwWindowHint(GLFW_CONTEXT_VERSION_MAJOR, 3);
    glfwWindowHint(GLFW_CONTEXT_VERSION_MINOR, 3);
    glfwWindowHint(GLFW_OPENGL_PROFILE, GLFW_OPENGL_CORE_PROFILE);
    //glfwWindowHint(GLFW_OPENGL_FORWARD_COMPAT, GL_TRUE);
  
    return 0;
}
```

main fonksiyonunda önce glfwInit() ile GLFW'yi başlatıyoruz, sonra glfwWindowHint() kullanarak GLFW'yi yapılandırabiliriz. glfwWindowHint'in ilk argümanı ile, bize GLFW_ ile önceden belirlenmiş seçenekler içerisinden hangisini yapılandıracağımızı belirtiriz. İkinci argüman, seçeneğimizin değerini belirleyen bir tam sayıdır. Tüm olası seçeneklerin ve karşılık gelen değerlerin bir listesi GLFW'nin pencere işleme belgelerinde bulunabilir. Uygulamayı bu hâliyle çalıştırmayı denerseniz ve çok fazla tanımlanmamış referans hatası verirse, GLFW kütüphanesini başarıyla bağlayamadığınız anlamına gelir. 

Bu eğitsellerin odağında OpenGL 3.3 olduğundan, GLFW'ye kullanacağımız sürümün 3.3 olduğunu belirtmek istiyoruz. Bu sayede GLFW, OpenGL bağlamını oluştururken gerekli düzenlemeleri yapabilecektir. Bu, bir kullanıcı uygun OpenGL sürümüne sahip olmadığında GLFW'nin hata vermesini sağlar. Hem küçük(ing. minor) hem de büyük(ing. major) sürümü 3'e ayarladık. Ayrıca GLFW'ye core-profile kullanmak istdiğimizi açıkça belirttik. GLFW'ye açıkça core-profile kullanmak istediğimizi söylemek, daha küçük bir OpenGL özelliği alt kümesine erişebileceğimiz anlamına gelir. MacOS X'te başlatma kodunuza çalışması için glfwWindowHint(GLFW_OPENGL_FORWARD_COMPAT,GL_TRUE) eklemeniz gerektiğini unutmayın.

> Sisteminizde/donanımınızda OpenGL sürüm 3.3 veya üstü yüklü olduğundan emin olun, aksi takdirde uygulama çökebilir veya tanımsız davranış gösterebilir. Makinenizde OpenGL sürümünü bulmak için Linux'ta glxinfo'yu çağırın veya Windows için OpenGL Extension Viewer gibi bir yardımcı program kullanın. Desteklenen sürümünüz daha düşükse, ekran kartınızın OpenGL 3.3+ ürününü destekleyip desteklemediğini kontrol edin veya sürücülerinizi güncelleyin.

Simdi bir pencere nesnesi oluşturmamız gerekiyor. Bu pencere nesnesi tüm pencere verilerini tutar ve GLFW'nin diğer işlevleri tarafından oldukça sık kullanılır.

```cpp
GLFWwindow* window = glfwCreateWindow(800, 600, "LearnOpenGL", NULL, NULL);
if (window == NULL)
{
    std::cout << "Failed to create GLFW window" << std::endl;
    glfwTerminate();
    return -1;
}
glfwMakeContextCurrent(window);
```
***glfwCreateWindow()*** işlevinin ilk iki argümanı sırasıyla pencere genişliği ve yüksekliğidir. Üçüncü argüman, pencere için bir isim oluşturmamızı sağlar. Şimdilik "LearnOpenGL" diyoruz, ancak istediğiniz şekilde adlandırmanıza izin veriliyor. Son 2 parametreyi görmezden gelebiliriz. İşlev, daha sonra diğer GLFW işlemlerinde ihtiyaç duyacağımız bir ##GLFWwindow## nesnesi döndürür. glfwMakeContextCurrent(window) ile GLFW'ye penceremizin bağlamını, mevcut konudaki ana bağlam haline getirmesini söyleriz.


## GLAD
Önceki eğitselde, GLAD'ın OpenGL için işlev işaretçilerini (ing. function pointers) yönettiğinden bahsettik, bu yüzden herhangi bir OpenGL işlevini çağırmadan önce GLAD'ı başlatmak istiyoruz:

```cpp
if (!gladLoadGLLoader((GLADloadproc)glfwGetProcAddress))
{
    std::cout << "Failed to initialize GLAD" << std::endl;
    return -1;
}
```
İşletim sistemine özel olan OpenGL fonksiyon işaretçilerinin adreslerini yüklemek için GLAD kullandık. GLFW, hangi işletim sistemi için derlediğimize bağlı olarak doğru işlevi tanımlayan glfwGetProcAdress'i verir.

## Görüş Alanı (ing. Viewport)
Sahnelemeye başlamadan önce son bir şey yapmalıyız. OpenGL’e görüntü oluşturma penceresinin boyutunu söylemeliyiz, böylece OpenGL pencereye göre verileri ve koordinatları nasıl göstermek istediğimizi bilir. Bu boyutları glViewport işlevi ile ayarlayabiliriz:

```cpp
glViewport(0, 0, 800, 600);
```

glViewport'un ilk iki parametresi, pencerenin sol alt köşesinin konumunu belirler. Üçüncü ve dördüncü parametre, sahneleme penceresinin genişliğini ve yüksekliğini GLFW'nin pencere boyutuna eşit olarak belirlediğimiz piksel cinsinden ayarlar.

Aslında görüş alanı boyutlarını GLFW'nin boyutlarından daha küçük değerlere ayarlayabiliriz. O zaman tüm OpenGL sahneleme işlemleri daha küçük bir pencerede görüntülenecekti ve örneğin OpenGL görüş alanı dışındaki diğer öğeleri görüntüleyebilirdik.

> Sahne arkasında OpenGL, 2-boyutlu koordinatları ekran koordinatlarına çevirmek için glViewport ile belirtilen verileri kullanmaktadır. Örneğin, işlenen bir konum (-0.5,0.5) iken ekran koordinatlarında (200,450) olarak eşleştirilir. OpenGL'de işlenen koordinatların -1 ile 1 arasında olduğunu ve bu nedenle (-1 ila 1)'in  (0, 800) ve (0, 600) aralığını etkin bir şekilde eşlendiğini unutmayın.

Ancak, bir kullanıcının pencereyi yeniden boyutlandırdığı an, görüş alanı da ayarlanmalıdır. Pencerenin her boyutlandırılışında çağrılan pencereye bir geri çağırım işlevi kaydedebiliriz. Bu yeniden boyutlandırma geri çağırım işlevi aşağıdaki prototiplere sahiptir:

```cpp
void framebuffer_size_callback(GLFWwindow* window, int width, int height); 
```

framebuffer_size işlevi, ilk parametre olarak GLFWwindow'u alır ve devamında yeni bir pencere boyutlarını gösteren iki adet tamsayı değer bulunmaktadır.Pencere her boyut değiştirdiğinde, GLFW bu işlevi çağırır ve sahneleme için uygun argümanları doldurur.

```cpp
void framebuffer_size_callback(GLFWwindow* window, int width, int height)
{
    glViewport(0, 0, width, height);
} 
```
GLFW'ye bu işlevi her pencere değiştirdğimizde tetiklemesi için kayıt ettirmemiz gerekmektedir:
```cpp
glfwSetFramebufferSizeCallback(window, framebuffer_size_callback); 
```
Pencere ilk görüntülendiğinde framebuffer_size_callback, elde edilen pencere boyutları ile birlikte çağrılır. Retina ekranlar için genişlik ve yükseklik orijinal giriş değerlerinden önemli ölçüde daha yüksek olacaktır.

Kendi işlevlerimizi kaydetmek için ayarlayabileceğimiz birçok geri çağırım işlevi mevcuttur. Örneğin, joystick giriş değişikliklerini, hata mesajlarını vb. işlemek için bir geri çağırım işlevi yapabiliriz. Pencereyi oluşturduktan sonra ve oyun döngüsü başlatılmadan önce geri çağırım işlevlerini kaydederiz.

## Motorlarınızı Hazırlayın

Uygulamanın sadece tek bir çizim yapmasını ve hemen ardından çıkış yapılmasını istemeyiz. Programın durmasını isteyene kadar çizim yapmasını ve kullanıcı girdilerini kullanmasını isteriz. Bu nedenle GLFW'ye durmasını söyleyene kadar devam eden sahneleme döngüsü (ing. render loop) olarak adlandırdığımız bir döngü oluşturmamız gerekiyor. Aşağıdaki kod çok basit bir sahneleme döngüsünü göstermektedir:
```cpp
while(!glfwWindowShouldClose(window))
{
    glfwSwapBuffers(window);
    glfwPollEvents();    
}
```
***glfwWindowShouldClose*** işlevi, GLFW'nin kapatılma talimatı verilip verilmediği taliamtını her döngü itersyonu başlangıcında kontrol eder. Eğer verildiyse true döndürür ve oyun döngüsü çalışması durur. ***glfwPollEvents*** işlevi, herhangi bir olayın tetiklenip tetiklenmediğini (klavye girişi veya fare hareketi olayları gibi) kontrol eder, pencere durumunu günceller ve karşılık gelen işlevleri çağırır. ***glfwSwapBuffers*** bu yineleme sırasında çizim için kullanılmış olan renk tamponunu değiştirir ve ekrana çıktı olarak gösterir.

> Çift Tampon (ing. Double Buffer) 
> Bir uygulama tek bir arabellek ile çizildiğinde, ortaya çıkan görüntü titrek olabilir. Bunun nedeni, elde edilen çıktı görüntüsünün anında çizilmemesi, ve piksel olarak genellikle soldan sağa ve yukarıdan aşağıya çekilmesidir. Bu görüntü, hala oluşturulurken kullanıcıya bir anda gösterilmediğinden, sonuç yapaylık içerebilir. Bu sorunları aşmak için, pencereleme uygulamaları sahneleme için çift tampon uygular. Ön tampon, ekranda gösterilen son çıktı görüntüsünü içerirken, tüm sahneleme komutları arka tampona çizilir. Tüm sahneleme komutları biter bitmez, arka tamponu ön tampona değiştirir, böylece görüntü kullanıcıya anında gösterilir ve yukarıda bahsedilen tüm yapaylığı siler.

## Son bir şey
Sahneleme döngüsünden çıkar çıkmaz, GLFW'nin tahsis edilen tüm kaynaklarını uygun şekilde temizlemek/silmek istiyoruz. Bunu ana işlevin sonunda çağırdığımız ***glfwTerminate*** işlevi ile yapabiliriz.

```cpp
glfwTerminate();
return 0;
```

Bu, tüm kaynakları temizler ve uygulamadan uygun şekilde çıkar. Şimdi uygulamanızı derlemeye çalışın ve her şey yolunda giderse aşağıdaki çıktıyı göreceksiniz:

<img src="https://learnopengl.com/img/getting-started/hellowindow.png">

Çok sıkıcı siyah bir görüntü varsa, doğru olanı yaptınız! Doğru görüntüyü elde edemediyseniz veya her şeyin birbirine nasıl uyduğu konusunda kafanız karıştıysa, [buradan](https://learnopengl.com/code_viewer_gh.php?code=src/1.getting_started/1.1.hello_window/hello_window.cpp) tüm kaynak kodunu kontrol edin.

Uygulamayı derlerken sorun yaşıyorsanız, ilk önce tüm linkleme seçeneklerinin doğru ayarlandığından ve IDE'nize doğru dizinleri doğru bir şekilde eklediğinizden emin olun (önceki derste açıklandığı gibi). Ayrıca kodunuzun doğru olduğundan emin olun; kaynak koduna bakarak kolayca doğrulayabilirsiniz. Hâlâ herhangi bir sorunuz varsa, sorununuzla ilgili bir yorum yazınız; ben ve/veya topluluk size yardım etmeye çalışacağız.

## Girdi

Ayrıca GLFW'de bir tür girdi kontrolü olmasını istiyoruz ve bunu GLFW'nin birçok girdi işleviyle başarabiliriz. Pencereyi bir tuşla birlikte girdi olarak alan GLFW'nin ***glfwGetKey*** işlevini kullanacağız. İşlev, bu tuşa şu anda basılmakta olup olmadığını döndürür. Tüm giriş kodlarını düzenli tutmak için bir *processInput* işlevi yaratıyoruz:
```cpp
void processInput(GLFWwindow *window)
{
    if(glfwGetKey(window, GLFW_KEY_ESCAPE) == GLFW_PRESS)
        glfwSetWindowShouldClose(window, true);
}
```

Burada kullanıcının ESC tuşuna basıp basmadığını kontrol ederiz (basılmazsa, glfwGetKey, GLFW_RELEASE değerini döndürür). Eğer kullanıcı ESC tuşuna basarsa, glfwSetwindowShouldClose kullanarak WindowShouldClose özelliğini true değerine ayarlayarak GLFW'yi kapatırız. Ana döngü sırasında bir sonraki durum kontrolü sonra başarısız olur ve uygulama kapanır.

Daha sonra processInput'u sahneleme döngüsünün her yinelemesinde çağırırız:

```cpp
while (!glfwWindowShouldClose(window))
{
    processInput(window);

    glfwSwapBuffers(window);
    glfwPollEvents();
}
```
Bu bize belirli tuşa basmayı kontrol etmenin ve her kareye göre tepki vermenin kolay bir yolunu sunar.

## Sahneleme

Tüm sahneleme komutlarını sahneleme döngüsüne yerleştirmek istiyoruz, çünkü tüm sahneleme komutlarını döngünün her yinelemesinde çalıştırmak istiyoruz. Bu biraz şuna benzeyecektir:

```cpp
// render loop
while(!glfwWindowShouldClose(window))
{
    // input
    processInput(window);

    // rendering commands here
    ...

    // check and call events and swap the buffers
    glfwPollEvents();
    glfwSwapBuffers(window);
}
```

Sadece işlerin gerçekten işe yarayıp yaramadığını test etmek için ekranı tercih ettiğimiz bir renkle temizlemek istiyoruz. Her sahneleme döngüsünün başında her zaman ekranı temizlemek istiyoruz, aksi halde önceki döngünün sonuçlarını görmeye devam edeceğiz (bu, aradığınız efekt olabilir, ancak genellikle istemezsiniz). Hangi tamponu temizlemek istediğimizi belirtmek için tampon bitlerini ilettiğimiz ***glClear*** işlevini kullanarak ekranın renk arabelleğini temizleyebiliriz. Ayarlayabileceğimiz olası bitler GL_COLOR_BUFFER_BIT, GL_DEPTH_BUFFER_BIT ve GL_STENCIL_BUFFER_BIT'dir. Şu anda sadece renk değerlerini önemsiyoruz, bu nedenle yalnızca renk arabelleğini temizliyoruz.

```cpp
glClearColor(0.2f, 0.3f, 0.3f, 1.0f);
glClear(GL_COLOR_BUFFER_BIT);
```
Ekranı temizlemek için ***glClearColor*** ile renk de ayarladığımızı unutmayın. Ne zaman glClear'ı çağırıp renk arabelleğini temizlersek, tüm renk arabelleği glClearColor tarafından yapılandırılan renkle doldurulur. Bu koyu yeşil-mavimsi bir renkle sonuçlanacaktır.

> OpenGL eğitselinden hatırlayabileceğiniz gibi, glClearColor işlevi durum ayarlama işlevidir ve glClear, temizleme rengini almak için geçerli durumu kullandığı durum kullanan bir işlevdir.

<img src="https://learnopengl.com/img/getting-started/hellowindow2.png">

Uygulamanın tam kaynak kodunu [burada](https://learnopengl.com/code_viewer_gh.php?code=src/1.getting_started/1.2.hello_window_clear/hello_window_clear.cpp) bulabilirsiniz.

Orijinal Kaynak: [Hello Window](https://learnopengl.com/Getting-started/Hello-Window)

Çeviri: [Nezihe Sözen](https://github.com/NeziheSozen)
