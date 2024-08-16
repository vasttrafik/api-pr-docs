**APR REST Interface**

Version 4.

# 1 Innehållsförteckning

- 2 Introduktion
- 3 Generell struktur
  - 3.1 Svarsformat
  - 3.2 Detaljanrop
  - 3.3 Enums
  - 3.4 Koordinater
  - 3.5 Datum
- 4 Åtkomst och autentisering
- 5 Versionshantering
- 6 Realtidsdata
  - 6.1 Störningsmeddelanden
  - 6.2 Fordonsrelaterat
    - 6.2.1 Planerad och beräknad tid
- 7 Tjänster för reseförslag
  - 7.1 Journeys
    - 7.1.1 Start, slut och via för resan
    - 7.1.2 Tidpunkt för resan
    - 7.1.3 Transportmedel
    - 7.1.4 Gångsträckor
    - 7.1.5 Cykel- och bilsträckor
    - 7.1.6 Pendelparkeringar
    - 7.1.7 Gång till närliggande hållplats
    - 7.1.8 Bytestider
    - 7.1.9 Paginering
  - 7.2 Journeys/{detailsReference}/details
    - 7.2.1 Biljettförslag
    - 7.2.2 Giltiga zoner i biljettförslag
    - 7.2.3 Resvägens koordinater
    - 7.2.4 ServiceJourney:ns koordinater
    - 7.2.5 Samtliga hållplatser för ServiceJourney:s
    - 7.2.6 Länkar
    - 7.2.7 Trängselprognos
  - 7.3 Journeys/reconstruct
  - 7.4 Journeys/valid-time-interval
- 8 Tjänster för hållplatser och koordinater
  - 8.1 Locations/by-text
  - 8.2 Locations/by-coordinates
    - 8.2.1 StopPoint i svaret
- 9 Tjänst för fordonspositionering
  - 9.1 Positions
    - 9.1.1 Filtrera på enskilda fordon
    - 9.1.2 Filtrera på enskilda linjer
- 10 Tjänster för ankomster och avgångar
  - 10.1 Stop-areas/{stopAreaGid}/departures
    - 10.1.1 Filtrera på hållplatsläge
    - 10.1.2 Filtrera på slutdestination
    - 10.1.3 Inställda avgångar
  - 10.2 Stop-areas/{stopAreaGid}/arrivals
  - 10.3 Stop-areas/{stopAreaGid}/departures/{detailsReference}/details
    - 10.3.1 ServiceJourney:ns koordinater
    - 10.3.2 Samtliga hållplatser för ServiceJourney:s
    - 10.3.3 Trängselprognos
  - 10.4 Stop-areas/{stopAreaGid}/arrivals/{detailsReference}/details
  - 10.5 Stop-points/{stopPointGid}/departures
  - 10.6 Stop-points/{stopPointGid}/arrivals
  - 10.7 Stop-points/{stopPointGid}/departures/{detailsReference}/details
  - 10.8 Stop-points/{stopPointGid}/arrivals/{detailsReference}/details
  - 10.9 Stop-areas/
- 11 Trängselprognoser
  - 11.1 Beräkning av trängsel
    - 11.1.1 Trängselnivåer
    - 11.1.2 Trängsel för StopPoint
    - 11.1. 3 Trängsel för TripLegs
    - 11.1.4 Trängsel för Journey / Departure
- 12 Språkval
- 13 Retry policy
- 14 Biljettköp
- 15 Appendix
  - 15.1 Termer och förklaringar
  - 15.2 Svarsobjekt
    - 15.2.1 Svarsobjekt för en resa
    - 15.2.2 TripLegs och ConnectionLinks

# 2 Introduktion

Västtrafiks APR (API Planera Resa) är ett REST interface för reseplanering inom kollektivtrafiken.
API:et är uppdelat i följande tjänster och resurser:

- Journeys
  - /
  - /reconstruct
  - /details
  - /valid-time-interval
- Locations
  - /by-text
  - /by-coordinates
- Positions
  - /
- Stop-Areas
  - /departures
  - /departures/details
  - /arrivals
  - /arrivals/details

