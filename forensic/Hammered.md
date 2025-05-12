<!DOCTYPE html>
<html>
<head>
        <link rel="stylesheet" type="text/css" href="sytle.css">
</head>
<body>
        <h1>[2025 SWING Forensic Study] Hammered Lab Write-Up</h1>
</body>
<br>
<br>
</html>

**Q1** Which service did the attackers use to gain access to the system?


공격자가 시스템에 접근하는 것이 auth.log에서 로그인 실패 기록을 살펴 보았다.

```
$ grep "Failed password" auth.log
```

![image](https://github.com/user-attachments/assets/feb5b485-2a84-4502-ba61-df401390eeff)

출력 결과를 보면 동일한 IP로 사용해서 여러번 로그인 시도를 한 것을 볼 수 있고 SSH를 사용한 것을 알 수 있다.

![image](https://github.com/user-attachments/assets/64f514e2-22c9-4dcb-954c-fc9a741436cd)

---

**Q2** What is the operating system version of the targeted system?

시스템 버전을 확인하는 거니 kern.log에서 리눅스 버전 문자열을 찾아보자

```
$ grep "Linux version" kern.log 
```

![image](https://github.com/user-attachments/assets/fdc8e86e-8182-4b95-a3b5-0558d5cc631d)

출력 결과를 보니 4.2.4-1ubuntu3인 것을 알 수 있다.

![image](https://github.com/user-attachments/assets/5633dd3e-b202-4c37-a23b-40ee3fae3669)

---

**Q3** What is the name of the compromised account?

침해당한 계정이 뭐냐고 물어봤으니 auth.log에서 로그인 성한한 기록을 살펴 보자

```
$ grep "Accepted password" auth.log
```

![image](https://github.com/user-attachments/assets/295e6e86-c1c1-4f22-8eb0-bd445f0f5d9e)

![image](https://github.com/user-attachments/assets/39fbeff6-3291-40b2-a668-6c542cb96629)

정답이 4글자니 root인것 같다.

![image](https://github.com/user-attachments/assets/0a27b891-56d4-4749-a3d0-53cc4407ee80)

---
**Q4** How many attackers, represented by unique IP addresses, were able to successfully access the system after initial failed attempts?

공격자가 몇 명인지 알려면 일단 root로 로그인 성공한 IP 주소를 가져오고 성공한 IP 주소에서 감지된 실패 시도 수를 출력해 보았다.

Q4.sh
```
#!/bin/bash

for ip in $(grep "Accepted password for root" auth.log | awk '{print $11}' | sort -u); do
  echo -n "$ip: "
  grep "authentication failure" auth.log | grep "$ip" | wc -l
done
```

![image](https://github.com/user-attachments/assets/2a87b727-1a95-4f8a-b53d-801c62c76eea)

비정상적으로 실패 횟수가 많은 IP가 6개다.

![image](https://github.com/user-attachments/assets/3a484986-eaba-4b07-b404-8c2f86c033ed)

---

**Q5** Which attacker's IP address successfully logged into the system the most number of times?

어떤 공격자 IP가 가장 많이 성공적으로 로그인 했는지 물어봤으니 Q4에서 찾은 IP들의 로그인 성공 횟수를 살펴보면 될 것 같다.


```
$ grep "Accepted password" auth.log | grep -E "121.11.66.70|122.226.202.12|219.150.161.20|222.169.224.197|222.66.204.246|61.168.227.12" | awk '{print $11}' | sort | uniq -c | sort -nr
```

![image](https://github.com/user-attachments/assets/e7206f22-d90f-431f-b4a0-d3cbbc0fe762)

가장 횟수가 많은건 219.150.161.20 이다.

![image](https://github.com/user-attachments/assets/e8782d35-84d2-4261-81ba-a381ee746627)

---

**Q6** How many requests were sent to the Apache Server?

Apache로 전송된 요청 횟수를 찾아야 하니 www-access.log 기록에서 찾아보았다.

```
cat apache2/www-access.log  | wc -l
```

![image](https://github.com/user-attachments/assets/9b70f380-a956-466c-a920-86f9569dce9a)

정답은 365다.

![image](https://github.com/user-attachments/assets/ac2203b8-c7fa-44e5-a503-fea31cba213d)


---

**Q7** How many rules have been added to the firewall?

방화벽에 추가된 규칙 개수를 찾아야 하니 리눅스에서 방화벽 규칙을 관리하는 명령 도구인 iptables 찾아보았다. 

![image](https://github.com/user-attachments/assets/7f4d3979-741d-4287-8ab0-67f80c66e099)

옵션 -A가 추가하는 옵션이니 총 6개다.

![image](https://github.com/user-attachments/assets/e10c2a17-7731-41d3-8ade-4b6baf204bfe)

---
**Q8** One of the downloaded files on the target system is a scanning tool. What is the name of the tool?

리눅스 시스템에서 설치나 업데이트 과정에서 configure 단계를 거치게 되니 해당 단어를 검색해서 스캐닝 도구를 찾아보았다.

![image](https://github.com/user-attachments/assets/dba4e64d-6e36-4d3e-8fb1-d8f272b8210e)

출력 결과를 살펴보니 nmap을 찾을 수 있었다.

![image](https://github.com/user-attachments/assets/9c23557b-2a71-41ec-858a-362a43feff92)

![image](https://github.com/user-attachments/assets/d2319106-4a14-4f32-a8e0-5bd889861a63)


---

**Q9** When was the last login from the attacker with IP 219.150.161.20? Format: MM/DD/YYYY HH:MM:SS AM


IP 219.150.161.20 공격자의 로그인을 출력해보자

```
grep "Accepted password" auth.log | grep "219.150.161.20"
```

![image](https://github.com/user-attachments/assets/c7e957a4-fd2d-41ff-b898-9a38ff043cea)

마지막 로그인은 04/19/2010 05:56:05 AM 이다.

![image](https://github.com/user-attachments/assets/0469ef1e-df0c-456f-bf78-8d301b1b6b55)

---

**Q10** The database showed two warning messages. Please provide the most critical and potentially dangerous one.

데이터 베이스에서 가장 중요하고 위험한 메시지를 알려달라고 daemon.log에서 했으니 mysql과 WARNING을 함께 검색해보았다.

```
grep "mysql" daemon.log | grep "WARNING"

```
![image](https://github.com/user-attachments/assets/01b1064d-3732-4183-ac8a-43cadb105e04)

"mysql.user contains 2 root accounts without password!" 인것 같다.

![image](https://github.com/user-attachments/assets/ac80a92c-8bb5-4553-ac99-d0593302be3d)

---
**Q11** Multiple accounts were created on the target system. Which account was created on April 26 at 04:43:15?

새로 생성된 계정을 찾아야 하니 auth.log에서 useradd를 검색해 보았다.

```
grep "useradd" auth.log
```

![image](https://github.com/user-attachments/assets/66bb698f-5652-44d1-8f27-5a87d0b3edf8)

4월 26일 04:43:15에 생성된 계정은 wind3str0y 이다.

![image](https://github.com/user-attachments/assets/3a02d321-ff8b-4781-aa87-cbfb712af659)

---
**Q12**
Few attackers were using a proxy to run their scans. What is the corresponding user-agent used by this proxy?

www-access.log 를 살펴보면 6번째 필드가 User-Agent 문자열이다. 

![image](https://github.com/user-attachments/assets/b24f68de-7969-405a-9d1b-bde66b7419f2)

중복을 제거하고6번째 필드만 출력해보자

```
awk -F'"' '{print $6}' apache2/www-access.log | sort | uniq
```

![image](https://github.com/user-attachments/assets/1f0c4c85-71af-4330-80be-3b1ee6048a4b)

pxyscand/2.1 인것 같다.

![image](https://github.com/user-attachments/assets/338abb6b-0821-4b55-bd2c-ddbf96c1d101)

---

![image](https://github.com/user-attachments/assets/ca969b6e-0f18-4dd8-a469-304db17bc6fe)

 CLEAR!!
