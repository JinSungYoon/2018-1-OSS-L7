---
layout: tutorial
status: publish
published: true
title: 'Tutorial 6 : Keyboard and Mouse'
date: '2011-05-08 08:26:13 +0200'
date_gmt: '2011-05-08 08:26:13 +0200'
categories: [tuto]
order: 60
tags: []
language: kr
---

Welcome for our 6th tutorial !
6번째 튜토리얼에 오신것을 환영합니다.

We will now learn how to use the mouse and the keyboard to move the camera just like in a FPS.
우리는 지금부터 FPS처럼  마우스와 키보드를 사용하여 카메라를 어떻게 움직이는지지에 대해서 배울것입니다.

# The interface
# 인터페이스

Since this code will be re-used throughout the tutorials, we will put the code in a separate file : common/controls.cpp, and declare the functions in common/controls.hpp so that tutorial06.cpp knows about them.
이 코드는 튜토리얼 전반에 걸쳐 재사용 될것이기 때문에, 우리는 이 코드를 별도의 파일(common/controls.cpp) 넣을것이며, 그리고 함수를 common/controls.hpp에 선언할것입니다.따라서 tutorial06.cpp는 common.controls.cpp와 common/controls.hpp를 인지하고 있습니다.

The code of tutorial06.cpp doesn't change much from the previous tutorial. The major modification is that instead of computing the MVP matrix once, we now have to do it every frame. So let's move this code inside the main loop :
tutorial06.cpp 코드는 이전의 튜토리얼에서 많이 변경되지 않았습니다. 주요 변경된 부분은 컴퓨팅 MVP 매트릭스를 한번에 계산하는 대신에 이제 모든 프레임에서 수행을 해야합니다.그래서 이 코드를 메인 루프 안으로 옮깁니다.

``` cpp
do{

    // ...

    // Compute the MVP matrix from keyboard and mouse input
    computeMatricesFromInputs();
    glm::mat4 ProjectionMatrix = getProjectionMatrix();
    glm::mat4 ViewMatrix = getViewMatrix();
    glm::mat4 ModelMatrix = glm::mat4(1.0);
    glm::mat4 MVP = ProjectionMatrix * ViewMatrix * ModelMatrix;

    // ...
}
```

This code needs 3 new functions :
이 코드는 3개의 새로운 함수가 필요합니다.

* computeMatricesFromInputs() reads the keyboard and mouse and computes the Projection and View matrices. This is where all the magic happens.
* getProjectionMatrix() just returns the computed Projection matrix.
* getViewMatrix() just returns the computed View matrix.
* computerMatricesFromInputs()는 키보드와 마우스를 읽고, 프로젝션과 뷰 행렬을 계산합니다.
* getProjectionMatrix()는 행렬의 프로젝션만 계산하여 반환합니다.
* getViewMatrix()는 행렬의 뷰 행렬만 계산하여 반환합니다.

This is just one way to do it, of course. If you don't like these functions, go ahead and change them.
이것은 단지 한가지 방법입니다.물론 이러한 종류의 함수를 당신이 싫어한다면, 함수 를 바꾸셔도 됩니다.

Let's see what's inside controls.cpp.
controls.cpp의 내부가 어떻게 구성되어 있는지 봅시다.

# The actual code
# 실제 코드

We'll need a few variables.
우리는 몇 가지의 변수가 필요합니다.

``` cpp
// position
// 위치

glm::vec3 position = glm::vec3( 0, 0, 5 );
// horizontal angle : toward -Z
// 수평각 : -z 방향
float horizontalAngle = 3.14f;
// vertical angle : 0, look at the horizon
// 수직각 : 0, 수평방향
float verticalAngle = 0.0f;
// Initial Field of View
// 초기 뷰의 범위
float initialFoV = 45.0f;

float speed = 3.0f; // 3 units / second
float mouseSpeed = 0.005f;
```

FoV is the level of zoom. 80&deg; = very wide angle, huge deformations. 60&deg; - 45&deg; : standard. 20&deg; : big zoom.
Fov는 줌의 정도입니다. 80&deg; = 매우 넓은 각도, 큰 변형. 60&deg; - 45&deg; : 표준, 20&deg; : 큰 줌.

We will first recompute position, horizontalAngle, verticalAngle and FoV according to the inputs, and then compute the View and Projection matrices from position, horizontalAngle, verticalAngle and FoV.
처음에 우리는 입력에 따라  위치와 수평각, 수직각 그리고 FoV를 다시 계산하고, 위치,수평각,수직각 그리고 FoV에서  뷰와 프로젝션 행렬을 계산합니다. 

