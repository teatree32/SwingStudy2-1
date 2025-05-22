<!DOCTYPE html>
<html>
<head>
        <link rel="stylesheet" type="text/css" href="sytle.css">
</head>
<body>
        <h1>[2025 SWING Reversing Study] anti-debugging Write-Up</h1>
</body>
<br>
<br>
</html>

문제 파일을 실행해보자

![image](https://github.com/user-attachments/assets/d9bc1d8b-021c-4b01-a961-3d64e92c1839)

패스워드를 입력하라고 나온다. 일단 아무값이나 넣으니 종료되버린다.

IDA로 열어보았다.

실행흐름을 쭉 보면

![image](https://github.com/user-attachments/assets/17b2fb9e-4267-4088-8a95-a87291a56f8c)

위의 사진에서 빨간 박스가 main 함수인데 main에서 왼쪽은 password가 맞았을 경우고 오른쪽은 password가 틀렸을 경우다.

Strings 뷰를 보면 디버깅 검사뿐만아니라 뭔가 많이 검사하고 있는 것을 알 수 있는데 그 중에서 check 문자열이 눈에 띈다.

![image](https://github.com/user-attachments/assets/31025983-7e2d-4d62-ae51-737f8d687d84)

해당 문자열이 있는 위치로 이동하면 메시지 박스가 있는 것을 알 수 있는데 아마도 flag일 것 같다.

![image](https://github.com/user-attachments/assets/318281a2-d1dd-41a8-8764-d33a6a14060b)

해당 코드 위로 쭉 타고 올라가다보면 흐름을 파악 할 수 있는데

password를 맞추면 디버깅을 포함한 여러 검사를 한다는 것을 알 수 있었다. 

(+문자열에서도 But ~~ 라고 했는데 이게 "비번은 맞지만 다른 검사에서 탈락"을 의미하는 것 같다.)

모든 검사를 통과하고 나면 문자열 디코딩 후 메시지박스를 호출한다.

그래서 아예 main 함수에서 바로 문자열 디코딩 후 메시지박스를 호출하는 위치로 점프하도록 패치하면 될것 같다고 생각했다.

계속 IDA에서 에러가 나서 x32dbg로 패치했다.

이동할 위치는  ```0x401663```이고 main에서 패스워드를 검사하는 곳은 아래와 같다.

![image](https://github.com/user-attachments/assets/26e2ac1d-cbb6-4b52-b8f5-f13f27331c7b)

해당 부분을 ```0x401663```이동하도록 패치하면 아래와 같다.

![image](https://github.com/user-attachments/assets/3e04062d-42a3-4af3-adee-17db17effad4)

패치 후 다시 디버깅시작해서 password 아무거나 넣으면 flag가 나온다.

![image](https://github.com/user-attachments/assets/12bb7d32-0f93-4a5b-b0b6-aa36dacf8296)

SECCON{check_Ascii85}

