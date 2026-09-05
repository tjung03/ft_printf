# ft_printf

**가변 인자를 받아 서식 문자열을 해석하고 표준 출력에 기록하는 C 라이브러리입니다.** 42 Seoul 프로젝트로, 변환 지정자·플래그·출력 폭·정밀도를 파싱한 뒤 자료형별 출력 함수로 연결합니다.

## 핵심 구현

| 구현 | 내용 | 코드 |
|---|---|---|
| 서식 파싱 | 지정자 탐색, 옵션 추출, 변환 후 상태 초기화 | [parsing_data.c](parsing_data.c) |
| 옵션 조합 | `-`·`0`·`#`·공백·`+`, 숫자 또는 `*`로 폭·정밀도 입력 | [parsing_options.c](parsing_options.c), [상세 옵션](parsing_detailed_options.c) |
| 자료형별 출력 | 문자·문자열·정수·포인터·16진수 처리 분리 | [handle_specifier.c](handle_specifier.c) |
| 숫자 변환 | 10진수·16진수 문자열 생성과 부호·접두사·padding 처리 | [itoa_base.c](itoa_base.c), [정수 출력](get_i_values.c) |
| 출력 길이 관리 | 일반 문자와 변환 결과의 출력 길이를 누적해 반환 | [ft_printf.c](ft_printf.c) |

## 지원 서식

| 지정자 | 입력 |
|---|---|
| `%c` · `%s` | 문자 · 문자열 |
| `%d` · `%i` | 부호 있는 정수 |
| `%u` | 부호 없는 정수 |
| `%x` · `%X` | 소문자 · 대문자 16진수 |
| `%p` | 포인터 |
| `%%` | 퍼센트 문자 |

`%8.3s`처럼 폭과 정밀도를 지정하거나 `%*.*s`로 인자에서 받을 수 있습니다. 음수 폭은 왼쪽 정렬로 처리하고, 음수 동적 정밀도는 정밀도 지정을 해제합니다.

정수 출력에서는 부호·공백·0 채움 순서를 나누고, 16진수의 `#`는 `0x`·`0X` 접두사를 처리합니다. 숫자 정밀도와 `0` 플래그가 함께 들어올 때는 정밀도 여부에 따라 padding 경로를 선택합니다.

## 저장소 구조

```text
.
├── ft_printf.c / .h         # 진입 함수와 서식 상태 구조체
├── parsing_*.c              # 지정자·플래그·폭·정밀도 파싱
├── handle_specifier.c       # 자료형별 처리 분기
├── get_*_values.c           # 값 추출과 출력
├── get_*_details.c          # 부호·접두사·정밀도·padding
├── itoa_base.c              # 숫자 문자열 변환
├── libft/                   # 포함된 문자열·메모리 유틸리티
└── Makefile                 # libftprintf.a 생성
```

## 빌드와 사용

C 컴파일러와 Make가 필요합니다. `make`는 포함된 `libft`를 먼저 라이브러리로 만든 뒤 printf 오브젝트를 합쳐 `libftprintf.a`를 생성합니다.

```bash
make
```

다음 내용을 로컬 `example.c`로 저장합니다.

```c
#include "ft_printf.h"

int main(void)
{
    ft_printf("name=%-8.3s value=%+06d hex=%#x\n", "abcdef", 42, 42u);
    ft_printf("[%*.*s]\n", 8, 3, "abcdef");
    return (0);
}
```

```bash
cc -Wall -Wextra -Werror example.c libftprintf.a -o example
./example
```

```text
name=abc      value=+00042 hex=0x2a
[     abc]
```

GCC에서 `itoa_base.c`의 부호 없는 값과 0의 비교가 `-Werror=type-limits`로 빌드를 중단하면, 해당 진단을 경고로 유지해 원본 코드를 빌드할 수 있습니다.

```bash
make CFLAGS='-Wall -Wextra -Werror -Wno-error=type-limits'
```

## 빌드 대상과 호출 규약

`ft_printf(const char *format, ...)`는 위 지원 서식을 사용하고 지정자에 맞는 인자 타입을 전달합니다. 포인터 변환은 `unsigned long`을 사용하는 구현입니다.

| 명령 | 결과 |
|---|---|
| `make` · `make bonus` | 같은 소스와 `libftprintf.a` 빌드 |
| `make clean` | printf·libft 오브젝트 삭제 |
| `make fclean` | 오브젝트와 정적 라이브러리 삭제 |
| `make re` | 전체 재빌드 |

기존 [ft_printf 학습 노트](https://tjung.notion.site/ft_printf-f5104a4779324f9ea8ffd29dc922ffff)를 함께 보관합니다.
