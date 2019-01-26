---
title: Çerçeve Tamponları
keywords: 
last_updated: 
tags: []
permalink: /advanced_opengl/framebuffers.html
sidebar: main_sidebar
---
# Çerçeve Tamponları

Şimdiye kadar birkaç çeşit ekran tamponu kullandık: Renk değerleri yazmak için bir renk tamponu (ing. collor buffer), derinlik bilgileri yazmak için bir derinlik tamponu(ing. depth buffer) ve nihayet bazı koşullara dayanarak belirli parçaları atmamıza izin veren bir şablon tamponu (ing. stencil buffer). Bu tamponların birleşimine çerçeve tamponu (ing. framebuffer) denir ve bellekte bir yere depolanır. OpenGL bize kendi çerçeve tamponumuzu tanımlama ve böylece kendi rengimizi, isteğe bağlı olarak bir derinlik ve şablon tamponu tanımlama esnekliği sağlar.

Şimdiye kadar yaptığımız sahneleme işlemlerinin tümü, varsayılan çerçeve tamponuna bağlı olan sahneleme tamponlarının (ing. render buffers) üzerinde yapıldı. Varsayılan çerçeve tamponu pencere oluşturulduğunda yaratılır ve yapılandırılır (GLFW bunu bizim için yapmaktadır). Kendi çerçeve tamponumuzu oluşturarak, sahnelemek için ek bir yol bulabiliriz.

Çerçeve tamponu uygulamaları hemen bir anlam ifade etmeyebilir,ancak sahnenizi farklı bir çerçeve tamponuna yansıtmak, bir sahnede aynalar oluşturmamızı ya da örneğin harika son-işlem (ing. post-processing) efektleri yapmamızı sağlar.İlk önce gerçekte nasıl çalıştıklarını tartışacağız ve sonra bu harika son-işlem efektlerini uygulayarak kullanacağız.

## Çevçeve Tamponu Oluşturma

OpenGL'deki diğer nesneler gibi, **glGenFramebuffers** adlı bir işlevi kullanarak bir çerçeve tampon nesnesi (kısaca FBO) oluşturabiliriz:
```cpp
unsigned int fbo;
glGenFramebuffers(1, &fbo);
```
Bu nesne yaratma ve kullanma kalıbı, şimdiye kadar onlarca kez gördüğümüz bir şeydir. Bu yüzden kullanım işlevleri, gördüğümüz diğer tüm nesnelere benzer; ilk önce bir framebuffer nesnesi yaratır, onu aktif framebuffer olarak bağlar, bazı işlemleri yapar ve çerçeveyi çıkarırız. To bind the framebuffer we use glBindFramebuffer: 
```cpp
glBindFramebuffer(GL_FRAMEBUFFER, fbo);
```
 By binding to the GL_FRAMEBUFFER target all the next read and write framebuffer operations will affect the currently bound framebuffer. It is also possible to bind a framebuffer to a read or write target specifically by binding to GL_READ_FRAMEBUFFER or GL_DRAW_FRAMEBUFFER respectively. The framebuffer bound to GL_READ_FRAMEBUFFER is then used for all read operations like glReadPixels and the framebuffer bound to GL_DRAW_FRAMEBUFFER is used as the destination for rendering, clearing and other write operations. Most of the times you won't need to make this distinction though and you generally bind to both with GL_FRAMEBUFFER.

Unfortunately, we can't use our framebuffer yet because it is not complete. For a framebuffer to be complete the following requirements have to be satisfied:

* We have to attach at least one buffer (color, depth or stencil buffer).
* There should be at least one color attachment.
* All attachments should be complete as well (reserved memory).
* Each buffer should have the same number of samples.

Don't worry if you don't know what samples are, we'll get to those in a later tutorial.

From the requirements it should be clear that we need to create some kind of attachment for the framebuffer and attach this attachment to the framebuffer. After we've completed all requirements we can check if we actually successfully completed the framebuffer by calling glCheckFramebufferStatus with GL_FRAMEBUFFER. It then checks the currently bound framebuffer and returns any of these values found in the specification. If it returns GL_FRAMEBUFFER_COMPLETE we're good to go: 

```cpp
if(glCheckFramebufferStatus(GL_FRAMEBUFFER) == GL_FRAMEBUFFER_COMPLETE)
  // execute victory dance
```
All subsequent rendering operations will now render to the attachments of the currently bound framebuffer. Since our framebuffer is not the default framebuffer, the rendering commands will have no impact on the visual output of your window. For this reason it is called off-screen rendering while rendering to a different framebuffer. To make sure all rendering operations will have a visual impact on the main window we need to make the default framebuffer active again by binding to 0:

