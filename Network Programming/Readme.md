# 네트워크 프로그래밍(TCP-IP)

## 1. 기본

1. 네트워크 프로그래밍 = 소켓 프로그래밍
2. 소켓을 기반으로 한다.
3. 네트워크로 연결된 둘 이상의 컴퓨터 사이에서의 데이터 송수신 프로그램 작성 의미

## 2. 소켓

1. 네트워크 연결 도구

2. 운영체제에 의헤 제공되는 소프트웨어 장치

3. 프로그래머에게 데이터 송수신에 대한 물리적, 소프트웨어적 세세한 신경을 쓰지 않게 한다.

4. TCP 소켓은 전화기에 비유

   ### 서버의 소켓 생성

   클라이언트보다 먼저 생성 되어야 한다.

   서버 소켓, 리스닝 소켓

   1. 소켓 생성

      **socket**

      서버의 소켓과 클라이언트의 소켓 생성 방법에는 차이가 있다.

      ```
      SOCKET socket = socket(PF_INET, SOCK_STREAM, 0); //(IPV4 프로토콜, TCP, 0)
      ```
      TCP는 SOCK_STREAM, UDP는 SOCK_DGRAM

   2. 소켓에 ip와 port 할당

      **bind**

      ```
      SOCKADDR_IN servAddr;
      servAddr.sin_family = AF_INET; //IPV4 주소
      servAddr.sin_addr.s_addr = htonl(INADDR_ANY); //어떤 ip로부터 접속을 받겠다.
      servAddr.sin_port = htons(atoi(argv[1])); //포트번호 할당
      bind(socket, (SOCKADDR*)&servAddr, sizeof(servAddr));
      ```

   3. 연결 가능한 상태의 소켓

      **listen**

      ```
      listen(socket, 5); //(소켓, 연결 요청 가능 개수)
      ```

   4. 연결 요청의 수락

      **accept**

      ```
      SOCKADDR_IN clntAddr;
      int szClntAddr;
      hClntSock = accept(socket, (SOCKADDR*)&clntAddr, &szClntAddr);
      ```

      연결 요청이 수락되어야 데이터의 송 수신이 가능

      수락 된 이후에 데이터의 송 수신은 양방향으로 가능하다.

   ### 클라이언트의 소켓 생성

   소켓의 생성과 연결 요청으로 구분

   1. 소켓 생성

      ```
      SOCKET Socket = socket(PF_INET, SOCK_STREAM, 0);
      ```

   2. 연결 요청

      ```
      SOCKADDR_IN servAddr;
      servAddr.sin_family = AF_INET;
      servAddr.sin_addr.s_addr = inet_addr(argv[1]); //서버의 주소 할당
      servAddr.sin_port = htons(atoi(argv[2])); //서버의 포트 할당
      connect(socket, (SOCKADDR*)&servAddr, sizeof(servAddr);
      ```

5. WSAStartup

   winsock의 초기화, winsock 함수 호출을 위한 라이브러리의 메모리 로드를 의미

   ```
   WSADATA wsaData;
   WSAStartup(MAKEWORD(2, 2), &wsaData); //winsock의 버전값(2.2), WSADATA 구조체의 포인터 주소
   ```
   종료

   ```
   WSACleanup();
   ```

6. 리눅스와 윈도우의 소켓 차이

   1. 리눅스는 소켓을 파일 입출력과 같이 처리

      **write**와 **read**

      **리눅스는 소켓도 파일로 간주하기 때문에 저 수준 파일 입출력 함수를 기반으로 소켓 기반의 통신이 가능하다.**

   2. 윈도우는 소켓을 파일 입출력과 별도로 처리

      **recv**와 **send**

      * send

      ```
      send(hClntSock, message, sizeof(message), 0); //(보낼 소켓, 보낼 메시지 버퍼, 버퍼의 크기, 0)
      ```

      * recv

      ```
      recv(hSocket, message, sizeof(message) - 1, 0); //(받을 소켓, 받을 메시지 버퍼, 버퍼의 크키, 0)
      ```

