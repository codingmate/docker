# 1. 도커 시작

## 목표
1. 이미지를 컨테이너로 빌드하고 실행
2. Docker Hub를 사용하여 이미지 공유
3. 데이터베이스와 함께 여러 컨테이너를 사용하여 Docker 애플리케이션 배포
4. Docker Compose를 사용하여 애플리케이션 실행

## 컨테이너
- 호스트 시스템의 다른 모든 프로세스와 격리된 시스템의 샌드박스 프로그램
    1. 이미지의 실행 가능한 인스턴스
    2. DockerAPI 또는 CLI를 사용하여 컨테이너를 생성, 시작, 중지, 이동, 삭제 가능
    3. 로컬 머신, 가상 머신에서 실행하거나 클라우드에 배포 가능
    4. 모든 OS로 이식 가능
    5. 다른 컨테이너와 격리되고 자체 소프트웨어, 바이너리 및 구성 실행

## 컨테이너 이미지
- 컨테이너를 실행할 때 격리된 파일 시스템을 사용
- 컨테이너의 파일 시스템이 포함되어 있으므로, 애플리케이션을 실행하는 데 필요한 모든 것(모든 종속성, 구성, 스크립트, 바이너리 등)이 포함되어야 함
- 환경 변수, 실행할 기본 명령, 기타 메타데이터가 설정되어 있어야 함

<br><br><br><br>

# 2. 애플리케이션 컨테이너화

## docker build -t getting-started .
- docker build -t *{Image tag} ${Direcotry}*

    : 현재 위치의 'Dockerfile'을 읽어 새 컨테이너 **이미지** 빌드하는 명령어
    + 많은 것을 다운하는 이유는, 빌더에게 이미지에서 시작하고 싶다고 명시했기 때문
    + 이미지가 로컬에 존재하지 않았기 때문에 이미지를 다운로드 진행
    - Image tag : Image를 사람이 식별할 수 있게 태그 생성
    - Direcotry : 'Dockerfile'을 찾는 디렉터리. '.'으로 명시하면, 현재 위치한 디렉터리에서 찾는다.

## docker run -dp 3000:3000 getting-started
- docker run *{Options} {Image tag}*    
    : 이미지를 컨테이너에서 실행
- Options : 컨테이너 실행 시 옵션
    - -d : 백그라운드 모드에서 실행
    - -p host port:container port : 호스트의 포트와 컨테이너의 포트 매핑
- Image tag : build 시 tag로 build된 이미지를 instance(container)화

<br><br>

## docker ps
| CONTAINER ID | IMAGE | COMMAND | CREATED | STATUS | PORTS | NAMES |
|---|---|---|---|---|---|---|
|2c6937a95215|getting-started:latest|"docker-entrypoint.sh"|3 minutes ago|Up 3 minutes|0.0.0.0:3000->3000/tcp|gracious_vaughan|
- CONTAINER ID : 인스턴스 아이디
- IMAGE : 이미지 태그명
- COMMAND : 컨테이너가 실행될 때 실행되는 명령. "docker-entrypoint.sh" 스크립트가 실행되는 것으로 보임
- CREATED : 컨테이너가 생성된 시간.
- STATUS : 컨테이너의 현재 상태.
- PORTS : 컨테이너의 포트 매핑 정보. '0.0.0.0:3000 -> 3000/tcp'는, 호스트의 3000 포트와 컨테이너의 3000 포트가 연결되어 있음을 나타냄
- NAMES : 컨테이너에 할당된 이름. 컨테이너를 식별하기 위해 사용됨. 시스템이 알아서 정해준 것으로 보임

<br><br><br><br>

# 3. 애플리케이션 업데이트

## 소스 코드 업데이트

### src/static/js/app.js 수정
```javascript
AS-IS
    <p className="text-center">No items yet! Add one above!</p>
TO-BE
    <p className="text-center">You have no todo items yet! Add one above!</p>
```

### docker build -t getting-started .

### docker run -dp 3000:3000 getting-started
- 에러 발생
```
docker: Error response from daemon: driver failed programming external connectivity on endpoint goofy_brown (b44ba697d149e102ff16d0fc331834c65ef7eef6073b4642e8e9f1f76c55864f): Bind for 0.0.0.0:3000 failed: port is already allocated.
```
- 이전 컨테이너가 계속 실행되는 동안 새 컨테이너를 시작할 수 없기 때문에 발생
- 이전 컨테이너가 포트 3000을 사용하고 있고, 머신의 한 프로세스(컨테이너 포함)만 특정 포트를 수신 대기할 수 있기 때문
- -> 이전 컨테이너를 제거해야 함

<br><br>

## 기존 컨테이너 제거

### docker ps
```
CONTAINER ID   IMAGE          COMMAND                  CREATED          STATUS          PORTS                    NAMES
2c6937a95215   4501ce165c8b   "docker-entrypoint.s…"   37 minutes ago   Up 37 minutes   0.0.0.0:3000->3000/tcp   gracious_vaughan
```

### docker stop 2c6937a95215
```
2c6937a95215
```
- 컨테이너 중지

### docker rm 2c6937a95215
```
2c6937a95215
```
- 컨테이너 ID로 컨테이너 삭제

<br><br>

## 업데이트된 앱 컨테이너 시작
### docker run -dp 3000:3000 getting-started

<br><br><br><br>

# 4. 애플리케이션 공유
## 저장소 만들기

### 이미지를 푸시하려면 Docker Hub에 Repository를 생성해야 함
1. Docker Hub에 가입 & 로그인
2. Repository 생성
3. Repository name 'getting-started', VIsibility 'Public' 확인
4. 생성 버튼 클릭
5. Repository 주소 확인 : 'docker push *{UserName}/getting-started:tagname'

<br><br>

## 이미지 푸시

### docker push docker/getting-started
```
$ getting-started\app>docker push docker/getting-started
Using default tag: latest
The push refers to repository [docker.io/docker/getting-started]
An image does not exist locally with the tag: docker/getting-started
```
=> 로그인이 되지 않아 생김

### docker login -u *{UserName}*
- 도커 허브에 로그인
```
Password: 
Login Succeeded

Logging in with your password grants your terminal complete access to your account.
For better security, log in with a limited-privilege personal access token. Learn more at https://docs.docker.com/go/access-tokens/
```

### docker tag getting-started *{UserName}*/getting-started
- 현재 로컬의 getting-started 이미지를 getting-started repository에 commit

### docker push hsykys0728/getting-started
- 원격저장소 DockerHub에 commit 내용 Push.

## 새 인스턴스에서 이미지 실행