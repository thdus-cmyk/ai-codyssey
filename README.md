# 1. 프로젝트 개요
본 프로젝트는 개발 워크스테이션 구축 미션의 일환으로, 리눅스 터미널 CLI 기반의 기본 조작, 파일 및 디렉토리 권한 변경, 그리고 Docker 환경 구축 및 관리를 실습하고 검증한 기술 문서입니다.

---

# 2. 실행 환경
- OS: Windows 11 (PowerShell)
- Shell: PowerShell
- Editor: Visual Studio Code
- Git: Installed
- Docker: 29.9.2 (Installed)

---

# 3. 수행 항목 체크리스트
- [x] 터미널 기본 조작 및 폴더 구성
- [x] 권한 변경 실습 (파일 및 디렉토리)
- [x] Docker 설치/점검
- [x] hello-world 실행
- [x] Dockerfile 빌드/실행
- [x] 포트 매핑 접속 (2회)
- [x] 바인드 마운트 반영
- [x] 볼륨 영속성 검증
- [x] Git 설정 + VSCode GitHub 연동

---

# 4. 터미널 조작 및 권한 실습 로그

### 4.1. 위치 확인 및 폴더 생성
현재 위치를 확인(`pwd`)하고, 작업용 디렉토리 `practice`를 생성(`mkdir`) 후 이동(`cd`)했습니다.

```powershell
PS C:\Users\User> pwd

Path
----
C:\Users\User

PS C:\Users\User> mkdir practice
PS C:\Users\User> cd practice
PS C:\Users\User\practice>

PS C:\Users\User\practice> New-Item test.txt -ItemType File

    디렉터리: C:\Users\User\practice

Mode                LastWriteTime         Length Name
----                -------------         ------ ----
-a----       2026-10-30  오후 4:31              0 test.txt

PS C:\Users\User\practice> ls

    디렉터리: C:\Users\User\practice

Mode                LastWriteTime         Length Name
----                -------------         ------ ----
-a----       2026-10-30  오후 4:31              0 test.txt

PS C:\Users\User\practice> icacls test.txt
test.txt NT AUTHORITY\SYSTEM:(I)(F)
         BUILTIN\Administrators:(I)(F)
         4F-73\User:(I)(F)

1 파일을 처리했으며 0 파일은 처리하지 못했습니다.

PS C:\Users\User\practice> icacls test.txt /deny "User:(W)"
처리된 파일: test.txt
1 파일을 처리했으며 0 파일은 처리하지 못했습니다.

PS C:\Users\User\practice> icacls test.txt
test.txt 4F-73\User:(DENY)(W)
         NT AUTHORITY\SYSTEM:(I)(F)
         BUILTIN\Administrators:(I)(F)
         4F-73\User:(I)(F)

1 파일을 처리했으며 0 파일은 처리하지 못했습니다.
```

# 5. Docker 설치 및 기본 점검 로그
Docker 버전과 데몬 동작 상태를 확인(`docker --version`, `docker info`)하여 정상 구동을 검증했습니다[cite: 1, 4, 8].

```powershell
PS C:\Users\User\practice> docker --version
Docker version 29.9.2, build a82e528

PS C:\Users\User\practice> docker info
Client:
 Version:     29.9.2
 Context:     desktop-linux
 ...
Server:
 Operating System: Docker Desktop
 Containers: 0
 Running: 0
 Images: 0
 ```

 # 6. Docker hello-world 실행 검증
`docker run hello-world` 명령어를 실행하여 도커 이미지 다운로드 및 컨테이너 구동이 정상적으로 수행되는지 검증했습니다[cite: 1, 8].

