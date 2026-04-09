# 1주차 코디세이 개발 워크스테이션 구축 미션

## 1. 프로젝트 개요
터미널, Docker, Git을 직접 세팅하여 재현 가능한 개발 환경을 구축
누구나 같은 방식으로 실행, 배포, 디버깅할 수 있는 환경 구성을 목표

## 2. 실행 환경
- OS: macOS
- Shell: zsh
- Terminal: macOS Terminal
- Docker: 28.5.2 (OrbStack)
- Git: 2.53.0

## 3. 수행 항목 체크리스트
- [x] 터미널 기본 조작
- [x] 파일 권한 실습
- [x] Docker 설치 및 점검
- [x] hello-world 컨테이너 실행
- [x] ubuntu 컨테이너 실행
- [x] Dockerfile 작성 및 커스텀 이미지 빌드
- [x] 포트 매핑 접속 확인
- [x] 바인드 마운트 실습
- [x] Docker 볼륨 영속성 검증
- [x] Git 설정 및 GitHub 연동

## 4. 터미널 조작 로그

### 현재 위치 확인
```bash
pwd
/Users/goddns48918695/Desktop/dev-workstation
```

### 파일 목록 확인 (숨김 파일 포함)
```bash
ls -a
.    ..   .git   README.md
```

### 폴더 생성
```bash
mkdir app
```

### 빈 파일 생성
```bash
touch test.txt
```

### 파일 복사
```bash
cp test.txt test_copy.txt
```

### 파일 이름 변경
```bash
mv test_copy.txt test_rename.txt
```

### 파일 삭제
```bash
rm test_rename.txt
```

### 파일 내용 확인
```bash
cat README.md
# dev-workstation
```

### 폴더 이동
```bash
cd app
cd ..
```

## 5. 권한 실습

### 권한 확인
```bash
ls -l
-rw-r--r--  test.txt
drwxr-xr-x  app
```

### 파일 권한 변경 (644 → 755)
```bash
chmod 755 test.txt
```
변경 후:
```bash
-rwxr-xr-x  test.txt
```

### 폴더 권한 변경 (755 → 700)
```bash
chmod 700 app
```
변경 후:
```bash
drwx------  app
```

## 6. Docker 설치 및 점검

### 버전 확인
```bash
docker --version
Docker version 28.5.2, build ecc6942
```

### 데몬 동작 확인
```bash
docker info
Server Version: 28.5.2
Containers: 0
Images: 0
Operating System: OrbStack
```

## 7. Docker 기본 운영 명령

### 이미지 목록
```bash
docker images
REPOSITORY    TAG       IMAGE ID       CREATED         SIZE
my-web        1.0       8045a097dd96   최근            62.2MB
hello-world   latest    e2ac70e7319a   13 days ago     10.1kB
ubuntu        latest    f794f40ddfff   5 weeks ago     78.1MB
```

### 컨테이너 목록
```bash
docker ps -a
CONTAINER ID   IMAGE         STATUS
0a9c72f6924e   ubuntu        Exited
67f9ed14faeb   hello-world   Exited
```

### 로그 확인
```bash
docker logs keen_archimedes
```

### 리소스 확인
```bash
docker stats --no-stream
```

## 8. 컨테이너 실행 실습

### hello-world 실행
```bash
docker run hello-world
Hello from Docker!
```

### ubuntu 컨테이너 실행
```bash
docker run -it ubuntu bash
root@0a9c72f6924e:/# ls
bin  dev  home  lib  ...
root@0a9c72f6924e:/# echo "hello docker"
hello docker
root@0a9c72f6924e:/# exit
```

## 9. Dockerfile 기반 커스텀 이미지

### 베이스 이미지
nginx:alpine

### 커스텀 포인트
- index.html 교체: 사용자 정의 웹페이지 표시
- ENV 설정: 개발 환경 변수 주입
- LABEL 추가: 이미지 메타정보 기록

### Dockerfile
```dockerfile
FROM nginx:alpine
LABEL org.opencontainers.image.title="my-custom-nginx"
ENV APP_ENV=dev
COPY index.html /usr/share/nginx/html/index.html
```

### 빌드 명령
```bash
docker build -t my-web:1.0 .
```

### 실행 명령
```bash
docker run -d -p 8080:80 --name my-web-server my-web:1.0
```

## 10. 포트 매핑 접속 증거
- 브라우저에서 localhost:8080 접속 성공
- 브라우저에서 localhost:8081 접속 성공 (바인드 마운트)

![포트매핑 접속 화면](screenshots/port-mapping.png)

## 11. 바인드 마운트 실습

### 실행 명령
```bash
docker run -d -p 8081:80 --name my-web-mount \
  -v $(pwd):/usr/share/nginx/html my-web:1.0
```

### 변경 전
안녕하세요! Docker 웹서버입니다 🐳

### 변경 후
바인드 마운트 테스트 🎉
파일을 수정했더니 바로 반영됐어요!

## 12. Docker 볼륨 영속성 검증

### 볼륨 생성
```bash
docker volume create mydata
```

### 컨테이너 실행 및 데이터 저장
```bash
docker run -d --name vol-test -v mydata:/data ubuntu sleep 300
docker exec -it vol-test bash -c "echo 'hello volume' > /data/hello.txt && cat /data/hello.txt"
hello volume
```

### 컨테이너 삭제
```bash
docker rm -f vol-test
```

### 새 컨테이너에서 데이터 확인
```bash
docker run -d --name vol-test2 -v mydata:/data ubuntu sleep 300
docker exec -it vol-test2 bash -c "cat /data/hello.txt"
hello volume
```
→ 컨테이너 삭제 후에도 데이터 유지 확인 ✅

## 13. Git 설정 및 GitHub 연동

### Git 사용자 설정
```bash
git config --global user.name "Jeongsuk"
git config --global user.email "jeongsook@ewhain.net"
git config --global --list
user.name=Jeongsuk
user.email=jeongsook@ewhain.net
```

### GitHub 연동
- VSCode에서 jeongsook-del (GitHub) 계정 연동 완료
- Settings Sync is On

## 14. 트러블슈팅

### 트러블슈팅 1: mv 명령어 확장자 누락
- **문제:** `mv jeongsook jeongsook_copy.txt` 실행 시 오류
- **오류:** `mv: rename jeongsook to jeongsook_copy.txt: No such file or directory`
- **원인:** 파일 이름에 `.txt` 확장자를 빠뜨림
- **해결:** `mv jeongsook.txt jeongsook_copy.txt` 로 확장자 포함하여 재입력

### 트러블슈팅 2: ls -l 과 ls -1 혼동
- **문제:** `ls -1` (숫자 1) 입력 시 권한 정보가 보이지 않음
- **원인:** 숫자 `1` 과 소문자 `l` 을 혼동
- **해결:** `ls -l` (소문자 엘) 로 재입력하여 해결

### 트러블슈팅 3: OrbStack WARNING 메시지
- **문제:** docker 명령어 실행 시 WARNING 메시지 출력
- **오류:** `WARNING: DOCKER_INSECURE_NO_IPTABLES_RAW is set`
- **원인:** OrbStack이 Mac에서 네트워크 설정하는 방식 때문
- **해결:** 정상 작동에 영향 없음, 무시해도 됨

### 트러블슈팅 4: 컨테이너 이름 중복
- **문제:** `docker run --name vol-test` 실행 시 오류
- **오류:** `container name already in use`
- **원인:** 같은 이름의 컨테이너가 이미 존재함
- **해결:** 기존 컨테이너가 실행 중이었으므로 다음 단계 진행
