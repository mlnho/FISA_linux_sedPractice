# FISA_linux_sedPractice

# 문제
1./etc/selinux/config 에 있는 SELINUX=[내용] 사항 어떤 내용이든 -> disabled 로 바꾸기!
	- sed -i 's/\(SELINUX=\)\(.*\)/\1disabled/' config
 - -i : 파일 수정을 허용
- \(SELINUX=\) : SELINUX **패턴**을 찾는다.
- `\(.*\)` : 패턴** 다음에 오는 모든 문자열을 의미
- `\1disabled` : SELINUX=" 그룹에 해당하는 내용을 유지한 채 "disabled"로 변경

2./etc/ssh/sshd_config 내에 #PermitRootLogin [내용] → PermitRootLogin Yes 로 바꾸기’
	- sed -i 's/#PermitRootLogin .*/PermitRootLogin Yes/' sshd_config

3.sed 명령어로 apt 저장소 변경
	- sed -i 's/kr.archive.ubuntu.com/mirror.kakao.com/g' /etc/apt/sources.list 