De facktermer och begrepp som är specifika för APR markeras i _kursiv_ stil i den löpande texten och
förklaras i appendix (15.1). Namn på parametrar, objekt och deras tillhörande egenskaper skrivs ut i
`monospace`-format. Välkända förkortningar och facktermer för API:er markeras inte i texten.

# 3 Generell struktur

## 3.1 Svarsformat

Alla svar är i JSON-format.

## 3.2 Detaljanrop

Frågor mot details-endpoints tar en `detailsReference`-parameter som följer med i svaret från
tillhörande endpoint. Till exempel returneras `detailsReference` i `journeys` (se kapitel 7.1),
som sedan används i `journeys/{detailsReference}/details` (se kapitel 7.2).

En `detailsReference` innehåller specifik trafik- och ruttdata och är bara giltigt inom samma
_trafikdygn_ som den skapades.

## 3.3 Enums

Enums skickas i utskriven sträng-form baserat på namn. Om en array av enums ska skickas med i ett
anrop upprepas parametern en gång för varje enum, till exempel `transportModes`:

```
journeys?originGid=9021014001760000&destinationGid=9021014003980000&limit=10&transportModes=tram&transportModes=bus&transportModes=train
```

## 3.4 Koordinater

Koordinater anges enligt WGS84-systemet såsom grader inom intervallen:

- -90 till 90 för latitud
- -180 till 180 för longitud

## 3.5 Datum

Alla datum anges i RFC 3339-format.

Exempel:

- 2022-06-14T15:30:00+02:00
- 2022-06-14T13:30:00Z

# 4 Åtkomst och autentisering

Bas-URL för API och token-server ges tillsammans med inloggningsuppgifter efter registrering på
Västtrafiks Utvecklarportal:

https://developer.vasttrafik.se

Bearer-token autentisering används för samtliga anrop:

https://developer.vasttrafik.se/guides/oauth2

# 5 Versionshantering

Större uppdateringar av API:et, som innebär att affärslogik kan sluta att fungera, lanseras via ett nytt
versionsnummer. Funktionalitetsändringar, bugg- och säkerhetsfixar där 100% bakåtkompabilitet
bibehålles kan appliceras på en existerande version utan förvarning.

Versionsnumret finns i den sektion av URL:en som är omedelbart innan namnet på endpoint:en, och
består av bokstaven ”v” plus versionsnummer, till exempel: ”.../v4/journeys?...”

# 6 Realtidsdata

Realtidsdata inkluderar störningsmeddelanden, förseningar och annan information för:

- _Linjer_
- _Hållplatser_
- _Turer_

## 6.1 Störningsmeddelanden

Störningsmeddelanden kan förbli synliga under en begränsad tid eller tills vidare. Maximal livslängd
är ett _trafikdygn._

I svaren från APR syns de som meddelanden under `notes`.

## 6.2 Fordonsrelaterat

När ett fordon registreras för en _serviceJourney_ börjar det rapportera realtidsdata.

Realtidsdata från fordon inkluderar:

- Förseningar på aktuell _serviceJourney_
- Handikappanpassning för fordonet

Eftersom data är kopplad till en specifik _serviceJourney_ kan den inte visas innan det designerade
fordonet är registrerat för turen. Detta görs oftast i samband med att turen påbörjas. Data kommer
sedan att försvinna när _serviceJourney_:n är avslutad och fordonet avregistrerat. Detta innebär att
retroaktiva sökningar kan returnera annorlunda data än motsvarande sökningar när _serviceJourney_:n
fortfarande var aktiv.

Inställda turer är också kopplat till _serviceJourney_ , men är inte beroende av fordonet. Därför visas
turerna även fortsättningsvis som inställda efter fordonets avregistrering på turen.

### 6.2.1 Planerad och beräknad tid

Alla ankomster och avgångar innehåller planerade tider. Där ingår ”planned” i namnet, såsom:

- `plannedTime`
- `plannedArrivalTime`
- `plannedDepartureTime`

Om det finns realtidsdata för turen skickas denna anpassade tid med i svaret. Dessa tidsangivelser
innehåller ”estimated” i namnet och motsvarar i övrigt namnen ovan:

- `estimatedTime`
- `estimatedArrivalTime`
- `estimatedDepartureTime`

