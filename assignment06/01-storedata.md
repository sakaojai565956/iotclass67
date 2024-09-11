# Store data.
จะทำการดึงข้อมูลจาก Kafka ไปที่ MongoDB ซึ่งเป็น Database รูปแบบของ NoSql เพื่อให้สามารถใช้ข้อมูลเหล่านี้ได้ในภายหลัง

#การทำงาน

โดยในการรับส่งข้อมูลนั้นมีจะส่งผ่าน Kafka connect ที่จะถูกเชื่อมต่อกับส่วนต่างๆ MQTT, Prometheus และ MongoDB ซึ่ง Kafka จะเป็นตัวจัดการ จะมี 3 collection สำหรับ MongoDB มีหลักการทำงานดังนี้

รับข้อมูลจาก iot_frames

ส่งข้อมูลไปที่ Collection ที่กำหนด โดย ใช้ iot_frames ลงใน Database IOT

File Path: kafka_connect/data/scripts/config/connect-mongodb-iot-frames-sink.json

bash


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
ในส่วนเชื่อมต่อที่ 2 เป็นการดึงข้อมูลจาก "iot-aggregate-metric-sensor" ไปที่ Collection "iot_aggregate_metric_sensor"

File Path: kafka_connect/data/scripts/config/connect-mongodb-iot-aggregate-metrics-sensor-sink.json
bash
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

ส่วนสุดท้่ายเป็นการดึงข้อมูลจาก Topic "iot-aggregate-metric-place" ไปที่ Collection "iot_aggreagate_metric_place"

File Path: kafka_connect/data/scripts/config/connect-mongodb-iot-aggregate-metrics-place-sink.json

bash

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

ในส่วนของข้อมูลที่เป็น Time Serires จะต้องเก็บข้อมูลไปที่ Prometheus โดยจะดึงข้อมูลจาก Topic "iot-metric-time-series" โดย Prometheus ดึงข้อมูลผ่าน HTTP server

File Path: kafka_connect/data/scripts/config/connect-prometheus-sink.json

bash
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
