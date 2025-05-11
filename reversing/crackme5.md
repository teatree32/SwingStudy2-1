<!DOCTYPE html>
<html>
<head>
        <link rel="stylesheet" type="text/css" href="sytle.css">
</head>
<body>
        <h1>[2025 SWING Reversing Study] crackme5 Write-Up</h1>
</body>
<br>
<br>
</html>

crackme5.exe를 실행해보았다.

![image](https://github.com/user-attachments/assets/72bd27ae-0f67-4167-9745-d1d2ff61279f)

임의로 Serial 값을 넣어주니 맞지 않다고 나온다.

![image](https://github.com/user-attachments/assets/63bebe3a-d1da-4e80-94c2-3852f6e2d2dd)

바로 dbg 돌려보자

문자열 참조로 올바른 Serial 값이 들어갔을 때 실행되는 것 같은 메시지 박스를 찾았다.

코드 대부분이 시스템 콜로 작성되어 있어 해석하기 쉬울것 같다.

![image](https://github.com/user-attachments/assets/3d97e4f4-e419-443f-8793-6426c56d5368)

메시지 박스위쪽으로 살펴보면 입력한 Serial 값과 특정 문자열을 비교하는 것을 알 수 있다.

```0x004010FC```에 BP를 걸로 실행한후 Serial 값에 1234를 넣어 Check를 누르니 내가 입력한 1234와 비교하는 문자열이 보인다.

![image](https://github.com/user-attachments/assets/dd38f97a-dbff-4406-b3dd-32818a7c734f)

해당 문자열 "L2C-57816784-ABEX"을 Serial 값에 넣었다.

![image](https://github.com/user-attachments/assets/1194826d-c41a-4167-8ba4-590b9b14ca69)

성공이다!!
