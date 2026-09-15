# Biblioteca
Gestione della biblioteca
📚 Biblioteca Management System - Spring Boot Learning Project
🎯 Idea del Progetto
Un'applicazione completa per gestire una biblioteca: catalogazione libri, prestiti, utenti, penali, prenotazioni e statistiche.

✨ Funzionalità Principali
Autenticazione e Ruoli: ADMIN (bibliotecario), STAFF, MEMBER (utente)

Catalogo Libri: CRUD libri, autori, editori, categorie

Gestione Copie: un libro può avere più copie fisiche

Prestiti: prendi in prestito, restituisci, rinnova

Prenotazioni: prenota libri attualmente in prestito

Penali: calcolo automatico per ritardi

Ricerca Avanzata: per titolo, autore, categoria, ISBN, ecc.

Dashboard Admin: statistiche (libri più prestati, utenti attivi, ritardi)

Notifiche: email per scadenze, prenotazioni disponibili

Storico: traccia tutti i prestiti passati

File Upload: copertine libri, documenti

Audit Log: chi fa cosa nel sistema

🛠️ Aspetti Spring Boot da Coprire
REST API con validazione

Spring Data JPA con query complesse

Spring Security JWT + ruoli

Scheduled Tasks (@Scheduled per scadenze/prestiti)

Email (Spring Mail per notifiche)

Exception Handling globale

Testing completo

Configuration multi-profilo

Docker + database

Actuator per monitoring

Caching (@Cacheable per cataloghi)

Transaction Management

🗄️ Schema del Database
text
┌─────────────────┐
│      USERS      │
├─────────────────┤
│ id (PK)         │
│ username        │
│ email           │
│ password        │
│ role            │ (ADMIN, STAFF, MEMBER)
│ first_name      │
│ last_name       │
│ phone           │
│ address         │
│ date_of_birth   │
│ membership_id   │ (codice socio)
│ membership_date │
│ enabled         │
│ max_books       │ (limite prestiti simultanei)
│ created_at      │
│ updated_at      │
│ last_login_at   │
└─────────────────┘
         │
         │ 1:N
         ▼
┌─────────────────┐
│    BORROWINGS   │ (Prestiti attivi)
├─────────────────┤
│ id (PK)         │
│ user_id (FK)    │ → USERS.id
│ book_copy_id(FK)│ → BOOK_COPIES.id
│ borrow_date     │
│ due_date        │
│ return_date     │ (null = non restituito)
│ renewed_count   │
│ status          │ (ACTIVE, RETURNED, OVERDUE, LOST)
│ notes           │
│ created_at      │
│ updated_at      │
└─────────────────┘

┌─────────────────┐
│      BOOKS      │
├─────────────────┤
│ id (PK)         │
│ title           │
│ subtitle        │
│ isbn            │ (unique)
│ edition         │
│ publication_year│
│ language        │
│ pages_count     │
│ description     │
│ cover_image_url │
│ publisher_id(FK)│ → PUBLISHERS.id
│ created_at      │
│ updated_at      │
└─────────────────┘
         │
         │ 1:N
         ▼
┌─────────────────┐
│   BOOK_COPIES   │ (Copie fisiche)
├─────────────────┤
│ id (PK)         │
│ book_id (FK)    │ → BOOKS.id
│ inventory_code  │ (codice a barre unico)
│ location        │ (scaffale, posizione)
│ condition       │ (NEW, GOOD, FAIR, POOR, DAMAGED)
│ status          │ (AVAILABLE, BORROWED, RESERVED, MAINTENANCE, LOST)
│ acquisition_date│
│ price           │
│ created_at      │
│ updated_at      │
└─────────────────┘

┌─────────────────┐
│     AUTHORS     │
├─────────────────┤
│ id (PK)         │
│ first_name      │
│ last_name       │
│ birth_date      │
│ death_date      │
│ nationality     │
│ biography       │
│ created_at      │
│ updated_at      │
└─────────────────┘

┌─────────────────┐
│  BOOK_AUTHORS   │ (Relazione N:M)
├─────────────────┤
│ book_id (FK)    │ → BOOKS.id
│ author_id (FK)  │ → AUTHORS.id
│ author_order    │ (1=primo autore, 2=secondo, ecc.)
│ PRIMARY KEY(book_id, author_id)
└─────────────────┘

┌─────────────────┐
│    PUBLISHERS   │
├─────────────────┤
│ id (PK)         │
│ name            │
│ address         │
│ city            │
│ country         │
│ website         │
│ email           │
│ created_at      │
│ updated_at      │
└─────────────────┘

┌─────────────────┐
│   CATEGORIES    │
├─────────────────┤
│ id (PK)         │
│ name            │
│ description     │
│ parent_id (FK)  │ → CATEGORIES.id (sub-categorie)
│ dewey_code      │ (classificazione Dewey)
│ created_at      │
│ updated_at      │
└─────────────────┘

┌─────────────────┐
│ BOOK_CATEGORIES │
├─────────────────┤
│ book_id (FK)    │ → BOOKS.id
│ category_id (FK)│ → CATEGORIES.id
│ PRIMARY KEY(book_id, category_id)
└─────────────────┘

