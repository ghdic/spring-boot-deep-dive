![2-2.springboot-1.png](/images/2-2.springboot-1.png)

2-1에서 만든 프로젝트 기준으로 Intellij로 키면 이렇게 나온다

순차적으로 알아보자

## src/main/java

자바 소스코드가 작성되는 곳이다

`StudyApplicaion.java` 파일을 열어보면 다음과 같다

```java
package com.marinelife.study;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class StudyApplication {

	public static void main(String[] args) {
		SpringApplication.run(StudyApplication.class, args);
	}

}
```

스프링부트 애플리케이션의 메인클래스로 애플리케이션 앱 실행의 진입점이다

`@SpringBootApplication`어노테이션이 붙어서 자동 설정과 빈 스캔을 시작한다

## src/main/resources/

설정파일과 정적 리소스를 관리하는 폴더이다

- application.properties
    - 기본 설정파일으로 .properties, .yaml 두가지 방식으로 작성가능
- static/
    - CSS, JS, 이미지 같은 정적 파일이 들어간다
    - 별다른 설정이 없는 경우 웹에서 바로 접근 가능
- templates/
    - Thymeleaf 같은 템플릿 엔진용 HTML 파일을 넣는 곳
    - 동적 페이지 렌더링에 사용
- src/test/java/
    - 테스트코드를 관리하는 디렉토리
- build.gradle
    
    ![2-2.springboot-2.png](/images/2-2.springboot-2.png)
    
    - 의존성 관리, 플러그인, 빌드 작업 정의 등을 한다
    - 우리가 프로젝트 생성때 추가해준 dependencies가 여기에 있는걸 볼수있다
    - plugins: 스프링부트 플러그인, 자바 플러그인을 추가한다.
        
        **1. org.springframework.boot**
        
        - **역할**: 스프링부트 전용 플러그인
        - **하는 일**:
            - 스프링부트 애플리케이션을 빌드하고 실행할 수 있게 해준다
            - bootJar 태스크로 실행 가능한 JAR 파일을 만든다
            - bootRun 태스크로 애플리케이션을 바로 실행할 수 있어
            - 내장 서버(Tomcat 등)를 포함한 패키징도 처리한다
        
        **2. io.spring.dependency-management**
        
        - **역할**: 스프링부트의 의존성 관리를 돕는 플러그인이다
        - **하는 일**:
            - 스프링부트가 제공하는 BOM(Bill of Materials)을 가져와서 의존성 버전을 자동으로 맞춰준다
            - 예: spring-boot-starter-web 추가하면 관련 라이브러리 버전을 수동으로 지정X
        
        **3. java**
        
        - **역할**: Gradle의 기본 Java 플러그인
        - **하는 일**:
            - .java 파일을 컴파일하고, 클래스 파일을 생성한다
            - 테스트 실행(test 태스크)도 지원함
            - 기본 빌드 작업(compile, jar 등)을 정의한다
        
        **실제 동작 예시**
        
        - ./gradlew build를 실행하면:
            - java 플러그인이 소스 코드를 컴파일하고
            - org.springframework.boot 플러그인이 JAR 파일을 만들고
            - io.spring.dependency-management 플러그인이 의존성 버전을 관리한다
        
        결과적으로 build/libs/에 실행 가능한 JAR 파일이 생김
        
    - dependencies: 필요한 라이브러리를 선언한다
        - implementation: 애플리케이션 실행에 필요한 의존성
        - testImplementation: 테스트코드 실행에 필요한 의존성 추가
        - api: implementation과 마찬가지로 컴파일과 런타임에 사용되지만 이 모듈에 의존하는 다른 모듈에서도 이 의존성을 볼 수 있음(멀티 모듈 프로젝트에서 공통 라이브러리를 공유해야 할 때 사용
        - compileOnly: 컴파일 할때만 필요한 의존성(주로 인터페이스나 어노테이션만 필요한 경우
        - runtimeOnly: 컴파일 필요없고 실행할때
        - testCompileOnly: 테스트 코드 컴파일에만 필요할때
        - testRuntimeOnly: 테스트 실행 시에만 필요할때
        - annotationProcessor: 어노테이션 프로세싱(어노테이션 기반 코드 생성도구)에 필요한 의존성 추가할때(ex) Lombok, MapStruct)

## settings.gradle

```groovy
rootProject.name = 'study'
```

Gradle 프로젝트의 설정 파일

멀티 모듈 프로젝트일때 모듈 이름을 정의하거나 프로젝트 이름을 설정한다