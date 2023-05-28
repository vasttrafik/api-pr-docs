**Övergång till APR**

# 1 Innehållsförteckning

- 2 Introduktion
- 3 Generella skillnader
  - 3.1 Returformat
  - 3.2 Språkstöd
  - 3.3 Tidsangivelser
- 4 Trip
  - 4.1 Parameternamn
  - 4.2 Svarsstruktur
- 5 JourneyDetail
  - 5.1 Parameternamn
  - 5.2 Svarsstruktur
- 6 Geometry
  - 6.1 Parameternamn
  - 6.2 Svarsstruktur
- 7 Location.name
  - 7.1 Parameternamn
  - 7.2 Svarsstruktur
- 8 Location.allstops
- 9 Location.nearbystops
  - 9.1 Parameternamn
  - 9.2 Svarsstruktur
- 10 Location.nearbyaddress
- 11 DepartureBoard / ArrivalBoard
  - 11.1 Parameternamn
  - 11.2 Svarsstruktur
- 12 Livemap
  - 12.1 Parameternamn
  - 12.2 Svarsstruktur

# 2 Introduktion

Detta dokument är framtaget för användare som ska migrera från Reseplaneraren (RP) till API
Planera Resa (APR). API:erna har resurser som motsvarar varandra, men utformningen av frågor,
resultat och parameternamn skiljer sig åt. Dessa skillnader är dokumenterade och förklarade för
varje resurs nedan.

# 3 Generella skillnader

## 3.1 Returformat

- RP erbjuder både JSON och XML som returformat.
- APR returnerar alltid JSON.

## 3.2 Språkstöd

- RP erbjuder ett antal språk via lang-parametern.
- APR stödjer enbart svenska och engelska via Accept-Language-headern (Se separat APR-
  dokumentation).

## 3.3 Tidsangivelser

- RP förutsätter alltid lokal tid och accepterar tidsangivelser med eller utan sekunder.
- APR kräver tidszon för alla tidsangivelser och följer strikt RFC 3339-formatet.
  T ex: 2022-06-14T15:30:00+02

# 4 Trip

Motsvarande endpoint i APR är journeys.

## 4.1 Parameternamn

| RP                                                                           | APR                                              | Kommentar                                                                                                                   |
| :--------------------------------------------------------------------------- | :----------------------------------------------- | :-------------------------------------------------------------------------------------------------------------------------- |
| originId                                                                     | originGid                                        |                                                                                                                             |
| originCoordLat                                                               | originLatitude                                   |                                                                                                                             |
| originCoordLong                                                              | originLongitude                                  |                                                                                                                             |
| originCoordName                                                              | originName                                       |                                                                                                                             |
| destId                                                                       | destinationGid                                   |                                                                                                                             |
| destCoordLat                                                                 | destinationLatitude                              |                                                                                                                             |
| destCoordLong                                                                | destinationLongitude                             |                                                                                                                             |
| destCoordName                                                                | destinationName                                  |                                                                                                                             |
| viaId                                                                        | viaGid                                           |                                                                                                                             |
| date dateTime                                                                | Inkluderar även tid och tidszon enligt RFC 3339. |                                                                                                                             |
| time                                                                         | -                                                | Ingår i dateTime                                                                                                            |
| searchForArrival                                                             | dateTimeRelatesTo                                | searchForArrival = 1 motsvaras av dateTimeRelatesTo = arrival                                                               |
| useVas, useLDTrain, useRegTrain, useBus, useMedical, useBoat, useTram, usePT | transportModes, transportSubModes                | Se separat APR-dokumentation.                                                                                               |
| originMedicalCon                                                             | -                                                | Används ej                                                                                                                  |
| destMedicalCon                                                               | -                                                | Används ej                                                                                                                  |
| wheelChairSpace                                                              | -                                                | Används ej                                                                                                                  |
| strollerSpace                                                                | -                                                | Används ej                                                                                                                  |
| lowFloor                                                                     | -                                                | Används ej                                                                                                                  |
| rampOrLift                                                                   | -                                                | Används ej                                                                                                                  |
| excludeDR                                                                    | - Används ej                                     |                                                                                                                             |
| originWalk, maxWalkDist                                                      | originWalk                                       | Sammanslagna parametrar i APR. Se separatdokumentation.                                                                     |
| walkSpeed                                                                    | -                                                | Används ej                                                                                                                  |
| originBike, maxBikeDist                                                      | originBike                                       | Sammanslagna parametrar i APR. Se separat dokumentation.                                                                    |
| bikeCriterion                                                                | -                                                | Används ej                                                                                                                  |
| bikeProfile                                                                  | -                                                | Används ej                                                                                                                  |
| originCar, maxCarDist                                                        | originCar                                        | Sammanslagna parametrar i APR. Se separat dokumentation.                                                                    |
| originCarWithParking                                                         | originPark                                       |                                                                                                                             |
| destWalk                                                                     | destWalk                                         |                                                                                                                             |
| destCar                                                                      | destCar                                          |                                                                                                                             |
| destPark                                                                     | destPark                                         |                                                                                                                             |
| onlyWalk                                                                     | -                                                | Används ej. Resor till fots presenteras automatiskt om de faller inom parametrarna för originWalk och anses optimala.       |
| onlyBike                                                                     | totalBike                                        |                                                                                                                             |
| onlyCar                                                                      | -                                                | Används ej                                                                                                                  |
| bikeCarriage                                                                 | -                                                | Används ej                                                                                                                  |
| needJourneyDetail                                                            | -                                                | Används alltid                                                                                                              |
| needGeo                                                                      | -                                                | Används alltid                                                                                                              |
| needItinerary                                                                | -                                                | Används ej                                                                                                                  |
| numTrips                                                                     | limit                                            |                                                                                                                             |
| format                                                                       | -                                                | Alltid JSON                                                                                                                 |
| jsonpCallback                                                                | -                                                | Används ej                                                                                                                  |
| additionalChangeTime                                                         | interchangeDurationInMinutes                     | Anger ny minimal bytestid för samtliga hållplatser, oavsett ordinarie bytestid. Ersätter tiden istället för att lägga till. |
| maxChanges                                                                   | onlyDirectConnections                            | Motsvaras av maxChanges = 0                                                                                                 |
| disregardDefaultChangeMargin                                                 | -                                                | Används ej                                                                                                                  |

