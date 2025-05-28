---
title: MQTT 근데 보통 라즈베리파이와 통신하는 API 서버는 어떻게 만들어?
date: 2025-05-21
time: 16:00
tags: []
---

# MQTT 근데 보통 라즈베리파이와 통신하는 API 서버는 어떻게 만들어?

좋은 질문이에요!  
**라즈베리파이(Raspberry Pi)와 통신하는 API 서버를 만들 때는**, 일반적인 웹 애플리케이션보다 **안정성, 보안, 네트워크 환경, 데이터 포맷, 프로토콜 등**을 더 신경 써야 합니다.

---

## 🧱 기본 개념: 라즈베리파이 ↔ API 서버 통신 구조

```
라즈베리파이 (센서 + MQTT 또는 HTTP 클라이언트)
         ↓
네트워크 (Wi-Fi / 이더넷 / LTE 등)
         ↓
API 서버 (Go + Gin + GORM 등)
         ↓
MySQL / PostgreSQL 등 DB 저장
         ↓
웹/앱에서 조회 가능
```

---

## ✅ 1. API 서버 설계 방향

| 항목 | 설명 |
|------|------|
| **통신 방식** | HTTP REST API or MQTT or WebSocket 등 |
| **보안** | HTTPS, 인증(Token/JWT), IP 제한 등 |
| **데이터 포맷** | JSON 사용 권장 |
| **에러 처리** | 정확한 HTTP 상태 코드 반환 |
| **로그 기록** | 요청 로그, 오류 로그 남기기 |

---

## 📡 2. 라즈베리파이와 주로 사용하는 통신 방식

### 1) **HTTP REST API**

- 가장 간단하고 직관적
- 라즈베리파이에서 `curl`, Python `requests` 등을 이용해 POST 요청 전송

#### 예시 (Python):

```python
import requests
import json

url = "http://api.yourserver.com/data"
data = {
    "device_id": "raspi-01",
    "temperature": 25.5,
    "humidity": 60.3
}

response = requests.post(url, json=data)
print(response.status_code)
```

#### Go(Gin) API 예시:

```go
r.POST("/data", func(c *gin.Context) {
	var payload struct {
		DeviceID    string  `json:"device_id"`
		Temperature float32 `json:"temperature"`
		Humidity    float32 `json:"humidity"`
	}

	if err := c.ShouldBindJSON(&payload); err != nil {
		c.JSON(http.StatusBadRequest, gin.H{"error": err.Error()})
		return
	}

	db.Create(&models.SensorData{
		DeviceID:    payload.DeviceID,
		Temperature: payload.Temperature,
		Humidity:    payload.Humidity,
	})

	c.JSON(http.StatusOK, gin.H{"status": "success"})
})
```

---

### 2) **MQTT 통신 (추천)**

- 실시간 센서 데이터 수신 시 유리
- 라즈베리파이가 발행(publish), 서버가 구독(subscribe)

#### 라즈베리파이에서 예시 (Python):

```python
import paho.mqtt.client as mqtt
import json

client = mqtt.Client()
client.connect("your.server.ip", 1883)

data = {
    "device_id": "raspi-01",
    "temperature": 25.5,
    "humidity": 60.3
}

client.publish("sensor/data", json.dumps(data))
```

#### Go 서버에서 수신 (MQTT Subscriber):

```go
func StartMQTTSubscriber(db *gorm.DB, broker, topic string) {
	opts := paho_mqtt_golang.NewClientOptions().AddBroker(broker)
	client := paho_mqtt_golang.NewClient(opts)
	client.Connect()

	token := client.Subscribe(topic, 1, func(client paho_mqtt_golang.Client, msg paho_mqtt_golang.Message) {
		var data map[string]interface{}
		json.Unmarshal(msg.Payload(), &data)

		sensor := models.SensorData{
			DeviceID:    data["device_id"].(string),
			Temperature: float32(data["temperature"].(float64)),
			Humidity:    float32(data["humidity"].(float64)),
		}
		db.Create(&sensor)
	})
	token.Wait()
}
```

---

### 3) **WebSocket** (선택사항)

- 실시간 양방향 통신 필요 시
- 예: 서버 → 라즈베리파이로 커맨드 전달 (예: LED 켜짐/꺼짐 명령)

---

## 🔐 3. 보안 관련 고려 사항

| 항목 | 설명 |
|------|------|
| **HTTPS** | 반드시 TLS 적용 (`Let's Encrypt`) |
| **인증** | JWT 토큰이나 API Key로 접근 제어 |
| **IP 제한** | 특정 IP(라즈베리파이의 공인 IP)만 접근 허용 |
| **Rate Limiting** | 과다 요청 차단 |
| **로그 기록** | 누가 언제 어떤 데이터를 보냈는지 기록 |

---

## 🌐 4. 네트워크 설정 주의점

| 항목 | 설명 |
|------|------|
| **공인 IP vs 내부망** | 외부에서 접근 가능해야 함 |
| **방화벽 설정** | API 포트(`80`, `443`), MQTT 포트(`1883`, `8883`) 열기 |
| **DDNS** | 공인 IP가 변하는 경우 도메인으로 고정 관리 가능 |
| **NAT 설정** | 라우터에서 포트 포워딩 필요할 수 있음 |

---

## 📦 5. 운영 환경 구성 추천

> 아래는 Docker + Traefik + Go + MySQL 구성입니다.

```yaml
version: '3'

services:
  traefik:
    image: traefik:v2.9
    command:
      - --api.insecure=true
      - --providers.docker
      - --entrypoints.web.address=:80
      - --entrypoints.websecure.address=:443
      - --certificatesresolvers.myresolver.acme.tlschallenge
      - --certificatesresolvers.myresolver.acme.email=you@example.com
      - --certificatesresolvers.myresolver.acme.storage=/letsencrypt/acme.json
    ports:
      - "80:80"
      - "443:443"
      - "8080:8080" # Traefik Dashboard
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock
      - ./letsencrypt:/letsencrypt

  api-server:
    build: .
    labels:
      - "traefik.http.routers.api.rule=Host(`api.example.com`)"
      - "traefik.http.routers.api.entrypoints=websecure"
      - "traefik.http.routers.api.tls.certresolver=myresolver"
      - "traefik.http.services.api.loadbalancer.server.port=8080"

  mysql:
    image: mysql:8.0
    environment:
      MYSQL_ROOT_PASSWORD: password
      MYSQL_DATABASE: sensordb
    volumes:
      - db_data:/var/lib/mysql

volumes:
  db_data:
```

---

## ✅ 요약

| 항목 | 추천 방법 |
|------|-----------|
| **통신 방식** | MQTT or HTTP |
| **데이터 포맷** | JSON |
| **보안** | HTTPS + API Key or JWT |
| **운영** | Docker + Traefik + Let's Encrypt |
| **DB 저장** | GORM 활용하여 MySQL 등에 저장 |
| **모니터링** | 로그, 대시보드(Traefik 제공), Prometheus 연동도 가능 |

---

필요하다면 아래 내용도 도와드릴게요 😊

- 라즈베리파이 센서 연결 방법 (GPIO, I2C 등)
- MQTT Broker(Docker Mosquitto) 설치 가이드
- JWT 기반 인증 추가 방법
- 전체 Docker Compose + Go 예제

어떤 걸 먼저 해볼까요?