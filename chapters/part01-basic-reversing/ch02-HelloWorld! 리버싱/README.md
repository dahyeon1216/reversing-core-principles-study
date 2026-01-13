# 2.1. Hello World! 프로그램

실습 소스 코드
```cpp
#include "windows.h"
#include "tchar.h"

int _tmain(int argc, TCHAR *argv[])
{
	MessageBox(NULL, 
			   L"Hello World!", 
			   L"www.reversecore.com", 
			   MB_OK);
	
	return 0;
}
```
## 실행 화면

HelloWorld.exe 실행 결과이다.  
MessageBox API를 통해 문자열이 출력된다.

<img width="261" height="212" alt="image" src="https://github.com/user-attachments/assets/c532c280-e67c-4bb7-9ca3-93e3b008cbef" />


이 때, Release 모드로 빌드해서 실행 파일을 만들었다.

---

## Release 모드 vs Debug 모드

**Debug = 디버깅용 (느림, 정보 많음)**  
**Release = 배포용 (빠름, 정보 적음)**

| 구분 | Debug | Release |
| --- | --- | --- |
| 최적화 | ❌ | ⭕ |
| 심볼 | ⭕ | ❌ |
| 속도 | 느림 | 빠름 |
| 분석 난이도 | 쉬움 | 어려움 |

빌드 과정에서 C 언어 소스 코드는 기계어(.exe) 파일로 변환된다.  
이러한 기계어는 사람이 직접 읽기 어렵기 때문에 디버거 유틸리티를 사용한다.

디버거에 탑재된 디스어셈블러 모듈이  
이 기계어를 어셈블리어 언어로 번역해서 보여주게 된다.

> **어셈블리 언어는 CPU에 종속되어 있다.**  
> 일반 PC에서 많이 사용되는 Intel x86 계열과  
> 모바일 제품에서 많이 사용되는 ARM 계열 CPU는  
> 서로 다른 어셈블리 명령어 형태를 가진다.

---

# 2.2 HelloWorld.exe 디버깅

본인은 x32dbg를 사용하였다.  
디버거 화면은 총 4개 영역으로 나뉜다.

<img width="1742" height="924" alt="image" src="https://github.com/user-attachments/assets/cf01a784-e508-4200-af4e-495b83430154" />


1. **Code Window** (왼쪽 상단)  
   실행 중인 어셈블리 코드를 보여주는 창
2. **Register Window** (오른쪽 상단)  
   CPU 레지스터 상태를 실시간으로 표시
3. **Dump Window** (왼쪽 하단)  
   메모리의 실제 값을 Hex / ASCII 형태로 보여주는 창
4. **Stack Window** (오른쪽 하단)  
   스택(Stack) 상태를 보여주는 창

> Code = 실행 흐름  
> Register = CPU 상태  
> Dump = 메모리 내용  
> Stack = 함수 호출 기록

---

## EP (Entry Point)

Windows 실행 파일의 코드 시작점으로  
CPU에 의해 가장 먼저 실행되는 코드 시작 위치이다.

- 항상 main 함수가 아님
- Stub code가 먼저 실행될 수 있음

**Stub Code**
- 실제 로직으로 연결되기 전의 임시 중간 코드
- 실질적인 기능은 없음
- 실제 로직은 다른 위치에 존재

---

## 기본 명령어

| 명령 | 단축키 | 의미 |
| --- | --- | --- |
| Run | F9 | 프로그램 실행 (다음 BP까지) |
| Pause | Ctrl + F9 | 실행 중 일시 정지 |
| Restart | Ctrl + F2 | 처음부터 다시 실행 |
| Step Into | F7 | 한 줄 실행 (함수 내부 진입) |
| Step Over | F8 | 한 줄 실행 (함수 건너뜀) |
| Step Out | Ctrl + F9 | 현재 함수 종료 후 복귀 |
| Run to Return | Ctrl + F9 | return까지 실행 |

---

## main 함수 찾기

처음에는 Step Into / Step Over를 반복하며 디버깅을 진행한다.

함수 호출을 만나면 내부로 진입해  
해당 함수가 main 함수인지 확인한다.

main 함수가 아니라면  
Run to Return 명령으로 빠져나와  
같은 방식으로 디버깅을 계속 진행한다.

이 과정을 반복하면  
MessageBoxW() API를 호출하는 코드를 발견할 수 있다.

이때 파라미터가  
`"Hello World!"`, `"www.reversecore.com"` 임을 확인할 수 있으며,  
이 API 호출이 포함된 함수가 main 함수이다.

---

# 2.3 디버거 좀 더 능숙하게 다루기

## 디버거 명령어

