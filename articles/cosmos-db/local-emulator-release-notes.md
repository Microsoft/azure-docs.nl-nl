---
title: Azure Cosmos DB-emulator downloaden en opmerkingen bij de release
description: Download de opmerkingen bij de release voor de Azure Cosmos DB-emulator voor verschillende versies en downloadgegevens.
ms.service: cosmos-db
ms.topic: conceptual
author: milismsft
ms.author: adrianmi
ms.date: 09/21/2020
ms.openlocfilehash: 92fdef48556a3306ee5e3f10e07ff609a8fc4be4
ms.sourcegitcommit: ad677fdb81f1a2a83ce72fa4f8a3a871f712599f
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/17/2020
ms.locfileid: "97656220"
---
# <a name="azure-cosmos-db-emulator---release-notes-and-download-information"></a>Azure Cosmos DB-emulator - opmerkingen bij de release en downloadgegevens
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Dit artikel bevat de opmerkingen bij de release voor Azure Cosmos DB-emulator met een lijst met functie-updates die in elke release zijn uitgevoerd. Ook wordt de meest recente versie van de emulator weergegeven die u kunt downloaden en gebruiken.

## <a name="download"></a>Downloaden

| | |
|---------|---------|
|**MSI-download**|[Microsoft Downloadcentrum](https://aka.ms/cosmosdb-emulator)|
|**Aan de slag**|[Lokaal ontwikkelen met Azure Cosmos DB-emulator](local-emulator.md)|

## <a name="release-notes"></a>Releaseopmerkingen

### <a name="2119-3-december-2020"></a>2.11.9 (3 december 2020)

 - In deze release wordt een aantal problemen met de Azure Cosmos DB Emulator-functionaliteit aangepakt, naast de algemene inhoudsupdate waarin de nieuwste functies en verbeteringen in Azure Cosmos DB zijn omvat:
 * Een oplossing voor een probleem waarbij grote documentpayloadaanvragen mislukken bij gebruik van de directe modus en Java-clienttoepassingen.
 * Een oplossing voor een connectiviteitsprobleem met MongoDB-eindpuntversie 3.6 wanneer deze is gericht op toepassingen die zijn gebaseerd op .NET.

### <a name="2118-6-november-2020"></a>2.11.8 (6 november 2020)

 - Deze release bevat een update voor de functie Data Explorer van de Cosmos-emulator waarmee een probleem wordt opgelost dat optreedt wanneer TLS 1.3-clients proberen Data Explorer te openen.

### <a name="2116-6-october-2020"></a>2.11.6 (6 oktober 2020)

 - Met deze release wordt een probleem met gelijktijdigheid opgelost als er meerdere containers tegelijk worden gemaakt. In dergelijke gevallen zijn de emulatorgegevens beschadigd en kunnen de volgende API-aanvragen aan het eindpunt van de emulator mislukken waarbij fouten worden weergegeven die aangeven dat de 'service niet beschikbaar is', waardoor deze opnieuw moet worden opgestart en de lokale emulatorgegevens opnieuw moeten worden ingesteld.

### <a name="2115-23-august-2020"></a>2.11.5 (23 augustus 2020)

In deze release zijn twee nieuwe opstartopties voor de Cosmos-emulator toegevoegd: 

* /EnablePreview: hiermee kunnen preview-functies voor de emulator worden ingeschakeld. De preview-functies zijn nog in ontwikkeling en toegankelijk via CI en voorbeeldschrijfbewerkingen.
* '/EnableAadAuthentication': hiermee kan de emulator aangepaste Azure Active Directory-tokens accepteren als alternatief voor de Azure Cosmos-hoofdsleutels. Deze functie is nog in ontwikkeling; specifieke roltoewijzingen en andere machtigingsinstellingen worden momenteel niet ondersteund.

### <a name="2112-07-july-2020"></a>2.11.2 (7 juli 2020)

- Met deze release wordt gewijzigd hoe ETL-traceringen die zijn vereist bij het oplossen van problemen met de Cosmos-emulator, worden verzameld. WPR-hulpprogramma's (Windows Performance Runtime) zijn nu de standaardhulpprogramma's voor het vastleggen van ETL-traceringen terwijl de oude vastleggingsmethode op basis van LOGMAN is afgeschaft. Deze wijziging is gedeeltelijk vereist omdat de nieuwste beveiligingsupdates van Windows een onverwachte invloed hebben op de werking van Logman wanneer het programma wordt uitgevoerd via de Cosmos-emulator.

### <a name="2111-10-june-2020"></a>2.11.1 (10 juni 2020)

- Deze versie corrigeert een aantal bugs die zijn gerelateerd aan de emulator Data Explorer. Bij het gebruik van de emulator Data Explorer via een webbrowser, kan het in bepaalde gevallen voorkomen dat deze geen verbinding kan maken met het Cosmos-emulatoreindpunt. Alle gerelateerde acties, zoals het maken van een database of een container, zullen dan mislukken. Het tweede probleem dat is opgelost heeft betrekking op het maken van een item vanuit een JSON-bestand met behulp van de uploadactie van Data Explorer.

### <a name="2110"></a>2.11.0

- In deze release wordt ondersteuning geïntroduceerd voor automatische schaalaanpassing van ingerichte doorvoer. Deze nieuwe functies omvatten de mogelijkheid om een aangepast maximaal doorvoerniveau in te stellen in aanvraageenheden (RU/s), automatische schaalaanpassing in te schakelen voor bestaande databases en containers en programmatische ondersteuning via Azure Cosmos DB SDK's.
- Oplossing voor een probleem tijdens het uitvoeren van een query op een grote hoeveelheid documenten (meer dan 1 GB), waarbij de emulator uitvalt met interne foutstatuscode 500.

### <a name="292"></a>2.9.2

- In deze release wordt een bug gecorrigeerd en tegelijk ondersteuning ingeschakeld voor MongoDB-eindpunt versie 3.2. Er wordt ook ondersteuning toegevoegd voor het genereren van ETL-traceringen voor het oplossen van problemen met behulp van WPR in plaats van LOGMAN.

### <a name="291"></a>2.9.1

- In deze release worden problemen in de query-API gecorrigeerd en de compatibiliteit met oudere besturingssystemen hersteld, zoals Windows Server 2012.

### <a name="290"></a>2.9.0

- In deze release wordt de optie toegevoegd om de consistentie in te stellen op consistent voorvoegsel en worden de maximum limieten verhoogd voor gebruikers en machtigingen.

### <a name="272"></a>2.7.2

- In deze release wordt MongoDB-serverondersteuning versie 3.6 toegevoegd aan de Cosmos-emulator. Als u een MongoDB-eindpunt met doelversie 3.6 van de service wilt starten, start u de emulator vanaf een beheerdersopdrachtregel met de optie '/EnableMongoDBEndpoint = 3.6 '.

### <a name="270"></a>2.7.0

- In deze release wordt een regressie gecorrigeerd waarmee wordt voorkomen dat gebruikers query's uitvoeren op het SQL-API-account van de emulator bij gebruik van .NET core- of x86 .NET-clients.

### <a name="246"></a>2.4.6

- Deze release biedt pariteit met de functies van de Azure Cosmos-service vanaf juli 2019, met de uitzonderingen die worden vermeld in [Lokaal ontwikkelen met Azure Cosmos DB-emulator](local-emulator.md). Daarnaast worden verschillende fouten met betrekking tot het afsluiten van de emulator opgelost wanneer deze via de opdrachtregel wordt aangeroepen, en bij overschrijvingen van het interne IP-adres voor SDK-clients die gebruikmaken van een verbinding in directe modus.

### <a name="243"></a>2.4.3

- Het starten van de MongoDB-service is standaard uitgeschakeld. Alleen het SQL-eindpunt is standaard ingeschakeld. De gebruiker moet het eindpunt handmatig starten met de opdrachtregeloptie '/EnableMongoDbEndpoint' van de emulator. Nu is deze gelijk aan alle andere service-eindpunten, zoals Gremlin, Cassandra en Table.
- Er is een fout opgelost in de emulator tijdens het starten met '/AllowNetworkAccess', waarbij de Gremlin-, Cassandra- en Table-eindpunten aanvragen van externe clients niet goed konden verwerken.
- Directe verbindingspoorten toegevoegd aan de instellingen van de firewallregels.

### <a name="240"></a>2.4.0

- Er is een probleem opgelost waarbij de emulator niet kon worden gestart wanneer netwerkbewaking-apps, zoals Pulse Client, aanwezig waren op de hostcomputer.
