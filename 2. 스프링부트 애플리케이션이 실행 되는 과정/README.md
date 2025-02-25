# 2. 스프링부트 애플리케이션이 실행 되는 과정

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

프로그램을 실행하면 `@SpringBootApplication`이 붙어있는 메인 메소드가 호출된다

여기서 `run()메소드`와 `@SpringBootApplication` 코드를 분석해보자

## run() 작동과정

```java
public ConfigurableApplicationContext run(String... args) {
        Startup startup = SpringApplication.Startup.create();
        if (this.properties.isRegisterShutdownHook()) {
            shutdownHook.enableShutdownHookAddition();
        }

        DefaultBootstrapContext bootstrapContext = this.createBootstrapContext();
        ConfigurableApplicationContext context = null;
        this.configureHeadlessProperty();
        SpringApplicationRunListeners listeners = this.getRunListeners(args);
        listeners.starting(bootstrapContext, this.mainApplicationClass);

        Throwable ex;
        try {
            ApplicationArguments applicationArguments = new DefaultApplicationArguments(args);
            ConfigurableEnvironment environment = this.prepareEnvironment(listeners, bootstrapContext, applicationArguments);
            Banner printedBanner = this.printBanner(environment);
            context = this.createApplicationContext();
            context.setApplicationStartup(this.applicationStartup);
            this.prepareContext(bootstrapContext, context, environment, listeners, applicationArguments, printedBanner);
            this.refreshContext(context);
            this.afterRefresh(context, applicationArguments);
            startup.started();
            if (this.properties.isLogStartupInfo()) {
                (new StartupInfoLogger(this.mainApplicationClass, environment)).logStarted(this.getApplicationLog(), startup);
            }

            listeners.started(context, startup.timeTakenToStarted());
            this.callRunners(context, applicationArguments);
        } catch (Throwable var10) {
            ex = var10;
            throw this.handleRunFailure(context, ex, listeners);
        }

        try {
            if (context.isRunning()) {
                listeners.ready(context, startup.ready());
            }

            return context;
        } catch (Throwable var9) {
            ex = var9;
            throw this.handleRunFailure(context, ex, (SpringApplicationRunListeners)null);
        }
    }
```

- `public ConfigurableApplicationContext run(String... args)`
    - 이 메서드는 스프링부트 앱을 실행하고 ConfigurableApplicationContext를 반환한다
    - args는 가변인자를 받아서 처리함
    - 전체적인 코드 흐름은 초기화 → 환경 설정 → 컨텍스트 준비 → 실행 → 마무리 순으로 간다

---

**1. 초기 설정**

```java
Startup startup = SpringApplication.Startup.create();
if (this.properties.isRegisterShutdownHook()) {
    shutdownHook.enableShutdownHookAddition();
}
```

- **Startup 객체 생성**: 앱 시작 시간을 추적하기 위한 Startup 객체를 만든다
    - SpringApplication.Startup.create()로 시작 시점을 기록
- **Shutdown Hook 등록**: 앱 종료 시 깔끔하게 정리되도록 훅을 추가한다
    - isRegisterShutdownHook()이 true면 JVM 종료 시 실행되는 훅을 활성화해
    - 이건 앱이 비정상 종료돼도 리소스를 정리하게 도와준다

---

**2. 부트스트랩 컨텍스트 생성**

```java
DefaultBootstrapContext bootstrapContext = this.createBootstrapContext();
```

- **BootstrapContext**: 초기 부팅 환경을 준비한다
    - 스프링 클라우드 같은 외부 설정을 가져올 때 쓰이는 임시 컨텍스트
    - 아직 메인 컨텍스트는 아니고 초기화 단계에서만 사용

---

**3. 초기 환경 설정 및 리스너 시작**

```java
this.configureHeadlessProperty();
SpringApplicationRunListeners listeners = this.getRunListeners(args);
listeners.starting(bootstrapContext, this.mainApplicationClass);
```

- **Headless 모드 설정**: GUI 없는 환경에서도 실행되도록 시스템 속성을 조정한다
    - java.awt.headless=true로 설정돼
- **RunListeners**: 앱 실행 상태를 감지하는 리스너를 불러온다
    - getRunListeners(args)로 등록된 리스너를 가져와
    - listeners.starting()으로 앱이 시작된다고 알린다
    - 이 리스너는 로깅이나 모니터링 같은 부가 작업을 처리

---

**4. 핵심 실행 단계 (try 블록)**

