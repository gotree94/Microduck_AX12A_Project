# Microduck 원본 자료 및 Clone 프로젝트 조사

## 1. 조사 목적

Microduck-AX12A 자작 프로젝트를 위해 기존 Microduck의 공식 자료와 공개 Clone/Replica 프로젝트를 조사한다.

목표는 다음과 같다.

```text
원본 Microduck 조사
        ↓
CAD / MJCF / STL 확보
        ↓
Clone / Replica 분석
        ↓
기구 구조 및 DOF 분석
        ↓
AX-12A 적용성 검토
        ↓
기구 재설계
        ↓
FEA / 동역학 해석
        ↓
3D Printing
        ↓
실제 조립
        ↓
MuJoCo
        ↓
PPO
        ↓
Sim2Real
```

---

# 2. 가장 중요한 원본 자료

## 2.1 Pollen Robotics 공식 Microduck

**공식 GitHub**

https://github.com/pollen-robotics/microduck

Microduck의 공식 Runtime 및 전체 소프트웨어 구조를 확인하기 위한 1차 자료다.

Microduck은 약 25 cm 크기의 소형 2족 로봇이며, 여러 개의 Dynamixel 서보를 이용해 몸체와 양쪽 다리를 제어한다.

### 주요 조사 대상

- `robotd`
- `configd`
- `padd`
- `mediad`
- `tof`
- `updater`
- `policies`
- `scripts`
- `docs`
- servo bus
- control loop
- IMU
- camera
- ToF
- policy
- simulation interface
- hardware configuration

기본적인 제어 구조는 다음과 같이 이해할 수 있다.

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

# 3. Microduck RL

## 3.1 공식 RL 저장소

**Pollen Robotics Microduck RL**

https://github.com/pollen-robotics/microduck_rl

이번 프로젝트에서 가장 중요한 자료 중 하나다.

MuJoCo 기반 RL 환경과 PPO 학습 구조가 포함되어 있으며, 학습된 정책을 ONNX로 내보내 실제 Microduck Runtime에서 사용하는 구조를 확인할 수 있다.

특히 다음 자료가 중요하다.

- MJCF
- STL mesh
- robot model
- actuator model
- PPO
- domain randomization
- ONNX export
- simulation configuration

---

## 3.2 MJCF 및 STL

공개된 RL 프로젝트에는 Microduck의 로봇 모델과 STL geometry가 포함되어 있다.

구조의 예:

```text
microduck_rl/
└── src/
    └── mjlab_microduck/
        └── robot/
            └── microduck/
                ├── assets/
                ├── robot_walk.xml
                ├── robot_groundcontact.xml
                └── ...
```

클론 프로젝트의 분석에 따르면 다수의 STL mesh와 전체 MJCF 모델이 포함되어 있다.

MJCF에는 다음과 같은 기구학/동역학 정보가 들어 있다.

- link hierarchy
- joint 위치
- joint axis
- joint limit
- mass
- inertia tensor
- 상대 위치
- collision geometry

따라서 Microduck의 기구학 모델을 복원하는 데 매우 유용하다.

---

# 4. XL330 모델

Microduck RL asset 디렉터리에는 XL330 관련 geometry도 공개되어 있다.

예:

```text
xl330.stl
xl330.part
```

따라서 다음과 같은 구조로 분석할 수 있다.

```text
Microduck
    │
    ├── Robot Geometry
    │
    ├── XL330 Geometry
    │
    └── MJCF
```

특히 AX-12A로 액추에이터를 교체하려면 XL330의 실제 장착 위치와 AX-12A의 장착 구조를 비교해야 한다.

---

# 5. 가장 중요한 Clone 프로젝트

## 5.1 microduck-replica

**GitHub**

https://github.com/fanhao375/microduck-replica

이 프로젝트는 단순한 코드 복제가 아니라 공식 MJCF와 Runtime을 분석하여 실제 조립 가능한 형태로 Microduck을 복원한 프로젝트다.

### 제공 자료

```text
Microduck Replica
│
├── Assembly drawings
├── Exploded views
├── CAD-importable assemblies
├── Printable parts
├── Electronics teardown
├── BOM
└── Hardware analysis
```

이 프로젝트는 **원본 Microduck의 구조를 이해하기 위한 핵심 참고자료**다.

---

# 6. Microduck Replica의 CAD

`microduck-replica`에는 CAD 관련 자료가 포함되어 있다.

주요 항목:

```text
cad/
print/
assembly-drawings/
hardware/
docs/
```

