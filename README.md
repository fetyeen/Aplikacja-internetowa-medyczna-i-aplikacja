[README.md](https://github.com/user-attachments/files/24102244/README.md)
# System Medyczny - Dokumentacja Integracji

## 📋 Spis Treści

1. [Architektura Systemu](#architektura-systemu)
2. [Integracja Systemów](#integracja-systemów)
3. [Bezpieczeństwo](#bezpieczeństwo)
4. [Skalowanie](#skalowanie)
5. [Standardy Branżowe](#standardy-branżowe)
6. [Instrukcja Instalacji](#instrukcja-instalacji)

---

## 🏗️ Architektura Systemu

### Diagram Architektury - Microservices

```
┌─────────────────────────────────────────────────────────────────────────┐
│                         KLIENT (Frontend)                                │
│                                                                           │
│  ┌──────────────────────────────────────────────────────────────────┐  │
│  │  Aplikacja Webowa (HTML5 + JavaScript)                           │  │
│  │  ┌─────────────┐  ┌─────────────┐  ┌──────────────────────────┐│  │
│  │  │ Panel       │  │ System      │  │ Panel Integracji         ││  │
│  │  │ Pacjentów   │  │ Medyczny    │  │ - Status Systemów       ││  │
│  │  │             │  │             │  │ - Monitorowanie         ││  │
│  │  └─────────────┘  └─────────────┘  └──────────────────────────┘│  │
│  └──────────────────────────────────────────────────────────────────┘  │
│                                                                           │
└───────────────────────────────┬───────────────────────────────────────────┘
                                │
                        (HTTPS + JWT Token)
                                │
┌───────────────────────────────▼───────────────────────────────────────────┐
│                      API Gateway (Port 5000)                              │
│                   Express.js + Rate Limiting                              │
│                                                                           │
│  ┌─────────────────────────────────────────────────────────────────┐   │
│  │ Request Validation & Security Checks                           │   │
│  │ - JWT Token Verification                                       │   │
│  │ - Rate Limiting (100 req/15 min)                               │   │
│  │ - CORS Policy                                                  │   │
│  │ - Input Validation (Joi)                                       │   │
│  └─────────────────────────────────────────────────────────────────┘   │
│                                                                           │
└───────────────────────────────┬───────────────────────────────────────────┘
                                │
        ┌───────────────────────┼───────────────────────┐
        │                       │                       │
        ▼                       ▼                       ▼
    ┌───────────┐         ┌──────────┐         ┌──────────┐
    │  CRM      │         │ Medyczny │         │ Recepty  │
    │  Service  │         │ Service  │         │ Service  │
    └───────────┘         └──────────┘         └──────────┘
        │                       │                       │
        ▼                       ▼                       ▼
    ┌────────────────────────────────────────────────────┐
    │        Message Queue (RabbitMQ - AMQP 1.0)       │
    │                                                  │
    │  Async Event Processing & System Integration    │
    │  - Patient Events                               │
    │  - Appointment Notifications                    │
    │  - Prescription Updates                         │
    └────────────────────────────────────────────────────┘
        │                       │                       │
        ▼                       ▼                       ▼
    ┌──────────┐         ┌──────────┐         ┌──────────┐
    │  Database│         │ Cache    │         │ Archive  │
    │ (SQL)    │         │(Redis)   │         │ (NoSQL)  │
    └──────────┘         └──────────┘         └──────────┘
```

### Diagram BPMN - Proces Wizyty Medycznej

```
Start
  │
  ▼
┌─────────────────────┐
│ Pacjent rejestruje  │
│ się w systemie      │
└────────┬────────────┘
         │
         ▼
    ┌─────────────────────┐
    │ Zaloguj się do      │
    │ panelu pacjenta     │
    └────────┬────────────┘
             │
             ▼
    ┌─────────────────────────────┐
    │ Wyszukaj dostępne terminy   │
    │ wizyt lekarskich             │
    └────────┬────────────────────┘
             │
             ▼
    ┌─────────────────────────────┐
    │ Zarezerwuj wizytę           │
    │ (wybierz datę, lekarza)     │
    └────────┬────────────────────┘
             │
             ▼
    ┌─────────────────────────────┐
    │ Wysłanie potwierdzenia      │
    │ SMS/Email                   │
    └────────┬────────────────────┘
             │
             ▼
    ┌─────────────────────────────┐
    │ Lekarz przygotowuje się     │
    │ do wizyty                   │
    └────────┬────────────────────┘
             │
             ▼
    ┌─────────────────────────────┐
    │ Wizyta medyczna             │
    │ w klinice                   │
    └────────┬────────────────────┘
             │
             ▼
    ┌─────────────────────────────┐
    │ Lekarz zapisuje diagnozy    │
    │ i przepisuje leki           │
    └────────┬────────────────────┘
             │
             ▼
    ┌─────────────────────────────┐
    │ Pacjent otrzymuje receptę   │
    │ (papierową/elektroniczną)   │
    └────────┬────────────────────┘
             │
             ▼
    ┌─────────────────────────────┐
    │ Historia medyczna aktualna  │
    │ w systemie                  │
    └────────┬────────────────────┘
             │
             ▼
          Koniec
```

### Diagram UML - Model Danych

```
┌─────────────────────┐
│     Patient         │
├─────────────────────┤
│ - id: string        │
│ - firstName: string │
│ - lastName: string  │
│ - email: string     │
│ - phone: string     │
│ - pesel: string     │
│ - dateOfBirth: date │
│ - gender: string    │
│ - address: string   │
│ - bloodType: string │
│ - status: string    │
└─────────────────────┘
          │
          │ has many
          ▼
┌──────────────────────┐
│   Appointment        │
├──────────────────────┤
│ - id: string         │
│ - patientId: string  │
│ - doctorId: string   │
│ - dateTime: datetime │
│ - duration: number   │
│ - reason: string     │
│ - status: string     │
└──────────────────────┘
          │
          │ has one
          ▼
┌──────────────────────┐
│      Doctor          │
├──────────────────────┤
│ - id: string         │
│ - firstName: string  │
│ - lastName: string   │
│ - email: string      │
│ - specialization: s. │
│ - licenseNumber: s.  │
│ - phone: string      │
│ - status: string     │
└──────────────────────┘

┌──────────────────────┐
│   MedicalRecord      │
├──────────────────────┤
│ - id: string         │
│ - patientId: string  │
│ - doctorId: string   │
│ - date: date         │
│ - diagnosis: string  │
│ - treatment: string  │
│ - notes: string      │
└──────────────────────┘

┌──────────────────────┐
│   Prescription       │
├──────────────────────┤
│ - id: string         │
│ - patientId: string  │
│ - doctorId: string   │
│ - date: date         │
│ - medications: array │
│ - status: string     │
└──────────────────────┘
```

---

## 🔄 Integracja Systemów

### Typ Integracji: Dwustronna (Bidirectional)

Systemy są zintegrowane w cztery kierunkach:

1. **Pacjenci ↔ Wizyty**: Pacjent posiada wiele wizyt
2. **Wizyty ↔ Lekarze**: Wizyta przypisana do konkretnego lekarza
3. **Pacjenci ↔ Historia**: Pacjent posiada historię medyczną
4. **Recepty ↔ Pacjenci**: Recepta przypisana pacjentowi

### Flow Danych

```
Pacjent rejestruje się
        │
        ▼
Tworzony rekord w Pacjentach
        │
        ▼
System CRM notyfikowany (AMQP)
        │
        ▼
Pacjent rezerwuje wizytę
        │
        ▼
Wizyta zapisywana + notyfikacja lekarza
        │
        ▼
Lekarz przyjmuje pacjenta
        │
        ▼
Historia medyczna dodawana
        │
        ▼
Przepisane leki → System Recept
        │
        ▼
Pacjent otrzymuje receptę
```

### REST API Endpoints

```
Authentication:
  POST   /api/auth/register         - Rejestracja
  POST   /api/auth/login            - Logowanie
  POST   /api/auth/verify           - Weryfikacja tokenu

Patients:
  GET    /api/patients              - Lista pacjentów
  GET    /api/patients/:id          - Szczegóły pacjenta
  POST   /api/patients              - Dodaj pacjenta
  PUT    /api/patients/:id          - Zaktualizuj pacjenta

Appointments:
  GET    /api/appointments          - Lista wizyt
  POST   /api/appointments          - Zaplanuj wizytę
  PUT    /api/appointments/:id      - Zmień wizytę
  DELETE /api/appointments/:id      - Anuluj wizytę

Medical Records:
  GET    /api/medical-records       - Historia medyczna
  GET    /api/medical-records/:id   - Historia pacjenta
  POST   /api/medical-records       - Dodaj zapis

Prescriptions:
  GET    /api/prescriptions/:id     - Recepty pacjenta
  POST   /api/prescriptions         - Nowa recepta

Monitoring:
  GET    /api/integrations/status   - Status integracji
  GET    /api/integrations/metrics  - Metryki systemu
```

---

## 🔐 Bezpieczeństwo

### Mechanizmy Bezpieczeństwa

#### 1. Autentykacja - OAuth2 + JWT

```
┌─────────────────────────────────────────────────┐
│ Użytkownik                                      │
│ Email: user@example.com                         │
│ Password: ••••••••                              │
└────────────────┬────────────────────────────────┘
                 │
                 ▼ POST /api/auth/login
         ┌───────────────────┐
         │  Weryfikacja      │
         │  poświadczeń      │
         │  (bcrypt)         │
         └────────┬──────────┘
                  │
                  ▼
        ┌──────────────────────┐
        │ Generowanie JWT      │
        │ Token (24h)          │
        │ Payload:             │
        │  - id                │
        │  - email             │
        │  - role              │
        │  - iat, exp          │
        └────────┬─────────────┘
                 │
                 ▼
    ┌─────────────────────────────┐
    │ Wysłanie tokenu do klienta  │
    │ Authorization: Bearer TOKEN │
    └─────────────────────────────┘
                 │
                 ▼
    ┌─────────────────────────────┐
    │ Każde żądanie zawiera token │
    │ w nagłówku Authorization    │
    │ Weryfikacja na serwerze     │
    └─────────────────────────────┘
```

#### 2. Szyfrowanie - TLS/HTTPS

```
Client (Browser)                Server (Backend)
    │                                  │
    │  HTTPS Request (Encrypted)       │
    ├─────────────────────────────────>│
    │  TLS 1.3 Handshake               │
    │  - Certificate Exchange          │
    │  - Key Agreement                 │
    │  - Cipher Selection              │
    │                                  │
    │  HTTPS Response (Encrypted)      │
    │<─────────────────────────────────┤
    │  All Data Encrypted              │
    │  AES-256-GCM                     │
```

#### 3. Walidacja Danych - Joi

```
Request Body:
{
  "email": "user@example.com",
  "password": "secure123",
  "firstName": "Jan",
  "lastName": "Kowalski"
}
        │
        ▼
    ┌─────────────────────┐
    │ Joi Validation      │
    │ Schema:             │
    │ - email (valid)     │
    │ - password (8+ ch)  │
    │ - firstName (req)   │
    │ - lastName (req)    │
    └────────┬────────────┘
             │
        ✓ Valid or ✗ Invalid
```

#### 4. Rate Limiting

```
IP: 192.168.1.100
  │
  Request 1   ✓ OK
  Request 2   ✓ OK
  Request 3   ✓ OK
  ...
  Request 100 ✓ OK
  Request 101 ✗ Rate Limited! (429 Too Many Requests)
  
  Limit: 100 requests / 15 minutes per IP
```

#### 5. CORS Policy

```
Client Domain: http://localhost:3000

  Request Origin: http://localhost:3000
        │
        ▼
  ┌─────────────────────┐
  │ CORS Check:         │
  │ Is origin allowed?  │
  └────────┬────────────┘
           │
    ✓ Yes (in whitelist) → Allow
    ✗ No → Block (403 Forbidden)
    
Allowed Origins:
  - http://localhost:3000
  - http://localhost:8080
  - file:// (dla aplikacji desktopowej)
```

---

## 📈 Skalowanie

### Load Balancing

```
┌──────────────────────────────────────────────────┐
│          Load Balancer (Entry Point)             │
│          Round Robin + Health Checks             │
└──────────────────┬───────────────────────────────┘
                   │
      ┌────────────┼────────────┐
      │            │            │
      ▼            ▼            ▼
  ┌─────────┐  ┌─────────┐  ┌─────────┐
  │Backend 1│  │Backend 2│  │Backend 3│
  │(Port)   │  │(Port)   │  │(Port)   │
  │5000     │  │5001     │  │5002     │
  └────┬────┘  └────┬────┘  └────┬────┘
       │            │            │
       └────────────┼────────────┘
                    │
                    ▼
            ┌──────────────────┐
            │  Database Pool   │
            │  (Shared)        │
            └──────────────────┘
```

### Message Queue (AMQP)

```
┌─────────────────────────────────────────┐
│       RabbitMQ (Message Broker)         │
│       Protocol: AMQP 1.0 (OASIS)        │
└─────────────────────────────────────────┘
          │              │
      Exchange         Queue
   medical-events   crm-inventory-sync
          │              │
    ┌─────┴──────┐      │
    │            │      │
    ▼            ▼      ▼
  CRM    Inventory   Prescription
 Event   Event       Event
 Handler Handler     Handler
```

### Caching (Redis)

```
Request
  │
  ▼
┌─────────────────────┐
│ Is in Cache?        │
└────────┬────────────┘
         │
    ┌────┴───┐
    │        │
   Yes       No
    │        │
    ▼        ▼
  Return  Fetch from
  Cached  Database
  Data      │
            ▼
           Store in
           Cache
            │
            ▼
           Return
           Data
```

---

## 📋 Standardy Branżowe

### OASIS Standards Compliance

#### 1. AMQP 1.0 (Advanced Message Queuing Protocol)

```
Specyfikacja: AMQP 1.0 (OASIS Standard)
Implementation: RabbitMQ
Port: 5672

Features:
- Reliable Message Delivery
- Persistent Messages
- Message Acknowledgment
- Dead Letter Queues
- Topic-based Pub/Sub

Message Format:
{
  "message-id": "msg-001",
  "timestamp": 1700000000000,
  "content-type": "application/json",
  "body": {
    "event": "patient-created",
    "patientId": "pat-001",
    "timestamp": "2024-11-20T10:00:00Z"
  }
}
```

#### 2. OData v4 (Open Data Protocol)

```
Standard: OData Version 4.0
Protocol: HTTP/HTTPS
Format: JSON/XML

Endpoints:
GET /api/patients?$filter=status eq 'active'
GET /api/appointments?$orderby=dateTime desc&$top=10
GET /api/medical-records?$select=id,diagnosis,date
GET /api/patients?$expand=appointments,medicalRecords

Filtering Operators:
- eq (equal)
- ne (not equal)
- gt (greater than)
- lt (less than)
- ge (greater or equal)
- le (less or equal)
- and, or, not
- substringof()
- startswith()
- endswith()
```

#### 3. OpenAPI 3.0 (Swagger)

```
openapi: 3.0.0
info:
  title: Medical System API
  version: 1.0.0
  description: System Integracji Medycznej

servers:
  - url: http://localhost:5000/api
    description: Development Server

paths:
  /patients:
    get:
      summary: Lista pacjentów
      security:
        - bearerAuth: []
      responses:
        '200':
          description: Lista pacjentów
          content:
            application/json:
              schema:
                type: array
                items:
                  $ref: '#/components/schemas/Patient'
        '401':
          description: Unauthorized
        
    post:
      summary: Dodaj pacjenta
      security:
        - bearerAuth: []
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/Patient'
      responses:
        '201':
          description: Pacjent stworzony

components:
  securitySchemes:
    bearerAuth:
      type: http
      scheme: bearer
      bearerFormat: JWT
      
  schemas:
    Patient:
      type: object
      required:
        - firstName
        - lastName
      properties:
        id:
          type: string
          example: pat-001
        firstName:
          type: string
          example: Jan
        lastName:
          type: string
          example: Kowalski
        email:
          type: string
          format: email
        phone:
          type: string
          example: +48123456789
        bloodType:
          type: string
          enum: [O+, O-, A+, A-, B+, B-, AB+, AB-]
```

---

## 🚀 Instrukcja Instalacji

### Wymagania

- Node.js 16+
- npm lub yarn
- Nowoczesna przeglądarka (Chrome, Firefox, Safari, Edge)

### Kroki Instalacji

#### 1. Instalacja Zależności

```bash
# Przejdź do katalogu projektu
cd d:\4 rok\site

# Zainstaluj zależności backendu
npm install
```

#### 2. Konfiguracja Zmiennych Środowiskowych

Utwórz plik `.env`:

```env
NODE_ENV=development
PORT=5000
CORS_ORIGIN=http://localhost:3000
JWT_SECRET=your-super-secret-jwt-key-change-in-production
JWT_EXPIRES_IN=24h

# AMQP Configuration
AMQP_URL=amqp://guest:guest@localhost:5672
AMQP_QUEUE=medical-events

# TLS/HTTPS
ENABLE_HTTPS=false
TLS_KEY_PATH=./certs/key.pem
TLS_CERT_PATH=./certs/cert.pem
```

#### 3. Uruchomienie Backendu

```bash
# Uruchomienie serwera
npm start

# Lub w trybie developerskim z nodemon
npm run dev
```

Backend będzie dostępny na `http://localhost:5000`

#### 4. Uruchomienie Frontendu

Otwórz plik `index.html` w przeglądarce:

- Bezpośrednio: `file:///d:/4 rok/site/index.html`
- Lub użyj prostego serwera HTTP:

```bash
# Python 3
python -m http.server 8000

# Node.js - za pomocą http-server
npx http-server
```

#### 5. Testowanie

```bash
# Health check
curl http://localhost:5000/health

# Rejestracja
curl -X POST http://localhost:5000/api/auth/register \
  -H "Content-Type: application/json" \
  -d '{
    "email": "user@example.com",
    "password": "password123",
    "firstName": "Jan",
    "lastName": "Kowalski"
  }'

# Logowanie
curl -X POST http://localhost:5000/api/auth/login \
  -H "Content-Type: application/json" \
  -d '{
    "email": "user@example.com",
    "password": "password123"
  }'
```

### Demo Dane Testowe

Aplikacja zawiera wstępnie załadowane dane:

**Pacjenci:**
- ID: pat-001, Imię: Jan, Nazwisko: Kowalski
- ID: pat-002, Imię: Maria, Nazwisko: Nowak

**Lekarze:**
- ID: doc-001, Imię: Adam, Nazwisko: Lewandowski, Specjalizacja: Cardiologia
- ID: doc-002, Imię: Anna, Nazwisko: Żakowska, Specjalizacja: Neurologia

---

## 📞 Wsparcie

Pytania lub problemy? Sprawdź konsolę przeglądarki (F12) dla błędów po stronie klienta lub logi backendu.

---

**Autor:** System Integracji Medycznej v1.0  
**Data:** 2024-11-26  
**Licencja:** MIT
