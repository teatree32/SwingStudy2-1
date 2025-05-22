<!DOCTYPE html>
<html>
<head>
        <link rel="stylesheet" type="text/css" href="sytle.css">
</head>
<body>
        <h1>[2025 SWING Reversing Study] WinAntiDbg0x300 Write-Up</h1>
</body>
<br>
<br>
</html>

문제 설명을 읽어보자 관리자 권한으로 실행하라고 한다. 

실행해주면 아래처럼 나온다.

![image](https://github.com/user-attachments/assets/1d32f656-49bb-4847-a068-2503dfed58ea)

x32dbg로 열어보았다. 실행코드로 가보니 크게 눈에 띄는게 없다.

![image](https://github.com/user-attachments/assets/061e14f7-ea0f-4eab-afaf-32a1327948a9)

그냥 IDA로 열어봤다. 뭐지 이건또 뭔 일이지??

start 함수밖에 안 뜬다.

![image](https://github.com/user-attachments/assets/e0e8d2af-6bbb-43dc-8613-b9f424503a09)

도저히 모르겠어서 구글링해봤다.

exe 파일이 UPX로 압축되어 있어서 그런거라고 한다.

![image](https://github.com/user-attachments/assets/f545b49e-3cee-438c-8e3d-daeeb71a6746)

kali에서 UPX 압축 풀면된다.

![image](https://github.com/user-attachments/assets/421bc5c5-3a77-407d-9cc0-ab02e8e1ce7c)

다시 IDA로 열어보자

일단 IDA에서 디버깅을 해보면 아래처럼 디버거가 감지되었다고 나온다.

![image](https://github.com/user-attachments/assets/f0e7ac9b-c3c6-4421-a5b2-ec09f5b4a614)

해당 오류 메시지가 출력되는 부분을 찾았다.

![image](https://github.com/user-attachments/assets/a051e1a1-fffc-4297-bdbf-2d0fbe4d93b8)

해당 부분을 그래프로 살펴보면 ```jz``` 명령어에서 분기되어 오류 메시지가 출력되는 것을 알 수 있다.

![image](https://github.com/user-attachments/assets/bb5281e1-9125-4bfa-8a1b-d2a8bd34ad88)

자세한건 더이상 잘 모르겠어서 flag가 출력되는 부분을 찾아보기로 했다.

다시 문자열이 보이는 곳에서 flag 관련 문자열이 있는지 찾아 보았다.

![image](https://github.com/user-attachments/assets/95c84fca-0a80-41db-9aca-93be9969d48e)

찾았다 해당 부분에서 플래그를 출력한다. 호출되는 부분을 살펴보면 ```loc_403929```로 호출된다.

![image](https://github.com/user-attachments/assets/fb856f95-15c8-40e2-92d0-f5656b43673e)

해당 부분에서 위로 올라가보면 아래와 같이 나온다.

![image](https://github.com/user-attachments/assets/21125dc6-0f96-485c-b895-d45e7c8827b7)

전체적으로 흐름만 보면 빨간박스 부분에서 왼쪽으로 가면 지속적으로 디버거를 확인하는 루프에 들어가 디버가가 있으면 프로그램을 종료하는 것 같다. 

오른쪽으로 가면 파란 박스를 기준으로 분기해서 flag가 나오는 것 같다.

일단 루프문에 빠지지 않고 실행되는게 중요한 것 같으니 프로그램이 시작될 때 ```jz``` 명령어가 아니라 ```jmp``` 명령어를 써서 플래그에 상관없이 무조건 오른쪽으로 진행되도록 바꿔보면 어떨까 싶다.

Edit - Patch program - Assemble 을 통해 ```jz``` 명령어를 ```jmp``` 명령어로 바꿨다.

![image](https://github.com/user-attachments/assets/0a4469fc-be57-4e3c-9785-13fd26f9fb72)

그러면 아래와 같이 실행 흐름이 바뀌게 된다.

![image](https://github.com/user-attachments/assets/4745c3ea-72f2-4fb1-bd51-b3025b430e9d)

Edit - Patch program - Apply patches to input file 로 path한거 저장후 실행해보자

![image](https://github.com/user-attachments/assets/d8434f4b-c5de-446f-ad3c-d46198ed138e)

flag가 나왔다!!

picoCTF[Wind0ws_antid3bg_0x300_86fcf897}
