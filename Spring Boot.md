# Spring Boot

## Czym jest Spring Boot?

Spring Boot jest rozszerzeniem Spring Framework, które upraszcza tworzenie aplikacji poprzez:

- automatyczną konfigurację,
- startery zależności,
- wbudowany serwer aplikacyjny,
- gotową konfigurację produkcyjną.

Przed Spring Boot programista musiał ręcznie konfigurować większość komponentów Springa.

Spring Boot eliminuje dużą część tej konfiguracji.

---

# Dlaczego powstał Spring Boot?

Problemy klasycznego Springa:

- dużo XML,
- skomplikowana konfiguracja,
- czasochłonne tworzenie projektu,
- trudne wdrożenia.

Spring Boot rozwiązuje te problemy.

---

# Zalety Spring Boot

## Szybszy start projektu

Projekt można stworzyć w kilka minut.

---

## Auto Configuration

Automatyczna konfiguracja komponentów.

---

## Starter Dependencies

Gotowe zestawy zależności.

---

## Embedded Server

Brak potrzeby instalowania Tomcata.

---

## Production Ready

Monitoring i metryki dzięki Spring Boot Actuator.

---

# Spring Initializr

Najpopularniejszy sposób tworzenia projektu.

Można wybrać:

- Java Version
- Spring Boot Version
- Maven lub Gradle
- Dependencies

Przykładowe zależności:

```text
Spring Web
Spring Data JPA
Spring Security
Lombok
Validation
PostgreSQL Driver
```

---

# Startery

Starter to zestaw gotowych bibliotek.

## Spring Web

```xml
spring-boot-starter-web
```

REST API + Embedded Tomcat.

---

## Spring Data JPA

```xml
spring-boot-starter-data-jpa
```

Hibernate + JPA.

---

## Spring Security

```xml
spring-boot-starter-security
```

Bezpieczeństwo aplikacji.

---

## Spring Test

```xml
spring-boot-starter-test
```

JUnit + Mockito.

---

# Główna klasa aplikacji

```java
@SpringBootApplication
public class Application {

    public static void main(String[] args) {
        SpringApplication.run(
                Application.class,
                args
        );
    }
}
```

---

# Co robi @SpringBootApplication?

Jest skrótem trzech adnotacji:

```java
@Configuration
@EnableAutoConfiguration
@ComponentScan
```

---

## @Configuration

Pozwala definiować beany.

---

## @EnableAutoConfiguration

Spring sam konfiguruje aplikację.

---

## @ComponentScan

Automatycznie wyszukuje komponenty.

---

# Auto Configuration

Spring Boot analizuje:

- zależności Maven/Gradle,
- konfigurację,
- klasy dostępne w classpath.

Na tej podstawie tworzy potrzebne beany.

Przykład:

Jeżeli dodasz:

```xml
spring-boot-starter-data-jpa
```

Spring automatycznie skonfiguruje:

- Hibernate,
- EntityManager,
- TransactionManager.

---

# Embedded Server

Domyślnie:

```text
Tomcat
```

Alternatywy:

```text
Jetty
Undertow
```

Uruchomienie:

```bash
java -jar app.jar
```

---

# JAR vs WAR

## JAR

Najczęściej używany.

```text
Application
↓
Embedded Tomcat
↓
JVM
```

---

## WAR

Wymaga zewnętrznego serwera.

```text
Application.war
↓
Tomcat
↓
JVM
```

---

# application.properties

Najważniejszy plik konfiguracyjny.

Przykład:

```properties
server.port=8080

spring.application.name=my-app
```

---

# application.yml

Alternatywa dla properties.

```yaml
server:
  port: 8080

spring:
  application:
    name: my-app
```

---

# Profiles

Pozwalają tworzyć konfiguracje dla środowisk.

Przykłady:

```text
dev
test
prod
```

---

## Aktywacja

```properties
spring.profiles.active=dev
```

---

## Pliki

```text
application-dev.properties
application-test.properties
application-prod.properties
```

---

# Wstrzykiwanie wartości

## @Value

```java
@Value("${server.port}")
private int port;
```

---

# Konfiguracja przez Environment

```java
@Autowired
private Environment environment;
```

---

# Bean Validation

Najczęściej używane adnotacje:

```java
@NotNull
@NotBlank
@NotEmpty
@Email
@Size
@Pattern
@Min
@Max
```

---

## Przykład

```java
public record UserDto(

        @NotBlank
        String name,

        @Email
        String email
) {
}
```

---

# CommandLineRunner

Kod wykonywany po starcie aplikacji.

```java
@Bean
CommandLineRunner run() {
    return args -> {
        System.out.println("Application started");
    };
}
```

---

# Lombok

Najczęściej używane adnotacje:

```java
@Getter
@Setter
@NoArgsConstructor
@AllArgsConstructor
@RequiredArgsConstructor
@Builder
@Data
```

---

# Spring Boot Actuator

Monitoring aplikacji.

Dependency:

```xml
spring-boot-starter-actuator
```

---

## Popularne endpointy

```text
/actuator/health
/actuator/info
/actuator/metrics
```

---

# Obsługa wyjątków

## Lokalna

```java
@ExceptionHandler
```

---

## Globalna

```java
@RestControllerAdvice
```

Przykład:

```java
@RestControllerAdvice
public class GlobalExceptionHandler {
}
```

---

# Konfiguracja Logowania

Domyślnie:

```text
SLF4J + Logback
```

---

## Logger

```java
private static final Logger log =
        LoggerFactory.getLogger(UserService.class);
```

---

# DTO

Data Transfer Object.

Cel:

- oddzielenie API od encji,
- bezpieczeństwo,
- lepsza wydajność.

Przykład:

```java
public record UserDto(
        Long id,
        String name,
        String email
) {
}
```

---

# Najczęstsze pytania rekrutacyjne

## Co daje Spring Boot?

Upraszcza konfigurację Springa.

---

## Co robi @SpringBootApplication?

Łączy:

- @Configuration
- @EnableAutoConfiguration
- @ComponentScan

---

## Co to jest Starter?

Gotowy zestaw zależności.

---

## Co to jest Auto Configuration?

Automatyczna konfiguracja beanów przez Spring Boot.

---

## application.properties vs application.yml

Oba służą do konfiguracji aplikacji.

---

## Po co są Profiles?

Pozwalają posiadać różne konfiguracje dla różnych środowisk.

---

## JAR vs WAR

JAR:

- embedded server,
- najczęściej używany.

WAR:

- zewnętrzny serwer aplikacyjny.

---

# Co trzeba znać na Junior Spring Boot Developer

- @SpringBootApplication
- Startery
- Auto Configuration
- Embedded Tomcat
- application.properties
- application.yml
- Profiles
- Bean Validation
- DTO
- Lombok
- Actuator
- CommandLineRunner
- Logging
- Global Exception Handling
- JAR vs WAR