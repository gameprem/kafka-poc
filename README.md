# Bus POC

ระบบนี้เป็น Proof of Concept (POC) สำหรับการเชื่อมต่อและมอนิเตอร์ message broker (Kafka, NanoMQ) พร้อมเครื่องมือสำหรับการตรวจสอบและแสดงผลข้อมูล (Prometheus, Grafana) โดยใช้ Docker Compose

## Service ที่มีในระบบ

- **Kafka**: ระบบ message broker สำหรับรับส่งข้อมูลแบบ pub/sub
- **Kafka UI**: Web UI สำหรับจัดการและตรวจสอบ Kafka
- **Prometheus**: ระบบมอนิเตอร์และเก็บ metrics จาก service ต่าง ๆ (เช่น Kafka)
- **Grafana**: Dashboard สำหรับแสดงผล metrics ที่ดึงมาจาก Prometheus
- **NanoMQ**: MQTT broker ที่สามารถเชื่อมต่อกับ Kafka ได้

## โครงสร้างไฟล์ที่สำคัญ

- `docker-compose.yml` : ไฟล์หลักสำหรับรัน service ต่าง ๆ
- `prometheus/prometheus.yml` : ไฟล์ config สำหรับ Prometheus

## วิธีเริ่มต้นใช้งาน

1. ติดตั้ง [Docker](https://www.docker.com/) และ [Docker Compose](https://docs.docker.com/compose/)
2. รันคำสั่งต่อไปนี้ใน root ของโปรเจกต์

```bash
docker-compose up -d
```

3. รอให้ service ต่าง ๆ start จนครบ

## การเข้าถึงแต่ละ Service

| Service     | URL/Port           | หมายเหตุ                |
|-------------|--------------------|-------------------------|
| Kafka       | 9092, 29092, 9093  | ใช้สำหรับ client และ admin |
| Kafka UI    | http://localhost:8081 | Web UI สำหรับ Kafka    |
| Prometheus  | http://localhost:9090 | Dashboard metrics      |
| Grafana     | http://localhost:3001 | User: admin / Pass: admin |
| NanoMQ (MQTT) | 1883 (MQTT), 8083 (WebSocket) | MQTT Broker |

## ตัวอย่างการตั้งค่า Prometheus

Prometheus จะ scrape metrics จาก Kafka ที่ port 9101 ตาม config ใน `prometheus/prometheus.yml`:

```yaml
global:
  scrape_interval: 15s

scrape_configs:
  - job_name: 'kafka'
    static_configs:
      - targets: ['kafka:9101']
```

## หมายเหตุ
- สามารถเพิ่ม/ปรับแต่ง service หรือ config ได้ตามต้องการในไฟล์ `docker-compose.yml` และ `prometheus/prometheus.yml`
- หากต้องการเก็บข้อมูล Grafana ถาวร จะมี volume `grafana-storage` ถูกสร้างขึ้นมาอัตโนมัติ

---

หากมีข้อสงสัยหรือปัญหาในการใช้งาน สามารถติดต่อผู้พัฒนาได้โดยตรง 