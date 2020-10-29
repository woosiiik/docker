# JDK 1.6에 Springboot Application을 실행

**JDK1.6을 지원하는 docker image를 찾는다**

$ docker search jdk1.6

```
NAME                               DESCRIPTION                                     STARS               OFFICIAL            AUTOMATED
kimicheng/jdk1.6.0_22              oracle java 1.6.0_22 for docker                 0
podgorskimike/jdk1.6.24-npm5.6.0                                                   0
litongjava/jdk1.6                                                                  0
chihcai/jdk1.6                                                                     0
fengyuanyang/jdk6-mvn3             jdk1.6.45.06 tomcat7                            0                                       [OK]
renewe0815/centos                  Did a "yum update", installed glibc.i686, in…   0
```

**Image download**

$ docker pull kimicheng/jdk1.6.0_22

**Dockerfile이 있는 위치에 Springboot jar 파일을 위치시킨다**

**Dockerfile 작성**

$ vi Dockerfile
```
FROM kimicheng/jdk1.6.0_22
ADD myidIssuerVerifier-0.0.1-SNAPSHOT.jar app.jar
ENTRYPOINT ["java", "-jar", "/app.jar"]
```

myidIssuerVerifier-0.0.1-SNAPSHOT.jar 라는 Springboot jar를 app.jar 이라는 이름으로 도커 이미지에 포함하고, 이 이미지가 실행 될 때 java -jar app.jar 이 실행 되도록 했다.


**새로운 도커 이미지를 만든다(woosiiik/jdk1.6.0_22_jar이라는 이름으로)**
docker build -t woosiiik/jdk1.6.0_22_jar .

**Docker 컨테이너를 만들면서 실행 한다**

$ docker run -p 8080:8080 woosiiik/jdk1.6.0_22_jar


# 다른 방법
아래와 같이 하는 방법도 가능할 것 같다.
1. ubuntu 이미지를 받는다.
2. ubuntu 이미지를 Container로 실행 한다.
3. Container에서 jdk1.6을 설치한다.
4. Container를 새로운 이미지로 만든다.
5. 이후에 위의 첫번째 방법을 새로운 이미지로 시도한다. 
