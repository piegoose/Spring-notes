# Microservices

## Definition of a Microservice

A microservice is a small, autonomous service that works together with other services to form a complete application.

Key characteristics:

- independently deployable,
- independently scalable,
- focused on a single business capability,
- owns its own data and logic,
- communicates with other services through well-defined interfaces.

In practice, a microservice should be responsible for one specific business domain and should be developed, deployed, and maintained independently of other services.

---

## Czym są mikroserwisy?

Mikroserwisy to styl architektury, w którym aplikacja jest podzielona na wiele małych, niezależnych usług.

Każda usługa odpowiada za konkretny obszar biznesowy.

Przykład:

```text
E-commerce
│
├── User Service
├── Product Service
├── Order Service
├── Payment Service
└── Notification Service
```

Każdy serwis może być rozwijany, testowany, wdrażany i skalowany niezależnie.

---

# Monolith

Monolit to aplikacja, w której wszystkie moduły znajdują się w jednym projekcie i są wdrażane jako jedna całość.

```text
Monolith Application
│
├── Users
├── Products
├── Orders
├── Payments
└── Notifications
```

---

## Zalety monolitu

- prostszy start projektu,
- prostsze debugowanie,
- łatwiejsze testowanie lokalne,
- jeden deployment,
- prostsza komunikacja między modułami.

---

## Wady monolitu

- trudniejsze skalowanie wybranych części aplikacji,
- większe ryzyko regresji,
- wolniejszy rozwój dużych systemów,
- jeden błąd może wpłynąć na całą aplikację,
- trudniejsze wdrażanie zmian niezależnie.

---

# Microservices

W mikroserwisach każdy moduł jest osobną aplikacją.

```text
User Service       Product Service

Order Service      Payment Service

Notification Service
```

Każdy serwis ma własną odpowiedzialność.

---

## Zalety mikroserwisów

- niezależne wdrażanie,
- niezależne skalowanie,
- mniejsze zespoły mogą pracować nad osobnymi usługami,
- awaria jednego serwisu nie musi zatrzymać całego systemu,
- łatwiejsze użycie różnych technologii.

---

## Wady mikroserwisów

- większa złożoność,
- trudniejszy monitoring,
- trudniejsze debugowanie,
- problemy z komunikacją sieciową,
- potrzeba DevOps, CI/CD, Dockera, Kubernetes,
- trudniejsze zarządzanie danymi.

---

# Monolith vs Microservices

| Cecha | Monolith | Microservices |
|---|---|---|
| Deployment | Jeden | Wiele niezależnych |
| Skalowanie | Cała aplikacja | Wybrane serwisy |
| Komunikacja | W pamięci aplikacji | HTTP / messaging |
| Baza danych | Często jedna | Często osobna per serwis |
| Debugowanie | Prostsze | Trudniejsze |
| Start projektu | Łatwiejszy | Trudniejszy |
| Awaria | Może dotknąć całość | Może być izolowana |

---

# Kiedy używać mikroserwisów?

Mikroserwisy mają sens, gdy:

- system jest duży,
- wiele zespołów pracuje równolegle,
- różne części aplikacji wymagają niezależnego skalowania,
- potrzebne są niezależne deploymenty,
- domena biznesowa jest dobrze podzielona.

---

# Kiedy nie używać mikroserwisów?

Nie warto zaczynać od mikroserwisów, gdy:

- projekt jest mały,
- zespół jest mały,
- domena nie jest dobrze zrozumiana,
- nie ma doświadczenia DevOps,
- monolit wystarczy.

Często dobrym podejściem jest:

```text
Start as monolith
↓
Modular monolith
↓
Microservices
```

---

# Bounded Context

Pojęcie z Domain Driven Design.

Bounded Context oznacza wyraźnie wydzielony obszar biznesowy.

Przykład:

```text
User Context
Order Context
Payment Context
Product Context
```

Każdy mikroserwis powinien odpowiadać jednemu bounded contextowi.

---

# Single Responsibility

Każdy mikroserwis powinien mieć jedną główną odpowiedzialność.

Przykład:

```text
User Service -> zarządzanie użytkownikami

Order Service -> obsługa zamówień

Payment Service -> płatności
```

---

# Database per Service

Dobra praktyka mówi, że każdy mikroserwis powinien mieć własną bazę danych.

```text
User Service    -> users_db

Order Service   -> orders_db

Payment Service -> payments_db
```

---

## Dlaczego osobna baza?

- niezależność serwisów,
- brak silnego powiązania,
- łatwiejsze skalowanie,
- niezależne zmiany schematu.

---

## Wada

Nie można robić prostych joinów między bazami różnych serwisów.

Zamiast tego używa się:

- komunikacji HTTP,
- eventów,
- replikacji danych,
- CQRS.

