---
title: "Spring REST Docs 소개 및 예제"
date: 2021-01-06
categories: posts
tags: ["spring rest docs"]
---

# Spring Rest Docs

생성일: 2021년 1월 5일 오전 9:19

*해당 내용은 [공식 문서](https://spring.io/projects/spring-restdocs) 2.0.5.RELEASE 버전을 참고 및 토대로 작성하였습니다.*

# Overview

---

Spring REST Docs 는 RESTful 서비스의 문서화를 도와주는 도구이다. Spring REST Docs 는 문서 작성 도구로 기본적으로 [Asciidoctor](https://asciidoctor.org/) 를 사용하며, 이것을 사용해 HTML 을 생성한다. 필요한 경우 Markdown 을 사용하도록 변경할 수 있다. 

Spring REST Docs 는 Spring MVC 의 [테스트 프레임 워크](https://docs.spring.io/spring-framework/docs/5.0.x/spring-framework-reference/testing.html#spring-mvc-test-framework), Spring WebFlux WebTestClient 또는 [Rest Assured 3](http://rest-assured.io/) 로 작성된 테스트 코드에서 생성된 Snippet 을 사용한다. 테스트 기반 접근 방식은 서비스 문서의 정확성을 보장해준다. Snippet 이 올바르지 않을 경우 테스트가 실패하기 때문이다. 

RESTful 서비스를 문서화 하는 것은 해당 리소스를 설명하는 것이다. 리소스 설명의 핵심은 HTTP 요청과 HTTP 응답의 세부정보이다. Spring REST Docs 를 사용하면 리소스와 HTTP 요청 및 응답을 사용하여 서비스 내부의 세부 정보로부터 문서를 보호할 수 있다. 즉, 서비스 내부 코드에 추가 또는 수정과 같이 영향을 주지 않고도 별도의 문서화 작업을 진행할 수 있게 된다. 이는 서비스 구현보다 API 를 문서화 하는데 도움이 된다. 또한 문서를 재 작업 하지 않고도 버전에 따라 변경할 수 있다.

많이 사용되는 RESTful API 문서도구로는 대표적으로 Swagger 가 있다. 아래 표는 Swagger 와 Spring REST Docs 의 비교 내용이다.

[Spring REST Docs vs Swagger ](https://www.notion.so/432e78700d6f47c78e7b6802e6c9dbfb)

자세한 내용은 [여기](https://docs.spring.io/spring-restdocs/docs/2.0.5.RELEASE/reference/html5/#introduction)를 참고한다.

# 시작하기

---

## 샘플 애플리케이션

여러 샘플 응용 프로그램을 사용할 수 있다.

[MockMvc](https://www.notion.so/fc85370464aa42a1a179d4605d55f89f)

[WebTestClient](https://www.notion.so/6a7d664d354248f296a123785fe1758b)

[REST Assured](https://www.notion.so/42b31974e4fb476fa53029c2fad1723b)

[고급](https://www.notion.so/749b79487ccf40859ff5ddbe23354ecd)

## 요구 사항

- Java 8 >
- Spring Framework 5 (5.0.2) >

## 빌드 시스템 설정

- **Maven**

    ```xml
    <!-- 
    	"test" scope 를 가지는 spring-restdocs-mockmvc 의존성 추가 
    	또는 spring-restdocs-webtestclient, spring-restdocs-restassured 추가
    -->
    <dependency> 
    	<groupId>org.springframework.restdocs</groupId>
    	<artifactId>spring-restdocs-mockmvc</artifactId>
    	<version>{project-version}</version>
    	<scope>test</scope>
    </dependency>

    <build>
    	<plugins>
    		<!-- asciidoctor 플러그인 추가 -->
    		<plugin> 
    			<groupId>org.asciidoctor</groupId>
    			<artifactId>asciidoctor-maven-plugin</artifactId>
    			<version>1.5.8</version>
    			<executions>
    				<execution>
    					<id>generate-docs</id>
    					<!-- "prepare-package" 옵션은 패키지내에 API 문서를 포함할 수 있게 한다. -->
    					<phase>prepare-package</phase> 
    					<goals>
    						<goal>process-asciidoc</goal>
    					</goals>
    					<configuration>
    						<backend>html</backend>
    						<doctype>book</doctype>
    					</configuration>
    				</execution>
    			</executions>
    			<dependencies>
    				<!-- 
    					"asciidoctor" 의 의존성으로 spring-restdocs-asciidoctor 추가 
    					.adoc 파일이 target/generated-snippets 아래에 생성된 Snippet 을 가리키는 
    					설정이 추가된다. 
    					또한 operation 블록 매크로를 사용할 수 있다. 
    				-->
    				<dependency> 
    					<groupId>org.springframework.restdocs</groupId>
    					<artifactId>spring-restdocs-asciidoctor</artifactId>
    					<version>{project-version}</version>
    				</dependency>
    			</dependencies>
    		</plugin>
    	</plugins>
    </build>
    ```

- **Gradle**

    ```java
    plugins { 
    	// Asciidoctor 플러그인 적용
    	id "org.asciidoctor.convert" version "1.5.9.2"
    }

    dependencies {
    	/* 
    		asciidocker 에 대한 spring-restdocs-asciidocker 의존성 추가
    		Maven 처럼 build/generated-snippets 밑에 생성된 Snippet 을 .adoc 파일이 자동으로
    		가리키도록 하는 설정 추가.
    		operation 블록 매크로 사용 가능
    	*/
    	asciidoctor 'org.springframework.restdocs:spring-restdocs-asciidoctor:{project-version}' 
    	// Maven 과 같이 test Scope 에 대한 mockMvc 의존성을 추가 (WebClient, Assured 사용가능) 
    	testCompile 'org.springframework.restdocs:spring-restdocs-mockmvc:{project-version}' 
    }

    ext { 
    	// Snippet 의 생성 위치를 지정
    	snippetsDir = file('build/generated-snippets')
    }

    test { 
    	// Snippets 디렉토리를 출력으로 작업하도록 설정
    	outputs.dir snippetsDir
    }

    asciidoctor { 
    	// Snippets 디렉토리를 Input 디렉토리로 설정
    	inputs.dir snippetsDir 
    	// 문서 생성 전 테스트가 실행되도록 test 에 종속 설정
    	dependsOn test 
    }
    ```

### **문서 패키징**

생성된 API 문서를 jar 또는 war 에 패키징하기 위한 설정이다. 이는 Spring Boot 에 의해 정적 콘텐츠로 제공된다. 

1. jar 가 빌드되기 전에 문서가 생성.
2. 생성된 문서는 jar 에 포함.

- **Maven**

    ```xml
    <plugin> 
    	<!-- Asciidoctor 플러그인 선언 -->
    	<groupId>org.asciidoctor</groupId>
    	<artifactId>asciidoctor-maven-plugin</artifactId>
    </plugin>
    <plugin> 
    	<!--
    		문서가 패키지로 복사되기 전에 생성되야 하므로 위에 Asciidoctor 플러그인 뒤에 선언한다.
    	-->
    	<artifactId>maven-resources-plugin</artifactId>
    	<version>2.7</version>
    	<executions>
    		<execution>
    			<id>copy-resources</id>
    			<phase>prepare-package</phase>
    			<goals>
    				<goal>copy-resources</goal>
    			</goals>
    			<configuration> 
    			  <!-- 생성된 문서를 static/docs 에 복사 -->
    				<outputDirectory>
    					${project.build.outputDirectory}/static/docs
    				</outputDirectory>
    				<resources>
    					<resource>
    						<directory>
    							${project.build.directory}/generated-docs
    						</directory>
    					</resource>
    				</resources>
    			</configuration>
    		</execution>
    	</executions>
    </plugin>
    ```

- **Gradle**

    ```java
    bootJar {
    	// 빌드 전 문서 생성 확인
    	dependsOn asciidoctor
    	// 생성된 문서를 static/docs 에 복사 
    	from ("${asciidoctor.outputDir}/html5") { 
    		into 'static/docs'
    	}
    }
    ```

## 문서 조각 생성

Spring REST Docs 는 테스트 프레임워크, WebTestClient 등을 사용해서 문서화에 필요한 서비스에 요청을 한다. 그 다음 요청 및 결과 응답에 대한 문서 Snippet 을 생성한다. 

### 테스트 설정

테스트 프레임워크에 따라 다르다. 여기에서는 JUnit 5 에 대한 설정을 다룬다. 

- **JUnit 5 테스트 설정**
    - RestDocumentationExtension 을 테스트 클래스에 적용한다.

        ```java
        @ExtendWith(RestDocumentationExtension.class)
        public class JUnit5ExampleTests {}
        ```

        RestDocumentationExtension 은 Maven 의 경우 "target/generated-snippets" Gradle 의 경우 "build/generated-snippets" 를 자동으로 출력 디렉토리로 설정되어있다.

    - 일반적인 Spring Application 의 경우에는 SpringExtension 을 적용한다.

        ```java
        @ExtendWith({ RestDocumentationExtension.class, SpringExtension.class })
        public class JUnit5ExampleTests {}
        ```

JUnit 5.1 을 사용하는 경우 클래스의 필드로 등록해서 출력 디렉토리를 임의로 지정할 수 있다. 

```java
public class JUnit5ExampleTests {

	@RegisterExtension
	final RestDocumentationExtension restDocumentation = new RestDocumentationExtension ("custom");

}
```

다음은 MockMvc 를 사용한 설정 방법이다.

```java
import static org.springframework.restdocs.mockmvc.MockMvcRestDocumentation.document;
import static org.springframework.restdocs.mockmvc.MockMvcRestDocumentation.documentationConfiguration;
import static org.springframework.restdocs.operation.preprocess.Preprocessors.preprocessRequest;
import static org.springframework.restdocs.operation.preprocess.Preprocessors.preprocessResponse;
import static org.springframework.restdocs.operation.preprocess.Preprocessors.prettyPrint;

private MockMvc mockMvc;

@BeforeEach
public void setUp(WebApplicationContext webApplicationContext,
		RestDocumentationContextProvider restDocumentation) {
	this.mockMvc = MockMvcBuilders.webAppContextSetup(webApplicationContext)
	.apply(documentationConfiguration(restDocumentation)) 
	.alwaysDo(document("{method-name}", preprocessRequest(prettyPrint()), preprocessResponse(prettyPrint())))
	.build();
}
```

documentationConfiguration 은 기본값이 있지만 사용자 정의를 위한 다양한 설정도 제공한다. 내용은 [여기](https://docs.spring.io/spring-restdocs/docs/2.0.5.RELEASE/reference/html5/#configuration)를 참고한다.

추가로 alwaysDo() 메서드에서 문서화로 보여줄 때 이쁘게 보여주기 위한 옵션도 추가한다. 

**JUnit 없이 테스트 설정**

- JUnitRestDocumentation 대신 ManualRestDocumentation 을 사용
- @Rule 어노테이션 제거

```java
private MockMvc mockMvc;

@Autowired
private WebApplicationContext context;

private ManualRestDocumentation restDocumentation = new ManualRestDocumentation(); 

@BeforeMethod
public void setUp(Method method) {
	this.mockMvc = MockMvcBuilders.webAppContextSetup(this.context)
			.apply(documentationConfiguration(this.restDocumentation))
			.build();
	this.restDocumentation.beforeTest(getClass(), method.getName());
}
```

마지막으로 각 테스트 후에 ManualRestDocumentation.afterTest 를 호출해야 한다. 

```java
@AfterMethod
public void tearDown() {
	this.restDocumentation.afterTest();
}
```

### RESTful 서비스 호출

- **RESTful 호출 예제**

    ```java
    // 서비스의 루트("/") 로 요청하고 응답 타입은 application/json 으로 한다.
    this.mockMvc.perform(get("/").accept(MediaType.APPLICATION_JSON)) 
    	// 응답 확인
    	.andExpect(status().isOk()) 
    	// 서비스 호출을 문서화 하는데 Snippet 이름을 index 로 지정. 
    	// Snippet 은 "RestDocumentationResultHandler" 에 의해 작성되며, 클래스의 인스턴스는
    	// "org.springframework.restdocs.mockmvc.MockMvcRestDocumentation" 의 "document" 
      // 메서드를 참고한다.
    	.andDo(document("index"));
    ```

기본적으로 6개의 Snippet 이 작성된다. 그 외에 Snippet 에 대한 정보는 [여기](https://docs.spring.io/spring-restdocs/docs/2.0.5.RELEASE/reference/html5/#documenting-your-api)를 참고한다. 

- `<output-directory>/index/curl-request.adoc`
- `<output-directory>/index/http-request.adoc`
- `<output-directory>/index/http-response.adoc`
- `<output-directory>/index/httpie-request.adoc`
- `<output-directory>/index/request-body.adoc`
- `<output-directory>/index/response-body.adoc`

## Snippet 사용

테스트 후 생성된 Snippet 을 사용하기위해 .adoc 파일을 생성해야 한다. 파일명은 아무렇게해도 상관없다.  그리고 생성된 snippet 을 연결해주면 된다.  

```java
include::{snippets}/index/curl-request.adoc[]
```

만들어 놓은 .adoc 과 생성된 Snippet 의 연결이 끝났다면 maven install 또는 gradle build 로 .html 파일을 생성해준다. 

[제목 없음](https://www.notion.so/7e3f06bc455448a08f98514d91190299)

# Simple Example

---

코드는 [여기](https://github.com/subji/spring-rest-docs-example)를 참고 한다.

> **의존성 추가**

- spring-boot-starter-web 과 Spring REST Docs 를 위한 spring-restdocs-mockmvc 를 추가한다.

```xml
<dependency>
	<groupId>org.springframework.boot</groupId>
	<artifactId>spring-boot-starter-web</artifactId>
</dependency>
<dependency>
	<groupId>org.springframework.restdocs</groupId>
	<artifactId>spring-restdocs-mockmvc</artifactId>
	<scope>test</scope>
</dependency>
```

- Plugin 설정
    - Asciidoctor 플러그인을 설정
    - 생성 된 Snippet 과 연결된 .adoc 을 빌드 했을 때 만들어지는 HTML 파일의 위치 설정
    - HTML 파일을 복사하여 웹에서 확인할 수 있는 디렉토리로 이동
    - maven-resources-plugin 은 Snippet 이 생성된 이후에 동작(파일 복사) 되어야 하므로 asciidoctor-maven-plugin 뒤에 오도록한다.

```xml
<plugin>
	<groupId>org.asciidoctor</groupId>
	<artifactId>asciidoctor-maven-plugin</artifactId>
	<version>1.5.8</version>
	<executions>
		<execution>
			<id>generate-docs</id>
			<phase>prepare-package</phase>
			<goals>
				<goal>process-asciidoc</goal>
			</goals>
			<configuration>
				<backend>html</backend>
				<doctype>book</doctype>
				<attributes>
					<snippets>${snippetsDirectory}</snippets>
				</attributes>
				<sourceDirectory>${basedir}/src/main/docs/asciidocs</sourceDirectory>
				<outputDirectory>${project.build.directory}/generated-docs</outputDirectory>
			</configuration>
		</execution>
	</executions>
	<dependencies>
		<dependency>
			<groupId>org.springframework.restdocs</groupId>
			<artifactId>spring-restdocs-asciidoctor</artifactId>
			<version>${spring-restdocs.version}</version>
		</dependency>
	</dependencies>
</plugin>
<plugin>
	<artifactId>maven-resources-plugin</artifactId>
	<version>2.7</version>
	<executions>
		<execution>
			<id>copy-resources</id>
			<phase>prepare-package</phase>
			<goals>
				<goal>copy-resources</goal>
			</goals>
			<configuration>
				<outputDirectory>
					${basedir}/src/main/resources/static/html/docs
				</outputDirectory>
				<resources>
					<resource>
						<directory>
							${project.build.directory}/generated-docs
						</directory>
					</resource>
				</resources>
			</configuration>
		</execution>
	</executions>
</plugin>
```

> 예제 Controller, Service, DTO 추가

---

- **DTO**

    ```java
    @Data
    public class RestDocsTest {
      
      private String id;
      private String name;
      private String body;

    }
    ```

- **Service**

    ```java
    @Service
    public class RestDocsTestService {
      
      public ResponseEntity<RestDocsTest> readContent(RestDocsTest restDocsTest) {
        RestDocsTest result = new RestDocsTest();
        
        try {
          result.setId("ID");
          result.setName("Name");
          result.setBody("Body");

          return new ResponseEntity<>(result, HttpStatus.OK);
        } catch (Exception e) {
          return new ResponseEntity<>(result, HttpStatus.BAD_REQUEST);
        }
      }

    }
    ```

- **Rest API Controller**

    ```java
    @RestController
    @RequestMapping("/test")
    public class RestDocsRestController {

      private final RestDocsTestService restDocsTestService;

      public RestDocsRestController(RestDocsTestService restDocsTestService)  {
        this.restDocsTestService = restDocsTestService;
      }
      
      @GetMapping(value = { "" })
      public ResponseEntity<RestDocsTest> readRestDocsTest(@RequestBody(required = false) RestDocsTest restDocsTest)  {
        ResponseEntity<RestDocsTest> result = restDocsTestService.readContent(restDocsTest);
        return result;
      }

    }
    ```

> 포트 설정(필요 시) 및 View Resolver 확장자 설정

---

```yaml
server: 
  port: 8080

spring:
  mvc:
    view:
      suffix: .html
```

> 테스트 코드 작성

---

```java
import static org.springframework.restdocs.mockmvc.MockMvcRestDocumentation.document;
import static org.springframework.restdocs.mockmvc.MockMvcRestDocumentation.documentationConfiguration;
import static org.springframework.restdocs.mockmvc.RestDocumentationRequestBuilders.get;
import static org.springframework.restdocs.operation.preprocess.Preprocessors.preprocessRequest;
import static org.springframework.restdocs.operation.preprocess.Preprocessors.preprocessResponse;
import static org.springframework.restdocs.operation.preprocess.Preprocessors.prettyPrint;
import static org.springframework.test.web.servlet.result.MockMvcResultMatchers.status;

import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;
import org.junit.jupiter.api.extension.ExtendWith;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.restdocs.RestDocumentationContextProvider;
import org.springframework.restdocs.RestDocumentationExtension;
import org.springframework.restdocs.payload.PayloadDocumentation;
import org.springframework.test.context.junit.jupiter.SpringExtension;
import org.springframework.test.web.servlet.MockMvc;
import org.springframework.test.web.servlet.setup.MockMvcBuilders;
import org.springframework.web.context.WebApplicationContext;

@ExtendWith({ RestDocumentationExtension.class, SpringExtension.class })
@SpringBootTest(classes = ExampleSprintRestDocsApplication.class)
class ExampleSprintRestDocsApplicationTests {

	private MockMvc mockMvc;

	@Test
	void contextLoads() {
	}

	@BeforeEach
	public void setUp(
		WebApplicationContext webApplicationContext, 
		RestDocumentationContextProvider restDocumentationContextProvider)	{
		this.mockMvc = MockMvcBuilders.webAppContextSetup(webApplicationContext)
			.apply(documentationConfiguration(restDocumentationContextProvider))
			.alwaysDo(document("{method-name}", preprocessRequest(prettyPrint()), preprocessResponse(prettyPrint())))
			.build();
	}

	@Test
	public void readExample() throws Exception {
		this.mockMvc.perform(get("/test"))
		.andExpect(status().isOk())
		.andDo(
			document("test-example", 
				preprocessRequest(prettyPrint()), 
				preprocessResponse(prettyPrint()), 
				PayloadDocumentation.responseFields(
					PayloadDocumentation.fieldWithPath("id")
						.description("ID 필드 입니다."), 
					PayloadDocumentation.fieldWithPath("name")
						.description("이름 필드 입니다."), 
					PayloadDocumentation.fieldWithPath("body")
						.description("데이터 필드 입니다.")
		)));
			// links(
			// 	// linkWithRel 은 텍스트를 링크화 한다. 
			// 	linkWithRel("test").description("The Test resource")), 
			// 		//subsectionWithPath 를 사용해서 메서드에 대한 응답을 문서화 했다.
			// 		responseFields(subsectionWithPath("_links")
			// 			.description("Links to other resources")),
			// 		// 응답에 "Content-type" 헤더를 추가하기 위해 headerWithName 을 사용
			// 		responseHeaders(headerWithName("Content-Type")
			// 			.description("The Content-Type of the payload, e.g. `application/hal+json`")))
	}

}
```

> **빌드 및 Snippet 확인**

---

만들어진 Snippet 들을 연결해줄 사용자 정의 .adoc 파일을 "src/main/docs/aciidocs" 경로에 만들고 다음과 같이 작성한다. 

```markdown
= RESTful Notes API Guide
:doctype: book
:icons: font
:source-highlighter: highlightjs
:toc: left
:toclevels: 4
:sectlinks:

include::{snippets}/test-example/curl-request.adoc[]

include::{snippets}/test-example/http-request.adoc[]

include::{snippets}/test-example/http-response.adoc[]

include::{snippets}/test-example/httpie-request.adoc[]

include::{snippets}/test-example/request-body.adoc[]

include::{snippets}/test-example/response-body.adoc[]

include::{snippets}/test-example/response-fields.adoc[]
```

이제 Snippet 들을 만들기 위한 빌드를 실행한다.

```bash
mvn clean install
```

빌드 및 테스트가 완료되면 target/generated-snippets 밑에 기본 파일들이 생긴다. 해당 Snippet 들은 자동으로 .adoc 과 연결된다. 또한 static/html/docs 밑에 html 이 생성된 것을 볼 수 있다.

![Spring%20Rest%20Docs%20502eb1eb790b4ad79a87d52cfca7c491/Untitled.png](https://subji.github.io/assets/images/2021-01-06-springrestdocsexample_2.png)

> **HTML 웹 페이지 연결**

---

이제 만들어진 HTML 을 웹 페이지로 볼 수 있게 연결해보자.

```java
@Controller
@RequestMapping("/docs")
public class RestDocsController {
  
  @GetMapping
  public String docs()  {
    return "html/docs/rest-test-api-guide";
  }

}
```

> **웹 페이지 확인**

---

스프링 부트 애플리케이션을 실행하고 다음 주소로 접속하면  Spring REST Docs 로 만들어진 문서를 볼 수 있다.

```java
http://localhost:8080/docs
```

![Spring%20Rest%20Docs%20502eb1eb790b4ad79a87d52cfca7c491/Untitled%201.png](https://subji.github.io/assets/images/2021-01-06-springrestdocsexample_1.png)