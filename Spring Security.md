# Spring Security

## Czym jest Spring Security?

Spring Security to moduł Spring Framework odpowiedzialny za bezpieczeństwo aplikacji.

Umożliwia obsługę:

- logowania,
- uwierzytelniania,
- autoryzacji,
- haseł,
- ról,
- JWT,
- OAuth2,
- zabezpieczania endpointów.

---

# Authentication

Authentication oznacza uwierzytelnianie.

Odpowiada na pytanie:

```text
Kim jesteś?
```

Przykład:

```text
login + password
```

Jeżeli dane są poprawne, użytkownik zostaje zalogowany.

---

# Authorization

Authorization oznacza autoryzację.

Odpowiada na pytanie:

```text
Co możesz zrobić?
```

Przykład:

```text
USER może odczytać dane

ADMIN może usuwać dane
```

---

# Authentication vs Authorization

```text
Authentication -> kim jesteś?

Authorization  -> co możesz zrobić?
```

Przykład:

```text
Logowanie -> Authentication

Sprawdzenie roli ADMIN -> Authorization
```

---

# Principal

Principal oznacza aktualnie zalogowanego użytkownika.

Najczęściej jest to:

```text
username
email
userId
```

---

# GrantedAuthority

Reprezentuje uprawnienie użytkownika.

Przykłady:

```text
ROLE_USER
ROLE_ADMIN
READ_PRODUCTS
WRITE_PRODUCTS
```

---

# Role vs Authority

## Role

Szersza grupa uprawnień.

```text
ROLE_ADMIN
ROLE_USER
```

---

## Authority

Konkretne uprawnienie.

```text
USER_READ
USER_WRITE
ORDER_DELETE
```

---

# PasswordEncoder

Hasła nie powinny być przechowywane jako plain text.

Spring Security używa `PasswordEncoder`.

---

## BCryptPasswordEncoder

Najczęściej używany encoder haseł.

```java
@Bean
public PasswordEncoder passwordEncoder() {
    return new BCryptPasswordEncoder();
}
```

Przykład:

```text
password123 -> $2a$10$...
```

---

# SecurityFilterChain

Od nowszych wersji Spring Security konfigurację robi się przez `SecurityFilterChain`.

```java
@Bean
public SecurityFilterChain securityFilterChain(
        HttpSecurity http
) throws Exception {
    return http
            .csrf(csrf -> csrf.disable())
            .authorizeHttpRequests(auth -> auth
                    .requestMatchers("/auth/**").permitAll()
                    .anyRequest().authenticated()
            )
            .build();
}
```

---

# authorizeHttpRequests

Określa dostęp do endpointów.

```java
.authorizeHttpRequests(auth -> auth
        .requestMatchers("/public/**").permitAll()
        .requestMatchers("/admin/**").hasRole("ADMIN")
        .anyRequest().authenticated()
)
```

---

# permitAll

Pozwala na dostęp bez logowania.

```java
.requestMatchers("/auth/login").permitAll()
```

---

# authenticated

Wymaga zalogowanego użytkownika.

```java
.anyRequest().authenticated()
```

---

# hasRole

Wymaga konkretnej roli.

```java
.requestMatchers("/admin/**").hasRole("ADMIN")
```

Dla `hasRole("ADMIN")` Spring oczekuje:

```text
ROLE_ADMIN
```

---

# hasAuthority

Wymaga konkretnego uprawnienia.

```java
.requestMatchers("/orders/**").hasAuthority("ORDER_READ")
```

---

# CSRF

CSRF (Cross-Site Request Forgery) to atak, w którym użytkownik wykonuje niechciane żądanie.

W klasycznych aplikacjach webowych CSRF jest ważne.

W REST API z JWT często się je wyłącza:

```java
.csrf(csrf -> csrf.disable())
```

---

# Session Based Authentication

Klasyczne logowanie oparte o sesję.

```text
Login
↓
Server tworzy Session ID
↓
Session ID trafia do cookie
↓
Kolejne requesty używają cookie
```

---

# Stateless Authentication

W REST API często używa się podejścia stateless.

Serwer nie przechowuje sesji użytkownika.

Każdy request zawiera token.

```text
Request
↓
Authorization: Bearer JWT
↓
Server weryfikuje token
```

---

# JWT

JWT (JSON Web Token) to token używany do uwierzytelniania użytkownika.

Składa się z trzech części:

```text
Header.Payload.Signature
```

---

# JWT Header

Zawiera informacje o typie tokena i algorytmie.

```json
{
  "alg": "HS256",
  "typ": "JWT"
}
```

---

# JWT Payload

Zawiera dane użytkownika.

Przykład:

```json
{
  "sub": "jan@example.com",
  "role": "USER",
  "exp": 1710000000
}
```

---

# JWT Signature

Podpis tokena.

Służy do sprawdzenia, czy token nie został zmieniony.

---

# JWT Flow

```text
User wysyła login i hasło
↓
Backend sprawdza dane
↓
Backend generuje JWT
↓
Frontend zapisuje JWT
↓
Frontend wysyła JWT w headerze
↓
Backend weryfikuje JWT
↓
Backend pozwala na dostęp
```

---

# Authorization Header

Token wysyła się w nagłówku:

```http
Authorization: Bearer <token>
```

---

# AuthenticationManager

Odpowiada za proces uwierzytelniania.

```java
authenticationManager.authenticate(
        new UsernamePasswordAuthenticationToken(
                email,
                password
        )
);
```

---

# UsernamePasswordAuthenticationToken

Reprezentuje dane logowania.

Przed autoryzacją:

```text
username + password
```

Po autoryzacji:

```text
principal + authorities
```

---

# UserDetails