7. TCP 소켓에 존재하는 입출력 버퍼

   * 입출력 버퍼는 TCP 소켓 각각에 대해 별도로 존재한다.
   * 입출력 버퍼는 소켓 생성시 자동으로 생성된다.
   * 소켓을 닫아도 출력버퍼(send, write)에 남아있는 데이터는 계속해서 전송이 이루어진다.
   * 소켓을 닫으면 입력버퍼(recv, read)에 남아있는 데이터는 소멸된다.
## 3. TCP
1. 연결 지향형 프로토콜
2. 데이터의 경계가 없다.
3. 전화(받았는지, 못받았는지 확인이 가능하다.)
4. UDP에 비해 전송 속도가 느리다.
5. 서버 소켓과 클라이언트 소켓의 구분이 있다.
6. 전송 순서대로 데이터가 수신된다.
7. 중간에 데이터가 소멸되지 않는다.
8. 소켓대 소켓은 1대1 연결 구조

## 4. UDP
1. 비 연결 지향향 프로토콜
2. 데이터의 경계가 있다.
3. 우편(받았는지, 못받았는지 확인이 불가능하다.)
4. TCP에 비해 전송 속도가 빠르다.
5. 서버 소켓과 클라이언트 소켓의 구분이 없다. 즉 1개만 있으면 된다.
6. 전송 순서와 상관없다.
7. 데이터 손실 및 파손의 우려가 있다.
8. 한번에 보낼 수 있는 데이터의 크기가 제한된다.

## 5. 주소와 포트

1. IPV4 주소 체계

   총 32비트(8비트.8비트.8비트.8비트)

   클래스 A : 1.0.0.1 ~ 126.255.255.254, **AAA.xxx.xxx.xxx**

   클래스 B : 128.0.0.1 ~ 191.255.255.254, **BBB.BBB.xxx.xxx**

   클래스 C : 192.0.0.1 ~ 223.255.255.254, **CCC.CCC.CCC.xxx**

   클래스 D : 224.0.0.0 ~ 239.255.255.255

   클래스E : 240.0.0. ~ 254.255.255.255

   클래스 A의 첫번째 바이트 범위 : 0 ~ 127, 항상 0으로 시작

   클래스 B의 첫번째 바이트 범위 : 128 ~ 191, 항상 10으로 시작

   클래스 C의 첫번째 바이트 범위 : 192 ~ 223, 항상 110으로 시작

2. PORT

   16비트, 0이상 65535이하

   0 ~ 1023은 Well-known Port, 용도가 이미 결정되어 있다.

## 6. 바이트 변환

1. 네트워크 바이트는 **빅 엔디안** 기준

2. cpu마다 저장 방식이 다르다.

3. 빅 엔디안

   상위 바이트 값을 작은 번지수에 저장

   0x1234567 -> 0x12 0x34 0x45 0x78 

4. 리틀 엔디안

   인텔 CPU는 **리틀 엔디안**

   상위 바이트 값을 큰 번지수에 저장

   0x12345678 -> 0x78 0x56 0x34 0x12

   산술연산유닛(ALU)에서 메모리를 읽는 방식이 메모리 주소가 낮은 쪽에서부터 높은 쪽으로 읽기 때문에 산술 연산의 수행이 더 쉽다.

5. htons, htonl

   host to network

   l : long 자료형, 4바이트, ip주소

   s : short 자료형, 2바이트, 포트번호

   내가 쓰는 pc는 인텔 cpu, 리틀 엔디안이고 네트워크는 빅 엔디안이기 때문에 변환해야 한다.

   ```
   //서버
   servAddr.sin_addr.s_addr = htonl(INADDR_ANY);
   servAddr.sin_port = htons(atoi(argv[1]));
   //클라이언트
   servAddr.sin_addr.s_addr = inet_addr(argv[1]);
   servAddr.sin_port = htons(atoi(argv[2]));
   ```