```cpp
glBindFramebuffer(GL_FRAMEBUFFER, 0);
```
When we're done with all framebuffer operations, do not forget to delete the framebuffer object: 
```cpp
glDeleteFramebuffers(1, &fbo);
```
 Now before the completeness check is executed we need to attach one or more attachments to the framebuffer. An attachment is a memory location that can act as a buffer for the framebuffer, think of it as an image. When creating an attachment we have two options to take: textures or renderbuffer objects.

## Texture attachments

When attaching a texture to a framebuffer, all rendering commands will write to the texture as if it was a normal color/depth or stencil buffer. The advantage of using textures is that the result of all rendering operations will be stored as a texture image that we can then easily use in our shaders.

Creating a texture for a framebuffer is roughly the same as a normal texture: 
```cpp
unsigned int texture;
glGenTextures(1, &texture);
glBindTexture(GL_TEXTURE_2D, texture);
  
glTexImage2D(GL_TEXTURE_2D, 0, GL_RGB, 800, 600, 0, GL_RGB, GL_UNSIGNED_BYTE, NULL);

glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_MIN_FILTER, GL_LINEAR);
glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_MAG_FILTER, GL_LINEAR); 
```
The main differences here is that we set the dimensions equal to the screen size (although this is not required) and we pass NULL as the texture's data parameter. For this texture, we're only allocating memory and not actually filling it. Filling the texture will happen as soon as we render to the framebuffer. Also note that we do not care about any of the wrapping methods or mipmapping since we won't be needing those in most cases.
If you want to render your whole screen to a texture of a smaller or larger size you need to call glViewport again (before rendering to your framebuffer) with the new dimensions of your texture, otherwise only a small part of the texture or screen would be drawn onto the texture.

Now that we've created a texture the last thing we need to do is actually attach it to the framebuffer: 

```cpp
glFramebufferTexture2D(GL_FRAMEBUFFER, GL_COLOR_ATTACHMENT0, GL_TEXTURE_2D, texture, 0);
```
 The glFrameBufferTexture2D has the following parameters:

 * target: the framebuffer type we're targeting (draw, read or both).
 * attachment: the type of attachment we're going to attach. Right now we're attaching a color attachment. Note that the 0 at the end suggests we can attach more than 1 color attachment. We'll get to that in a later tutorial.
 * textarget: the type of the texture you want to attach.
 * texture: the actual texture to attach.
 * level: the mipmap level. We keep this at 0.
 
 Aside from the color attachments we can also attach a depth and a stencil texture to the framebuffer object. To attach a depth attachment we specify the attachment type as GL_DEPTH_ATTACHMENT. Note that the texture's format and internalformat type should then become GL_DEPTH_COMPONENT to reflect the depth buffer's storage format. To attach a stencil buffer you use GL_STENCIL_ATTACHMENT as the second argument and specify the texture's formats as GL_STENCIL_INDEX.

It is also possible to attach both a depth buffer and a stencil buffer as a single texture. Each 32 bit value of the texture then consists for 24 bits of depth information and 8 bits of stencil information. To attach a depth and stencil buffer as one texture we use the GL_DEPTH_STENCIL_ATTACHMENT type and configure the texture's formats to contain combined depth and stencil values. An example of attaching a depth and stencil buffer as one texture to the framebuffer is given below: 

```cpp
glTexImage2D(
  GL_TEXTURE_2D, 0, GL_DEPTH24_STENCIL8, 800, 600, 0, 
  GL_DEPTH_STENCIL, GL_UNSIGNED_INT_24_8, NULL
);

glFramebufferTexture2D(GL_FRAMEBUFFER, GL_DEPTH_STENCIL_ATTACHMENT, GL_TEXTURE_2D, texture, 0);
```
## Renderbuffer object attachments

Renderbuffer objects were introduced to OpenGL after textures as a possible type of framebuffer attachments, so textures were the only attachments used in the good old days. Just like a texture image, a renderbuffer object is an actual buffer e.g. an array of bytes, integers, pixels or whatever. A renderbuffer object has the added advantage though that it stores its data in OpenGL's native rendering format making it optimized for off-screen rendering to a framebuffer.

Renderbuffer objects store all the render data directly into their buffer without any conversions to texture-specific formats, thus making them faster as a writeable storage medium. However, renderbuffer objects are generally write-only, thus you cannot read from them (like with texture-access). It is possible to read from them via glReadPixels though that returns a specified area of pixels from the currently bound framebuffer, but not directly from the attachment itself.

Because their data is already in its native format they are quite fast when writing data or simply copying their data to other buffers. Operations like switching buffers are thus quite fast when using renderbuffer objects. The glfwSwapBuffers function we've been using at the end of each render iteration might as well be implemented with renderbuffer objects: we simply write to a renderbuffer image, and swap to the other one at the end. Renderbuffer objects are perfect for these kind of operations.

