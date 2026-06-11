# Spring Data JPA

## Czym jest JPA?

JPA (Java Persistence API) jest specyfikacją służącą do mapowania obiektów Java na dane przechowywane w bazie danych.

Nie jest biblioteką.

Jest jedynie zestawem interfejsów i zasad.

---

# Hibernate

Najpopularniejsza implementacja JPA.

```text
Application
↓
Spring Data JPA
↓
Hibernate
↓
Database
```

W praktyce używając Spring Data JPA najczęściej korzystasz z Hibernate.

---

# ORM

ORM (Object Relational Mapping) pozwala mapować:

```text
Tabela -> Klasa

Wiersz -> Obiekt

Kolumna -> Pole
```

Przykład:

Tabela:

```text
users
```

| id | name |
|----|------|
| 1 | Jan |

Klasa:

```java
public class User {
    private Long id;
    private String name;
}
```

---

# Encja

Encja reprezentuje tabelę w bazie danych.

```java
@Entity
public class User {
}
```

---

# @Table

Pozwala określić nazwę tabeli.

```java
@Entity
@Table(name = "users")
public class User {
}
```

---

# Klucz główny

```java
@Id
private Long id;
```

---

# Automatyczne generowanie ID

```java
@Id
@GeneratedValue
private Long id;
```

---

# Strategie generowania ID

## IDENTITY

```java
@GeneratedValue(
        strategy = GenerationType.IDENTITY
)
```

Najczęściej używana.

Baza generuje ID.

---

## SEQUENCE

```java
@GeneratedValue(
        strategy = GenerationType.SEQUENCE
)
```

Popularne w PostgreSQL.

---

## AUTO

```java
@GeneratedValue(
        strategy = GenerationType.AUTO
)
```

Hibernate sam wybiera strategię.

---

# Kolumny

## @Column

```java
@Column(name = "email")
private String email;
```

---

## nullable

```java
@Column(nullable = false)
```

Kolumna nie może być NULL.

---

## unique

```java
@Column(unique = true)
```

Wartość musi być unikalna.

---

# Relacje

Najczęściej pojawiający się temat na rozmowach.

---

# One To One

```java
@OneToOne
private Address address;
```

Przykład:

```text
User ↔ Passport
```

Jeden użytkownik ma jeden paszport.

---

# One To Many

```java
@OneToMany
private List<Order> orders;
```

Przykład:

```text
User → Orders
```

Jeden użytkownik posiada wiele zamówień.

---

# Many To One

```java
@ManyToOne
private User user;
```

Przykład:

```text
Orders → User
```

Wiele zamówień należy do jednego użytkownika.

---

# Many To Many

```java
@ManyToMany
private List<Role> roles;
```

Przykład:

```text
Users ↔ Roles
```

---

# Join Column

Tworzy klucz obcy.

```java
@ManyToOne
@JoinColumn(name = "user_id")
private User user;
```

---

# Fetch Types

Określają sposób pobierania danych.

---

## EAGER

```java
@OneToOne(fetch = FetchType.EAGER)
```

Pobiera dane od razu.

---

## LAZY

```java
@OneToMany(fetch = FetchType.LAZY)
```

Pobiera dane dopiero przy użyciu.

Rekomendowane w większości przypadków.

---

# Cascade

Pozwala propagować operacje.

```java
@OneToMany(
        cascade = CascadeType.ALL
)
```

---

Najpopularniejsze:

```java
CascadeType.PERSIST
CascadeType.MERGE
CascadeType.REMOVE
CascadeType.ALL
```

---

# Repository

Spring automatycznie generuje implementację.

```java
public interface UserRepository
        extends JpaRepository<User, Long> {
}
```

---

# Metody JpaRepository

```java
save()
findById()
findAll()
delete()
deleteById()
existsById()
count()
```

---

# Derived Queries

Spring generuje zapytania na podstawie nazw metod.

```java
findByEmail(String email)
```

---

Przykład:

```java
Optional<User> findByEmail(
        String email
);
```

---

# Query Methods

```java
findByName()
findByAgeGreaterThan()
findByEmailContaining()
findByActiveTrue()
```

---

# @Query

Własne zapytanie.

```java
@Query("""
       SELECT u
       FROM User u
       WHERE u.email = :email
       """)
```

---

# JPQL

Java Persistence Query Language.

Działa na encjach.

```java
SELECT u FROM User u
```

Nie:

```sql
SELECT * FROM users
```

---

# Native Query

SQL wykonywany bezpośrednio.

```java
@Query(
        value = """
                SELECT *
                FROM users
                """,
        nativeQuery = true
)
```

---

# Optional

Metoda findById zwraca:

```java
Optional<User>
```

Przykład:

```java
User user = repository
        .findById(id)
        .orElseThrow();
```

---

# Transakcje

Grupa operacji wykonywana jako całość.

```text
SUCCESS -> COMMIT

ERROR -> ROLLBACK
```

---

# @Transactional

```java
@Transactional
public void transferMoney() {
}
```

Jeżeli wystąpi wyjątek:

```text
ROLLBACK
```

---

# Auditing

Automatyczne śledzenie zmian.

---

## Włączenie

```java
@EnableJpaAuditing
```

---

## Pola

```java
@CreatedDate
private LocalDateTime createdAt;

@LastModifiedDate
private LocalDateTime updatedAt;
```

---

# N+1 Problem

Jeden z najpopularniejszych problemów Hibernate.

Przykład:

```text
1 query -> Users

100 queries -> Orders
```

Razem:

```text
101 queries
```

---

Rozwiązania:

- JOIN FETCH
- EntityGraph
- DTO Projection

---

# Entity Lifecycle

```text
Transient
↓
Managed
↓
Detached
↓
Removed
```

---

# DTO Projection

Pobieranie tylko potrzebnych danych.

Zamiast:

```java
User
```

można pobrać:

```java
UserDto
```

---

# Najczęstsze pytania rekrutacyjne

## JPA vs Hibernate

JPA = specyfikacja.

Hibernate = implementacja.

---

## @Entity

Mapuje klasę na tabelę.

---

## @Id

Oznacza klucz główny.

---

## LAZY vs EAGER

LAZY pobiera dane dopiero przy użyciu.

EAGER pobiera od razu.

---

## @Transactional

Tworzy transakcję.

---

## JpaRepository

Gotowe CRUD.

---

## OneToMany vs ManyToOne

OneToMany:

```text
User → Orders
```

ManyToOne:

```text
Order → User
```

---

## Optional

Chroni przed NullPointerException.

---

# Co trzeba znać na Junior Spring Data JPA Developer

- JPA
- Hibernate
- ORM
- @Entity
- @Table
- @Id
- @GeneratedValue
- Relacje
- @OneToMany
- @ManyToOne
- @JoinColumn
- FetchType
- CascadeType
- JpaRepository
- Derived Queries
- @Query
- JPQL
- @Transactional
- Auditing
- Optional
- N+1 Problem (podstawy)