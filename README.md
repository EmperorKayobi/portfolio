# Personal Portfolio 
A list of all my project.
Regularly updated.
 # Overview
- [Makerspace Terminal](https://github.com/EmperorKayobi/portfolio#Makerspace Terminal)

# News
```
 13/12/20
 Started a new AI powered PCB refolw oven project. More details soon.
```
# Makerspace Terminal
![](https://github.com/EmperorKayobi/portfolio/blob/main/images/borne.jpg) 

Hardware: 
- Arduino
- Raspberry pi
- Rfid module
Code:
```
//author : Emperor_Kayobi and Jesus
//hours of work : idk, way too much
//special thanks to our brain for not going (totally) crazy
/* RST/Reset   RST          9             5         D9         RESET/ICSP-5     RST
   SPI SS      SDA(SS)      10            53        D10        10               10
   SPI MOSI    MOSI         11 / ICSP-4   51        D11        ICSP-4           16
   SPI MISO    MISO         12 / ICSP-1   50        D12        ICSP-1           14
   SPI SCK     SCK          13 / ICSP-3   52        D13        ICSP-3           15
*/
#include <Ethernet.h>
#include <MySQL_Connection.h>
#include <MySQL_Cursor.h>
#include <SPI.h>
#include <MFRC522.h>
#include <AP_Sync.h>
AP_Sync streamer(Serial);
#define SS_PIN         8
#define RST_PIN        9

byte mac_addr[] = { 0x90, 0xA2, 0xDA, 0x0D, 0xCD, 0x81 };
int txData = 0;
String txData2 = "";
char user[] = "";	//use custom values here
char password[] = "";	//use custom values here
char INSERT_SQL[] = "INSERT INTO .logs(UserID) VALUES ('%d');";
char query[128];

IPAddress server_addr(192, 168, 192, 104);
MFRC522 mfrc522(SS_PIN, RST_PIN);
EthernetClient client;
MySQL_Connection conn((Client *)&client);

void setup() {
  Serial.begin(115200);
  SPI.begin();
  digitalWrite(10, HIGH);
  digitalWrite(8, LOW);
  mfrc522.PCD_Init();
  mfrc522.PCD_DumpVersionToSerial();
  Serial.println();
  Ethernet.begin(mac_addr);
}
void loop() {
  if (digitalRead(8) == HIGH) {
    Serial.println("1");
    digitalWrite(8, LOW);
    digitalWrite(10, HIGH);
    Serial.println("2");
    mfrc522.PCD_Init();
  }
  Serial.println("Ready!");
  x1:
  if (mfrc522.PICC_IsNewCardPresent()) {
    mfrc522.PICC_ReadCardSerial();
    goto x2;
  }
  else {
    goto x1;
  }
  x2:
  txData2 = String(mfrc522.uid.uidByte[0]) + String(mfrc522.uid.uidByte[1]) + String(mfrc522.uid.uidByte[2]) + String(mfrc522.uid.uidByte[3]) + String(mfrc522.uid.uidByte[4]) + String(mfrc522.uid.uidByte[5]) + String(mfrc522.uid.uidByte[6]) + String(mfrc522.uid.uidByte[7]) + String(mfrc522.uid.uidByte[8]) + String(mfrc522.uid.uidByte[9]);
  int txData1 = txData2.toInt();
  txData = txData2.toInt();
  digitalWrite(8, HIGH);
  digitalWrite(10, LOW);
  Serial.println("Starting Connection ...");
  streamer.sync("stalin",1);
  conn.connect(server_addr, 3306, user, password);
  Serial.println("Data & Connection ready...");
  MySQL_Cursor *cur_mem = new MySQL_Cursor(&conn);
  sprintf(query, INSERT_SQL, txData);
  Serial.println(query);
  cur_mem->execute(query);
  delete cur_mem;
  Serial.println("Data seems fine. Login complete(maybye)");
  x3:
  if (mfrc522.PICC_IsNewCardPresent()) {
    mfrc522.PICC_ReadCardSerial();
  }
  txData2 = String(mfrc522.uid.uidByte[0]) + String(mfrc522.uid.uidByte[1]) + String(mfrc522.uid.uidByte[2]) + String(mfrc522.uid.uidByte[3]) + String(mfrc522.uid.uidByte[4]) + String(mfrc522.uid.uidByte[5]) + String(mfrc522.uid.uidByte[6]) + String(mfrc522.uid.uidByte[7]) + String(mfrc522.uid.uidByte[8]) + String(mfrc522.uid.uidByte[9]);
  int txData3 = txData2.toInt();
  if (txData1 == txData3 && mfrc522.PICC_IsNewCardPresent()){
    Serial.println("wait");
    goto x3;
  }
  else{}
  txData1 = 0;
  streamer.sync("stalin",0);
}
```
# AI powered Reflow Oven
References:


