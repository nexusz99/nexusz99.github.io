---
layout: post
title: "운영 환경 별, properties 분리하기"
date: 2017-02-19 14:08:00 +0900
categories: java
---

# maven profile 을 이용한 환경 분리 

어플리케이션을 개발하다보면 다양한 설정파일들을 관리하게 된다. 데이터베이스 접근 정보, 레디스 접근 정보 등..

이런 설정들은 개발, 테스트, 운영 단계별 서로 다른 설정을 가질 수 밖에 없다. 만약 이런 내용들이 코드에 정적으로 들어가 있다면 단계별로 코드의 내용을 수정해서 다시 컴파일을 해야하는 매우 번거로운 상황이 일어날 것이다.

만약 별 다른 기능을 사용하지 않는다면 아마 코드에는 다음과 같은 내용이 들어갈 것이다.

```java
if(env.equals("local")) {
    ......
}
else if (env.equals("dev")) {
    .....
}
```

maven 을 이용해서 개발하는 java 프로젝트일 경우, maven profile 을 이용하여 빌드 시, 외부에서 넣어주는 파라미터를 기준으로 서로 다른 설정파일을 참조하게 하여 환경 별로 다른 설정파일을 선택하도록 해줄 수 있다.

## 설정 파일

설정파일은 다양한 포멧을 가질 수 있다. 가장 많이 쓰이는 것이 yaml 와 properties 이다. maven 에서는 properties 파일을 이용해서 설정 파일을 관리하고 있다.

properties 설정 파일의 형태는 다음과 같이 ```key = value``` 형태를 가진다.

```properties
url = http://nhnent.com
```

그리고 설정 파일은 대부분 **src/main/resources** 폴더 아래에 위치한다.

### 환경 별 리소스 폴더 구분

위에서 설정 파일은 대부분은 src/main/resources 에 저장된다고 하였다. 그럼 환경별로 리소스 폴더를 다르게 두고 그것을 classpath 에 넣으면 환경 별로 설정파일을 다르게 가져갈 수 있다.

* 개발자 환경
  * src/main/resources-local
* 개발 서버 환경
  * src/main/resources-dev
* 운영 서버 환경
  * src/main/resources-real

와 같이 리소스 폴더를 구분해 줄 수 있다. 

## maven profile 설정
이제 maven profile 설정을 통해, 빌드 단계에서 원하는 리소스 폴더를 classpath 에 추가하는 작업을 진행할 것이다.

우선, pom.xml 에 아래와 같이 profile 을 추가한다.
```xml
<profiles>
    <profile>
      <id>dev</id>
      <properties>
        <env>dev</env>
      </properties>
    </profile>
    <profile>
      <id>local</id>
      <properties>
        <env>local</env>
      </properties>
    </profile>
    <profile>
      <id>real</id>
      <properties>
        <env>real</env>
      </properties>
    </profile>
</profiles>
```

이렇게 설정된 profile 을 maven 명령어에서 **P**옵션으로 같이 선택할 수 있다.

```
maven clean package -P dev
```

다음은 선택된 profile 에 맞는 리소스 폴더를 classpath 에 넣는 작업이다. 기본즉으로 **src/main/resources** 는 들어가고 부가적으로 환경 별 리소스 폴더를 넣으면 된다. 아래 내용을 pom.xml 의 **build** 부분에 추가한다.

```xml
<resources>
  <resource>
    <directory>src/main/resources</directory>
  </resource>
  <resource>
    <directory>src/main/resources-${env}</directory>
  </resource>
</resources>
```

자 여기까지 하면 빌드 시, 환경에 맞는 설정파일이 classpath 로 들어가게 된다.

## 코드에서 properties 값 가져오기

이제 코드에서 properties 에 설정된 값을 가져와야 한다.

```java
Properties prop = new Properties();
prop.load(OAuthPayco.class.getClassLoader().getResourceAsStream("payco.properties"));
clientId = prop.getProperty("clientId");
clientSecret = prop.getProperty("clientSecret");
```

위 코드와 같이 아주 간단하게 코드에서 properties 에 있는 값을 가져올 수 있다.

# 참고 자료
* https://www.lesstif.com/pages/viewpage.action?pageId=14090588