| 명령어 | 단축키 | 설명 |
| --- | --- | --- |
| Go to | Ctrl + G | 주소로 이동 (실행 ❌) |
| Execute till Cursor | F4 | 커서 위치까지 실행 |
| Comment | ; | 주석 추가 |
| Label | : | 라벨 추가 |
| Set / Reset BP | F2 | BP 설정 / 해제 |
| Run | F9 | BP까지 실행 |
| Show current EIP | * | 현재 EIP 위치 표시 |
| Show previous Cursor | - | 이전 커서 위치 |
| Preview CALL / JMP | Enter | 호출 주소 미리 보기 |

---

## Label vs Comment

**Label**
- 특정 주소에 붙이는 이름 (식별자)
- 해당 주소의 역할을 정의
- 함수 시작, 분기 지점, 중요한 위치에 사용

**Comment**
- 코드에 대한 설명 또는 메모
- 명령어 의미 해석 용도
- 실행 흐름에는 영향 없음

---

## 베이스 캠프

디버거를 재실행할 때마다  
EP 코드부터 다시 시작하는 불편함이 있다.

그래서 디버깅 중  
중요한 분석 지점을 저장해  
빠르게 이동할 수 있도록 사용하는 개념이 베이스 캠프이다.

### 베이스 캠프 설정 방법

1. **Goto**
   - 주소 기억 후 이동
   - Execute till Cursor로 실행

2. **Breakpoint**
   - 가장 많이 사용하는 방법
   - ALT + B로 BP 목록 확인

3. **Comment**
   - 주석을 달아 검색으로 이동

4. **Label**
   - 코드 가독성 향상
   - 흐름 파악에 매우 유용

---

# 2.4 원하는 코드를 빨리 찾아내는 방법

## 2.4.1 코드 실행 방법

프로그램의 기능이 명확한 경우  
명령어를 하나씩 실행하며  
원하는 위치를 직접 찾아가는 방법이다.

기능이 단순할 때는 효과적이지만  
코드 규모가 크거나 복잡한 경우에는 비효율적이다.

---

## 2.4.2 문자열 검색 방법

디버거는 프로세스 메모리를 분석해  
참조되는 문자열 목록을 제공한다.

All referenced text strings 명령을 사용하면  
프로그램에서 사용된 문자열을 확인할 수 있다.

문자열을 더블 클릭하면  
해당 문자열을 사용하는 코드로 이동한다.

Dump 창에서 Ctrl + G를 사용하면  
유니코드 문자열을 직접 확인할 수 있다.

> VC에서는 static 문자열을 기본적으로 유니코드로 저장한다.  
> static code란 프로그램 내부에 하드코딩된 문자열을 의미한다.

---

## 2.4.3 API 검색 방법 (1)

화면 출력과 같은 기능은  
Win32 API 사용을 의미한다.

기능에 따라 사용되었을 API를 예상하고  
해당 호출을 찾으면 디버깅이 쉬워진다.

본 예제에서는  
user32.MessageBoxW() API가 사용된다.

API 호출 목록은  
All intermodular calls 명령으로 확인할 수 있다.

---

## 2.4.4 API 검색 방법 (2)

Packer 또는 Protector가 사용된 경우  
API 호출 목록이 보이지 않을 수 있다.

**Packer**
- 실행 파일 압축 또는 암호화
- 실행 시 메모리에서 해제
- 분석 방해 목적

**Protector**
- 디버깅, 덤프, 리버싱 방어
- 안티 디버깅, 무결성 검사 포함

이 경우 로딩된 DLL 내부 API에  
직접 BP를 설정하여 추적한다.

- Alt + M : 로드된 모듈 확인
- Ctrl + N : API 이름 검색
- API 내부에 BP 설정 후 추적

---

# 2.5 “Hello World!” 문자열 패치

패치 대상은  
파일 또는 메모리가 될 수 있으며,  
코드와 데이터 모두 수정 가능하다.

---

## 문자열 패치 방법

### 1. 문자열 버퍼 직접 수정

MessageBoxW 함수에 전달되는  
문자열 주소의 버퍼를 직접 수정한다.

- Go to로 주소 이동
- Ctrl + E로 수정
- 디버거 종료 시 변경 내용 소멸
- Copy to executable file로 파일 반영 가능

> 원본 문자열보다 긴 문자열을 덮어쓸 경우  
> 뒤의 데이터를 훼손하지 않도록 주의해야 한다.  
> 유니코드 문자열은 2바이트 NULL로 종료된다.

---

### 2. 새로운 문자열 버퍼 생성

원본 문자열보다  
더 긴 문자열을 사용해야 할 경우 적용한다.

새로운 메모리 영역에 문자열을 생성하고  
해당 주소를 MessageBoxW 함수에 전달한다.

Dump 창에서  
NULL padding 영역을 문자열 버퍼로 사용할 수 있다.

어셈블리 창에서  
MessageBoxW 함수의 파라미터 주소를 수정하면  
출력 문자열을 변경할 수 있다.
