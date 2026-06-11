# Spring Testing

## Dlaczego testujemy?

Testy pozwalają:

- wykrywać błędy,
- zapobiegać regresjom,
- bezpiecznie refaktoryzować kod,
- dokumentować zachowanie aplikacji.

---

# Piramida Testów

```text
          E2E
         /   \
 Integration
      /     \
 Unit Tests
```

Najwięcej powinno być testów jednostkowych.

---

# Rodzaje testów

## Unit Tests

Testują pojedynczą klasę.

Przykład:

```text
UserService
```

bez:

- bazy danych,
- Spring Context,
- API.

---

## Integration Tests

Sprawdzają współpracę komponentów.

Przykłady:

```text
Controller + Service

Service + Repository

Repository + Database
```

---

## End-To-End Tests

Testują cały system.

```text
Frontend
↓
Backend
↓
Database
```

---

# JUnit 5

Najpopularniejszy framework testowy.

Dependency:

```xml
spring-boot-starter-test
```

---

# Podstawowy test

```java
@Test
void shouldReturnUser() {

}
```

---

# Assertions

## assertEquals

```java
assertEquals(5, result);
```

---

## assertTrue

```java
assertTrue(result);
```

---

## assertFalse

```java
assertFalse(result);
```

---

## assertNull

```java
assertNull(user);
```

---

## assertNotNull

```java
assertNotNull(user);
```

---

## assertThrows

```java
assertThrows(
        UserNotFoundException.class,
        () -> service.findById(1L)
);
```

---

# AAA Pattern

Najpopularniejszy sposób pisania testów.

```text
Arrange
Act
Assert
```

---

## Przykład

```java
@Test
void shouldCalculateSum() {

    // Arrange
    Calculator calculator =
            new Calculator();

    // Act
    int result =
            calculator.add(2, 3);

    // Assert
    assertEquals(5, result);
}
```

---

# Mockito

Framework do mockowania zależności.

---

# @Mock

Tworzy mock.

```java
@Mock
private UserRepository repository;
```

---

# @InjectMocks

Wstrzykuje mocki.

```java
@InjectMocks
private UserService service;
```

---

# MockitoExtension

```java
@ExtendWith(MockitoExtension.class)
```

---

# when(...).thenReturn(...)

Konfiguracja mocka.

```java
when(repository.findById(1L))
        .thenReturn(Optional.of(user));
```

---

# verify()

Sprawdza wywołania.

```java
verify(repository)
        .save(user);
```

---

# Unit Test Service

Przykład:

```java
@ExtendWith(MockitoExtension.class)
class UserServiceTest {

    @Mock
    UserRepository repository;

    @InjectMocks
    UserService service;

}
```

---

# Testowanie wyjątków

```java
when(repository.findById(1L))
        .thenReturn(Optional.empty());
```

```java
assertThrows(
        UserNotFoundException.class,
        () -> service.findById(1L)
);
```

---

# @SpringBootTest

Uruchamia cały Spring Context.

```java
@SpringBootTest
class UserServiceTest {
}
```

---

## Kiedy używać?

Gdy potrzebujesz:

- beanów Springa,
- konfiguracji,
- integracji komponentów.

---

## Wady

- wolniejsze testy,
- większe zużycie pamięci.

---

# Test Slice

Spring pozwala ładować tylko fragment aplikacji.

---

## @WebMvcTest

Testowanie kontrolerów.

```java
@WebMvcTest(UserController.class)
```

---

## @DataJpaTest

Testowanie repozytoriów.

```java
@DataJpaTest
```

---

# MockMvc

Pozwala testować REST API.

```java
@Autowired
private MockMvc mockMvc;
```

---

# GET Request Test

```java
mockMvc.perform(get("/users/1"))
       .andExpect(status().isOk());
```

---

# POST Request Test

```java
mockMvc.perform(post("/users"))
       .andExpect(status().isCreated());
```

---

# JSON Assertions

```java
.andExpect(
        jsonPath("$.name")
                .value("Jan")
);
```

---

# Testowanie Repository

```java
@DataJpaTest
class UserRepositoryTest {
}
```

---

# Testcontainers

Pozwala uruchamiać prawdziwe usługi w Dockerze.

Przykłady:

- PostgreSQL
- MySQL
- MongoDB
- Kafka

---

# PostgreSQL Container

```java
@PostgreSQLContainer
```

lub

```java
@Container
static PostgreSQLContainer<?> postgres;
```

---

# Zalety Testcontainers

- testy bliskie produkcji,
- prawdziwa baza danych,
- brak mockowania SQL.

---

# H2 Database

Lekka baza in-memory.

```text
jdbc:h2:mem:testdb
```

---

# Test Coverage

Pokazuje jaki procent kodu został przetestowany.

Popularne narzędzie:

```text
JaCoCo
```

---

# Dobre praktyki

## Jedna odpowiedzialność testu

Jeden test powinien sprawdzać jedną rzecz.

---

## Czytelne nazwy

```java
shouldReturnUserWhenUserExists()

shouldThrowExceptionWhenUserNotFound()
```

---

## Nie testuj implementacji

Testuj zachowanie.

Źle:

```text
sprawdzenie prywatnych metod
```

Dobrze:

```text
sprawdzenie wyniku działania
```

---

# Najczęstsze pytania rekrutacyjne

## Mock vs Stub

Stub zwraca dane.

Mock dodatkowo pozwala weryfikować wywołania.

---

## Mockito vs SpringBootTest

Mockito:

- szybkie testy jednostkowe.

SpringBootTest:

- testy integracyjne.

---

## @Mock vs @MockBean

@Mock:

```text
Mockito
```

@MockBean:

```text
Spring Context
```

---

## @WebMvcTest

Testuje warstwę Controller.

---

## @DataJpaTest

Testuje Repository.

---

## verify()

Sprawdza czy metoda została wywołana.

---

## assertThrows()

Sprawdza wyjątki.

---

# Co trzeba znać na Junior Java Developer

- JUnit 5
- @Test
- Assertions
- Mockito
- @Mock
- @InjectMocks
- when().thenReturn()
- verify()
- assertThrows()
- @SpringBootTest
- @WebMvcTest
- @DataJpaTest
- MockMvc
- Testcontainers (podstawy)
- H2
- JaCoCo