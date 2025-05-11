<!DOCTYPE html>
<html>
<head>
        <link rel="stylesheet" type="text/css" href="sytle.css">
</head>
<body>
        <h1>[2025 SWING Reversing Study] crackme3 Write-Up</h1>
</body>
<br>
<br>
</html>

crackme3.exe를 실행시켜보았다.

![image](https://github.com/user-attachments/assets/861ecae4-7add-4131-8983-b8e6e8542534)

keyfile이 있을 경우 OK를 누르라고 한다.
일단 그냥 눌러봤다.

![image](https://github.com/user-attachments/assets/c66f6141-3667-4e9f-9112-d74994e7fbf3)

keyfile이 없다고 뜬다.

바로 dbg로 돌려보았다.

main 실행 코드를 살펴보니 매우 짧은 편이다.

![image](https://github.com/user-attachments/assets/8cf5f08e-05d3-4fad-b39a-2ebf21553af7)

우선 눈에 띄는 것은 ```CreateFileA```와 ```GetFileSize``` 시스템 콜이다. 해당 시스템 콜을 검색해보니 
```CreateFileA```는 지정된 파일이나 디파이스에 대한 핸들을 생성하고 
```GetFileSize```는 얻은 핸들에서 파일의 크기를 반환하는 시스템 콜이다.

시스템 흐름을 보면 abex.l2c라는 이름의 파일의 주소를 받고 해당 주소를 ```GetFileSize``` 시스템 콜 인자로 넣어주는 것 같다.
그리고 ```GetFileSize``` 시스템 콜이 리턴한 파일의 크기(eax)를 ```0x12```(18)와 비교하여 같지않으면 ```0x00401060```로 점프한다.

그렇다면 파일명이 abex.l2c 인 18byte의 파일을 만들면 될것 같다.

```
# creatfile

with open("abex.l2c", "wb") as f:
    data = bytes([
        0x41, 0x42, 0x45, 0x58,      
        0x01, 0x02, 0x03, 0x04,
        0x05, 0x06, 0x07, 0x08,
        0x09, 0x0A, 0x0B, 0x0C,
        0x0D, 0x0E
    ])
    f.write(data)
```

위의 파이썬 코드로 abex.l2c 파일을 만들었다.

만들어진 abex.l2c 파일을 crackme3.exe와 같은 경로에 넣어주었다.

![image](https://github.com/user-attachments/assets/87195bd5-74c2-426a-b073-e96178cdf7f9)

![image](https://github.com/user-attachments/assets/da8a2d42-2e74-4146-be49-fa169594e3fa)

성공했다!!