---

# Komunikacja synchroniczna

Najczęściej przez REST API.

```text
Order Service
↓ HTTP
User Service
```

Przykład:

```http
GET /users/10
```

---

## Zalety

- prosta do zrozumienia,
- łatwa do debugowania,
- dobra dla operacji wymagających natychmiastowej odpowiedzi.

---

## Wady

- zależność od dostępności drugiego serwisu,
- większe opóźnienia,
- ryzyko kaskadowych awarii.

---

# Komunikacja asynchroniczna

Serwisy komunikują się przez wiadomości lub eventy.

```text
Order Service
↓ event
Message Broker
↓
Notification Service
```

Przykład eventu:

```json
{
  "event": "OrderCreated",
  "orderId": 123
}
```

---

## Zalety

- mniejsze sprzężenie,
- większa odporność,
- łatwiejsze skalowanie,
- dobry model dla event-driven architecture.

---

## Wady

- trudniejsze debugowanie,
- eventual consistency,
- trudniejsze testowanie,
- potrzeba brokera wiadomości.

---

# Message Broker

Pośrednik komunikacji asynchronicznej.

Przykłady:

```text
RabbitMQ
Kafka
ActiveMQ
```

---

# RabbitMQ

RabbitMQ jest brokerem wiadomości opartym o kolejki.

```text
Producer
↓
Queue
↓
Consumer
```

Dobre zastosowania:

- kolejki zadań,
- powiadomienia,
- przetwarzanie w tle.

---

# Kafka

Kafka to platforma do event streaming.

```text
Producer
↓
Topic
↓
Consumer Group
```

Dobre zastosowania:

- duży wolumen eventów,
- analiza danych,
- logi,
- event sourcing,
- systemy real-time.

---

# RabbitMQ vs Kafka

| Cecha | RabbitMQ | Kafka |
|---|---|---|
| Model | Queue | Log / Topic |
| Typowe użycie | Zadania, kolejki | Event streaming |
| Przechowywanie wiadomości | Krótsze | Dłuższe |
| Skalowanie | Dobre | Bardzo dobre |
| Replay eventów | Ograniczony | Naturalny |

---

# API Gateway

API Gateway to pojedynczy punkt wejścia do systemu.

```text
Client
↓
API Gateway
↓
User Service
Order Service
Product Service
```

---

## Zadania API Gateway

- routing requestów,
- autoryzacja,
- rate limiting,
- agregacja odpowiedzi,
- ukrycie wewnętrznej struktury systemu.

---

# Service Discovery

Mechanizm odnajdywania usług.

Problem:

```text
Order Service musi znać adres User Service
```

W środowisku mikroserwisowym adresy mogą się zmieniać.

Service Discovery pozwala znaleźć usługę po nazwie.

```text
USER-SERVICE
ORDER-SERVICE
PAYMENT-SERVICE
```

---

# Load Balancing

Rozdzielanie ruchu między wiele instancji.

```text
Client
↓
Load Balancer
↓
User Service Instance 1
User Service Instance 2
User Service Instance 3
```

---

# Circuit Breaker

Mechanizm chroniący system przed kaskadowymi awariami.

Jeżeli jeden serwis nie odpowiada, circuit breaker może przerwać kolejne próby i zwrócić fallback.

```text
Closed -> Open -> Half-Open -> Closed
```

---

# Retry

Ponowienie requestu po niepowodzeniu.

Używać ostrożnie, bo zbyt wiele retry może przeciążyć system.

---

# Timeout

Limit czasu oczekiwania na odpowiedź.

Każda komunikacja między serwisami powinna mieć timeout.

---

# Fallback

Alternatywna odpowiedź, gdy serwis nie działa.

Przykład:

```text
Product Recommendation Service down
↓
Return default recommendations
```

---

# Resilience

Odporność systemu na błędy.

Techniki:

- timeout,
- retry,
- circuit breaker,
- fallback,
- bulkhead,
- rate limiting.

---

# Observability

Observability pozwala zrozumieć, co dzieje się w systemie.

Składa się z:

```text
Logs
Metrics
Traces
```

---

# Logs

Logi pokazują, co wydarzyło się w aplikacji.

Przykład:

```text
Order created with id 123
```

---

# Metrics

Metryki pokazują liczby.

Przykłady:

```text
request_count
error_rate
response_time
cpu_usage
memory_usage
```

---

# Distributed Tracing

Śledzenie jednego requestu przez wiele serwisów.

```text
Client
↓
Gateway
↓
Order Service
↓
Payment Service
↓
Notification Service
```

Dzięki tracingowi można zobaczyć, gdzie request zwolnił lub się wywalił.

---

# Correlation ID

Unikalny identyfikator requestu przekazywany między serwisami.

