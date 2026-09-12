# BMG Clock v1.0.4

**BMG Clock** è un orologio da scrivania basato su **ESP32-C3 SuperMini** e display rotondo **GC9A01 240×240**, progettato da **BMG di Buson Mattia Giovanni**.

Il firmware integra orologio sincronizzato via Internet, configurazione Wi-Fi tramite captive portal, temi grafici, modalità notte, animazioni, minigiochi controllabili da smartphone e aggiornamenti firmware OTA tramite GitHub Releases.

## Caratteristiche principali

- Sincronizzazione automatica dell'ora tramite NTP
- Gestione automatica ora solare / ora legale per il fuso italiano
- Configurazione Wi-Fi tramite rete `BMG Clock Setup`
- Interfaccia web accessibile dalla rete locale
- 6 temi grafici
- Nome personalizzabile sul display, fino a 9 caratteri
- Formato 12 / 24 ore
- Secondi attivabili o disattivabili
- Luminosità software regolabile
- Modalità notte programmabile
- Animazioni periodiche configurabili
- BMG Bot come mascotte originale
- Animazioni dedicate al cambio dell'ora e alla mezzanotte
- Minigiochi controllabili dallo smartphone
- Aggiornamento firmware manuale e automatico OTA
- Indicazione grafica della perdita di connessione Wi-Fi
- Ripristino delle impostazioni di fabbrica

## Hardware supportato

- **ESP32-C3 SuperMini**
- **GC9A01 240×240 SPI**

### Collegamenti

| GC9A01 | ESP32-C3 SuperMini |
|---|---|
| VCC | 3V3 |
| GND | GND |
| RST | GPIO 0 |
| CS | GPIO 1 |
| DC | GPIO 10 |
| SDA / MOSI | GPIO 3 |
| SCL / SCK | GPIO 4 |
| BL / BLK | 3V3 |

Il pulsante BOOT della ESP32-C3 SuperMini utilizza **GPIO 9**.

### Funzioni del pulsante BOOT

- **Pressione breve:** visualizza per 5 secondi l'indirizzo IP del dispositivo
- **Pressione 5–9 secondi:** avvia la modalità `BMG Clock Setup`
- **Pressione di almeno 10 secondi:** esegue il ripristino delle impostazioni di fabbrica

## Requisiti software

Per compilare il firmware con Arduino IDE sono richiesti:

- **Arduino IDE 2.x**
- **esp32 by Espressif Systems**, versione 3.x recente
- Scheda selezionata: **ESP32C3 Dev Module**
- **GFX Library for Arduino** di Moon On Our Nation

Le altre librerie utilizzate fanno parte del core ESP32:

- `WiFi`
- `WebServer`
- `DNSServer`
- `Preferences`
- `ESPmDNS`
- `Update`
- `HTTPClient`
- `HTTPUpdate`
- `NetworkClientSecure`
- `time`

### Impostazioni consigliate

- **USB CDC On Boot:** Enabled
- **Flash Size:** 4 MB
- **Partition Scheme:** una configurazione che supporti OTA

> Per utilizzare gli aggiornamenti firmware OTA è necessario scegliere una partition scheme con spazio dedicato all'aggiornamento. Le configurazioni `No OTA` non sono compatibili.

## Prima configurazione

Quando non sono presenti credenziali Wi-Fi salvate, BMG Clock crea automaticamente una rete:

```text
BMG Clock Setup
```

Collegandosi alla rete viene aperto il captive portal di configurazione. Se il portale non compare automaticamente, è disponibile all'indirizzo:

```text
http://192.168.4.1
```

Dopo aver salvato SSID e password, il dispositivo si riavvia e si collega alla rete configurata.

Quando mDNS è disponibile, l'interfaccia web può essere raggiunta anche tramite:

```text
http://bmg-clock.local
```

In alternativa, una pressione breve del pulsante BOOT mostra per 5 secondi sul display l'indirizzo IP locale.