När det finns realtidsdata bör denna tid användas framför den planerade. För detta finns det fält som
visar beräknad tid, om den finns, annars planerad tid. Dessa tidsangivelser innehåller
”estimatedOtherwisePlanned” i namnet, såsom:

- `estimatedOtherwisePlannedTime`
- `estimatedOtherwisePlannedArrivalTime`
- `estimatedOtherwisePlannedDepartureTime`

# 7 Tjänster för reseförslag

## 7.1 Journeys

Denna endpoint anropas för att söka resor mellan start- och slutpunkt.

Reseförslagen för denna tjänst optimeras på bl.a. kortast restid och minst antal byten, men även
andra faktorer. Resultaten kan skiljas åt väsentligt beroende på hur långa gångsträckor som får
användas, hur kort marginal som tillåts mellan byten, vilka _TransportMode_:s som önskas, om endast
direktanslutningar får användas mm. De flesta parametrar är självförklarande, dessa introduceras
inte här. Övriga parametrar beskrivs nedan.

### 7.1.1 Start, slut och via för resan

Journeys-tjänsten beräknar en _Journey_ från en _Origin_ till en _Destination_. Det är också möjligt att
ange en _StopArea_ som en via-parameter för att tvinga _Journey_:n att gå via den.

### 7.1.2 Tidpunkt för resan

Om ingen tidpunkt anges hämtas reseförslag med så tidig avgång som möjligt. Om en tidpunkt
skickas med i `dateTime`-parametern måste det följa RFC3339-formatet (se 3.5) och vara inom det
giltiga tidsintervallet (se 7.4).

Standardinställningen är att `dateTime`-parametern anger önskad avgångstid. Ska den istället ange
ankomsttid sätts `dateTimeRelatesTo`-parametern till `arrival`.

### 7.1.3 Transportmedel

Standardinställningen är att alla transportmedel får användas i sökningen. Antalet tillåtna
transportmedel går att begränsa genom att använda `transportModes`- och
`transportSubModes`-parametrarna.

### 7.1.4 Gångsträckor

Om _Origin_ och/eller _Destination_ är av typen _Address_ går det att ställa in min- och maxparametrar
för gångsträckan till och från _Address:_ en. Detta kan påverka resultatet väsentligt eftersom det
möjliggör nya hållplatser och linjer.

_7.1.4.1 Parameternamn_
Parameternamnet för gångsträckor från en _Origin Address_ till den första _StopPoint_:en i det första
_TripLeg:_ et är: `originWalk`

Parameternamnet för gångsträckor från den sista _StopPoint_:en i det sista _TripLeg:_ et till en
_Destination Address_ är: `destWalk`

_7.1.4.2 Parameterns struktur och standardvärden_
Parametern är i strängform och består av tre delar:

1. Tillåt gångsträckor (bool).
   Möjliga värden:
   0 = Nej
   1 = Ja
2. Minsta sträcka i meter (int).
   Möjliga värden: >=0
3. Maximal sträcka i meter (int).
   Möjliga värden: >=0

De tre delarna sammanfogas sedan med kommatecken. Till exempel:

- Tillåt gångsträckor mellan 0 och 3000 meter: ”1,0,3000”
- Tillåt gångsträckor mellan 500 och 2000 meter: ”1,500,2000”

Om min- och maxsträckorna saknas används standardvärden. Till exempel:

- Tillåt inte gångsträckor: ”0”
- Tillåt gångsträckor enligt standardinställningarna: ”1”

Standardvärdet är att tillåta gångsträckor upp till 2000 meter:

- ”1,0,2000”

_7.1.4.3 DepartureAccessLink och ArrivalAccessLink_
Gångsträckorna till första och från sista _StopPoint_:en visas i reseförslaget under egenskaperna:

`departureAccessLink` – gångsträcka från _Address_ till första _StopPoint_:en

`arrivalAccessLink` – gångsträcka från sista _StopPoint_:en till _Address_

### 7.1.5 Cykel- och bilsträckor

Motsvarande parametrar finns även för cykel- och bilsträckor. De har samma struktur och funktion
som parametrarna för gångsträckor med följande skillnader:

- De har andra standardvärden
- De resulterande reseförslagen har högre medelhastigheter för tidsberäkningen av sträckan.

