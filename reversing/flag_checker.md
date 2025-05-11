<!DOCTYPE html>
<html>
<head>
        <link rel="stylesheet" type="text/css" href="sytle.css">
</head>
<body>
        <h1>[2025 SWING Reversing Study] flag_checker Write-Up</h1>
</body>
<br>
<br>
</html>

우선 문제 파일을 실행시켜보았다.

![image](https://github.com/user-attachments/assets/ea326a87-8668-4ed5-ac9d-61b2c4bf4450)

flag를 찾으면 될것 같다.

일단 소스코드가 없기 때문에 gdb로 분석해보았다.

![image](https://github.com/user-attachments/assets/df336047-4207-40bc-b419-0b1c31a132a6)

아 이건 생각지 못한 경우인데...

```main()``` 함수가 없다??? 이런경우가 다 있네

흠.. 웬만하면 gdb로 풀어보려고 했는데 너무 감도 못잡겠어서 그냥 IDA로 돌려보았다.

![image](https://github.com/user-attachments/assets/b5d4b458-4629-443e-8b6f-588e3df78bfe)

```main()``` 함수를 보면 

```s```는 40바이트 크기로 정의하고 있다.

```fgets()``` 함수로 최대 34문자를 읽고 있으니 오버플로우는 없을 것 같다.

이어서 ```strcspn(s, "\n")```는 첫 줄바꿈 문자의 위치를 찾아 null로 바꿔 개행 제거를 하고있다.

```sub_127A()``` 함수는 입력된 s를 받아 검증하는 함수인 것 같다. true를 반환하면 "Correct!", 아니면 "Incorrect!"를 출력한다.

```sub_127A()``` 함수가 중요한것 같다.

![image](https://github.com/user-attachments/assets/709c75cf-0eda-462b-af3e-35024f686abc)

이어서 ```sub_127A()``` 살펴보면

if문에서 입력 문자열의 길이가 34자인지 확인 하고 있고

```sub_11E9()``` 함수는 입력 문자열 ```a1```을 특정 조건으로 바꿔서 ```v3```에 결과를 저장하는 함수같다. 아마 암호화나 변환 함수이지 않을까 싶다.

for문에서 ```v3```와 ```byte_2020```을 1바이트씩 비교하고 34바이트 전부 일치하면 "Correct!"를 출력한다.


![image](https://github.com/user-attachments/assets/344d4b3c-9377-46dc-ac66-6991a517d00d)

```sub_11E9()``` 함수를 살펴보면 

입력된 문자열의 각 문자를 0x54(90)으로 xor 연산하고 result를 왼쪽으로 shift 5, 오른쪽으로 shift 3하고 있다. (8 * result = result << 3)

즉, 8비트 정수가 있을 때 왼쪽으로 3칸 밀고 밀려나간 상위 비트를 다시 오른쪽에 넣는 것이다. 

위의 연산을 식으로 만들어 보면 아래와 같다.

```
result = (input[i] ^ 0x5A) + i;
output[i] = ROL3(result); // 왼쪽으로 3비트 회전
```

위의 식을 복호화하는 식은 아래와 같다.
```
result = ROR3(output[i]);  // 오른쪽으로 3비트 회전
input[i] = (result - i) ^ 0x5A;
```


![image](https://github.com/user-attachments/assets/8a5bfd2e-c9de-418c-a5bb-f503877069ec)

다음으로 ```byte_2020```을 살펴보면 위와 같이 나온다.

```sub_11E9()``` 함수를 복호화하는 코드에 ```byte_2020```을 넣어서 exploit 코드를 작성해 보았다.

```
#exploit.py
def ror3(byte):
    return ((byte >> 3) | ((byte & 0x7) << 5)) & 0xFF

encrypted = [
    0xF8, 0xA8, 0xB8, 0x21, 0x60, 0x73, 0x90, 0x83, 0x80, 0xC3,
    0x9B, 0x80, 0xAB, 0x09, 0x59, 0xD3, 0x21, 0xD3, 0xDB, 0xD8,
    0xFB, 0x49, 0x99, 0xE0, 0x79, 0x3C, 0x4C, 0x49, 0x2C, 0x29,
    0xCC, 0xD4, 0xDC, 0x42
]

flag = ""
for i in range(34):
    rotated = ror3(encrypted[i])
    original = (rotated - i) ^ 0x5A
    flag += chr(original)

print(flag)
```

exploit.py를 실행시켜 보면

![image](https://github.com/user-attachments/assets/0071c9ae-1abb-453f-bdf2-91c8d52f680b)

flag로 추정되는 문자열이 나온다.

ENO{R3V3R53_3NG1N33R1NG_M45T3R!!!}

해당 문자열을 넣어보자

![image](https://github.com/user-attachments/assets/b00af76c-55b9-4ac6-9a02-739f20a0be1c)

정답이다!!