```java
try {
    ApplicationArguments applicationArguments = new DefaultApplicationArguments(args);
    ConfigurableEnvironment environment = this.prepareEnvironment(listeners, bootstrapContext, applicationArguments);
    Banner printedBanner = this.printBanner(environment);
    context = this.createApplicationContext();
    context.setApplicationStartup(this.applicationStartup);
    this.prepareContext(bootstrapContext, context, environment, listeners, applicationArguments, printedBanner);
    this.refreshContext(context);
    this.afterRefresh(context, applicationArguments);
    startup.started();
    if (this.properties.isLogStartupInfo()) {
        (new StartupInfoLogger(this.mainApplicationClass, environment)).logStarted(this.getApplicationLog(), startup);
    }
    listeners.started(context, startup.timeTakenToStarted());
    this.callRunners(context, applicationArguments);
}
```

- **ApplicationArguments**: 명령줄 인수(args)를 파싱해서 사용 가능하게 만든다
- **Environment 준비**: prepareEnvironment로 설정값을 로드한다
    - application.properties, 환경 변수 등이 환경 객체에 반영
    - 리스너에도 이 환경을 전달
- **Banner 출력**: 시작 시 콘솔에 보이는 스프링 로고를 출력한다
    - 커스텀 배너를 설정할 수도 있어
- **ApplicationContext 생성**: createApplicationContext로 IoC 컨테이너를 만든다
    - 웹 앱이면 AnnotationConfigServletWebServerApplicationContext 같은 걸 생성
- **컨텍스트 준비**: prepareContext로 빈 스캔, 자동 설정, 리소스를 준비한다
    - 부트스트랩 컨텍스트, 환경, 인수 등을 연결해
- **컨텍스트 리프레시**: refreshContext로 빈 생성과 의존성 주입을 완료한다
    - 스프링의 핵심 로직인 refresh()가 실행
- **리프레시 후 처리**: afterRefresh로 추가 작업을 한다 (기본은 비어 있음)
    - 애플리케이션 시작 후 추가적으로 하고 싶은 작업있으면 오버라이드해서 정의 가능
- **Started 상태**: startup.started()로 시작 완료를 기록한다
- **로그 출력**: 시작 정보(시간 등)를 로그로 남긴다
- **리스너 알림**: listeners.started()로 앱이 준비됐다고 알린다
- **Runner 실행**: callRunners로 CommandLineRunner, ApplicationRunner를 호출한다
    - 앱 시작 후 바로 실행할 로직을 처리해

---

**5. 예외 처리**

java

```java
catch (Throwable var10) {
    ex = var10;
    throw this.handleRunFailure(context, ex, listeners);
}
```

- 실행 중 에러가 나면 handleRunFailure로 처리한다
- 리스너에도 실패를 알리고 예외를 던져

---

**6. 실행 완료**

java

```java
try {
    if (context.isRunning()) {
        listeners.ready(context, startup.ready());
    }
    return context;
} catch (Throwable var9) {
    ex = var9;
    throw this.handleRunFailure(context, ex, (SpringApplicationRunListeners)null);
}
```

- **Ready 알림**: 앱이 정상 실행 중이면 listeners.ready()로 준비 완료를 알린다
- **컨텍스트 반환**: ConfigurableApplicationContext를 반환해서 앱이 계속 동작하게 한다
- **예외 처리**: 여기서도 실패 시 handleRunFailure를 호출해

---

**코드 흐름 요약**

1. 초기화: Startup, Shutdown Hook, BootstrapContext 준비한다
2. 환경 설정: Environment, 리스너 시작한다
3. 핵심 로직: 인수 처리 → 환경 준비 → 컨텍스트 생성 → 빈 초기화 → 실행한다
4. 마무리: 시작 완료 알리고 컨텍스트 반환한다
5. 예외 관리: 생성 실패시 handleRuneFailure를 뱉으며 에러처리한다

## @SpringBootApplication

```java
@Target({ElementType.TYPE})
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Inherited
@SpringBootConfiguration
@EnableAutoConfiguration
@ComponentScan(
    excludeFilters = {@Filter(
    type = FilterType.CUSTOM,
    classes = {TypeExcludeFilter.class}
), @Filter(
    type = FilterType.CUSTOM,
    classes = {AutoConfigurationExcludeFilter.class}
)}
)
public @interface SpringBootApplication {
    @AliasFor(
        annotation = EnableAutoConfiguration.class
    )
    Class<?>[] exclude() default {};

    @AliasFor(
        annotation = EnableAutoConfiguration.class
    )
    String[] excludeName() default {};

    @AliasFor(
        annotation = ComponentScan.class,
        attribute = "basePackages"
    )
    String[] scanBasePackages() default {};

    @AliasFor(
        annotation = ComponentScan.class,
        attribute = "basePackageClasses"
    )
    Class<?>[] scanBasePackageClasses() default {};

    @AliasFor(
        annotation = ComponentScan.class,
        attribute = "nameGenerator"
    )
    Class<? extends BeanNameGenerator> nameGenerator() default BeanNameGenerator.class;

    @AliasFor(
        annotation = Configuration.class
    )
    boolean proxyBeanMethods() default true;
}
```

