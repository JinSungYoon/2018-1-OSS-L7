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

6번째 튜토리얼에 오신것을 환영합니다.

우리는 지금부터 FPS처럼  마우스와 키보드를 사용하여 카메라를 어떻게 움직이는지지에 대해서 배울것입니다.

# 인터페이스

이 코드는 튜토리얼 전반에 걸쳐 재사용 될것이기 때문에, 우리는 이 코드를 별도의 파일(common/controls.cpp) 넣을것이며, 그리고 함수를 common/controls.hpp에 선언할것입니다.따라서 tutorial06.cpp는 common.controls.cpp와 common/controls.hpp를 인지하고 있습니다.

tutorial06.cpp 코드는 이전의 튜토리얼에서 많이 변경되지 않았습니다. 주요 변경된 부분은 컴퓨팅 MVP 매트릭스를 한번에 계산하는 대신에 이제 모든 프레임에서 수행을 해야합니다.그래서 이 코드를 메인 루프 안으로 옮깁니다.

``` cpp
do{

    // ...

    // 키보드와 마우스의 입력으로 MVP 행렬을 계산하시오.
    computeMatricesFromInputs();
    glm::mat4 ProjectionMatrix = getProjectionMatrix();
    glm::mat4 ViewMatrix = getViewMatrix();
    glm::mat4 ModelMatrix = glm::mat4(1.0);
    glm::mat4 MVP = ProjectionMatrix * ViewMatrix * ModelMatrix;

    // ...
}
```

이 코드는 3개의 새로운 함수가 필요합니다.

* computerMatricesFromInputs()는 키보드와 마우스를 읽고, 프로젝션과 뷰 행렬을 계산합니다.
* getProjectionMatrix()는 행렬의 프로젝션만 계산하여 반환합니다.
* getViewMatrix()는 행렬의 뷰 행렬만 계산하여 반환합니다.

이것은 단지 한가지 방법입니다.물론 이러한 종류의 함수를 당신이 싫어한다면, 함수 를 바꾸셔도 됩니다.

controls.cpp의 내부가 어떻게 구성되어 있는지 봅시다.

# 실제 코드

우리는 몇 가지의 변수가 필요합니다.

``` cpp
// 위치

glm::vec3 position = glm::vec3( 0, 0, 5 );
// 수평각 : -z 방향
float horizontalAngle = 3.14f;
// 수직각 : 0, 수평방향
float verticalAngle = 0.0f;
// 초기 뷰의 범위
float initialFoV = 45.0f;

float speed = 3.0f; // 3 units / second
float mouseSpeed = 0.005f;
```

Fov는 줌의 정도입니다. 80&deg; = 매우 넓은 각도, 큰 변형. 60&deg; - 45&deg; : 표준, 20&deg; : 큰 줌.

처음에 우리는 입력에 따라  위치와 수평각, 수직각 그리고 FoV를 다시 계산하고, 위치,수평각,수직각 그리고 FoV에서  뷰와 프로젝션 행렬을 계산합니다. 

## 방향

마우스의 위치를 읽는것은 쉽습니다.

``` cpp
// 마우스의 위치를 가져온다.

int xpos, ypos;
glfwGetMousePos(&xpos, &ypos);
```

우리는 커서를 화면 중앙으로 되돌려 놓는것을 조심해야한다, 그렇지 않으면 윈도우 창 밖으로 마우스가 나가서 더이상 움직일 수 없다.

``` cpp
// 다음 프레임의 마우스 위치 재설정
glfwSetMousePos(1024/2, 768/2);
```

이 코드는 윈도우에서 1024*768이라고 가정해야 한다. 물론 이것이 필수적인 사항은 아니다.당신이 원한다면 giFwGetWindowSize를 사용해도 된다.

우리는 시야각을 계산할 수 있다.

``` cpp
// 새로운 방향을 계산한다.
horizontalAngle += mouseSpeed * deltaTime * float(1024/2 - xpos );
verticalAngle   += mouseSpeed * deltaTime * float( 768/2 - ypos );
```

오른쪽부터 왼쪽으로 읽어봅시다.

