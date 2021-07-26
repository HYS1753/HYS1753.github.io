---
author_profile: true
date: 2021-07-00
title: "AWS S3 정적 웹호스팅"
categories: 
    - AWS
tag: 
    - S3
    - 정적 웹호스팅

# 목차
toc: true  
toc_sticky: true 
# sidebar:
#  nav: "docs"
---

# Simple Storage Service(S3)를 사용해 정적 웹호스팅 하기

---

정적 웹 페이지란 말 그대로 서버에 미리 저장된 파일이 그대로 사용자에게 전달되는 웹페이지 입니다. 

때문에 서버에 저장된 파일이 변경되지 않는 한 사용자는 고정된 웹 페이지를 보게 됩니다.

반면 동적 웹 페이지의 경우 사용자의 요청, 시간 등에 따라 피이지가 변경되어 보여지게 되는 웹 페이지 입니다.

AWS S3는 따로 서버를 설정할 필요없이 정적 웹 페이지를 호스팅 할 수 있는 기능이 있습니다. 

웹페이지는 직접 만들 수 도 있지만 이번에는 간단하게 부트스트랩에서 제공하는 무료 템플릿을 통해 정적 웹 호스팅을 해보도록 하겠습니다.

​

## 1. 부트스트랩 페이지 방문하기

- [부트스트랩](startbootstrap.com)
![bootstrap 1](/assets/images/AWS57.png){: .align-center}
- 위의 부트스트랩 페이지에 접속하면 다양한 템플릿을 확인할 수 있으며 여기서 원하는 무료 템플릿을 선택한 후 저장 합니다. 
- 부트스태랩 무료 템플릿 안의 파일
![bootstrap 2](/assets/images/AWS58.png){: .align-center}
- 저장한 zip파일을 압축해제 하면 위와 같은 파일이 들어있는 것을 확인 할 수 있습니다.

## 2. 부트스트랩 파일 S3에 업로드 하기

- S3 버킷을 생성합니다. 
- 생성을 안했을 경우 이전 포스트의 S3 버킷 만들기를 통해 만들면 됩니다.
- 부트스트랩에서 받은 무료 템플릿을 압축 헤제한 파일들을 그대로 S3 버킷에 저장합니다. 업로드 버튼 또는 드래그 엔 드롭으로 가능합니다.

![bootstrap 3](/assets/images/AWS59.png){: .align-center}
- 위와 같이 부트스트랩에서 받은 파일 그대로 S3 버킷에 넣어줍니다.

## 3. S3 버킷 설정하기

![bootstrap 4](/assets/images/AWS60.png){: .align-center}
- 생성한 버킷에 속성 탭을 눌러보면 위와 같은 버킷의 속성을 확인 할 수 있습니다. 이중 정적 웹 사이트 호스팅을 활성화 시켜 줍니다.

![bootstrap 5](/assets/images/AWS61.png){: .align-center}
- 정적 웹사이트 호스팅에서 "이 버킷을 사용하여 웹 사이트를 호스팅합니다."에 체크하고 저장합니다.

- 여기서 인덱스 문서는 정적 웹사이트에서 첫화면이 될 html파일을 지정하는 것으로 S3에 업로드한 파일중 index.html이 첫페이지이므로 인덱스 문서에 index.html이라고 입력한 후 저장을 눌러줍니다.
​
## 4. 정적 웹 사이트 접속하기

- 다시 S3 개요 탭으로 가서 index.html파일을 클릭하면 아래와 같은 정보가 나오게 됩니다.

![bootstrap 6](/assets/images/AWS62.png){: .align-center}

- 여기서 객체 URL이 정적 웹사이트의 주소가 됩니다. 하지만 지금 상태로 접속을 시도해 보면 
![bootstrap 7](/assets/images/AWS63.png){: .align-center}

- 위와 같은 에러가 발생하게 됩니다. 이는 버킷의 퍼블릭 엑세스 권한을 주지 않아 Accecc denied된것 입니다. 따라서 버킷에 업로드한 파일 전부의 엑세스 권한을 변경해 주어야 합니다.
![bootstrap 8](/assets/images/AWS64.png){: .align-center}

- 우선 S3 버킷의 권한 탭에서 모든 퍼블릭 엑세스 차단을 비활성화 시켜 줍니다.
![bootstrap 9](/assets/images/AWS65.png){: .align-center}

- 다음으로, 위의 사진과 같이 버킷 개요에 있는 파일을 전체 선택하고 작업에서 퍼블릭으로 설정을 설정해 모든 파일에 읽기 권한을 줍니다. 완료 후 다시 index.html의 URL을 클릭하면 정적 웹 호스팅된 페이지를 확인할 수 있습니다.
![bootstrap 10](/assets/images/AWS66.png){: .align-center}

- 또한 index.html의 URL주소만을 통해서도 외부에서 접속할 수 있게됩니다.