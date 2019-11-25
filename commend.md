
### Docker Image
##### 이미지 리스트
    $ docker images

##### 이미지 정보 확인 (inspect) 혹은 컨테이너 정보 확인
    $ docker image inspect iconloop/basic_idp:1.1

##### 도커 이미지 전체 (일괄)  삭제 (주의 : 모든 데이터 다 날라감)
    $ docker rmi -f $(docker images -q)    
    
### Docker Container
##### 컨테이너 리스트
    $ docker ps
    $ docker ps -a		// 실행 중단 된 컨테이너까지 모두 확인
    $ docker ps -q          // 컨테이너의 아이디만 출력 $(docker ps -a -q)로 자주 사용됨. 아이디를 출력해서 한꺼번에 삭제를 한다던지..

##### 컨테이너 만들기 (컨테이너 시작 하지 않고,  내부로 들어가지 않음) - 이름을 --name으로 지정
    $ docker pull centos:7
    $ docker create -i -t --name mycentos centos:7
    1a845d823af37684613e30313f46a5800e0b451e716d0c566e573118f4193632

##### 도커 컨테이너 전체 삭제
    $ docker rm -f $(docker ps -a -q)
    
    
##### 컨테이너 시작
    $ docker start mycentos
    mycentos

##### 시작된 컨테이너 내부로 들어가기
    $ docker attach mycentos
    [root@1a845d823af3 /]# 


##### 컨테이너를 만들고 실행 (shell 로 진입) : 일반적으로 많이 사용함.
    $ docker images | grep ubuntu
    ubuntu                                      latest              cf0f3ca922e0        2 days ago          64.2MB
    $ docker run -i -t ubuntu /bin/bash
    -i : interactive 
    -t : tts (shell 실행)
    -d : Detached 모드로 실행 (컨테이너를 백그라운드에서 동작하게 함)
     -i -t 로 실행 하거나 -d 로 백그라운드에서 실행 함.


##### 컨테이너 shell에서 빠져나오기
    $  exit 			// 컨테이너 종료하고 나오기
    Ctrl + P, Q 		// 컨테이너 종료하지 않고 나오기


##### 종료된 컨테이너 실행
    $ docker ps -a
    CONTAINER ID        IMAGE               COMMAND             CREATED              STATUS                      PORTS               NAMES
    0178e305347f        ubuntu              "/bin/bash"         About a minute ago   Exited (0) 44 seconds ago                       relaxed_lalande
    6309c55fc25a        ubuntu              "/bin/bash"         4 minutes ago        Exited (0) 2 minutes ago                        xenodochial_pare
    $ docker restart 6309c55fc25a

##### 컨테이너로 들어가기
    $ docker attach 6309c55fc25a

##### docker 컨테이너에서 git등을 설치 하고 새로운 이미지를 만든다.
##### 컨테이너에서 apt-get install -y git 등으로 설치를 하고 나서 아래와 같이 하면 새로운 도커 이미지가 만들어진다.

    $ docker ps -a
    CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS                     PORTS               NAMES
    0178e305347f        ubuntu              "/bin/bash"         6 minutes ago       Exited (0) 5 minutes ago                       relaxed_lalande
    6309c55fc25a        ubuntu              "/bin/bash"         9 minutes ago       Up 4 minutes                                   xenodochial_pare
    $ docker commit 6309c55fc25a ubuntu:git
    sha256:7a143d78a8c16fc2255d0997a3eab1ed50b09f8383b3a6079e8f20929f5c3391
    
    $ docker images | grep ubuntu
    ubuntu                                      git                 7a143d78a8c1        About a minute ago   185MB
    ubuntu                                      latest              cf0f3ca922e0        2 days ago           64.2MB



### Docker hub
##### PUSH 로 도커 이미지 올리기
Docker Hub에 이미지를 올릴 때는 이미지 이름을 [Docker Hub 사용자 계정]/[이미지 이름]:[태그] 형태로 생성해야 합니다.
    $ docker push woosiiik/uni-resolver-web


##### PULL로 도커 이미지 가져오기 (docker hub)
    $ docker pull ubuntu