## Interfaccia web

L'interfaccia di configurazione è suddivisa in più sezioni.

### Dashboard

Visualizza le principali informazioni sul dispositivo e sullo stato del sistema.

### Wi-Fi

Permette di:

- configurare una nuova rete
- cambiare rete Wi-Fi
- rimuovere le credenziali salvate
- tornare alla modalità di configurazione

### Grafica

Permette di configurare:

- tema grafico
- luminosità software
- visualizzazione dei secondi
- formato 12 / 24 ore
- nome personalizzato fino a 9 caratteri

Temi disponibili:

- **BMG RGB**
- **Cyberpunk**
- **Minimal**
- **Retro**
- **Matrix**
- **Ice Blue**

Quando i secondi vengono disattivati, l'orario viene automaticamente ingrandito e centrato sul display.

## Luminosità

Con il pin `BL` del GC9A01 collegato direttamente a 3,3 V, la retroilluminazione fisica rimane sempre attiva.

La regolazione disponibile nel firmware agisce quindi sull'intensità dei colori mostrati a schermo, riducendo i valori RGB. Questa funzione è particolarmente utile durante l'utilizzo notturno.

Una futura revisione hardware può prevedere il pilotaggio della retroilluminazione tramite PWM.

## Modalità notte

La modalità notte permette di definire:

- attivazione / disattivazione
- orario di inizio
- orario di fine
- luminosità notturna
- attivazione o disattivazione delle animazioni

Sono supportate anche fasce orarie che attraversano la mezzanotte, ad esempio `23:00 → 07:00`.

## Animazioni

Le animazioni possono essere:

- disattivate
- eseguite ogni 5 minuti
- eseguite ogni 10 minuti
- eseguite ogni 15 minuti
- eseguite ogni 30 minuti

Gli effetti possono essere abilitati o disabilitati singolarmente e visualizzati tramite la funzione **Anteprima** direttamente dall'interfaccia web.

Sono inoltre presenti effetti dedicati:

- al cambio dell'ora
- alla mezzanotte
- alla mascotte **BMG Bot**

L'anteprima non modifica le impostazioni salvate e, al termine, il display ritorna automaticamente all'orologio.

## Giochi

BMG Clock include diversi minigiochi visualizzati sul GC9A01 e controllati dallo smartphone tramite l'interfaccia web.

Giochi inclusi:

- **Snake**
- **BMG Maze**
- **Pong**
- **Breakout**
- **BMG Runner**

### Snake

I bordi del display sono attraversabili: uscendo da un lato, il serpente rientra automaticamente dal lato opposto.

### BMG Runner

Include differenti tipi di ostacolo, livelli progressivi e aumento graduale della velocità.

### Breakout

Include livelli successivi, schemi differenti, vite disponibili e difficoltà crescente.

Quando la pagina di controllo di un gioco viene chiusa o la comunicazione con lo smartphone viene interrotta, il dispositivo ritorna automaticamente alla modalità orologio.

## Stato Wi-Fi

Quando il dispositivo è correttamente connesso non viene mostrata alcuna scritta permanente.

In caso di perdita della connessione compare solamente una piccola **icona Wi-Fi rossa barrata**. L'orologio continua a funzionare utilizzando il clock interno dell'ESP32 finché il dispositivo rimane alimentato.

In assenza di un RTC hardware, dopo uno spegnimento completo è necessaria almeno una sincronizzazione di rete per recuperare l'ora corretta.

## Aggiornamenti firmware

BMG Clock v1.0.4 integra il controllo automatico degli aggiornamenti tramite le **GitHub Releases** del repository ufficiale.

Il dispositivo:

1. controlla la release più recente dopo l'avvio
2. confronta la versione disponibile con quella installata
3. segnala la presenza di un nuovo firmware
4. può scaricare e installare direttamente l'aggiornamento OTA