Creating a renderbuffer object looks similar to the framebuffer's code: 
```cpp
unsigned int rbo;
glGenRenderbuffers(1, &rbo);
```
And similarly we want to bind the renderbuffer object so all subsequent renderbuffer operations affect the current rbo:
```cpp
glBindRenderbuffer(GL_RENDERBUFFER, rbo);
```
 Since renderbuffer objects are generally write-only they are often used as depth and stencil attachments, since most of the time we don't really need to read values from the depth and stencil buffers but still care about depth and stencil testing. We need the depth and stencil values for testing, but don't need to sample these values so a renderbuffer object suits this perfectly. When we're not sampling from these buffers, a renderbuffer object is generally preferred since it's more optimized.

Creating a depth and stencil renderbuffer object is done by calling the glRenderbufferStorage function: 
```cpp
glRenderbufferStorage(GL_RENDERBUFFER, GL_DEPTH24_STENCIL8, 800, 600);
```
 Creating a renderbuffer object is similar to texture objects, the difference being that this object is specifically designed to be used as an image, instead of a general purpose data buffer like a texture. Here we've chosen the GL_DEPTH24_STENCIL8 as the internal format, which holds both the depth and stencil buffer with 24 and 8 bits respectively.

Last thing left to do is actually attach the renderbuffer object: 
```cpp
glFramebufferRenderbuffer(GL_FRAMEBUFFER, GL_DEPTH_STENCIL_ATTACHMENT, GL_RENDERBUFFER, rbo);
```
Renderbuffer objects could provide some optimizations in your framebuffer projects, but it is important to realize when to use renderbuffer objects and when to use textures. The general rule is that if you never need to sample data from a specific buffer, it is wise to use a renderbuffer object for that specific buffer. If you need to someday sample data from a specific buffer like colors or depth values, you should use a texture attachment instead. Performance-wise it doesn't have an enormous impact though. 

## Rendering to a texture

Now that we know how framebuffers (sort of) work it's time to put them to good use. We're going to render the scene into a color texture attached to a framebuffer object we created and then draw this texture over a simple quad that spans the whole screen. The visual output is then exactly the same as without a framebuffer, but this time it's all printed on top of a single quad. Now why is this useful? In the next section we'll see why.