### Docker hub를 통하지 않고 이미지를 이동
##### Docker 이미지 Export / Import
https://www.leafcats.com/240  
https://miiingo.tistory.com/182

##### 전체이미지 tar로 저장 (이렇게 하면 이미지 이름이 none로 표시된다)
    $ docker save -o XXXXX.tar $(docker images -q)
##### tar 로드
    $ docker load -i XXXXX.tar  
    $ docker save -o uni_icon.tar universalresolver/driver-did-icon universalresolver/driver-did-icon  
    $ docker save -o uni_web.tar universalresolver/uni-resolver-web universalresolver/uni-resolver-web  
    $ docker save -o uni_base.tar universalresolver/base-alpine universalresolver/base-alpine

### Docker Log
##### 로그 확인  
    $ docker logs [container name | container id]  
    $ docker logs [container name | container id] -t   // 시간 포함  
    $ docker logs [container name | container id] -f   // 실시간 로그 계속확인  


##### 도커가 로그를 남기는 위치 - 아래 위치에 json 형태로 로그를 남긴다.
    /var/lib/docker/containers/[container_id]/[container_id].log

##### 로그 확인 : Docker-compose
    $ docker-compose -f [yml file name] logs -t -f
##### 마지막 라인 2000줄만 읽는 것은 아래와 같음.
    $ docker-compose -f docker-compose.yml logs -t --tail 2000








### 고급

##### 컨테이너 외부에 어플리케이션을 노출하기 위한 포트 설정
    $ docker run -i -t --name mywebserver -p 80:80 ubuntu
    root@0ea5537192b3:/# 
    option -p : 컨테이너의 포트를 호스트의 포트와 바인딩 한다. [호스트의포트]:[컨테이너의포트]

##### 호스트의 특정 아이피 사용, 여러 포트를 설정
    $ docker run -i -t -p 3306:3306 -p 192.168.0.100:7777:80 ubuntu 
    -p 옵션을 여러개 사용
    호스트의 192.168.0.100 아이피의 7777포트를 컨테이너의 80으로 연결.

##### 컨테이너 실행 옵션 : 환경 변수
    $ docker run -i -t --name mysql_attach_test \
    -e MYSQL_ROOT_PASSWORD=password \
    -e MYSQL_DATABASE=wordpress \
    mysql:5.7

##### 이미 실행 중인 컨테이너의 내부 Shell을 사용하는 방법
##### 컨테이너는 하나의 모니터를 기본적으로 가지고 있는 형태이기 때문에 실행과 동시에 어떤 App을 실행 한다고 하면 (예를 들어 mysql) shell 같은 다른 app을 사용할 수 없다. 방법은 아래와 같다.
    $ docker exec -i -t wordpress /bin/bash
    (책 35 page)
 

######################### Dockerfile #########################

    $ docker build . -t xxxxx/xxxxx:latest
    -t : 새롭게 만들 이미지 이름을 지정.
    . : Dockerfile 위치
    
    FROM iconloop/basic_idp:1.0
    FROM : 위 이미지로 부터 새로운 이미지를 만든다.
    
    RUN mkdir xxxx
    RUN echo "hello"
    RUN : bash shell 입력과 동일.
    
    ADD test.txt
    ADD : test.txt 파일을 docker container에 추가
    ADD backend /home/ubuntu/backend
    ADD : backend 라는 폴더를 docker container에 추가 (/home/ubuntu/backend) 를 추가 한다. 
    
    CMD ["/bin/bash"]
    ENTRYPOINT ["/bin/bash]
    CMD : 실행 (단 컨테이너 실행 시 명령어를 입력하면 입력된 명령어가 CMD대신 실행 된다.)
    ENTRYPOINT : CMD와 동일하지만, 컨테이너 실행 시 입력된 명령어가 수행 되지 않는다. 



############### Docker-Componse ##################

    $ docker-compose xxxx.yml up
    $ docker-compose xxxx.yml up -d   # Detached Mode 로 실행
    $ docker-compose xxxx.yml down
    $ docker-compose xxxx.yml pull
    $ docker-compose xxxx.yml bulld



version: ‘3’ : compose 파일의 버전

service
image
port
volumes

