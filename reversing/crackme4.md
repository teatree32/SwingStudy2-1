<!DOCTYPE html>
<html>
<head>
        <link rel="stylesheet" type="text/css" href="sytle.css">
</head>
<body>
        <h1>[2025 SWING Reversing Study] crackme4 Write-Up</h1>
</body>
<br>
<br>
</html>

crackme4.exe를 실행해보았다.

![image](https://github.com/user-attachments/assets/7eb4a128-1965-44a3-a857-4cd70f93de48)

Serial 값을 넣는 칸이 있지만 어떤 값을 넣어도 Registerd 버튼이 활성화되지 않는다.

바로 dbg 돌려보자

우선 문자열 참조로 Serial 값이 올바르게 들어갔을 때 나오는 것 같은 메시지 박스를 발견했다. 자세히 살펴보자

![image](https://github.com/user-attachments/assets/17fa325b-9993-46ce-8617-36cc5034d48c)

해당 메시지박스가 출력되려면 위쪽 코드를 확인해봐야 할 것 같아 확인 해보았다. 
상단으로 이동해서 스택 프레임 부분을 통해 이벤트 핸들러를 찾아보았다.

![image](https://github.com/user-attachments/assets/dc05c02b-d42f-4df2-b878-64e6a73f9bfd)

```0x00401E10```에 BP를 걸고 실행해보았는데 프로그램 실행 루틴이 해당 이벤트 핸들러 부분까지 도달하지 못한다.
그렇다면 아마도 Serial 값이 정답일 때 Registered 버튼이 활성화되어서 메시지 박스가 있는 이벤트 핸드러로 넘어오는 것 같다.

checkme2와 비슷하게 아마 입력한 Serial 값과 정답인 Serial 값을 비교하는 함수가 있을것 같다.
찾아보자

cmp라는거 보니까 이거인것 같다.

![image](https://github.com/user-attachments/assets/4cd3219f-bda1-458b-972d-07d32c22077b)

확인해보니 맞는 것 같다. 

![image](https://github.com/user-attachments/assets/e3711b1f-eb45-4ec9-84ef-3aa1b17240f6)

해당 함수에 BP를 걸고 실행시켜보았다.

문자열을 비교하는 이벤트를 찾을 수 있었다. 해당 부분에서 비교해주는 값의 스택을 확앤해보니 "2075625" 값이 들어있다.

![image](https://github.com/user-attachments/assets/587818f3-c499-4d69-a27d-f95f617be50e)

해당값을 넣으니 Registered 버튼이 활성화되었다.

![image](https://github.com/user-attachments/assets/d0f81cfd-cb94-415f-9374-746398f3748a)
![image](https://github.com/user-attachments/assets/fb2b906d-a062-4614-972c-ed5b5fd9a4d8)

성공했다!!
