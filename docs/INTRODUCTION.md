**Introduktion till APR**

# 1 Innehållsförteckning

- 2 Bakgrund
- 3 Översikt kategorier
- 4 Söka plats
  - Namnsök
  - Koordinatsök
- 5 Söka resa
  - Sök resealternativ
  - Visa detaljer
- 6 Visa avgångar och ankomster
  - Visa avgångar
  - Visa ankomster
- 7 Visa fordonspositioner
  - Hämta inom rektangel
  - Hämta position för tur och linje

# 2 Bakgrund

APR (API Planera Resa) började som ett internt API för att möta det ökande behovet av interna förfrågningar av reseplaneringsdata inom Västtrafik. Det byggdes sedan ut till att aggregera data från flera källor, inklusive trängselprognoser och biljettdata. Nu används det av To Go-appen och reseplaneraren på Västtrafiks hemsida. I linje med Västtrafiks policy om öppen data blir nu API:et även publikt.

Det finns ett antal begrepp, termer och strukturer som är bra att känna till för att kunna förstå och fråga efter reseplaneringsdata. Detta dokument utgör grunden för denna förståelse från en utvecklares perspektiv.

# 3 Översikt kategorier

_Resurserna_ i APR är uppdelade i ett antal kategorier som motsvarar de olika frågetyperna. Kategorierna är:

- Söka plats
- Söka resa
- Visa avgångar och ankomster
- Visa fordonspositioner

Dessa förklaras steg för steg nedan. Genomgången är på en generell, konceptuell nivå med enkla exempel för att få en snabb överblick. För den fullständiga dokumentationen av parametrar och svarsobjekt hänvisar vi till Swagger-sidan för API:et och den detaljerade dokumentationen: [APR REST Interface ](REST.md).

# 4 Söka plats

En resa (se kapitel 5) kan börja och sluta på en adress, hållplats eller intressepunkt.

Hållplatser som start- och slutpunkt är den enklaste resesökningen att göra. Varje hållplats har ett unikt id, ett ”GID”, som refererar till exakt en start- eller slutpunkt.

Adresser och intressepunkter har inga GID, men kan användas som start- och slutpunkter via de geografiska koordinater de befinner sig på.

Detta ger två olika scenarion för hur man söker på start- och slutpunkter:

1. Namnet på adressen, hållplatsen eller intressepunkten finns och behöver översättas till antingen en GID eller koordinater (namnsök).
1. Endast koordinater finns och behöver översättas till en GID för närmsta hållplats (koordinatsök).

## Namnsök

I scenariot namnsök görs anrop mot locations/by-text

Söksträngen i anropet matchas mot adresser, hållplatser och intressepunkter med liknande stavning. Faktorer som påverkar relevansen för en matchning är:

- Antal matchande tecken i rätt följd
  - Oberoende av om det är i början eller inuti ett namn.
- Matchning av hela ord i namnet
  - Detta höjer relevansen mer än någon annan faktor.
- Antalet avgångar från hållplatsen
  - En högtrafikerad hållplats får högre relevans.

I svarsobjektet ingår hållplatser, adresser och intressepunkter (se ovan). Dessa används sedan för vidare anrop mot API:et. Om man vill söka efter en resa från dörr-till-dörr ska denna _endpoint_ användas eftersom resultaten även inkluderar adresser.

**Exempel**

Visa de tio första hållplatserna med ”brunn” i namnet:

```
locations/by-text?q=brunn&limit=10
```

## Koordinatsök

För scenariot koordinatsök görs anrop mot locations/by-coordinates

Hållplatser i närheten av koordinaterna returneras, upp till en given maxradie. Närmre hållplatser får högre relevans.

Det är inte rekommenderat att söka på _StopPoint_ (hållplatsläge) eftersom det kan ge oväntade sökresultat. Använd _StopArea_ (hållplatsområde) istället för bättre resultat.

**Exempel**

Visa de tio närmaste hållplatserna för en radie på 500 meter från koordinaterna:

```
locations/by-coordinates?latitude=57.708734&longitude=11.974764&radiusInMeters=500&types=stoparea&limit=10
```

# 5 Söka resa

När start- och slutpunkterna för resan är definierade kan resesökningar utföras, detta sker i två steg.

1. Sök resealternativ mellan start- och slutpunkt
2. Visa detaljer för enskilt resealternativ

