# Gesto Detektor – Ukazováček + lokální MP3 + Meme overlay

## Co aplikace dělá
- Po načtení požádá o přístup ke kameře (a mikrofonu).
- Pomocí knihovny **MediaPipe Hands** detekuje v reálném čase ruku a jejích 21 klíčových bodů.
- Pokud po dobu **delší než 300 ms** rozpozná gesto „zvednutý ukazováček, ostatní prsty (palec, prostředníček, prsteníček, malíček) skrčené“:
  - zobrazí přes video **meme overlay** s animací fade-in / scale-in,
  - spustí přehrávání lokálního MP3 souboru přes `<audio>` element.

## Než to spustíš

### 1. Vlastní meme obrázek
Aplikace očekává soubor **`meme.png`** ve stejné složce jako `index.html`.

### 2. Vlastní MP3
Aplikace očekává soubor **`track.mp3`** ve stejné složce jako `index.html`.
Pokud chceš jiný název/formát, uprav v HTML:

```html
<audio id="audioPlayer" src="track.mp3" preload="auto"></audio>
```

> Proč ne Spotify? Mobilní prohlížeče blokují programové `play()` v cizím
> (cross-origin) iframu i po kliknutí na stránce. Nativní `<audio>` element
> lze ale spustit programově po jakémkoliv user gesture (kliknutí na
> "Start"), takže ho lze spustit i z callbacku detekce gesta ruky –
> spolehlivě funguje i na mobilu.

## Jak to lokálně spustit

Kvůli bezpečnostním politikám prohlížečů (přístup ke kameře/mikrofonu vyžaduje
zabezpečený kontext) **nelze** stránku jen otevřít dvojklikem jako `file://...`.
Je potřeba ji servovat přes `http://localhost` nebo `https://`.

### Varianta A – VS Code Live Server (nejjednodušší)
1. Otevři složku s `index.html` ve VS Code.
2. Nainstaluj rozšíření **„Live Server“** (autor: Ritwick Dey).
3. Klikni pravým tlačítkem na `index.html` → **„Open with Live Server“**.
4. Prohlížeč se otevře na `http://127.0.0.1:5500/...` – povol kameru a mikrofon.

### Varianta B – Python (bez VS Code)
V terminálu ve složce s `index.html`:

```bash
python3 -m http.server 8000
```

Pak otevři `http://localhost:8000` v prohlížeči.

### Varianta C – Node.js
```bash
npx serve .
```

## Postup používání
1. Po otevření stránky povol přístup ke kameře/mikrofonu.
2. Klikni na tlačítko **„▶ Start (povolit zvuk a kameru)“** – tím prohlížeč
   "odemkne" `<audio>` element pro pozdější programové přehrání.
3. Ukaž do kamery gesto: **zvednutý ukazováček**, ostatní prsty (včetně palce)
   skrčené k dlani.
4. Po cca 300 ms se objeví meme overlay a spustí se `track.mp3` od začátku.

## Poznámky k robustnosti detekce
- Detekce je založená na **poměrech vzdáleností** špiček prstů a jejich
  kloubů od zápěstí (geometrie, ne barva/jas) → odolnější vůči různým
  světelným podmínkám.
- `minDetectionConfidence` a `minTrackingConfidence` jsou nastaveny na 0.7
  pro lepší stabilitu; pokud detekce na tvé kameře nefunguje dobře, zkus
  hodnoty trochu snížit (např. na 0.5).
- Pokud se zvuk nepřehraje, ověř, že jsi klikl na tlačítko **Start** – bez
  toho prohlížeč přehrání `<audio>` elementu blokuje (autoplay policy),
  a zkontroluj, že soubor `track.mp3` skutečně existuje ve stejné složce.
  
