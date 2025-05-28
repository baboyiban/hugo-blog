---
title: 프로젝트 kosta-2
date: 2025-05-26
time: 16:24
tags: []
---

# 프로젝트 kosta-2

네, MQTT Publisher, Broker, Subscriber, 그리고 API Server를 연동하는 전체적인 예제를 보여드리겠습니다.

이 예제는 다음 구성 요소들을 포함합니다:

1.  **MQTT Broker**: Mosquitto (가장 대중적인 오픈소스 MQTT Broker)
2.  **MQTT Publisher**: Python 스크립트 (임베디드 장치 역할)
3.  **MQTT Subscriber (API Server 연동 로직)**: Python 스크립트 (Broker에서 메시지를 받아 API Server로 전달)
4.  **API Server**: Go Gin 프레임워크 (이전 예제에서 사용한 API 서버)

### 1단계: Mosquitto MQTT Broker 설치 및 실행

Mosquitto는 MQTT 연동을 위한 필수 요소입니다.

*   **설치**:
    *   **Ubuntu/Debian**: `sudo apt update && sudo apt install mosquitto mosquitto-clients`
    *   **macOS (Homebrew)**: `brew install mosquitto`
    *   **Windows**: Mosquitto 공식 웹사이트에서 설치 파일을 다운로드합니다.
*   **실행**:
    *   대부분의 OS에서 설치 후 자동으로 서비스로 실행됩니다.
    *   수동 실행: `mosquitto -c /etc/mosquitto/mosquitto.conf` (설정 파일 경로는 OS마다 다를 수 있습니다)
    *   기본 포트: 1883

Broker가 실행되는지 확인하는 가장 쉬운 방법은 Mosquitto 클라이언트를 사용하여 구독해보는 것입니다:
새로운 터미널을 열고 다음 명령어를 실행합니다:
```bash
mosquitto_sub -h localhost -p 1883 -t "sensor/temperature"
```
이제 이 터미널은 `sensor/temperature` 토픽으로 오는 메시지를 기다리게 됩니다.

### 2단계: Go API Server (이전 예제와 동일)

이전 질문에서 제공된 Go API 서버 코드를 그대로 사용합니다. 이 서버는 `/api/sensor_data`와 같은 엔드포인트를 제공하여 센서 데이터를 받을 것입니다.

**API Server 코드 수정 (New Endpoint for Sensor Data):**

`main.go` 파일에 `SensorData` 모델과 해당 `createHandler`를 추가해야 합니다.

먼저, `SensorData` 구조체를 정의합니다:

```go
// main.go 파일 내
type SensorData struct {
	gorm.Model
	SensorID       string    `gorm:"type:varchar(50);not null"`
	Temperature    float64   `gorm:"not null"`
	Humidity       float64   `gorm:"not null"`
	Timestamp      time.Time `gorm:"not null"`
}
```

이제 `initDB` 함수에서 `SensorData`를 `AutoMigrate`에 추가합니다:

```go
func initDB() *gorm.DB {
	dsn := getDSN()
	db, err := gorm.Open(mysql.Open(dsn), &gorm.Config{})
	if err != nil {
		log.Fatalf("데이터베이스 연결 실패: %v", err)
	}
	err = db.AutoMigrate(
		&Product{},
		&Zone{},
		&Vehicle{},
		&OperationRecord{},
		&OperationProduct{},
		&SensorData{}, // --- 이 줄 추가 ---
	)
	if err != nil {
		log.Fatalf("데이터베이스 마이그레이션 실패: %v", err)
	}
	log.Println("데이터베이스 연결 및 마이그레이션 성공")
	return db
}
```

마지막으로 `main` 함수에서 `SensorData` 엔드포인트를 등록합니다:

```go
func main() {
	db := initDB()
	router := gin.Default()

	api := router.Group("/api")
	{
		// ... 기존 라우터들 ...

		// Sensor Data Endpoint
		api.POST("/sensor_data", createHandler[SensorData](db)) // --- 이 줄 추가 ---
		api.GET("/sensor_data", getAllHandler[SensorData](db))  // --- 이 줄 추가 ---
	}

	port := ":8080"
	log.Printf("서버가 %s 포트에서 실행 중...", port)
	if err := router.Run(port); err != nil {
		log.Fatalf("서버 실행 실패: %v", err)
	}
}
```

