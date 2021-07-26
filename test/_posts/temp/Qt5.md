---
author_profile: true
date: 2021-07-00
title: "Qt LineEdit"
categories: 
    - Qt
tag: 
    - Line Edit 

# 목차
toc: true  
toc_sticky: true 
# sidebar:
#  nav: "docs"
---

# LineEdit 활용하기

---

Qt에서 제공하는 LineEdit에는 다양한 기능이 있다.

lineEdit는 출력도 할수 있지만 입력용으로 많이 사용된다.

이번에는 validator와 Mask에 대해 알아본다.


##  QValidator

- QValidator는 입력을 제한할 수 있는 기능을 가지고 있다.

- 예를 들어 0~100 까지의 정수형만 입력가능하게하고 싶을 경우 QValidator를 사용할 수 있다.

- 먼저 ui는 아래와 같이 lineEdit 2개를 만들어 본다. (ui 만드는 방법은 이전 포스트에 있습니다.)

![UI 설정](/assets/images/Qt22.png){: .align-center}


- lineEdit를 만들고 위의 linEdit 우클릭을 통해 slot을 만들어 준다.

- 저번에는 Clicked() 를 사용했지만 이번에는 엔터를 누르면 수행이되는 returnPressed()를 사용해 본다.

- MainWindow.cpp에 아래와 같이 입력한다.
    ```c
    #include "mainwindow.h"
    #include "ui_mainwindow.h"

    MainWindow::MainWindow(QWidget *parent)
        : QMainWindow(parent)
        , ui(new Ui::MainWindow)
    {
        ui->setupUi(this);
       // 정수형 0~100까지만 입력가능한 QValidator 정의
        QValidator *validator = new QIntValidator(0, 100, this);
        // lineEdit에 validator 설정
        ui->lineEdit->setValidator(validator);
    }

    MainWindow::~MainWindow()
    {
        delete ui;
    }

    void MainWindow::on_lineEdit_returnPressed()
    {
         ui->lineEdit_2->setText(ui->lineEdit->text());
    }
    ```

- 입력 후 실행해 보면 0 ~ 100 이내의 숫자는 입력 후 엔터 시 lineEdit_2에 표시가 되지만 이외의 숫자는 출력이 안되는 것을 확인할 수 있다.

![100 이상의 숫자는 출력이 안된다.](/assets/images/Qt23.png){: .align-center}

- 더욱 자세한 QValidator의 기능은 [Qt Documentation](doc.qt.io)를 확인한다.

---

##  Mask

- 이번에는 lineEdit에 입력을 제한하는 방법을 알아본다.

- UI는 1번과 동일하게 생성하고, MainWindow.cpp에 다음과 같이 코드를 입력한다.
    ```c
    #include "mainwindow.h"
    #include "ui_mainwindow.h"

    MainWindow::MainWindow(QWidget *parent)
        : QMainWindow(parent)
        , ui(new Ui::MainWindow)
    {
        ui->setupUi(this);
        // Mask 설정
        ui->lineEdit->setInputMask("000");
    }

    MainWindow::~MainWindow()
    {
        delete ui;
    }

    void MainWindow::on_lineEdit_returnPressed()
    {
        ui->lineEdit_2->setText(ui->lineEdit->text());
    }
    ```

- 실행해 보면 다음과 같이 숫자 3자리만 입력 가능 한 것을 확인할 수 있다.
![setInputMask](/assets/images/Qt24.png){: .align-center}

- 이 Mask를 살짝응용하면 다음과 같이 IP주소와 같은 입력을 수행할 수 있다.
    ```c
    ui->lineEdit->setInputMask("000.000.000.000");
    ```

![IP Mask](/assets/images/Qt25.png){: .align-center}

- 더욱 자세한 Mask의 기능은 [Qt Documentation](doc.qt.io)를 확인한다.