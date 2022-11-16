# thermal_camera
ICT_mentoring Project


#include "thermal_camera.h"
#define TEMPERATURE_LIMIT 37

#include <SoftwareSerial.h>
SoftwareSerial soft(3, 2);
SoftwareSerial soft2(4, 5);

//SoftwareSerial(RX, TX)
SoftwareSerial BTSerial(6, 7);

thermal_camera tc(soft);

void setup()
{
  Serial.begin(9600);
  soft.begin(9600);
  BTSerial.begin(9600);
  
}

void loop()
{
  soft.begin(9600);
  
  if(BTSerial.available())
  Serial.write(BTSerial.read());
  
  if(Serial.available())
  BTSerial.write(Serial.read());
  
  float max_value, min_value;
  if ( tc.get_min_max_temperature(max_value, min_value) == true )
  {
    Serial.print("MAX : ");
    Serial.print(max_value);
    Serial.print(",");
    Serial.print("MIN : ");
    Serial.print(min_value);
    Serial.println("");
  }
  else
  {
    Serial.println("FAILED");
    return;
  }

  if ( max_value > TEMPERATURE_LIMIT )
  {
    soft2.begin(9600);
    soft2.write(1);
    soft2.end();

    tone(13, 1000);
    delay(500);
    noTone(13);
  }
  else
  {
    soft2.begin(9600);
    soft2.write((int)0);
    soft2.end();
  }
}

int wait_serial_available(int required_byte, unsigned int delay_time) // 시리얼 신호를 받을때까지 대기 대기시간은 0.1초
{
  if ( soft.available() >= required_byte) return soft.available();

  unsigned long start_millis = millis();

  while ( ( millis() - start_millis ) < delay_time )
  {
    if ( soft.available() >= required_byte ) return soft.available();
  }
  return 0;
}
