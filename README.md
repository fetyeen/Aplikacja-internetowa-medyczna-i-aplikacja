
# 🚀 GETTING STARTED - Szybki start w 5 minut

## ⚡ Quickstart

### 1️⃣ Instalacja (30 sekund)
```bash
cd "d:\4 rok\site"
npm install
```

### 2️⃣ Start serwera (10 sekund)
```bash
npm start
```
Powinieneś zobaczyć:
```
✅ Server running on http://localhost:5000
✅ JWT Secret configured
✅ CORS enabled
```

### Windows

```powershell
.\start-server.ps1
```

### 3️⃣ Otwórz aplikację (5 sekund)
W przeglądarce wpisz:
```
http://localhost:5000
```

### 4️⃣ Zaloguj się (1 sekunda)
```
Email: user@example.com
Hasło: password123
```

### 5️⃣ Odkrywaj aplikację! 🎉

---

## 📁 Struktura projektu

```
d:\4 rok\site\
│
├── 🔧 BACKEND
│   ├── backend.js              ← Główny plik serwera (uruchom npm start)
│   ├── package.json            ← Zależności
│   ├── .env.example            ← Konfiguracja
│
├── 💻 FRONTEND WEB
│   ├── index.html              ← Główna aplikacja webowa (otwórz w przeglądarce)
│
├── 📱 FRONTEND MOBILNY
│   ├── mobile.html             ← Aplikacja mobilna (otwórz na telefonie lub F12)
│
├── 📚 DOKUMENTACJA
│   ├── README.md               ← Pełna dokumentacja + diagramy
│   ├── INSTALLATION_GUIDE.md   ← Szczegółowa instalacja
│   ├── API_EXAMPLES.md         ← Przykłady API (curl, JavaScript)
│   ├── API_TESTING.js          ← Gotowe funkcje do testowania
│   ├── PROJECT_SUMMARY.md      ← Podsumowanie projektu
│   ├── BEST_PRACTICES.md       ← Best practices i guidelines
│   ├── DEPLOYMENT_GUIDE.md     ← Wdrażanie w produkcji
│   ├── GETTING_STARTED.md      ← Ten plik
│
├── 🐳 DOCKER
│   ├── Dockerfile              ← Build Docker image
│   ├── docker-compose.yml      ← Full stack (backend + DB + RabbitMQ + Nginx)
│   ├── .dockerignore           ← Pliki do pominięcia w Docker
│   ├── nginx.conf              ← Konfiguracja reverse proxy
│
├── 🔄 CI/CD
│   ├── .github/workflows/ci-cd.yml  ← GitHub Actions pipeline
│   ├── render.yaml             ← Render.com deployment
│
└── 🛠️ CONFIG
    ├── .gitignore              ← Ignoruj przy git commit
```

---

## 🎯 Główne cechy

### ✅ Co jest w pudełku?

**Backend:**
- Node.js/Express API
- JWT autentykacja
- 20+ endpoints
- In-memory database (dla demo)
- Rate limiting & security

**Web Frontend:**
- Login/Register
- Dashboard
- Zarządzanie pacjentami
- Planowanie wizyt
- Historia medyczna
- Recepty
- Monitoring

**Mobile Frontend:**
- Native-like interface
- Bottom navigation
- Touch-optimized
- Safe area support
- Dark mode ready

**Dokumentacja:**
- Architecture diagrams
- Security flows
- API reference
- Deployment guides
- Best practices

---

## 🧪 Testowanie API

### Metoda 1: Browser Console (Najłatwsze!)

1. Otwórz DevTools: **F12**
2. Idź do **Console** tab
3. Skopiuj i wklej:

```javascript
// Zaloguj się
await fetch('http://localhost:5000/api/auth/login', {
  method: 'POST',
  headers: { 'Content-Type': 'application/json' },
  body: JSON.stringify({ email: 'user@example.com', password: 'password123' })
})
.then(r => r.json())
.then(d => {
  localStorage.setItem('token', d.token);
  console.log('✅ Zalogowano!', d);
});

// Pobierz pacjentów
const token = localStorage.getItem('token');
await fetch('http://localhost:5000/api/patients', {
  headers: { 'Authorization': `Bearer ${token}` }
})
.then(r => r.json())
 .then(d => console.table(d.data));
```

### Metoda 2: API_TESTING.js

```javascript
// Wklej tę linię w konsoli
exampleFullWorkflow();
```

