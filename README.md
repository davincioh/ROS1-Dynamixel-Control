# ROS로 Dynamixel 을 움직여보자!
다이나믹셀은 모터, 제어기, 드라이버, 센서, 감속기 및 네트워크 기능을 하나의 모듈로 만든 로봇 전용 스마트 액추에이터입니다.
ROS를 활용하여 다이나믹셀을 제어해보는 실습을 해보겠습니다.

준비물
--

- 하드웨어 : PC, Dynamixel, OpenCR
- 소프트웨어 : OS_Ubuntu 16.04, ROS Kinetic Kame
- ROS패키지 : Dynamixel-Workbench 


다이나믹셀 워크벤치 패키지 이용하기
--
다이나믹셀 워크벤치는 로보티즈에서 생산하고 판매하는 다이나믹셀(서보모터)를 쉽게 제어하고 프로그래밍을 도와주는 라이브러리입니다.
이 라이브러리는 다이나믹셀SDK를 기반으로 개발되었으며 ROS에서 활용가능한 패키지도 제공하고 있습니다. 

- 01 내 PC에 워크벤치 패키지를 다운로드하고 설치(빌드)합니다.
```
$ git clone https://github.com/ROBOTIS-GIT/dynamixel-workbench.git
$ git clone https://github.com/ROBOTIS-GIT/dynamixel-workbench-msgs.git
$ git clone https://github.com/ROBOTIS-GIT/DynamixelSDK.git
$ cd ~/catkin_ws && catkin_make
```

- 02 다운로드한 패키지를 catkin_ws/src 폴더에 넣어줍니다.



워크벤치 예제 실행하기(내 PC에 연결된 다이나믹셀 정보는?)
--
먼저 내 PC와 다이나믹셀을 연결합니다. 그리고 전원을 넣어줍니다. 패키지를 빌드하였기 때문에 'rosrun' 명령어를 통해 워크벤치 패키지에 구성된 노드(프로그램)들을 실행할 수 있습니다.

내 PC에 연결된 다이나믹셀을 확인해보는 프로그램(워크벤치 패키의 find_dynamixel 서비스)을 실행해 보겠습니다. 
```
$ rosrun dynamixel_workbench_controllers find_dynamixel /dev/ttyACM0
```

프로그램을 실행하게되면 "catkin_ws/src" 디렉토리에 위치한 "dynamixel-workbench 폴더(패키지)"의 "find_dynamixel" 프로그램(서비스)가 실행됩니다. 

프로그램이 실행이되면 터미널 창에 다음과 같은 메세지가 보입니다. 저는 XM430-W350 2개가 연결되어 있으며 각각 ID1, ID2로 할당되어 있습니다.


다이나믹셀을 움직이기 위해서 필요한 것들
--
다운로드한 다이나믹셀워크벤치의 구성은 다음과 같습니다.다이나믹셀을 움직이기 위해서는 워크벤치의 "dynamixel_controllers 프로그램(패키지)"를 이용합니다.
Controllsers  프로그램(패키지)을 이용하여 모터를 원하는 각도로 움직일 수 있습니다.

먼저, 다이나믹셀을 움직여보기 전, 가지고 있는 다이나믹셀과 연결을 위해 port명과 앞서 find_dynamixel을 통해 알게된 baudrate를 작성(수정)합니다.

- 01 dynamixel_controllers.launch 파일 수정
- 02 파일이 수정되면 catkin_make를 새로이 해주어 프로그램의 실행파일을 최신상태로 유지합니다.
- 03 메이크
```
$ cd ~/catkin_ws && catkin_make
```

두번째, Controllers 프로그램은 yaml  파일을 참조하여 다이나믹셀을 제어하게됩니다. yaml 파일이란 모터를 움직이기위한 셋팅값이 적힌 파일이라 보면됩니다. 

```
pan:

  ID: 1

  Return_Delay_Time: 0        -> 모터가 동작하고 패킷이 얼마나 늦게 올것인가?

  Operating_Mode: 3             -> 모터를 어떻게 제어할 것인가?  위치제어, PWM제어, 바퀴모드 등...

  Profile_Acceleration: 0        -> 모터의 가속도  설정

  Profile_Velocity: 0                -> 모터의 속도 설정 

tilt:

  ID: 2

  Return_Delay_Time: 0

  Operating_Mode: 3

  Profile_Acceleration: 0

  Profile_Velocity: 0
  ```
  
 DataSheet에서 셋팅값 확인해보기 
 - [다이나믹셀 프로토콜](http://emanual.robotis.com/docs/en/dxl/protocol2/)
 
 
 Controllers 프로그램 실행하기, 모터 동작준비 
 --
dynamixel_controllers.launch 파일을 실행합니다. 
현재는 이 파일이 어떻게 구성되고 어떤 코드로 구성되어 동작하는지는 다루지 않겠습니다.
앞서 우리는 다이나믹셀의 정보를 찾기위해 'rosrun' 을 이용하여 find_dynamixel 프로그램을 실행하였습니다.

이번에는 'roslaunch' 를 이용하여 프로그램을 실행해보고자 합니다. roslaunch는 하나 이상의 정해진 노드를 실행하는 명령어입니다. 
특히 XML파일을 기반으로 셋팅된 옵션값을 참조하여 프로그램을 실행하게 됩니다. 우린 방금  dynamixel_controllers.launch 파일의 port와 baudrate를 변경해주었습니다.

roslaunch는 port와 baudrate 셋팅값과 같은 각 '태그' 의 값을 참조하여 프로그램을 실행합니다.
  
- 01 controller를 실행합니다.
- 01-1 다음과 같은 결과를 확인하실 수 있습니다.


모터를 회전시켜보자, 위치제어
--
Controllers를 실행하면 모터가 움직이기위한 준비가 완료되었습니다. 모터에 토크가 들어가게되고 손으로 돌렸을 때 움직이지 않는 상태입니다.이제부터 모터에 'OO도로 회전해' 라는 명령어를 내리겠습니다.

- 01 먼저 GUI 도구 (rqt)를 이용하여 명령을 내려보겠습니다.
```
$ rqt
```

- 02 Open rqt and Plugins -> Services -> Service Caller

Expression 에 상기 이미지와 같이 값을 넣어줍니다.

"ID 1번 모터를 2048위치로 이동시킨다."

call 버튼을 누르면 모터가 움직이게 됩니다. value값을 변경하면 변경 값에 따라 모터가 움직이게 됩니다.


커멘드 명령어
--
GUI 도구가 아닌 터미널에서 명령을 내려보겠습니다.

```
$ rosservice call /dynamixel_workbench/dynamixel_command "command: ''

id: 1

addr_name: 'Goal_Position'

value: 2048"
```