`@SpringBootApplication` 어노테이션은 스프링부트 애플리케이션의 핵심인데, 여러 어노테이션을 하나로 묶어서 초기 실행관련 처리에 대한 도움을 준다

---

**어노테이션 정의**

```java
@Target({ElementType.TYPE})
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Inherited
```

- @Target({ElementType.TYPE}): 이 어노테이션은 클래스, 인터페이스, 열거형 같은 타입에만 붙일 수 있다
- @Retention(RetentionPolicy.RUNTIME): 런타임까지 어노테이션 정보가 유지된다 (리플렉션으로 접근 가능)
- @Documented: JavaDoc에 이 어노테이션이 포함된다
- @Inherited: 이 어노테이션이 붙은 클래스를 상속하면 자식 클래스에도 적용된다

---

**핵심 구성 어노테이션**

```java
@SpringBootConfiguration
@EnableAutoConfiguration
@ComponentScan(...)
```

@SpringBootApplication은 세 가지 주요 어노테이션을 결합한 거야

1. **@SpringBootConfiguration**
    - @Configuration의 스프링부트 버전이다
    - 이 클래스가 설정 클래스임을 나타내고, 빈을 정의할 수 있게 한다
    - 스프링부트에서 추가적인 최적화를 제공한다
2. **@EnableAutoConfiguration**
    - 자동 설정을 활성화한다
    - 클래스패스에 있는 라이브러리를 보고 필요한 설정을 자동으로 추가한다 (예: spring-boot-starter-web 있으면 웹 설정 적용)
3. **@ComponentScan**
    - 컴포넌트 스캔을 실행한다
    - @Component, @Service, @Controller 같은 어노테이션이 붙은 클래스를 찾아 빈으로 등록한다
    - **excludeFilters**: 특정 클래스를 스캔에서 제외한다
        - @Filter(type = FilterType.CUSTOM, classes = {TypeExcludeFilter.class}): 타입 기반 필터로 제외
        - @Filter(type = FilterType.CUSTOM, classes = {AutoConfigurationExcludeFilter.class}): 자동 설정 관련 클래스를 제외

---

**속성(Attribute) 분석**

@SpringBootApplication은 몇 가지 속성을 정의해서 커스터마이징할 수 있다

1. **exclude와 excludeName** java
    
    ```java
    @AliasFor(annotation = EnableAutoConfiguration.class)
    Class<?>[] exclude() default {};
    @AliasFor(annotation = EnableAutoConfiguration.class)
    String[] excludeName() default {};
    ```
    
    - 자동 설정에서 제외할 클래스를 지정한다
    - exclude: 클래스 타입으로 제외 (예: exclude = {DataSourceAutoConfiguration.class})
    - excludeName: 클래스 이름으로 제외 (예: excludeName = {"org.springframework.boot.autoconfigure.jdbc.DataSourceAutoConfiguration"})
    - @AliasFor로 @EnableAutoConfiguration의 속성을 재정의한다
2. **scanBasePackages와 scanBasePackageClasses** java
    
    ```java
    @AliasFor(annotation = ComponentScan.class, attribute = "basePackages")
    String[] scanBasePackages() default {};
    @AliasFor(annotation = ComponentScan.class, attribute = "basePackageClasses")
    Class<?>[] scanBasePackageClasses() default {};
    ```
    
    - 컴포넌트 스캔 범위를 지정한다
    - scanBasePackages: 패키지 이름으로 스캔 범위 설정 (예: {"com.marinelife.study"})
    - scanBasePackageClasses: 특정 클래스가 있는 패키지를 기준으로 스캔한다
    - 기본값은 현재 클래스(@SpringBootApplication 붙은 클래스) 패키지부터 스캔한다
3. **nameGenerator** java
    
    ```java
    @AliasFor(annotation = ComponentScan.class, attribute = "nameGenerator")
    Class<? extends BeanNameGenerator> nameGenerator() default BeanNameGenerator.class;
    ```
    
    - 빈 이름을 생성하는 방법을 커스터마이징한다
    - 기본값은 BeanNameGenerator로, 클래스 이름 기반으로 이름을 만든다