공식 MJCF에서 얻은 geometry를 조립하여 실제 Microduck assembly 형태로 복원한 자료도 확인할 수 있다.

따라서 다음과 같은 목적으로 사용할 수 있다.

> 원본 Microduck의 부품 구성과 조립 관계를 분석한다.

---

# 7. Editable SolidWorks CAD

## 7.1 microduck-replica-cad

**GitHub**

https://github.com/fanhao375/microduck-replica-cad

이 프로젝트는 단순 STL이 아니라 편집 가능한 CAD 자료를 제공한다.

주요 자료:

- SolidWorks source
- BOM
- Assembly drawings
- Component drawings
- Print files
- Assembly instructions

---

# 8. 다른 Servo를 사용한 실제 Clone 사례

이 프로젝트가 특히 중요한 이유는 **원본 XL330 외에 다른 Servo를 적용한 버전도 존재하기 때문**이다.

대표적으로:

| 버전 | Actuator | 용도 |
|---|---|---|
| v1.1 | Dynamixel XL330 | 원본 Microduck |
| v2.x | Feetech HD-1910 | 대체 액추에이터 |

즉 이미 다음과 같은 프로젝트가 존재한다.

```text
Original
XL330
  │
  ▼
Microduck CAD
  │
  ▼
HD-1910
  │
  ▼
Modified CAD
```

이는 AX-12A 프로젝트에서 매우 중요한 참고 사례다.

우리는 이를 다음과 같이 확장할 수 있다.

```text
Original
XL330
  │
  ▼
Microduck CAD
  │
  ▼
AX-12A
  │
  ▼
Modified CAD
```

---

# 9. 실제 HD-1910 기반 Replica

`microduck-replica-cad`의 자료에서는 HD-1910 버전이 실제 15개 액추에이터를 사용한 실물 제작으로 이어진 사례를 확인할 수 있다.

따라서 이것은 단순 CAD 예제가 아니라,

```text
Actuator substitution
        ↓
Mechanical modification
        ↓
3D Printing
        ↓
Assembly
        ↓
Real Robot
```

이라는 실제 제작 사례다.

AX-12A로 액추에이터를 교체할 때 가장 좋은 참고 사례 중 하나다.

---

# 10. 또 다른 Clone 프로젝트

## xaioxiaodream/microduck_20260917

GitHub:

https://github.com/xaioxiaodream/microduck_20260917

이 프로젝트는 `fanhao375/microduck-replica`를 기반으로 발전한 형태다.

구조 예:

```text
assembly-drawings/
assets/
build-log/
cad/
docs/
hardware/
print/
scripts/
tools/
BOM.md
BUILD-LOG.md
PROGRESS.md
```

특히 `BUILD-LOG.md`와 `PROGRESS.md`를 통해 실제 제작 과정을 참고할 수 있다.

---

# 11. OpenMicroDuck

## SaberOnGo/open-microduck

GitHub:

https://github.com/SaberOnGo/open-microduck

이 프로젝트는 단순 CAD 복제보다는 Microduck의 전체 시스템을 기술적으로 분석하고 문서화하는 성격이 강하다.

### Hardware

```text
Hardware
├── Mechanics
├── Electronics
├── Sensors
├── Power
├── DYNAMIXEL bus
└── BOM
```

### Software

```text
Software
├── Control loop
├── Runtime
├── Policy
├── Simulation
└── Training
```

원본 Microduck의 시스템 구조를 이해하기 위한 좋은 자료다.

---

# 12. Microduck DOF 및 제어 구조

OpenMicroDuck에서 정리된 자료를 참고하면 다음과 같은 항목을 확인할 수 있다.

```text
15 physical motor IDs
14 policy-controlled joints
61 movement-policy input width
50 Hz movement-control frequency
```

즉 실제 모터 수와 정책에서 직접 제어하는 관절 수가 반드시 동일하지 않을 수 있다.

따라서 AX-12A 버전을 설계할 때도 단순히 "서보 15개를 달면 원본과 같다"고 생각하지 않고 실제 제어 구조를 분석해야 한다.

---

# 13. 전자회로 자료

## 13.1 RPI Robot HAT

Pollen Robotics가 공개한 관련 하드웨어 저장소:

https://github.com/pollen-robotics/elec_RPI_Robot_HAT

공개 자료에는 다음이 포함되어 있다.

- KiCad project
- Schematic
- PCB
- Gerber
- BOM
- Pick & Place
- STEP

따라서 Microduck의 Raspberry Pi 계열 HAT 구조를 분석할 때 활용할 수 있다.

---

# 14. IMU / Dynamixel Bus

