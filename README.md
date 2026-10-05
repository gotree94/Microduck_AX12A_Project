# 🐤 Microduck-AX12A

> **Open-source Microduck을 기반으로 ROBOTIS DYNAMIXEL AX-12A를 적용하여  
> 직접 설계·제작·제어·시뮬레이션·강화학습까지 수행하는 오픈 로봇 프로젝트**

![Microduck-AX12A Concept](https://img.shields.io/badge/Project-Microduck--AX12A-blue)
![Mechanical](https://img.shields.io/badge/Mechanical-FreeCAD-orange)
![Simulation](https://img.shields.io/badge/Simulation-MuJoCo-green)
![RL](https://img.shields.io/badge/RL-PPO-purple)
![Servo](https://img.shields.io/badge/Servo-AX--12A-red)

---

## 1. 프로젝트 개요

**Microduck-AX12A**는 Pollen Robotics의 **Microduck** 오픈 프로젝트와 관련 커뮤니티의 복제·분석 자료를 출발점으로 삼아, 원래의 액추에이터 구성을 그대로 복제하지 않고 **ROBOTIS DYNAMIXEL AX-12A**를 중심으로 기구를 재설계하는 개인 연구·제작 프로젝트입니다.

단순히 완성된 로봇을 조립하는 것이 아니라,

> **자료 조사 → 역설계 → 기구 설계 → 해석 → 3D 프린팅 → 조립 → 서보 제어 → 시뮬레이션 → 강화학습 → Sim2Real**

이라는 전체 개발 과정을 직접 경험하고 검증하는 것을 목표로 합니다.

### 핵심 목표

- Microduck의 원본 구조와 소프트웨어 분석
- 공개된 Clone / Replica 프로젝트 분석
- 기존 액추에이터와 AX-12A의 기계적 차이 분석
- AX-12A용 브래킷 및 링크 구조 재설계
- FreeCAD 기반 파라메트릭 3D 모델 작성
- 구조/기구학/동역학 해석
- 3D 프린터를 이용한 실제 제작
- AX-12A 버스 제어 및 피드백 구현
- MuJoCo 기반 디지털 트윈 구축
- PPO 기반 보행 정책 학습
- ONNX를 이용한 정책 배포
- 실제 로봇의 측정 데이터를 이용한 Sim2Real 보정

---

# 2. 왜 Microduck + AX-12A인가?

Microduck은 소형 이족 보행 로봇의 기구, 제어, 센서, 시뮬레이션, 강화학습을 한 프로젝트 안에서 연결해서 볼 수 있다는 장점이 있습니다.

특히 공식 소프트웨어와 RL 프로젝트뿐 아니라 여러 Clone / Replica 프로젝트가 공개되어 있어 **실제 제작과 역설계까지 접근할 수 있는 좋은 연구 플랫폼**이 됩니다.

반면 이 프로젝트에서는 원래의 액추에이터를 그대로 사용하는 대신 **DYNAMIXEL AX-12A**를 적용합니다.

이렇게 하면 단순 복제가 아니라 다음과 같은 설계 문제가 발생합니다.

- 액추에이터 크기 및 형상 차이
- 질량 및 무게중심 변화
- 출력축 위치 차이
- 토크와 속도 차이
- 감속기/백래시 특성
- 장착 홀 및 브래킷 구조 차이
- 전원 조건 차이
- 통신 프로토콜 차이
- 제어 주기 및 명령 지연 차이
- 실제 액추에이터 특성을 반영한 RL 모델 필요

즉, **기존 로봇을 새로운 액추에이터에 맞게 재설계하는 좋은 시스템 엔지니어링 과제**가 됩니다.

---

# 3. 전체 개발 구조

```text
┌─────────────────────────────────────────────────────────────┐
│                    Microduck 원본 조사                      │
│        Official Repo / RL / HAT / MJCF / STL / 문서        │
└──────────────────────────────┬──────────────────────────────┘
                               │
                               ▼
┌─────────────────────────────────────────────────────────────┐
│                 Clone / Replica 프로젝트 분석              │
│       Reverse Engineering / CAD / BOM / Assembly           │
└──────────────────────────────┬──────────────────────────────┘
                               │
                               ▼
┌─────────────────────────────────────────────────────────────┐
│                AX-12A 적용 가능성 분석                      │
│       Size / Mass / Torque / Speed / Mount / Protocol      │
└──────────────────────────────┬──────────────────────────────┘
                               │
                               ▼
┌─────────────────────────────────────────────────────────────┐
│                    FreeCAD 기구 재설계                       │
│       Servo Mount → Link → Joint → Leg → Full Robot        │
└──────────────────────────────┬──────────────────────────────┘
                               │
                               ▼
┌─────────────────────────────────────────────────────────────┐
│                     구조 / 기구 해석                        │
│          FEA / Kinematics / Dynamics / Modal Analysis      │
└──────────────────────────────┬──────────────────────────────┘
                               │
                               ▼
┌─────────────────────────────────────────────────────────────┐
│                   3D Printing & Assembly                    │
│             Prototype → Test → Reinforcement → Final       │
└──────────────────────────────┬──────────────────────────────┘
                               │
                               ▼
┌─────────────────────────────────────────────────────────────┐
│                     AX-12A 제어                             │
│        MCU / TTL Half Duplex / Position / Load / Temp      │
└──────────────────────────────┬──────────────────────────────┘
                               │
                               ▼
┌─────────────────────────────────────────────────────────────┐
│                       MuJoCo                               │
│       CAD → MJCF → Actuator Model → Sensor Model           │
└──────────────────────────────┬──────────────────────────────┘
                               │
                               ▼
┌─────────────────────────────────────────────────────────────┐
│                   PPO Reinforcement Learning                 │
│       Domain Randomization / Actuator Randomization         │
└──────────────────────────────┬──────────────────────────────┘
                               │
                               ▼
┌─────────────────────────────────────────────────────────────┐
│                     ONNX / Sim2Real                         │
│       Simulation Policy → Real Robot → Data → Retrain       │
└─────────────────────────────────────────────────────────────┘
```

---

# 4. Microduck 원본 및 참고 프로젝트

본 프로젝트는 기존 공개 프로젝트를 적극적으로 활용하되, **공식 프로젝트와 커뮤니티 Clone/Replica 자료를 구분하여 사용**합니다.

## 4.1 Official Microduck

### Pollen Robotics — Microduck

urlOfficial Microduck Repositoryhttps://github.com/pollen-robotics/microduck

Microduck의 공식 소프트웨어 및 로봇 런타임 관련 자료입니다.

주요 분석 대상:

- Robot Runtime
- Motor / Servo Configuration
- Robot State
- Control Loop
- Sensor
- Camera / ToF
- Configuration
- Policy 실행 구조

---

## 4.2 Official Microduck RL

urlMicroduck RL Repositoryhttps://github.com/pollen-robotics/microduck_rl

MuJoCo 기반 강화학습 환경과 PPO 및 ONNX 배포 구조를 분석하기 위한 핵심 자료입니다.

특히 다음 항목을 중요하게 참고합니다.

- MJCF robot model
- STL geometry
- Joint hierarchy
- Joint limits
- Mass / inertia
- Actuator model
- PPO
- Domain Randomization
- Battery voltage variation
- Voltage sag
- Command delay
- Friction
- Backlash
- ONNX policy deployment

원래 Microduck의 액추에이터 특성을 그대로 사용하는 것이 아니라 **AX-12A에 맞는 actuator model로 교체**하는 것이 핵심입니다.

---

## 4.3 RPI Robot HAT

urlPollen Robotics RPI Robot HAT Repositoryhttps://github.com/pollen-robotics/elec_RPI_Robot_HAT

공개된 전자회로 및 PCB 설계 자료입니다.

- Schematic
- PCB
- BOM
- Gerber
- Pick & Place
- STEP

본 프로젝트에서는 원본 회로를 그대로 복제하기보다 **AX-12A 기반의 단순화된 제어 시스템**을 우선 검토합니다.

---

# 5. Clone / Replica 프로젝트

## 5.1 microduck-replica

urlmicroduck-replicahttps://github.com/fanhao375/microduck-replica

실제 Microduck을 역설계하고 복제하기 위한 대표적인 커뮤니티 프로젝트입니다.

참고 항목:

- Hardware reverse engineering
- Assembly drawing
- Exploded view
- CAD
- Printable parts
- Electronics teardown
- BOM
- MJCF
- Assembly information

---

## 5.2 microduck-replica-cad

urlmicroduck-replica-cadhttps://github.com/fanhao375/microduck-replica-cad

본 프로젝트에서 특히 중요한 참고 자료입니다.

기계 CAD와 함께 **액추에이터 변경 사례**가 존재하기 때문입니다.

```text
Original
   │
   └── XL330
         │
         ▼
Replica CAD
   │
   └── HD-1910 variant
         │
         ▼
Microduck-AX12A
   │
   └── AX-12A redesign
```

즉, 기존 Microduck 구조에서 액추에이터가 변경되면서 어떤 부품을 수정해야 하는지를 분석하는 **선행 사례**로 활용할 수 있습니다.

---

## 5.3 기타 Replica / Documentation

### microduck_20260917

urlmicroduck_20260917https://github.com/xaioxiaodream/microduck_20260917

다음과 같은 실제 제작 관련 자료를 참고합니다.

- Assembly drawings
- CAD
- Print files
- Build log
- Hardware
- Documentation
- BOM
- Progress

### OpenMicroDuck

urlOpenMicroDuckhttps://github.com/SaberOnGo/open-microduck

Microduck의 하드웨어, 기구, 센서, 전원, DYNAMIXEL, 제어, 시뮬레이션 및 정책 구조를 이해하기 위한 커뮤니티 문서입니다.

---

# 6. AX-12A 적용 전략

AX-12A를 단순히 기존 액추에이터와 교체하는 방식으로 접근하지 않습니다.

## 설계 원칙

```text
Original Microduck Geometry
          │
          ▼
   Mechanical Analysis
          │
          ▼
   AX-12A Envelope
          │
          ▼
   New Mount / Bracket
          │
          ▼
   Modified Link
          │
          ▼
   Joint Position
          │
          ▼
   Recalculate COM / Inertia
          │
          ▼
   FEA + Dynamics
          │
          ▼
   MuJoCo Model
```

### AX-12A에서 중요하게 볼 항목

| 항목 | 검토 내용 |
|---|---|
| 정격/최대 토크 | 보행 시 필요한 관절 토크와 비교 |
| 속도 | 보행 주기와 관절 속도 검토 |
| 질량 | 기존 액추에이터 대비 무게 증가/감소 |
| 외형 | 링크와 브래킷 간 간섭 |
| 출력축 | 관절 회전 중심과 일치 여부 |
| 장착홀 | 기존 구조와 호환 여부 |
| 위치 분해능 | 제어 정밀도 |
| 백래시 | 보행 안정성 영향 |
| TTL 통신 | Half-Duplex Bus 설계 |
| Protocol | AX-12A Protocol 1.0 |
| 전원 | 9~12 V 계열 전원 구조 검토 |
| 피드백 | Position / Load / Temperature / Voltage |

> **주의:** AX-12A의 최대/스톨 토크를 지속 운전 가능한 설계 토크로 사용하지 않습니다. 실제 설계에서는 관절별 하중과 안전율을 적용합니다.

---

# 7. 기구 설계

## CAD 도구

**FreeCAD**를 기본 CAD로 사용합니다.

권장 설계 순서는 다음과 같습니다.

```text
AX-12A Reference Model
        ↓
Servo Mount
        ↓
Bracket
        ↓
Link
        ↓
Single Joint
        ↓
Single Leg
        ↓
Hip / Knee / Ankle
        ↓
Body
        ↓
Full Robot Assembly
```

### 설계 원칙

- 가능한 한 Parametric CAD로 작성
- 기준 치수를 Spreadsheet로 관리
- Servo 중심축을 기준으로 설계
- 체결부에 충분한 여유 확보
- 3D Printer 공차 반영
- 조립/분해가 가능한 구조
- 케이블 경로 확보
- 출력축 주변의 응력 집중 방지
- 필요 시 Rib / Fillet 적용

---

# 8. 구조 및 동역학 해석

3D 프린팅 전에 최소한 다음 항목을 검토합니다.

## 구조해석

- Von Mises Stress
- Displacement
- Safety Factor
- Contact Stress
- Bolt / Screw 주변 응력
- Servo Mount 응력

## 기구학

- Forward Kinematics
- Inverse Kinematics
- Joint Limit
- Workspace
- Foot trajectory

## 동역학

- Link mass
- Center of Mass
- Inertia Tensor
- Joint torque
- Ground reaction force
- Impact load

## Modal Analysis

가능하다면 다음을 확인합니다.

- 고유진동수
- 구조 공진
- 링크 진동
- Servo mount의 진동 특성

---

# 9. 3D Printing

처음부터 최종품을 출력하지 않고 **Prototype → Test → Revision** 방식으로 진행합니다.

```text
CAD
 ↓
Small / Critical Part Print
 ↓
Servo Fit Test
 ↓
Single Joint Test
 ↓
Single Leg
 ↓
Full Robot
 ↓
Walking Test
 ↓
Failure Analysis
 ↓
CAD Revision
```

특히 다음 부품을 먼저 출력하는 것을 권장합니다.

1. AX-12A Mount
2. Hip Bracket
3. Knee Link
4. Ankle Bracket
5. Foot
6. Body Frame

---

# 10. AX-12A 제어 시스템

초기에는 복잡한 원본 Microduck 전자 시스템을 그대로 복제하지 않습니다.

### 1차 제어 구조

```text
PC / Raspberry Pi
        │
        │ USB / UART
        ▼
MCU
        │
        │ TTL Half-Duplex
        ▼
AX-12A Bus
 ┌──────┼──────┬──────┐
 ID1    ID2    ID3   ... ID15
```

별도의 IMU를 연결하여 다음 정보를 함께 사용합니다.

```text
AX-12A
 ├─ Position
 ├─ Load
 ├─ Temperature
 └─ Voltage

IMU
 ├─ Acceleration
 ├─ Gyroscope
 └─ Orientation
```

### 초기 테스트

- Servo ID 확인
- Torque Enable
- Goal Position
- Present Position
- Present Load
- Temperature
- Voltage
- 이동 속도
- 위치 반복성
- 통신 안정성

---

# 11. MuJoCo 디지털 트윈

실제 로봇을 제작하기 전에 가능한 한 많은 문제를 시뮬레이션에서 발견합니다.

```text
FreeCAD
   │
   ├── Mass
   ├── COM
   ├── Inertia
   └── Geometry
          │
          ▼
        MJCF
          │
          ├── Body
          ├── Joint
          ├── Actuator
          ├── Sensor
          └── Contact
                 │
                 ▼
              MuJoCo
```

### AX-12A 모델에 포함할 항목

- Joint limit
- Position control
- Torque limit
- Velocity limit
- Servo delay
- Backlash
- Friction
- Saturation
- Dead zone
- Sensor noise
- Command delay

가능하면 실제 AX-12A에서 데이터를 측정하여 actuator model을 보정합니다.

---

# 12. PPO 강화학습

최종 목표는 단순한 자세 제어가 아니라 **보행 정책을 학습시키는 것**입니다.

### 기본 구조

```text
                 ┌──────────────┐
                 │    MuJoCo    │
                 │   Robot Sim  │
                 └──────┬───────┘
                        │
                 Observation
                        │
                        ▼
                 ┌──────────────┐
                 │     PPO      │
                 │    Policy    │
                 └──────┬───────┘
                        │
                    Action
                        │
                        ▼
                 ┌──────────────┐
                 │   Actuator   │
                 │    Model     │
                 └──────────────┘
```

## Domain Randomization

Sim2Real을 고려하여 다음 값을 랜덤화합니다.

- Robot mass
- Center of mass
- Friction
- Motor strength
- Servo delay
- Joint damping
- Backlash
- Sensor noise
- Ground friction
- Battery voltage
- Control frequency

---

# 13. ONNX / Sim2Real

학습된 정책을 실제 하드웨어에 적용합니다.

```text
        Simulation
            │
            ▼
          PPO
            │
            ▼
       Trained Policy
            │
            ▼
          ONNX
            │
            ▼
     Real Robot Runtime
            │
            ▼
       AX-12A Robot
            │
            ▼
    Measured Sensor Data
            │
            ▼
      Model Correction
            │
            └──────────────► Retraining
```

핵심은 **Simulation → Real Robot → Measurement → Simulation Model Update**의 반복입니다.

---

# 14. 단계별 Milestone

## M1 — Single Leg Prototype

목표:

- AX-12A 3개 이상으로 단일 다리 구성
- Servo mount 제작
- Position control
- 각도 측정
- 하중 측정
- 기구학 검증

**완료 조건**

- 모든 관절이 정상 동작
- 기구 간섭 없음
- 예상 workspace 확보
- 기본적인 하중 테스트 통과

---

## M2 — Full Microduck-AX12A

목표:

- 전체 기구 설계
- 3D printing
- 전체 조립
- Servo bus 구축
- IMU 추가
- 기본 자세 제어

---

## M3 — Digital Twin

목표:

- FreeCAD → MJCF
- Mass / inertia 반영
- Joint limit 반영
- AX-12A actuator model 구축
- MuJoCo에서 기본 동작 확인

---

## M4 — Walking PPO

목표:

- PPO 환경 구성
- Reward 설계
- Domain Randomization
- Walking policy 학습
- ONNX export

---

## M5 — Sim2Real

목표:

- 실제 AX-12A robot에서 policy 실행
- 센서 데이터 기록
- Simulation과 실제 동작 비교
- Actuator model 보정
- 재학습

---

# 15. 권장 프로젝트 폴더 구조

```text
Microduck-AX12A/
│
├── README.md
│
├── docs/
│   ├── original-research/
│   ├── clone-research/
│   ├── mechanical/
│   ├── electronics/
│   ├── control/
│   ├── mujoco/
│   └── reinforcement-learning/
│
├── cad/
│   ├── ax12a/
│   ├── brackets/
│   ├── links/
│   ├── body/
│   ├── legs/
│   └── assembly/
│
├── analysis/
│   ├── fea/
│   ├── kinematics/
│   ├── dynamics/
│   └── modal/
│
├── electronics/
│   ├── controller/
│   ├── power/
│   ├── imu/
│   └── dynamixel/
│
├── firmware/
│   ├── servo_test/
│   ├── calibration/
│   ├── imu/
│   └── robot_control/
│
├── simulation/
│   ├── mujoco/
│   ├── mjcf/
│   └── actuator_model/
│
├── rl/
│   ├── environment/
│   ├── ppo/
│   ├── domain_randomization/
│   └── policies/
│
├── onnx/
│
├── data/
│   ├── servo/
│   ├── imu/
│   ├── walking/
│   └── experiments/
│
├── print/
│   ├── prototype/
│   └── final/
│
└── tools/
    ├── cad/
    ├── calibration/
    ├── logging/
    └── visualization/
```

---

# 16. 핵심 설계 원칙

### ① 원본을 먼저 이해한다

원본 Microduck을 바로 수정하지 않고,

**원본 → Clone → CAD → MJCF → Runtime → RL**

순서로 분석합니다.

### ② 액추에이터가 바뀌면 로봇 전체를 다시 생각한다

AX-12A는 단순한 "교체 부품"이 아닙니다.

액추에이터의

- 크기
- 질량
- 출력축
- 토크
- 속도
- 백래시
- 통신
- 전원

이 바뀌면 **링크, 무게중심, 관성, 관절 토크, 보행 정책까지 영향을 받습니다.**

### ③ CAD와 Simulation을 분리하지 않는다

```text
CAD
 ↓
Mass / COM / Inertia
 ↓
MJCF
 ↓
Simulation
 ↓
RL
```

가능한 한 동일한 파라미터를 공유합니다.

### ④ 실제 데이터를 다시 Simulation으로 가져온다

AX-12A의 실제 응답을 측정하여,

```text
Command
   ↓
Actual Position
   ↓
Delay / Overshoot / Backlash
   ↓
Actuator Model
```

로 모델을 개선합니다.

---

# 17. 프로젝트의 최종 형태

이 프로젝트의 최종 결과물은 단순한 3D 프린팅 로봇 한 대가 아닙니다.

```text
                  ┌───────────────────┐
                  │   Open Research   │
                  └─────────┬─────────┘
                            │
        ┌───────────────────┼───────────────────┐
        ▼                   ▼                   ▼
     Mechanical          Electronics           AI
        │                   │                   │
     FreeCAD             MCU/AX12A            PPO
        │                   │                   │
       FEA                  IMU               ONNX
        │                   │                   │
        └───────────────────┼───────────────────┘
                            ▼
                       MuJoCo Twin
                            │
                            ▼
                         Sim2Real
                            │
                            ▼
                 ┌───────────────────┐
                 │ Microduck-AX12A   │
                 │ Physical Robot    │
                 └───────────────────┘
```

궁극적으로는 다음과 같은 **End-to-End Robotics 개발 경험**을 확보하는 것이 목표입니다.

> **Reverse Engineering → Mechanical Design → FEA → Manufacturing → Embedded Control → Digital Twin → Reinforcement Learning → Sim2Real**

---

# 18. 다음 작업

이 README를 프로젝트의 첫 페이지로 사용하고, 다음 단계에서는 실제 원본 파일을 기반으로 **정량적인 설계 데이터**를 추출합니다.

### 다음 분석 대상

1. Microduck MJCF의 전체 Body / Joint hierarchy
2. Joint 이름 및 ID
3. Joint axis
4. Joint limit
5. Link mass
6. Inertia tensor
7. Link position
8. Actuator mapping
9. STL geometry
10. Original actuator mounting geometry
11. Clone CAD의 실제 치수
12. XL330 ↔ HD-1910 변경점
13. AX-12A 장착을 위한 변경 부품 목록

최종적으로 다음과 같은 **부품별 변경표**를 작성합니다.

| Microduck 부품 | 원본 | AX-12A 적용 | 작업 |
|---|---|---|---|
| Servo Mount | Original actuator | AX-12A | **RE-DESIGN** |
| Hip Bracket | Original | AX-12A | **RE-DESIGN** |
| Link | Original | Modified | **MODIFY** |
| Knee Assembly | Original | AX-12A | **RE-DESIGN** |
| Ankle Assembly | Original | AX-12A | **RE-DESIGN** |
| Body | Original | TBD | **CHECK** |
| Foot | Original | Modified | **CHECK** |
| MJCF | Original actuator | AX-12A model | **REBUILD** |
| Actuator Model | Original | AX-12A | **REBUILD** |
| Controller | Original | AX-12A Protocol 1.0 | **NEW** |

---

## 19. Related Documents

현재 프로젝트에서 함께 사용하는 문서:

- `Microduck_AX12A_Project_Plan.md` — 전체 프로젝트 개발 계획
- `Microduck_Original_and_Clone_Research.md` — 원본 및 Clone/Replica 조사
- `README.md` — **본 문서 / 프로젝트 첫 페이지**

---

## 20. License / Attribution

이 프로젝트는 공개된 Microduck 및 관련 커뮤니티 프로젝트를 **연구·교육·개발 목적으로 참고**합니다.

원본 프로젝트의 코드, CAD, 이미지, 문서 등을 직접 재배포할 경우에는 각 원본 저장소의 라이선스 및 저작권 조건을 반드시 확인해야 합니다.

특히 다음을 구분합니다.

- Official Microduck
- Official Microduck RL
- Community Replica
- Community CAD
- 본 프로젝트의 자체 설계 결과물

---

# 🐤 Microduck-AX12A

**작은 이족보행 로봇 하나를 직접 만들면서  
기구 설계부터 강화학습과 Sim2Real까지 전체 로봇 개발 과정을 연결한다.**

> **Design → Build → Control → Simulate → Learn → Walk**
