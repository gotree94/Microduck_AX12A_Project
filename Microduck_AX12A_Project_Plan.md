# Microduck-AX12A 자작 프로젝트 계획

## 1. 프로젝트 개요

이 프로젝트는 기존 **Microduck**의 구조와 소프트웨어를 분석한 뒤, **ROBOTIS AX-12A**를 액추에이터로 사용하도록 기구를 재설계하고, 3D 프린팅으로 실제 로봇을 제작한 후 **MuJoCo 기반 시뮬레이션 → 강화학습 → Sim2Real**까지 연결하는 것을 목표로 한다.

단순히 기존 Microduck을 복제하는 것이 아니라,

> **Microduck의 운동학적 구조와 보행 알고리즘을 참고하면서 AX-12A에 맞는 새로운 기구와 제어 시스템을 설계하는 프로젝트**

로 정의한다.

---

## 2. 전체 개발 흐름

```text
┌──────────────────────────────┐
│ ① Original Microduck 조사    │
│ CAD / MJCF / BOM / SW 분석   │
└──────────────┬───────────────┘
               ↓
┌──────────────────────────────┐
│ ② AX-12A 적용성 분석         │
│ Torque / Size / Speed / Bus  │
└──────────────┬───────────────┘
               ↓
┌──────────────────────────────┐
│ ③ 기구 재설계                │
│ FreeCAD / STEP / STL         │
└──────────────┬───────────────┘
               ↓
┌──────────────────────────────┐
│ ④ 구조 / 동역학 해석         │
│ FEA + Kinematics + Dynamics │
└──────────────┬───────────────┘
               ↓
┌──────────────────────────────┐
│ ⑤ 3D Printing & Assembly     │
│ PLA/PETG/ABS + AX-12A        │
└──────────────┬───────────────┘
               ↓
┌──────────────────────────────┐
│ ⑥ Low-level Robot Control    │
│ MCU / SBC / DYNAMIXEL        │
└──────────────┬───────────────┘
               ↓
┌──────────────────────────────┐
│ ⑦ MuJoCo → RL → Sim2Real     │
│ PPO / ONNX / Real Robot      │
└──────────────────────────────┘
```

---

# 3. ① Original Microduck 자료 조사

## 3.1 조사 대상

먼저 기존 Microduck 관련 자료를 최대한 확보한다.

### 조사 항목

- 공식 Microduck 저장소
- Microduck hardware
- Microduck runtime
- Microduck RL
- MJCF 모델
- STL/STEP/CAD 자료
- BOM
- 전자회로
- Servo configuration
- IMU
- 제어 주기
- 보행 알고리즘
- PPO 학습 환경
- ONNX deployment
- 기존 Replica 프로젝트

---

## 3.2 원본 Microduck의 기본 구조

Microduck은 약 25 cm 크기의 소형 2족 로봇이며, 여러 개의 Dynamixel 서보를 이용하여 몸체와 양쪽 다리를 제어한다.

소프트웨어 측면에서는 대략 다음과 같은 구조를 갖는다.

```text
15 Servos
    │
    ▼
Dynamixel Bus
    │
    ▼
50 Hz Control Loop
    │
    ▼
Robot State / IMU
    │
    ▼
Walking Policy
    │
    ▼
Joint Target
```

---

# 4. ② AX-12A 적용성 분석

## 4.1 AX-12A 주요 사양

| 항목 | AX-12A |
|---|---:|
| 크기 | 약 32 × 50 × 40 mm |
| 무게 | 약 54.6 g |
| 동작 전압 | 9 ~ 12 V |
| 권장 전압 | 11.1 V |
| Stall Torque | 약 1.5 N·m |
| 무부하 속도 | 약 59 rpm |
| 위치 범위 | 0 ~ 300° |
| 위치 분해능 | 약 0.29° |
| 통신 | TTL Half Duplex |
| Protocol | Dynamixel Protocol 1.0 |
| ID | 0 ~ 253 |
| 피드백 | Position / Temperature / Load / Voltage 등 |

> 실제 설계에서는 Stall Torque를 그대로 설계 허용 토크로 사용하지 않고 충분한 안전 여유를 둔다.

---

## 4.2 AX-12A와 원본 액추에이터 비교

원본 Microduck과 AX-12A는 크기, 질량, 토크, 속도, 통신 프로토콜 등이 다르다.

따라서 원본 CAD와 제어 정책을 그대로 사용하는 것은 적절하지 않다.

```text
Original Microduck
        │
        ├── Original Servo
        ├── Original Frame
        ├── Original Mass
        └── Original Dynamics
```

를 다음과 같이 변경한다.

```text
Microduck-AX12A
        │
        ├── AX-12A
        ├── New Servo Bracket
        ├── Modified Leg
        ├── Modified Body
        └── Modified Mass Distribution
```