Microduck에는 IMU 데이터를 Dynamixel Bus를 통해 전달하는 구조가 분석되어 있다.

개념적으로:

```text
LSM6DSV16X
      │
      ▼
    MCU
      │
      ▼
Dynamixel TTL Bus
```

형태다.

분석 자료에서는 IMU가 Dynamixel bus의 특정 ID 및 register를 통해 데이터를 제공하는 것으로 설명된다.

다만 완전한 공식 회로도/BOM이 공개되지 않은 부분도 있으므로 이 부분은 직접 구현하는 것이 현실적이다.

AX-12A 버전에서는 다음과 같이 단순화할 수 있다.

```text
STM32
 │
 ├── IMU
 │
 └── AX-12A Bus
```

---

# 15. Microduck RL의 중요한 특징

공식 `microduck_rl`은 단순한 이상적인 관절 모델을 사용하지 않는다.

다음과 같은 실제 액추에이터 특성을 고려한다.

- voltage control law
- back-EMF
- Coulomb friction
- Stribeck friction
- load-dependent friction
- battery voltage randomization
- voltage sag
- command delay
- friction randomization
- backlash

즉 공식 프로젝트 자체가 **Sim2Real에서 actuator dynamics가 중요하다**는 점을 반영하고 있다.

---

# 16. AX-12A 버전에서는 새로운 Actuator Model 필요

원본:

```text
Original Microduck
      │
      ▼
BAM M6 / XL330 model
      │
      ▼
PPO
```

AX-12A 버전:

```text
AX-12A Microduck
      │
      ▼
AX-12A Actuator Model
      │
      ├── Torque
      ├── Velocity
      ├── Voltage
      ├── Delay
      ├── Backlash
      ├── Friction
      └── Position Error
      │
      ▼
PPO
```

실제 AX-12A의 측정 데이터를 이용해 MuJoCo 모델을 보정하는 것이 중요하다.

---

# 17. 프로젝트별 중요도

| 프로젝트 | 성격 | 활용도 |
|---|---|---:|
| `pollen-robotics/microduck` | 공식 Runtime | ★★★★★ |
| `pollen-robotics/microduck_rl` | 공식 MuJoCo/RL | ★★★★★ |
| `pollen-robotics/elec_RPI_Robot_HAT` | 공식 PCB | ★★★★☆ |
| `fanhao375/microduck-replica` | 하드웨어 역설계 | ★★★★★ |
| `fanhao375/microduck-replica-cad` | SolidWorks CAD | ★★★★★ |
| `xaioxiaodream/microduck_20260917` | Replica/Fabrication | ★★★★☆ |
| `SaberOnGo/open-microduck` | 전체 기술 분석 | ★★★★★ |

---

# 18. 우선적으로 집중할 Clone 3개

## 18.1 microduck-replica

https://github.com/fanhao375/microduck-replica

### 목적

원본 Microduck의 구조와 부품을 이해한다.

---

## 18.2 microduck-replica-cad

https://github.com/fanhao375/microduck-replica-cad

### 목적

실제 CAD를 기반으로 기구를 수정한다.

특히 XL330 → HD-1910 변경 사례를 분석하면 AX-12A 적용에 직접적인 도움을 얻을 수 있다.

---

## 18.3 open-microduck

https://github.com/SaberOnGo/open-microduck

### 목적

원본 Microduck의 하드웨어, 소프트웨어, 통신, 센서 구조를 이해한다.

---

# 19. 자료 활용 우선순위

```text
                 ┌─────────────────────┐
                 │ Pollen Microduck    │
                 │ Official Runtime    │
                 └──────────┬──────────┘
                            │
                            ▼
                 ┌─────────────────────┐
                 │ Microduck RL        │
                 │ MJCF + STL + PPO    │
                 └──────────┬──────────┘
                            │
             ┌──────────────┴──────────────┐
             ▼                             ▼
┌────────────────────────┐     ┌─────────────────────┐
│ microduck-replica      │     │ open-microduck      │
│ Reverse Engineering    │     │ System Analysis     │
└────────────┬───────────┘     └──────────┬──────────┘
             │                            │
             ▼                            │
┌────────────────────────┐                │
│ microduck-replica-cad  │                │
│ SolidWorks CAD         │                │
└────────────┬───────────┘                │
             │                            │
             └──────────────┬─────────────┘
                            ▼
                 ┌─────────────────────┐
                 │ AX-12A Modification │
                 └─────────────────────┘
```

---

# 20. Microduck-AX12A 프로젝트로 연결