## Orientation
## 방향

Reading the mouse position is easy :
마우스의 위치를 읽는것은 쉽습니다.

``` cpp
// Get mouse position
// 마우스의 위치를 가져온다.

int xpos, ypos;
glfwGetMousePos(&xpos, &ypos);
```

but we have to take care to put the cursor back to the center of the screen, or it will soon go outside the window and you won't be able to move anymore.
우리는 커서를 화면 중앙으로 되돌려 놓는것을 조심해야한다, 그렇지 않으면 윈도우 창 밖으로 마우스가 나가서 더이상 움직일 수 없다.

``` cpp
// Reset mouse position for next frame
// 다음 프레임의 마우스 위치 재설정
glfwSetMousePos(1024/2, 768/2);
```

Notice that this code assumes that the window is 1024*768, which of course is not necessarily the case. You can use glfwGetWindowSize if you want, too.
이 코드는 윈도우에서 1024*768이라고 가정해야 한다. 물론 이것이 필수적인 사항은 아니다.당신이 원한다면 giFwGetWindowSize를 사용해도 된다.

We can now compute our viewing angles :
우리는 시야각을 계산할 수 있다.

``` cpp
// Compute new orientation
// 새로운 방향을 계산한다.
horizontalAngle += mouseSpeed * deltaTime * float(1024/2 - xpos );
verticalAngle   += mouseSpeed * deltaTime * float( 768/2 - ypos );
```

Let's read this from right to left :
오른쪽부터 왼쪽으로 읽어봅시다.

* 1024/2 - xpos means : how far is the mouse from the center of the window ? The bigger this value, the more we want to turn.
* float(...) converts it to a floating-point number so that the multiplication goes well.
* mouseSpeed is just there to speed up or slow down the rotations. Fine-tune this at will, or let the user choose it.
* += : If you didn't move the mouse, 1024/2-xpos will be 0, and horizontalAngle+=0 doesn't change horizontalAngle. If you had a "=" instead, you would be forced back to your original orientation each frame, which isn't good.

* 1024/2 - xpos 의미 : 윈도우의 중심으로부터 마우스가 얼마나 멀어져 있나? 이 값이 클수록 더 많이 돌려야 한다.
* float(...) 곱셈이 잘 되도록 부동 소수점을 수로 변환합니다.
* mousespeed는 마우스 속도를 높이거나 회전을 느리게 할 수 있습니다.이 기능을 원하는대로 미세 조정하거나 사용자가 선택할 수 있도록 해주세요.
* += : 만일 마우스를 움지깅지 않으면 1024/2-xpos는 0이되고, horizontalAngle +=은 0 horizontalAngle을 변경하지 않습니다. 대신 "=" 기호를 사용하면 각 프레임의 원래 방향으로 되돌아 가게 됩니다. 이는 별로 좋지 않습니다.  

We can now compute a vector that represents, in World Space, the direction in which we're looking

``` cpp
// Direction : Spherical coordinates to Cartesian coordinates conversion
glm::vec3 direction(
    cos(verticalAngle) * sin(horizontalAngle),
    sin(verticalAngle),
    cos(verticalAngle) * cos(horizontalAngle)
);
```

This is a standard computation, but if you don't know about cosine and sinus, here's a short explanation :

<img class="alignnone whiteborder" title="Trigonometric circle" src="http://www.numericana.com/answer/trig.gif" alt="" width="150" height="150" />

The formula above is just the generalisation to 3D.

Now we want to compute the "up" vector reliably. Notice that "up" isn't always towards +Y : if you look down, for instance, the "up" vector will be in fact horizontal. Here is an example of to cameras with the same position, the same target, but a different up:

![]({{site.baseurl}}/assets/images/tuto-6-mouse-keyboard/CameraUp.png)

In our case, the only constant is that the vector goes to the right of the camera is always horizontal. You can check this by putting your arm horizontal, and looking up, down, in any direction. So let's define the "right" vector : its Y coordinate is 0 since it's horizontal, and its X and Z coordinates are just like in the figure above, but with the angles rotated by 90&deg;, or Pi/2 radians.

``` cpp
// Right vector
glm::vec3 right = glm::vec3(
    sin(horizontalAngle - 3.14f/2.0f),
    0,
    cos(horizontalAngle - 3.14f/2.0f)
);
```

