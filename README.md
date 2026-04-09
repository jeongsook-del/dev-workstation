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
- VSCode: 1.114.0

## 3. 프로젝트 디렉토리 구조
dev-workstation/
├── README.md            # 프로젝트 기술 문서
├── app/                 # 웹서버 소스코드 디렉토리
│   ├── Dockerfile       # 커스텀 이미지 빌드 설정
│   └── index.html       # 웹서버에서 제공할 HTML 파일
├── test.txt             # 터미널 실습용 파일
└── jeongsook_copy.txt   # 파일 복사 실습 결과

구성 기준:
- app/ 폴더에 웹서버 관련 파일을 모아서 역할별로 분리
- Dockerfile과 index.html을 같은 폴더에 두어 빌드 컨텍스트를 단순화

## 4. 수행 항목 체크리스트
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

## 5. 터미널 조작 로그

### 현재 위치 확인 (pwd)
pwd = Print Working Directory = 지금 내가 어디 있는지 보여줘
```bash
pwd
/Users/goddns48918695/Desktop/dev-workstation
```

### 파일 목록 확인 (ls -a)
ls -a = 숨김 파일까지 전부 보여줘
```bash
ls -a
.    ..   .git   README.md
```

### 폴더 생성 (mkdir)
mkdir = Make Directory = 새 폴더 만들기
```bash
mkdir app
```

### 빈 파일 생성 (touch)
touch = 빈 파일 만들기
```bash
touch test.txt
```

### 파일 복사 (cp)
cp = Copy = 파일 복사하기
```bash
cp test.txt test_copy.txt
```

### 파일 이름 변경 (mv)
mv = Move = 파일 이동 또는 이름 바꾸기
```bash
mv test_copy.txt test_rename.txt
```

### 파일 삭제 (rm)
rm = Remove = 파일 삭제 (휴지통 없이 바로 삭제!)
```bash
rm test_rename.txt
```

### 파일 내용 확인 (cat)
cat = 파일 안에 뭐가 써있는지 보여줘
```bash
cat README.md
# dev-workstation
```

### 폴더 이동 (cd)
cd = Change Directory = 폴더 이동
```bash
cd app      # app 폴더로 이동
cd ..       # 상위 폴더로 이동
```

## 6. 절대 경로 vs 상대 경로

### 절대 경로
맨 처음(루트)부터 전체 주소를 다 쓰는 것
어디서든 이 주소로 찾아갈 수 있다
/Users/goddns48918695/Desktop/dev-workstation

### 상대 경로
지금 내 위치 기준으로 쓰는 것
내가 어디 있느냐에 따라 달라진다
./app       # 지금 있는 곳에서 app 폴더로
..          # 바로 위 폴더로

### 선택 기준
| 상황 | 선택 | 이유 |
|------|------|------|
| 어디서든 접근해야 할 때 | 절대 경로 | 위치에 상관없이 항상 같은 경로 |
| 현재 폴더 기준으로 이동할 때 | 상대 경로 | 짧고 간결하게 표현 가능 |

## 7. 파일 권한 실습

### 권한 확인
```bash
ls -l
-rw-r--r--  test.txt
drwxr-xr-x  app
```

### 권한 숫자 표기 규칙
권한은 소유자 / 그룹 / others 3자리로 구성

| 권한 | 숫자 |
|------|------|
| r (읽기) | 4 |
| w (쓰기) | 2 |
| x (실행) | 1 |
| 없음 | 0 |

예시:
- 755 = 소유자(7=rwx) / 그룹(5=r-x) / others(5=r-x)
- 644 = 소유자(6=rw-) / 그룹(4=r--) / others(4=r--)
- 700 = 소유자(7=rwx) / 그룹(0=---) / others(0=---)

### 파일 권한 변경 (644 → 755)
```bash
chmod 755 test.txt
```
변경 전: -rw-r--r-- (644)
변경 후: -rwxr-xr-x (755)

### 폴더 권한 변경 (755 → 700)
```bash
chmod 700 app
```
변경 전: drwxr-xr-x (755)
변경 후: drwx------ (700)

## 8. Docker 설치 및 점검

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
CPUs: 6
Total Memory: 15.67GiB
```

## 9. Docker 기본 운영 명령

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
root@0a9c72f6924e:/# ls
bin   dev  home  lib64  mnt  proc  run   srv  tmp  var
root@0a9c72f6924e:/# echo "hello docker"
hello docker
```

### 리소스 확인
```bash
docker stats --no-stream
CONTAINER ID   NAME      CPU %     MEM USAGE / LIMIT   MEM %
```

## 10. 컨테이너 실행 실습

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

### attach vs exec 차이
| 방식 | 설명 |
|------|------|
| attach | 컨테이너 메인 프로세스에 연결, exit하면 컨테이너 종료 |
| exec | 새 프로세스로 접속, exit해도 컨테이너 유지 |

## 11. 이미지와 컨테이너의 차이

| 구분 | 이미지 | 컨테이너 |
|------|------|------|
| 빌드 | docker build 로 생성 | 이미지로부터 생성 |
| 실행 | 실행 안 됨 (설계도) | docker run 으로 실행 |
| 변경 | 변경 불가 (고정됨) | 실행 중 변경 가능 |
| 비유 | 붕어빵 틀 | 실제 붕어빵 |

- 이미지 하나로 컨테이너 여러 개 만들 수 있음
- 컨테이너를 삭제해도 이미지는 남아있음

## 12. Dockerfile 기반 커스텀 이미지

### 베이스 이미지
nginx:alpine