핵심은 **액추에이터를 바꾸면서 기구학과 동역학을 다시 맞추는 것**이다.

---

# 5. ③ 기구 설계

## 5.1 CAD 설계 도구

권장 CAD workflow:

```text
Reference CAD
     ↓
STEP
     ↓
FreeCAD
     ↓
Parametric Model
     ↓
Assembly
     ↓
STL
     ↓
3D Printer
```

가능하면 STL 파일을 직접 수정하지 않고 **STEP/Parametric CAD를 기준 모델**로 유지한다.

---

## 5.2 프로젝트 폴더 구조

```text
Microduck_AX12A/
│
├── 00_reference/
│   ├── microduck_original/
│   ├── papers/
│   ├── datasheets/
│   └── github/
│
├── 01_reverse_engineering/
│   ├── dimensions/
│   ├── joints/
│   ├── mass/
│   └── kinematics/
│
├── 02_cad/
│   ├── original/
│   ├── ax12a_mount/
│   ├── body/
│   ├── leg/
│   └── assembly/
│
├── 03_analysis/
│   ├── static/
│   ├── modal/
│   ├── stress/
│   └── dynamics/
│
├── 04_print/
│   ├── prototype/
│   └── final/
│
├── 05_firmware/
│   ├── ax12a_test/
│   ├── servo_calibration/
│   └── gait/
│
├── 06_simulation/
│   ├── mujoco/
│   ├── mjcf/
│   └── rl/
│
└── 07_docs/
    ├── README.md
    ├── BOM.md
    ├── CAD.md
    └── CHANGELOG.md
```

---

# 6. ④ AX-12A Mount부터 제작

처음부터 로봇 전체를 설계하지 않는다.

먼저 **AX-12A 하나를 정확하게 CAD 모델링**한다.

```text
AX-12A
   │
   ▼
3D CAD
   │
   ▼
Servo Bracket
   │
   ▼
3D Print
   │
   ▼
실제 AX-12A 장착
```

확인해야 할 사항:

- 나사 위치
- 축 위치
- 회전 중심
- 회전 범위
- 케이블 간섭
- 주변 부품과의 간섭
- 나사 조립성
- 3D 프린터 출력 방향
- 프레임 강성

---

# 7. ⑤ 한쪽 다리부터 개발

전체 로봇을 한 번에 만들지 않고 **한쪽 다리 하나를 먼저 완성**한다.

예:

```text
        BODY
          │
       Joint 1
          │
       [AX-12A]
          │
       Joint 2
          │
       [AX-12A]
          │
       Joint 3
          │
       [AX-12A]
          │
         FOOT
```

## 테스트 순서

### Test 1 — 단일 관절

```text
J1 → ±30°
J2 → ±30°
J3 → ±30°
```

### Test 2 — 두 관절 동시 동작

### Test 3 — 세 관절 동시 동작

### Test 4 — 정적 하중 테스트

### Test 5 — 실제 바닥에서 동작 테스트

이 과정을 통과한 후 양쪽 다리와 몸체를 확장한다.

---

# 8. ⑥ 기구학 설계

다리 하나를 다음과 같이 단순화할 수 있다.

```text
        BODY
          │
          O  J1
          │\
          │ \
          │  L1
          │   \
          │    O  J2
          │     \
          │      \ L2
          │       \
          │        O J3
          │        │
          │       FOOT
```

주요 파라미터:

```text
L1 = Thigh length
L2 = Shin length

J1 = Hip joint
J2 = Knee joint
J3 = Ankle joint
```

FreeCAD 모델도 이 값을 파라미터화한다.

예:

```text
L1 = 45 mm
L2 = 55 mm
```

에서

```text
L1 = 50 mm
L2 = 60 mm
```

로 변경해도 전체 모델이 변경되도록 설계한다.

---

# 9. ⑦ 토크 계산

기구 설계에서 가장 중요한 것 중 하나는 **관절 토크 계산**이다.

기본적인 관계:

```text
τ = F × L
```

예를 들어 0.5 kg의 질량이 관절에서 50 mm 떨어져 있다고 하면,

```text
F = m × g
  = 0.5 × 9.81
  ≈ 4.905 N

L = 0.05 m

τ = 4.905 × 0.05
  ≈ 0.245 N·m
```

따라서 AX-12A의 최대 토크만 보고 설계하지 말고,

- 로봇 전체 질량
- 무게중심
- 다리 길이
- 보행 시 동적 하중
- 충격 하중
- 관절 각도

를 고려해야 한다.

---

# 10. ⑧ 구조해석

3D 프린팅 부품은 금속보다 변형이 크므로 구조해석이 중요하다.

## 해석 항목

- Static structural analysis
- Von Mises stress
- Displacement
- Safety factor
- Contact stress
- Modal analysis
- Dynamic loading

