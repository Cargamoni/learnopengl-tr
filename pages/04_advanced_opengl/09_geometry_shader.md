---
title: Geometri Gölgelendiricisi
keywords: 
last_updated: 
tags: []
permalink: /advanced_opengl/geometry_shader.html
sidebar: main_sidebar
---
Geometry Shader

Between the vertex and the fragment shader there is an optional shader stage called the geometry shader. A geometry shader takes as input a set of vertices that form a single primitive e.g. a point or a triangle. The geometry shader can then transform these vertices as it sees fit before sending them to the next shader stage. What makes the geometry shader interesting however is that it is able to transform the (set of) vertices to completely different primitives possibly generating much more vertices than were initially given.

We're going to throw you right into the deep by showing you an example of a geometry shader:

```glsl
#version 330 core
layout (points) in;
layout (line_strip, max_vertices = 2) out;

void main() {    
    gl_Position = gl_in[0].gl_Position + vec4(-0.1, 0.0, 0.0, 0.0); 
    EmitVertex();

    gl_Position = gl_in[0].gl_Position + vec4( 0.1, 0.0, 0.0, 0.0);
    EmitVertex();
    
    EndPrimitive();
}  
```
At the start of every geometry shader we need to declare the type of primitive input we're receiving from the vertex shader. We do this by declaring a layout specifier in front of the in keyword. This input layout qualifier can take any of the following primitive values from a vertex shader:

    points: when drawing GL_POINTS primitives (1).
    lines: when drawing GL_LINES or GL_LINE_STRIP (2).
    lines_adjacency: GL_LINES_ADJACENCY or GL_LINE_STRIP_ADJACENCY (4).
    triangles: GL_TRIANGLES, GL_TRIANGLE_STRIP or GL_TRIANGLE_FAN (3).
    triangles_adjacency : GL_TRIANGLES_ADJACENCY or GL_TRIANGLE_STRIP_ADJACENCY (6).

These are almost all the rendering primitives we're able to give to rendering calls like glDrawArrays. If we'd chosen to draw vertices as GL_TRIANGLES we should set the input qualifier to triangles. The number within the parenthesis represents the minimal number of vertices a single primitive contains.

Then we also need to specify a primitive type that the geometry shader will actually output and we do this via a layout specifier in front of the out keyword. Like the input layout qualifier, the output layout qualifier can also take several primitive values:

    points
    line_strip
    triangle_strip

With just these 3 output specifiers we can create almost any shape we want from the input primitives. To generate a single triangle for example we'd specify triangle_strip as the output and then output 3 vertices.

