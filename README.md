# BMG Clock V1.0.3

Firmware per **ESP32-C3 SuperMini + display rotondo GC9A01 240x240**.
Branding **BMG di Buson Mattia Giovanni**.

## Collegamenti usati

| GC9A01 | ESP32-C3 SuperMini |
|---|---|
| VCC | 3V3 |
| GND | GND |
| RST | GPIO 0 |
| CS | GPIO 1 |
| DC | GPIO 10 |
| SDA / MOSI | GPIO 3 |
| SCL / SCK | GPIO 4 |
| BL / BLK (se presente) | 3V3 |

Pulsante BOOT della SuperMini: **GPIO 9**.

- pressione breve: mostra per 5 secondi l’indirizzo IP da aprire nel browser
- pressione 5–9 s: apre `BMG Clock Setup`
- pressione >=10 s: ripristino di fabbrica

## Requisiti Arduino IDE

1. Pacchetto schede: **esp32 by Espressif Systems**, versione 3.x recente.
2. Scheda: **ESP32C3 Dev Module**.
3. Libreria esterna: **GFX Library for Arduino** (Moon On Our Nation).
4. Le altre librerie usate (`WiFi`, `WebServer`, `DNSServer`, `Preferences`, `ESPmDNS`, `Update`, `time`) fanno parte del core ESP32.

Impostazioni tipiche:

- USB CDC On Boot: Enabled
- Flash Size: 4 MB
- Partition Scheme: una partizione che supporti OTA

> Per usare gli aggiornamenti firmware dal browser è indispensabile scegliere una partition scheme con spazio OTA.

## Prima accensione

Se non esiste una rete salvata, BMG Clock crea un access point aperto chiamato:

`BMG Clock Setup`

Il captive portal dovrebbe aprirsi automaticamente. In alternativa visitare `192.168.4.1`.

Dopo il salvataggio del Wi-Fi il dispositivo si riavvia. Nella rete locale è raggiungibile, quando mDNS è supportato dal router/telefono, anche come:

`http://bmg-clock.local`

## Menu web

- **Dashboard** – stato rapido
- **Wi-Fi** – scelta/cambio rete e rimozione credenziali
- **Grafica** – temi, luminosità software, secondi, 12/24 h, nome fino a 9 caratteri
- **Modalità notte** – fascia oraria, luminosità, gestione animazioni
- **Animazioni** – intervallo OFF/5/10/15/30 min, selezione effetti, anteprima sul display, cambio ora e mezzanotte
- **Giochi** – Snake, BMG Maze, Pong, Breakout, BMG Runner con controller dal telefono
- **Sistema** – diagnostica, riavvio, factory reset, firmware OTA

## Temi inclusi

- BMG RGB
- Cyberpunk
- Minimal
- Retro
- Matrix
- Ice Blue

## Luminosità

Con il pin BL del GC9A01 collegato direttamente a 3,3 V non è possibile regolare via firmware la vera retroilluminazione. La voce "luminosità" scala i valori RGB verso colori più scuri. È utile soprattutto di notte, ma il backlight resta fisicamente acceso.


## Wi-Fi offline

Quando il Wi-Fi è connesso non viene mostrata alcuna scritta. Se cade la connessione compare solo una piccola icona Wi-Fi rossa barrata. L'orologio continua a usare il clock interno finché l'ESP32 resta alimentato.


## OTA e sicurezza

La pagina Sistema accetta file `.bin` ed `Update` del core ESP32 esegue la validazione tecnica dell'immagine prima di installarla. Questa V1 non implementa firma crittografica del firmware né autenticazione della pagina web.

## Migliorie V1.0.3

- Corretto BMG Runner: durante il primo salto non rimangono più residui/rettangoli del BMG Bot sospesi sul display.
- Nella pagina **Animazioni** ogni effetto dispone del pulsante **ANTEPRIMA**; sono disponibili anche anteprima del cambio ora e della mezzanotte. L’anteprima non modifica le impostazioni salvate e al termine torna automaticamente all’orologio.
- Pressione breve del tasto **BOOT**: viene mostrato per 5 secondi l’indirizzo IP del dispositivo; poi il display torna automaticamente all’orologio. In modalità setup viene mostrato l’IP dell’access point.
- Durante i messaggi del pulsante BOOT il clock non ridisegna sopra alla schermata informativa.

## Migliorie V1.0.2

- Con i secondi disattivati, l'orario viene ingrandito e spostato al centro.
- Corretto il vuoto nelle tacche in alto a destra: l'icona Wi-Fi non cancella più il frame.
- Dopo factory reset / assenza di Wi-Fi salvato, la schermata `BMG Clock Setup` resta visibile anche se l'RTC conserva una vecchia ora.
- Snake usa bordi passanti: uscendo a destra si rientra a sinistra, e viceversa anche in verticale.
- BMG Runner include ostacoli diversi (casse, spuntoni, torri, droni e doppi ostacoli), livelli e aumento progressivo della velocità. I droni si evitano con la freccia GIÙ.
- Breakout ora ha livelli successivi, layout differenti, 3 vite e velocità crescente; completare un livello non provoca più Game Over.

## Nota sul prodotto

Questa release va considerata **V1 di sviluppo / pre-produzione**.


## Nota ESP32-C3 / WebServer
Dalla versione firmware 1.0.1 il Wi-Fi viene inizializzato prima del server HTTP.
Questo evita il crash `assert failed: xQueueSemaphoreTake queue.c:1709 (( pxQueue ))` osservato su alcune configurazioni ESP32-C3 con Arduino-ESP32 3.x.