예:

```text
          ↓ F

      ┌─────────┐
      │ AX-12A  │
      └────┬────┘
           │
           │
           │
         FOOT
```

발에 하중이 가해졌을 때

```text
Stress
Displacement
Safety Factor
```

를 확인한다.

---

# 11. ⑨ 3D 프린팅 및 경량화

초기 Prototype은 보수적으로 제작한다.

## Prototype V0

```text
두꺼운 벽
높은 infill
충분한 강성
```

을 사용한다.

이후

```text
V0
 ↓
FEA
 ↓
Stress 확인
 ↓
불필요한 재료 제거
 ↓
Rib 구조 적용
 ↓
V1
 ↓
질량 감소
```

순으로 최적화한다.

---

# 12. ⑩ 전자 및 제어 시스템

AX-12A는 TTL Half-Duplex Dynamixel Bus를 사용한다.

기본 구조:

```text
             ┌──────── AX-12A
             │
MCU ─ UART ──┼──────── AX-12A
             │
             ├──────── AX-12A
             │
             └──────── AX-12A
```

초기 개발에서는 다음 정도로 단순하게 구성한다.

```text
STM32
   │
UART
   │
TTL Half Duplex
   │
AX-12A × 3
```

ROS2나 AI를 처음부터 넣지 않는다.

---

# 13. ⑪ 첫 번째 펌웨어

첫 번째 목표는 **AX-12A 기본 제어 및 상태 읽기**다.

예:

```text
AX12A_TEST

ID 1

0°
 ↓
30°
 ↓
60°
 ↓
90°
 ↓
60°
 ↓
30°
 ↓
0°
```

그리고 다음 데이터를 읽는다.

```text
Read Position
Read Load
Read Temperature
Read Voltage
```

이 단계에서 각 서보의 실제 특성을 측정한다.

---

# 14. ⑫ Servo Calibration

각 AX-12A마다 실제 장착 상태를 기준으로 보정한다.

필요한 항목:

- Center position
- Minimum angle
- Maximum angle
- Direction
- Offset
- Position error
- Load
- Temperature
- Voltage
- Response delay

예:

```text
Command Angle
       │
       ▼
AX-12A
       │
       ▼
Actual Angle
       │
       ▼
Error
       │
       ▼
Calibration Offset
```

이 데이터를 나중에 MuJoCo 모델에도 반영한다.

---

# 15. ⑬ MuJoCo 시뮬레이션

원본 Microduck의 MJCF를 참고하여 AX-12A 버전의 로봇 모델을 만든다.

```text
Original Microduck MJCF
          │
          ▼
     AX12A Model
          │
          ├── Mass 수정
          ├── Inertia 수정
          ├── Joint Limit 수정
          ├── Link Length 수정
          ├── Torque 수정
          ├── Speed 수정
          └── Backlash 수정
```

---

# 16. ⑭ 실제 서보의 동역학을 시뮬레이션에 반영

단순한 이상적인 관절 모델을 사용하지 않고 실제 AX-12A 특성을 반영한다.

반영 대상:

```text
Mass
Inertia
Torque
Velocity
Joint Limit
Backlash
Servo Delay
Position Error
Motor Response
```

이렇게 하면 실제 로봇과 시뮬레이션 사이의 차이를 줄일 수 있다.

---

# 17. ⑮ 강화학습

최종 단계에서 PPO 기반 보행 정책을 학습한다.

```text
              MuJoCo
                 │
                 ▼
            Robot Model
                 │
                 ▼
                PPO
                 │
                 ▼
          Walking Policy
                 │
                 ▼
               ONNX
                 │
                 ▼
          Real Microduck
```

보행 학습에서 고려할 수 있는 목표:

- 넘어지지 않기
- 목표 방향으로 이동
- 목표 속도 유지
- 에너지 최소화
- 관절 제한 준수
- 발 미끄러짐 최소화
- 안정적인 착지

---

# 18. ⑯ Sim2Real

최종 목표는 시뮬레이션에서 학습한 정책을 실제 AX-12A 로봇에 적용하는 것이다.

```text
             FREECAD
                │
                ▼
           Robot CAD
                │
        ┌───────┴────────┐
        ▼                ▼
       FEA              MJCF
        │                │
        │                ▼
        │             MuJoCo
        │                │
        │                ▼
        │               PPO
        │                │
        │              ONNX
        │                │
        └───────┬────────┘
                ▼
          AX-12A Robot
                │
                ▼
           Real Walking
                │
                ▼
        Parameter correction
                │
                └────────→ MuJoCo
```

즉,

> **시뮬레이션 → 실제 로봇 → 실제 데이터 → 모델 수정 → 다시 시뮬레이션**

의 반복 구조를 만든다.

---

# 19. ⑰ 권장 개발 단계

