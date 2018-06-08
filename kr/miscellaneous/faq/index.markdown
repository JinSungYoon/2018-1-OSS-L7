---
layout: page
status: publish
published: true
title: FAQ
date: '2012-04-29 13:22:31 +0200'
date_gmt: '2012-04-29 13:22:31 +0200'
categories: []
order: 10
tags: []
language: kr
---

transrated by kim jong min from sungkyunkwan univ.

# 메일 전송과 관련하여...

contact@opengl-tutorial.org으로 이메일을 보내는 것은 도움을 받을 수 있는 가장 효과적인 방법입니다.

만약, 여러분이 문제가 있다면, 다음을 정보들을 최대한 상세히 메일에 적어서 보내주세요.

* OS : 윈도우 xp ? Gentoo ? (주의사항: 맥 사용자라면 2.1 port를 사용할 것)
* 32 비트 환경인지 64비트 환경인지?
* 그래픽 카드 : NVIDIA ? AMD ? Intel ? S3 ? Matrox ? (주의사항 : 내장 GPU를 사용하는 경우에는 2.1 port를 사용하세요!)

... 그리고 여러분이 유용하게 쓰일 것 같다고 생각하는 정보들도 같이 보내주세요.
예를 들면:

* GPU Driver version
* Call stack
* screenshots
* console output
* minidump...

그리고 다음의 FAQ를 먼저 읽어주세요.


# tutuorials이 컴파일이 안돼요.

