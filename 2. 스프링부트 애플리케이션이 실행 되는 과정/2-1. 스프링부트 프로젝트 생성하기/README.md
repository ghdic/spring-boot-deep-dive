# 

![2-1.springboot-1.png](/images/2-1.springboot-1.png)

https://start.spring.io/

IntelliJ Ultimate를 사용한다면 바로 생성할수 있지만 무료버전을 쓴다면 해당 사이트에서 생성하는게 일반적인 방법이다. 각 옵션에 대해 알아보자

## Project

프로젝트 빌드 도구를 선택하는 옵션이다

- Maven: XML기반의 설정 파일을 사용하는 빌드 도구로, 의존성 관리와 빌드 프로세스가 간단하고 널리 사용된다
- Gradle: Groovy나 Kotlin DSL을 활용해 더 유연하고 강력한 설정이 가능한 빌드도구이다

일반적으로 Java를 쓴다면 Gradle-Groovy, Kotlin을 쓴다면 Gradle-Kotlin을 쓰게 된다

## Language

프로젝트에서 사용할 프로그래밍 언어를 선택한다

- **Java**: 스프링부트의 기본 언어로, 가장 널리 사용되며 안정적이다
- **Kotlin**: 간결하고 안전한 문법을 제공하며, 스프링부트와의 통합이 잘 되어 인기가 높아지고 있다
- **Groovy**: 동적 언어로, 스크립트처럼 사용 가능하며 간단한 프로젝트나 프로토타입에 적합하다

본인이 사용할 언어로 Java, Kotlin 중에 선택하면 되시겠다

## Spring Boot

스프링부트 버전을 선택한다

- M2(Milestone Release): 개발 중인 버전의 중간단계로 주요 기능이 어느정도 완성된 상태(신규 기능을 써볼 수 있으나 버그나 호환성에 문제가 있을수 있음)
- SNAPSHOT: 개발 중인 버전의 스냅샷으로, 아직 정식으로 릴리즈되지 않은 버전이며 최신 코드 상태를 반영한다.(해당버전의 최신코드 경험가능 but 자주 바뀔수있으며 안정성보장 X)

따라서 괄호 옵션이 없는 최종 릴리즈된 버전 중 하나를 적절하게 선택하는걸 권장한다

## Project Metadata

프로젝트의 기본 정보를 설정하는 섹션

- **Group**: 프로젝트의 그룹 ID를 지정한다. (예: com.example) 보통 회사 도메인 이름을 역순으로 사용한다
- **Artifact**: 프로젝트의 고유한 이름(아티팩트 ID)을 지정한다 (예: demo)
- **Name**: 프로젝트 이름을 설정한다 (기본값: Artifact와 동일)
- **Description**: 프로젝트에 대한 간단한 설명을 입력한다 (기본값 사용 가능)
- **Package name**: 기본 패키지 구조를 설정한다 (기본값: Group + Artifact, 예: com.example.demo)
- **Packaging**: 패키징 방식을 선택한다
    - **JAR**: 내장 서버(예: Tomcat)를 포함해 독립적으로 실행 가능한 애플리케이션을 만든다. 스프링부트의 기본 권장 방식이다
    - **WAR**: 외부 웹 서버에 배포할 수 있는 형태로 패키징한다. 기존 서버 인프라를 활용할 때 사용된다
- **Java**: 사용할 Java 버전을 지정한다 (예: 17, 21, 23 등)

## Dependencies

프로젝트에 필요한 의존성을 추가한다. 주요 선택지로는 다음과 같은게 있다

- **Spring Web**: REST API나 웹 애플리케이션 개발에 필요하다
- **Spring Data JPA**: 데이터베이스 연동을 위한 ORM 도구이다
- **Spring Security**: 인증 및 권한 관리를 위한 보안 기능이다
- **Thymeleaf**: 서버 사이드 HTML 템플릿 엔진으로, 동적 웹 페이지를 생성한다
- **Spring Boot DevTools**: 개발 시 자동 재시작, 라이브 리로드 등 편리한 기능을 제공한다

이외에도 필요에 따라 추가해주시면 되겠다

## 프로젝트 직접생성해보기

![2-1.springboot-2.png](/images/2-1.springboot-2.png)

간단하게 Spring Web만 Dependencies로 추가하여 생성해보고 압축을 풀어 Intellij로 해당 폴더를 열어보자