### Dockerfile
```dockerfile
FROM nginx:alpine
LABEL org.opencontainers.image.title="my-custom-nginx"
ENV APP_ENV=dev
COPY index.html /usr/share/nginx/html/index.html
```

### 커스텀 포인트
| 항목 | 목적 |
|------|------|
| FROM nginx:alpine | nginx 웹서버를 베이스로 사용 |
| LABEL | 이미지 메타정보 기록 |
| ENV APP_ENV=dev | 개발 환경 변수 주입 |
| COPY index.html | 사용자 정의 웹페이지 표시 |

### 빌드 명령
```bash
docker build -t my-web:1.0 .
```

### 실행 명령
```bash
docker run -d -p 8080:80 --name my-web-server my-web:1.0
```

## 13. 포트 매핑

### 포트 매핑이 필요한 이유
컨테이너는 독립된 공간이라 외부에서 직접 접근 불가능
포트 매핑으로 내 Mac과 컨테이너를 연결해야 함
브라우저 → 내 Mac (8080) → 컨테이너 (80)

- 컨테이너 내부 포트(80)로 직접 접속 불가
- -p 8080:80 으로 연결해야 브라우저에서 접속 가능
- 같은 이미지로 포트만 다르게 여러 컨테이너 실행 가능

### 포트 매핑 접속 증거
- localhost:8080 접속 성공 ✅
- localhost:8081 접속 성공 (바인드 마운트) ✅

### 호스트 포트 충돌 시 진단 순서

오류 메시지 확인
"port is already allocated"
충돌 포트 확인
lsof -i :8080
해결 방법
A. 다른 포트 사용: -p 8082:80
B. 기존 컨테이너 중지: docker stop 컨테이너이름


## 14. 바인드 마운트 실습

### 실행 명령
```bash
docker run -d -p 8081:80 --name my-web-mount \
  -v $(pwd):/usr/share/nginx/html my-web:1.0
```

### 변경 전
안녕하세요! Docker 웹서버입니다 🐳
Dockerfile로 만든 커스텀 웹서버예요!

### 변경 후
바인드 마운트 테스트 🎉
파일을 수정했더니 바로 반영됐어요!

바인드 마운트로 내 Mac 폴더와 컨테이너가 실시간 연결되어
파일 수정 시 컨테이너 재시작 없이 바로 반영됨

## 15. Docker 볼륨 영속성 검증

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

컨테이너 삭제 후에도 데이터 유지 확인 ✅

### 컨테이너 삭제 후 데이터 보존 방법
컨테이너를 삭제하면 내부 데이터도 함께 사라짐
해결 방법: Docker 볼륨 사용
- 볼륨은 컨테이너와 독립적으로 존재
- 컨테이너 삭제해도 볼륨은 유지됨
- 새 컨테이너에 같은 볼륨 연결하면 데이터 그대로

## 16. Git 설정 및 GitHub 연동

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
- Settings Sync is On ✅

## 17. 트러블슈팅

### 트러블슈팅 1: mv 명령어 확장자 누락
- **문제:** mv jeongsook jeongsook_copy.txt 실행 시 오류
- **오류:** mv: rename jeongsook to jeongsook_copy.txt: No such file or directory
- **원인 가설:** 파일 이름에 .txt 확장자를 빠뜨림
- **확인:** ls 로 파일 목록 확인 → jeongsook.txt 로 저장되어 있음
- **해결:** mv jeongsook.txt jeongsook_copy.txt 로 확장자 포함하여 재입력

### 트러블슈팅 2: ls -l 과 ls -1 혼동
- **문제:** ls -1 (숫자 1) 입력 시 권한 정보가 보이지 않음
- **원인 가설:** 숫자 1과 소문자 l을 혼동
- **확인:** ls -1 은 파일 목록만 세로로 출력, 권한 정보 없음
- **해결:** ls -l (소문자 엘) 로 재입력하여 해결

### 트러블슈팅 3: OrbStack WARNING 메시지
- **문제:** docker 명령어 실행 시 WARNING 메시지 출력
- **오류:** WARNING: DOCKER_INSECURE_NO_IPTABLES_RAW is set
- **원인 가설:** OrbStack이 Mac에서 네트워크 설정하는 방식 때문
- **확인:** docker 명령어는 정상 작동함
- **해결:** 정상 작동에 영향 없음, 무시해도 됨

### 트러블슈팅 4: 컨테이너 이름 중복
- **문제:** docker run --name vol-test 실행 시 오류
- **오류:** container name already in use
- **원인 가설:** 같은 이름의 컨테이너가 이미 존재함
- **확인:** docker ps -a 로 확인 → vol-test 이미 있음
- **해결:** 기존 컨테이너가 실행 중이었으므로 다음 단계 진행

### 트러블슈팅 5: git push 인증 실패
- **문제:** git push 시 Authentication failed 오류
- **오류:** remote: Invalid username or token
- **원인 가설:** GitHub가 보안 강화로 비밀번호 대신 토큰을 요구함
- **확인:** GitHub 문서 확인 → Personal Access Token 필요
- **해결:** GitHub에서 Personal Access Token 발급 후 비밀번호 대신 입력

### 가장 어려웠던 지점
git push 인증 실패가 가장 어려웠다.
- 가설: 비밀번호가 틀렸다
- 확인: 여러 번 시도해도 동일한 오류 발생
- 조치: GitHub Personal Access Token 발급하여 해결
- 배움: GitHub는 보안상 비밀번호 대신 토큰을 사용해야 함
