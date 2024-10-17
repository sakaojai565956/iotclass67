# Store data.
จะทำการดึงข้อมูลจาก Kafka ไปที่ MongoDB ซึ่งเป็น Database รูปแบบของ NoSql เพื่อให้สามารถใช้ข้อมูลเหล่านี้ได้ในภายหลัง

#การทำงาน

โดยในการรับส่งข้อมูลนั้นมีจะส่งผ่าน Kafka connect ที่จะถูกเชื่อมต่อกับส่วนต่างๆ MQTT, Prometheus และ MongoDB ซึ่ง Kafka จะเป็นตัวจัดการ จะมี 3 collection สำหรับ MongoDB มีหลักการทำงานดังนี้

รับข้อมูลจาก iot_frames

ส่งข้อมูลไปที่ Collection ที่กำหนด โดย ใช้ iot_frames ลงใน Database IOT

ตัวอย่างการทำงาน

```bash


{
   "name":"iot-frames-mongodb-sink",
   "config":{
      "connector.class":"com.mongodb.kafka.connect.MongoSinkConnector",
      "tasks.max":1,
      "topics":"iot-frames",
      "connection.uri":"mongodb://devroot:devroot@mongo:27017",
      "database":"iot",
      "collection":"iot_frames",
      "value.converter": "org.apache.kafka.connect.json.JsonConverter",
      "key.converter": "org.apache.kafka.connect.json.JsonConverter",
      "value.converter.schemas.enable": false,
      "key.converter.schemas.enable":false
   }
}
```
ในส่วนเชื่อมต่อที่ 2 ทำการตั้งค่าเพื่อเก็บข้อมูลที่ได้รับจาก "iot-aggregate-metric-sensor" ไปที่ Collection "iot_aggregate_metric_sensor"

```bash
{
   "name":"iot-aggregate-metrics-sensor-mongodb-sink",
   "config":{
      "connector.class":"com.mongodb.kafka.connect.MongoSinkConnector",
      "tasks.max":1,
      "topics":"iot-aggregate-metrics-sensor",
      "connection.uri":"mongodb://devroot:devroot@mongo:27017",
      "database":"iot",
      "collection":"iot_aggregate_metrics_sensor",
      "value.converter": "org.apache.kafka.connect.json.JsonConverter",
      "key.converter": "org.apache.kafka.connect.storage.StringConverter",
      "value.converter.schemas.enable": false,
      "key.converter.schemas.enable":false
   }
}
```

ในส่วนที่ 3: ส่งข้อมูลตามสถานที่ (place) จาก Kafka ไปยัง MongoDB
ในกรณีนี้ Kafka topic ที่ใช้คือ iot-aggregate-metrics-place ซึ่งจะทำการเก็บข้อมูลไปยังคอลเล็กชัน iot_aggregate_metrics_place


```bash

{
   "name":"iot-aggregate-metrics-place-mongodb-sink",
   "config":{
      "connector.class":"com.mongodb.kafka.connect.MongoSinkConnector",
      "tasks.max":1,
      "topics":"iot-aggregate-metrics-place",
      "connection.uri":"mongodb://devroot:devroot@mongo:27017",
      "database":"iot",
      "collection":"iot_aggregate_metrics_place",
      "value.converter": "org.apache.kafka.connect.json.JsonConverter",
      "key.converter": "org.apache.kafka.connect.storage.StringConverter",
      "value.converter.schemas.enable": false,
      "key.converter.schemas.enable":false
   }
}
```

การเชื่อมต่อ Kafka กับ Prometheus
Prometheus เป็นระบบที่ใช้ในการเก็บข้อมูลแบบ time-series ซึ่งเหมาะสำหรับการติดตามค่าต่างๆ ที่เปลี่ยนแปลงตามเวลา เช่น ข้อมูลจาก IoT metrics ดังนั้นการเชื่อมต่อกับ Prometheus นั้นจะทำให้เราสามารถเก็บข้อมูลจาก Kafka ในรูปแบบที่สามารถใช้ในการสังเกตการณ์ (monitoring) ได้อย่างมีประสิทธิภาพ

ในที่นี้เราใช้ PrometheusMetricsSinkConnector เพื่อส่งข้อมูลจาก Kafka topic ที่ชื่อ iot-metrics-time-series ไปเก็บไว้ใน Prometheus
ในส่วนของข้อมูลที่เป็น Time Serires จะต้องเก็บข้อมูลไปที่ Prometheus โดยจะดึงข้อมูลจาก Topic "iot-metric-time-series" โดย Prometheus ดึงข้อมูลผ่าน HTTP server



```bash
{
  "name" : "prometheus-connector-sink",
  "config" : {
   "topics":"iot-metrics-time-series",
   "connector.class" : "io.confluent.connect.prometheus.PrometheusMetricsSinkConnector",
   "tasks.max" : "1",
   "confluent.topic.bootstrap.servers":"kafka:9092",
   "prometheus.scrape.url": "http://0.0.0.0:8084/iot-metrics-time-series",
   "prometheus.listener.url": "http://0.0.0.0:8084/iot-metrics-time-series",
   "value.converter": "org.apache.kafka.connect.json.JsonConverter",
   "key.converter": "org.apache.kafka.connect.json.JsonConverter",
   "value.converter.schemas.enable": false,
   "key.converter.schemas.enable":false,
   "reporter.bootstrap.servers": "kafka:9092",
   "reporter.result.topic.replication.factor": "1",
   "reporter.error.topic.replication.factor": "1",
   "behavior.on.error": "log"
  }
}
```
