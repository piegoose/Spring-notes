# Spring Core

## Czym jest Spring?

Spring Framework to najpopularniejszy framework dla języka Java, który ułatwia tworzenie aplikacji poprzez dostarczanie gotowych rozwiązań dla:

- zarządzania obiektami,
- komunikacji webowej,
- dostępu do baz danych,
- bezpieczeństwa,
- testowania,
- budowy mikroserwisów.

Najważniejszą ideą Springa jest odwrócenie kontroli (IoC) oraz wstrzykiwanie zależności (DI).

---

# Architektura Spring Framework

Spring Framework składa się z wielu modułów:

```text
Spring Framework
│
├── Spring Core
├── Spring Context
├── Spring AOP
├── Spring MVC
├── Spring Data
├── Spring Security
├── Spring Test
└── Spring Boot
```

Spring Core jest fundamentem wszystkich pozostałych modułów.

---

# IoC (Inversion of Control)

## Co to jest?

Inversion of Control oznacza, że tworzeniem oraz zarządzaniem obiektami zajmuje się Spring, a nie programista.

### Bez IoC

```java
UserRepository repository = new UserRepository();
UserService service = new UserService(repository);
```

Programista sam tworzy wszystkie obiekty.

### Z IoC

```java
@Service
public class UserService {
}
```

Spring tworzy obiekt za nas.

---

# IoC Container

Kontener Springa odpowiada za:

- tworzenie beanów,
- konfigurację beanów,
- zarządzanie cyklem życia,
- wstrzykiwanie zależności.

Najważniejsze implementacje:

```text
BeanFactory
ApplicationContext
```

---

## BeanFactory

Podstawowy kontener Springa.

Cechy:

- leniwe tworzenie beanów,
- prostszy,
- rzadko używany bezpośrednio.

---

## ApplicationContext

Najczęściej używany kontener.

Dodatkowo wspiera:

- eventy,
- internacjonalizację,
- AOP,
- automatyczne wykrywanie beanów.

Przykłady:

```java
ApplicationContext context;
```

---

# Dependency Injection (DI)

Dependency Injection oznacza dostarczanie zależności przez Spring Container.

## Dlaczego?

Bez DI klasy są mocno ze sobą powiązane.

DI zmniejsza coupling.

---

# Rodzaje Dependency Injection

## Constructor Injection

Rekomendowany sposób.

```java
@Service
public class UserService {

    private final UserRepository repository;

    public UserService(UserRepository repository) {
        this.repository = repository;
    }
}
```

### Zalety

- immutable fields,
- łatwiejsze testowanie,
- wymusza wymagane zależności.

---

## Setter Injection

```java
@Service
public class UserService {

    private UserRepository repository;

    @Autowired
    public void setRepository(UserRepository repository) {
        this.repository = repository;
    }
}
```

Używane dla opcjonalnych zależności.

---

## Field Injection

```java
@Autowired
private UserRepository repository;
```

Nie jest zalecane.

Powody:

- trudniejsze testowanie,
- ukryte zależności,
- brak immutability.

---

# Bean

Bean to obiekt zarządzany przez Spring Container.

Przykład:

```java
@Service
public class UserService {
}
```

Po uruchomieniu aplikacji Spring tworzy instancję tej klasy.

---

# Tworzenie Beanów

## @Component

Podstawowa adnotacja.

```java
@Component
public class UserService {
}
```

---

## @Service

Warstwa biznesowa.

```java
@Service
public class UserService {
}
```

---

## @Repository

Warstwa dostępu do danych.

```java
@Repository
public class UserRepository {
}
```

Dodatkowo tłumaczy wyjątki bazodanowe.

---

## @Controller

Kontroler MVC.

```java
@Controller
public class UserController {
}
```

---

## @RestController

REST API.

```java
@RestController
public class UserController {
}
```

---

# Stereotype Annotations

```text
@Component
│
├── @Service
├── @Repository
└── @Controller
```

Wszystkie są beanami.

---

# @Autowired

Służy do automatycznego wstrzykiwania zależności.

```java
@Autowired
private UserRepository repository;
```