Standardvärdet för cykelsträckor är: ”0,0,3000”
Standardvärdet för bilsträckor är: ”0,0,5000”

Parameternamnen är: `originBike, originCar, destBike, destCar`

_7.1.5.1 Kombinationer av sträckor_
Om flera olika typer av gång-, cykel- och bil-sträckor tillåts samtidigt kommer API:et beräkna resor
för alla kombinationer och presentera bästa resealternativen, med avseende på kortast restid och
minst antal byten.

_7.1.5.2 Endast cykel_
API:et stödjer ett specialfall där hela resan utgörs av en enda cykelsträcka. Parametern har samma
struktur och funktion som cykelsträckor (7.1.5) med följande skillnad:

- Den totala sträckan för resan måste vara mindre än cykelsträckans maxvärde.

Standardvärdet är: ”0,0,25000”

Parameternamnet är: `totalBike`

Om denna parameter används, och resan är genomförbar, kommer det första reseförslaget att baseras
på en _destinationLink_ med `bike` som _transportMode_. Resterande reseförslag kan dock bygga på
alternativa _transportMode_:s.

### 7.1.6 Pendelparkeringar

För pendelresor finns det möjlighet att söka på resor med bil till och från pendelparkeringar och
därutöver med kollektivtrafik. Parametrarna har samma struktur och funktion som parametrarna för
bilsträckor (7.1.5)

Standardvärdet är: ”0,2000,50000”

Parameternamnen är: `originPark, destPark`

### 7.1.7 Gång till närliggande hållplats

Standardinställningen är att _DepartureAccessLink_ och _ArrivalAccessLink_ endast används för resor
till och från _Address_. Det går att ändra denna inställning så att reseförslagen tillåter gångsträckor till
och från _Origin_ och _Destination_ även när de inte är av typen _Address._ Detta kan leda till fler möjliga
resor, vilket i sin tur kan leda till bättre reseförslag.

Parameternamnet för denna inställning är: `includeNearbyStopAreas`

Standardvärdet är: `false`

Sätt parametern till `true` för att aktivera funktionen.

Inställningarna för gångsträckorna styrs av `originWalk` och `destWalk` enligt ovan (se 7.1.4).

### 7.1.8 Bytestider

Beräknad bytestid beror på hållplatsområdets storlek och utformning. De beräknade bytestiderna går
att ersätta med ett manuellt värde. Då används det manuella värdet för alla byten i reseförslagen. Om
detta värde anges kommer bara reseförslag returneras som har byten med en marginal som är större
än eller lika med angivet värde.

Parameternamnet är: `interchangeDurationInMinutes`

_7.1.8.1 Realtidsresor_
Om realtidsdata ökar tillgänglig bytestid mellan två anslutningar kan nya reseförslag returneras, som
skulle vara omöjliga under ordinarie trafik.

Om realtidsdata minskar tillgänglig bytestid mellan två anslutningar får reseförslaget en `note` med
”Risk att missa bytet.”

### 7.1.9 Paginering

Antalet resultat i en sökning begränsas genom följande parameter: `limit`

I varje svar finns ett `links`-objekt med länkar till föregående, nuvarande och nästa sida. Dessa
länkar används för att hämta paginerade resultat.

Länkarna är repetitioner av den ursprungliga frågan med en extra pagineringsreferens i följande
parameter: `paginationReference`

## 7.2 Journeys/{detailsReference}/details

Denna endpoint används för att hämta ut detaljer för ett reseförslag. Se 3.2 för information om
`detailsReference`. Följande tillval kan hämtas ur detaljanropet:

### 7.2.1 Biljettförslag

Aktiveras med parameter: `includes=ticketsuggestions`

Detta tillval berikar svaret med en lista på biljettförslag. Vissa av biljettförslagen har speciella regler
för giltighet som hanteras av Västtrafiks interna kanaler. Angivna priser kan också skilja sig mot
slutgiltigt pris i säljkanalen. De bör därför ej användas som förslag på biljettköp av externa
konsumenter. Se 14 för mera information om biljettköp för externa konsumenter.

_7.2.1.1 Filtrera på resenärskategori_
Det går att begränsa biljettförslagen till en specifik resenärskategori (`travellerCategory`).

