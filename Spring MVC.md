# Spring MVC

## Czym jest Spring MVC?

Spring MVC (Model-View-Controller) to moduł Spring Framework służący do budowy aplikacji webowych i REST API.

MVC oznacza:

```text
Model
View
Controller
```

W aplikacjach backendowych najczęściej wykorzystujemy tylko:

```text
Controller
↓
Service
↓
Repository
↓
Database
```

---

# Architektura Spring MVC

```text
HTTP Request
      ↓
DispatcherServlet
      ↓
Controller
      ↓
Service
      ↓
Repository
      ↓
Database
```

Po wykonaniu operacji odpowiedź wraca do klienta.

```text
Database
↓
Repository
↓
Service
↓
Controller
↓
HTTP Response
```

---

# DispatcherServlet

Najważniejszy element Spring MVC.

Jest Front Controllerem.

Odpowiada za:

- odbieranie requestów,
- znalezienie odpowiedniego kontrolera,
- wywołanie metody,
- zwrócenie odpowiedzi.

---

# Controller

Kontroler obsługuje żądania HTTP.

```java
@Controller
public class UserController {
}
```

---

# @Controller vs @RestController

## @Controller

Zwraca widoki.

```java
@Controller
public class HomeController {

    @GetMapping("/")
    public String home() {
        return "home";
    }
}
```

---

## @RestController

Zwraca dane JSON.

```java
@RestController
public class UserController {
}
```

Najczęściej używana adnotacja w backendzie.

---

# Request Mapping

Mapowanie adresów URL.

```java
@RequestMapping("/users")
```

Przykład:

```java
@RestController
@RequestMapping("/users")
public class UserController {
}
```

---

# Endpointy REST

## GET

Pobieranie danych.

```java
@GetMapping("/{id}")
```

---

## POST

Tworzenie zasobu.

```java
@PostMapping
```

---

## PUT

Aktualizacja całego obiektu.

```java
@PutMapping("/{id}")
```

---

## PATCH

Aktualizacja częściowa.

```java
@PatchMapping("/{id}")
```

---

## DELETE

Usuwanie zasobu.

```java
@DeleteMapping("/{id}")
```

---

# Path Variable

Dane pobierane z URL.

Przykład:

```java
@GetMapping("/{id}")
public UserDto getUser(
        @PathVariable Long id
)
```

Dla URL:

```text
/users/5
```

id = 5

---

# Request Param

Parametry query.

Przykład:

```java
@GetMapping
public List<UserDto> findUsers(
        @RequestParam String name
)
```

URL:

```text
/users?name=jan
```

---

# Request Body

Dane przesyłane w body requestu.

```java
@PostMapping
public UserDto create(
        @RequestBody UserDto dto
)
```

JSON:

```json
{
  "name": "Jan",
  "email": "jan@gmail.com"
}
```

---

# Request Header

Odczyt nagłówków.

```java
@GetMapping
public String get(
        @RequestHeader String authorization
)
```

Przykład:

```text
Authorization: Bearer token
```

---

# ResponseBody

Zwraca dane bezpośrednio do odpowiedzi HTTP.

```java
@ResponseBody
```

`@RestController` zawiera ją automatycznie.

---

# ResponseEntity

Pozwala kontrolować odpowiedź HTTP.

```java
return ResponseEntity.ok(user);
```

---

## Status 200

```java
return ResponseEntity.ok(user);
```

---

## Status 201

```java
return ResponseEntity.status(201)
                     .body(user);
```

---

## Status 204

```java
return ResponseEntity.noContent()
                     .build();
```

---

## Status 404

```java
return ResponseEntity.notFound()
                     .build();
```

---

# HttpStatus

Najczęściej używane:

```text
200 OK
201 Created
204 No Content

400 Bad Request
401 Unauthorized
403 Forbidden
404 Not Found

500 Internal Server Error
```

---

# DTO

Data Transfer Object.

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

# Walidacja

## @Valid

Uruchamia walidację.

```java
@PostMapping
public UserDto create(
        @Valid @RequestBody UserDto dto
)
```

---

# Najpopularniejsze adnotacje walidacyjne

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

# Przykładowa architektura REST API

```text
controller
│
├── UserController
│
service
│
├── UserService
│
repository
│
├── UserRepository
│
entity
│
├── User
│
dto
│
├── UserDto
```

---

# REST API Best Practices

## Używaj rzeczowników

Dobrze:

```text
/users
/orders
/products
```

Źle:

```text
/getUsers
/createUser
/deleteUser
```

---

## Używaj odpowiednich metod HTTP

```text
GET    -> odczyt
POST   -> utworzenie
PUT    -> aktualizacja
DELETE -> usunięcie
```

---

## Zwracaj poprawne statusy

```text
200
201
204
400
404
500
```

---

# Najczęstsze pytania rekrutacyjne

## @Controller vs @RestController

@Controller zwraca widoki.

@RestController zwraca JSON.

---

## @RequestParam vs @PathVariable

@PathVariable pobiera dane z URL.

@RequestParam pobiera query params.

---

## @RequestBody

Mapuje JSON na obiekt Java.

---

## ResponseEntity

Pozwala kontrolować status HTTP i body.

---

## Co robi @Valid?

Uruchamia Bean Validation.

---

## DispatcherServlet

Front Controller Spring MVC.

---

# Co trzeba znać na Junior Spring MVC Developer

- MVC
- DispatcherServlet
- @Controller
- @RestController
- @RequestMapping
- @GetMapping
- @PostMapping
- @PutMapping
- @DeleteMapping
- @RequestBody
- @RequestParam
- @PathVariable
- @RequestHeader
- ResponseEntity
- DTO
- Validation
- @Valid
- Exception Handling
- REST Best Practices