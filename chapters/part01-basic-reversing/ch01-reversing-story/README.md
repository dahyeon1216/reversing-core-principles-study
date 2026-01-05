# Chapter 01 – Introduction

## 🔍 Reverse Engineering
**완성된 프로그램(바이너리)을 분석하여 내부 동작 원리, 구조, 로직을 이해하는 과정**

---

## 🧠 Analysis Methods

### 1. Static Analysis (정적 분석)
- 실행하지 않고 파일 자체를 분석
- 파일 형식, 크기, 헤더, Import/Export API, 문자열 정보 확인
- 디스어셈블러를 통해 내부 코드 구조 분석

**Disassembler**
- 기계어 → 어셈블리어 변환 도구
- CPU가 실제 실행하는 명령을 사람이 읽을 수 있도록 변환

---

### 2. Dynamic Analysis (동적 분석)
- 프로그램을 실제 실행하여 행위 분석
- 디버거를 사용해 코드 흐름과 메모리 상태 추적
- 파일 / 레지스트리 / 네트워크 동작 관찰

📌 **정적 분석을 통해 구조를 예측한 뒤,  
동적 분석으로 이를 검증하는 방식이 효과적**

---

## ⚠️ Common Misconception
- **리버싱 = 디버깅 ❌**
- 디버깅은 리버싱 기법 중 하나일 뿐, 리버싱의 일부

---

## 🔄 Code Representation Flow

```
[소스 코드]
HelloWorld.cpp
    |
    |  (Build / Compile)
    v
[실행 파일]
HelloWorld.exe
    |
    |  (기계어 형태)
    v
[Hex Editor]
(16진수 데이터)
    |
    |  (분석)
    v
[Disassembler]
어셈블리 코드
```

---

## 🔧 Patch vs Crack

### Patch
- 파일 또는 프로세스 메모리를 수정
- 목적: 취약점 수정, 기능 개선

### Crack
- 기술적으로는 패치와 동일
- 목적: 보호 우회 등 비합법적 사용

⚠️ **리버싱 기술은 양날의 검임을 항상 인지해야 함**

---

## 🔗 Full Notes (Notion)
👉 https://delicate-dish-b60.notion.site/1-2dfa4c0c42728020ae50fb2f28964353?source=copy_link
