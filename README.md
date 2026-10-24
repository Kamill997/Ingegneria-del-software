# MyResto – Ingegneria del Software

Progetto del corso di **Ingegneria del Software** (Corso di Laurea Magistrale in Ingegneria Informatica, A.A. 2024/2025).

**MyResto** è un sistema per la gestione di un ristorante: prenotazioni, ordini, pagamenti, menu, tavoli, pianificazione del personale, report, programma fedeltà e feedback dei clienti. La repository contiene sia la **documentazione di analisi e progettazione** (processo iterativo, con diagrammi UML) sia l'**implementazione in Java** con relativa suite di test.

## Autori e contributi

| Autore | Contributo |
|---|---|
| **Luca Camiolo** | Modellazione UML con Astah e documentazione |
| **Giorgia Grazia Mucciarella** | Testing (progettazione e implementazione dei test, documentazione dei test) |

---

## Contenuto della repository

```
Ingegneria-del-software/
├── 1.Ideazione/                 # Documento di visione, specifiche supplementari e glossario
├── 2.Iterazione 1/              # UC1, UC4, UC6, UC10
├── 3.Iterazione 2/              # UC2, UC3, UC5, UC8
├── 4.Iterazione 3/              # UC7, UC9
├── 5.Elaborato Completo/        # Documento completo (63 pagine) e modello Astah finale
└── MyRestoNew.zip               # Codice sorgente Java (progetto Maven)
```

Ogni cartella di iterazione contiene:

| Elemento | Descrizione |
|---|---|
| `Iterazione N.pdf` | Documento dell'iterazione (casi d'uso, contratti, progettazione) |
| `Ideazione aggiornamento ... .pdf` | Aggiornamento della fase di ideazione per i casi d'uso dell'iterazione |
| `SSD/` | *System Sequence Diagram* dei casi d'uso |
| `SD/` | *Sequence Diagram* di progettazione delle singole interazioni |
| `Modello di Dominio.png` | Modello di dominio aggiornato |
| `Diagramma delle classi.png` | Diagramma delle classi di progetto |
| `*.asta` | File sorgente dei diagrammi, apribili con [Astah](https://astah.net/) |

> I file `.bak` presenti in alcune cartelle `SD/` sono copie di backup generate da Astah.

---

## Funzionalità e casi d'uso

| Caso d'uso | Attore primario | Descrizione | Iterazione |
|---|---|---|---|
| **UC1** – Gestione prenotazione (CRUD) | Cliente | Inserimento, modifica, ricerca ed eliminazione di una prenotazione | 1 |
| **UC4** – Gestione menu (CRUD) | Amministratore | Gestione di piatti e ingredienti del menu | 1 |
| **UC6** – Pianificazione del personale | Amministratore | Assegnazione dei turni ai dipendenti | 1 |
| **UC10** – Gestione tavoli (CRUD) | Amministratore | Inserimento e gestione dei tavoli del ristorante | 1 |
| **UC2** – Gestione ordine (CRUD) | Cliente | Creazione dell'ordine a partire dal menu, con modifica degli ingredienti e note | 2 |
| **UC3** – Gestione pagamento | Amministratore | Calcolo del totale e registrazione del pagamento | 2 |
| **UC5** – Modifica ordine già effettuato | Cliente | Aggiornamento di un ordine esistente | 2 |
| **UC8** – Fidelity card | Cliente | Accumulo punti e riscatto premi | 2 |
| **UC7** – Generazione report | Amministratore | Report sulle vendite (es. piatti più venduti) e incassi | 3 |
| **UC9** – Feedback clienti | Cliente | Valutazioni e commenti sulle prenotazioni/piatti | 3 |

La visione completa del prodotto, le parti interessate e i requisiti non funzionali (sicurezza, usabilità, prestazioni, vincoli di implementazione) sono descritti in `1.Ideazione/`.

---

## Implementazione

Il codice si trova in `MyRestoNew.zip` (progetto **Maven**, Java 21). L'applicazione è a riga di comando e **non usa un database relazionale**: i dati sono salvati su **file di testo** nella cartella `src/main/resources/data/`.

### Struttura del progetto

```
MyRestoNew/
├── pom.xml
└── src/
    ├── main/java/org/example/
    │   ├── Main.java            # Punto di ingresso, composizione delle dipendenze e scenario dimostrativo
    │   ├── model/               # Entità di dominio (Cliente, Ordine, Piatto, Tavolo, Prenotazione, Turno, ...)
    │   ├── repository/          # Interfacce di accesso ai dati (CrudRepository e specializzazioni)
    │   │   └── impl/            # Implementazioni su file (File*Repository)
    │   ├── service/             # Logica di business (un servizio per entità) e facade MyRestoService
    │   └── exception/           # Eccezioni applicative (es. AuthenticationException)
    ├── main/resources/data/     # File di testo di persistenza
    └── test/java/org/example/service/   # Test unitari dei servizi
```

### Architettura a livelli

L'architettura separa in modo netto **presentazione**, **logica di business** e **persistenza**:

- **Model** – classi di dominio: `Cliente`, `Amministratore`, `Dipendente`, `Prenotazione`, `Tavolo`, `Menu`, `Piatto`, `Ingrediente`, `Variazione`, `Ordine`, `RigaOrdine`, `Pagamento`, `FidelityCard`, `Premio`, `PremioRiscattato`, `Turno`, `Report`, `Feedback`.
- **Repository** – pattern *Repository*: un'interfaccia per ogni entità, estesa da `CrudRepository`, e implementazioni su file basate su `FileBasedCrudRepository`.
- **Service** – un servizio per ogni entità e la classe `MyRestoService`, che funge da **facade** verso l'intero sistema.

### Test

I test sono scritti con **JUnit 5** e **Mockito** e coprono i servizi del sistema (`ClienteServiceTest`, `OrdineServiceTest`, `PrenotazioneServiceTest`, `PagamentoServiceTest`, `TurnoServiceTest`, `ReportServiceTest`, ecc.). Vengono usati repository fittizi in memoria, così i test risultano isolati, veloci e ripetibili. La descrizione dei test, divisa per iterazione, è nel documento completo.

---

## Esecuzione

### Prerequisiti

- JDK **21**
- Apache **Maven 3.x**
- (consigliato) IntelliJ IDEA

### Passi

1. Estrarre `MyRestoNew.zip` e aprire la cartella `MyRestoNew` come progetto.
2. Compilare e installare le dipendenze:
   ```bash
   mvn clean install
   ```
3. Avviare l'applicazione eseguendo la classe `org.example.Main` (da IntelliJ: *Run 'Main'*).
4. Per lanciare solo i test:
   ```bash
   mvn test
   ```

All'avvio viene richiesta l'autenticazione come amministratore per sbloccare le funzionalità riservate (creazione piatti, aggiunta dipendenti, ecc.). Credenziali di prova:

| Username | Password |
|---|---|
| `admin` | `adminpass` |

> ⚠️ All'avvio `Main` **ripulisce la cartella dei dati**, mantenendo solo il file dell'amministratore: i dati salvati nelle esecuzioni precedenti vengono quindi rimossi.

---

## Tecnologie

- **Java 21**
- **Maven** (build e gestione delle dipendenze)
- **JUnit 5** e **Mockito** (test)
- **Astah** (modellazione UML)

---

## Documentazione

Il documento di riferimento è `5.Elaborato Completo/Documento completo.pdf`, che raccoglie in un unico file:

1. Documento di visione, specifiche supplementari e glossario
2. Casi d'uso
3. Elaborazione (Iterazioni 1, 2 e 3)
4. Testing
5. Istruzioni di configurazione ed esecuzione
