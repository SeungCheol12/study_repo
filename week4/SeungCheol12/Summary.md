3장 예외처리 요약
1. 예외란 무엇인가
프로그램 실행 중 정상적인 흐름을 방해하는 사건을 예외(Exception)라고 한다. 문법 오류가 실행 전에 잡히는 것과 달리, 예외는 실행 도중에 발생한다. 존재하지 않는 파일을 열거나, 0으로 나누거나, 리스트의 범위를 벗어난 인덱스에 접근하는 경우가 대표적이다. 예외를 처리하지 않으면 프로그램은 그 지점에서 중단되고 트레이스백을 출력한 뒤 종료된다.

핵심은 예외가 "버그"와 동의어가 아니라는 점이다. 네트워크 단절이나 사용자의 잘못된 입력처럼 정상적인 상황에서도 충분히 발생할 수 있는 사건이므로, 이를 예상하고 대응하는 것이 예외 처리의 목적이다.

2. try-except 기본 구조
try:
    number = int(input("숫자를 입력하세요: "))
    result = 100 / number
except ValueError:
    print("숫자가 아닌 값을 입력했습니다.")
except ZeroDivisionError:
    print("0으로 나눌 수 없습니다.")
try 블록에 예외가 발생할 수 있는 코드를 넣고, except 블록에서 처리한다. except는 여러 개를 나열할 수 있으며 위에서부터 순서대로 매칭되므로, 구체적인 예외를 먼저 쓰고 포괄적인 예외를 나중에 써야 한다. 순서가 반대면 아래쪽 except는 영원히 실행되지 않는다.

여러 예외를 한 번에 잡으려면 튜플로 묶는다. 예외 객체가 필요하면 as 로 받는다.

except (ValueError, TypeError) as e:
    print(f"입력 오류: {e}")
except: 처럼 아무 예외나 잡는 방식은 피해야 한다. KeyboardInterrupt 같은 시스템 예외까지 삼켜버려서 프로그램을 중단할 수 없게 만들고, 진짜 버그를 숨긴다. 최소한 except Exception as e: 로 범위를 좁히고 로그를 남기는 것이 낫다.

3. else와 finally
try:
    f = open("data.txt", "r")
except FileNotFoundError:
    print("파일이 없습니다.")
else:
    print(f.read())
    f.close()
finally:
    print("작업 종료")
else는 예외가 발생하지 않았을 때만 실행된다. try 블록에는 예외가 날 수 있는 최소한의 코드만 두고, 성공했을 때의 후속 처리는 else로 분리하면 어느 줄에서 예외가 났는지 명확해진다.

finally는 예외 발생 여부와 무관하게 항상 실행된다. 심지어 try 블록 안에서 return 을 만나도 함수가 반환되기 직전에 finally가 먼저 실행된다. 파일 닫기, DB 연결 해제, 락 반납 같은 정리 작업에 쓴다. 다만 파일이나 커넥션처럼 컨텍스트 매니저를 지원하는 대상은 with 문을 쓰는 편이 더 간결하고 안전하다.

4. 예외 계층과 직접 발생시키기
파이썬의 예외는 클래스 상속 구조를 가진다. BaseException 이 최상위이고, 그 아래 Exception 이 있으며 우리가 다루는 대부분의 예외는 여기서 파생된다. ValueError, TypeError, KeyError 등이 모두 Exception 의 자식이다. 이 구조 때문에 부모 예외를 잡으면 자식 예외도 함께 잡힌다.

raise 로 예외를 직접 발생시킬 수도 있다. 도메인에 맞는 예외를 직접 정의하면 호출하는 쪽에서 상황을 구분해 처리하기 쉬워진다.

class InsufficientBalanceError(Exception):
    """잔액이 부족할 때 발생하는 예외"""
    pass

def withdraw(balance, amount):
    if amount > balance:
        raise InsufficientBalanceError(f"잔액 부족: 현재 {balance}원")
    return balance - amount
5. 정리하며
예외 처리의 목표는 오류를 숨기는 것이 아니라 예상 가능한 실패에 대해 프로그램이 어떻게 반응할지 명시하는 것이다. 그래서 잡을 수 있는 예외만 좁게 잡고, 처리할 수 없는 예외는 오히려 그대로 위로 전파시키는 편이 낫다. 잡아서 아무것도 하지 않는 코드는 문제를 나중으로 미룰 뿐이다.

남은 학습 과제
컨텍스트 매니저(with)와 __enter__ / __exit__ 직접 구현해보기
예외 체이닝(raise ... from ...)의 활용 사례 정리
logging 모듈과 결합한 예외 기록 패턴