* Tutorial 1을 확실히 읽어주세요. 프로젝트를 다시 생성하는 대신에 CMake를 이용해 주세요. 또는 다음 링크를 읽어주세요.[Building your own C application](http://www.opengl-tutorial.org/miscellaneous/building-your-own-c-application/).
* 먄약 AssImp library와 관련된 에러가 발생하였다면, 빠른 시일 내에 수정될 것입니다.; 이 에러는 오직 하나의 tutorial에만 영향을 미치며, 다른 것들은 정상적으로 빌드 될 것입니다.
* 먄약 AntTweakBar library와 관련된 에러가 발생하였다면, 빠른 시일 내에 수정될 것입니다.; 이 에러는 오직 하나의 tutorial에만 영향을 미치며, 다른 것들은 정상적으로 빌드 될 것입니다.
* 문제가 생기면, 저희에게 주저하지 말고 메일을 보내주세요.

# tutorials를 컴파일 완료하였는데, 동작하지 않습니다. 왜 그럴까요?

여러가지 가능한 원인들:

## 호환될지 않는 GPU/OS

Intel card를 가지고 있는지 확인해주세요. glewinfo, GPU Caps viewer, 또는 다른 도구를 통해서 확인할 수 있습니다.


최근 HD4000을 제외한 Intel cards는 OpneGL 3.3.을 지원하지 않고, 대부분은 OpenGL 2.1.을 지원합니다. 사용자는 2.1. version을 다운로드 해야 합니다.

다른 가능성은 사용자 환경이 Mac, pre-Lion version인 경우입니다.

## 잘못된 작업 폴더


사용자 분들께서 잘못된 작업 폴더에서 실행시켰을 가능성이 있습니다. .exe을 탐색기에서 더블 클릭 해주세요.


IDE 설정을 위해서 튜토리얼1을 확인하시면 디버깅 할 수 있을 겁니다.


.exe 파일이 *build* 디렉토리에 컴파일 되고, 자동으로 *source* 디렉토리에 복사된다는 것을 주목하세요. 그러면, 그 파일들은 필요한 이미지, 3D 모델들, shaders과 같은 것을 찾을 수 있을 것입니다.


## VAO가 없을 때

프로그램을 처음부터 새로 만드실 때는, VAO를 생성하셨는지 확인하여야 합니다.:

```
GLuint VertexArrayID;
 glGenVertexArrays(1, &VertexArrayID);
 glBindVertexArray(VertexArrayID);
```

## GLEW 버그

GLEW 는 핵심 내용을 사용하지 못하게 만드는 버그를 포함하고 있다. (수정된 tutorials의 소스코드를 사용하는 경우를 제외하고)

3가지 해결 방법:



* GLFW에 Compatibility Profile을 요청한다:

```

glfwOpenWindowHint(GLFW_OPENGL_PROFILE, GLFW_OPENGL_COMPAT_PROFILE);
```

* glewExperimental을 이용한다.; 가장 추천하는 방법이다:

```

glewExperimental = true;
```

* glew를 수정하기 위해서는 다음을 참고하라. [this patch](http://code.google.com/p/opengl-tutorial-org/source/browse/external/glew-1.5.8.patch?name=0009_33).


## CMake


확실히 Tutorial 1을 정독 하였는가? 자신만의 고유한 makefile를 만들고 모든것을 스스로 빌드하였는가?


# 어떤 버젼의 OpenGL을 사용하는 것이 좋을까?

사실은, 저희는 OpenGL 3 또는 그 이상의 버전을 애플리케이션을 위해서 추천하지 않습니다. 저희는 그 버전을 이 튜토리얼에 사용하였는데 이를 통해 **깔끔하게** OpenGL을 배울수 있기 때문입니다. 그리고 이미 3.3을 사용하는 방법을 알기 때문에 2.1 버전을 사용하시는 것을 쉬울 것 입니다.


저희가 추천 드리는 것은 :

* OpenGL 3.3으로 배우시면, 올바른 사용법을 익힐 수 있습니다.
* 여러분의 애플리케이션을 위한 대상 하드웨어를 셋팅 하세요. 예를 들면,  *require *FBOs and GLSL.
* 모든 확장파일을 로드 하실때 GLEW를 사용하세요.
* 여러분들이 버전 3.3을 사용하고 계시다면, 약간의 변경이 있을수 있지만, 대부분을 코딩하실 수 있습니다.
* 만약 여러분이 예전의 하드웨어를 다루고 싶다면, FBOs를 요청하는 효과를 무효화 함으로써 여전히 다룰 수 있습니다.  

<div><span style="font-size: medium;"><span style="line-height: 24px;">여러분들이 버전 4.2와 같은 가장 최신의 버전을 사용하고자 하는 상황이 있을 수 있습니다.: 당신이 최신 연구를 하는 대학원 생이면, 정말로 최신 기술이 필요할 것이며, 그리고 호환성은 신경쓰지 않을 것입니다. 왜냐하면 당신의 소프트웨어는 연구실 이외의 환경에서는 작동할 필요가 없으니까요. 이런 경우에, 망설이지 마시고 당신의 하드웨어가 지원하는 최신 버전의 OpenGL을 사용하세요.</span></span></div>

# 어디서 OpenGL 3을 다운로드 할 수 있을 까요?

불가능 합니다.


윈도우 환경에서는, 대신에, OpenGL 1.1을 위한 opengl32.dll을 이용할 수 있습니다. 거기에는 wglGetProcAddress() 라는 함수가 있는데, opengl32.dll에서 직접 구현되지 않는 함수를 불러오는 것을 가능하게 합니다. 이것을 driver에서 사용할 수 있습니다.


GLEW 는 wglGetProcAddress를 모든 필요한 symbols에 호출하며, 그것들을 여러분이 사용가능하게 합니다. (스스로 할 수 있으시겠지만 매우 지루한 작업이 될 수 있습니다.) 그것을 또한 10년 전에는 존재하지 않던 상수들을 선언할 수 있게 합니다. 예를 들면, GL_VERTEX_ATTRIB_ARRAY_DIVISOR_ARB.

그래서, 여러분의 GPU driver가 필요한 버전을 지원하는지 확인하고, GLEW를 사용하세요. 그러면 충분합니다.



# VAO를 각 튜토리얼에 생성하였는데 한번도 사용하지 않은 이유가 무엇인가요?


아닙니다. VAO는 실행시간 동안 사용되고 있습니다.

VAOs는 VBOs를 감싸고 있습니다. 이들은 어떤 버퍼가 어떤 속성이나 다양한 것에 속해있는지 기억합니다. glDrawArrays/Elements()전에 이것은 OpenGL 호출 수를 감소 시킵니다. OpenGL 3 Core 이후로, 이것들은 필수적입니다. 하지만 여러분은 딱 한번만  사용하고 꾸준히 그것들을 수정할 것 입니다. (튜토리얼 에 포함된 것)

VAOs이 초보 사용자 분들에게는 혼란수러울 수 있습니다. 그리고 OpenGL 2(개발을 위해서 가장 추천하는 버전, 관련 FAQ를 보세요)와 동일한 것이 없습니다. 그리고 성능 향상이 불분명 합니다. 만약 여러분이 VAOs에 관심이 있다면, OpenGL의 wiki를 확인해 주세요. 이것은 *아마도* 조금이나마 여러분들의 애플리케이션을 간단히 하고 and *아마도* 조그마한 성능향상을 보여줄수 있습니다. 그러나 항상 그런것은 아닙니다.
<div><span style="font-size: medium;"><span style="line-height: 24px;">
</span></span></div>

# "Unable to start program ALL_BUILD"라는 에러가 발생합니다.

ALL_BUILD는 CMake에 의해 생성되는 도움을 주는 프로젝트이지 실제 프로그램은 아닙니다.

튜토리얼1에서 한 것 처럼, 여러분은 실행시키고 싶은 프로젝트를 오른쪽 더블클릭 함으로써 선택하여야 하고, "Set up as startup project"를 선택해야 합니다. 다음과 같이:

![]({{site.baseurl}}/assets/images/faq/StartupProject.png)


# I've got a message about the working directory, and the program crashes.
# 작업폴더와 관련된 메시지가 나오면서, 프로그램 충돌이 발생합니다.

tutorial01_first_window/, tutorial02_red_triangle/, etc에 있는 프로그램을 실행시켜야 합니다. 만약 여러분이 자신만의 IDE를 사용하고 계시다면, 앞서 말씀드린 프로그램이 실행이 되도록 설정해주어야 합니다.


더 자세한 사항을 위해서는 튜토리얼 1을 읽어 주세요.

