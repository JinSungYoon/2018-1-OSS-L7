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

<div><span style="font-size: medium;"><span style="line-height: 24px;">There's one big situation where you might want to use a very recent version, say 4.2 : you're a graduate student doing high-end research, you really need a recent feature, and you don't care about compatibility because your software will never be ran outside your lab. In this case, don't waste time and go straight to the highest OpenGL version your hardware supports.</span></span></div>
<div><span style="font-size: medium;"><span style="line-height: 24px;">여러분들이 버전 4.2와 같은 가장 최신의 버전을 사용하고자 하는 상황이 있을 수 있습니다.: 당신이 최신 연구를 하는 대학원 생이면, 정말로 최신 기술이 필요할 것이며, 그리고 호환성은 신경쓰지 않을 것입니다. 왜냐하면 당신의 소프트웨어는 연구실 이외의 환경에서는 작동할 필요가 없으니까요. 이런 경우에, 망설이지 마시고 당신의 하드웨어가 지원하는 최신 버전의 OpenGL을 사용하세요.</span></span></div>

# Where do I download OpenGL 3 ?

You don't.

On Windows, for instance, you only have opengl32.dll, which is only OpenGL 1.1. BUT there is this function, wglGetProcAddress(), which makes is possible to get functions that are not implemented directly in opengl32.dll, but which are available in the driver.

GLEW calls wglGetProcAdress on all needed symbols, and make them available to you. (you can do it yourself but it's horribly boring). It also declares new constants which did not exist 10 years ago, like, for instance, GL_VERTEX_ATTRIB_ARRAY_DIVISOR_ARB.

So, just make sure your GPU driver supports the needed version, use GLEW, and you're good to go.



# Why do you create a VAO in each tutorial, but you never use it ?


Wrong. It's bound, so in fact, it's used during the whole execution.

VAOs are wrappers around VBOs. They remember which buffer is bound to which attribute and various other things. This reduces the number of OpenGL calls before glDrawArrays/Elements(). Since OpenGL 3 Core, they are compulsory, but you may use only one and modify it permanently (which is what is done in the tutorial).

VAOs may be confusing for this beginner's tutorial, and there is no equivalent in OpenGL 2 (which I recommend for production, see related FAQ), and the performance benefits are not clear. If you're interested in VAOs, please have a look at OpenGL's wiki. It *may* slightly simplify your application and *may* increase the performance a tiny bit, but not always.
<div><span style="font-size: medium;"><span style="line-height: 24px;">
</span></span></div>

# I've got error "Unable to start program ALL_BUILD"

ALL_BUILD is just a helper project generated by CMake; it's not a real program.

As stated in Tutorial 1, you have to select the project you want to run by right-clicking on a project (from inside Visual) and select "Set up as startup project", like this :

![]({{site.baseurl}}/assets/images/faq/StartupProject.png)




# I've got a message about the working directory, and the program crashes.

You have to start the program from tutorial01_first_window/, tutorial02_red_triangle/, etc. If you start the program from your IDE, you have to configure it from him to do so.

Please read Tutorial 1 for details.

