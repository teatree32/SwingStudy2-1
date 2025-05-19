<!DOCTYPE html>
<html>
<head>
        <link rel="stylesheet" type="text/css" href="sytle.css">
</head>
<body>
        <h1>[2025 SWING Forensic Study] CFReDS - Hacking Case Write-Up</h1>
</body>
<br>
<br>
</html>

문제 설명
---
https://cfreds-archive.nist.gov/Hacking_Case.html

On 09/20/04 , a Dell CPi notebook computer, serial # VLQLW, was found abandoned along with a wireless PCMCIA card and an external homemade 802.11b antennae. It is suspected that this computer was used for hacking purposes, although cannot be tied to a hacking suspect, G=r=e=g S=c=h=a=r=d=t. (The equal signs are just to prevent web crawlers from indexing this name; there are no equal signs in the image files.)  Schardt also goes by the online nickname of “Mr. Evil” and some of his associates have said that he would park his vehicle within range of Wireless Access Points (like Starbucks and other T-Mobile Hotspots) where he would then intercept internet traffic, attempting to get credit card numbers, usernames & passwords.

Find any hacking software, evidence of their use, and any data that might have been generated. Attempt to tie the computer to the suspect, G=r=e=g S=c=h=a=r=d=t.
 
첨부되어진 이미지 파일들을 다운받아주면 된다.

---
Q1. What is the image hash? Dose the acquisition and verification hash match?

FTK Imager에서 SCHARDT.001과 4Dell Latitude CPi.E01의 Verify Drive/Image를 확인해 보면 
E01 이미지에는 stored verification hash가 포함되어 있지만 DD 이미지에는 포함되어 있지 않다.

![image](https://github.com/user-attachments/assets/ab043a6e-0ec0-4721-bf7a-11849c628ee9)

![image](https://github.com/user-attachments/assets/fe03b024-88cf-4231-b1a6-0d3704262d7d)


A. AEE4FCD9301C03B3B054623CA261959A, Yes 

---
Q2. What operating system was used on the computer?

컴퓨터에서 사용하는 운영체제가 무엇인지 찾는 문제이다.
파일 구조상으로 Documents and Settings 폴더 안에 User 들이 들어 있기 때문에 Windows XP 같다.

![image](https://github.com/user-attachments/assets/e01d46b8-b9bd-4440-bfa1-874bf433ded7)

확실히 하기 위해 RegistryExplorer을 사용해 레지스트리에 들어 있는 키를 확인해보겠다.

운영체제와 관련된 내용은 SOFTWARE키에 있으니 C:\WINDOWS\system32\config\SOFTWARE 해당 경로에 있는 하이브를 추출해서 RegistryExplorer로 열어보자

![image](https://github.com/user-attachments/assets/14ca18b2-055d-47da-b341-45d66ed2afb9)

운영체제 정보를 담고 있는 레지스트리 키 경로는 다음과 같다.

HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion - ProductName

![image](https://github.com/user-attachments/assets/2fcab835-0ff0-42c5-a9fc-1cea984ce5d6)

예상한대로 Windows XP가 맞다.

A. Windows XP

---
Q3. When was the install date?

운영체제를 설치한 날짜 2번 문제와 동일한 경로에서 InstallDate를 확인하면 된다.

![image](https://github.com/user-attachments/assets/19358d71-e660-4dc8-9831-b10132ff1235)

해당 값 1092955707 아래 사이트를 통해 디코딩 할 수 있다.

https://www.epochconverter.com/

![image](https://github.com/user-attachments/assets/be1b8f94-f5f4-45ed-ad1f-251208c6f952)

A. 08/19/04 10:48:27 PM 

4번 문제에서 timezone이 UTC-5 라서 답은 08/19/04 05:48:27 PM 이 정확하다.

---
Q4. What is the timezone settings?

컴퓨터의 timezone 설정을 묻고 있다.
timezone은 SYSTEM 레지스트리 키 파일에서 확인이 가능하다.

HKLM\SYSTEM\CurrentControlSet\Control\TimeZoneInformation - ActiveTimeBias

![image](https://github.com/user-attachments/assets/a91efe85-de6a-419a-b281-26e3e8c7753a)

300은 5시간이므로 timeZone은 UTC-5이다.

A.UTC-5

UTC-5니까 3번 문제 답은 08/19/04 05:48:27 PM 이 정확하다.

---
Q5. Who is the registered owner?

registered owner를 묻고있다.
registered owner는 다음 경로에서 찾아 볼 수 있다.

HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion - RegisteredOwner

![image](https://github.com/user-attachments/assets/306a4a93-aa17-43b2-bf6c-d17b461e40b3)

A. Greg Schardt

---
Q6. What is the computer account name?

컴퓨터 이름을 찾는 문제다. 컴퓨터 이름은 다음 경로에서 찾을 수 있다.

HKLM\SYSTEM\ControlSet001\ComputerName\ComputerName - ComputerName

![image](https://github.com/user-attachments/assets/ee6775a3-2517-4d4b-8dc8-f4e4ee03750b)

A. N-1A9ODN6ZXK4LQ

---
Q7. What is the primary domain name?

기본 도메인 이름을 찾는 문제다.
도메인 이름은 다음 경로에서 찾을 수 있다.

HKLM\SYSTEM\ControlSet001\Services\Tcpip\Parameters - Domain

![image](https://github.com/user-attachments/assets/8dc2678d-34d2-498a-a062-34baa87fe3d9)

도메인 이름은 설정되어 있지 않다.

A. None

---
Q8. When was the last recorded computer shutdown date/time?

컴퓨터 셧다운 시간과 날짜는 아래 경로에서 찾을 수 있다.

HKLM\SYSTEM\ControlSet001\Control\Windows - ShutdownTime

![image](https://github.com/user-attachments/assets/ac71aa2b-1fb6-4e76-b933-972767699c72)

나온 C4FC00074D8CC401 값을 DCode를 사용해서 디코딩 해보면 다음과 같이 나온다.

![image](https://github.com/user-attachments/assets/11b63e09-7804-4315-b8aa-6c1f3db08156)

A. 08/27/04 10:46:33 AM

---
Q9. How many accounts are recorded (total number)?

사용자 계정은 아래 경로에서 찾을 수 있다.

HKLM\SAM\SAM\Domains\Account\Users - User accounts

![image](https://github.com/user-attachments/assets/108abe2c-2635-4a0a-b143-961438170d3a)

총 5개의 계정이 있는걸 알 수 있다.

A. 5

---
Q10. What is the account name of the user who mostly uses the computer?

11번 문제와 동일한 경로에서 로그인 횟수를 보면 15번으로 Mr.Evil의 로그인 회수가 가장 많다.

![image](https://github.com/user-attachments/assets/7fcedffd-84aa-46d3-bb87-6711112ff94f)

A. Mr.Evil

---
Q11. Who was the last user to logon to the computer?

10번 문제에서 확인 했듯이 다른 계정은 로그인한 흔적이 없기 때문에 마지막으로 로그인한 사람은 Mr.Evil이다.

A. Mr.Evil