Parameternamnet är: `travellerCategories`

Möjliga värden: `adult, youth`

_7.2.1.2 Filtrera på biljettyp_
Det går att begränsa biljettförslagen till specifika `productType`.

Denna funktion används bara inom vissa typer av biljettköpsflöden (se 14).

Parameternamnet är: `productTypes`

_7.2.1.3 Filtrera på försäljningskanal_
Det går att begränsa biljettförslagen till specifika `saleChannels`.

Denna funktion används bara inom vissa typer av biljettköpsflöden (se 14).

Parameternamnet är: `channelIds`

### 7.2.2 Giltiga zoner i biljettförslag

Aktiveras med parameter: `includes=validZones`

Detta tillval utökar biljettförslagen med listor över giltiga zoner för varje biljett och används bara
inom vissa typer av biljettköpsflöden (se 14).

### 7.2.3 Resvägens koordinater

Aktiveras med parameter: `includes=triplegcoordinates`

Detta tillval berikar svaret med koordinater för varje _TripLeg_ längs resans färdväg. Kan användas
för att representera resan med en polyline på en karta.

### 7.2.4 ServiceJourney:ns koordinater

Aktiveras med parameter: `includes=serviceourneycoordinates`

Detta tillval berikar svaret med listor med koordinater för alla _ServiceJourney_:s i _Journey_:n.
Koordinaterna visar hela _ServiceJourney_:ns sträcka, även de delar som inte ingår i reseförslaget.

### 7.2.5 Samtliga hållplatser för ServiceJourney:s

Aktiveras med parameter: `includes=servicejourneycalls`

Detta tillval berikar svaret med samtliga hållplatser för alla _ServiceJourney_:s i _Journey_:n, även de
som inte ingår i reseförslaget.

### 7.2.6 Länkar

Aktiveras med parameter: `includes=links`

Detta tillval inkluderar `departureAccessLink, arrivalAccessLink` och
`destinationLink` i svaret, om de ingick i det ursprungliga reseförslaget.

### 7.2.7 Trängselprognos

Aktiveras med parameter: `includes=occupancy`

Detta tillval berikar svarets hållplatser med trängselprognoser (se kap. 11).

## 7.3 Journeys/reconstruct

I varje svar från Journeys ingår en `reconstructionReference`. Referensen kan anges i denna
endpoint för att göra om samma sökning igen. Skillnader kan uppstå om realtidsdata har ändrats.
Max ett reseförslag returneras i svaret.

## 7.4 Journeys/valid-time-interval

Mängden historisk och framtida resedata är begränsad. Denna endpoint anger hur långt tillbaka och
hur långt framåt data existerar. API-frågor utanför detta tidsintervall kommer att generera ett
felmeddelande.

# 8 Tjänster för hållplatser och koordinater

## 8.1 Locations/by-text

Denna endpoint söker efter _Address_:er, _StopArea_:s, _MetaStation_:s eller _PointOfInterest_.

Faktorer som påverkar relevansen för en matchning är:

- Antal matchande tecken i rätt följd
  o Oberoende av om det är i början eller inuti ett namn.
- Matchning av hela ord i namnet
  o Detta höjer relevansen mer än någon annan faktor.
- Antalet avgångar från hållplatsen
  o En högtrafikerad hållplats får högre relevans.

## 8.2 Locations/by-coordinates

Denna endpoint tar latitud och longitud som parametrar och returnerar _StopPoint_:s i närheten av de
givna koordinaterna. Närmre hållplatser får högre relevans. Önskad maxradie anges med parametern
radiusInMeters.

### 8.2.1 StopPoint i svaret

API:et returnerar _StopPoint_:s i svaret som standard. Dessa bör användas med försiktighet. Frågor
mot Journeys-tjänsten som använder _StopPoint:_ s som _Origin_ eller _Destination_ kan få oväntade
resultat. Detta på grund av att reseförslaget då begränsas till ett specifikt hållplatsläge på hållplatsen.
Använd överliggande _StopArea_ istället.

# 9 Tjänst för fordonspositionering

## 9.1 Positions

