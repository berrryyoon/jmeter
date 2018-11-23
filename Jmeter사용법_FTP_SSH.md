[TOC]

<br><br><br><br><br><br><br><br><br><br><br><br>

# 1. JMeter

## 1-1. JMeter 버전
* Binaries : JMeter를 변경없이 사용하는 버전
* Source : Jmeter에 소스 코드를 수정 또는 추가하거나 컴파일해서 사용하는 버전

## 1-2. JMeter 다운로드
* https://jmeter.apache.org/download_jmeter.cgi

## 1-3. 실행 환경
현재 JMeter의 최신 버전으로는 Java 8이나 9가 필요함. JMeter는 순수 자바 애플리케이션임.

## 1-4. 설치
별도의 설치 과정 없이 압축 파일을 풀고 apache-jmeter-2.xx/bin 의 디렉터리에 있는 시작 명령어만 실행하면 바로 구동가능.

## 1-5. 실행
아래 경로에서 jmeter.bat 실행. 실행하면 cmd 창이랑 애플리케이션 뜸

```
C:\각자 설치한디렉토리 아래\apache-jmeter-5.0\apache-jmeter-5.0\bin
```
----------

# 2. JMeter FTP 테스트

## 2-1. Thread Group 생성

- #### 의미
  Add one Thread Group, which is placeholder for all other elements like Samplers, Controllers, and Listeners.

- #### 생성 방법
  - 왼쪽 Test Plan > 마우스 우클릭 > ADD > Threads > Tread Group 선택.

![화면 캡쳐640](/assets/화면%20캡쳐640.png)


- #### 옵션 설명
  * **Name**
  그룹 이름.
  * **Action to be taken after a Sampler error**
  실행 중 에러 발생시 어떻게 처리할 것인지에 대한 선택.
  * **Thread Properties**
  루핑 도는 방법 설정.
      - Number of Threads(users)
        한번에 몇 개의 쓰레드를 생성할 것인지, 즉 동시에 몇 명의 가상 사용자로 테스트할 것인지에 대한 설정값이다. 일반적으로 이 값이 커질수록 서버에 대한 부하는 커지게 된다.
        *테스트할 사용자의 수*
      - Ramp-up Period(in seconds)
        한번의 실행을 몇초동안 완료시킬 것인지 나타내는 값. 즉, 서서히 사용자가 증가하는 역할을 하고자 할때 쓰임.
        *희망 테스트 시간(1이면 거의 모든 스레드가 동시 실행)*
      - Loop Count
        반복하고자 하는 횟수.
        *한 스레드당 반복 횟수*
      - Scheduler를 이용하여 예약 실행도 가능
- #### 설정한 옵션
    ```
    Name : FTPusers
    Number of Threads (users) : 4
    Ramp-up Period : 1                //leave the default value of 1 seconds.
    Loop count : 10
    ```


*예를 들어, Number of Thread: 4, Ramup-up Period:1, Loop Count:10"이면 0.25(1/4)초 간격으로 4개의 쓰레드가 가동되고 10번을 반복 실행하라는 의미.*

![화면 캡쳐639](/assets/화면%20캡쳐639.png)



## 2-2. Add Sampler
- #### 의미
  Now that we have defined our users, it is time to define the tasks that they will be performing. Add FTP Request elements. We add two FTP request elements, one which retrieves a file and the other which puts a file on the ftp site.

- #### 생성 방법
  FTP Users > 마우스 우클릭 > ADD > Sampler > FTP request

  ![화면 캡쳐636](/assets/화면%20캡쳐636.png)

- #### 옵션 설명
  * **Name**
  Descriptive name for this sampler that is shown in the tree.
  * **Server Name or IP**
  Domain name or IP address of the FTP server.
  * **Port**
  Port to use. If this is >0, then this specific port is used, otherwise JMeter uses the default FTP port.
  * **Romote File**
    - *다운로드할 파일*
    - File to retrieve or name of destination file to upload.  
    - the path to the file on the FTP server, which will be **downloaded** or used as a destination path for a file upload
  * **Local File**
    - *업로드할 파일*
    - File to upload, or destination for downloads (defaults to remote file name).
    - the path to the file on the local file system (where JMeter is running), which will be **uploaded** to the FTP server or to where the remote file is stored locally
  * **Local File Contents**
  in case of a file upload request, the source file content can be provided via this input field (can be used instead of the “Local File”, overrides “Local File” input)
  * **get(RETR) / put(STOR)**
  Whether to retrieve or upload a file.
  * **Use Binary mode?**
  Check this to use Binary mode (default ASCII)
  * **Save File in Response?**
  Whether to store contents of retrieved file in response data. If the mode is ASCII, then the contents will be visible in the View Results Tree.
  * **Username**
  FTP account username.
  * **Password**
  FTP account password. N.B. This will be visible in the test plan.