```powershell
PS C:\Users\User\practice> docker run hello-world
Unable to find image 'hello-world:latest' locally
latest: Pulling from library/hello-world
4f55086f7dd0: Pull complete 
d5e71e642bf5: Download complete 
Digest: sha256:c567b231f65614688711a75f31518a064258a51d442117a3e771a3a8fdba78ef
Status: Downloaded newer image for hello-world:latest

Hello from Docker!
This message shows that your installation appears to be working correctly.

To generate this message, Docker took the following steps:
 1. The Docker client contacted the Docker daemon.
 2. The Docker daemon pulled the "hello-world" image from the Docker Hub.
    (amd64)
 3. The Docker daemon created a new container from that image which runs the
    executable that produces the output you are currently reading.
 4. The Docker daemon streamed that output to the Docker client, which sent it
    to your terminal.

To try something more ambitious, you can run an Ubuntu container with:
 $ docker run -it ubuntu bash

Share images, automate workflows, and more with a free Docker ID:
 [https://hub.docker.com/](https://hub.docker.com/)

For more examples and ideas, visit:
 [https://docs.docker.com/get-started/](https://docs.docker.com/get-started/)
```

 # 7. Ubuntu 컨테이너 진입 및 종료 실습
`docker run -it ubuntu bash` 명령어로 우분투 컨테이너 내부 셸에 진입하여 내부 파일 시스템(`ls`)을 확인하고, `exit` 명령어로 정상적으로 빠져나오는 과정을 검증했습니다.

```powershell
PS C:\Users\User> docker run -it ubuntu bash
root@71f925e8307a:/# ls
bin boot dev etc home lib lib64 media mnt opt proc root run sbin srv sys tmp usr var
root@71f925e8307a:/# exit
exit
PS C:\Users\User>
```

# 8. Dockerfile 작성 및 이미지 빌드
`nginx:alpine` 기반의 커스텀 웹 서버 이미지(`my-web-server`)를 생성하기 위해 `Dockerfile`을 작성하고 빌드(`docker build`)를 수행했습니다.

```powershell
PS C:\Users\User\practice> Set-Content -Path "Dockerfile" -Value "FROM nginx:alpine`nCOPY site /usr/share/nginx/html" -Encoding UTF8
PS C:\Users\User\practice> docker build -t my-web-server .
[+] Building 6.6s (8/8) FINISHED
...
 => naming to docker.io/library/my-web-server:latest
 PS C:\Users\User\practice> docker run -d -p 8080:80 --name my-running-server my-web-server
PS C:\Users\User\practice> docker run -d -p 9090:80 --name my-running-server-2 my-web-server
![웹페이지 접속 화면](./screenshot_8080.png)
![웹페이지 접속 화면](./screenshot_9090.png)
```

# 10. 바인드 마운트(Bind Mount) 실습
로컬 디렉터리(`site`)를 컨테이너 내부 웹서버 경로와 마운트(`-v`)하여, 이미지 재빌드 없이 소스 코드 수정 사항이 실시간으로 웹에 반영되는 것을 검증했습니다.

```powershell
PS C:\Users\User\practice> docker run -d -p 8080:80 --name bind-server -v "${PWD}/site:/usr/share/nginx/html" nginx:alpine
PS C:\Users\User\practice> Set-Content -Path "site/index.html" -Value "Hello Bind Mount!" -Encoding UTF8
```

# 11. Docker 볼륨 영속성(Persistence) 검증
Docker 볼륨(`my-volume`)을 생성하고, 컨테이너가 삭제된 이후에도 볼륨에 저장된 데이터가 안전하게 유지되어 새로운 컨테이너에서 정상적으로 조회되는지 검증했습니다.

```powershell
PS C:\Users\User\practice> docker volume create my-volume
PS C:\Users\User\practice> docker run -it --name volume-test-container -v my-volume:/data ubuntu bash
root@9f8050359d4a:/# echo "Hello Docker Volume Persistence!" > /data/persistent.txt
root@9f8050359d4a:/# cat /data/persistent.txt
Hello Docker Volume Persistence!
root@9f8050359d4a:/# exit
PS C:\Users\User\practice> docker rm volume-test-container
PS C:\Users\User\practice> docker run --name volume-verify-container -v my-volume:/data ubuntu cat /data/persistent.txt
Hello Docker Volume Persistence!

