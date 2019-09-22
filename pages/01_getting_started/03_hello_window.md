---
title: Merhaba Pencere
keywords: 
last_updated: 
tags: []
permalink: /getting_started/hello_window.html
sidebar: main_sidebar
---

# Merhaba Pencere

GLFW'yi kurup çalıştırabilir miyiz bir bakalım. İlk önce bir .cpp dosyası oluşturun ve aşağıdakileri yeni oluşturulan dosyanızın üstüne ekleyin.

```cpp
#include <glad/glad.h>
#include <GLFW/glfw3.h>
````
>> GLFW'den önce GLAD'ı eklediğinizden emin olun. GLAD için include dosyası, doğru OpenGL başlıklarını içerir (GL / gl.h gibi), böylece OpenGL gerektiren diğer başlık dosyalarından önce GLAD dahil etmek bir ipucudur.

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

>> Sisteminizde/donanımınızda OpenGL sürüm 3.3 veya üstü yüklü olduğundan emin olun, aksi takdirde uygulama çökebilir veya tanımsız davranış gösterebilir. Makinenizde OpenGL sürümünü bulmak için Linux'ta glxinfo'yu çağırın veya Windows için OpenGL Extension Viewer gibi bir yardımcı program kullanın. Desteklenen sürümünüz daha düşükse, ekran kartınızın OpenGL 3.3+ ürününü destekleyip desteklemediğini kontrol edin veya sürücülerinizi güncelleyin.

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