Od Spring 4.3 nie jest wymagane przy pojedynczym konstruktorze.

---

# Component Scan

Spring automatycznie wyszukuje klasy oznaczone:

```java
@Component
@Service
@Repository
@Controller
```

w pakietach objętych skanowaniem.

---

# Konfiguracja Java Based

Tworzenie beanów za pomocą klasy konfiguracyjnej.

```java
@Configuration
public class AppConfig {
}
```

---

## @Bean

```java
@Bean
public UserService userService() {
    return new UserService();
}
```

Spring zarejestruje zwrócony obiekt jako bean.

---

# Scope Beanów

Domyślny scope:

```java
singleton
```

---

## Singleton

Jedna instancja na aplikację.

```java
@Scope("singleton")
```

Domyślne zachowanie.

---

## Prototype

Nowa instancja przy każdym pobraniu.

```java
@Scope("prototype")
```

---

## Request

Jedna instancja na request HTTP.

```java
@RequestScope
```

---

## Session

Jedna instancja na sesję.

```java
@SessionScope
```

---

# Bean Lifecycle

Etapy:

```text
Instantiate
↓
Populate Properties
↓
PostConstruct
↓
Ready
↓
PreDestroy
↓
Destroy
```

---

## @PostConstruct

Wykonywane po stworzeniu beana.

```java
@PostConstruct
public void init() {
}
```

---

## @PreDestroy

Wykonywane przed usunięciem beana.

```java
@PreDestroy
public void cleanup() {
}
```

---

# Qualifier

Gdy istnieje kilka implementacji tego samego interfejsu.

```java
@Service
public class EmailService
```

```java
@Service
public class SmsService
```

---

Rozwiązanie:

```java
@Autowired
@Qualifier("emailService")
private NotificationService service;
```

---

# Primary

Domyślna implementacja.

```java
@Primary
@Service
public class EmailService
```

Spring wybierze ją automatycznie.

---

# Lazy Initialization

Bean tworzony dopiero przy pierwszym użyciu.

```java
@Lazy
@Service
public class UserService {
}
```

---

# Spring Expression Language (SpEL)

Pozwala wykonywać wyrażenia.

```java
@Value("#{2 + 3}")
```

Przykład:

```java
@Value("${server.port}")
private int port;
```

---

# Events

Spring wspiera Event Driven Programming.

## Tworzenie eventu

```java
public class UserCreatedEvent {
}
```

---

## Publikacja

```java
publisher.publishEvent(event);
```

---

## Nasłuchiwanie

```java
@EventListener
public void handle(UserCreatedEvent event) {
}
```

---

# AOP (Aspect Oriented Programming)

Pozwala oddzielić logikę biznesową od logiki technicznej.

Przykłady:

- logowanie,
- monitoring,
- bezpieczeństwo,
- transakcje.

---

## Aspect

```java
@Aspect
@Component
public class LoggingAspect {
}
```

---

# Najczęstsze pytania rekrutacyjne

## Co to jest IoC?

Odwrócenie kontroli. Tworzeniem obiektów zarządza Spring.

---

## Co to jest DI?

Mechanizm dostarczania zależności przez kontener Spring.

---

## Bean vs Object

Każdy Bean jest obiektem.

Nie każdy obiekt jest Beanem.

---

## Constructor Injection vs Field Injection

Constructor Injection:

- bezpieczniejsze,
- łatwiejsze do testowania,
- rekomendowane.

---

## ApplicationContext vs BeanFactory

BeanFactory:

- podstawowy kontener.

ApplicationContext:

- rozszerzony kontener używany w praktyce.

---

## Singleton vs Prototype

Singleton:

- jedna instancja.

Prototype:

- nowa instancja przy każdym pobraniu.

---

# Co trzeba znać na Junior Spring Developer

- IoC
- Dependency Injection
- Bean
- @Component
- @Service
- @Repository
- @Controller
- @Autowired
- @Qualifier
- @Primary
- Bean Lifecycle
- Scope
- ApplicationContext
- @Configuration
- @Bean
- AOP (podstawy)
- Eventy Springowe