Interfejs reprezentujący użytkownika w Spring Security.

Zawiera:

- username,
- password,
- authorities,
- informacje czy konto jest aktywne.

---

# UserDetailsService

Ładuje użytkownika z bazy danych.

```java
public interface UserDetailsService {
    UserDetails loadUserByUsername(
            String username
    );
}
```

---

# SecurityContext

Przechowuje informacje o zalogowanym użytkowniku.

```java
SecurityContextHolder.getContext()
```

---

# SecurityContextHolder

Pozwala pobrać aktualnego użytkownika.

```java
Authentication authentication =
        SecurityContextHolder
                .getContext()
                .getAuthentication();
```

---

# JWT Filter

W aplikacjach JWT tworzy się filtr, który:

- odczytuje token z headera,
- waliduje token,
- pobiera użytkownika,
- ustawia Authentication w SecurityContext.

---

# OncePerRequestFilter

Filtr wykonywany raz dla każdego requestu.

```java
public class JwtAuthenticationFilter
        extends OncePerRequestFilter {
}
```

---

# Dodanie filtra JWT

```java
.addFilterBefore(
        jwtAuthenticationFilter,
        UsernamePasswordAuthenticationFilter.class
)
```

---

# Method Security

Pozwala zabezpieczać metody.

```java
@EnableMethodSecurity
```

---

# @PreAuthorize

Sprawdza uprawnienia przed wykonaniem metody.

```java
@PreAuthorize("hasRole('ADMIN')")
public void deleteUser(Long id) {
}
```

---

# @PostAuthorize

Sprawdza uprawnienia po wykonaniu metody.

```java
@PostAuthorize("returnObject.owner == authentication.name")
```

---

# CORS

CORS kontroluje, z jakich domen frontend może komunikować się z backendem.

Przykład:

```text
Frontend: http://localhost:5173

Backend: http://localhost:8080
```

Bez konfiguracji CORS request może zostać zablokowany.

---

# Konfiguracja CORS

```java
@Bean
public CorsConfigurationSource corsConfigurationSource() {
    CorsConfiguration configuration =
            new CorsConfiguration();

    configuration.setAllowedOrigins(
            List.of("http://localhost:5173")
    );

    configuration.setAllowedMethods(
            List.of("GET", "POST", "PUT", "DELETE")
    );

    configuration.setAllowedHeaders(
            List.of("*")
    );

    UrlBasedCorsConfigurationSource source =
            new UrlBasedCorsConfigurationSource();

    source.registerCorsConfiguration(
            "/**",
            configuration
    );

    return source;
}
```

---

# OAuth2

OAuth2 to standard autoryzacji umożliwiający delegowanie dostępu.

Przykłady:

```text
Logowanie przez Google
Logowanie przez GitHub
Logowanie przez Facebook
```

---

# OAuth2 Flow

```text
User klika "Login with Google"
↓
Google loguje użytkownika
↓
Google zwraca authorization code
↓
Backend wymienia code na token
↓
Aplikacja uzyskuje dane użytkownika
```

---

# Basic Auth

Najprostszy mechanizm uwierzytelniania.

```http
Authorization: Basic base64(username:password)
```

Rzadziej używany w nowoczesnych aplikacjach.

---

# Form Login

Klasyczne logowanie formularzem.

```java
.formLogin()
```

Używane częściej w aplikacjach MVC niż REST API.

---

# Logout

W sesyjnym logowaniu usuwa sesję.

W JWT często logout polega na:

- usunięciu tokena po stronie frontendu,
- ewentualnym blacklistowaniu tokenów.

---

# Najczęstsze błędy

## Przechowywanie haseł plain text

Źle:

```text
password123
```

Dobrze:

```text
$2a$10$...
```

---

## Brak walidacji JWT

Token musi być sprawdzany przy każdym requestcie.

---

## Zwracanie hasła w DTO

Nigdy nie zwracaj hasła w response.

---

## Mylenie 401 i 403

```text
401 -> brak logowania

403 -> brak uprawnień
```

---

# Najczęstsze pytania rekrutacyjne

## Authentication vs Authorization

Authentication sprawdza, kim jesteś.

Authorization sprawdza, co możesz zrobić.

---

## Co to jest JWT?

Token zawierający dane użytkownika, podpisany przez backend.

---

## Z czego składa się JWT?

```text
Header
Payload
Signature
```

---

## Co robi PasswordEncoder?

Hashuje hasła.

---

## Dlaczego BCrypt?

Jest bezpieczniejszy niż zwykłe hashowanie, bo używa soli i jest wolniejszy obliczeniowo.

---

## Co robi UserDetailsService?

Ładuje użytkownika z bazy danych.

---

## Co to jest SecurityContext?

Miejsce, gdzie Spring przechowuje aktualnie zalogowanego użytkownika.

---

## hasRole vs hasAuthority

hasRole automatycznie dodaje prefix:

```text
ROLE_
```

hasAuthority sprawdza dokładną nazwę uprawnienia.

---

## Co to jest SecurityFilterChain?

Konfiguracja zabezpieczeń aplikacji.

---

## Dlaczego w REST API często wyłącza się CSRF?

Bo JWT API zwykle nie używa sesji i cookies do autoryzacji.

---

# Co trzeba znać na Junior Spring Security Developer

- Authentication
- Authorization
- Principal
- Role
- Authority
- PasswordEncoder
- BCryptPasswordEncoder
- SecurityFilterChain
- permitAll
- authenticated
- hasRole
- hasAuthority
- CSRF
- CORS
- JWT
- Authorization Header
- UserDetails
- UserDetailsService
- AuthenticationManager
- SecurityContext
- OncePerRequestFilter
- @PreAuthorize
- OAuth2 basics