We have a "right" vector and a "direction", or "front" vector. The "up" vector is a vector that is perpendicular to these two. A useful mathematical tool makes this very easy : the cross product.

``` cpp
// Up vector : perpendicular to both direction and right
glm::vec3 up = glm::cross( right, direction );
```

To remember what the cross product does, it's very simple. Just recall the Right Hand Rule from Tutorial 3. The first vector is the thumb; the second is the index; and the result is the middle finger. It's very handy.

## Position

The code is pretty straightforward. By the way, I used the up/down/right/left keys instead of the awsd because on my azerty keyboard, awsd is actually zqsd. And it's also different with qwerZ keyboards, let alone korean keyboards. I don't even know what layout korean people have, but I guess it's also different.

``` cpp
// Move forward
if (glfwGetKey( GLFW_KEY_UP ) == GLFW_PRESS){
    position += direction * deltaTime * speed;
}
// Move backward
if (glfwGetKey( GLFW_KEY_DOWN ) == GLFW_PRESS){
    position -= direction * deltaTime * speed;
}
// Strafe right
if (glfwGetKey( GLFW_KEY_RIGHT ) == GLFW_PRESS){
    position += right * deltaTime * speed;
}
// Strafe left
if (glfwGetKey( GLFW_KEY_LEFT ) == GLFW_PRESS){
    position -= right * deltaTime * speed;
}
```

The only special thing here is the deltaTime. You don't want to move from 1 unit each frame for a simple reason :

* If you have a fast computer, and you run at 60 fps, you'd move of 60*speed units in 1 second
* If you have a slow computer, and you run at 20 fps, you'd move of 20*speed units in 1 second

Since having a better computer is not an excuse for going faster, you have to scale the distance by the "time since the last frame", or "deltaTime".

* If you have a fast computer, and you run at 60 fps, you'd move of 1/60 * speed units in 1 frame, so 1*speed in 1 second.
* If you have a slow computer, and you run at 20 fps, you'd move of 1/20 * speed units in 1 second, so 1*speed in 1 second.

which is much better. deltaTime is very simple to compute :

``` cpp
double currentTime = glfwGetTime();
float deltaTime = float(currentTime - lastTime);
```

## Field Of View

For fun, we can also bind the wheel of the mouse to the Field Of View, so that we can have a cheap zoom :

``` cpp
float FoV = initialFoV - 5 * glfwGetMouseWheel();
```

## Computing the matrices

Computing the matrices is now straightforward. We use the exact same functions than before, but with our new parameters.

``` cpp
// Projection matrix : 45&deg; Field of View, 4:3 ratio, display range : 0.1 unit <-> 100 units
ProjectionMatrix = glm::perspective(glm::radians(FoV), 4.0f / 3.0f, 0.1f, 100.0f);
// Camera matrix
ViewMatrix       = glm::lookAt(
    position,           // Camera is here
    position+direction, // and looks here : at the same position, plus "direction"
    up                  // Head is up (set to 0,-1,0 to look upside-down)
);
```

# Results

![]({{site.baseurl}}/assets/images/tuto-6-mouse-keyboard/moveanim.gif)


## Backface Culling

Now that you can freely move around, you'll notice that if you go inside the cube, polygons are still displayed. This can seem obvious, but this remark actually opens an opportunity for optimisation. As a matter of fact, in a usual application, you are never _inside_ a cube.

The idea is to let the GPU check if the camera is behind, or in front of, the triangle. If it's in front, display the triangle; if it's behind, *and* the mesh is closed, *and* we're not inside the mesh, *then* there will be another triangle in front of it, and nobody will notice anything, except that everything will be faster : 2 times less triangles on average !

The best thing is that it's very easy to check this. The GPU computes the normal of the triangle (using the cross product, remember ?) and checks whether this normal is oriented towards the camera or not.

This comes at a cost, unfortunately : the orientation of the triangle is implicit. This means that is you invert two vertices in your buffer, you'll probably end up with a hole. But it's generally worth the little additional work. Often, you just have to click "invert normals" in your 3D modeler (which will, in fact, invert vertices, and thus normals) and everything is just fine.

Enabling backface culling is a breeze :

``` cpp
// Cull triangles which normal is not towards the camera
glEnable(GL_CULL_FACE);
```

# Exercices


* Restrict verticalAngle so that you can't go upside-down
* Create a camera that rotates around the object ( position = ObjectCenter + ( radius * cos(time), height, radius * sin(time) ) ); bind the radius/height/time to the keyboard/mouse, or whatever
* Have fun !