## Sök resealternativ

Sökningen specificeras av ett antal parametrar, men det enda som är obligatoriskt är en start- och en slutpunkt. Eftersom en GID är det kortaste och tydligaste sättet att beskriva en punkt, blir det enklaste anropet en sökning mellan två hållplatsområden, till exempel:

```
journeys?originGid=9021014001760000&destinationGid=9021014003980000
```

Detta kommer att ge de bästa resealternativen, med avseende på kortast restid och minst antal byten, från Brunnsparken till Korsvägen med snarast möjliga avgång.

Som standard används alla tillgängliga transportmedel och medellånga gångsträckor, men detta kan justeras tillsammans med många andra parametrar vid behov. Se vår detaljerade API-dokumentation för mer information.

I svarsobjektet för varje resealternativ finns en egenskap som heter detailsReference. Denna sträng används för detaljanropet nedan.

## Visa detaljer

Med detailsReference kan man hämta ut detaljer om ett enskilt resealternativ. Detta är inte nödvändigt för alla scenarion, utan endast om man behöver den ytterligare informationen. Bland detaljerna kan följande ingå:

- Koordinater
- Biljettförslag
- Samtliga hållplatser på resan
- Trängselprognos på hållplatsnivå
- Zoninformation

En detailsReference är bara giltig samma dag som den skapades.

**Exempel**

Visa detaljer för en resa inklusive samtliga hållplatser:

```
journeys/V3eyJUIjpbeyJSIjoiMXw0MjczN3w3fDgwfDIxMDQyMDIyIiwiTyI6MTUsIkQiOjE2fSx7IlIiOiIxfDQyNzE1fDI0fDgwfDIxMDQyMDIyIiwiTyI6NiwiRCI6OX1dfQ/details?includes=servicejourneycalls
```

# 6 Visa avgångar och ankomster

Nästa kategori gäller avgångar och ankomster till och från hållplatser. Vanliga scenarion är att fråga efter alla avgångar från en hållplats eller ankomster till en hållplats.

## Visa avgångar

Det enda som behövs för att visa avgångar är hållplatsens GID (se avsnitt ovan). Man kan även skicka med namnet för ett specifikt hållplatsläge för att filtrera resultatet.

**Exempel**

Visa avgångar från hållplatsläge C2 i Brunnsparken:

```
/stop-areas/9021014001760000/departures?platforms=C2
```

## Visa ankomster

Denna resurs fungerar likadant som ”Visa avgångar”, med skillnaden att linjens startstation visas i stället för linjens slutstation och fordonets ankomsttid visas i stället för fordonets avgångstid.

**Exempel**

Visa ankomster till Göteborgs Central:

```
/stop-areas/9021014008000000/arrivals
```

# 7 Visa fordonspositioner

Den sista kategorin är till för att hämta positioner för fordon i trafik.

## Hämta inom rektangel

Genom att ange koordinaterna för två punkter hämtas alla fordonspositioner inom den rektangel som punkterna definierar (de diagonalt motsatta hörnen anges).

Resultatet innehåller position, riktning och linjeinformation för varje fordon. Detta kan sedan sättas ut på en karta för att få en grafisk översikt på alla aktiva kollektivtrafiksfordon.

**Exempel**

Visa alla fordonspositioner i Kungsbacka tätort:

```
/positions?lowerLeftLat=57.470638&lowerLeftLong=12.044563&upperRightLat=57.523342&upperRightLong=12.106774
```

## Hämta position för tur och linje

Om man vill begränsa resultatet för en enskild tur eller linje går det att lägga till parametrar för detta.

**Exempel**

Visa alla fordonspositioner för linje 2 i Kungsbacka tätort:

```
/positions?lowerLeftLat=57.470638&lowerLeftLong=12.044563&upperRightLat=57.523342&upperRightLong=12.106774&lineDesignations=2
```

Visa alla fordonspositioner för en specifik tur av linje 2 i Kungsbacka tätort (detailsReference är bara giltig samma dag):

```
/positions?lowerLeftLat=57.470638&lowerLeftLong=12.044563&upperRightLat=57.523342&upperRightLong=12.106774& detailsReferences=V3eyJUIjpbeyJSIjoiMXwzOTY4N3w2fDgwfDIyMDgyMDIyIn1dfQ
```