Denna endpoint tar latitud och longitud för en bounding box (rektangel) som parametrar och
returnerar position, riktning och linjeinformation för alla fordon som befinner sig inom
koordinaterna (de diagonalt motsatta hörnen för rektangeln anges).

Positionerna är ungefärliga och bygger på tiden som gått från senaste hållplats, som fordonet stannat vid, och sträckan till nästa planerade hållplats. En genomsnittlig hastighet för fordonet används längs den planerade rutten och tillsammans med tidpunkten för anropet resulterar det i en beräknad position. Beräkningen förutsätter ett normalt körtempo, som under normala trafikförhållanden stämmer relativt väl. Under trafikstörningar kan större eller mindre avvikelser förekomma.

### 9.1.1 Filtrera på enskilda fordon

Det går att filtrera resultatet på enskilda fordon genom att ange `detailsReference`-värdet från
APR:s tjänster som parameter.

Parameternamnet är: `detailsReferences`

Parametern går att skicka med flera gånger i frågan, en gång för varje `detailsReference`-
värde.

### 9.1.2 Filtrera på enskilda linjer

Det går att filtrera resultatet på enskilda linjer genom att ange `line.name`-värdet från APR:s
tjänster som parameter.

Parameternamnet är: `lineDesignations`

Parametern går att skicka med flera gånger i frågan, en gång för varje `line.name`-värde.

# 10 Tjänster för ankomster och avgångar

## 10.1 Stop-areas/{stopAreaGid}/departures

Denna endpoint hämtar avgångar för en _StopArea_. Om det finns realtidsinformation om avgången
bör den visas för en slutanvändare i stället för den planerade avgångstiden (se 6.2.1).

### 10.1.1 Filtrera på hållplatsläge

Det går att filtrera avgångarna på hållplatsläge, men då används inte _GID_:en för hållplatsläget utan
namnet, till exempel ”C”. Hållplatslägets namn återfinns under `stopPoint.platform` i svaren
från APR. Se 10.5 för att visa avgångar från hållplatsläge med _GID_.

Parameternamnet är: `platforms`

### 10.1.2 Filtrera på slutdestination

Det går att filtrera avgångarna på _ServiceJourney_:ns ändhållplats. Ändhållplatsen är den sista _StopArea_:n på turen, vilket kan hämtas genom att göra ett anrop till details-endpoint:en (se 10.3) med parametern: `includes=servicejourneycalls`.

Parameternamnet är: `directionGid`

### 10.1.3 Inställda avgångar

Observera att realtidsdata även kan innebära inställda avgångar. En avgång kan vara helt eller delvis
inställd, där delvis innebär att en eller flera, men inte alla, hållplatser på turen är inställda.

`isCancelled`

`isPartCancelled`

## 10.2 Stop-areas/{stopAreaGid}/arrivals

Denna endpoint fungerar som 10.1 men visar ankomster istället.

## 10.3 Stop-areas/{stopAreaGid}/departures/{detailsReference}/details

Denna endpoint används för att hämta ut ytterligare detaljer för en avgång. Se 3.2 för information
om `detailsReference`. Följande tillval kan hämtas ur detaljanropet:

### 10.3.1 ServiceJourney:ns koordinater

Aktiveras med parameter: `includes=servicejourneycoordinates`

Detta tillval berikar svaret med koordinater för alla _ServiceJourney_:s i avgången. Koordinaterna
visar hela _ServiceJourney_:ns sträcka, även de delar som kommer innan avgångshållplatsen.

### 10.3.2 Samtliga hållplatser för ServiceJourney:s

Aktiveras med parameter: `includes=servicejourneycalls`

Detta tillval berikar svaret med samtliga hållplatser för alla _ServiceJourney_:s i avgången, även de
som kommer innan avgångshållplatsen.

### 10.3.3 Trängselprognos

Aktiveras med parameter: `includes=occupancy`

Detta tillval berikar svarets hållplatser med trängselprognoser (se 11). Kan bara aktiveras
tillsammans med `includes=servicejourneycalls`

## 10.4 Stop-areas/{stopAreaGid}/arrivals/{detailsReference}/details

Denna endpoint fungerar som 10.3 för ankomster.

## 10.5 Stop-points/{stopPointGid}/departures

