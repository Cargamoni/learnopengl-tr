---
title: Ayarlama
keywords: 
last_updated: 
tags: []
permalink: /in_practice/2d_game/setting_up.html
sidebar: main_sidebar
---

Gerçek oyun mekaniğine başlamadan önce, oyunun oturması için basit bir çerçeve oluşturmamız gerekiyor. Oyun, birçoğu önceki derslerde tanıtılan birkaç üçüncü parti kütüphane kullanacak. Yeni bir kütüphane gerektiğinde, düzgün bir şekilde tanıtılacaktır.

İlk olarak, ilgili tüm sahneleme ve oyun kodunu içeren <span style="color:green">uber </span> oyun sınıfı tanımlarız. Böyle bir oyun sınıfının amacı, oyunun tüm kodlarını oyundan ayırırken oyun kodunuzu düzenlemesidir. Bu şekilde, aynı sınıfı fazla çaba sarf etmeden tamamen farklı bir pencere kütüphanesinde (örneğin SDL veya SFML gibi) kullanabilirsiniz.

> Oyun/ grafik kodunu sınıflara ve nesnelere soyutlamaya/ genelleştirmeye çalışmanın binlerce yolu vardır. Bu eğitimde göreceğiniz şey, bu sorunları çözmek için sadece bir yaklaşımdır. Daha iyi bir yaklaşım olduğunu düşünüyorsanız, uygulamada kendi gelişiminizi bulmaya çalışın.

Oyun sınıfımız bir başlatma fonksiyonu, bir güncelleme fonksiyonu, girdileri işleme gonksiyonu ve sahneleme fonksiyonu barındırır:

```cpp
class Game
{
    public:
        // Game state
        GameState  State;	
        GLboolean  Keys[1024];
        GLuint	   Width, Height;
        // Constructor/Destructor
        Game(GLuint width, GLuint height);
        ~Game();
        // Initialize game state (load all shaders/textures/levels)
        void Init();
        // GameLoop
        void ProcessInput(GLfloat dt);
        void Update(GLfloat dt);
        void Render();
};
```

Sınıf, bir oyun sınıfından bekleyebileceğiniz şeyleri barındıtmaktadır. Bir genişlik ve yükseklik vererek oyunu başlatırız (bu oyunu oynamak istediğiniz çözünürlüğe karşılık gelmektedir). Gölgelendiricileri, dokuları yüklemek ve tüm oyun durumunu başlatmak için <span style="color:red">Init </span> fonksiyonunu kullanırız. <span style="color:red">ProcessInput </span>'u çağırarak girişi <span style="color:blue">Keys </span> dizisinde depolandığı gibi işleyebilir ve <span style="color:red">Update </span> fonksiyonundaki tüm oyun olaylarını (oyuncu/ top hareketi gibi) güncelleyebiliriz. Son olarak, oyunu <span style="color:red">Render </span> fonksiyonunu çağırarak sahneleyebiliriz. Hareket mantığını sahneleme mantığından ayırdığımızı unutmayın.

<span style="color:red">Game </span> sınıfı ayrıca aşağıda tanımlandığı gibi <span style="color:green">GameState </span> türünde <span style="color:blue">State </span> adlı bir değişkeni de barındırır:

```cpp
// Represents the current state of the game
enum GameState {
    GAME_ACTIVE,
    GAME_MENU,
    GAME_WIN
};
```
Bu, oyunun şu anda hangi durumda olduğunu takip etmemizi sağlar. Bu şekilde, oyunun mevcut durumuna bağlı olarak farklı öğeleri oluşturmaya ve/veya işlemeye karar verebiliriz (örneğin, oyun menüsü gibi).

Şu andan itibaren, gerçek oyun kodunu henüz yazmadığımızdan, oyun sınıfının işlevleri tamamen boş, ancak burada Oyun sınıfının [başlığı](https://learnopengl.com/code_viewer.php?code=in-practice/breakout/game_setting-up.h) ve [kod](https://learnopengl.com/code_viewer.php?code=in-practice/breakout/game_setting-up) dosyası var.

## Yardımcı
 Since we're creating a large application we'll frequently have to re-use several OpenGL concepts, like textures and shaders. It thus makes sense to create a more easy-to-use interface for these two items as similarly done in one of the earlier tutorials where we created a shader class.

A shader class is defined that generates a compiled shader (or generates error messages if it fails) from two or three strings (if a geometry shader is present). The shader class also contains a lot of useful utility functions to quickly set uniform values. A texture class is also defined that generates a 2D texture image (based on its properties) from a byte array and a given width and height. Again, the texture class also hosts utility functions.

We won't delve into the details of the classes since by now you should easily understand how they work. For this reason you can find the header and code files, fully commented, below:

    Shader: header, code.
    Texture: header, code.

Note that the current texture class is solely designed for 2D textures only, but could easily be extended for alternative texture types. 

## Kaynak Yönetimi

 While the shader and texture classes function great by themselves, they do require either a byte array or several strings to initialize them. We could easily embed file loading code within the classes themselves, but this slightly violates the single responsibility principle in that the classes should only focus on either textures or shaders respectively and not necessarily their file-loading mechanics.

For this reason it is often considered a more organized approach to create a single entity designed for loading game-related resources called a resource manager. There are several approaches to creating a resource manager; for this tutorial we chose to use a singleton static resource manager that is (due to its static nature) always available throughout the project hosting all loaded resources and its relevant loading functionality.

Using a singleton class with static functionality has several advantages and disadvantages with its disadvantages mostly being losing OOP properties and losing control over construction/destruction. However, for relative small projects like these it is easy to work with.

Like the other class files, the resource manager is listed below:

    Resource Manager: header, code.

Using the resource manager we can easily load shaders into the program like: 

```cpp

Shader shader = ResourceManager::LoadShader("vertex.vs", "fragment.vs", nullptr, "test");
// then use it
shader.Use();
// or
ResourceManager::GetShader("test").Use();
```
##Program

The defined Game class together with the resource manager and the easily manageable Shader and Texture2D classes form the basis for the next tutorials as we'll be extensively using these classes to implement the Breakout game. 

 We still need a window for the game and set some initial OpenGL state. We make use of OpenGL's face-culling functionality and of its blending functionality. We do not use depth testing; since the game is entirely in 2D, all vertices are defined with the same z-values, so enabling depth testing would be of no use and likely cause z-fighting.

The startup code of the Breakout game is relatively simple: we create a window with GLFW, register a few callback functions, create the Game object and propagate all relevant functionality to the game class. The code is given below:

    Program: code.

Running the code should give you the following output: 

<img src="https://learnopengl.com/img/in-practice/breakout/setting-up.png">

By now we have a solid framework for the upcoming tutorials; we'll be continuously extending the game class to host new functionality. Hop over to the next tutorial once you're ready. 
