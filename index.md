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

Bu eğitsellerin odağında Modern OpenGL vardır. Learning (and using) modern OpenGL requires a strong knowledge of graphics programming and how OpenGL operates under the hood to really get the best of your experience. So we will start by discussing core graphics aspects, how OpenGL actually draws pixels to your screen and how we can abuse that knowledge to create some funky looking effects.

Aside from the core knowledge we will discuss many useful techniques that you can use for your applications like: traversing a scene, create beautiful lighting, load custom-made objects from a modelling program, do cool post-processing techniques and much more. We also feature a walkthrough tutorial where we actually create a small game based on our obtained OpenGL knowledge, so you will really get a feel of what it's like to actually do graphics programming.

If you want to keep up to date on the site's progress and/or other LearnOpenGL news, please follow me on Twitter.


# Giriş

Buraya geldiğinize göre muhtemelen bilgisayar grafiklerinin iç çalışmasını öğrenmek istiyorsunuz. Bir şeyleri kendiniz yapmanız son derece eğlenceli ve sizi her işin altından kalkmanızı sağlayarak grafik programlamayı harika bir şekilde anlamanızı sağlar. Ancak, yolculuk başlamadan önce dikkate alınması gereken birkaç öğe var.

## Önkoşullar

OpenGL bir grafik API olduğundan ve kendi başına bir platform olmadığından, çalışması için bir programlama dili gerektirir ve tercih edilen dil C++'dır. Bu nedenle bu dersler için C++ programlama dilinin iyi bilinmesi gerekir. Bununla birlikte, kullanılan ileri düzey C++ konuları da dahil olmak üzere, kavramların çoğunu açıklamaya çalışacağım. Bu yüzden C++ 'da bir uzman olmanız gerekmiyor, ancak bir "Merhaba Dünya" programından daha fazlasını yazabilmelisiniz. C++ ile ilgili fazla tecrübeniz yoksa,  www.learncpp.com adresindeki dersleri önerebilirim.

Ayrıca, bir miktar matematiği (doğrusal cebir, geometri ve trigonometri) kullanacağız ve gerekli olan matematiğin gerekli tüm kavramlarını açıklamaya çalışacağım.Ancak, bir matematikçi değilim ve bu yüzden açıklamalarımın anlaşılması kolay olsa da, büyük olasılıkla eksik kalacaktır. Bu nedenle gerektiğinde materyali daha eksiksiz bir şekilde açıklayan iyi kaynaklara işaretçiler sunacağım. OpenGL'e yolculuğunuza başlamadan önce gerekli olan matematiksel bilgilerden korkmayın.Hemen hemen tüm kavramlar temel bir matematiksel arka planla anlaşılabilir ve matematiği mümkün olan en düşük seviyede tutmaya çalışacağım. İşlevselliklerin çoğu, nasıl kullanılacağını bildiğiniz sürece tüm matematiği anlamanızı gerektirmez.

## Yapı

LearnOpenGL is broken down into a number of general subjects. Each subject contains several sections that each explain different concepts in large detail. Each of the subjects can be found at the menu to your left. The subjects are taught in a linear fashion (so it is advised to start from the top to the bottom, unless otherwise instructed) where each page explains the background theory and the practical aspects.

To make the tutorials easier to follow and give them some added structure the site contains boxes, code blocks, color hints and function references.

### Kutular
Green boxes encompasses some notes or useful features/hints about OpenGL or the subject at hand.
Red boxes will contain warnings or other features you have to be extra careful with.

### Kod

You will find plenty of small pieces of code in the website that are located in dark-gray boxes with syntax-highlighted code as you can see below:


// This box contains code    

Since these provide only snippets of code, wherever necessary I will provide a link to the entire source code required for a given subject.
Color hints

Some words are displayed with a different color to make it extra clear these words portray a special meaning:

    Definition: green words specify a definition i.e. an important aspect/name of something you're likely to hear more often.
    Program logic: red words specify function names or class names.
    Variables: blue words specify variables including all OpenGL constants.

#### OpenGL İşlev Referansları

A particularly well appreciated feature of LearnOpenGL is the ability to review most of OpenGL's functions wherever they show up in the content. Whenever a function is found in the content that is documented at the website, the function will show up with a slightly noticeable underline. You can hover the mouse over the function and after a small interval, a pop-up window will show relevant information about this function including a nice overview of what the function actually does. Hover your mouse over glEnable to see it in action.

Now that you got a bit of a feel of the structure of the site, hop over to the Getting Started section to start your journey in OpenGL!




