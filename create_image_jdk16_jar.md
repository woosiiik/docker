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

$ docker build -t woosiiik/jdk1.6.0_22_jar .

**Docker 컨테이너를 만들면서 실행 한다**

$ docker run -p 8080:8080 woosiiik/jdk1.6.0_22_jar


# JDK1.6 도커 이미지에 bouncy castle 라이브러리 추가 하는 방법
**JCE cannot authenticate the provider BC라는 에러가 발생 할 때**

참고 : https://kwebtoon.tistory.com/38

위 내용처럼 jre/lib/ext 에 bouncy castle 관련 라이브러리를 추가 하고, java.security 파일에 bouncy castle을 security provide로 등록한다.

docker 이미지를 실행 한다. 


실행중인 컨테이너에 파일 복사
```
docker cp bcprov-jdk15on-1.60.jar 061112c37b2c:/home/bcprov-jdk15on-1.60.jar
```
jar 파일을 JRE의 lib/ext 에 복사
```
cd /usr/java/jdk1.6.0_22/jre/lib/ext
cp /home/bcprov-jdk15on-1.60.jar .
```
security 폴더로 이동
```
cd /usr/java/jdk1.6.0_22/jre/lib/security
```
편집
```
vi java.security
```
```
security.provider.9=org.bouncycastle.jce.provider.BouncyCastleProvider
```

docker 에서 빠져나오기 (컨테이너 종료하지 않고)
```
ctrl + P, Q
```
컨테이너로 새로운 docker image 만들기
```
docker commit 061112c37b2c woosiiik/jdk6_add_bc
```