## Milestone 1 — AX-12A Mechanical Prototype

목표:

> AX-12A 3개로 다리 하나를 움직인다.

결과물:

- FreeCAD
- STEP
- STL
- 3D Printed Leg
- AX-12A × 3
- STM32 또는 Raspberry Pi
- 기본 Servo Test Program

---

## Milestone 2 — Full Robot

목표:

> AX-12A 12~15개를 이용해 Microduck 형태의 로봇을 제작한다.

구성:

- Body
- Left Leg
- Right Leg
- Head
- Electronics
- Battery
- IMU

---

## Milestone 3 — Digital Twin

목표:

> 실제 로봇과 동일한 AX-12A Robot Model을 MuJoCo에 구현한다.

반영 항목:

- Mass
- Center of Gravity
- Inertia
- Joint Limit
- Torque
- Speed
- Backlash
- Servo Delay
- Friction

---

## Milestone 4 — AI Walking

목표:

> PPO를 이용하여 실제 AX-12A Microduck의 보행 정책을 학습한다.

```text
PPO
 ↓
Walking Policy
 ↓
ONNX
 ↓
Real Robot
```

---

# 20. 최종 시스템 구성

```text
                 ┌───────────────────┐
                 │       PC          │
                 │                   │
                 │ FreeCAD           │
                 │ FEA               │
                 │ MuJoCo            │
                 │ PPO               │
                 │ ONNX              │
                 └─────────┬─────────┘
                           │
                       Wi-Fi / USB
                           │
                           ▼
                 ┌───────────────────┐
                 │ Raspberry Pi /    │
                 │ Jetson / SBC      │
                 └─────────┬─────────┘
                           │
                    UART / TTL BUS
                           │
          ┌────────────────┼────────────────┐
          │                │                │
       AX-12A           AX-12A           AX-12A
          │                │                │
          ├────────────────┼────────────────┤
          │        AX-12A × 12~15          │
          └─────────────────────────────────┘
                           │
                    IMU / Battery
```

---

# 21. 최종 프로젝트의 핵심

이 프로젝트의 핵심은 단순한 3D 프린터 로봇 제작이 아니다.

다음의 전체 개발 사이클을 하나로 연결하는 것이다.

```text
        기존 로봇 분석
              ↓
       Reverse Engineering
              ↓
         CAD 설계
              ↓
        AX-12A 적용
              ↓
        구조해석 / FEA
              ↓
        3D Printing
              ↓
        실제 조립
              ↓
      Servo Calibration
              ↓
        Kinematics
              ↓
        MuJoCo Model
              ↓
        PPO Learning
              ↓
           ONNX
              ↓
        Real Robot
              ↓
          Sim2Real
              ↓
       실제 데이터 수집
              ↓
        모델 재보정
              ↺
```

따라서 **기구설계 + 해석 + 임베디드 제어 + 로봇공학 + 시뮬레이션 + AI**를 모두 포함하는 통합 프로젝트로 발전시킬 수 있다.

---

# 22. 다음 단계

가장 먼저 진행할 작업은 다음 순서가 적절하다.

### STEP 1
**Microduck 원본 자료 수집**

- 공식 GitHub
- CAD/STL
- MJCF
- BOM
- Servo 정보
- Runtime
- RL 코드

### STEP 2
**Microduck 구조 분석**

- DOF
- Joint 위치
- Link length
- Joint limit
- Body dimensions
- Mass distribution
- Center of gravity

### STEP 3
**AX-12A와 원본 Servo 비교**

- 크기
- 무게
- 토크
- 속도
- 회전범위
- 통신
- 전압
- 제어주기

### STEP 4
**AX-12A용 다리 1개 설계**

- AX-12A CAD
- Servo bracket
- Link
- Joint
- Foot

### STEP 5
**구조해석**

- Torque
- Static stress
- Displacement
- Safety factor
- Weight optimization

### STEP 6
**3D Printing**

- Prototype
- Assembly
- Mechanical test

### STEP 7
**AX-12A 제어**

- ID 설정
- Position control
- Feedback
- Calibration

### STEP 8
**MuJoCo 모델**

- MJCF
- Mass
- Inertia
- Servo dynamics

### STEP 9
**PPO 보행 학습**

### STEP 10
**ONNX + 실제 로봇 + Sim2Real**

---

# 23. 권장 프로젝트 명칭

### 프로젝트명

**Microduck-AX12A**

### 대안

- **AX12-Duck**
- **Microduck AX12A Replica**
- **Open Microduck AX12A**
- **Microduck-AX12A Sim2Real**

### 프로젝트 설명

> **Open-source biped robot project based on Microduck, redesigned around ROBOTIS AX-12A actuators, with FreeCAD mechanical design, structural analysis, 3D printing, MuJoCo simulation, PPO reinforcement learning, and Sim2Real deployment.**

