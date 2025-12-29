# Linux Kernel Driver 기반 스마트 기상 시계 & 불쾌지수 측정기
> **Linux Kernel Device Driver Project (Intel AI SW 8기)**

본 프로젝트는 리눅스 커널 드라이버를 직접 설계하여 하드웨어를 제어하고, 실시간 시계(RTC), 온습도 데이터 처리 및 불쾌지수(THI) 시각화를 구현한 임베디드 시스템 프로젝트입니다.

---

## 프로젝트 개요
단순히 라이브러리를 사용하는 수준을 넘어, **커널 공간(Kernel Space)**에서 직접 GPIO 인터럽트를 핸들링하고 디바이스 드라이버를 작성하여 **유저 공간(User Space)** 애플리케이션과 데이터를 주고받는 전체 프로세스를 구현했습니다.

## 주요 하드웨어 구성
- **Main Board:** Raspberry Pi 4 (64-bit Kernel)
- **Sensors:** - `DHT11`: 온습도 데이터 수집
  - `DS1302`: 실시간 시간 정보(RTC) 제공
  - `Rotary Encoder`: 페이지 전환 및 시간 설정 인터페이스
- **Display & Output:**
  - `OLED (I2C)`: 정보 출력 (시간, 온습도, 불쾌지수)
  - `8-Channel LED`: 불쾌지수 단계별 시각화

## 시스템 아키텍처
1. **Kernel Space (`driver.c`)**:
   - GPIO 제어 및 인터럽트(IRQ) 핸들링 (로터리 엔코더 디바운싱 처리 등)
   - DS1302 및 DHT11 제어 로직 구현
   - `ioctl` 및 `read/write` 인터페이스 제공
2. **User Space (`application.c`)**:
   - 커널 드라이버로부터 데이터를 받아 OLED 화면 구성
   - 불쾌지수(THI) 산출 및 LED 레벨 제어 로직 실행

## 핵심 기술 포인트
- **Interrupt Handling & Debouncing**: 로터리 엔코더의 기계적 떨림 현상을 커널 내에서 타이머를 활용해 방지.
- **Context Management**: 뮤텍스를 사용하여 커널 내 공유 자원 보호.
- **Custom Character Device**: 시스템 고유의 메이저 번호를 할당받아 장치 파일 생성.
- **I2C & GPIO Protocol**: 센서별 통신 프로토콜을 드라이버 레벨에서 직접 제어.

## 파일 구조
- `driver.c`: 리눅스 커널 모듈 소스 코드
- `application.c`: 유저 애플리케이션 (OLED 및 메인 로직)
- `Makefile`: 커널 빌드 환경(`ARCH=arm64`) 설정

## 실행 방법
1. **커널 모듈 빌드 및 로드**
   ```bash
   make
   sudo insmod driver.ko
   ```
2. **애플리케이션 실행**
   ```
   gcc -o app application.c
   sudo ./app
   ```
3. **팀원**
   김동현, 서채건, 박서정
   
