# Nova 인프라 모니터링 구성

N100 서버 환경을 위한 Prometheus, Grafana, Loki 기반 모니터링/로깅 설정입니다. 인프라 메트릭과 애플리케이션 로그를 통합 관리하는 것을 목적으로 합니다.

## 실행 방법

1. 환경 변수 설정
.env.example을 참고하여 .env 파일을 생성합니다

2. 컨테이너 실행
docker-compose up -d

## 구성 요소 및 포트 정보

### 메트릭 수집 (Prometheus)
- Prometheus (9095): 시계열 데이터 저장 및 쿼리
- Grafana (3000): 데이터 시각화
- Node Exporter (9100): 호스트 리소스(CPU, Memory, Disk) 상태
- cAdvisor (8080): 컨테이너별 리소스 사용량

### 로그 관리 (Loki)
- Loki (3100): 로그 데이터 저장소
- Promtail (9080): Docker 및 시스템 로그 수집기

### 데이터베이스 및 앱 익스포터
- MySQL Exporter (9104): MySQL 성능 지표
- Redis Exporter (9121): Redis 성능 지표
- Spring Boot Actuator: Micrometer 기반 앱 메트릭 (4001 포트 타겟)

## 관제 지표 설정

RED(Rate, Errors, Duration) 및 USE(Utilization, Saturation, Errors) 모델을 기준으로 관제합니다

1. 인프라 및 컨테이너
- CPU/Memory 포화도 및 사용률
- 디스크 잔여 용량 (DB 장애 방지용)
- 컨테이너 재시작 횟수 (OOM 감지)

2. 애플리케이션 (Spring Boot)
- RPS (초당 요청 수)
- 상태 코드별 에러율 (4xx, 5xx)
- API 응답 지연 시간 (Latency)
- JVM Heap 및 GC 수행 시간
- HikariCP 커넥션 풀 상태 (Active/Idle)

## 서버 환경 최적화 설정

- 네트워크: Docker 브리지 오버헤드를 줄이기 위해 모든 서비스에 host 네트워크 모드를 적용했습니다
- 데이터 보관 주기: 디스크 용량 관리를 위해 보관 기간을 제한했습니다
    - Prometheus: 15일
    - Loki: 31일
- 리소스 사용: 상시 구동 시 메모리 점유율을 약 1GB 내외로 유지하도록 설정했습니다

## Grafana 대시보드 참조 (Dashboard ID)

아래 ID를 사용하여 공식 대시보드를 임포트할 수 있습니다
- Node Exporter: 1860
- JVM (Micrometer): 4701
- cAdvisor: 14282
- MySQL: 14057
- Redis: 11835