### Metoda 3: cURL (PowerShell)

```powershell
# Zalogowanie
$login = Invoke-WebRequest -Uri "http://localhost:5000/api/auth/login" `
  -Method POST -ContentType "application/json" `
  -Body '{"email":"user@example.com","password":"password123"}'

$token = ($login.Content | ConvertFrom-Json).token

# Pobierz pacjentów
$headers = @{ Authorization = "Bearer $token" }
Invoke-WebRequest -Uri "http://localhost:5000/api/patients" -Headers $headers | 
  Select-Object -ExpandProperty Content | ConvertFrom-Json | 
 Select-Object -ExpandProperty data | Format-Table
```

---

## 🔐 Dane testowe

| Typ | Wartość |
|-----|---------|
| **Email** | user@example.com |
| **Hasło** | password123 |
| **Admin Email** | admin@example.com |
| **Admin Hasło** | admin123 |

Note: `user@example.com` is a patient (role: `patient`). The server enforces that patient users only receive their own data (appointments, medical records, prescriptions). JWTs issued on login include a `patientId` when applicable.

### Preloaded data
- 2 pacjentów (pat-001, pat-002)
- 2 lekarzy (doc-001, doc-002)
- 1 wizyta (apt-001)
- 1 historia medyczna (rec-001)

---

## 🏗️ Architektura

```
┌─────────────────────────────────────────────────────┐
│                    UŻYTKOWNIK                       │
├─────────────────────────────────────────────────────┤
│  💻 Web (index.html)   |   📱 Mobile (mobile.html)  │
├─────────────────────────────────────────────────────┤
│                    REST API                         │
│        (http://localhost:5000/api)                  │
├─────────────────────────────────────────────────────┤
│  🔧 Backend (backend.js)                            │
│  ├── JWT Authentication                            │
│  ├── Rate Limiting                                 │
│  ├── Input Validation                              │
│  └── CORS Protection                               │
├─────────────────────────────────────────────────────┤
│  💾 Database (In-memory Map - demo)                │
│  ├── Patients                                      │
│  ├── Doctors                                       │
│  ├── Appointments                                  │
│  ├── Medical Records                               │
│  └── Prescriptions                                 │
├─────────────────────────────────────────────────────┤
│  🔗 Integrations                                    │
│  ├── CRM System                                    │
│  ├── Inventory Management                          │
│  ├── RabbitMQ (Message Queue)                      │
│  └── Email Service (Ready)                         │
└─────────────────────────────────────────────────────┘
```

---

## 📱 Testowanie aplikacji mobilnej

### Opcja 1: DevTools emulacji
1. Otwórz index.html lub mobile.html
2. Naciśnij **F12**
3. Kliknij ikona "Toggle device toolbar" (Ctrl+Shift+M)
4. Wybierz "iPhone 12 Pro" lub inny telefon

### Opcja 2: Rzeczywisty telefon
1. Skopiuj `mobile.html` do katalogu dostępnego z sieci
2. Otwórz na telefonie: `http://your-computer-ip:8000/mobile.html`
3. Lub użyj IP zamiast localhost w DevTools

### Opcja 3: QR Code
```
Jeśli wdrażasz na serwerze, wygeneruj QR kod do:
https://yourdomain.com/mobile.html
```

---

## 🔄 Docker (Opcjonalnie)

Jeśli chcesz całą aplikację w kontenerach:

```bash
# Uruchom full stack (backend + PostgreSQL + RabbitMQ)
docker-compose up -d

# Dostęp:
# Web: http://localhost
# RabbitMQ Management: http://localhost:15672
# PostgreSQL: localhost:5432

# Zatrzymaj
docker-compose down
```

---

## 🆘 Troubleshooting

### ❌ "npm: command not found"
```bash
# Zainstaluj Node.js ze strony https://nodejs.org
# Sprawdź czy jest zainstalowany:
node --version
npm --version
```

### ❌ "Port 5000 already in use"
```bash
# Zmień port w .env:
PORT=3001

# Lub weź inny port:
npm start -- --port 3001
```

### ❌ "Cannot find module 'express'"
```bash
# Zainstaluj zależności:
npm install
```