- #### 설정한 옵션
  * **get 설정** (파일 다운로드)
  ```
  Name : FTP Request get
  Server Name or IP :                   //서버 주소
  Remote File : /home/testftp.txt        //다운로드할 파일 경로
  Local File : -                        //빈칸
  Select get(RETR)
  Tick Fave file in Response
  Username : root
  Password : ~~~~~~
  ```
  * **put 설정** (파일 업로드)
  ```
  Name : FTP Request put
  Server Name or IP :                   //서버 주소
  Remote File : test.txt                //업로드할 파일 생성
  Local File : -                        //빈칸
  Local File Contents : This is created by Jmeter at ${__time(hh:mm:ss dd/MM/yyyy,)}
  Select put(STOR)
  Username : root
  Password : ~~~~
  ```
## 2-3. Add Listeners
- #### 의미
  The final element you need to add to your Test Plan is a Listener. This element is responsible for storing all of the results of your FTP requests in a file and presenting a visual model of the data.

- #### 생성 방법
  FTP users > 마우스 우클릭 > Add > Listener > View Results Tree.

## 2-4. Run the Test plan
  Now save the above test plan as ftpsite_test.jmx.
- #### 생성 방법
  - 좌측 상단 메뉴 Run > start
  - 혹은 중간쯤에 있는 Start 버튼 클릭

## 2-5. 확인
 ![화면 캡쳐637](/assets/화면%20캡쳐637.png)
- User에서 Thread 4개 설정했으므로 put 4개, get 4개
- 상세 정보 확인 : 'FTP Request get' 클릭 > Sampler result 등등 카테고리 클릭
- The retrieved file for GET request is stored in the /bin folder.
In our case, it is /home/manisha/apache-jmeter-2.9/bin/.
*(사실 아직 어느 경로로 다운로드 되는지 찾지 못했습니다~! 우선 설명은 이렇다고 하는데 저는 왜 안보이는지 모르겠어요 ^^...)*
- For PUT request, the file is uploaded at the path ftp:서버주소/root.


----------
# 3. JMeter SSH 테스트
[참고] https://github.com/yciabaud/jmeter-ssh-sampler
## 3-0. SSH Sampler 설치
* 문서 최하단에 참고 1. Plugin Manager 추가를 참고하여 Plugins Manager 설치
* Available Plugins 에서 'SSH Protocol Support' 선택
* Apply Changes and Restart JMeter 선택
![화면 캡쳐643](/assets/화면%20캡쳐643.png)
* 자동으로 Jmerter 재시작

## 3-1. Thread Group 생성
- #### 생성 방법
  - 왼쪽 Test Plan > 마우스 우클릭 > ADD > Threads > Tread Group 선택.

![화면 캡쳐642](/assets/화면%20캡쳐642.png)

- #### 설정한 옵션

```
Name : SSHusers
Number of Threads(users) : 4
Ramp-Up Period(in seconds) 1
Loop Count : 10
```

![화면 캡쳐641](/assets/화면%20캡쳐641.png)

## 3-2. Add Sampler
- #### 생성 방법
  - SSHusers > 마우스 우클릭 > Add > Sampler > SSH Command
![화면 캡쳐644](/assets/화면%20캡쳐644.png)

* **Server**
    * Hostname
    * Port : 22
    * Connection Timeout : 5000
* **Login**
    * User Name : root
    * Password : ~~~~~~~~~~~~~~~~
* **Client certificate**
    * SSH Key File : ??
    * Passphrase : ??
* **Execute**
    * Command : date
    * Use return code : True
    * Print Standard Error output : True


```
Server
  Hostname :                //서버 주소
  Port : 22
  Connection Timeout : 5000
Login
  User Name : root
  Password : ~~~~~~~~~
Client certificate
  SSH Key File : ??         //뭔지 모르겠어여.. 근데 안써도 될 때가 있고 안될때가 있고...
  Passphrase : ??
Execute
  Command : date
  Use return code : True
  Print Standard Error output : True
```
*(키파일 설정 안해서 처음 실행은 실패, 두번째는 되는데 왜 세번째는 또 안되지?)*

## 3-3. Add Listeners
- #### 생성 방법
  - SSHusers > 마우스 우클릭 > Add > Listeners > View Results Tree

![화면 캡쳐646](/assets/화면%20캡쳐646.png)

## 3-4. Run the Test plan
- #### 시작
  ![화면 캡쳐647](/assets/화면%20캡쳐647.png)

## 3-5. 확인
- #### 확인

  ![화면 캡쳐648](/assets/화면%20캡쳐648.png)

------
# 참고

#### 1. Plugin Manager 추가
* Jmeter는 플러그인 추가가 가능.
* 아래 주소를 참고하여 plugins-manager.jar 파일을 다운로드 받아서 JMeter홈/lib/ext 디렉토리에 붙여넣기.
```
https://jmeter-plugins.org/install/Install/
```
* Jmeter 재시작
* 좌측 상단 Options > Plugins Manager 추가된 것 확인.

#### 2. 그래프로 보여주는 View
* Thread Group > ADD > Listener > Spline Visualizer

#### 3. 중요 지표들을 보여주는 Report
* Thread Group > ADD > Listener > Summary Report










































----

> * 성능 테스트, 부하 테스트, 스트레스 테스트 툴 소개
*[참고] : http://woowabros.github.io/experience/2018/05/08/billing-performance_test_experience.html*
