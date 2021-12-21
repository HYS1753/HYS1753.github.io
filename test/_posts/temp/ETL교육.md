Data stage(iBM)
ETL(추출 변환 적재)

데이터 변환 및 통합하는 소프트 웨어 

web console
desinger client: 가장 많이 사용됨. 
director client
administrator client

# administration clinet

프로젝트에 대한 추가 삭제 설정 커멘드 수행 등등
프로젝트 관리 설정

general project 탭

프로젝트 : 

job : server job / parallel(PX) Job
    서버 : 단일
    병렬 : 멀티 커넥션 =

batch : job sequencer
    job 들의 배치 그룹화

project : 추가 삭제 설정
    업무의 하나 여러개의 배치를 추가 삭제 설정 한다.

잡이 4000개 이상 넘어가면 디렉터리 설정상 더이상 만들어지지 않는 문제 있음


프로젝트의 properties에서 다음과 같은거 확인
- general - enviroment 중요
  - 어떠한 파라미터를 만들어 작업을 추가할 때 사용자 정의 함수 정의시 사용.
  - User Define 가장 상위에서 만들을 환경을 설정 해 줌.
  - parameter set에서 여러 사람 같이 사용 가능
- logs - auto-purge of job log -> 자동 삭제 (플러싱) 로그 관리
  - 데일리 혹은 30일 정도


# designer client

- 잡과 컨테이너 생성, 잡의 파라미터 링크 등등
- 잡 properties 설정
- 컴파일 및 수행, 백업 등의 기능 수행
- administrter에서 생성한 프로젝트가 시작할 떄 선택할 수 있다.

- 드래그 엔 드롭을 통해 서 설정 할 수 있게끔 되어 있다 
- standard, canvas, palette, 등등

## standard 
    - job properties 아이콘 : 노란색 아이콘 자주 사용될 예정 (컨트롤 + J)
    - compile, Run  아이콘 : 
    - Help 아이콘 : 기능에 대한 의문은 help로 확인 필요


## palette
    - DB, FIle, Processing 만 주로 사용
    - 각 요소 중에 connector라고 적혀 있는 것들리 중요하다.
    - 디자이너 색깔만 봐도 잡의 상태 알 수 있음(빨강 - 병렬, 노랑  등등0
- 
## job properties
  - general
    - 서브 루틴 확인
    - 작업이 수행이 되면 작업에 대한 결과로 다른거 활용
  - parameter
    - 상위에서 만든 파라메터 사용하겠다 선언하는 부분
    - projectdefault 사용
## Export
  -  백업은 소중하다
  -  백업할 때는 디자인만 하는 것이 좋다. 실행파일 같이 하면 용량이 좋다. 
  -  dsx Type으로 백업 (export) 한다.
  -  분석용으로는 excel로 지정하는 게 좋음

## Import 
  - all 또는 특정 부분만

### 사용방법 
palette에서 스테이지 선택 후 link 로 연결한다. (디자인의 시작)
각각 스테이지를 더블클릭 또는 우클릭_properties 에서 원하는방향으로 설정
1. file 등에 따른 구조 확인
2. 컬럼에 대한 정의 length 타입 등등이 있다.
3. 개발시 쏠쏠 한 내용 찍 
   1. 각 스테이지= 아이콘을 복사하면 같은 프로젝트 내에서 붙여넣기로 설정값 복사 가능
   2. 개발 할 떄는 일반적으로 다 정의도어져 있는 프로젝트 =Copy해서 개발 한다. 시간 비용 줄이기 위해 
   3. tablle difiineition 
   4. 테이블 정보또한 바로 옭겨오기 가능 (copy 기능 활용해서)


# Derector client
- validate start stop reset job log 스케줄 등등을 수행...
스캐줄 정리 로그 확인 정도가 역할의 전부 

디렉터ㅘ 디자이너 왔다 갔다 하면서 확인한다.
활용도 적음


웹 
디자이너 -> tools -> reporting 뭐시기 하면 브라우저 뜸 여기서 
콘솔 뒷수분을 지워 버리고 
http: ~~ ds/console/ 로하게 되면 페이지를 볼 수 있다. 

웹으로 한눈에 보고 실행까지 가능 하다.
