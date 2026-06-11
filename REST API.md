# REST API

## Czym jest REST?

REST (Representational State Transfer) to styl architektury służący do projektowania API.

REST API pozwala klientowi komunikować się z serwerem za pomocą protokołu HTTP.

Przykład:

```text
Frontend
↓
HTTP Request
↓
Backend REST API
↓
Database
```

---

# Resource

W REST najważniejszym pojęciem jest zasób.

Zasób to coś, czym zarządza aplikacja.

Przykłady:

```text
users
orders
products
payments
```

---

# Endpoint

Endpoint to adres URL, pod którym dostępny jest zasób.

Przykłady:

```text
/users
/users/1
/orders
/products/5
```

---

# HTTP Methods

## GET

Służy do pobierania danych.

```http
GET /users
```

```http
GET /users/1
```

---

## POST

Służy do tworzenia nowego zasobu.

```http
POST /users
```

---

## PUT

Służy do pełnej aktualizacji zasobu.

```http
PUT /users/1
```

---

## PATCH

Służy do częściowej aktualizacji zasobu.

```http
PATCH /users/1
```

---

## DELETE

Służy do usuwania zasobu.

```http
DELETE /users/1
```

---

# CRUD vs HTTP

```text
Create -> POST
Read   -> GET
Update -> PUT / PATCH
Delete -> DELETE
```

---

# Dobre nazewnictwo endpointów

## Dobrze

```text
/users
/users/1
/users/1/orders
/products
```

## Źle

```text
/getUsers
/createUser
/deleteUser
/updateProduct
```

W REST używamy rzeczowników, a nie czasowników.

---

# Liczba mnoga w endpointach

Najczęściej używa się liczby mnogiej.

```text
/users
/products
/orders
```

Nie:

```text
/user
/product
/order
```

---

# Path Variable

Używane do identyfikacji konkretnego zasobu.

```http
GET /users/10
```

W Springu:

```java
@GetMapping("/{id}")
public UserDto getUser(
        @PathVariable Long id
) {
    return userService.getUser(id);
}
```

---

# Query Parameters

Używane do filtrowania, sortowania i paginacji.

```http
GET /users?name=jan
```

```http
GET /products?category=books
```

```http
GET /orders?page=0&size=20
```

W Springu:

```java
@GetMapping
public List<UserDto> findUsers(
        @RequestParam String name
) {
    return userService.findUsers(name);
}
```

---

# Request Body

Używane przy POST, PUT i PATCH.

```json
{
  "name": "Jan",
  "email": "jan@example.com"
}
```

W Springu:

```java
@PostMapping
public UserDto createUser(
        @RequestBody UserDto dto
) {
    return userService.createUser(dto);
}
```

---

# Response Body

Dane zwracane przez API.

```json
{
  "id": 1,
  "name": "Jan",
  "email": "jan@example.com"
}
```

---

# HTTP Status Codes

## 2xx Success

### 200 OK

Poprawna odpowiedź.

```text
GET /users/1 -> 200 OK
```

---

### 201 Created

Zasób został utworzony.

```text
POST /users -> 201 Created
```

---

### 204 No Content

Operacja udana, ale bez body.

```text
DELETE /users/1 -> 204 No Content
```

---

# 4xx Client Error

### 400 Bad Request

Niepoprawne dane wejściowe.

```text
POST /users -> 400 Bad Request
```

---

### 401 Unauthorized

Brak uwierzytelnienia.

```text
Brak tokena JWT
```

---

### 403 Forbidden

Brak uprawnień.

```text
Użytkownik zalogowany, ale nie ma dostępu
```

---

### 404 Not Found

Zasób nie istnieje.

```text
GET /users/999 -> 404 Not Found
```

---

# 5xx Server Error

### 500 Internal Server Error

Błąd po stronie serwera.

```text
NullPointerException
Błąd bazy danych
```

---

# ResponseEntity

W Springu `ResponseEntity` pozwala kontrolować:

- status HTTP,
- body,
- headers.

Przykład:

```java
@GetMapping("/{id}")
public ResponseEntity<UserDto> getUser(
        @PathVariable Long id
) {
    UserDto user = userService.getUser(id);
    return ResponseEntity.ok(user);
}
```

---

# Przykłady ResponseEntity

## 200 OK

```java
return ResponseEntity.ok(user);
```

---

## 201 Created

```java
return ResponseEntity
        .status(HttpStatus.CREATED)
        .body(user);
```

---

## 204 No Content

```java
return ResponseEntity
        .noContent()
        .build();
```

---

## 404 Not Found

```java
return ResponseEntity
        .notFound()
        .build();
```

---

# DTO

DTO (Data Transfer Object) służy do przesyłania danych między klientem a backendem.

Nie powinno się wystawiać encji JPA bezpośrednio w API.

---

## Dlaczego nie zwracać encji?

Powody:

- bezpieczeństwo,
- ukrycie struktury bazy danych,
- unikanie problemów z relacjami,
- kontrola nad odpowiedzią JSON.

---

# Przykład DTO

```java
public record UserDto(
        Long id,
        String name,
        String email
) {
}
```

---