## 4.2 Svarsstruktur

Endast ett urval av skillnaderna är med i jämförelsen. Se Swagger-dokumentationen för fullständiga
returobjekt.

| RP                                                | APR                                              | Kommentar                                                                                                                                                           |
| :------------------------------------------------ | :----------------------------------------------- | :------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| TripList.Trip[]                                   | results[] array                                  |                                                                                                                                                                     |
| TripList.Trip[].Leg[]                             | results[].tripLegs[] results[].connectionLinks[] | array. TripLegs i RP motsvaras av tripLegs eller connectionLinks i APR beroende på om det är kollektivtrafik eller inte. Se separat APR-dokumentation för detaljer. |
| Leg[].JourneyDetailRef.ref, Leg[].GeometryRef.ref | results[].detailsReference                       | RP har en JourneyDetailRef / GisRef för varje Leg. APR har en detailsReference för hela resan.                                                                      |
| Leg[]                                             | results[].tripLegs[].line                        | Egenskaperna för fordonet finns på ett eget objekt i APR.                                                                                                           |
| TripList.servertime                               | -                                                | Date-headern används för servertid.                                                                                                                                 |

# 5 JourneyDetail

Motsvarande endpoint i APR är journeys/[detailsReference]/details. APR genererar en
detailsReference för alla tripLegs i resan och returnerar detaljerna för både journeyDetail och
geometry i samma svar (se kapitel 6 nedan).

## 5.1 Parameternamn

| RP  | APR              | Kommentar                                                                     |
| :-- | :--------------- | :---------------------------------------------------------------------------- |
| ref | detailsReference | Del av URL i APR.                                                             |
| -   | includes         | APR har flera ytterligare berikningar av datan. Se separat APR-dokumentation. |

## 5.2 Svarsstruktur

Endast ett urval av skillnaderna är med i jämförelsen. Se Swagger-dokumentationen för fullständiga
returobjekt.

| RP                        | APR                                                                                                                         | Kommentar |
| :------------------------ | :-------------------------------------------------------------------------------------------------------------------------- | :-------- |
| -                         | tripLegs[] APR returnerar details för alla tripLegs på resan i samma anrop. RP returnerar details för en tripLeg per anrop. |           |
| JourneyDetail.Stop[]      | tripLegs[].callsOnTripLeg[]                                                                                                 | array     |
| JourneyDetail.JourneyName | tripLegs[].serviceJourneys[]                                                                                                | array     |
| JourneyDetail.JourneyType | tripLegs[].serviceJourneys[]                                                                                                | array     |
| JourneyDetail.JourneyId   | tripLegs[].serviceJourneys[]                                                                                                |           |

# 6 Geometry

Motsvarande endpoint i APR är journeys/[detailsReference]/details.

## 6.1 Parameternamn

| RP  | APR              | Kommentar         |
| :-- | :--------------- | :---------------- |
| ref | detailsReference | Del av URL i APR. |

## 6.2 Svarsstruktur

Endast ett urval av skillnaderna är med i jämförelsen. Se Swagger-dokumentationen för fullständiga
returobjekt.

| RP                      | APR                                                      | Kommentar                                                                                                        |
| :---------------------- | :------------------------------------------------------- | :--------------------------------------------------------------------------------------------------------------- |
| -                       | tripLegs[]                                               | APR returnerar details för alla tripLegs på resan i samma anrop. RP returnerar details för en tripLeg per anrop. |
| Geometry.Points.Point[] | tripLegs[].serviceJourneys[].serviceJourneyCoordinates[] | array                                                                                                            |