The geometry shader also expects us to set a maximum number of vertices it outputs (if you exceed this number, OpenGL won't draw the extra vertices) which we can also do within the layout qualifier of the out keyword. In this particular case we're going to output a line_strip with a maximum number of 2 vertices.

In case you are wondering what a line strip is: a line strip binds together a set of points to form one continuous line between them with a minimum of 2 points. Each extra point given to the rendering call results in a new line between the new point and the previous point as you can see in the image below where we have 5 point vertices: 

<img src="https://learnopengl.com/img/advanced/geometry_shader_line_strip.png">

 With the current shader we'll only output a single line since the maximum number of vertices is equal to 2.

To generate meaningful results we need some way to retrieve the output from the previous shader stage. GLSL gives us a built-in variable called gl_in that internally (probably) looks something like this:


in gl_Vertex
{
    vec4  gl_Position;
    float gl_PointSize;
    float gl_ClipDistance[];
} gl_in[];  

Here it is declared as an interface block (as discussed in the previous tutorial) that contains a few interesting variables of which the most interesting one is gl_Position that contains the similar vector we set as the vertex shader's output.

Note that it is declared as an array, because most render primitives consist of more than 1 vertex and the geometry shader receives all vertices of a primitive as its input.

Using the vertex data from the previous vertex shader stage we can start to generate new data which is done via 2 geometry shader functions called EmitVertex and EndPrimitive. The geometry shader expects you to generate/output at least one of the primitives you specified as output. In our case we want to at least generate one line strip primitive.

```glsl
void main() {    
    gl_Position = gl_in[0].gl_Position + vec4(-0.1, 0.0, 0.0, 0.0); 
    EmitVertex();

    gl_Position = gl_in[0].gl_Position + vec4( 0.1, 0.0, 0.0, 0.0);
    EmitVertex();
    
    EndPrimitive();
}    
```
Each time we call EmitVertex the vector currently set to gl_Position is added to the primitive. Whenever EndPrimitive is called, all emitted vertices for this primitive are combined into the specified output render primitive. By repeatedly calling EndPrimitive after one or more EmitVertex calls multiple primitives can be generated. This particular case emits two vertices that were translated by a small offset from the original vertex position and then calls EndPrimitive, combining these two vertices into a single line strip of 2 vertices.

Now that you (sort of) know how geometry shaders work you can probably guess what this geometry shader does. This geometry shader takes a point primitive as its input and creates a horizontal line primitive with the input point at its center. If we were to render this it looks something like this: 

<img src="https://learnopengl.com/img/advanced/geometry_shader_lines.png">

 Not very impressive yet, but it's interesting to consider that this output was generated using just the following render call:


glDrawArrays(GL_POINTS, 0, 4);  

While this is a relatively simple example, it does show you how we can use geometry shaders to (dynamically) generate new shapes on the fly. Later in this tutorial we'll discuss some interesting effects that we can achieve using geometry shaders, but for now we're going to start by creating a simple geometry shader.
Using geometry shaders

To demonstrate the use of a geometry shader we're going to render a really simple scene where we just draw 4 points on the z-plane in normalized device coordinates. The coordinates of the points are:


float points[] = {
	-0.5f,  0.5f, // top-left
	 0.5f,  0.5f, // top-right
	 0.5f, -0.5f, // bottom-right
	-0.5f, -0.5f  // bottom-left
};  

The vertex shader only needs to draw the points on the z-plane so we'll only need a basic vertex shader:


#version 330 core
layout (location = 0) in vec2 aPos;

void main()
{
    gl_Position = vec4(aPos.x, aPos.y, 0.0, 1.0); 
}

And we'll simply output the color green for all points which we hardcode in the fragment shader:


#version 330 core
out vec4 FragColor;

void main()
{
    FragColor = vec4(0.0, 1.0, 0.0, 1.0);   
}  

Generate a VAO and a VBO for the points' vertex data and then draw them via glDrawArrays:


shader.use();
glBindVertexArray(VAO);
glDrawArrays(GL_POINTS, 0, 4); 

The result is a dark scene with 4 (difficult to see) green points: 

<img src="https://learnopengl.com/img/advanced/geometry_shader_points.png">

 But didn't we already learn to do all this? Yes, and now we're going to spice this little scene up by adding a geometry shader to the scene.

For learning purposes we're going to create what is called a pass-through geometry shader that takes a point primitive as its input and passes it to the next shader unmodified:


#version 330 core
layout (points) in;
layout (points, max_vertices = 1) out;

void main() {    
    gl_Position = gl_in[0].gl_Position; 
    EmitVertex();
    EndPrimitive();
}  

By now this geometry shader should be fairly easy to understand. It simply emits the unmodified vertex position it received as input and generates a point primitive.

A geometry shader needs to be compiled and linked to a program just like the vertex and fragment shader, but this time we'll create the shader using GL_GEOMETRY_SHADER as the shader type:


geometryShader = glCreateShader(GL_GEOMETRY_SHADER);
glShaderSource(geometryShader, 1, &gShaderCode, NULL);
glCompileShader(geometryShader);  
...
glAttachShader(program, geometryShader);
glLinkProgram(program);  

The shader compilation code is basically the same as the vertex and fragment shader code. Be sure to check for compile or linking errors!

If you'd now compile and run you should be looking at a result similar to the following: 

<img src="https://learnopengl.com/img/advanced/geometry_shader_points.png">

 It's exactly the same as without the geometry shader! It's a bit dull, I'll admit that, but the fact that we were still able to draw the points means that the geometry shader works, so now it's time for the more funky stuff!
Let's build some houses

Drawing points and lines isn't that interesting so we're going to get a little creative by using the geometry shader to draw a house for us at the location of each point. We can achieve this by setting the output of the geometry shader to triangle_strip and draw a total of three triangles: two for a square and one for the roof.

A triangle strip in OpenGL is a more efficient way to draw triangles using less vertices. After the first triangle is drawn, each subsequent vertex will generate another triangle next to the first triangle: every 3 adjacent vertices will form a triangle. If we have a total of 6 vertices that form a triangle strip we'd get the following triangles: (1,2,3), (2,3,4), (3,4,5) and (4,5,6) forming a total of 4 triangles. A triangle strip needs at least 3 vertices and will generate N-2 triangles; with 6 vertices we created 6-2 = 4 triangles. The following image illustrates this: 

<img src="https://learnopengl.com/img/advanced/geometry_shader_triangle_strip.png">

Using a triangle strip as the output of the geometry shader we can easily create the house shape we're after by generating 3 adjacent triangles in the correct order. The following image shows in what order we need to draw what vertices to get the triangles we need with the blue dot being the input point: 

<img src="https://learnopengl.com/img/advanced/geometry_shader_house.png">

 This translates to the following geometry shader:


#version 330 core
layout (points) in;
layout (triangle_strip, max_vertices = 5) out;

void build_house(vec4 position)
{    
    gl_Position = position + vec4(-0.2, -0.2, 0.0, 0.0);    // 1:bottom-left
    EmitVertex();   
    gl_Position = position + vec4( 0.2, -0.2, 0.0, 0.0);    // 2:bottom-right
    EmitVertex();
    gl_Position = position + vec4(-0.2,  0.2, 0.0, 0.0);    // 3:top-left
    EmitVertex();
    gl_Position = position + vec4( 0.2,  0.2, 0.0, 0.0);    // 4:top-right
    EmitVertex();
    gl_Position = position + vec4( 0.0,  0.4, 0.0, 0.0);    // 5:top
    EmitVertex();
    EndPrimitive();
}

void main() {    
    build_house(gl_in[0].gl_Position);
}  

This geometry shader generates 5 vertices, with each vertex being the point's position plus an offset, to form one large triangle strip. The resulting primitive is then rasterized and the fragment shader runs on the entire triangle strip, resulting in a green house for each point we've drawn: 

<img src="https://learnopengl.com/img/advanced/geometry_shader_houses.png">

 You can see that each house indeed consists of 3 triangles - all drawn using just a single point in space. The green houses do look a bit boring though so let's liven it up a bit by giving each house a unique color. To do this we're going to add an extra vertex attribute with color information per vertex in the vertex shader, direct it to the geometry shader that further forwards it to the fragment shader.

The updated vertex data is given below:


float points[] = {
    -0.5f,  0.5f, 1.0f, 0.0f, 0.0f, // top-left
     0.5f,  0.5f, 0.0f, 1.0f, 0.0f, // top-right
     0.5f, -0.5f, 0.0f, 0.0f, 1.0f, // bottom-right
    -0.5f, -0.5f, 1.0f, 1.0f, 0.0f  // bottom-left
};  

Then we update the vertex shader to forward the color attribute to the geometry shader using an interface block:


#version 330 core
layout (location = 0) in vec2 aPos;
layout (location = 1) in vec3 aColor;

out VS_OUT {
    vec3 color;
} vs_out;

void main()
{
    gl_Position = vec4(aPos.x, aPos.y, 0.0, 1.0); 
    vs_out.color = aColor;
}  

Then we also need to declare the same interface block (with a different interface name) in the geometry shader:


in VS_OUT {
    vec3 color;
} gs_in[];  

Because the geometry shader acts on a set of vertices as its input, its input data from the vertex shader is always represented as arrays of data even though we only have a single vertex right now.
We don't necessarily have to use interface blocks to transfer data to the geometry shader. We could have also written it as:


in vec3 vColor[];

If the vertex shader forwarded the color vector as out vec3 vColor. However, interface blocks are much easier to work with in shaders like the geometry shader. In practice, geometry shader inputs can get quite large and grouping them in one large interface block array makes a lot more sense.

Then we should also declare an output color vector for the next fragment shader stage:


out vec3 fColor;  

Because the fragment shader expects only a single (interpolated) color it doesn't make sense to forward multiple colors. The fColor vector is thus not an array, but a single vector. When emitting a vertex, each vertex will store the last stored value in fColor for its fragment shader run. For the houses, we can thus only fill fColor once with the color from the vertex shader before the first vertex is emitted to color the entire house:


fColor = gs_in[0].color; // gs_in[0] since there's only one input vertex
gl_Position = position + vec4(-0.2, -0.2, 0.0, 0.0);    // 1:bottom-left   
EmitVertex();   
gl_Position = position + vec4( 0.2, -0.2, 0.0, 0.0);    // 2:bottom-right
EmitVertex();
gl_Position = position + vec4(-0.2,  0.2, 0.0, 0.0);    // 3:top-left
EmitVertex();
gl_Position = position + vec4( 0.2,  0.2, 0.0, 0.0);    // 4:top-right
EmitVertex();
gl_Position = position + vec4( 0.0,  0.4, 0.0, 0.0);    // 5:top
EmitVertex();
EndPrimitive();  

All the emitted vertices will have the last stored value in fColor embedded into their data that is equal to the vertex's color as we defined in their attributes. All the houses will now have a color of their own: 

<img src="https://learnopengl.com/img/advanced/geometry_shader_houses_colored.png">

 Just for fun we could also pretend it's winter and give their roofs a little snow by giving the last vertex a color of its own: white.


fColor = gs_in[0].color; 
gl_Position = position + vec4(-0.2, -0.2, 0.0, 0.0);    // 1:bottom-left   
EmitVertex();   
gl_Position = position + vec4( 0.2, -0.2, 0.0, 0.0);    // 2:bottom-right
EmitVertex();
gl_Position = position + vec4(-0.2,  0.2, 0.0, 0.0);    // 3:top-left
EmitVertex();
gl_Position = position + vec4( 0.2,  0.2, 0.0, 0.0);    // 4:top-right
EmitVertex();
gl_Position = position + vec4( 0.0,  0.4, 0.0, 0.0);    // 5:top
fColor = vec3(1.0, 1.0, 1.0);
EmitVertex();
EndPrimitive();  

The result now looks something like this: 

<img src ="https://learnopengl.com/img/advanced/geometry_shader_houses_snow.png">

 You can compare your source code with the OpenGL code here.

You can see that with geometry shaders you can get pretty creative even with the simplest primitives. Because the shapes are generated dynamically on the ultra-fast hardware of your GPU this is a lot more efficient than defining these shapes yourself within vertex buffers. Geometry buffers are therefore a great tool for optimization on simple often-repeating shapes like cubes in a voxel world or grass leaves on a large outdoor field.
Exploding objects

While drawing houses is fun and all, it's not something we're going to use that much. That's why we're now going to take it up one notch and explode objects! That is something we're also probably not going to use that much either, but it does show you some of the powers of geometry shaders.

When we say exploding an object we're not actually going to blow up our precious bundled sets of vertices, but we're going to move each triangle along the direction of their normal vector over a small period of time. The effect is that the entire object's triangles seem to explode along the direction of each of the triangle's normal vectors. The effect of exploding triangles on the nanosuit model looks a bit like this: 

<img src="https://learnopengl.com/img/advanced/geometry_shader_explosion.png">

 The great thing about such a geometry shader effect is that it works on all objects, regardless of their complexity.

Because we're going to translate each vertex into the direction of the triangle's normal vector we first need to calculate this normal vector. What we need to do is calculate a vector that is perpendicular to the surface of a triangle, using just the 3 vertices we have access to. You might remember from the transformations tutorial that we can retrieve a vector perpendicular to two other vectors using the cross product. If we were to retrieve two vectors a and b that are parallel to the surface of the triangle we can retrieve the normal vector by doing a cross product on those vectors. The following geometry shader function does exactly this to retrieve the normal vector using 3 input vertex coordinates:


vec3 GetNormal()
{
   vec3 a = vec3(gl_in[0].gl_Position) - vec3(gl_in[1].gl_Position);
   vec3 b = vec3(gl_in[2].gl_Position) - vec3(gl_in[1].gl_Position);
   return normalize(cross(a, b));
}  

Here we retrieve two vectors a and b that are parallel to the surface of the triangle using subtraction. Subtracting two vectors from each other results in a vector that is the difference of the two vectors and since all 3 points lie on the triangle plane, subtracting any of its vectors from each other results in a vector parallel to the plane. Do note that if we switched a and b in the cross function we'd get a normal vector that points in the opposite direction - order is important here!

Now that we know how to calculate a normal vector we can create an explode function that takes this normal vector along with a vertex position vector. The function returns a new vector that translates the position vector along the direction of the normal vector:


vec4 explode(vec4 position, vec3 normal)
{
    float magnitude = 2.0;
    vec3 direction = normal * ((sin(time) + 1.0) / 2.0) * magnitude; 
    return position + vec4(direction, 0.0);
} 

The function itself shouldn't be too complicated. The sin function receives a time variable as its argument that based on the time returns a value between -1.0 and 1.0. Because we don't want to implode the object we transform the sin values to the [0,1] range. The resulting value is then multiplied by the normal vector and the resulting direction vector is added to the position vector.

The complete geometry shader for the explode effect while drawing a model loaded using our model loader then looks like this:


#version 330 core
layout (triangles) in;
layout (triangle_strip, max_vertices = 3) out;

in VS_OUT {
    vec2 texCoords;
} gs_in[];

out vec2 TexCoords; 

uniform float time;

vec4 explode(vec4 position, vec3 normal) { ... }

vec3 GetNormal() { ... }

void main() {    
    vec3 normal = GetNormal();

    gl_Position = explode(gl_in[0].gl_Position, normal);
    TexCoords = gs_in[0].texCoords;
    EmitVertex();
    gl_Position = explode(gl_in[1].gl_Position, normal);
    TexCoords = gs_in[1].texCoords;
    EmitVertex();
    gl_Position = explode(gl_in[2].gl_Position, normal);
    TexCoords = gs_in[2].texCoords;
    EmitVertex();
    EndPrimitive();
}  

Note that we're also outputting the appropriate texture coordinates before emitting a vertex.

Also don't forget to actually set the time variable in your OpenGL code:


shader.setFloat("time", glfwGetTime());  

The result is a 3D model that seems to continually explode its vertices over time after which it returns to normal again. Although not exactly super useful, it does show you a more advanced use of the geometry shader. You can compare your source code with the complete source code here.
Visualizing normal vectors

In this section we're going to give you an example of using the geometry shader that is actually useful: visualizing the normal vectors of any object. When programming lighting shaders you will eventually run into weird visual outputs of which the cause is hard to determine. A common cause of lighting errors is due to incorrect normal vectors caused by incorrectly loading vertex data, improperly specifying them as vertex attributes or incorrectly managing them in the shaders. What we want is some way to detect if the normal vectors we supplied are correct. A great way to determine if your normal vectors are correct is by visualizing them and it just so happens that the geometry shader is an extremely useful tool for this purpose.

The idea is as follows: we first draw the scene as normal without a geometry shader and then we draw the scene a second time, but this time only displaying normal vectors that we generate via a geometry shader. The geometry shader takes as input a triangle primitive and generates 3 lines from those in the direction of the normal vector - one normal vector for each vertex. In pseudocode it'll look something like this:


shader.use();
DrawScene();
normalDisplayShader.use();
DrawScene();

This time we're creating a geometry shader that uses the vertex normals supplied by the model instead of generating them ourselves. To accommodate for scaling and rotations (due to the view and model matrix) we'll transform the normals first with a normal matrix before transforming it to clip-space coordinates (geometry shader receives its position vectors as clip-space coordinates so we should also transform the normal vectors to the same space). This can all be done in the vertex shader:


#version 330 core
layout (location = 0) in vec3 aPos;
layout (location = 1) in vec3 aNormal;

out VS_OUT {
    vec3 normal;
} vs_out;

uniform mat4 projection;
uniform mat4 view;
uniform mat4 model;

void main()
{
    gl_Position = projection * view * model * vec4(aPos, 1.0); 
    mat3 normalMatrix = mat3(transpose(inverse(view * model)));
    vs_out.normal = normalize(vec3(projection * vec4(normalMatrix * aNormal, 0.0)));
}

The transformed clip-space normal vector is then passed to the next shader stage via an interface block. The geometry shader then takes each vertex (with a position and a normal vector) and draws a normal vector from each position vector:


#version 330 core
layout (triangles) in;
layout (line_strip, max_vertices = 6) out;

in VS_OUT {
    vec3 normal;
} gs_in[];

const float MAGNITUDE = 0.4;

void GenerateLine(int index)
{
    gl_Position = gl_in[index].gl_Position;
    EmitVertex();
    gl_Position = gl_in[index].gl_Position + vec4(gs_in[index].normal, 0.0) * MAGNITUDE;
    EmitVertex();
    EndPrimitive();
}

void main()
{
    GenerateLine(0); // first vertex normal
    GenerateLine(1); // second vertex normal
    GenerateLine(2); // third vertex normal
}  

The contents of geometry shaders like these should be self-explanatory by now. Note that we're multiplying the normal vector by a MAGNITUDE vector to restrain the size of the displayed normal vectors (otherwise they'd be a bit too large).

Since visualizing normals are mostly used for debugging purposes we can just display them as mono-colored lines (or super-fancy lines if you feel like it) with the help of the fragment shader:


#version 330 core
out vec4 FragColor;

void main()
{
    FragColor = vec4(1.0, 1.0, 0.0, 1.0);
}  

Now rendering your model with normal shaders first and then with the special normal-visualizing shader you'll see something like this: 

<img src="https://learnopengl.com/img/advanced/geometry_shader_normals.png">

 Apart from the fact that our nanosuit now looks like a hairy guy with kitchen mittens on, it gives us a really useful method for determining if the normal vectors of a model are indeed correct. You can imagine that geometry shaders like this are also frequently used for adding fur to objects.

You can find the OpenGL's source code here. 
