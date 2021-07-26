---
author_profile: true
date: 2021-07-00
title: "Qt 프로젝트 생성"
categories: 
    - Qt
tag: 
    - Qt widget application

# 목차
toc: true  
toc_sticky: true 
# sidebar:
#  nav: "docs"
---

# Qt 프로젝트 생성

---
Qt를 설치 완료하였으면, 본격적인 Qt 사용방법을 알아봅니다. 

Qt widget application 및 C++을 사용해 윈도우 프로그램을 작성합니다.


![Qt Creator 메인화면](/assets/images/Qt6.png){: .align-center}
1. Qt Creator 메인 화면에서 좌측 상단의 New Project 클릭

![프로젝트 생성 1](/assets/images/Qt7.png){: .align-center}
2. 자신이 사용할 Qt 템플릿을 선택합니다.
- Qt widget application은 c++을 사용한 ui기반 프로젝트이고, 
- Qt for Python은 Pyqt를 사용하는 템플릿, Qt quick apllication은 Qml기반 프로젝트 입니다

![프로젝트 생성 2](/assets/images/Qt8.png){: .align-center}
3. choose를 통해 다음화면으로 오면 프로젝트의 이름과 저장경로를 설정합니다.

![프로젝트 생성 3](/assets/images/Qt9.png){: .align-center}
4. 다음 화면에서 빌드 시스템을 설정해 줍니다. qmake, cmake 등이 있지만, 기본으로 qmake를 본 블로그에서는 사용합니다.

![프로젝트 생성 4](/assets/images/Qt10.png){: .align-center}
5. 다음 화면에서는 생성하고자 하는 프로젝트의 class를 설정합니ㅏㄷ.
- Qt widget application에서는 기본적으로 `Main`, Gui를 설정하는 `.ui` file, ui를 제어하는 `MainWindow`로 구성됩니다.

![프로젝트 생성 5](/assets/images/Qt11.png){: .align-center}
6. 다음 화면에서는 사용할 Kit를 설정합니다. Kit 안에는 C, C++ 컴파일러 및 디버거에 관련한 설정이 있습니다. 자신의 프로젝트에 사용할 Kit을 선택합니다.

![프로젝트 생성 6](/assets/images/Qt12.png){: .align-center}
7. 만약 위의 화면에 아무것도 표시 안되거나 MSVS(비주얼스튜디오)가 안나오는 경우 MS VisaulStudio를 설치해 줍니다.
- Qt 메인 화면에서 툴바의 Tools -> Options -> Kits -> Kits 에서 add를 통해 위의 그림과 같이 설정해 줍니다.
- 비주얼 스튜디오의 버전에 따라 컴파일러 및 디버거의 위치는 상이할 수 있습니다.

![프로젝트 생성 7](/assets/images/Qt13.png){: .align-center}
8. 마지막 단계에서는 설정한 클래스의 값들을 확인하고 Finish를 통해 프로젝트를 생성합니다.

![프로젝트 생성 8](/assets/images/Qt14.png){: .align-center}
9. 프로젝트를 생성하면 기본적으로 위와 같은 C++ 클래스들이 자동으로 생성됩니다.