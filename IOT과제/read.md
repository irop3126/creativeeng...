IOT과제

다음 두 가지가 가능하도록 IoT를 구현하기

1. 스마트폰의 버튼 2개를 만들고 서버컴퓨터(PC)의 USB와 연결된 아두이노의 13번 LED를 제어할 수 있도록 코딩한다
   
2. 아두이노에서 온도 회로와 조도회로를 만들고 온도는 A0, 조도는 A1의 입력으로 연결하고, 1초에 한번씩 보내고, 스마트폰의 화면에 나타나도록 한다.

아두이노 코드
```
void setup() {
  Serial.begin(9600);
  pinMode(13, OUTPUT);
}
double th(int v) {
  double t;
  t = log(((10240000/v) - 10000));
  t = 1 /(0.001129148 + (0.000234125*t) + (0.0000000876741*t*t*t));
  t = t - 273.15;
  return t;
}
void loop() {
  int a = analogRead(A0);
  int b = analogRead(A1);
  Serial.print(th(a));
  Serial.print(" ");
  Serial.println(b);
  delay(1000);
  if (Serial.available() > 0) {
    String m = Serial.readStringUntil('\n');
    if (m == "on") digitalWrite(13, HIGH);
    if (m == "off") digitalWrite(13, LOW);
  }
}
```

프로세싱 코드
```
import processing.net.*;
import processing.serial.*;
Server s;
Client c;
Serial p;
void setup() {
  s = new Server(this, 12345);
  p = new Serial(this, "COM3", 9600);
}
String msg="hi";
void draw() {
  c = s.available();
  if (c!=null) {
    String m = c.readString();
    if (m.indexOf("GET /")==0) {
      c.write("HTTP/1.1 200 OK\r\n\r\n");
      c.write(msg);
    }
    if (m.indexOf("PUT /")==0) {
      int n = m.indexOf("\r\n\r\n")+4;
      m = m.substring(n);
      m += '\n'; 
      p.write(m);
      print(m);
    }
    c.stop();
  }
  if (p.available()>0) {
    String m = p.readStringUntil('\n');
    if (m!=null)  msg = m;
    print(msg);
  }
}
```

MIT 앱인벤터 사진

![blocks (3)](https://github.com/irop3126/creativeeng.../assets/127822814/1406a01b-c4ef-42eb-92a1-66e68e2bde9d)

작동여부 사진

![20231105_173854](https://github.com/irop3126/creativeeng.../assets/127822814/7cc72678-b8b4-4ebf-b622-ff500e4b0bda)

작성소감

20230945김범석

IOT를 만들면서 어느정도 난관에 부딪혀가면서 기능을 구현하면서 gpt의 도움을 받으면서 어떻게든 기능을 구현하고 그러다보니 조금 허술하고 조잡한 프로그램을 만들었다는 생각이 들면서도 나름대로 조건에 맞는 프로그램을 만들어냈다는 생각에 뿌듯함을 느꼈고, 좀 더 프로그래밍 언어에 대해 공부를 하지않으면 힘들겠다는 생각도 들었습니다. 그리고 처음 받았을때 아두이노 키트에서 10K옴 저항이 하나뿐인 상황에서 교수님께 문의했는데 친절히 여러방법으로 해결방법을 알려주셔서 감사했고, 어떻게든 이러한 문제를 해결하고 아두이노를 완성할수 있게되어서 기쁩니다.
