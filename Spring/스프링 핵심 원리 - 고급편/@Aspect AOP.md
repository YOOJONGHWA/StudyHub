## @Aspect AOP (Aspect-Oriented Programming)

### @Aspect 프록시 적용

스프링에서 AOP를 적용하기 위해서는 @Aspect 애노테이션을 사용하여 포인트컷(Pointcut)과 어드바이스(Advice)를 결합한 **어드바이저(Advisor)**를 만들어야 합니다. 이를 통해 스프링 빈에 자동으로 프록시를 적용할 수 있습니다.

### @Aspect 애노테이션

- @Aspect는 AspectJ 프로젝트에서 제공하는 애노테이션으로, 이를 사용하여 AOP를 쉽게 적용할 수 있습니다.

- 스프링은 이 애노테이션을 사용하여 프록시 기반 AOP를 적용합니다.

- LogTraceAspect 예시
  다음은 로그 추적을 위해 AOP를 사용하는 예시입니다. @Aspect 애노테이션을 사용하여 프록시를 적용하고, @Around로 해당 메서드의 실행 전후에 로그를 기록합니다.

```java

package hello.proxy.config.v6_aop.aspect;

import hello.proxy.trace.TraceStatus;
import hello.proxy.trace.logtrace.LogTrace;
import lombok.extern.slf4j.Slf4j;
import org.aspectj.lang.ProceedingJoinPoint;
import org.aspectj.lang.annotation.Around;
import org.aspectj.lang.annotation.Aspect;

@Slf4j
@Aspect
public class LogTraceAspect {
    private final LogTrace logTrace;

    public LogTraceAspect(LogTrace logTrace) {
        this.logTrace = logTrace;
    }

    @Around("execution(* hello.proxy.app..*(..))")
    public Object execute(ProceedingJoinPoint joinPoint) throws Throwable {
        TraceStatus status = null;
        try {
            String message = joinPoint.getSignature().toShortString();
            status = logTrace.begin(message);
            Object result = joinPoint.proceed(); // 실제 호출 대상 실행
            logTrace.end(status);
            return result;
        } catch (Exception e) {
            logTrace.exception(status, e);
            throw e;
        }
    }
}

```

- @Aspect: 이 애노테이션을 사용하면 스프링이 AOP를 처리할 수 있는 클래스를 인식합니다.

- @Around("execution(_ hello.proxy.app.._(..))"): 특정 포인트컷(메서드 실행)을 지정합니다.

- execution은 AspectJ 포인트컷 표현식을 사용하여, hello.proxy.app 패키지 내 모든 메서드에 대해 어드바이스를 적용합니다.

- ProceedingJoinPoint: 실제 메서드를 호출할 수 있는 객체입니다. proceed()를 호출하여 실제 비즈니스 로직을 실행합니다.

### AOPConfig 클래스

@Aspect 애노테이션을 활용한 로그 추적 AOP를 스프링 애플리케이션에 적용하려면 AopConfig에서 이를 스프링 빈으로 등록해야 합니다.

```java

package hello.proxy.config.v6_aop;

import hello.proxy.config.AppV1Config;
import hello.proxy.config.AppV2Config;
import hello.proxy.config.v6_aop.aspect.LogTraceAspect;
import hello.proxy.trace.logtrace.LogTrace;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.context.annotation.Import;

@Configuration
@Import({AppV1Config.class, AppV2Config.class})
public class AopConfig {
    @Bean
    public LogTraceAspect logTraceAspect(LogTrace logTrace) {
        return new LogTraceAspect(logTrace);
    }
}

```

- @Import({AppV1Config.class, AppV2Config.class}): 기존 V1, V2 애플리케이션 설정 클래스를 수동으로 스프링 빈으로 등록합니다.

- @Bean logTraceAspect(): @Aspect 클래스는 @Bean으로 등록하여 스프링 빈으로 만들어야 프록시가 적용됩니다.

### ProxyApplication 클래스

스프링 애플리케이션을 실행하는 메인 클래스에서 AopConfig를 임포트하여 AOP 설정을 적용합니다.

```java

@SpringBootApplication(scanBasePackages = "hello.proxy.app")
@Import(AopConfig.class)
public class ProxyApplication {
    public static void main(String[] args) {
        SpringApplication.run(ProxyApplication.class, args);
    }

    @Bean
    public LogTrace logTrace() {
        return new ThreadLocalLogTrace();
    }
}

```

- @Import(AopConfig.class): AOP 설정을 애플리케이션에 적용합니다.

### @Aspect 프록시 적용 원리

- 스프링은 자동 프록시 생성기를 사용하여, @Aspect 애노테이션이 붙은 클래스를 찾아서 이를 **어드바이저(Advisor)**로 변환하고, 해당 어드바이저를 기준으로 프록시를 생성합니다.

- 프록시 생성기(예: AnnotationAwareAspectJAutoProxyCreator)는 다음과 같은 과정을 거쳐 프록시를 생성합니다.

1. @Aspect 애노테이션을 찾아서 어드바이저로 변환:

   - 스프링 컨테이너에서 @Aspect 애노테이션이 붙은 빈들을 찾아서 어드바이저로 변환합니다.

2. 어드바이저를 기반으로 프록시 생성:

   - 어드바이저에서 정의된 포인트컷에 맞는 대상 객체를 찾아서, 해당 객체에 프록시를 적용합니다.

3. 프록시 적용 대상 체크:

   - 객체의 클래스와 메서드를 포인트컷에 맞춰 매칭하여 프록시를 적용할지 결정합니다.

4. 프록시 생성 및 빈 등록:

   - 프록시를 생성한 후, 이를 스프링 빈으로 등록하여 프록시가 적용된 객체를 사용하도록 합니다.

정리

- @Aspect 애노테이션을 활용하여 AOP 프록시를 쉽게 적용할 수 있습니다.

- 스프링은 자동 프록시 생성기를 사용하여 @Aspect 클래스를 어드바이저로 변환하고, 프록시를 생성하여 적용합니다.

- 횡단 관심사(cross-cutting concerns)를 해결하는 데 유용하며, 실무에서 AOP를 적용할 때 주로 사용하는 방식입니다.

- AOP를 사용하면 여러 클래스와 메서드에서 공통적인 로직을 재사용할 수 있어, 코드의 유지보수성과 가독성을 높일 수 있습니다.
