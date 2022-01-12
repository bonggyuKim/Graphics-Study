# OpenGL

#### Chapter 1. Introduction to OpenGL

 OpenGL은 그래픽 hardware의 기능에 접근하기 위한 API이다.

 OpenGL은 3D object를 묘사하는 기능을 제공하지 않는다. 대신 너가 직접 point, lines, triangles, patches를 이용하여 구성해줘야 한다.

  List that OpenGL perform to render an image

- OpenGL의 기하학적 원형으로부터 데이터를 지정한다
- Rendering attributes를 결정하기위해 다양한 셰이더를 실행하여 input값에 대한 계산을 한다.
- Rasterization을 해준다
  - Rasterization은 input값의 수학적 표현을 fragment로 바꿔준다
- Rasterization을 통해 나온 각각의 fragments들의 fragment shader를 실행한다
- blending 같은 추가적인Per-fragment 기능을 제공한다

![스크린샷(4)](C:\Users\sharm\Pictures\Screenshots\스크린샷(4).png)