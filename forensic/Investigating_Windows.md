<!DOCTYPE html>
<html>
<head>
        <link rel="stylesheet" type="text/css" href="sytle.css">
</head>
<body>
        <h1>[2025 SWING Forensic Study] Tryhackme - Investigating Windows Write-Up</h1>
</body>
<br>
<br>
</html>

Q. Whats the version and year of the windows machine?

Windows 버전이나, 하드웨어 정보 등은 Windows 설정 > 시스템 > 정보에서 찾을 수 있다.

![image](https://github.com/user-attachments/assets/1387c652-0f1b-49fa-990a-8dabf2a4c5be)

A. Windows Server 2016

---
Q. Which user logged in last?

Windows 이벤트는 로그에는 로그 출처, 날짜, 시간, 사용자 정보, 이벤트 ID 등이 담겨 있다. 
이벤트 로그는 크게 세가지가 있다.

Application : 응용 프로그램에서 발생하는 로그가 포함
Security : 로그인, 로그오프 등의 보안 이벤트와 관련된 로그가 포함
System : 웅영 체제 자체에서 생성되는 로그가 포함

이러한 이벤트 로그는 Event Viewer로 볼수 있고 Security 섹션으로 가서 필터를 사용해 이벤트 ID 4624를 보면 로그인 관련 로그만 볼 수 있다.

![image](https://github.com/user-attachments/assets/94e093c9-b34a-480b-8ef8-0af881853e74)

A. Administrator

---
Q. When did John log onto the system last?

net user 명령어를 사용하면 마지막으로 로그온한 사용자 정보를 확인 할 수 있다. 

![image](https://github.com/user-attachments/assets/1eddb415-40a1-414f-a30d-7e4989d5580f)

A. 03/02/2019 5:48:32 PM

---
Q. What IP does the system connect to when it first starts?

시스템이 처음 시작할 때 어떤 IP에 연결되는지 보려면 Registry Editor를 사용하면된다.
HKEY_LOCAL_MACHINE > SOFTWARE > Microsoft > Windows > CurrentVersion > Run 경로로 이동한다.
해당 레지스트리 키는 사용자가 로그인할 때마다 시행되는 프로그램을 제어할 수 있다. 

![image](https://github.com/user-attachments/assets/3d56156f-ea48-43ae-b217-eda3977d39b4)


A. 10.34.2.3

---
Q. What two accounts had administrative privileges (other than the Administrator user)?

아래 명령어를 PowerShell에서 사용하면 관리자 그룹에 어떤 사용자가 있는지 확인할 수 있다.
```
Get-LocalGroupMember -Group "Administrators"
```

![image](https://github.com/user-attachments/assets/2b7ee2c2-fb5c-4bb6-9603-161e06d80573)

A. Guest, Jenny

---
Q. Whats the name of the scheduled task that is malicous.

Windows의 Task Scheduler는 시스템에서 모든 작업을 자동으로 예약하고 실행할 수 있는 도구로 
대부분의 경우 악성코드는 해당 도구를 이용해서 시스템에 악의적인 행위를 한다.

시스템에서 활성 예약된 작업을 보려면 Task Scheduler > Task Scheduler Livrary 를 보면 된다.
출력된 목록을 클릭하면 타임스탬프, 작업 동작 등 자세한 정보를 볼 수 있다.

![image](https://github.com/user-attachments/assets/f5329dd0-b220-4ed5-950b-c0fc8d34b24e)

A. Clean file system

---
Q. What file was the task trying to run daily?

![image](https://github.com/user-attachments/assets/d4ab0213-16cc-45fa-90cd-008e32f287c9)

A. nc.ps1

---
Q. What port did this file listen locally for?

![image](https://github.com/user-attachments/assets/87978c47-ae83-44e4-a458-db96c3b70e3c)

A. 1348

---
Q. When did Jenny last logon?

net user 명령어를 사용하여 Jenny의 마지막 로그온 시간을 확인 할 수 있다. 

![image](https://github.com/user-attachments/assets/79d3e4c3-7f97-4f2d-b054-93df46f8981e)

A. Never

---
Q. At what date did the compromise take place?

침해가 일어난 날을 물어보는것이니 파일들의 마지막 수정날짜를 보면 된다.

![image](https://github.com/user-attachments/assets/aca6c526-f0ad-43bc-addf-bfe015260013)

A. 03/02/2019

----
Q. During the compromise, at what time did Windows first assign special privileges to a new logon?

새 로그온에 특별 권한이 할당하는 것은 이벤트 ID 4672를 사용한다. 해당 값을 필터리하면된다.

![image](https://github.com/user-attachments/assets/77663536-4ef4-4a92-98d5-d96d34a860bd)

A. 03/02/2019 4:04:49 PM

----
Q. What tool was used to get Windows passwords?

Task Scheduler를 보면 " GameOver " 라는 작업이 있다.
시스템의 TMP 디렉터리에 " mim.exe "라는 실행 파일이 있는데 이 파일은 5분 마다 실행되고 같은 디렉터리의 o.txt 파일 에 출력을 저장하고 있다.
해당 파일을 찾아보면 mimikatz 도구가 Windows 비밀번호를 캡처하고 있는 것을 알 수 있다.

![image](https://github.com/user-attachments/assets/7bc54678-c57b-4a82-9651-cd29c70c910e)

A. mimikatz

---
Q. What was the attackers external control and command servers IP?

C:\ > Windows > System32 > drivers > etc 경로에 hosts 파일이 있는 것을 확인 할 수 있다. 
해당 파일을 확인하면 google.com과 www.google.com 있는 것을 확인 할 수 있다. 
일반적으로 hosts 파일에 해당 주소가 있을 필요가 없기 때문에 매우 의심스럽다.

![image](https://github.com/user-attachments/assets/1eaa5245-5db7-467d-b4f3-1cf94155db3a)

A. 76.32.97.132

---
Q. What was the extension name of the shell uploaded via the servers website?

C:\ > inetpub > wwwroot 를 보면 세 개의 파일이 있는데 그중 두 개는 .jsp 파일이 셸 파일인것 같다.

![image](https://github.com/user-attachments/assets/3ad893f6-de37-4d91-9f33-3ceef4c4557b)

A. .jsp

---
Q. What was the last port the attacker opened?

Windows Firewall with Advanced Security에서 Inbound Rules는 유입되는 트래픽으로부터 네트워크를 보호한다.
맬웨어나 DDOS 관련 공격으로부터 시스템을 보호하는 데 항상 도움이 된다. 
또한 로컬 및 원격 서버의 포트 와 주소에 대한 세부 정보도 포함되어 있다.

![image](https://github.com/user-attachments/assets/08d7a155-5908-44f9-8c33-a0c1155f15df)

A. 1337

---
Q. Check for DNS poisoning, what site was targeted?

표적이된 공격 사이트는 hosts 파일에서 확인 할 수 있다.

![image](https://github.com/user-attachments/assets/bca0a38a-c3fa-40cd-af2e-8680ca7093ab)

A. google.com

---

![image](https://github.com/user-attachments/assets/0ec9df67-149a-478f-9fad-6f21e3f1309d)