# 7 Location.name

Motsvarande endpoint i APR är locations/by-text

## 7.1 Parameternamn

| RP            | APR | Kommentar   |
| :------------ | :-- | :---------- |
| input         | q   |             |
| format        | -   | Alltid JSON |
| jsonpCallback | -   | Används ej  |

## 7.2 Svarsstruktur

Endast ett urval av skillnaderna är med i jämförelsen. Se Swagger-dokumentationen för fullständiga
returobjekt.

| RP                           | APR       | Kommentar                                                                         |
| :--------------------------- | :-------- | :-------------------------------------------------------------------------------- |
| LocationList[]               | results[] | array                                                                             |
| LocationList[].StopLocation  | results[] | APR returnerar samma svarsobjekt för alla typer. Differentierar med locationType. |
| LocationList[].CoordLocation | results[] |                                                                                   |

# 8 Location.allstops

Används ej i APR

# 9 Location.nearbystops

Motsvarande endpoint i APR är locations/by-coordinates

## 9.1 Parameternamn

| RP              | APR            | Kommentar   |
| :-------------- | :------------- | :---------- |
| originCoordLat  | latitude       |
| originCoordLong | longitude      |
| maxDist         | radiusInMeters |
| maxNo           | limit          |
| format          | -              | Alltid JSON |
| jsonpCallback   | -              | Används ej  |

## 9.2 Svarsstruktur

Endast ett urval av skillnaderna är med i jämförelsen. Se Swagger-dokumentationen för fullständiga
returobjekt.

| RP                           | APR                                                        | Kommentar                        |
| :--------------------------- | :--------------------------------------------------------- | :------------------------------- |
| LocationList[]               | results[]                                                  | array                            |
| LocationList[].StopLocation  | results[] APR returnerar samma svarsobjekt för alla typer. | Differentierar med locationType. |
| LocationList[].CoordLocation | results[]                                                  |                                  |

# 10 Location.nearbyaddress

Används ej i APR

# 11 DepartureBoard / ArrivalBoard

Motsvarande endpoints i APR är:

stop-areas/[stopAreaGid]/departures

stop-areas/[stopAreaGid]/arrivals

## 11.1 Parameternamn

| RP                                                        | APR                                                      | Kommentar                                        |
| :-------------------------------------------------------- | :------------------------------------------------------- | :----------------------------------------------- |
| id                                                        | stopAreaGid                                              | Del av URL i APR.                                |
| direction, maxDeparturesPerLine                           | maxDeparturesPerLineAndDirection                         | APR:s parameter är sammanslagen.                 |
| -                                                         | limit APR kan även begränsa det totala antalet resultat. |                                                  |
| date                                                      | startDateTime                                            | Inkluderar även tid och tidszon enligt RFC 3339. |
| time                                                      | -                                                        | Ingår i startDateTime                            |
| timeSpan                                                  | timeSpanInMinutes                                        |                                                  |
| useVas, useLDTrain, useRegTrain, useBus, useBoat, useTram | -                                                        | Används ej                                       |
| excludeDR                                                 | -                                                        | Används ej                                       |
| needJourneyDetail                                         | -                                                        | Används alltid                                   |
| format                                                    | -                                                        | Alltid JSON                                      |
| jsonpCallback                                             | -                                                        | Används ej                                       |

## 11.2 Svarsstruktur

Endast ett urval av skillnaderna är med i jämförelsen. Se Swagger-dokumentationen för fullständiga
returobjekt.

| RP                                         | APR                        | Kommentar                                                                        |
| :----------------------------------------- | :------------------------- | :------------------------------------------------------------------------------- |
| ArrivalBoard.(Departure/Arrival)[]         | results[]                  | APR har delat upp egenskaperna på underordnade objekt: serviceJourney, stopPoint |
| (Departure/Arrival)[].JourneyDetailRef.ref | results[].detailsReference |                                                                                  |

# 12 Livemap

Motsvarande endpoint i APR är positions

## 12.1 Parameternamn

| RP           | APR            | Kommentar  |
| :----------- | :------------- | :--------- |
| miny         | lowerLeftLat   |            |
| minx         | lowerLeftLong  |            |
| maxy         | upperRightLat  |            |
| maxx         | upperRightLong |            |
| onlyRealtime | -              | Används ej |

## 12.2 Svarsstruktur

Endast ett urval av skillnaderna är med i jämförelsen. Se Swagger-dokumentationen för fullständiga
returobjekt.

| RP                 | APR     | Kommentar                                                      |
| :----------------- | :------ | :------------------------------------------------------------- |
| livemap.vehicles[] | []      | APR har en array i rotobjektet.                                |
| -                  | [].line | APR har linje-relaterade data här i stället för i rotobjektet. |