First thing to do is to create an actual framebuffer object and bind it, this is all relatively straightforward:
```cpp
unsigned int framebuffer;
glGenFramebuffers(1, &framebuffer);
glBindFramebuffer(GL_FRAMEBUFFER, framebuffer);
```
Next we create a texture image that we attach as a color attachment to the framebuffer. We set the texture's dimensions equal to the width and height of the window and keep its data uninitialized: 
```cpp
// generate texture
unsigned int texColorBuffer;
glGenTextures(1, &texColorBuffer);
glBindTexture(GL_TEXTURE_2D, texColorBuffer);
glTexImage2D(GL_TEXTURE_2D, 0, GL_RGB, 800, 600, 0, GL_RGB, GL_UNSIGNED_BYTE, NULL);
glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_MIN_FILTER, GL_LINEAR );
glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_MAG_FILTER, GL_LINEAR);
glBindTexture(GL_TEXTURE_2D, 0);

// attach it to currently bound framebuffer object
glFramebufferTexture2D(GL_FRAMEBUFFER, GL_COLOR_ATTACHMENT0, GL_TEXTURE_2D, texColorBuffer, 0);
```
 We also want to make sure OpenGL is able to do depth testing (and optionally stencil testing if you're into that) so we have to make sure to add a depth (and stencil) attachment to the framebuffer as well. Since we'll only be sampling the color buffer and not the other buffers we can create a renderbuffer object for this purpose. Remember that they're a good choice when you're not going to sample from the specific buffer(s)?

Creating a renderbuffer object isn't too hard. The only thing we have to remember is that we're creating it as a depth and stencil attachment renderbuffer object. We set its internal format to GL_DEPTH24_STENCIL8 which is enough precision for our purposes. 

```cpp
unsigned int rbo;
glGenRenderbuffers(1, &rbo);
glBindRenderbuffer(GL_RENDERBUFFER, rbo); 
glRenderbufferStorage(GL_RENDERBUFFER, GL_DEPTH24_STENCIL8, 800, 600);  
glBindRenderbuffer(GL_RENDERBUFFER, 0);
```

Once we've allocated enough memory for the renderbuffer object we can unbind the renderbuffer.

Then, as a final step before we can complete the framebuffer, we attach the renderbuffer object to the depth and stencil attachment of the framebuffer: 


```cpp
glFramebufferRenderbuffer(GL_FRAMEBUFFER, GL_DEPTH_STENCIL_ATTACHMENT, GL_RENDERBUFFER, rbo);
```

Then as a final measure we want to check if the framebuffer is actually complete and if it's not, we print an error message.

```cpp
if(glCheckFramebufferStatus(GL_FRAMEBUFFER) != GL_FRAMEBUFFER_COMPLETE)
	std::cout << "ERROR::FRAMEBUFFER:: Framebuffer is not complete!" << std::endl;
glBindFramebuffer(GL_FRAMEBUFFER, 0);
```
 Then also be sure to unbind the framebuffer to make sure we're not accidentally rendering to the wrong framebuffer.

Now that the framebuffer is complete all we need to do to render to the framebuffer's buffers instead of the default framebuffers is simply bind to the framebuffer object. All subsequent rendering commands will then influence the currently bound framebuffer. All the depth and stencil operations will also read from the currently bound framebuffer's depth and stencil attachments if they're available. If you were to omit a depth buffer for example, all depth testing operations will no longer work, because there's not a depth buffer present in the currently bound framebuffer.

So, to draw the scene to a single texture we'll have to take the following steps:

 1. Render the scene as usual with the new framebuffer bound as the active framebuffer.
 2. Bind to the default framebuffer.
 3. Draw a quad that spans the entire screen with the new framebuffer's color buffer as its texture.

We'll draw the same scene we've used in the depth testing tutorial, but this time with the old-school container texture.

To draw the quad we're going to create a fresh set of simple shaders. We're not going to include any fancy matrix transformations since we'll just be supplying the vertex coordinates as normalized device coordinates so we can directly specify them as the output of the vertex shader. The vertex shader looks like this: 

```cpp
#version 330 core
layout (location = 0) in vec2 aPos;
layout (location = 1) in vec2 aTexCoords;

out vec2 TexCoords;

void main()
{
    gl_Position = vec4(aPos.x, aPos.y, 0.0, 1.0); 
    TexCoords = aTexCoords;
}
```
Nothing too fancy. The fragment shader will be even more basic since the only thing we have to do is sample from a texture: 

```cpp
#version 330 core
out vec4 FragColor;
  
in vec2 TexCoords;

uniform sampler2D screenTexture;

void main()
{ 
    FragColor = texture(screenTexture, TexCoords);
} 
```
It is then up to you to create and configure a VAO for the screen quad. A render iteration of the framebuffer procedure then has the following structure: 
```cpp

// first pass
glBindFramebuffer(GL_FRAMEBUFFER, framebuffer);
glClearColor(0.1f, 0.1f, 0.1f, 1.0f);
glClear(GL_COLOR_BUFFER_BIT | GL_DEPTH_BUFFER_BIT); // we're not using the stencil buffer now
glEnable(GL_DEPTH_TEST);
DrawScene();	
  
// second pass
glBindFramebuffer(GL_FRAMEBUFFER, 0); // back to default
glClearColor(1.0f, 1.0f, 1.0f, 1.0f); 
glClear(GL_COLOR_BUFFER_BIT);
  
screenShader.use();  
glBindVertexArray(quadVAO);
glDisable(GL_DEPTH_TEST);
glBindTexture(GL_TEXTURE_2D, textureColorbuffer);
glDrawArrays(GL_TRIANGLES, 0, 6);
```
 There are a few things to note. First, since each framebuffer we're using has its own set of buffers, we want to clear each of those buffers with the appropriate bits set by calling glClear. Second, when drawing the quad, we're disabling depth testing since we don't really care about depth testing because we're drawing a simple quad; we'll have to enable depth testing again when we draw the normal scene though.

There are quite some steps that could go wrong here, so if you have no output, try to debug where possible and re-read the relevant sections of the tutorial. If everything did work out successfully you'll get a visual result that looks like this: 

<img src=https://learnopengl.com/img/advanced/framebuffers_screen_texture.png>
 The left shows the visual output which is exactly the same as we've seen in the depth testing tutorial, but this time, rendered to a simple quad. If we render the scene in wireframe it becomes obvious we've only drawn a single quad in the default framebuffer.

You can find the source code of the application here.

So what was the use of this again? Well, because we can now freely access each of the pixels of the completely rendered scene as a single texture image, we can create some interesting effects in the fragment shader. The combination of all these interesting effects are called post-processing effects. 

# Post-processing

Now that the entire scene is rendered to a single texture we can create some interesting effects simply by manipulating the texture data. In this section we'll show you some of the more popular post-processing effects and how you might create your own with some added creativity.

Let's start with one of the simplest post-processing effects.
## Inversion

We have access to each of the colors of the render output so it's not so hard to return the inverse of these colors in the fragment shader. We're taking the color of the screen texture and inverse it by subtracting it from 1.0: 
```cpp
void main()
{
    FragColor = vec4(vec3(1.0 - texture(screenTexture, TexCoords)), 1.0);
}
```