4. **proxyBeanMethods** java
    
    ```java
    @AliasFor(annotation = Configuration.class)
    boolean proxyBeanMethods() default true;
    ```
    
    - @Configuration에서 빈 메서드 호출 시 프록시를 사용할지 결정한다
    - true: 메서드 호출이 빈을 새로 생성하지 않고 컨텍스트에서 가져온다 (싱글톤 보장)
    - false: 프록시 없이 직접 호출한다 (경량 설정에 유리)

---

**결론**

- @SpringBootApplication은 @SpringBootConfiguration, @EnableAutoConfiguration, @ComponentScan을 하나로 묶어서 스프링부트 앱의 기본 설정을 제공한다
- 설정 클래스 정의, 자동 설정 활성화, 컴포넌트 스캔을 한 번에 처리하고, 속성으로 세부 조정도 가능하다
- 이 어노테이션 덕분에 메인 클래스 하나만으로 스프링부트 앱을 띄울 수 있음

## @SpringBootConfiguration, @EnableAutoConfiguration, @ComponentScan 분석해보기

### **@SpringBootConfiguration**

```java
@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Configuration
public @interface SpringBootConfiguration {
    @AliasFor(annotation = Configuration.class)
    boolean proxyBeanMethods() default true;
}
```

**분석**

- **핵심 구성**:
    - @Configuration: 스프링의 기본 @Configuration 어노테이션을 포함한다
        - 이건 클래스가 설정 클래스임을 나타내고, 빈 정의를 할 수 있게 한다
    - proxyBeanMethods: @Configuration의 속성을 재정의한다
        - 기본값 true: 빈 메서드 호출 시 프록시를 통해 싱글톤을 보장한다
        - false로 설정하면 프록시 없이 직접 호출된다 (성능 최적화 가능)
- **역할**:
    - 스프링부트에서 @Configuration을 대체하는 어노테이션이다
    - 설정 클래스로 동작하면서 스프링부트 특화 기능을 추가로 제공한다 (예: 자동 설정과의 통합)

---

### **@EnableAutoConfiguration**

```java
@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Inherited
@AutoConfigurationPackage
@Import(AutoConfigurationImportSelector.class)
public @interface EnableAutoConfiguration {
    String ENABLED_OVERRIDE_PROPERTY = "spring.boot.enableautoconfiguration";

    Class<?>[] exclude() default {};
    String[] excludeName() default {};
}
```

**분석**

- **핵심 구성**:
    - **@AutoConfigurationPackage**:
        - 자동 설정 패키지를 등록한다
        - 내부적으로 @Import(AutoConfigurationPackages.Registrar.class)를 포함해서 메인 클래스의 패키지를 자동 설정에 반영한다
        - 예: com.marinelife.study에 있는 클래스가 자동 설정 기준이 된다
    - **@Import(AutoConfigurationImportSelector.class)**:
        - 자동 설정의 핵심 로직을 담당한다
        - AutoConfigurationImportSelector는 spring.factories 파일을 읽어서 자동 설정 클래스를 로드한다
        - 클래스패스에 있는 의존성을 보고 필요한 설정을 동적으로 추가한다 (예: JPA 있으면 DataSourceAutoConfiguration 로드)
- **속성**:
    - exclude: 자동 설정에서 제외할 클래스 지정 (예: exclude = {DataSourceAutoConfiguration.class})
    - excludeName: 클래스 이름으로 제외 (예: excludeName = {"org...DataSourceAutoConfiguration"})
    - ENABLED_OVERRIDE_PROPERTY: spring.boot.enableautoconfiguration 속성으로 자동 설정을 끌 수 있다
- **역할**:
    - 스프링부트의 자동 설정을 활성화한다
    - spring.factories에서 정의된 설정 클래스를 조건에 따라 로드해서 환경을 준비한다
    - 이게 스프링부트의 "마법" 같은 편리함을 제공하는 핵심이다

---

**3. @ComponentScan**

