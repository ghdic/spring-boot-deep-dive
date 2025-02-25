# 2-3. build.gradle 의존성추가
## 의존성 관리란 무엇인가

프로젝트에서 사용하는 외부라이브러리(의존성)을 효율적으로 추가하고, 버전을 통합하며, 충돌을 방지하는 과정이다

스프링 같은 프레임워크를 쓰다보면 여러 라이브러리를 사용하게 되는데, 이걸 수동으로 관리하면 복잡하고 오류가 생기기 쉬워 Gradle이나 Maven 같은 빌드 도구가 의존성을 체계적으로 다룰 수 있게 도와준다

## **스프링부트에서 의존성 관리는 어떻게 되나**

![2.3.springboot-10.png](/images/2-3.springboot-10.png)

web starter하나만 추가했는데 이렇게 기본적으로 많은 패키지들이 딸려온다

스프링부트는 의존성 관리를 쉽게 하기 위해 몇 가지 도구를 제공한다

**1. 스프링부트 스타터(Starters)**

- spring-boot-starter-*로 시작하는 의존성은 관련 라이브러리를 묶어서 제공한다
- 예: spring-boot-starter-web은 Spring MVC, Jackson, Tomcat 등을 포함
- 장점: 개별 라이브러리 버전을 신경 쓰지 않아도 호환되는 조합이 자동으로 추가된다

**2. BOM (Bill of Materials)**

https://mvnrepository.com/artifact/org.springframework.boot/spring-boot-dependencies

여기서 직접 볼수있으며 `/.gradle/caches/~`에 `spring-boot-dependencies-버전.pom` 으로 저장되어 관리 된다

- 스프링부트는 BOM이라는 의존성 버전 목록을 제공한다
- spring-boot-dependencies라는 부모 POM에 모든 라이브러리의 버전이 정의돼 있어
- Gradle에서는 io.spring.dependency-management 플러그인이 이 BOM을 가져와서 적용한다
- 예시 (build.gradle):groovy
    
    ```groovy
    plugins {
        id 'io.spring.dependency-management' version '1.1.4'
    }
    dependencies {
        implementation 'org.springframework.boot:spring-boot-starter-web'
    }
    ```
    
    - 여기서 spring-boot-starter-web 안에 들어가는 Jackson, Spring Core 등의 버전은 BOM에서 자동으로 관리

**3. 버전 명시 생략**

- BOM 덕분에 의존성을 추가할 때 버전을 안 써도 된다
- 예: implementation `org.springframework.boot:spring-boot-starter-web`만 쓰면 스프링부트 버전에 맞는 최적의 버전이 적용
- 하지만 예기치 못한 변화가 있을수 있으니 버전을 정확히 명시해주는게 좋음

## 의존성 추가하기

이미 프로젝트를 생성했는데 다른거도 추가하고 싶다! 하면 어떻게 해야되는가

## Intellij Ultimate를 쓰고있다면…

build.gradle 파일에서 그냥 우클릭을 해주면 의존성을 추가해줄수 있다

![2-3.springboot-1.png](/images/2-3.springboot-1.png)

![2-3.springboot-2.png](/images/2-3.springboot-2.png)

![2-3.springboot-3.png](/images/2-3.springboot-3.png)

## Intellij Community를 쓰고 있다면…

![2-3.springboot-5.png](/images/2-3.springboot-5.png)

![2-3.springboot-6.png](/images/2-3.springboot-6.png)

예… 맥북에서 gradle일때 이런버그가 있다고..(Maven에선 괜찮다함) Artifact검색이 안된다네(극히 일부만됌)

https://youtrack.jetbrains.com/issue/IDEA-237566/Maven-Artifact-Search-doesnt-work-properly

5년이나 지난이슈인데 아직도 해결 안해준듯,,,

## MavenRepository 사이트에 가서 검색하고 직접 추가하기

https://mvnrepository.com/

![2-3.springboot-7.png](/images/2-3.springboot-7.png)

![2-3.springboot-8.png](/images/2-3.springboot-8.png)

이런식으로 나오는거 그냥 복사붙여넣기하면 된다. Intellij는 이런과정을 간소화해준거일뿐

![2-3.springboot-9.png](/images/2-3.springboot-9.png)

이제 의존성추가가 다 끝났으면 Gradle reload를 해주면 설정해준대로 의존성 관련 파일들을 다운받거나 정리한다