Denna endpoint fungerar som 10.1 för _StopPoint_:s.

## 10.6 Stop-points/{stopPointGid}/arrivals

Denna endpoint fungerar som 10.2 för _StopPoint_:s.

## 10.7 Stop-points/{stopPointGid}/departures/{detailsReference}/details

Denna endpoint fungerar som 10.3 för _StopPoint_:s.

## 10.8 Stop-points/{stopPointGid}/arrivals/{detailsReference}/details

Denna endpoint fungerar som 10.4 för _StopPoint_:s.

## 10.9 Stop-areas/

Denna endpoint returnerar alla stop-areas i en lista.

# 11 Trängselprognoser

Västtrafik samlar löpande in data om beläggning på fordon i trafik. Detta används sedan till att
skapa trängselprognoser för de olika turerna på dygnet.

Följande endpoints inkluderar trängseldata med `includeOccupancy`-parametern:

- Journeys
- Stop-areas/{stopAreaGid}/departures
- Stop-points/{stopPointGid}/departures

Följande endpoints kan inkludera trängseldata om det anges i `includes`-parametern.

- Journeys/{detailsReference}/details
- Stop-areas/{stopAreaGid}/departures/{detailsReference}/details
- Stop-points/{stopPointGid}/departures/{detailsReference}/details

Trängselprognoser är för tillfället inte tillgängliga för färjor.

Egenskapen på svarsobjekten som innehåller trängseldata heter alltid `occupancy`.

## 11.1 Beräkning av trängsel

### 11.1.1 Trängselnivåer

Trängsel klassas i följande nivåer:

- Low
- Medium
- High
- Incomplete
- Missing
- NotPublicTransport

### 11.1.2 Trängsel för StopPoint

Beräkningen börjar alltid på _StopPoint-_ nivå. Lägsta trängselnivå är `low` och högsta är `high`. Om
det saknas data för en hållplats används `missing`.

### 11.1. 3 Trängsel för TripLegs

Ett _TripLeg_ får alltid den högsta trängselnivån av de trafikerade hållplatserna. Om en eller flera
hållplatser är `missing` blir resultatet `incomplete`, undantaget är om en eller flera av
hållplatserna är `high`, då blir resultatet alltid `high`.

Om samtliga hållplatser är `missing` blir resultatet `missing`.
Om samtliga hållplatser är `notPublicTransport` blir resultatet `notPublicTransport`.

### 11.1.4 Trängsel för Journey / Departure

`journey`- och `departure`-objekten får alltid den högsta trängselnivån av de underliggande
`tripLeg`-objekten, på samma sätt som med hållplatserna i 11.1.3.

# 12 Språkval

Följande tjänster stödjer lokalisering:

- Samtliga endpoints i Journeys
- Samtliga endpoints i Stop-Areas
- Samtliga endpoints i Positions

När lokalisering används i en tjänst kommer meddelanden under `notes` att returneras i det valda
språket, där språkstöd finns. Språkstödet byggs ut kontinuerligt. Även `maneuverDescription`
översätts till valt språk. Språkvalet görs genom att skicka med önskat språk i Accept-Language-
headern:

https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Accept-Language

Språkvalet följer Lookup-proceduren i RFC4667:

https://www.rfc-editor.org/rfc/rfc4647#section-3.

Tillgängliga språk är svenska och engelska. APR accepterar subTags (till exempel sv-SE, en-US)
men tar inte med dem i parsningen, utan tolkar alltid språkvalet som ett av följande val:

- sv
- en

Språksträngen är ej skiftlägeskänslig.

Om språkval saknas eller om samtliga val är ogiltiga används engelska som standard. Om det finns
flera giltiga val används det som har högst q-värde, alternativt det som kommer först i headern.

Det använda språket för svaret går att utläsa ur Content-Language-headern i svaret.

# 13 Retry policy

APR har en inbyggd retry-policy mot underliggande system. Det förbättrar inte den totala svarstiden
att avbryta ett anrop och försöka igen. Det konsumerande systemet bör alltid invänta svaret från
APR innan anropet skickas på nytt.

# 14 Biljettköp

APR har inga tjänster för biljettköp. Dessa API:er tillhandahålls externt av Samtrafiken och BoB
som förmedlar biljetter för Västtrafik:

