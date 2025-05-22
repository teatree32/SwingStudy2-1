<!DOCTYPE html>
<html>
<head>
        <link rel="stylesheet" type="text/css" href="sytle.css">
</head>
<body>
        <h1>[2025 SWING Reversing Study] WinAntiDbg0x100 Write-Up</h1>
</body>
<br>
<br>
</html>

문제설명을 보자 안티티버깅 문제라고 한다. 

문제 파일을 바로 실행시켜보자

![image](https://github.com/user-attachments/assets/58bb648e-145d-4457-931c-4bfe141fc061)

특별한건 없어 보인다. 바로 x32dbg로 열어보았다.

```main```에서 쭉 따라가다 보니 실행했을 때 봤던 문자열들을 발견했다. 

이어서 조금 더 살펴보니 ```IsDebuggerPresent``` 함수를 발견했다.

![image](https://github.com/user-attachments/assets/fa2e5db4-d92b-490f-8ef3-020aae2f2027)

한줄씩 실행시키면서 살펴보면 먼저 ```IsDebuggerPresent``` 함수를 호출한다. 

![image](https://github.com/user-attachments/assets/c7db4564-c69d-4d6b-8c49-8a51f541b8f5)

이때 ```eax``` 값은 ```0x1```이고 ```test eax, eax``` 는 ```eax```가 0이면 ```ZF```가 1, 1이면 ```ZF```가 0이 될것 이다.

eax 값이 1이니 ZF가 0으로 설정되고 ZF가 1이 아니니 다음줄인 ```je winantidbg0x100.A6161B```가 실행 되지 않는다.

그러면 디버거가 감지되었다고 출력된다.

즉, ZF를 1로 바꾼다면 쉽게 우회 할 수 있다.

```je``` 명령어가 시작되기 전에 ZF를 1로 바꾸고 계속 실행해보자

![image](https://github.com/user-attachments/assets/993b4d4c-0118-44a4-b7aa-a968fd142144)

그럼 예상대로 ```je``` 명령어가 실행된다. 계속 한 줄씩 실행하다보면 flag가 나온다.

![image](https://github.com/user-attachments/assets/f1ad674a-d9b9-417c-8fab-5d9e5cb19167)

picoCTF{{d3bug_f0r_th3_Win_0x100_e70398c9}}






