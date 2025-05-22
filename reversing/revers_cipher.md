<!DOCTYPE html>
<html>
<head>
        <link rel="stylesheet" type="text/css" href="sytle.css">
</head>
<body>
        <h1>[2025 SWING Reversing Study] revers_cipher Write-Up</h1>
</body>
<br>
<br>
</html>

먼저 문제를 읽어보자

"We have recovered a binary and a text file. Can you reverse the flag.“

flag를 뒤집으라는 것 같다.

IDA로 rev를 열어 보았다. 
```
int __fastcall main(int argc, const char **argv, const char **envp)
{
  _BYTE ptr[23]; // [rsp+0h] [rbp-50h] BYREF
  char v5; // [rsp+17h] [rbp-39h]
  int v6; // [rsp+2Ch] [rbp-24h]
  FILE *v7; // [rsp+30h] [rbp-20h]
  FILE *stream; // [rsp+38h] [rbp-18h]
  int j; // [rsp+44h] [rbp-Ch]
  int i; // [rsp+48h] [rbp-8h]
  char v11; // [rsp+4Fh] [rbp-1h]

  stream = fopen("flag.txt", "r");
  v7 = fopen("rev_this", "a");
  if ( !stream )
    puts("No flag found, please make sure this is run on the server");
  if ( !v7 )
    puts("please run this on the server");
  v6 = fread(ptr, 0x18uLL, 1uLL, stream);
  if ( v6 <= 0 )
    exit(0);
  for ( i = 0; i <= 7; ++i )
  {
    v11 = ptr[i];
    fputc(v11, v7);
  }
  for ( j = 8; j <= 22; ++j )
  {
    v11 = ptr[j];
    if ( (j & 1) != 0 )
      v11 -= 2;
    else
      v11 += 5;
    fputc(v11, v7);
  }
  v11 = v5;
  fputc(v5, v7);
  fclose(v7);
  return fclose(stream);
}
```

main 함수를 보면 문자열 인덱스 7까지는 그대로 두고

인덱스 8부터 22까지는 짝수면 +5, 홀수면 -2를 연산을 수행한다.

즉, rev_this.txt에 저장된 값은 flag.txt에 있던 값이 위의 연산을 통해 변형된 것이고 
원래 값을 구하려면 반대로 계산하면 된다.

역계산 하기 위해 rev_this.txt를 불러와 문자열 앞의 인덱스 7까지는 그대로 가져오고 인덱스 8~22까지 짝수면 -5, 홀수면 +2가 되도록 코드를 작성해보았다.

![image](https://github.com/user-attachments/assets/adcc28cd-31e9-489e-bd05-f569c769f17a)

실행해보면 

![image](https://github.com/user-attachments/assets/0770b7bc-77fa-43e7-a0eb-ef1deae6393b)

원래 flag 값은 ```picoCTF{r3v3rs37ee84d27}``` 이다.