```java
@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.TYPE)
@Documented
@Repeatable(ComponentScans.class)
public @interface ComponentScan {
    @AliasFor("basePackages")
    String[] value() default {};

    String[] basePackages() default {};

    Class<?>[] basePackageClasses() default {};

    Class<? extends BeanNameGenerator> nameGenerator() default BeanNameGenerator.class;

    Class<? extends ScopeMetadataResolver> scopeResolver() default AnnotationScopeMetadataResolver.class;

    ScopedProxyMode scopedProxy() default ScopedProxyMode.DEFAULT;

    String resourcePattern() default "**/*.class";

    boolean useDefaultFilters() default true;

    Filter[] includeFilters() default {};

    Filter[] excludeFilters() default {};

    boolean lazyInit() default false;

    @Retention(RetentionPolicy.RUNTIME)
    @Target({})
    @interface Filter {
        FilterType type() default FilterType.ANNOTATION;
        Class<?>[] classes() default {};
        String[] pattern() default {};
    }
}
```

**분석**

- **어노테이션 정의**:
    - @Repeatable: 여러 번 붙일 수 있다 (Java 8 이상)
- **속성**:
    - value / basePackages: 스캔할 패키지 지정 (기본은 현재 패키지)
    - basePackageClasses: 특정 클래스 패키지를 기준으로 스캔
    - nameGenerator: 빈 이름 생성 방식 커스터마이징
    - scopeResolver: 빈 스코프 결정 로직 (기본: 어노테이션 기반)
    - scopedProxy: 프록시 모드 설정 (기본: 없음)
    - resourcePattern: 스캔할 파일 패턴 (기본: **/*.class)
    - useDefaultFilters: 기본 필터 사용 여부 (기본: @Component 계열 스캔)
    - includeFilters / excludeFilters: 스캔 포함/제외 조건
    - lazyInit: 지연 초기화 설정 (기본: false)
- **역할**:
    - 지정된 패키지에서 @Component, @Service, @Controller 같은 어노테이션이 붙은 클래스를 찾아 빈으로 등록한다
    - @SpringBootApplication에서는 기본 패키지부터 하위 패키지까지 스캔한다
    - excludeFilters로 특정 클래스(예: 자동 설정 관련)는 제외한다

---

**결론**

1. **시작**:
    - 메인 클래스에 붙어서 실행의 기준점이 된다
2. **BootstrapContext 생성**:
    - @EnableAutoConfiguration이 외부 설정 로드를 준비한다
3. **ApplicationContext 생성**:
    - @SpringBootConfiguration으로 이 클래스가 설정 클래스임을 알린다
4. **Context 준비**:
    - @ComponentScan: 메인 클래스 패키지부터 @Component 계열 클래스를 찾아 빈으로 등록한다
    - @EnableAutoConfiguration: spring.factories에서 자동 설정 클래스를 로드해서 환경에 맞게 설정한다
5. **Context 리프레시**:
    - 스캔된 빈과 자동 설정이 실제로 적용된다
6. **Runners 실행**:
    - @ComponentScan으로 등록된 CommandLineRunner, ApplicationRunner 빈을 실행한다
7. **실행 완료**:
    - 자동 설정으로 내장 서버 같은 구성이 완료된다

## 전체 동작과정

**1. 컴파일 시점: 어노테이션 처리**

- @SpringBootApplication은 @SpringBootConfiguration, @EnableAutoConfiguration, @ComponentScan을 포함하는 메타 어노테이션
- 이 어노테이션들은 소스 코드에 붙어서 컴파일될 때 JVM이 읽을 수 있는 메타데이터로 변환된다
- @Retention(RetentionPolicy.RUNTIME) 덕분에 이 메타데이터는 런타임까지 유지
- 하지만 컴파일 시점에선 "작동"이라기보단 정보가 준비되는 단계
- 예를 들어, @ComponentScan의 basePackages 같은 속성이 코드에 기록된다

**2. 런타임 시작: SpringApplication.run() 호출**

- SpringApplication.run(DemoApplication.class, args)가 호출되면서 애플리케이션이 시작된다
- 이때 StudyApplication.class는 @SpringBootApplication이 붙은 메인 클래스
- run 메서드는 이 클래스의 어노테이션 정보를 읽고 그에 따라 동작을 결정한다

**3. 어노테이션 정보 반영**

- run 메서드 안에서 @SpringBootApplication의 구성 요소가 실제로 기능을 발휘한다
    - @SpringBootConfiguration: 클래스가 설정 클래스임을 인식해서 ApplicationContext 초기화에 반영
    - @EnableAutoConfiguration: AutoConfigurationImportSelector를 통해 자동 설정 클래스를 로드한다
    - @ComponentScan: 지정된 패키지에서 컴포넌트를 스캔해서 빈으로 등록한다
- 이 과정은 run 메서드의 createApplicationContext(), prepareContext(), refreshContext() 단계에서 실행된다

---