```text
X-Correlation-ID: abc-123
```

Pomaga łączyć logi z różnych serwisów.

---

# Centralized Configuration

Centralne zarządzanie konfiguracją wielu serwisów.

Problem:

```text
10 serwisów
3 środowiska
wiele plików konfiguracyjnych
```

Rozwiązanie:

```text
Config Server
```

lub konfiguracja przez zmienne środowiskowe.

---

# Environment Variables

W mikroserwisach konfigurację często trzyma się poza kodem.

Przykład:

```bash
DATABASE_URL=jdbc:postgresql://localhost:5432/users
JWT_SECRET=my-secret
```

---

# Stateless Services

Mikroserwisy powinny być bezstanowe.

Nie powinny trzymać sesji użytkownika w pamięci aplikacji.

Stan powinien być przechowywany w:

- bazie danych,
- Redisie,
- zewnętrznym storage.

---

# Scaling

Mikroserwisy można skalować niezależnie.

Przykład:

```text
User Service -> 2 instancje

Product Service -> 3 instancje

Order Service -> 10 instancji
```

---

# Docker

Docker pozwala uruchamiać aplikacje w kontenerach.

```text
Application
↓
Docker Image
↓
Container
```

---

# Dockerfile

Przykład:

```dockerfile
FROM eclipse-temurin:21-jdk
COPY target/app.jar app.jar
ENTRYPOINT ["java", "-jar", "app.jar"]
```

---

# Docker Compose

Pozwala uruchomić wiele usług lokalnie.

```yaml
services:
  user-service:
    image: user-service

  postgres:
    image: postgres
```

---

# Kubernetes

Kubernetes służy do zarządzania kontenerami.

Odpowiada za:

- deployment,
- scaling,
- self-healing,
- service discovery,
- rolling updates.

---

# CI/CD

Automatyzacja procesu:

```text
Code
↓
Build
↓
Test
↓
Docker Image
↓
Deploy
```

---

# Eventual Consistency

W systemach mikroserwisowych dane nie zawsze są natychmiast spójne.

Przykład:

```text
Order Service tworzy zamówienie

Payment Service po chwili potwierdza płatność

Notification Service wysyła maila
```

System osiąga spójność po czasie.

---

# Saga Pattern

Sposób zarządzania procesem biznesowym rozproszonym między serwisami.

Przykład:

```text
Create Order
↓
Reserve Product
↓
Process Payment
↓
Send Confirmation
```

Jeżeli płatność się nie powiedzie:

```text
Cancel Order
Release Product
```

---

# CQRS

Command Query Responsibility Segregation.

Oddzielenie:

```text
Command -> zapis

Query -> odczyt
```

Używane w bardziej złożonych systemach.

---

# Najczęstsze błędy w mikroserwisach

## Zbyt szybkie przejście na mikroserwisy

Mały projekt zwykle lepiej zacząć jako monolit.

---

## Wspólna baza danych

Jeżeli wszystkie serwisy korzystają z jednej bazy, są mocno powiązane.

---

## Brak timeoutów

Brak timeoutu może zawiesić cały przepływ.

---

## Brak observability

Bez logów, metryk i tracingu trudno diagnozować problemy.

---

## Za dużo komunikacji synchronicznej

Może prowadzić do kaskadowych awarii.

---

# Najczęstsze pytania rekrutacyjne

## Monolith vs Microservices

Monolith to jedna aplikacja.

Microservices to wiele niezależnych usług.

---

## Czy każdy mikroserwis powinien mieć własną bazę?

Najczęściej tak, żeby zachować niezależność.

---

## REST vs Messaging

REST jest synchroniczny.

Messaging jest asynchroniczny.

---

## Co to jest API Gateway?

Pojedynczy punkt wejścia do systemu.

---

## Co to jest Service Discovery?

Mechanizm odnajdywania usług po nazwie.

---

## Co to jest Circuit Breaker?

Mechanizm chroniący przed kaskadowymi awariami.

---

## RabbitMQ vs Kafka

RabbitMQ jest dobry do kolejek zadań.

Kafka jest dobra do event streamingu.

---

## Co to jest eventual consistency?

Dane stają się spójne po pewnym czasie.

---

## Co to jest Saga Pattern?

Wzorzec zarządzania transakcją rozproszoną.

---

# Co trzeba znać na Junior / Junior+ Backend Developer

- Monolith vs Microservices
- Bounded Context
- Database per Service
- REST communication
- Messaging
- RabbitMQ basics
- Kafka basics
- API Gateway
- Service Discovery
- Load Balancing
- Circuit Breaker
- Retry
- Timeout
- Fallback
- Observability
- Logs
- Metrics
- Tracing
- Docker
- Docker Compose
- Kubernetes basics
- Eventual Consistency
- Saga Pattern basics
