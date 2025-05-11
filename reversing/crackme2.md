<!DOCTYPE html>
<html>
<head>
        <link rel="stylesheet" type="text/css" href="sytle.css">
</head>
<body>
        <h1>[2025 SWING Reversing Study] crackme2 Write-Up</h1>
</body>
<br>
<br>
</html>

crackme2.exe를 실행시켜보았다.

Name과 Serial을 입력하는 칸이나오는데 아무거나 입력해보니 "Wrong serial!" 메시지박스가 든다.

![image](https://github.com/user-attachments/assets/5f201d30-606b-43f9-bcd2-c53626feb608)
![image](https://github.com/user-attachments/assets/ab8cb7e7-a499-4d5c-9eb6-fe9438f24f7b)

바로 dbg로 열어보자

우선 문자열 참조를 해보니 아래처럼 올바른 입력값을 입력했을 때 나오는 것으로 추측되는 문자열을 찾았다.

![image](https://github.com/user-attachments/assets/6d686d33-79a5-4181-8abd-ce75c3ed692d)

해당 문자열로 이동해서 명령어를 확인해보았다. 

![image](https://github.com/user-attachments/assets/08aafd08-7114-47f5-ac57-99ed1e4743b7)

해당 메시지박스가 출력되려면 위쪽 코드를 확인해봐야 할 것 같아 확인 해보았다. 
뭔가 함수명? 같은 것들이 있길래 검색을 해보니 Visual Basic for Applications(VBA) 런타임 함수들을 사용하는 프로그램에서 주로 사용하는 함수라고 한다.

- ```__vbaFreeObj``` : VBA 오브젝트 메모리를 해제하는 함수
- ```__vbaVarTstEq``` : 두 변수의 값을 비교(equality test)하는 함수
- ```__vbaVarDup``` : 변수 복사를 수행하는 함수

아마도 ```__vbaVarTstEq``` 함수에서 입력한 값이 ax 레지스터에 저장되는 것 같다.
그리고 ```ax == 0``` 인 경우 즉, 두 값이 일치하지 않으면 ```0x403408```으로 점프한다.

```__vbaVarTstEq``` 함수 실행 전에 push되어 들어온 eax, edx가 인자일 것이고 그럼 두 인자중 하나가 serial 값일 것으로 추측된다.
그럼 eax, edx 값 변화를 확인 해보자 

아래 사진 처럼 0x00403324에 BP를 걸고 실행해서 Name에 user를 Serial에 1234를 넣고 Check를 눌렀다.
edx값이 0x0019F1F4로 바뀌고 멈췄다. 

![스크린샷 2025-05-12 034446](https://github.com/user-attachments/assets/06f91612-f622-42a2-bc35-34b07f14377d)

해당 값으로 찾아보니 근처에 내가 입력한 1234가 있고 "D9D7C9D6" 문자열이 있다 아마 이게 Serial 값인것 같다.

Name에 user를 Serial에 D9D7C9D6 넣어 Check를 누르니 

![스크린샷 2025-05-12 034606](https://github.com/user-attachments/assets/62eb81af-f293-42d6-873c-62c328f47ee3)

성공했다!!