## 7. TCP 연결, 3 way handshake

1. IP는 3계층(네트워크 계층)이고, TCP & UDP는 4계층(전송 계층)

2. 서로의 통신을 위해 확인하고, 연결하는 과정이 3번의 요청/응답 후에 연결된다.

   ![img](http://cfile24.uf.tistory.com/image/267C5D39537EDD2C176BD3)

   1. Client -> Server : SYN

      SYN : 서버에게 연결 요청

   2. Server -> Client : ACK + SYN

      서버는 SYN 데이터를 받고 SYN_RCV 상태로 변경 된다.

      ACK : 1번 메시지에 대한 응답

      SYN : 클라이언트도 포트를 열어 달라는 메시진

   3. Client -> Server : ACK

      ACK : 2번에서 받은 메시지에 응답을 보낸다.


   이와 같이 3번의 통신이 정상적으로 이루어지면서, 서로의 포트가 ESTABLISHED 상태가 된다.

3. SYN : 연결 요청

   SYN을 보낸 쪽은 ACK가 올 때 까지 기다리고 있다.

4. ACK : 응답

5. status

   Closed : 닫힌 상태

   LISTEN : 포트가 열린 상태로 연결 요청 대기 중

   SYN_RCV : SYNC 요청을 받고 상대방의 응답을 기다리는 중

   ESTABLISHED : 포트 연결 상태
## 8. TCP 종료, 4 way handshake
![img](http://cfile21.uf.tistory.com/image/2678E035537EEE9126A109)

1. 통신을 종료하고자 하는 Client가 서버에게 FIN 패킷을 보내고 자신은 FIN_WAIT_1 상태로 대기한다.
2. FIN 패킷을 받은 서버는 해당 포트를 CLOSE_WAIT으로 바꾸고 잘 받았다는 ACK 를 Client에게 전하고 ACK를 받은 Client는 상태를 FIN_WAIT_2로 변경한다.

그와 동시에 Server에서는 해당 포트에 연결되어 있는 Application에게 Close()를 요청한다.

3. Close() 요청을 받은 Application은 종료 프로세스를 진행시켜 최종적으로 close()가 되고 server는 FIN 패킷을 Client에게 전송 후 자신은 LAST_ACK 로 상태를 바꾼다.

4. FIN_WAIT_2 에서 Server가 연결을 종료했다는 신호를 기다리다가 FIN 을 받으면 받았다는 ACK를 Server에 전송하고 자신은 TIME_WAIT 으로 상태를 바꾼다. 

   (TIME_WAIT 에서 일정 시간이 지나면 CLOSED 되게 된다.)

최종 ACK를 받은 서버는 자신의 포트도 CLOSED로 닫게 된다.

## 9. TCP 문제점
* 문제점

  클라이언트는 문장 단위로 데이터를 송 수신 하기 때문에 데이터의 경계가 없는 TCP에서 문제가 발생한다.

  서버는 send/write 함수를 이용해 데이터를 전송 하기 때문에 문제되지 않는다.

* 해결책

  서버로 부터 전송한 데이터의 길이만큼 읽어 들이기 위한 작업이 필요하다.

  즉, 서버는 사전에 데이터를 얼만큼 보낼지 미리 클라이언트에게 보내야 한다.

  클라이언트는 사전에 서버로 부터 받은 데이터의 길이 만큼, 서버로 부터 데이터를 수신 받으면 된다.

## 10. UDP

* UDP 소켓은 SEQ, ACK와 같은 메시지 전달을 하지 않는다.

* Flow Control이 없다.

* 연결의 설정과 해제의 과정도 존재하지 않는다.

* 데이터 분실 및 손실의 위험이 있다.

* 확인의 과정이 존재하지 않기 때문에 데이터의 전송이 빠르다.

* 성능이 중시될 때 UDP를 사용한다.

* 서버 소켓과 클라이언트 소켓의 구분이 없다.

* 하나의 소켓으로 둘 이상의 영역과 데이터 송수신이 가능하다.

* 따라서 데이터를 전송할 때 마다 목적지에 대한 정보를 같이 전달해햐 한다.

* 데이터의 전송지가 둘 이상이 될 수 있으므로 데이터 수신 후 전송지가 어디인지 확인할 필요가 있다.

* sendto : 클라이언트

  ```
  sendro(sock, message, strlen(message), 0, (struct sockaddr*)&serv_adr, sizeof(serv_adr));
  ```

* recvfrom : 서버

  ```
  recvfrom(serv_sock, message, BUF_SIZE, 0, (struct sockaddr*)&clnt_adr, &clnt_adr_sz);
  ```

* 데이터의 경계가 존재한다.

* connected UDP 소켓과 unconnected UDP 소켓이 있다.

* unconnected UDP 소켓

  sendto 함수 호출과정

  1. UDP 소켓에 목적지의 IP와 PORT 번호 등록
  2. 데이터 전송
  3. UDP 소켓에 등록된 목적지 정보 삭제

* connected UDP 소켓의 경우 1단계와 3단계의 과정을 매회 거치지 않는다.

* connected UDP 소켓은 TCP와 같이 상대 소켓과의 연결을 의미하지는 않는다. 그러나 소켓에 목적지에 대한 정보는 등록된다.

* connected UDP 소켓을 대상으로는 read, write 함수의 호출이 가능하다.

## 11. TCP 기반의 Half-close

* close, closesocket 함수의 기능

  * 소켓의 완전 소멸을 의미한다.
  * 소켓이 소멸되므로 더 이상의 입출력은 불가능하다.
  * 상대방의 상태에 상관 없이 일방적인 종료의 형태를 띤다.
  * 상대 호스트의 데이터 송수신이 아직 완료되지 않은 상황이라면, 문제가 발생할 수 있다.

* 소켓

  * 소켓을 통해 호스트 연결
  * 상호간 데이터 송수신 가능 상태
  * 이러한 상태를 스트림이 형성된 상태라고 함

* 스트림

  * 두 소켓이 연결되어 데이터의 송수신이 가능한 상태
  * 스트림은 한쪽 방향으로만 형성
  * 양방향 통신을 위해 두 개의 스트림이 필요하다.

* Half-close

  * 종료를 한다는 것은, 더 이상 전송할 데이터가 존재하지 않는 상황이다.

  * 출력 스트림은 종료해도 된다.

  * 상대방도 종료를 원하는지 확인되지 않은 상황이므로, 출력 스트림은 종료시키지 않을 필요가 있다.

  * 일반적으로 Half-close 라고 하면, 입력 스트림만 종료하는 것을 의미한다.

  * shutdown 함수를 사용한다.

    ```
    shutdown(clnt_sd, SHUT_WR) (소켓, 종료 방법에 대한 정보 전달)
    ```

    종료 방법

    SHUT_RD : 입력 스트림 종료

    SHUT_WR : 출력 스트림 종료

    SHUT_RDWR : 입출력 스트림 종료

## 12. 도메인과 DNS 서버

* 도메인 이름
  * IP를 대신하는 서버의 주소, 가상의 주소
  * 실제 접속에 사용되는 주소는 아니다. 이 정보는 IP로 변환이 되어야 접속이 가능하다.
  * 도메인 이름을 이용해서 서버에 접속하면, 접속 이전에 DNS 서버에 해당 도메인의 IP 주소를 묻게 되고, 그 결과로 얻게 된 IP를 이용해서 서버에 접속하게 된다.
* DNS 서버(Domain Name Server)
  * 도메인 이름을 IP로 변환해주는 서버
  * DNS는 일종의 분산 데이터베이스 시스템
* gethostbyname 함수 : 도메인 이름을 이용해서 IP 주소를 얻어온다.
* gethostbyaddr 함수 : IP 주소를 이용해서 도메인 주소를 얻어온다.

## 13. 멀티프로세스 기반 서버

* 다중 접속 서버 : 둘 이상의 클라이언트에게 동시에 접속을 허용하여, 동시에 둘 이상의 클라이언트에게 서비스를 제공하는 서버를 의미한다.

* 멀티프로세스 기반 서버 : 다수의 프로세스를 생성하는 방식으로 서비스 제공

* 멀티플렉싱 기반 서버 : 입출력 대상을 묶어서 관리하는 방식으로 서비스 제공

* 멀티쓰레딩 기반 서버 : 클라이언트 수만큼 쓰레드를 생성하는 방식으로 서비스 제공

* 프로세스

  * 메모리 공간을 차지한 상태에서 실행 중인 프로그램을 의미
  * 멀티프로세스 운영체제는 둘 이상의 프로세스를 동시에 생성 가능하다.
  * 실행중인 프로그램에 관련된 메모리, 리소스 등을 총칭하는 의미이다.
  * 프로세스 ID : 운영체제는 생성되는 모든 프로세스에 ID를 할당한다.

* fork 함수 : 프로세스 생성

  * fork 함수가 호출되면, 호출한 프로세스가 복사되어 fork 함수 호출 이후를 각각의 프로세스가 독립적으로 실행하게 된다.
  * fork 함수 호출 이후의 반환 값의 차이로 부모, 자식 프로세스를 구분한다.
  * 부모 프로세스 : fork 함수의 반환 값은 자식 프로세스의 ID
  * 자식 프로세스 : fork 함수의 반환 값은 0
  * fork 함수를 호출한 프로세스는 부모 프로세스
  * fork 함수의 호출을 통해서 생성된 프로세스는 자식 프로세스

* 좀비 프로세스

  * 실행이 완료되었음에도 불구하고, 소멸되지 않은 프로세스
  * 프로세스도 main 함수가 반환되면 소멸되어야 한다.
  * 소멸되지 않았다는 것은 프로세스가 사용한 리소스가 메모리 공간에 여전히 존재한다는 의미이다.

* 좀비 프로세스의 생성 원인

  * ```
    fork 함수의 호출로 생성된 자식 프로세스 종료상황
    - 인자를 전달하면서 exit를 호출하는 경우
    - main 함수에서 return 문을 실행하면서 값을 반환하는 경우
    - 자식 프로세스의 종료 상태 값이 운영체제에 전달되는 경로
    ```

  * 자식 프로세스가 종료되면서 반환하는 상태 값이 부모 프로세스에게 전달되지 않으면 해당 프로세스는 소멸되지 않고 좀비가 된다.

* 좀비 프로세스의 소멸1 - wait 함수의 사용

  * 부모 프로세스가 자식 프로세스의 전달 값을 요청하는 함수
  * wait 함수는 종료된 프로세스 관련 정보를 가져온다.
  * 자식 프로세스가 종료되지 않은 상황에서는 반환하지 않고 블로킹 상태에 놓인다.
  * WIFEXITED : 자식 프로세스가 정상 조료한 경우 true 를 반환한다.
  * WEXITSTATUS : 자식 프로세스의 전달 값을 반환한다.

* 좀비 프로세스의 소멸2 - waitpid 함수의 사용

  * 블로킹 상태에 놓이지 않는다.

    ```
    waitpid(-1, &status, WNOHANG) (pid(-1을 전달하면 wait 함수와 마찬가디로 임의의 자식 프로세스가 종료되기를 기다린다., statloc, options)
    ```

* 시그널

  * 특정 상황이 되었을 때 운영체제가 프로세스에게 해당 상황이 발생했음을 알리는 일종의 메시지
  * SIGALRM : alarm 함수호출을 통해서 등록된 시간이 된 상황
  * SIGINT : CTRL + C 가 입력된 상황
  * SIGCHLD : 자식 프로세스가 종료된 상황

* 시그널 등록

  * 특정 상황에서 운영체제로부터 프로세스가 시그널을 받기 위해서는 해당 상황에 대해서 등록의 과정을 거쳐야 한다.
  * signal(SIGCHLD, func1) : 자식 프로세스가 종료되면 함수 호출
  * signal(SIGALRM, func2) : alarm 함수호출을 통해서 등록된 시간이 지나면 함수 호출
  * signal(SIGINT, func3) : 특정 키가 입력되면 함수호출
  * sigaction 함수를 대신 사용한다.

* sigaction

  * 구조체 변수를 선언해서, 시그널 등록 시 호출될 함수의 정보를 채워서 함수 호출시 인자로 전달한다.
  * sigaction(SIGALRM, &act, 0)

* 프로세스 기반 다중접속 서버의 모델

  * 연결이 하나 생성될 때 마다 프로세스를 생성해서 해당 클라이언트에 대한 서비스를 제공하는 것이다.

  1. 에코 서버는 accept 함수 호출을 통해서 연결 요청을 수락한다.
  2. 이때 얻게 되는 소켓의 파일 디스크립터를 자식 프로세스를 생성해서 넘겨준다.
  3. 자식 프로세스는 전달받은 파일 디스크럽터를 바탕으로 서비스를 제공한다.

* 입출력 루틴 분할

  * 소켓은 양방향 통신이 가능핟. 입력을 담당하는 프로세스와, 출력을 담당하는 프로세스를 각각 생성하면, 입력과 출력을 각각 별도로 진행시킬 수 있다.
  * 부모 프로세스에서는 수신 코드만 작성
  * 자식 프로세스에서는 송신 코드만 작성
  * 입출력 루틴을 분할하면, 보내고 받는 구조가 아니라, 이 둘이 동시에 진행 가능
  * 클라이언트는 데이터의 수신 여부와 상관 없이 데이터 전송이 가능해진다.

## 14. 프로세스간 통신

* 두 프로세스의 통신이 가능하다.

  * 두 프로세스 사이에서의 데이터 전달이 가능하다.
  * 두 프로세스가 함께 공유하는 메모리 공간이 존재해햐 하낟.
  * 변수를 통한 두 프로세스 간의 통신

* 프로세스간 통신의 어려움

  * 모든 프로세스는 자신만의 메모리 공간을 독립적으로 구성한다.
  * 즉 A 프로세스에서는 B 프로세스의 메모리 공간에 접근이 불가능하고, B 프로세스에서는 A 프로세스의 메모리 공간 접근이 불가능하다.
  * 운영체제가 별도의 메모리 공간을 마련해 줘야 프로세스간 통신이 가능하다.

* 파이프 기반의 프로세스간 통신

  * 운영체제는 서로 다른 프로세스가 함께 접근할 수 있는 메모리 공간을 만들고, 이 공간의 접근에 사용되는 파일 디스크립터를 반환한다.

  * 파이프는 운영체제에 속하는 자원

    ```
    pipe(int filedes[2]) ([0] : 파이프로부터 데이터를 수신하는데 사용되는 파일 디스크립터 저장, [1] : 파이프로 데이터를 전송하는데 사용되는 파일 디스크립터가 저장)
    ```

  * fds[0] : 파이프로부터 데이터를 수신, 입구

  * fds[1] : 파이프로부터 데이터를 송신, 출구

  * 하나의 파이프를 이용해 양방향 통신을 하는 경우, 타이밍이 매우 중요하기 때문에 사실상 불가능하다.

  * 양방향 통신을 위해서는 두 개의 파이프를 생성해야 한다.

## 15. IO 멀티플렉싱

* 멀티 프로세스 서버의 단점
  * 프로세스의 빈번한 생성은 성능의 저하로 이어진다. - 많은 연산, 큰 메모리 공간 필요
  * 멀티 프로세스의 흐름을 고려해서 구현해야 하기 때문에 구현이 쉽지 않다.
  * 프로세스간 통신이 필요한 상황에서는 서버의 구현이 더 복잡해진다.
* 멀티 프로세스 서버의 대안
  * 하나의 프로세스가 다수의 클라이언트에게 서비스를 할 수 있도록 한다.
  * 하나의 프로세스가 여러 개의 소켓을 핸들링 할 수 있는 방법이 존재해야 한다. = IO 멀티플렉싱
* 하나의 리소스를 둘 이상의 영역에서 공유하는 것을 멀티 플렉싱

## 16. 멀티쓰레드 기반 서버

* 프로세스는 부담스럽다.
  * 프로세스의 생성에는 많은 리소스가 소모된다.
  * 프로세스가 생성되면, 프로세스간 컨텍스트 스위칭(문맥 교환)으로 인해서 성능이 저하된다.
  * 문맥 교환은 프로세스의 정보를 하드디스크에 저장, 복원하는 일이다.
* 데이터 교환이 어렵다.
  * 프로세스간 메모리가 독립적으로 운영되기 때문에 프로세스간 데이터 공유는 힘들다.
  * 운영체제가 별도로 제공하는 메모리 공간을 대상으로 별도의 IPC 기법을 사용한다.
* 쓰레드의 등장
  * 프로세스보다 가벼운, 경량화된 프로세스이다.
  * 문맥교환이 빠르다.
  * 쓰레드 별로 메모리 공유가 가능하기 때문에 IPC 기법이 필요없다.
  * 프로세스 내에서의 프로그램의 흐름을 추가한다.
* 쓰레드는 프로세스 내에서의 실행 흐름을 가진다.
  * Thread 각각 스택 영역
  * Thread 공유 데이터 영역
  * Thread 공유 힙 영역
* 하나의 운영체제 내에서는 둘 이상의 프로세스가 생성되고, 하나의 프로세스 내에서는 둘 이상의 쓰레드가 생성된다.
* pthread_create() : 쓰레드 생성
* 프로세스가 종료되면, 해당 프로세스 내에서 생성된 쓰레드도 함께 소멸된다.
* pthread_join() : 첫 번째 인자로 전달되는 ID의 쓰레드가 종료될 때 까지, 이 함수를 호출한 프로세스(또는 쓰레드)를 대기상태에 둔다.
* 둘 이상의 쓰레드가 동시에 호출하면 문제를 일으키는 함수를 가리켜 쓰레드에 불안전한 함수
* 둘 이상의 쓰레드가 동시에 호출해도 문제를 일으키지 않은 함수를 가리켜 쓰레드 세잎 함수

## 16. 임계 영역과 동기화

* 둘 이상의 쓰레드가 동시에 실행하면 문제를 일으키는 영역이 임계영역
* 동기화가 필요한 상황
  * 동일한 메모리 영역으로의 동시접근이 발생하는 상황
  * 동일한 메모리 영역에 접근하는 쓰레드의 실행순서를 지정해야 하는 상황
  * 동기화를 통해서 동시접근을 막을 수 있고, 접근의 순서를 지정하는 것도 가능하다.
* 동기화 기법
  * Mutex 기반 동기화
  * Semaphore 기반 동기화
* 뮤텍스
  * pthread_mutex_init() : 생성
  * pthread_mutex_destroy() : 소멸
  * pthread_mutex_lock(&mutex) : 임계 영역의 시작
  * pthread_mutex_unlock(&mutex) : 임계 영역의 끝
* 세마포어
  * 세마포어 카운트 값을 통해서 임계영역에 동시 접근이 가능한 쓰레드의 수를 제한할 수 있다.
  * 세마포어 카운트가 0이면 진입 불가, 0보다 크면 진입가능
  * sem_init() : 생성
  * sem_destroy() : 소멸
  * sem_wait(&sem) : 세마포어 값을 0으로 (감소), 임계영역의 시작
  * sem_post(&sem) : 세마포어 값을 1로 (증가), 임계영역의 끝

## 17. 커널 오브젝트(Kernel Objects)

* 운영체제가 만드는 리소스의 유형

  * 프로그램의 실행과 관련된 프로세스와 쓰레드
  * 입출력의 도구가 되는 소켓과 파일
  * 쓰레드간 동기화의 도구로 사용되는 세마포어, 뮤텍스

* 리소스와 커널 오브젝트의 관계

  * 리소스 관리를 위해서 운영체제가 만드는 데이터 블록이 커널 오브젝트
  * 커널 오브젝트에는 해당 리소스의 정보가 저장되어 있다.
  * 리소스의 종류에 따라서 생성되는 커널 오브젝트의 형태에 차이가 있다.

* 커널 오브젝트의 소유자

  * 커널 오브젝트이 생성, 관리 및 소멸은 운영체제가 담당한다.
  * 커널 오브젝트의 소유자는 운영체제이다.

* 운영체제 레벨에서 쓰레드를 지원한다. 따라서 main 함수의 호출은 쓰레드에 의해서 이뤄진다.

* 쓰레드를 추가로 생성하지 않으면, 하나의 프로세스 내에 하나의 쓰레드가 생성되어, 쓰레드에 의해서 실행된다.

* 단일 쓰레드 모델의 프로그램 : 추가로 쓰레드를 생성하지 않은 모델의 프로그램

* 멀티 쓰레드 모델의 프로그램 : 프로그램 내에서 추가로 쓰레드를 생성하는 모델의 프로그램

* 윈도우에서의 쓰레드 생성

  ```
  _beginthreadex(NULL, 0, ThreadFunc, (void*&param), 0, &threadID);
  ```

* 커널 오브젝트이 두 가지 상태

  * non-signled 상태
    * 이벤트가 발생하지 않은 상태, 해당 리소스가 특정 상황에 이르지 않은 상태
  * signled 상태
    * 이벤트가 발생한 상태, 해당 리소스가 특정상황에 도달한 상태

* 전달된 핸들의 커널 오브젝트가 signled 상태가 되어야 함수가 반환을 한다.

* signaled 상태로 인한 반환시 WAIT_OBJECT_0가 반환된다.

* 호출된 함수가 반환되면서 자동으로 non-siganed 상태로 변경되는 커널 오브젝트를 auto-reset 모드 커널 오브젝트라고 한다.

* 반대는 manual-reset 모드 커널 오브젝트

## 18. CRITICAL_SECTION 동기화

* 유저 모드(User Mode) : 응용 프로그램이 실행되는 기본모드, 물리적인 영역으로의 접근이 허용되지 않으며, 접근할 수 있는 메모리의 영역에도 제한이 따른다. 응용 프로그램의 실행모드
* 커널 모드(Kernel Mode) : 운영체제가 실행될 때의 모드, 메모리 뿐만 아니라, 하드웨어의 접근에도 제한이 따르지 않는다. 운영체제의 실행모드
* 유저모드로 동작을 하면, 제한된 메모리 및 리소스에만 접근이 가능
* 쓰레드의 생성과 같은 운영체제에 의해서 완성되는 연산을 위해서는 유저모드에서 커널모드로의 전환이 이뤄져야 하고, 반대로 커널모드에서 유저모드로의 전환도 이뤄져야 한다.
* 유저모드 동기화
  * 운영체제에 의해서 이뤄지는 동기화가 아닌, 순수 라이브러리에 의해서 완성되는 동기화 기법
  * 운영체제에 의해서 제공되지 않으므로 커널모드로의 전환이 불필요. 가볍고 속도 빠르다.
  * 기능이 제한적
  * CRITICAL_SECTION 기반 동기화
* 커널모드 동기화
  * 커널모드 동기화는 커널에 의해서 제공되는 동기화 기법
  * 커널에 의해 제공되는 동기화이므로 다양한 기능 제공
  * 서로 다른 프로세스의 두 쓰레드 간 동기화 가능
  * Dead-lock에 걸리지 않도록 타임아웃을 지정할 수 있음
* Mutex
* Semaphore
* Event