* 1024/2 - xpos 의미 : 윈도우의 중심으로부터 마우스가 얼마나 멀어져 있나? 이 값이 클수록 더 많이 돌려야 한다.
* float(...) 곱셈이 잘 되도록 부동 소수점을 수로 변환합니다.
* mousespeed는 마우스 속도를 높이거나 회전을 느리게 할 수 있습니다.이 기능을 원하는대로 미세 조정하거나 사용자가 선택할 수 있도록 해주세요.
* += : 만일 마우스를 움지깅지 않으면 1024/2-xpos는 0이되고, horizontalAngle +=은 0 horizontalAngle을 변경하지 않습니다. 대신 "=" 기호를 사용하면 각 프레임의 원래 방향으로 되돌아 가게 됩니다. 이는 별로 좋지 않습니다.  

우리는 실제 공간에서 우리가 보는 방향을 나타내는 벡터를 계산할 수 있다.

``` cpp
// 방향 : 구면 좌표를 데카르트 좌표로 변환한다.
glm::vec3 direction(
    cos(verticalAngle) * sin(horizontalAngle),
    sin(verticalAngle),
    cos(verticalAngle) * cos(horizontalAngle)
);
```

이것은 표준적인 계산이다, 하지만 당신이 코사인 사인을 모른다면, 간단히 설명해 드리겠습니다.

<img class="alignnone whiteborder" title="Trigonometric circle" src="http://www.numericana.com/answer/trig.gif" alt="" width="150" height="150" />

위의 수식은 3D로 일반화 된것이다.

우리는 상향  벡터를 안정적으로  계산하려고 한다. 상향은 항상 +Y 방향으로 향하는것은 아니다. 만일 당신이 밑을 보고 있다면, 상향 벡터는 수평이 됩니다. 다음은 동일한 위치, 동일한 타켓, 다른 높이의 카메라에 대한 예입니다.

![]({{site.baseurl}}/assets/images/tuto-6-mouse-keyboard/CameraUp.png)

우리의 경우 유일한 상수가 카메라의 오른쪽으로 향하는 벡터가 항상 수평이라는 것입니다.당신은 당신의 팔을 수평으로 놓고 어느방향으로든 아래를 위로 올려다보면서 확인할 수 있다.그래서 우리는 오른쪽 벡터를 정의 한다. 그것은 Y좌표는 수평이므로 0이고, X와 Z좌표는 위의 그림과 같지만 각도는 90&deg; 또 Pi/2라디안으로 회전합니다.

``` cpp
// 오른쪽 벡터
glm::vec3 right = glm::vec3(
    sin(horizontalAngle - 3.14f/2.0f),
    0,
    cos(horizontalAngle - 3.14f/2.0f)
);
```

우리는 오른쪽 벡터와 방향 또는 전위 벡터를 알고 있다.상향 벡터는 이 두개의 벡터에 수직인 벡터입니다.수학 식에 이것을 매우 쉽게하는 것이 있는데 그것은 외적이다.

``` cpp
// 상향 벡터 : 두 방향과 오른쪽에 대하여 수직이다.
glm::vec3 up = glm::cross( right, direction );
```

외적이 무엇인지 기억하는것은 매우 간단하다. Tutorial 3의 오른손의 법칙을 회상해보아라. 첫번째 벡터는 엄지이다. 두번째 벡터는 검지이다. 결과는 중지이다. 이것은 매우 편합니다.

## 위치

이 코드는 꽤 간단합니다.그런데 제가 위/아래/오른쪽/왼쪽 키를 awsd(키보드에서 알파벳 a,w,s,d) 대신에 사용한다. 왜냐하면 나의 키보드에서 실제로 awsd가 zpsd이기 때문이다.그리고 qwerZ 키보드와 한국 키도드도 다릅니다. 나는 한국사람들이 무슨 레이아웃을 했는지 알지 못 한다.그러나 나는 그 또한 다르다고 생각한다.

``` cpp
// 앞으로 전진
if (glfwGetKey( GLFW_KEY_UP ) == GLFW_PRESS){
    position += direction * deltaTime * speed;
}
// 뒤로 후진
if (glfwGetKey( GLFW_KEY_DOWN ) == GLFW_PRESS){
    position -= direction * deltaTime * speed;
}
// 바로 오른쪽
if (glfwGetKey( GLFW_KEY_RIGHT ) == GLFW_PRESS){
    position += right * deltaTime * speed;
}
// 바로 왼쪽
if (glfwGetKey( GLFW_KEY_LEFT ) == GLFW_PRESS){
    position -= right * deltaTime * speed;
}
```

유일하고 특별한것은 deltaTime(프레임을 생멸하는데 걸리는 시간을 뜻하는 함수?)이다.우리는 간단한 이유로 각 프레임을 1 단위로 움직이는것을 원치 않는다.

