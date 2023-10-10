먼저, 아두이노와 컴퓨터를 연결하는 USB to 


아두이노 코드

```c++
int s = 11;  // 스위치 핀 번호
int l = 13; // LED 핀 번호

void setup() {
  pinMode(l, OUTPUT);  // LED를 출력 핀으로 설정
  pinMode(s, INPUT_PULLUP);  // 내부 풀업 저항을 사용하는 입력 핀으로 설정
  Serial.begin(9600);  // 시리얼 통신 시작
}

void loop() {
  int sw = digitalRead(s);  // 스위치 상태를 읽어옴
  Serial.println(sw);  // 스위치 상태를 시리얼 모니터에 출력
  delay(1000);  // 1초 대기

  if (Serial.available() > 0) {  // 시리얼 입력이 있는 경우
    String c = Serial.readStringUntil('\n');  // 시리얼에서 문자열을 읽어옴, 개행 문자('\n')까지 읽음
    if (c == "5") {  // 만약 입력이 "5"라면
      digitalWrite(l, HIGH);  // LED를 켬
    } else if (c == "6") {  // 만약 입력이 "6"이라면
      digitalWrite(l, LOW);  // LED를 끔
    } else if (c == "7") {  // 만약 입력이 "7"이라면
      playSong();  // playSong() 함수를 호출하여 소리를 출력
    }
  }
}

void playSong() {
  int m[] = {523, 523, 783, 783, 880, 880, 783};  // 소리를 나타내는 주파수 배열
  int n = 500;  // 음악 소리가 나는 시간(ms)

  for (int i = 0; i < 7; i++) {
    tone(4, m[i], n);  // 주파수와 시간을 지정하여 소리를 내기
    delay(n);  // 소리가 재생되는 동안 대기
  }
  noTone(4);  // 소리 중지
}
```

프로세싱 코드
```java
import processing.net.*;
import processing.serial.*;

Server s;
Client c;
Serial p;

void setup() {
  s = new Server(this, 12345);  // 서버 생성, 포트 12345 사용
  p = new Serial(this, "COM3", 9600);  // COM3 포트와 시리얼 통신 시작
}

String msg = "hi";  // 초기 메시지 설정

void draw() {
  c = s.available();  // 클라이언트 연결 확인
  if (c != null) {  // 클라이언트가 연결된 경우
    String m = c.readString();  // 클라이언트로부터 데이터를 읽어옴
    if (m.indexOf("GET /") == 0) {  // 만약 클라이언트 요청이 "GET /"이라면
      c.write("HTTP/1.1 200 OK\r\n\r\n");  // 응답 헤더를 작성하여 스마트폰으로 보냄
      c.write(msg);  // 메시지를 보냄
    }
    if (m.indexOf("PUT /") == 0) {  // 만약 클라이언트 요청이 "PUT /"이라면
      int n = m.indexOf("\r\n\r\n") + 4;  // 데이터 위치 찾기
      m = m.substring(n);  // 데이터를 추출
      m += '\n';  // 개행 문자를 추가
      p.write(m);  // 시리얼 포트를 통해 데이터를 아두이노로 보냄
      print(m);  // 메시지를 출력
      c.write("HTTP/1.1 200 OK\r\n\r\n");  // 클라이언트에게 응답을 보냄
      c.write(m);  // 클라이언트에게도 메시지를 보냄
    }
    c.stop();  // 클라이언트 연결 종료
  }

  if (p.available() > 0) {  // 시리얼 데이터가 있는 경우
    String m = p.readStringUntil('\n');  // 시리얼에서 데이터를 읽어옴, 개행 문자('\n')까지 읽음
    if (m != null)  // 데이터가 유효한 경우
      msg = m;  // 메시지를 업데이트
    print(msg);  // 메시지를 출력
  }
}
```

앱인벤터

![제목 없음](https://github.com/irop3126/asdf/assets/127822814/fffe5fe5-cc79-49a1-98e0-7306a4cbf031)
![blocks (2)](https://github.com/irop3126/asdf/assets/127822814/aa8ee3bb-32f6-4282-9668-24c479e43cbd)

