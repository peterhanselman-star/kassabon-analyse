# Kassabon Analyse – context (10 nov 2026)
- Aparte app (naar analogie van Hapje/Drankje) om kassabonnen vast te leggen, te controleren en via WhatsApp te versturen.
  Stap 1 van het plan: vastleggen. Stap 3 (prijzen vergelijken) komt later; de data is daar al op ingericht.
- Hosting: https://peterhanselman-star.github.io/kassabon-analyse/ (alleen relatieve paden, geen tracking).
- Eén bestand index.html; manifest.json, apple-touch-icon-v2.png (180), icon-192-v2.png, icon-512-v2.png (witte bon met € en loep op bordeauxrood #7B1E2B; oude teal-plaatjes blijven staan).

## v1 (10 nov 2026, index-v1.html = huidige index.html) — marker: const APP_VERSION = '1'
- Tabs onder: Bonnen (lijst, nieuwste eerst), Nieuw (foto/PDF of zelf invullen), Versturen (per periode), Meer
  (letters, back-up, versie). Tekst standaard 22px, "Letters A− A+" (16/19/22/25/28; localStorage kassabonTextSize).
  Knoppen ≥ 44px, je-vorm, ongedaan maken bij verwijderen/samenvoegen, "Nu vernieuwen" bij nieuwe versie
  (haalt index.html?check=… op en vergelijkt APP_VERSION), installatietip Safari/Chrome.
- Bon: winkel, datum, regels {omschrijving, aantal of kg, prijs per eenheid, totaal, optioneel verpakking + eenheid
  g/kg/ml/l/stuk → vergelijkprijs per 100 g / per liter / per stuk}. Prijzen incl. btw; btw wordt nooit getoond.
  Altijd één lege regel onderaan (telt mee als je hem invult) + "+ Nog een regel". Alles aanpasbaar, ook afronding.
- Zelfde winkel + zelfde datum → automatisch samengevoegd ("samengevoegd uit 2 bonnen"), met "Splits weer" en
  ongedaan maken. Elke bron bewaart lineIds/totaal/afronding. keepApart voorkomt opnieuw samenvoegen.
- Pinbon: herkend aan trefwoorden (Betaling/Kaarthouder/Akkoord/Terminal…); "Dit is een pinbon: controleer tegen totaal"
  → Klopt / Verschil € x. Staat de pinbon op dezelfde foto, dan wordt het gelezen pinbedrag gekozen dat gelijk is aan
  de som van die bonnen (anders het vaakst gelezen bedrag).
- Delen: "Stuur via WhatsApp" per bon (whatsapp:// eerst, dan wa.me) + "Andere app / kopiëren";
  "Alle bonnen versturen" voor deze week / deze maand / vorige maand / eigen periode.
- Back-up: exporteren als JSON; importeren voegt samen op id (of vervangt alles); verkeerd bestand wordt geweigerd.
- Opslag: localStorage kassabonBills, kassabonDraft (concept + wachtrij overleven herladen), kassabonInstallHint,
  kassabonPendingPhotoDelete; foto's in IndexedDB kassabonDB / store photos.

## OCR
- Tesseract.js 5.1.1 (nld+eng) lazy van jsDelivr; pdf.js 4.10.38 legacy (isEvalSupported:false): eerst tekstlaag, anders OCR.
- findSlips(): kolomprojectie van "inkt" (lange verticale én horizontale lijnen = papier/tafelranden eruit) splitst
  een foto in losse bonnen; per bon wordt het grootste aaneengesloten tekstblok genomen (losse vlekken weg).
- Per bon meerdere leesrondes OCR_VARIANTS [[1600,4,25,'6'],[1060,5,60,'6'],[1060,5,25,'4']] met
  achtergrondnormalisatie; beste via scoreParse; vroeg stoppen als regelsom = TOTAL (pinbon: alle rondes).
- Parser = pure functie ReceiptParser.parse(text, knownSuppliers) tussen // ==PARSER-START== en // ==PARSER-END==.
  Patronen "q X p  t", weegregels (kg  €/kg  totaal), naam+prijs, naam op eigen regel met code (UTK1).
  Negeert SUBTOTAL/AFROND(→afronding)/TOTAL(→controle)/CASH/STK/BTW/BEDANKT. Winkel = regel boven het straatadres
  of fuzzy match met bekende winkel; spelling binnen één foto per meerderheid. Datum ontbreekt → van andere bon op
  dezelfde foto, anders bestandsdatum, anders vandaag. q×p ≠ t → ⚠ en bonbedrag aangehouden. Altijd controlescherm.
- Tip in de app: "Fotografeer één bon per foto, recht van boven, goed licht."
- Resultaat testfoto (3 bonnen op één foto, elk maar ~430 px breed): 3 bonnen gevonden (~14 s), winkel Poelier Jonker,
  datum 10-11-2026 en TOTAL 4,25 / 22,15 goed, pinbon 26,40 goed. Artikelregels deels: bon B 3 van 4 regels
  (0,618 kg × 16,00 = 9,89 gelezen als 9,09; Diverse 3,50; Scharreleieren 10 × 0,55 = 5,50; namen soms verminkt),
  bon A geen regels → handmatig aanvullen. Scherpe bon / PDF met tekstlaag: 100% goed.

## Tests (/workspace/kb-tools, server op poort 8766)
- ptest.js parser 25/25; e2e1.js 214/214 (invoer, samenvoegen/splitsen, pinbon, delen, perioden, back-up, undo,
  lettergrootte, geen horizontale overloop op 375/390 bij alle 5 groottes, knoppen ≥44px); e2e_ocr.js 30/30.
- Bron: kb-tools/src/p1.html p2.html p3.js p4.js p5.js + src/parser.js → build.py → index.html.

## v2 (10 nov 2026, index-v2.html = huidige index.html) — marker: const APP_VERSION = '2'
- Alleen de achtergrond van het beginschermplaatje is bordeauxrood (#7B1E2B); bon, regels, teal € en gele loep ongewijzigd. Kleuren in de app zelf (theme_color, CSS) zijn ongewijzigd.
- Nieuwe bestandsnamen vanwege iOS-cache: apple-touch-icon-v2.png, icon-192-v2.png, icon-512-v2.png.