# Request DTO i Response DTO

Dobra praktyka to rozdzielenie obiektów wejściowych i wyjściowych.

```text
CreateUserRequest
UserResponse
```

---

## Request DTO

```java
public record CreateUserRequest(
        String name,
        String email,
        String password
) {
}
```

---

## Response DTO

```java
public record UserResponse(
        Long id,
        String name,
        String email
) {
}
```

Nie zwracamy hasła w odpowiedzi.

---

# Validation

Walidacja danych wejściowych.

Najczęściej używane adnotacje:

```java
@NotNull
@NotBlank
@NotEmpty
@Email
@Size
@Min
@Max
@Pattern
```

---

# @Valid

```java
@PostMapping
public ResponseEntity<UserResponse> createUser(
        @Valid @RequestBody CreateUserRequest request
) {
    return ResponseEntity.status(HttpStatus.CREATED)
            .body(userService.createUser(request));
}
```

---

# Error Response

Dobrą praktyką jest zwracanie spójnej struktury błędu.

```json
{
  "timestamp": "2026-01-01T12:00:00",
  "status": 404,
  "error": "Not Found",
  "message": "User not found",
  "path": "/users/10"
}
```

---

# Global Exception Handling

W Springu używamy:

```java
@RestControllerAdvice
```

oraz:

```java
@ExceptionHandler
```

Przykład:

```java
@RestControllerAdvice
public class GlobalExceptionHandler {

    @ExceptionHandler(UserNotFoundException.class)
    public ResponseEntity<ErrorResponse> handle(
            UserNotFoundException exception
    ) {
        return ResponseEntity
                .status(HttpStatus.NOT_FOUND)
                .body(new ErrorResponse(exception.getMessage()));
    }
}
```

---

# Pagination

Paginacja służy do dzielenia danych na strony.

```http
GET /users?page=0&size=20
```

W Spring Data:

```java
Page<User> findAll(Pageable pageable);
```

---

# Sorting

Sortowanie:

```http
GET /users?sort=name,asc
```

```http
GET /users?sort=createdAt,desc
```

---

# Filtering

Filtrowanie:

```http
GET /products?category=books
```

```http
GET /users?active=true
```

---

# Idempotency

Idempotentność oznacza, że wielokrotne wykonanie tej samej operacji daje ten sam efekt.

## Idempotentne

```text
GET
PUT
DELETE
```

## Nie zawsze idempotentne

```text
POST
PATCH
```

---

# Stateless

REST API powinno być bezstanowe.

Serwer nie powinien przechowywać stanu klienta między requestami.

Każdy request powinien zawierać wszystkie potrzebne informacje.

---

# Content Negotiation

Klient może określić oczekiwany format odpowiedzi.

```http
Accept: application/json
```

Najczęściej REST API zwraca JSON.

---

# Headers

Popularne nagłówki:

```text
Authorization
Content-Type
Accept
Location
```

---

# Authorization Header

```http
Authorization: Bearer <token>
```

Najczęściej używane z JWT.

---

# Content-Type

Określa format wysyłanych danych.

```http
Content-Type: application/json
```

---

# Swagger / OpenAPI

Służy do dokumentowania API.

Popularna zależność:

```xml
springdoc-openapi-starter-webmvc-ui
```

Po dodaniu można wejść na:

```text
/swagger-ui.html
```

lub:

```text
/swagger-ui/index.html
```

---

# REST Best Practices

## Używaj poprawnych statusów HTTP

Nie zwracaj zawsze `200 OK`.

---

## Nie wystawiaj encji JPA

Używaj DTO.

---

## Waliduj dane wejściowe

Używaj Bean Validation.

---

## Obsługuj błędy globalnie

Używaj `@RestControllerAdvice`.

---

## Używaj paginacji

Nie zwracaj ogromnych list.

---

## Stosuj spójne nazewnictwo endpointów

```text
/users
/orders
/products
```

---

# Najczęstsze pytania rekrutacyjne

## GET vs POST

GET pobiera dane.

POST tworzy zasób.

---

## PUT vs PATCH

PUT aktualizuje cały zasób.

PATCH aktualizuje część zasobu.

---

## 401 vs 403

401: użytkownik nie jest uwierzytelniony.

403: użytkownik jest uwierzytelniony, ale nie ma dostępu.

---

## 200 vs 201

200: operacja zakończona sukcesem.

201: zasób został utworzony.

---

## 204

Operacja zakończona sukcesem, ale brak body.

---

## Co to jest DTO?

Obiekt do transferu danych między warstwami lub systemami.

---

## Dlaczego nie zwracać encji?

Bo encja reprezentuje strukturę bazy danych, a API powinno mieć własny kontrakt.

---

## Co to znaczy stateless?

Serwer nie przechowuje stanu klienta między requestami.

---

# Co trzeba znać na Junior Backend Developer

- REST
- Resource
- Endpoint
- HTTP Methods
- Status Codes
- DTO
- Request Body
- Response Body
- Path Variable
- Query Param
- ResponseEntity
- Validation
- Error Handling
- Pagination
- Sorting
- Stateless
- Swagger / OpenAPI