Go API 서버를 실행합니다:
```bash
go run main.go
```

### 3단계: MQTT Publisher (Python) - 임베디드 장치 역할

이 스크립트는 가상의 센서 데이터를 생성하여 MQTT Broker로 발행합니다.

필요한 라이브러리 설치: `pip install paho-mqtt`

`mqtt_publisher.py`:

```python
import paho.mqtt.client as mqtt
import time
import json
import random

# MQTT Broker 설정
BROKER_ADDRESS = "localhost"
BROKER_PORT = 1883
TOPIC = "sensor/temperature"

# MQTT 클라이언트 초기화
client = mqtt.Client()

def on_connect(client, userdata, flags, rc):
    if rc == 0:
        print("MQTT Broker에 연결되었습니다!")
    else:
        print(f"연결 실패, 에러 코드: {rc}")

client.on_connect = on_connect

# 브로커에 연결
print(f"MQTT Broker ({BROKER_ADDRESS}:{BROKER_PORT})에 연결 시도...")
client.connect(BROKER_ADDRESS, BROKER_PORT, 60)
client.loop_start() # 백그라운드 스레드에서 네트워크 루프 시작

try:
    sensor_id = "ESP32_001"
    print(f"센서 {sensor_id}로부터 데이터 발행 시작...")
    while True:
        temperature = round(random.uniform(20.0, 30.0), 2) # 20.0 ~ 30.0 랜덤 온도
        humidity = round(random.uniform(40.0, 60.0), 2)    # 40.0 ~ 60.0 랜덤 습도
        timestamp = int(time.time() * 1000) # 밀리초 단위 타임스탬프

        payload = {
            "sensorID": sensor_id,
            "temperature": temperature,
            "humidity": humidity,
            "timestamp": timestamp
        }
        
        # 메시지 발행
        client.publish(TOPIC, json.dumps(payload))
        print(f"'{TOPIC}' 토픽 발행: {json.dumps(payload)}")
        time.sleep(5) # 5초마다 데이터 발행
except KeyboardInterrupt:
    print("발행 중단.")
finally:
    client.loop_stop() # 네트워크 루프 중지
    client.disconnect()
    print("MQTT 연결 해제.")

```
`python mqtt_publisher.py` 실행. Mosquitto 클라이언트(`mosquitto_sub`) 터미널에서 발행되는 메시지를 확인할 수 있을 겁니다.

### 4단계: MQTT Subscriber (Python) - API Server 연동 로직 역할

이 스크립트는 MQTT Broker로부터 메시지를 구독하고, 수신된 메시지를 Go API 서버로 HTTP POST 요청을 통해 전달합니다.

필요한 라이브러리 설치: `pip install paho-mqtt requests`

`mqtt_api_integrator.py`:

```python
import paho.mqtt.client as mqtt
import requests
import json
import datetime

# MQTT Broker 설정
BROKER_ADDRESS = "localhost"
BROKER_PORT = 1883
TOPIC = "sensor/temperature"

# API Server 설정
API_SERVER_URL = "http://localhost:8080/api/sensor_data"

# MQTT 클라이언트 초기화
client = mqtt.Client()

def on_connect(client, userdata, flags, rc):
    if rc == 0:
        print("MQTT Broker에 연결되었습니다!")
        client.subscribe(TOPIC) # 연결 성공 시 토픽 구독
        print(f"토픽 '{TOPIC}' 구독 시작.")
    else:
        print(f"연결 실패, 에러 코드: {rc}")

def on_message(client, userdata, msg):
    print(f"메시지 수신 - 토픽: {msg.topic}, 페이로드: {msg.payload.decode()}")
    
    try:
        data = json.loads(msg.payload.decode())
        
        # API 서버로 보낼 데이터 형식 변환 (Go 모델에 맞게)
        # Go의 time.Time은 ISO 8601 형식 문자열을 잘 처리합니다.
        # Python timestamp는 초 단위이므로, Go의 time.Time으로 변환하기 쉽도록 ISO 8601 문자열로 변환하는 것이 일반적입니다.
        # 고의 float64, Go의 string에 맞게.
        
        # timestamp가 밀리초 단위로 가정하고 초 단위로 변환 후 datetime 객체 생성
        if 'timestamp' in data:
            dt_object = datetime.datetime.fromtimestamp(data['timestamp'] / 1000.0)
            data['timestamp'] = dt_object.isoformat() + "Z" # GORM이 UTC 시간을 기대하므로 'Z' 추가
        else:
            data['timestamp'] = datetime.datetime.utcnow().isoformat() + "Z"

        # 필드 이름 매핑
        api_payload = {
            "SensorID": data.get("sensorID"),
            "Temperature": data.get("temperature"),
            "Humidity": data.get("humidity"),
            "Timestamp": data.get("timestamp")
        }

        # API 서버로 POST 요청 보내기
        print(f"API 서버로 POST 요청 전송: {json.dumps(api_payload)}")
        response = requests.post(API_SERVER_URL, json=api_payload)
        
        if response.status_code == 201:
            print("데이터가 API 서버에 성공적으로 전송되었습니다.")
            # print(response.json()) # API 서버 응답 확인
        else:
            print(f"API 서버 전송 실패. 상태 코드: {response.status_code}, 응답: {response.text}")
            
    except json.JSONDecodeError:
        print("JSON 디코딩 오류: 유효하지 않은 페이로드입니다.")
    except requests.exceptions.RequestException as e:
        print(f"API 서버 요청 중 오류 발생: {e}")
    except Exception as e:
        print(f"예상치 못한 오류 발생: {e}")


client.on_connect = on_connect
client.on_message = on_message

# 브로커에 연결
print(f"MQTT Broker ({BROKER_ADDRESS}:{BROKER_PORT})에 연결 시도...")
client.connect(BROKER_ADDRESS, BROKER_PORT, 60)

# 네트워크 루프를 무한정 실행하여 메시지 대기
client.loop_forever()
```
`python mqtt_api_integrator.py` 실행.

### 전체 시스템 동작 시나리오:

1.  **Mosquitto Broker**가 실행됩니다 (`mosquitto`).
2.  **Go API Server**가 실행됩니다 (`go run main.go`). `/api/sensor_data` 엔드포인트를 통해 센서 데이터를 받을 준비를 합니다.
3.  **MQTT Publisher** (`mqtt_publisher.py`)가 실행됩니다. 5초마다 가상의 센서 데이터를 JSON 형식으로 만들어 `sensor/temperature` 토픽으로 MQTT Broker에 발행합니다.
4.  **MQTT Subscriber (API Integrator)** (`mqtt_api_integrator.py`)가 실행됩니다. `sensor/temperature` 토픽을 구독하여 MQTT Broker로부터 Publisher가 보낸 메시지를 실시간으로 수신합니다.
5.  Subscriber는 수신된 MQTT 메시지(JSON 페이로드)를 파싱하고, Go API 서버의 `SensorData` 모델에 맞는 형식으로 데이터를 준비합니다.
6.  Subscriber는 준비된 데이터를 Go API 서버의 `/api/sensor_data` 엔드포인트로 HTTP POST 요청을 보냅니다.
7.  Go API 서버는 이 POST 요청을 받아 데이터를 데이터베이스에 저장합니다. 저장 성공 시 201 Created 응답을 Subscriber에게 보냅니다.

이제 Postman이나 cURL을 사용하여 Go API 서버의 `/api/sensor_data` 엔드포인트에 `GET` 요청을 보내면 데이터베이스에 저장된 센서 데이터 목록을 확인할 수 있습니다:

```bash
curl http://localhost:8080/api/sensor_data
```

이 예제는 임베디드 장치와 클라우드/API 서버 간의 MQTT 기반 데이터 파이프라인의 기본적인 형태를 보여줍니다. 실제 프로덕션 환경에서는 보안(TLS/SSL, 인증), 에러 처리, 재시도 로직, 고가용성 등을 위한 더 많은 고려가 필요합니다.