---
title: "MSA 인증 서비스 Keycloak 설치 및 설정"
date: 2020-07-08
categories: posts
tags: ["msa", "keycloak"]
---

### **Keycloak 서버 요구사항**
---
- Java 가 실행될 수 있는 OS
- Java 8 JDK 이상
- zip or gzip and tar 명령어 실행 가능
- 최소 512M 램 
- 최소 1G 이상의 디스크 공간
- 공유 외부 DB (MySQL, Oracle, PostgreSQL, ...) 을 실행할 수 있고 클러스터를 구성할 수 있는 환경

그 밖에 네트워크 및 리눅스의 추가 적인 요구사항은 [공식문서](https://www.keycloak.org/docs/latest/server_installation/#installation)를 참고하자.

<br>

### **keycloak 서버 디렉토리 구성**
---

- **bin/** : 서버를 부팅하거나 서버에서 다른 관리 작업을 수행하는 다양한 스크립트가 포함되어 있다.

- **domain/** : 다음에 설명한 모드 중 *도메인 모드* 에서 keycloak 을 실행할 때 구성 파일과 Working Directory 가 포함된다.

- **modules/** : 서버에서 사용하는 Java 라이브러리를 포함한 디렉토리. 나중에 설명하겠지만 외부 RDBMS 를 연결할때 필요한 jdbc driver 도 여기에 위치하게 된다.

- **standalone/** : *독립 실행 형 모드* 에서 keycloak 을 실행할 때 구성 파일과 Working Directory 가 포함된다.

- **standalone/deployments/** : keycloak 에 대한 extension 들을 추가하는 공간.

- **themes/** : 로그인, 회원가입 등의 UI 에 대한 파일 포함. UI 는 Freemarker 로 되어 있다.

참고 : [공식 문서 - 디렉토리 구조](https://www.keycloak.org/docs/latest/server_installation/#distribution-directory-structure)

<br>

## **Keycloak 실행 모드**
---
keycloak 은 기본적으로 WildFly Application Server 에서 구성되었고 아래 내용은 keycloak 의 배포에 대한 기본적인 내용만 설명한다. 자세한 내용은 [WildFly 18 Document](http://docs.wildfly.org/18/Admin_Guide.html) 를 참고하자.

- **Standalone Mode (독립 실행 형 모드)** : 하나의 keycloak 서버 인스턴스 만 실행하려는 경우에 유용하다. keycloak 에서 사용되는 모든 캐시는 클러스터로 구성되어 있지 않기 때문에 분배되지 않으며 로컬 전용이다. 프로젝트를 운영에서 사용하게 될 경우 독립 실행 형 모드는 단일 인스턴스이기 때문에 오류 발생 시 모든 서비스가 인증 & 권한 부여 서비스를 사용할 수 없게 되어 치명적인 장애가 발생한다. 따라서 해당 모드는 테스트 시에 사용하도록 권유한다.   
설정 파일은 **standalone/configuration/standalone.xml** 이다.   
   
   **Standalone Mode 실행 방법**   
      
  ```sh
  $ bin/standalone.sh --server-config=standalone.xml # bin\standalone.bat
  ```

- **Standalone Clustered Mode (독립형 클러스터 모드)** : 독립 실행 형 모드를 클러스터로 구성하는 모드이다. 이 모드는 각각의 keycloak 인스턴스의 구성을 변경해줘야 한다는 점에서 시간이 많이 소요되며, 오류를 발생할 수 도 있다. 또한 반드시 공유 DB 연결을 구성하고 로드 밸런서를 구성해야 한다.   
설정 파일은 **standalone/configuration/standalone.ha.xml** 이다.    
   
   **Standalone Clustered Mode 실행 방법**   
      
  ```sh
  $ bin/standalone.sh --server-config=standalone-ha.xml # bin\standalone.bat
  ```
  
- **Domain Clustered Mode (도메인 클러스터 모드)** : 독립형 클러스터 모드의 단점을 해결할 수 있는 방법이다. 중앙에서 keycloak 인스턴스를 관리하는 모드이다. 초기 설정이 복잡하고 시간이 많이 소요될 수 있지만 추후 관리적인 측면에서는 앞서말한 독립형 클러스터 모드보다 낫다.   
도메인 클러스터 모드는 다음 내용들로 구성되어있다.
  - **Domain Controller** : 클러스터의 각 노드에 대한 일반 구성을 저장, 관리 및 게시하는 프로세스. 이 프로세스는 클러스터 노드가 구성을 얻는 중심점 역할을 한다.
  - **Host Controller** : 특정 물리 서버에서 여러개의 서버 인스턴스들을 관리한다. 도메인 컨트롤러는 여러 컴퓨터에 설치된 호스트 컨트롤러와 상호 작용하여 클러스터를 관리할 수 있다. 또한 하나의 서버에서 호스트, 도메인같이 여러 프로세스가 실행되는걸 방지하고 싶을 때는 도메인 컨트롤러로 호스트 컨트롤러 역할을 줄 수 있다.
  - **Domain Profile** : 클러스터에 속한 여러 노드들의 설정들이다. 도메인 컨트롤러에서 다른 노드들의 프로필을 정의할 수 있다.
  - **Server Group** : 말 그대로 서버 그룹이다. 하나의 서버 그룹에 도메인 프로파일을 할당 할 수 있으며, 그룹 내의 모든 서비스는 같은 도메인 프로파일을 사용한다.
   
  설정 파일은 **domain/configuration/domain.xml** 이다. 도메인 클러스터 모드의 자세한 내용은 다음을 [공식문서](https://www.keycloak.org/docs/latest/server_installation/#domain-configuration)에 나와있다.

- **Cross-Datacenter Replication Mode** : 내용도 많이 길고 해서 간단하게 소개 정도만 하겠다. 자세한 내용은 [공식문서](https://www.keycloak.org/docs/latest/server_installation/#crossdc-mode) 를 참고하자. 교차 데이터센터 복제 모드는 아직 완전히 지원되지 않는 모드이다. 위에 말한 3가지 모드는 하나의 공유 데이터베이스를 사용했지만 여기서는 여러개의 데이터베이스를 서로의 클러스터가 사용하는 모드이다.

참고 : [공식 문서 - 실행 모드](https://www.keycloak.org/docs/latest/server_installation/#_operating-mode)

<br>

## **Keycloak RDBMS 설정**
---
이제 keycloak 에서 h2 데이터베이스를 사용하는 것이 아니라 사용자의 DB 의 데이터를 사용하여 로그인하는 설정을 추가해보자.

1. jdbc driver 설치 (여기서는 MariaDB) : 다운로드 받은 driver 파일을 modules 디렉토리 아래 위치시킨다. modules 디렉토리는 keycloak 의 클래스 경로이며, Dependency 를 정의한 곳이다. jdbc driver 를 다음과 같이 경로를 생성하여 위치시키자.   
   
   *modules/system/layers/keycloak/org/{database name}/main/*   
      
      <div style="width: 100%; text-align: center;">
        <img src="https://subji.github.io/assets/images/keycloak11.png" style="width: 80%">
      </div>   

      위에서 말한 경로를 보면 **module.xml** 파일이 보이는 데 여기에 내가 추가한 driver 를 keycloak 의 모듈로 사용하겠다라고 설정해준다.   
      ```xml
      <?xml version="1.0" ?>
      <!-- name 에 org.{database name} 을 적어준다 -->
      <module xmlns="urn:jboss:module:1.3" name="org.mariadb">

          <resources>
              <!-- 실제 driver jar 파일의 이름 -->
              <resource-root path="mariadb-java-client-2.6.1.jar"/>
          </resources>

          <dependencies>
              <module name="javax.api"/>
              <module name="javax.transaction.api"/>
          </dependencies>

      </module>
      ```  

2. Driver 선언 및 로드 : 이제 추가한 jdbc driver 모듈을 서버에 로드하기 위한 설정을 추가하자.   
**standalone.xml / standalone-ha.xml / domain.xml** 설정 파일   
   ```xml
    <subsystem xmlns="urn:jboss:domain:datasources:5.0">
      <datasources>
       <drivers>
          <driver name="h2" module="com.h2database.h2">
              <xa-datasource-class>org.h2.jdbcx.JdbcDataSource</xa-datasource-class>
          </driver>
          <!-- 여기서 추가한 jdbc driver 작성 -->
          <!-- driver 이름을 작성, module 은 1번에서 name 에 설정한 이름 -->
          <driver name="mariadb" module="org.mariadb">
              <xa-datasource-class>org.mariadb.jdbc.MariaDbDataSource</xa-datasource-class>
          </driver>
       </drivers>
     </datasources>
    </subsystem>
   ```
  
3. keycloak 의 데이터 소스를 변경 : 기본 H2 를 사용하는 데이터 소스를 mariadb 를 사용하는 데이터 소스로 변경하자. 같은 파일에서 다음 부분을 수정하면된다. 여기서 주의할 점이 하나 있는데 MySQL, MariaDB 의 경우 기본적으로 utf8mb4 로 되어 있는데, 반드시 utf8 로 변경 후 다음을 작성하자. [how to change utf8mb4 to utf8](https://www.dangtrinh.com/2018/03/how-to-fix-index-column-size-too-large.html)   
   
   ```xml
    <subsystem xmlns="urn:jboss:domain:datasources:5.0">
      <datasources>
        <datasource jndi-name="java:jboss/datasources/KeycloakDS" pool-name="KeycloakDS" enabled="true" use-java-context="true">
            <!-- 사용자 DB 의 주소 (인코딩 UTF8 로 변경) -->
            <connection-url>jdbc:mariadb://localhost/keycloak?characterEncoding=UTF-8</connection-url>
            <!-- 데이터 소스 설정의 이름 -->
            <driver>mariadb</driver>
            <pool>
                <max-pool-size>20</max-pool-size>
            </pool>
            <security>
                <user-name>{username}</user-name>
                <password>{password</password>
            </security>
        </datasource>
      </datasources>
    </subsystem>
   ```

4. 서버 데이터베이스 구성 : 이제 keycloak 서버의 데이터 베이스를 구성해보자. 역시 같은 파일에서 다음 부분을 수정하면 된다.   
   ```xml
    <subsystem xmlns="urn:jboss:domain:keycloak-server:1.1">
      <spi name="connectionsJpa">
      <provider name="default" enabled="true">
          <properties>
              <property name="dataSource" value="java:jboss/datasources/KeycloakDS"/>
              <property name="initializeEmpty" value="false"/>
              <property name="migrationStrategy" value="manual"/>
              <property name="migrationExport" value="${jboss.home.dir}/keycloak-database-update.sql"/>
          </properties>
      </provider>
      </spi>
    </subsystem>
   ```   
   - dataSource : 데이터 소스 이름, 3번 설정에서 jndi-name 부분이다.
   - driver : 초기 설정에서는 안보인다. hibernate 가 자동으로 감지하기 때문에 따로 설정할 필요가 없다.
   - initializeEmpty : value 가 false 일 경우 수동으로 초기화 한다. 
   - migrationStrategy : update, manual, validate 가 있다. update 는 자동으로 keycloak 관련 테이블을 생성. manual 의 경우 수동으로 생성해줘야 한다. validate 는 단순히 데이터베이스의 스키마가 최신인지만 검사한다.
   - migrationExport : 데이터베이스 초기화 / 마이그레이션 파일을 작성하는 경로
   - showSql : **Default false**, SQL 을 출력할지 여부
   - formatSql : **Default true** SQL 포맷화 여부


이렇게 해서 RDBMS 와의 연결이 끝났다. 서버를 실행 한 후 데이터베이스를 확인해보면 여러개의 테이블이 추가된 것을 확인할 수 있다.

<br>

## **마치며**
--- 
다음은 Keycloak 에서 제공하는 REST API 를 요청하고 받는 방법과 Custom User Attribute 를 사용하기 위한 User Storage SPI 를 활용해보는 걸 알아보자.