Il controllo viene eseguito inizialmente circa 15 secondi dopo il boot e successivamente ogni 6 ore.

### Modalità disponibili

- **OFF** — nessun controllo automatico
- **Controlla e chiedi conferma** — segnala la nuova versione e richiede conferma
- **Installa automaticamente** — scarica e installa automaticamente una versione più recente

La modalità consigliata è **Controlla e chiedi conferma**.

### Formato delle release

Ogni GitHub Release destinata all'aggiornamento OTA deve contenere un asset chiamato esattamente:

```text
BMG_Clock.bin
```

Esempio:

```text
Tag: v1.0.5
Asset: BMG_Clock.bin
```

La numerazione delle versioni segue il formato:

```text
major.minor.patch
```

Durante l'aggiornamento il display mostra la percentuale di avanzamento. Al termine, il dispositivo si riavvia automaticamente con il nuovo firmware.

L'aggiornamento manuale tramite file `.bin` rimane disponibile come procedura alternativa.

Per la procedura di pubblicazione delle release è disponibile il file:

```text
RELEASE_GUIDE.md
```

## Ripristino di fabbrica

Il factory reset cancella le impostazioni memorizzate e riporta il dispositivo alla configurazione iniziale.

Dopo il ripristino viene nuovamente creata la rete:

```text
BMG Clock Setup
```

Il ripristino può essere avviato dall'interfaccia web oppure tramite pressione prolungata del pulsante BOOT.

## Proprietà intellettuale dei giochi

Il firmware non include personaggi, loghi o risorse grafiche appartenenti a videogiochi commerciali.

**BMG Maze**, **BMG Runner** e **BMG Bot** sono elementi originali del progetto BMG Clock.

## Sicurezza OTA

La versione 1.0.4 utilizza HTTPS per il download delle release GitHub.

In questa fase di sviluppo il client TLS utilizza `setInsecure()` per garantire compatibilità con i redirect GitHub. La connessione è cifrata, ma la verifica crittografica dell'identità del server non rappresenta ancora la configurazione definitiva prevista per una distribuzione commerciale.

Per una versione destinata alla produzione sono consigliati:

- autenticazione dell'interfaccia amministrativa
- verifica crittografica del firmware
- firmware firmato
- recovery dopo aggiornamenti interrotti
- verifica della partition table OTA
- test prolungati su hardware reale

## Novità della versione 1.0.4

- Aggiornamenti automatici tramite GitHub Releases
- Controllo della nuova versione dopo l'avvio e ogni 6 ore
- Modalità OFF, conferma manuale e installazione automatica
- Visualizzazione della versione installata e disponibile
- Pulsante di controllo aggiornamenti
- Download OTA diretto di `BMG_Clock.bin`
- Barra di avanzamento sul display
- Aggiornamento manuale `.bin` mantenuto come fallback

## Note tecniche ESP32-C3

Il Wi-Fi viene inizializzato prima del server HTTP per evitare problemi osservati su alcune configurazioni ESP32-C3 con Arduino-ESP32 3.x.

Il rendering dell'orologio è ottimizzato per evitare il ridisegno completo continuo del display e ridurre il flickering.

## Stato del progetto

BMG Clock è in sviluppo attivo. Prima di una distribuzione commerciale su larga scala sono raccomandati test prolungati relativi a:

- stabilità nel funzionamento continuo
- riconnessione Wi-Fi
- captive portal su Android e iOS
- cambio ora solare / legale
- modalità notte
- aggiornamenti OTA
- recovery dopo interruzione dell'aggiornamento
- minigiochi e controller web

## Autore

**BMG di Buson Mattia Giovanni**

BMG Clock è sviluppato come progetto hardware/software dedicato a ESP32-C3 e GC9A01.

Per le licenze e gli avvisi relativi alle librerie di terze parti consultare:

```text
THIRD_PARTY_NOTICES.md
```
