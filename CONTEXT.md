# Kassabon Analyse – context (bijgewerkt 12 nov 2026)
- Aparte app (naar analogie van Hapje/Drankje) om kassabonnen vast te leggen, te controleren en via WhatsApp te versturen.
  Stap 1 van het plan: vastleggen. Stap 3 (prijzen vergelijken) komt later; de data is daar al op ingericht.
- Hosting: https://peterhanselman-star.github.io/kassabon-analyse/ (alleen relatieve paden, geen tracking).
- Eén bestand index.html; manifest.json, apple-touch-icon-v3.png (180), icon-192-v3.png, icon-512-v3.png (witte boodschappentas met bordeauxrood €-schild op #7B1E2B; oude plaatjes blijven staan).

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

## v2 (10 nov 2026, index-v2.html) — marker: const APP_VERSION = '2'
- Alleen de achtergrond van het beginschermplaatje is bordeauxrood (#7B1E2B); bon, regels, teal € en gele loep ongewijzigd. Kleuren in de app zelf (theme_color, CSS) zijn ongewijzigd.
- Nieuwe bestandsnamen vanwege iOS-cache: apple-touch-icon-v2.png, icon-192-v2.png, icon-512-v2.png.

## v3 (10 nov 2026, index-v3.html) — marker: const APP_VERSION = '3'
- Alleen het beginschermplaatje: de kassabon is vervangen door een witte boodschappentas met twee hengsels
  en een bordeauxrood schild met wit €, in dezelfde platte stijl als Hapje/Drankje. Geen loep, geen andere kleuren.
- Nieuwe bestandsnamen vanwege iOS-cache: apple-touch-icon-v3.png, icon-192-v3.png, icon-512-v3.png.
  Kleuren in de app zelf (theme_color, CSS) zijn ongewijzigd.

## v4 (12 nov 2026, index-v4.html = huidige index.html, LOKAAL – nog niet gepubliceerd) — marker: const APP_VERSION = '4'
Aanleiding: Peter las 3 foto's in (2 kassabonnen + pinbon Poelier Jonker, 10-11-2026) en kreeg onzin + een wachtrij
("hierna volgen nog 2 bonnen"). Testfoto's: /workspace/kb-tools/user-bonnen/1_bouten.jpg, 2_pin.jpg, 3_bami.jpg.
- OCR
  - findSlips() snijdt nu ALTIJD bij tot het tekstblok, ook bij één bon per foto (vroeger ging dan de hele foto met
    grote witte randen naar Tesseract). "Hele foto opnieuw lezen" neemt het blok om alle bonnen heen.
  - Doorbraak: hoogte samendrukken (sy 0,45–0,55). Kassabonnen gebruiken een hoge, smalle letter; half zo hoog leest
    Tesseract die bijna foutloos. OCR_VARIANTS = objecten {w (breedte tekstblok px), sy, psm, enh?}:
    {1100,.5,6} {1100,.45,4} {1300,.55,6} {900,.55,4} {1600,1,enh,6} {1060,.6,enh 5/60,6}. Minimaal 4 rondes,
    daarna stoppen als alles klopt (som = totaal, datum, alle regels q×p=t, namen herkenbaar); pinbon na 2 gelijke.
  - combineParses(): alle rondes samen. Winkel/datum/totaal/afronding per meerderheid (datum in de toekomst telt niet);
    per regel (zelfde regeltotaal) de duidelijkste naam (nameScore = aandeel woorden uit kleine Nederlandse
    boodschappen-woordenlijst) en een aantal × prijs dat klopt; daarna de regelset kiezen waarvan som + afronding = totaal.
    Pinbon zodra één ronde een pinbon ziet en geen ronde een kloppende kassabon.
  - Parser: "0, 618" → 0.618; "4. 25" → 4.25; "| X" / "IX" → "1 X"; "10 % 0,55" → 10 × 0,55; "Tota" = totaal;
    AFROND/CASH/BTW-tabel beëindigen de regels; verkeerd gelezen SUBTOTAL (Su…) en regels die gelijk zijn aan de som
    van de regels ervoor tellen niet; code zonder "(" weg ("UN)", "cutk1)").
  - fixName(): kleine leesfouten verbeteren met de woordenlijst (alleen woorden ≥ 5 letters, max. 1–2 letters verschil,
    korte merknamen blijven staan), losse stukken samenvoegen ("Scharrele ieren" → Scharreleieren), "macar. mihoen"
    → "macar./mihoen" als de naam al met / werkt. NB: de bon zelf zegt "GERGRILDE"; de app maakt er "Gegrilde" van.
  - Resultaat Peters 3 foto's (~12 s op de box): alles exact goed, ook bij 70 % / 160 % formaat en 1,2° scheef
    (scheef: alleen "Brmi/nasi/…" i.p.v. "Bami"). Oude testfoto met 3 bonnen op één foto: 4 van 4 regels bon B goed
    (naam bami verminkt), bon A nog zonder regels (bon is daar maar ~430 px breed).
- Nieuwe werkwijze na het inlezen (geen wachtrij meer)
  - Alle gekozen foto's → één scherm "Bon controleren" (of "N bonnen controleren"). buildBatch(): winkelnaam gelijk
    trekken (bewaarde naam gaat voor), ontbrekende datum van andere bon van dezelfde winkel, zelfde winkel + datum →
    één bon (mergeDrafts, bronnen met lineIds/totaal/afronding, dus "Splits weer" werkt na bewaren), pinbon gekoppeld
    aan de bon met hetzelfde bedrag/dezelfde dag en meteen gecontroleerd op hetzelfde scherm (Klopt / Verschil, bedrag
    aanpasbaar, "Deze pinbon hoort niet bij deze bon"). Losse pinbon: eigen blok met "Hoort bij bon op dit scherm?"
    of vergelijken met een bewaarde bon. Andere winkels/dagen: onder elkaar op hetzelfde scherm, met "Deze bon weglaten".
  - Eén knop "✓ Bewaren" (of "✓ Alles bewaren"); "Annuleren" (vraagt bevestiging bij meer bonnen).
  - Scherm als een papieren bon: kleine foto's (tik = groot), Winkel, Datum, regels (omschrijving – loopt door op
    meer regels –, Aantal of Gewicht kg met stuks/kg-knop, Prijs, Totaal), Regels samen, Afronding, Totaal op de bon,
    Eindtotaal, controle, pinbon. "Inhoud verpakking" en vergelijkprijs staan NIET meer op dit scherm (pkgSize/pkgUnit/
    cmp blijven in de gegevens voor stap 3 prijzen vergelijken).
  - Opslag concept: kassabonDraft = { batch, raw } (raw zonder foto's); oude {draft, queue} wordt nog ingelezen.
- Tests: ptest.js 39/39, e2e1.js 243/243 (o.a. nieuw blok 7b "meer foto's tegelijk" + overzicht in de opmaaktest),
  e2e_ocr.js 25/25, e2e_user3.js 25/25 (Peters 3 foto's → 1 bon € 26,40, pinbon Klopt). Voorbeelden:
  /workspace/kb-tools/v4_preview_*.png + v4_preview_tekst.txt.
