---
title: Breakout
keywords: 
last_updated: 
tags: []
permalink: /in_practice/2d_game/breakout.html
sidebar: main_sidebar
---

# Breakout
Bu dersler boyunca OpenGL'in iç çalışma mekanizması ve bunları süslü grafikler oluşturmak için nasıl kullanabileceğimiz hakkında pek çok şey öğrendik. Ancak, birkaç teknik demo dışında, OpenGL ile gerçekten pratik bir uygulama oluşturmadık. Bu, OpenGL kullanarak nispeten basit 2B bir oyun oluşturma hakkında daha büyük bir eğitselin giriş kısmıdır. Eğitim serisi, OpenGL'i daha büyük, daha karmaşık bir ortamda nasıl kullanabileceğimizi gösterecek. Eğitim dizisinin yeni OpenGL konseptleri sunmadığını, ancak bu konseptleri daha büyük bir bütüne nasıl uygulayabileceğimizi az çok gösterdiğini unutmayın.

İşleri basit tutmayı tercih ettiğimiz için 2B oyunumuzu halihazırda mevcut bir 2B arcade türünde oyuna dayandıracağız. Atari 2600 konsolunda 1976'da piyasaya sürülen klasik bir 2B oyun olan Breakout ile tanışın. Breakout, küçük bir yatay kürek kontrol eden oyuncunun, topun alt sınır çizgisine ulaşmasına izin vermeden her tuğlaya karşı top zıplatarak tüm tuğlaları yok etmesini gerektirir. Oyuncu tüm tuğlaları yok ettikten sonra oyunu tamamlar.

Aşağıda, Breakout'un başlangıçta Atari 2600'de nasıl olduğunu görebiliriz:

<img src="https://learnopengl.com/img/in-practice/breakout/breakout2600.png">

Oyun aşağıdaki mekaniklere sahiptir:
* Küçük bir kürek oyuncu tarafından kontrol edilir ve sadece ekranın sınırları içinde sola veya sağa hareket edebilir.
* Top ekran boyunca hareket eder ve her çarpışma, topun nereye çarptığına bağlı olarak yönünü değiştirmesine neden olur. Bu, ekran sınırları, tuğlalar ve kürek için geçerlidir.
* Top ekranın alt sınırına ulaşırsa duruma göre ya oyun biter ya da bir can kaybedilir.
* Top, bir tuğlaya dokunduğu anda onu yok eder
* Tüm tuğlalar yok edildiğinde oyun kazanılır.
* Topun yönü, topun kürek merkezinden ne kadar uzak olduğu ile ilgilidir.

Top zaman zaman tuğla duvarın üzerindeki sınıra ulaşan küçük bir boşluk bulabileceğinden, üst sınır ile tuğla tabakasının üst kenarı arasında yukarı ve geri sıçramaya devam edecektir. Top sonunda tekrar bir boşluk bulana kadar bunu devam ettirecektir.

## OpenGL Breakout

Bu klasik arcade oyununu, tamamen OpenGL ile uygulayacağımız 2B oyun olarak ele alacağız. Breakout'un bu sürümü, bazı güzel ekstra özelliklerle klasik Breakout oyununu geliştirme yeteneğini sunan grafik kartı üzerinde çalışacak.

Klasik mekaniğin yanı sıra, bizim versiyonumuzda yer alacak özellikler:
* Şimdiye kadar gördüğünüz en iyi grafikler
* Parçacıklar (ing. particles)
* Gelişmiş metin sahneleme
* PowerUp
* Son-işlem efektler
* Çoklu (özelleştirilebilir) seviyeler

Daha fazla heyecanlanmanız için eğitsel serisini bitirdikten sonra oyunun nasıl görüneceğini aşağıda görebilirsiniz:
<img src="https://learnopengl.com/img/in-practice/breakout/cover.png">

Bu eğitseller, daha önceki derslerde tartışıldığı gibi çok sayıda kavramı birleştirecek ve bir bütün olarak birlikte nasıl çalışabileceklerini gösterecektir. Bu nedenle, bu eğitsellere geçmeden önce en azından [Başlarken](https://cg-translators.github.io/learnopengl-tr/getting_started/opengl.html) bölümünü bitirmiş olmanız önemlidir.

Ayrıca, bazı eğitsellerde kavramlar (örneğin Gelişmiş OpenGL bölümünden Framebuffers) gerekli olacaktır. Bu nedenle gerektiğinde bu eğitseller listelenir.

> LearnOpenGL'de son yıllarda birkaç kod geliştirmesine sahip olduğunu, ancak hepsinin 2D Breakout koduna entegre olmadığını unutmayın. Dikkat edilmesi gereken iki nokta vardır:
1. Breakout, GLAD yerine GLEW kullanır. Her ikisi de aynı şeyi yapar, bu yüzden Breakout kodu için GLAD kullanımında hiçbir sorun yoktur.
2. Breakout, matrislerin varsayılan olarak Identity matrisi ile  başlatıldığı eski bir GLM sürümünü kullanır. Daha sonraki sürümlerde bu artık geçerli değildir ve varsayılan olarak sonraki tüm matris işlemlerini null matrisi başlatır. Bunu düzeltmek için glm :: mat4 matrix = glm :: mat4 (1.0f); kullanarak tüm GLM matrislerini başlatın.


Hazır olduğunuzu düşünüyorsanız bir [sonraki](https://cg-translators.github.io/learnopengl-tr/in_practice/2d_game/setting_up.html) eğitsele geçiniz.
