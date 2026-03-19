# Jetson Orin Nano Developer Kit PLA Case 열해석

## 1. 프로젝트 개요
본 해석은 **Jetson Orin Nano Developer Kit**를 **PLA 케이스** 내부에 장착한 구조에 대해,  
ANSYS를 이용하여 **Steady-State Thermal(정상상태 열해석)** 을 수행한 1차 검토 결과를 정리한 문서이다.

해석의 1차 목적은 다음과 같다.

- 내부 발열원의 최고 온도 경향 확인
- PLA 케이스가 열을 얼마나 가두는지 1차 검토
- 향후 열유체 해석(Fluent CHT) 전 기본 열거동 파악
- 통풍구 효과 비교 및 설계 최적화를 위한 사전 단계 확보

---

## 2. 해석 대상

### 시스템 구성
- **Device**: Jetson Orin Nano Developer Kit
- **Case Material**: PLA
- **Cooling**: Fan 포함
- **Board**: 실제 PCB 형상 사용
- **Heat Source**: SoC/모듈 발열부

### 구조 개요
해석 대상은 다음 부품들로 구성된다.

- 상부 케이스
- 하부 케이스
- Jetson Orin Nano Developer Kit
- Fan 및 히트싱크 구조
- PCB 및 모듈부

---

## 3. 해석 목적

본 단계의 해석 목적은 아래와 같다.

1. **내부 최고 온도 확인**
2. **케이스 내부 열 축적 경향 확인**
3. **향후 통풍구 효과 비교를 위한 기준 모델 확보**
4. **열유체 유동 해석 이전의 초기 열 검증 수행**

본 해석은 정상상태 열해석이므로,  
실제 내부 공기 유동 및 fan에 의한 강제대류는 직접 해석하지 않고  
**외부 대류 경계조건으로 단순화**하여 적용하였다.

---

## 4. 해석 조건

### 4.1 해석 유형
- **ANSYS Steady-State Thermal**

### 4.2 발열 조건
- **총 발열량**: `15 W`
- 발열은 **팬 하부 모듈 body(SoC 발열부를 대표하는 body)** 에 부여
- 발열 방식:
  - `Heat Generation` 적용
  - 총 15W를 body 체적 기준 체적발열률로 환산하여 입력

### 4.3 재질 조건

#### PLA
- Density: `1240 kg/m^3`
- Specific Heat (Cp): `1800 J/kg-K`
- Thermal Conductivity: `0.18 W/m-K`

#### PCB Equivalent
- Density: `1900 kg/m^3`
- Specific Heat (Cp): `900 J/kg-K`
- Thermal Conductivity: `0.6 W/m-K`

#### SoC / Module Equivalent
- Density: `2300 kg/m^3`
- Specific Heat (Cp): `800 J/kg-K`
- Thermal Conductivity: `5 W/m-K`

> 주의: SoC/Module 재질은 NVIDIA의 공식 패키지 적층 물성이 공개되지 않은 관계로,  
> 본 해석에서는 **초기 열해석용 등가 재질**로 가정하여 사용하였다.

### 4.4 외기 및 대류 조건
- Ambient Temperature: `22 °C`
- Convection Film Coefficient: `5 W/m²·K`

적용 위치:
- 케이스 외부 표면 전체 또는 외기에 노출되는 면

---

## 5. 해석 절차

### Step 1. Geometry Import
- `Orin.x_t` 파일을 ANSYS Workbench에 불러옴
- Geometry는 Parasolid 형식 사용

### Step 2. Material 정의
Engineering Data에서 아래 재질을 정의함.
- PLA
- PCB_Equivalent
- SoC_Material 또는 Module_Equivalent

### Step 3. Material Assignment
각 body에 재질 지정
- Case Top / Bottom → PLA
- PCB → PCB Equivalent
- SoC 또는 Module Body → SoC/Module Equivalent

### Step 4. Heat Source 정의
- 팬 아래 발열 모듈 body에 발열 적용
- 총 발열량 15W 기준으로 Heat Generation 입력

### Step 5. Boundary Condition 정의
- 외부 노출면에 Convection 적용
- 자연대류 가정

### Step 6. Mesh 생성
- 기본 mesh 생성
- 필요 시 발열부 주변 국부 refinement 적용 가능

### Step 7. Solve
- Solution에 Temperature 결과 항목 추가
- 정상상태 열해석 수행

---

## 6. 모델링 가정 및 한계

본 해석은 **1차 정상상태 열해석**으로, 다음과 같은 가정을 포함한다.

### 가정
- 발열은 SoC가 위치한 모듈 body에 집중된 것으로 가정
- 외부 냉각은 자연대류 계수로 단순화
- 내부 공기 유동은 직접 해석하지 않음
- fan 회전 효과는 직접 반영하지 않음
- SoC/모듈 재질은 등가 물성 사용

### 한계
- 내부 공기 순환 및 실제 fan 유동을 반영하지 못함
- 통풍구 효과를 정밀하게 판단하기 어려움
- 국부 hotspot 분포는 실제보다 단순화될 수 있음
- 접촉열저항(TIM, 접촉면 상태 등)을 정밀 반영하지 않음

---

## 7. 해석 결과에서 중점적으로 볼 항목

정상상태 열해석 결과에서 아래 항목들을 우선적으로 확인한다.

- SoC/모듈 최고 온도
- 히트싱크 온도 분포
- PCB 온도 수준
- 케이스 내부면/외부면 온도
- PLA 케이스의 열 축적 경향

---

## 8. 향후 계획

본 해석은 1차 열 검토 단계이며, 다음 단계에서는 **Fluent 기반 열유체 해석(CHT)** 으로 확장할 예정이다.

### 다음 단계 목표
- 내부 공기 유동 해석
- fan 영향 반영
- 상부/하부 통풍구 효과 비교
- 유동 dead zone 확인
- 설계 최적화 수행

### 비교 예정 설계안 예시
- 현재 통풍구 구조
- 상부 홀 확대안
- 하부 홀 추가/확대안
- 측면 통풍구 추가안
- 케이스 형상 변경안

---

## 9. 사용 소프트웨어
- ANSYS Workbench
- ANSYS Mechanical
- Geometry Source: Parasolid (`.x_t`)

---

## 10. 파일 구성 예시
```bash
project/
├─ Orin.x_t
├─ README.md
├─ ansys_project.wbpj
└─ ansys_project_files/