# FISA_linux_sedPractice

# 문제
1./etc/selinux/config 에 있는 SELINUX=[내용] 사항 어떤 내용이든 -> disabled 로 바꾸기!
	- sed -i 's/\(SELINUX=\)\(.*\)/\1disabled/' config
 - `-i` : 파일 수정을 허용
- `\(SELINUX=\)` : SELINUX **패턴**을 찾는다.
- `\(.*\)` : **패턴** 다음에 오는 모든 문자열을 의미
- `\1disabled` : SELINUX=" 그룹에 해당하는 내용을 유지한 채 "disabled"로 변경

2./etc/ssh/sshd_config 내에 #PermitRootLogin [내용] → PermitRootLogin Yes 로 바꾸기’
	- sed -i 's/#PermitRootLogin .*/PermitRootLogin Yes/' sshd_config
 - **#PermitRootLogin + 어떠한 내용(`.*`)** → **PermitRootLogin Yes** 로 바꾸기 처리하기

3.sed 명령어로 apt 저장소 변경
	- sed -i 's/kr.archive.ubuntu.com/mirror.kakao.com/g' /etc/apt/sources.list 
4.sed 명령어로 static IP 설정하기
<aside> :전구: 실제 현업에서는 ip를 dhcp를 이용해서 자동 할당하지 않음
왜?
dhcp는 약 8만초마다 새로 할당
서버를 통신해서 기능을 구동하려면 그 주소를 알아야 함. 설정해주는게 찾아서 사용 가능
dhcp는 네트워크 보안 장비하에 MAC주소 인증/할당 방식으로 사용되는 경우도 있음
⇒ 정적 (static)으로 정적IP 설정해보자 포트포워딩 설정해둔 것도 있고 괜한 충돌 나지 않게 우리는 10.0.2.15로 수정해볼 것
</aside>
ip a 를 통해 지금 어댑터 확인 가능
netplan 설정파일을 수정해야하는데 없다면, sudo netplan generate 로 생성 가능
/etc/netplan/00-installer-config.yaml 을 수정할 것
먼저 dhcp 가 true로 돼있기 때문에 이를 false로 바꿔 줄 것
sed로 내용 확인
$ sed -n '1,$p' 00-installer-config.yaml
b. default 값들 확인
# route의 default값 확인
$ ip route | grep default
=> 10.0.2.2
#DNS 서버 주소 확인
$ cat /etc/resolv.conf
=> 127.0.0.53
b. sed 명령어 활용해서 값 바꿔주기
address : 설정할 IP와 Netmask설정
gateway : 라우터 설정
nameservers 설정
sed -i 's/dhcp4: true/dhcp4: false\\n      addresses: [10.0.2.15\\/24]\\n      routes:\\n        - to: 0.0.0.0\\/0\\n          via: 10.0.2.2\\n          metric: 100\\n      nameservers:\\n        addresses: [127.0.0.53]/' 00-installer-config.yaml
설정 반영
sudo netplan apply
설정 반영 확인
ping 8.8.8.8 