현재 조사 결과를 기반으로 하면 다음 개발 흐름이 가장 합리적이다.

```text
Original Microduck
        ↓
MJCF / STL / CAD 분석
        ↓
Joint / DOF / Mass / Inertia 추출
        ↓
XL330 구조 분석
        ↓
AX-12A 사양 비교
        ↓
AX-12A CAD 모델 작성
        ↓
Servo Bracket 재설계
        ↓
Link 재설계
        ↓
FEA
        ↓
3D Printing
        ↓
Assembly
        ↓
AX-12A Calibration
        ↓
MuJoCo AX-12A Model
        ↓
Actuator Dynamics Identification
        ↓
PPO
        ↓
ONNX
        ↓
Real Robot
        ↓
Sim2Real
```

---

# 21. AX-12A 적용성 분석에서 확인할 항목

다음 단계에서는 Microduck의 각 부품을 다음과 같이 분류하는 것이 좋다.

| 부품 | 원본 | AX-12A 적용 | 예상 조치 |
|---|---|---|---|
| Body | XL330 기반 | AX-12A | 수정 |
| Hip Bracket | XL330 기반 | AX-12A | 재설계 |
| Thigh | 구조물 | AX-12A | 검토 |
| Knee Bracket | XL330 기반 | AX-12A | 재설계 |
| Shin | 구조물 | AX-12A | 검토 |
| Ankle | XL330 기반 | AX-12A | 재설계 |
| Foot | 구조물 | AX-12A | 수정 |
| Head | 구조물 | AX-12A | 유지 가능성 검토 |
| Electronics | 원본 | AX-12A | 재구성 |
| IMU | 원본 | 별도 구성 | 단순화 가능 |

이 표는 실제 CAD와 MJCF를 분석한 후 확정해야 한다.

---

# 22. 가장 중요한 발견

이번 조사에서 가장 중요한 참고 사례는 다음이다.

```text
Original Microduck
       │
       │ XL330
       ▼
Microduck Replica CAD
       │
       │ actuator replacement
       ▼
Feetech HD-1910
       │
       ▼
Modified Mechanical Design
       │
       ▼
3D Printing
       │
       ▼
Real Robot
```

즉 이미 **다른 액추에이터를 사용하기 위해 Microduck 기구를 수정한 사례**가 존재한다.

따라서 이번 프로젝트에서는 이 경험을 기반으로:

```text
Original XL330
       ↓
Reference CAD
       ↓
AX-12A
       ↓
Mechanical Redesign
       ↓
FEA
       ↓
3D Printing
       ↓
Real Robot
       ↓
MuJoCo AX-12A Model
       ↓
PPO
       ↓
Sim2Real
```

을 구축하는 것이 가장 효율적이다.

---

# 23. 다음 조사 단계

다음 단계에서는 단순한 자료 목록을 넘어 실제 설계 데이터를 추출한다.

## A. MJCF 데이터 추출

- Joint 이름
- Joint axis
- Joint limit
- Link hierarchy
- Link mass
- Inertia
- Link position
- Actuator position

## B. Microduck DOF 구조 작성

```text
BODY
 ├── LEFT LEG
 │    ├── Hip
 │    ├── Knee
 │    └── Ankle
 │
 └── RIGHT LEG
      ├── Hip
      ├── Knee
      └── Ankle
```

실제 MJCF를 기준으로 정확한 DOF 구조를 확정한다.

## C. XL330 ↔ AX-12A 정밀 비교

비교 항목:

- 외형
- 무게
- 축 위치
- Horn
- Mounting hole
- 전압
- 토크
- 속도
- 회전범위
- Resolution
- Backlash
- 통신 Protocol
- Feedback
- Current/Load
- 제어주기

## D. AX-12A 변환 설계표 작성

각 부품별로:

```text
KEEP
MODIFY
REDESIGN
REMOVE
NEW PART
```

를 지정한다.

---

# 24. 최종 목표

최종적으로는 단순한 Microduck Clone이 아니라 다음 플랫폼을 구축한다.

> **Microduck-AX12A Research Platform**

### 구성

```text
Mechanical Design
      +
Structural Analysis
      +
3D Printing
      +
Embedded Control
      +
DYNAMIXEL AX-12A
      +
MuJoCo
      +
PPO
      +
ONNX
      +
Sim2Real
```

이를 통해 하나의 프로젝트에서

- Reverse Engineering
- Mechanical CAD
- FEA
- 3D Printing
- Embedded System
- Robot Kinematics
- Dynamics
- MuJoCo
- Reinforcement Learning
- Sim2Real

까지 연결할 수 있다.
