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

도저히 모르겠어서 좀 찾아봤다. 

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

그렇다면 ```jz``` 명령문에서 점프하는 주소를 바꾸면 될것 같다.

다시 문자열이 보이는 곳에서 flag 관련 문자열이 있는지 찾아 보았다.

![image](https://github.com/user-attachments/assets/95c84fca-0a80-41db-9aca-93be9969d48e)

찾았다 해당 부분에서 플래그를 출력한다. 호출되는 부분을 살펴보면 ```loc_403929```로 호출된다.

![image](https://github.com/user-attachments/assets/fb856f95-15c8-40e2-92d0-f5656b43673e)

다시 ```jz``` 명령어로 돌아와 아래 사진처럼 코드를 바꿔준다.

![image](https://github.com/user-attachments/assets/24e13bc3-8fe8-49df-8d84-580909ee0ae8)


