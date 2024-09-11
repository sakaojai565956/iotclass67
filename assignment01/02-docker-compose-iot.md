# IoT Docker compose
>> ให้นำไฟล์ docker-compose.yaml มาอธิบายว่า แต่ละส่วนคืออะไร โดยใช้การ comment ในไฟล์ docker-compose.yaml

ติดตั้ง Docker
https://www.docker.com/
ไปที่ลิ้งdocker แล้วกดโหลดตามที่เราใช้งาน กลุ่มเราโหลดบนwindow 
ติดตั้ง docker compose ต้องติดตั้งdocker destop  ด้วย ใช้คำสั่ง 
sudo curl -L "https://github.com/docker/compose/releases/download/1.29.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose

สร้างไฟล์'docker-compose.yml':  เปิดใช้งานใน vs code
version: '3.8'

services:
  mqtt-broker:
    image: eclipse-mosquitto
    ports:
      - "1883:1883"
      - "9001:9001"

  node-red:
    image: nodered/node-red
    ports:
      - "1880:1880"
    volumes:
      - node_red_data:/data

volumes:
  node_red_data:
 
 ระบุบริการการใช้งาน


## start-service #0
>> อธิบายว่า  หน้าจอที่ 1 ที่ต้องเปิดใช้งาน มีการเปิด service อะไรบ้างใน docker

start_0zookeeper_kafka.sh

Zookeeper ซึ่งเป็นเซิร์ฟเวอร์สำหรับการจัดการการประสานงานระหว่างโหนดใน Kafka cluster. Zookeeper ใช้เพื่อเก็บสถานะของ Kafka brokers และช่วยในการจัดการกระบวนการต่าง ๆ ของ Kafka

## start-service #1
>> อธิบายว่า  หน้าจอที่ 1 ที่ต้องเปิดใช้งาน มีการเปิด service อะไรบ้างใน docker

start_1kafka_service.sh 

เริ่มต้น Kafka service ซึ่งเป็นระบบที่ใช้จัดการคิวข้อความ (message queue) สำหรับส่งและรับข้อมูลที่ถูกส่งมาจาก IoT sensor หรือโปรแกรมอื่นๆ. Kafka จะจัดการการรับส่งข้อมูลอย่างมีประสิทธิภาพและรองรับการประมวลผลข้อมูลในปริมาณมาก

## start-service #2
>> อธิบายว่า  หน้าจอที่ 1 ที่ต้องเปิดใช้งาน มีการเปิด service อะไรบ้างใน docker

start_2iot_processor.sh  

ใช้สำหรับเริ่มต้น IoT processor ซึ่งเป็นส่วนที่รับข้อมูลจาก Kafka แล้วประมวลผลข้อมูลที่ได้จาก IoT sensor ข้อมูลที่ประมวลผลแล้วอาจจะถูกส่งต่อไปยังระบบอื่นเพื่อจัดเก็บหรือใช้ในการวิเคราะห์เพิ่มเติม


## start-service #3
>> อธิบายว่า  หน้าจอที่ 1 ที่ต้องเปิดใช้งาน มีการเปิด service อะไรบ้างใน docker

start_3iot_sensor.sh

สคริปต์นี้น่าจะใช้สำหรับการเริ่มต้น IoT sensor ซึ่งอาจเป็นซอฟต์แวร์จำลองการทำงานของอุปกรณ์ IoT ที่ทำหน้าที่ส่งข้อมูล (เช่น อุณหภูมิและความเข้มของแสง) ไปยัง Kafka ผ่าน broker