┌─────────────────┐
│  RESERVATIONS   │
├─────────────────┤
│ id (PK)         │
│ user_id (FK)    │ → USERS.id
│ book_id (FK)    │ → BOOKS.id
│ request_date    │
│ expiry_date     │ (scade se non ritirato)
│ status          │ (PENDING, READY, FULFILLED, CANCELLED, EXPIRED)
│ notified_at     │ (quando avvisato che è disponibile)
│ created_at      │
│ updated_at      │
└─────────────────┘

┌─────────────────┐
│     FINES       │ (Penali)
├─────────────────┤
│ id (PK)         │
│ user_id (FK)    │ → USERS.id
│ borrowing_id(FK)│ → BORROWINGS.id
│ amount          │
│ reason          │ (LATE_RETURN, LOST_BOOK, DAMAGED_BOOK)
│ days_overdue    │
│ status          │ (PENDING, PAID, WAIVED)
│ payment_date    │
│ payment_method  │ (CASH, CARD, ONLINE)
│ notes           │
│ created_at      │
│ updated_at      │
└─────────────────┘

┌─────────────────┐
│ BORROWING_HISTORY│ (Storico prestiti)
├─────────────────┤
│ id (PK)         │
│ user_id (FK)    │ → USERS.id
│ book_id (FK)    │ → BOOKS.id
│ book_copy_id(FK)│ → BOOK_COPIES.id
│ borrow_date     │
│ due_date        │
│ return_date     │
│ days_borrowed   │
│ was_overdue     │ (boolean)
│ fine_amount     │ (eventuale penale pagata)
│ created_at      │
└─────────────────┘

┌─────────────────┐
│  NOTIFICATIONS  │
├─────────────────┤
│ id (PK)         │
│ user_id (FK)    │ → USERS.id
│ type            │ (DUE_SOON, OVERDUE, RESERVATION_READY, etc.)
│ title           │
│ message         │
│ reference_type  │ (BORROWING, RESERVATION, FINE)
│ reference_id    │
│ read            │ (boolean)
│ read_at         │
│ email_sent      │ (boolean)
│ created_at      │
└─────────────────┘

┌─────────────────┐
│    REVIEWS      │
├─────────────────┤
│ id (PK)         │
│ book_id (FK)    │ → BOOKS.id
│ user_id (FK)    │ → USERS.id
│ rating          │ (1-5)
│ title           │
│ content         │
│ approved        │ (boolean, per moderazione)
│ created_at      │
│ updated_at      │
│ UNIQUE(book_id, user_id)
└─────────────────┘

┌─────────────────┐
│   AUDIT_LOG     │
├─────────────────┤
│ id (PK)         │
│ user_id (FK)    │ → USERS.id
│ action          │ (BORROW, RETURN, CREATE_BOOK, etc.)
│ entity_type     │ (BOOK, BORROWING, USER, etc.)
│ entity_id       │
│ old_value       │ (JSON)
│ new_value       │ (JSON)
│ ip_address      │
│ created_at      │
└─────────────────┘

┌─────────────────┐
│ SYSTEM_SETTINGS │
├─────────────────┤
│ id (PK)         │
│ key             │ (unique)
│ value           │
│ description     │
│ updated_by(FK)  │ → USERS.id
│ updated_at      │
└─────────────────┘
📋 Tabella SYSTEM_SETTINGS - Esempi
key	value	description
MAX_BORROW_DAYS	14	Giorni massimi prestito
MAX_RENEWALS	2	Rinnovi consentiti
FINE_PER_DAY	0.50	Euro al giorno di ritardo
MAX_BOOKS_PER_USER	5	Limite libri simultanei
RESERVATION_EXPIRY_DAYS	3	Giorni per ritirare prenotazione

🔑 Relazioni Chiave
Relazione	Tipo	Descrizione
USER → BORROWINGS	1:N	Storico prestiti attivi
BOOK → BOOK_COPIES	1:N	Un titolo, più copie fisiche
BOOK ↔ AUTHOR	N:M	Tramite BOOK_AUTHORS
BOOK ↔ CATEGORY	N:M	Tramite BOOK_CATEGORIES
CATEGORY → CATEGORY	1:N	Categorie nidificate
USER → FINES	1:N	Penali utente
USER → BORROWING_HISTORY	1:N	Storico completo
USER → NOTIFICATIONS	1:N	Notifiche utente

💡 Casi d'Uso da Implementare
Prendi in prestito: decrementa copie disponibili, crea borrowing

Restituisci: aggiorna stato copia, calcola penale se in ritardo

Prenota: metti in coda per libro non disponibile

Notifica scadenze: @Scheduled daily per email

Cerca libri: full-text search su titolo, autore, ISBN

Dashboard: query aggregate per statistiche

Rinnova prestito: estendi due_date se possibile

Paga penale: aggiorna stato fine e borrowing

Approva review: moderazione contenuti utenti

Export report: genera CSV/PDF statistiche

🎯 Obiettivi

✅ Entità complesse con relazioni multiple

✅ Query JPQL aggregate (COUNT, SUM, AVG)

✅ Stored procedures per report complessi

✅ Transaction multi-operazione (prestito + update copia)

✅ Scheduled tasks (@Scheduled)

✅ Business logic (calcolo penali, disponibilità)

✅ Security (ownership check, role-based access)

✅ Audit (@EntityListeners, @PreUpdate, @PrePersist)

✅ Caching (catalogo libri, categorie)

✅ Email (notifiche scadenze)

✅ File upload (copertine, documenti)

✅ Validation (DTO, custom validators)