https://samtrafiken.atlassian.net/wiki/spaces/BOB/overview

För mera information om hur du kan bli återförsäljare av Västtrafiks biljetter, gå till:

https://www.vasttrafik.se/foretag/digital-aterforsaljare/

# 15 Appendix

## 15.1 Termer och förklaringar

| Term                | Förklaring                                                                                                                     |
| :------------------ | :----------------------------------------------------------------------------------------------------------------------------- |
| Address             | En adress. Representeras av latitud och longitud.                                                                              |
| ArrivalAccessLink   | En transport mellan en StopPoint och enAddress som inte är kollektivtrafik.                                                    |
| Call                | Ett stopp vid en hållplats. Har en StopPoint. Kan ha ankomst- och avgångstid. Kan ha Occupancy.                                |
| DepartureAccessLink | En transport mellan en Address och en StopPoint som inte är kollektivtrafik.                                                   |
| Destination         | En StopPoint , StopArea , MetaStation eller                                                                                    |
| Address             | Definierar slutpunkten för en Journey.                                                                                         |
| DestinationLink     | En transport mellan två Address :er / StopPoint :s som inte är kollektivtrafik.Utgör en hel resa från start till slut.         |
| GID                 | Ett unikt ID för entiteter på Västtrafik. Finns i olika kategorier med respektive prefix.                                      |
| Hållplats           | Se Call eller StopPoint.                                                                                                       |
| Journey             | Resenärens resa. Kan ha en ArrivalAccessLink , DepartureAccessLink ,DestinationAccessLink och noll, en eller flera TripLeg :s. |
| Line                | En representation av en samling ServiceJourney :s mellan två StopPoint :s. Har namn, färg, TransportMode mm.                   |
| Linje               | Se Line                                                                                                                        |
| MetaStation         | En samling av StopAreas.                                                                                                       |
| Occupancy           | Trängselprognos                                                                                                                |
| Origin              | En StopPoint , StopArea , MetaStation eller                                                                                    |
| Address.            | Definierar startpunkten för en Journey.                                                                                        |
| PointOfInterest     | En plats av intresse med ett eget namn, till exempel Lisebergsteatern. Representeras av latitud och longitud.                  |
| ServiceJourney      | En trafiktur för ett kollektivtrafiksfordon, för en linje vid ett tillfälle. Har en GID.                                       |
| StopArea            | Ett hållplatsområde. Består av ett eller flera StopPoint :s. Har GID , latitud och longitud.                                   |
| StopPoint           | Ett hållplatsläge för kollektivtrafik. Har GID , latitud och longitud.                                                         |
| Trafikdygn          | En 24-timmarsperiod från kl 04:00                                                                                              |
| TransferLink        | En transport mellan två StopPoints som inte är kollektivtrafik.                                                                |
| TransportMode       | Anger typ av transport. Till exempel: tram, bus, ferry, train                                                                  |
| TripLeg             | En del av Journey. Kan vara en delmängd av en ServiceJourney eller en TransferLink                                             |
| Tur                 | Se ServiceJourney                                                                                                              |

## 15.2 Svarsobjekt

Alla svarsobjekt med tillhörande egenskaper och enums finns dokumenterade i APR:s Swagger.
Objekten nedan får extra förtydligande.

### 15.2.1 Svarsobjekt för en resa

En resa kan bestå av kollektivtrafik och/eller alternativa kommunikationsmedel. Diagrammet nedan
visar strukturen för en resa med dess svarsobjekt (blå färg), kollektivtrafik (grön färg) och
alternativa kommunikationsmedel (lila färg).

I diagrammet kan en adress vara både adress och hållplats. En hållplats är alltid en
kollektivtrafikhållplats.

![diagram](https://github.com/vasttrafik/api-pr-docs/assets/14925434/21adac87-2c72-4508-b781-fee9b39f757b)

### 15.2.2 TripLegs och ConnectionLinks

TripLegs och ConnectionLinks returneras i separata listor. För att kunna återskapa deras
kronologiska ordning används egenskapen `journeyLegIndex`. Detta index är gemensamt för
bägge listorna och visar deras inbördes ordning i resan.
