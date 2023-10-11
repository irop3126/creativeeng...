1.아두이노 센서의 회로도롤 작성한 것입니다.
![20231011_182523](https://github.com/irop3126/creativeeng.../assets/127822814/4c4afd9e-bc69-4a8b-8c53-b05e637ec022)

2.아두이노에서 측정된 온도센서가 보내는 데이터를 서버가 잘 받아서 화면에 출력중인 사진입니다.

![console](https://github.com/irop3126/creativeeng.../assets/127822814/20c90f63-e7e4-44c9-b097-9079dcbd6b9d)

3.코드들

아두이노 코드

```c++
void setup() {
  Serial.begin(9600);
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
  Serial.println(th(a));
  delay(500);
}
```
Serial.begin(9600);: 시리얼 통신을 초기화하고, 데이터 전송 속도를 9600 bps로 설정합니다.
double th(int v) { ... }: th라는 함수는 아날로그 핀 A0에서 읽은 값 v를 사용하여 온도를 계산합니다. 이 함수는 NTC(음극 이온화 저항체) 센서를 사용한 온도 변환 공식을 구현합니다.
int a = analogRead(A0);: 아날로그 핀 A0에서 데이터를 읽어서 a 변수에 저장합니다.
Serial.println(th(a));: th 함수를 사용하여 a 값을 온도로 변환하고 시리얼 포트를 통해 프로세싱으로 전송합니다.
delay(500);: 500ms(0.5초) 동안 대기합니다.

프로세싱 코드

```java
import processing.serial.*;
Serial p;
void setup()
{
  p = new Serial(this,"COM3",9600);
}
void draw()
{
  if(p.available()>0)
  {
    String m = p.readStringUntil('\n');
    if(m != null)
    {
      print(m);
    }
  }
}
```
Serial p;: 프로세싱의 Serial 객체를 생성합니다.
p = new Serial(this, "COM3", 9600);: COM 포트 "COM3"을 통해 아두이노와 시리얼 통신을 설정합니다. 데이터 전송 속도는 9600 bps로 설정됩니다.
void draw() { ... }: 이것은 프로세싱의 주요 실행 루프입니다. 시리얼 포트를 모니터링하여 데이터를 읽고 출력합니다.
if (p.available() > 0) { ... }: 시리얼 버퍼에 데이터가 있는지 확인합니다.
String m = p.readStringUntil('\n');: 줄 바꿈 문자('\n')까지의 데이터를 읽어 m 변수에 저장합니다.
if (m != null) { ... }: 만약 m 변수가 null이 아니면, 데이터를 출력합니다.