### ❌ "ERR_CONNECTION_REFUSED" przy otwieraniu http://localhost:5000
- Upewnij się, że serwer jest uruchomiony (`npm start` lub `.\start-server.ps1`).
- Jeśli serwer jest uruchomiony, a przeglądarka nadal pokazuje `ERR_CONNECTION_REFUSED`, sprawdź zaporę Windows (Firewall). Możesz zezwolić na połączenia przychodzące na porcie 5000 (PowerShell uruchomiony jako administrator):

```powershell
# Zezwól na przychodzące TCP na porcie 5000 (uruchom jako Administrator):
New-NetFirewallRule -DisplayName "Allow Node Backend 5000" -Direction Inbound -Action Allow -Protocol TCP -LocalPort 5000
```

Po tej komendzie spróbuj ponownie otworzyć `http://localhost:5000`.

### ❌ "CORS errors in console"
```javascript
// Upewnij się że:
// 1. Backend jest uruchomiony (npm start)
// 2. API_URL w index.html jest poprawny:
const API_URL = 'http://localhost:5000/api';
// 3. Backend ma CORS enabled (powinien być)
```

### ❌ "Cannot login - Invalid credentials"
```bash
# Sprawdź dane testowe:
Email: user@example.com
Hasło: password123

# Lub zaloguj się jako admin:
Email: admin@example.com
Hasło: admin123
```

---

## 📚 Następne kroki

### Jeśli chcesz się uczyć:
1. Przeczytaj `README.md` - Pełna dokumentacja
2. Sprawdź `API_EXAMPLES.md` - Przykłady
3. Przejrzyj `BEST_PRACTICES.md` - Best practices

### Jeśli chcesz modyfikować kod:
1. Edytuj `backend.js` - dodaj nowe routes
2. Edytuj `index.html` - dodaj nowy UI
3. Edytuj `mobile.html` - dodaj nowe screens
4. Testuj w przeglądarce (F12 → Console)

### Jeśli chcesz wdrożyć w produkcji:
1. Przeczytaj `DEPLOYMENT_GUIDE.md`
2. Wybierz hosting (Heroku, AWS, DigitalOcean, itp.)
3. Skonfiguruj zmienne `.env`
4. Deploy!

### Jeśli chcesz połączyć rzeczywistą bazę danych:
1. Zainstaluj PostgreSQL
2. Zamień `Map` w `backend.js` na SQL queries
3. Zmiguj dane z `.env.example`
4. Restart serwera

---

## 🎓 Uczenie się Node.js/Express

```javascript
// Podstawowa struktura
const express = require('express');
const app = express();

// Middleware
app.use(express.json());

// Route
app.get('/api/hello', (req, res) => {
  res.json({ message: 'Hello World!' });
});

// Start server
app.listen(5000, () => {
  console.log('Server running on port 5000');
});
```

## 🧠 Koncepty do nauki

1. **HTTP Requests**: GET, POST, PUT, DELETE
2. **REST API**: Struktura i conventions
3. **JWT Tokens**: Autentykacja i autoryzacja
4. **Databases**: SQL/NoSQL basics
5. **Security**: Hashing, validation, CORS
6. **Async/Await**: JavaScript promises
7. **Middleware**: Request processing
8. **Error Handling**: Try/catch, error middleware

---

## 📞 Potrzebujesz pomocy?

1. **Sprawdź dokumentację** - `README.md`, `INSTALLATION_GUIDE.md`
2. **Sprawdź DevTools** - F12 → Console → Network
3. **Sprawdź dziennik serwera** - Terminal gdzie uruchomiłeś `npm start`
4. **Testuj API** - Use `API_TESTING.js` examples
5. **Google** - Wiele problemów ma rozwiązania online

---

## ✅ Checklist

- [x] Backend zainstalowany (`npm install`)
- [x] Serwer uruchomiony (`npm start`)
- [x] Web dostępny na http://localhost:5000
- [x] Zalogowałem się (`user@example.com` / `password123`)
- [x] Widzę dashboard
- [x] Mogę dodać pacjenta
- [x] Mogę zaplanować wizytę
- [x] Aplikacja działa! 🎉

---

## 🚀 Teraz Twoja kolej!

Masz teraz:
- ✅ Pełny system medyczny
- ✅ Dokumentacja
- ✅ Przykłady
- ✅ Best practices
- ✅ Deployment guides

**Zacznij kodować! 💻**

---

**Powodzenia! Jeśli coś nie działa, sprawdź INSTALLATION_GUIDE.md lub console (F12).** 🎉