* 만일 당신이 빠른 컴퓨터를 사용하는 경우, 당신이 60 fps를 실행한다면, 1초에 60단위씩 움직일 것입니다.
* 만일 당신이 느린 컴퓨터를 사용하는 경우, 당신이 20 fps를 실행한다면, 1초에 20 단위씩 움직일 것입니다.

좋은 컴퓨터를 사용한다는것은 더 빨리 실행한다는 것은 아니기 때문에 당신은 마지막 프레인 시간이나 deltaTime만큼 거리를 조정해야 합니다.

* 만일 당신이 빠른 컴퓨터를 사용하고 60 fps로 실행한다면 1 프레임에서 1/60 속도 단위로 이동하므로 1초당 1속도가 됩니다.
* 만일 당신이 느린 컴퓨터를 사용하고 20 fps로 실행한다면 1초에 1/20 속도 단위로 이동하므로 1초 안에 1속도가 됩니다.

어떤것이 더 좋을까요. deltaTime은 매우 간단한 계산입니다.

``` cpp
double currentTime = glfwGetTime();
float deltaTime = float(currentTime - lastTime);
```

## 시야

재미를 위해 시야에 마우스 휠을 묶어서 우리는 저렴하게 확대/축소를 할 수 있다.

``` cpp
float FoV = initialFoV - 5 * glfwGetMouseWheel();
```

## 행렬 계산

이제 행렬을 계산하는것은 간단하다. 우리는 이전에 썼던 기능을 다시 사용하지만, 새로운 매개변수를 사용합니다.

``` cpp
//내젹 : 45&deg; 시야, 4:3 비율, 화면 범위 0.1에서 100단위
ProjectionMatrix = glm::perspective(glm::radians(FoV), 4.0f / 3.0f, 0.1f, 100.0f);
// 카메라 행렬
ViewMatrix       = glm::lookAt(
    position,           // 카메라는 여기입니다.
    position+direction, // 여기는 같은 위치에 플러스 방향
    up                  // Head를 위로 (0,-1,0으로 설정하면 거꾸로 보입니다.)
);
```

# 결과

![]({{site.baseurl}}/assets/images/tuto-6-mouse-keyboard/moveanim.gif)


## 후면 컬링

이제 우리는 자유롭게 움직일 수 있으므로, 당신이 큐브의 안쪽으로 이동하면,다각형은 여전히 표시된다는것을 알고 있을것입니다.이것은 명백하지만 이것은 최적화를 위한 기회를 줍니다. 실제로는 어플리케이션에서 큐브 안으로는 절대로 이동할 수 없습니다.

이 아이디어는 GPU가 카메라 삼각형의 뒤에 혹은 앞에 있는지 확인하도록하는 것입니다.만일 앞에 있다면 삼격형을 표시하세요. 메쉬가 닫혀 있고 메시가 내부에 있지 않다면 앞에 다른 삼각형이 있는것입니다. 아마 아무도 모를것입니다. 단 모든것이 더 빠를것입니다. 삼각형이 평균보다 두배는 없기때문이죠.

가장 좋은것은 이것을 쉽게 확인하는것입니다.GPU는 삼각형의 법선을 계산하고(외적을 이용하는것은 기억하는가?) 이 법선이 카메라를 향하고 있는지 확인합니다.

불행하게도 이것은 비용이 든다. 삼각형의 방향은 매우 함축적이다.이것은 당신의 버퍼에서 두개의 정점을 뒤집으면, 아마도 구명이 생기는 것을 의미합니다.그러나 일반저으로 약간의 추가작업을 할 가치가 있습니다. 종종 3D 모델러에서 "반전 법선"을 클릭하기만하면 됩니다.(실제로 정점을 뒤집어서 법선을 뒤집습니다.)그리고 모든것이 잘 됩니다.

후면 컬링을 가능하게 하는것은 쉽습니다.

``` cpp
// Cull triangles which normal is not towards the camera
// 법선이 카메라쪽으로 향하지 않는 컬 삼각형
glEnable(GL_CULL_FACE);
```

# 연습

* verticalAngle을 뒤집어 놓을 수 없도록 제한합니다.
* 물체 주위를 회전하는 카메라를 만듭니다.(positio=ObjectCenter + (radius *cos(time), height, radius * sin(time) ) ); radius/height/time을 마우쓰 도는 키보드에 설정합니다.
* 재미있게 해보세요.
