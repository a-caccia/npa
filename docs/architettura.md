# Architettura della Nuova Piattaforma Appalti (NPA)

## Introduzione

La Nuova Piattaforma Appalti (NPA) rappresenta il sistema centrale dell'ecosistema nazionale di e-procurement in Italia. Progettata dall'Autorità Nazionale Anticorruzione (ANAC), la piattaforma svolge un ruolo fondamentale nel monitoraggio dell'intero ciclo di vita degli appalti pubblici, dalla pianificazione all'esecuzione.

Questo documento descrive l'architettura della piattaforma, i principali pattern architetturali utilizzati, i componenti del sistema e le modalità di interazione tra di essi.

## Panoramica dell'Architettura

L'architettura della NPA è progettata per supportare la digitalizzazione completa dei processi dell'intero ciclo di vita dell'appalto, abilitando l'integrazione e l'interoperabilità tra le piattaforme telematiche dell'ecosistema di e-procurement nazionale.

![Architettura Generale](./immagini/architettura-generale.png)

### Componenti Principali

La piattaforma è composta da tre componenti principali:

1. **Nuova Piattaforma Appalti (NPA Core)** - Responsabile del monitoraggio del ciclo di vita dell'appalto
2. **Fascicolo Virtuale dell'Operatore Economico (FVOE)** - Gestisce le informazioni relative ai requisiti degli operatori economici
3. **Fascicolo Virtuale dell'Appalto (FVA)** - Raccoglie tutte le informazioni relative ad un appalto

## Pattern Architetturali

### 1. Service-Oriented Architecture (SOA)

La piattaforma adotta un'architettura orientata ai servizi, dove le funzionalità sono esposte tramite servizi ben definiti organizzati in contesti funzionali:
- pianificazioneAppalto
- gestioneUtenti
- comunicaAppalto
- pubblicaAvviso
- comunicaPostPubblicazione
- serviziComuni
- FVOE
- FVA

Ogni contesto offre servizi specifici accessibili tramite API REST.

### 2. API-First Design

L'intero sistema è progettato seguendo l'approccio API-First, con interfacce definite utilizzando le specifiche OpenAPI. Questo approccio:
- Facilita l'integrazione con sistemi esterni
- Garantisce la chiarezza delle interfacce
- Supporta l'evoluzione controllata dei servizi

Le API sono documentate in file YAML che descrivono le operazioni disponibili, i parametri richiesti e i possibili codici di risposta.

### 3. Orchestrazione e Workflow

Un motore di orchestrazione gestisce il flusso delle informazioni (schede) che caratterizzano il singolo appalto. Questo componente:
- Effettua la validazione sintattica e semantica dei dati di input
- Verifica la coerenza della scheda dati con lo stato dell'appalto
- Attiva i servizi di backend necessari
- Gestisce le transizioni di stato del processo

Le regole di orchestrazione sono definite in maniera dichiarativa e consultabili nella cartella `docs/orchestratore`.

### 4. Event-Driven Architecture

Il sistema utilizza un approccio basato su eventi per tracciare il ciclo di vita degli appalti. Ogni evento significativo viene notificato alla NPA tramite l'invio di una opportuna "scheda" di dati.

Questo approccio consente:
- Tracciabilità completa delle operazioni
- Disaccoppiamento tra i componenti
- Flessibilità nelle implementazioni client

### 5. State Machine Pattern

La piattaforma implementa macchine a stati esplicite per le entità principali:
- Appalto
- Avviso
- TED (Tenders Electronic Daily)

I diagrammi di stato definiscono chiaramente gli stati possibili e le transizioni consentite, facilitando il controllo e la validazione del flusso di processo.

![Diagramma Stati Appalto](./immagini/diagramma-stati-appalto.png)

### 6. Sicurezza e Autenticazione

#### JWT/JWS per la Sicurezza

La piattaforma utilizza token JWS (JSON Web Signature) per garantire l'autenticazione e l'autorizzazione. Il token contiene:
- Identificazione dell'utente e ruolo
- Identificazione della stazione appaltante
- Identificazione della piattaforma e del componente
- Informazioni di tracciamento

#### Contesto di Sicurezza Multi-livello

L'accesso ai servizi è controllato a più livelli:
1. Autenticazione della piattaforma fruitrice tramite PDND
2. Autorizzazione dell'utente in base al ruolo
3. Verifica della relazione tra utente e stazione appaltante

### 7. Interoperabilità e Integrazione

#### Gateway di Interoperabilità

La NPA funge da gateway centrale per l'interoperabilità tra:
- Piattaforme di negoziazione delle Stazioni Appaltanti
- Operatori economici
- Enti certificanti
- TED (Tenders Electronic Daily) per la pubblicità europea

#### Modello B2B

Le integrazioni seguono il modello B2B (Business-to-Business) tramite servizi di interoperabilità che consentono l'integrazione tra sistemi informativi diversi.

La piattaforma PDND (Piattaforma Digitale Nazionale Dati) è utilizzata come infrastruttura di base per tutte le comunicazioni tra sistemi.

### 8. Modello Dati Modulare

Il modello dati è organizzato in "schede" autoconsistenti che rappresentano eventi significativi nel ciclo di vita dell'appalto:
- `SchedaPianificazioneType` - Pianificazione dell'appalto
- `SchedaComunicaAppaltoType` - Comunicazioni relative all'appalto
- `SchedaPostPubblicazioneType` - Comunicazioni successive alla pubblicazione

Questo approccio modulare consente:
- Estensibilità del modello
- Aggiornamenti incrementali
- Validazione contestuale

## Ambienti e Deployment

La piattaforma è disponibile in tre ambienti distinti:

1. **Ambiente di QUALIFICAZIONE** - Destinato alle PDA durante la fase di certificazione
2. **Ambiente di ATTESTAZIONE** - Per test funzionali delle piattaforme certificate
3. **Ambiente di ESERCIZIO** - Per l'uso in produzione

Tutti gli ambienti sono accessibili esclusivamente attraverso l'infrastruttura PDND, con politiche di accesso specifiche per ciascun ambiente.

## Evolutività e Governance

L'architettura è progettata per supportare un'evoluzione continua:
- Le specifiche di interoperabilità sono mantenute nel repository GitHub
- Gli aggiornamenti seguono tempistiche codificate
- Le modifiche al modello dati e al flusso di monitoraggio sono pubblicate con adeguato preavviso

Questa struttura garantisce trasparenza e prevedibilità nelle modifiche, consentendo alle piattaforme interoperabili di pianificare gli aggiornamenti necessari.

## Conclusioni

L'architettura della NPA rappresenta un esempio di sistema complesso progettato per l'interoperabilità e la scalabilità, adatto a supportare processi critici come quelli degli appalti pubblici. I pattern architetturali adottati consentono flessibilità, estensibilità e manutenibilità, garantendo al contempo sicurezza e tracciabilità delle operazioni.