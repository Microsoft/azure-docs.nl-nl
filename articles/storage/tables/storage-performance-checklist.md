---
title: Controlelijst voor prestaties en schaalbaarheid van Table-opslag - Azure Storage
description: Een controlelijst met bewezen procedures voor het gebruik van Table-opslag in het ontwikkelen van toepassingen met hoge prestaties.
services: storage
author: tamram
ms.author: tamram
ms.service: storage
ms.topic: overview
ms.date: 10/10/2019
ms.subservice: tables
ms.custom: devx-track-csharp
ms.openlocfilehash: 71b1f3cfa1df86b417c468d56f67cd7fe8d71d73
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96004701"
---
# <a name="performance-and-scalability-checklist-for-table-storage"></a>Controlelijst voor prestaties en schaalbaarheid van Table-opslag

Microsoft heeft een aantal bewezen procedures ontwikkeld voor het ontwikkelen van hoogwaardige toepassingen met Table-opslag. Deze controlelijst vermeldt de belangrijkste procedures die ontwikkelaars kunnen volgen om de prestaties te optimaliseren. Neem bij het ontwerpen van uw toepassing en tijdens het gehele proces deze procedures in acht.

Azure Storage bevat schaalbaarheids- en prestatiedoelen voor capaciteit, transactiesnelheid en bandbreedte. Zie [Schaalbaarheids- en prestatiedoelen voor standaardopslagaccounts](../common/scalability-targets-standard-account.md?toc=%2fazure%2fstorage%2ftables%2ftoc.json) en [Schaalbaarheids- en prestatiedoelen voor Table-opslag](scalability-targets.md) voor meer informatie over schaalbaarheidsdoelen van Azure Storage.

## <a name="checklist"></a>Controlelijst

In dit artikel worden bewezen procedures voor prestaties georganiseerd in een controlelijst die u kunt volgen tijdens het ontwikkelen van uw Table-opslagtoepassing.

| Gereed | Categorie | Ontwerpoverwegingen |
| --- | --- | --- |
| &nbsp; |Schaalbaarheidsdoelen |[Kunt u uw toepassing zo ontwerpen dat deze niet meer dan het maximale aantal opslagaccounts gebruikt?](#maximum-number-of-storage-accounts) |
| &nbsp; |Schaalbaarheidsdoelen |[Wilt u niet de capaciteits- en transactielimieten benaderen?](#capacity-and-transaction-targets) |
| &nbsp; |Schaalbaarheidsdoelen |[Benadert u de schaalbaarheidsdoelen voor entiteiten per seconde?](#targets-for-data-operations) |
| &nbsp; |Netwerken |[Hebben apparaten aan de clientzijde voldoende bandbreedte en lage latentie om de benodigde prestaties te verwezenlijken?](#throughput) |
| &nbsp; |Netwerken |[Hebben apparaten aan de clientzijde een netwerkkoppeling van hoge kwaliteit?](#link-quality) |
| &nbsp; |Netwerken |[Bevindt de clienttoepassing zich in dezelfde regio als de opslagaccount?](#location) |
| &nbsp; |Directe clienttoegang |[Maakt u gebruik van Shared Access Signatures (SAS) en Cross-Origin Resource Sharing (CORS) om directe toegang tot Azure Storage te krijgen?](#sas-and-cors) |
| &nbsp; |Batchverwerking |[Worden de updates voor de toepassing batchgewijs bijgewerkt met behulp van entiteitsgroepstransacties?](#batch-transactions) |
| &nbsp; |.NET-configuratie |[Gebruikt u .NET Core 2.1 of hoger voor optimale prestaties?](#use-net-core) |
| &nbsp; |.NET-configuratie |[Hebt u uw client geconfigureerd voor het gebruik van een voldoende aantal gelijktijdige verbindingen?](#increase-default-connection-limit) |
| &nbsp; |.NET-configuratie |[Hebt u voor .NET-toepassingen .NET geconfigureerd voor het gebruik van een voldoende aantal threads?](#increase-minimum-number-of-threads) |
| &nbsp; |Parallelle uitvoering |[Hebt u ervoor gezorgd dat parallelle uitvoering op de juiste wijze is gebonden, zodat de functionaliteit van uw client niet wordt overbelast of de schaalbaarheidsdoelen worden genaderd?](#unbounded-parallelism) |
| &nbsp; |Hulpprogramma's |[Gebruikt u de nieuwste versies van door Microsoft meegeleverde clientbibliotheken en hulpprogramma's?](#client-libraries-and-tools) |
| &nbsp; |Nieuwe pogingen |[Gebruikt u een beleid voor opnieuw proberen met exponentieel uitstel voor het beperken van fouten en time-outs?](#timeout-and-server-busy-errors) |
| &nbsp; |Nieuwe pogingen |[Voorkomt uw toepassing nieuwe pogingen voor fouten waarbij geen nieuwe pogingen mogelijk zijn?](#non-retryable-errors) |
| &nbsp; |Configuratie |[Gebruikt u JSON voor uw tabelaanvragen?](#use-json) |
| &nbsp; |Configuratie |[Hebt u het Nagle-algoritme uitgeschakeld om de prestaties van kleine aanvragen te verbeteren?](#disable-nagle) |
| &nbsp; |Tabellen en partities |[Hebt u uw gegevens goed gepartitioneerd?](#schema) |
| &nbsp; |Dynamische partities |[Vermijdt u patronen voor alleen toevoegen en alleen laten voorafgaan?](#append-only-and-prepend-only-patterns) |
| &nbsp; |Dynamische partities |[Worden uw invoegingen/updates verspreid over veel partities?](#high-traffic-data) |
| &nbsp; |Querybereik |[Hebt u uw schema zo ontworpen dat in de meeste gevallen puntquery's kunnen worden gebruikt en spaarzaam tabelquery's kunnen worden gebruikt?](#query-scope) |
| &nbsp; |Querydichtheid |[Worden in uw query's doorgaans alleen de rijen gescand en geretourneerd die door uw toepassing worden gebruikt?](#query-density) |
| &nbsp; |De geretourneerde gegevens beperken |[Gebruikt u filteren om te voorkomen dat entiteiten worden geretourneerd die u niet nodig hebt?](#limiting-the-amount-of-data-returned) |
| &nbsp; |De geretourneerde gegevens beperken |[Gebruikt u projectie om te voorkomen dat entiteiten worden geretourneerd die u niet nodig hebt?](#limiting-the-amount-of-data-returned) |
| &nbsp; |Denormalisatie |[Hebt u uw gegevens gedenormaliseerd, zodat u inefficiënte query's of meerdere leesaanvragen voorkomt wanneer u gegevens wilt ophalen?](#denormalization) |
| &nbsp; |Invoegen, bijwerken en verwijderen |[Verwerkt u batchgewijs aanvragen die transactioneel moeten zijn of tegelijk kunnen worden uitgevoerd om het aantal retouren te verminderen?](#batching) |
| &nbsp; |Invoegen, bijwerken en verwijderen |[Wilt u voorkomen dat u alleen maar een entiteit ophaalt om te bepalen of u wel of niet invoeg- of bijwerkacties moet aanroepen?](#upsert) |
| &nbsp; |Invoegen, bijwerken en verwijderen |[Hebt u overwogen om een reeks regelmatig tegelijk opgehaalde gegevens in één entiteit als eigenschappen op te slaan in plaats van meerdere entiteiten?](#storing-data-series-in-a-single-entity) |
| &nbsp; |Invoegen, bijwerken en verwijderen |[Hebt u overwogen om blobs te gebruiken in plaats van tabellen voor entiteiten die altijd tegelijk worden opgehaald en in batches kunnen worden geschreven (bijvoorbeeld tijdreeksgegevens)?](#storing-structured-data-in-blobs) |

## <a name="scalability-targets"></a>Schaalbaarheidsdoelen

Als uw toepassing een van de schaalbaarheidsdoelen benadert of overschrijdt, kunnen er meer latenties in of beperkingen voor transacties optreden. Wanneer Azure Storage uw toepassing beperkt, begint de service met het retourneren van de foutcodes 503 (server bezet) of 500 (time-out voor bewerking). Het vermijden van deze fouten door binnen de grenzen van de schaalbaarheidsdoelen te blijven vormt een belangrijk onderdeel van het verbeteren van de prestaties van uw toepassing.

Zie [Schaalbaarheids- en prestatiedoelen voor de Table service](scalability-targets.md) voor meer informatie over de schaalbaarheidsdoelen voor Table-opslag.

### <a name="maximum-number-of-storage-accounts"></a>Maximum aantal opslagaccounts

Als u het maximumaantal opslagaccounts nadert dat is toegestaan voor een bepaalde combinatie van abonnement/regio, maakt u dan gebruik van meerdere opslagaccounts om in een shard op te slaan, waarmee de hoeveelheid inkomend/uitgaand verkeer, het aantal I/O-bewerkingen per seconde (IOPS) of de capaciteit wordt verhoogd? In dit scenario raadt Microsoft u aan gebruik te maken van verhoogde limieten voor opslagaccounts om zo mogelijk het aantal opslagaccounts te beperken dat is vereist voor uw workload. Neem contact op met [Ondersteuning voor Azure](https://azure.microsoft.com/support/options/) om verhoogde limieten voor uw opslagaccount aan te vragen. Zie [Grotere opslagaccounts met een hogere schaal](https://azure.microsoft.com/blog/announcing-larger-higher-scale-storage-accounts/) voor meer informatie.

### <a name="capacity-and-transaction-targets"></a>Capaciteits- en transactiedoelen

Als uw toepassing de schaalbaarheidsdoelen voor één opslagaccount nadert, kunt u een van de volgende benaderingen overwegen:  

- Bekijk de workload die ervoor zorgt dat uw toepassing het schaalbaarheidsdoel benadert of overschrijdt. Kunt u het op een andere manier ontwerpen waardoor u minder bandbreedte of capaciteit of minder transacties gebruikt?
- Als uw toepassing een van de schaalbaarheidsdoelen moet overschrijden, maakt u meerdere opslagaccounts en partitioneert u uw toepassingsgegevens over deze meerdere opslagaccounts. Als u dit patroon gebruikt, moet u ervoor zorgen dat u uw toepassing zo ontwerpt, dat u in de toekomst meer opslagaccounts kunt toevoegen voor de taakverdeling. Opslagaccounts zelf hebben geen andere kosten dan uw gebruik in termen van opgeslagen gegevens, gemaakte transacties of overgedragen gegevens.
- Als uw toepassing de bandbreedtedoelen nadert, kunt u de gegevens aan de clientzijde comprimeren om de bandbreedte te verminderen die nodig is om de gegevens naar Azure Storage te verzenden.
    Hoewel met het comprimeren van gegevens bandbreedte kan worden bespaard en de netwerkprestaties worden verbeterd, kan dit ook negatieve gevolgen voor de prestaties hebben. Evalueer de invloed op de prestaties van de extra verwerkingsvereisten voor de compressie en decompressie van gegevens aan de clientzijde. Houd er rekening mee dat het door het opslaan van gecomprimeerde gegevens lastiger wordt om problemen op te lossen, aangezien het lastiger kan zijn om de gegevens te bekijken met behulp van standaardhulpprogramma's.
- Als uw toepassing de schaalbaarheidsdoelen nadert, moet u ervoor zorgen dat u een exponentieel uitstel gebruikt voor nieuwe pogingen. U kunt het beste voorkomen dat u de schaalbaarheidsdoelen bereikt door de aanbevelingen te implementeren die in dit artikel worden beschreven. Als u echter een exponentieel uitstel gebruikt voor nieuwe pogingen, voorkomt u dat uw toepassing snel opnieuw pogingen onderneemt, waarmee de beperkingen ernstiger kunnen worden. Zie de sectie met de titel [Time-out- en server-bezetfouten](#timeout-and-server-busy-errors) voor meer informatie.

### <a name="targets-for-data-operations"></a>Doelen voor gegevensbewerkingen

Azure Storage zorgt voor een taakverdeling naarmate het verkeer naar uw opslagaccount toeneemt, maar als het verkeer plotselinge pieken vertoont, zou het kunnen dat u dit doorvoervolume niet onmiddellijk kunt behalen. U zult tijdens de piek naar verwachting met beperkingen en/of time-outs te maken krijgen, aangezien Azure Storage automatisch voor een taakverdeling voor uw tabel zorgt. Langzaam verhogen biedt doorgaans betere resultaten, omdat het systeem dan de tijd heeft om op de juiste manier de taken te verdelen.

#### <a name="entities-per-second-storage-account"></a>Entiteiten per seconde (opslagaccount)

De schaalbaarheidslimiet voor het openen van tabellen is maximaal 20.000 entiteiten (1 KB elk) per seconde voor een account. In het algemeen geldt dat elke entiteit die wordt ingevoegd, bijgewerkt, verwijderd of gescand wordt meegeteld voor dit doel. Een batch invoegen die 100 entiteiten bevat, telt dus als 100 entiteiten. Een query die 1000 entiteiten scant en 5 retourneert, telt als 1000 entiteiten.

#### <a name="entities-per-second-partition"></a>Entiteiten per seconde (partitie)

Binnen één partitie zijn de schaalbaarheidsdoelen voor het openen van tabellen 2000 entiteiten (1 KB elk) per seconde, met dezelfde telling als beschreven in de vorige sectie.

## <a name="networking"></a>Netwerken

De beperkingen van het fysieke netwerk van de toepassing kunnen een grote invloed hebben op de prestaties. In de volgende secties worden enkele beperkingen beschreven die gebruikers kunnen tegenkomen.  

### <a name="client-network-capability"></a>Netwerkmogelijkheden van de client

De bandbreedte en de kwaliteit van de netwerkkoppeling spelen een belangrijke rol in prestaties van toepassingen, zoals beschreven in de volgende secties.

#### <a name="throughput"></a>Doorvoer

Voor bandbreedte is het probleem vaak de mogelijkheden van de client. Grotere Azure-exemplaren hebben NIC's met een grotere capaciteit. U moet dus overwegen een grotere exemplaar of meer VM's te gebruiken als u hogere netwerklimieten voor één computer nodig hebt. Als u Azure Storage opent vanuit een on-premises toepassing, geldt dezelfde regel: zorg ervoor dat u inzicht krijgt in de netwerkmogelijkheden van het clientapparaat en de netwerkverbinding met de Azure Storage-locatie en verbeter deze, indien nodig. U kunt ook uw toepassing zo ontwerpen dat deze binnen deze mogelijkheden kan werken.

#### <a name="link-quality"></a>Kwaliteit van de koppeling

Net als bij elk netwerkgebruik moet u er rekening mee houden dat de netwerkomstandigheden die fouten en pakketverlies veroorzaken, ervoor zorgen dat de effectieve doorvoer wordt vertraagd.  Het gebruik van WireShark of NetMon kan helpen bij het vaststellen van dit probleem.  

### <a name="location"></a>Locatie

Wanneer u de client dicht bij de server plaatst, levert dit in een gedistribueerde omgeving de beste prestaties op. Als u toegang wilt tot Azure Storage met de laagste latentie, bevindt de beste locatie voor uw client zich in dezelfde Azure-regio. Als u bijvoorbeeld een Azure-web-app hebt die gebruikmaakt van Azure Storage, plaatst u deze beide binnen één regio, zoals US - west of Azië - zuidoost. Wanneer u resources bij elkaar plaatst, vermindert u de latentie en de kosten, omdat het bandbreedtegebruik binnen één regio gratis is.  

Als clienttoepassingen toegang krijgen tot Azure Storage, maar niet worden gehost in Azure, zoals apps voor mobiele apparaten of on-premises bedrijfsservices, kan de latentie worden verminderd door het opslagaccount in een regio in de buurt van die clients te plaatsen. Als uw clients breed worden gedistribueerd (bijvoorbeeld sommige in Noord-Amerika en sommige in Europa), kunt u een opslagaccount per regio gebruiken. Deze aanpak is eenvoudiger te implementeren als de gegevens die de app opslaat specifiek zijn voor afzonderlijke gebruikers en als er geen replicatie van gegevens nodig is tussen opslagaccounts.

## <a name="sas-and-cors"></a>SAS en CORS

Stel dat u code moet machtigen zoals JavaScript dat wordt uitgevoerd in de webbrowser van een gebruiker of in een app op een mobiele telefoon om toegang te krijgen tot gegevens in Azure Storage. Een aanpak is om een servicetoepassing te compileren die als een proxy fungeert. Het apparaat van de gebruiker wordt geverifieerd bij de service, die op zijn beurt toegang tot Azure Storage-resources verleent. Op deze manier kunt u voorkomen dat de sleutels van uw opslagaccount op onveilige apparaten worden weergegeven. Met deze benadering wordt echter een aanzienlijke overhead op de servicetoepassing geplaatst, omdat alle gegevens die worden overgedragen tussen het apparaat van de gebruiker en Azure Storage via de servicetoepassing moeten worden doorgegeven.

U kunt voorkomen dat een servicetoepassing wordt gebruikt als een proxy voor Azure Storage met behulp van Shared Access Signatures (SAS). Met SAS kunt u het apparaat van uw gebruiker in staat stellen om aanvragen rechtstreeks bij Azure Storage in te dienen met behulp van een beperkt toegangstoken. Als een gebruiker bijvoorbeeld een foto wil uploaden naar uw toepassing, kan uw servicetoepassing een SAS genereren en deze verzenden naar het apparaat van de gebruiker. Het SAS-token kan toestemming geven om naar een Azure Storage-resource te schrijven gedurende een opgegeven tijdsinterval, waarna het SAS-token verloopt. Zie [Beperkte toegang verlenen tot Azure Storage-resources via SAS (Shared Access Signatures)](../common/storage-sas-overview.md) voor meer informatie over SAS.  

Normaal gesproken staat een webbrowser geen JavaScript toe op een pagina die wordt gehost door een website op het ene domein om bepaalde bewerkingen, zoals schrijfbewerkingen, uit te voeren op het andere domein. Op basis van dit beleid, dat bekend staat als 'beleid voor zelfde oorsprong', wordt voorkomen dat een schadelijk script op één pagina de toegang tot gegevens op een andere webpagina kan verkrijgen. Hetzelfde 'beleid voor zelfde oorsprong' kan echter een beperking zijn bij het compileren van een oplossing in de cloud. Cross-Origin Resource Sharing (CORS) is een browserfunctie waarmee het doeldomein van het brondomein afkomstige aanvragen kan doorgeven aan een vertrouwde browser.

Stel bijvoorbeeld dat een webtoepassing die in Azure wordt uitgevoerd een aanvraag voor een resource indient bij een Azure Storage-account. De webtoepassing is het brondomein en het opslagaccount is het doeldomein. U kunt CORS voor elk van de Azure Storage-services configureren om met de webbrowser aanvragen door te geven vanuit het brondomein dat worden vertrouwd door Azure Storage. Zie [CORS-ondersteuning (cross-origin resource sharing) voor Azure Storage](/rest/api/storageservices/Cross-Origin-Resource-Sharing--CORS--Support-for-the-Azure-Storage-Services) voor meer informatie over CORS.  
  
Met SAS en CORS kunt u voorkomen dat uw webtoepassing onnodig wordt belast.  

## <a name="batch-transactions"></a>Batchtransacties

De Table service ondersteunt batchtransacties op entiteiten die zich in dezelfde tabel bevinden en deel uitmaken van dezelfde partitiegroep. Zie [Entiteitsgroepstransacties uitvoeren](/rest/api/storageservices/performing-entity-group-transactions) voor meer informatie.

## <a name="net-configuration"></a>.NET-configuratie

Als u het .NET Framework gebruikt, worden in deze sectie verschillende snelle configuratie-instellingen weergegeven die u kunt gebruiken om belangrijke verbeteringen in de prestaties aan te brengen.  Als u andere talen gebruikt, controleert u of er soortgelijke concepten van toepassing zijn in de taal die u hebt gekozen.  

### <a name="use-net-core"></a>.NET Core gebruiken

Ontwikkel uw Azure Storage-toepassingen met .NET Core 2.1 of hoger om te profiteren van de betere prestaties. Het gebruik van .NET Core 3.x wordt aanbevolen als dat mogelijk is.

Raadpleeg de volgende blogberichten voor meer informatie over betere prestaties in .NET Core:

- [Prestatieverbeteringen in .NET Core 3.0](https://devblogs.microsoft.com/dotnet/performance-improvements-in-net-core-3-0/)
- [Prestatieverbeteringen in .NET Core 2.1](https://devblogs.microsoft.com/dotnet/performance-improvements-in-net-core-2-1/)

### <a name="increase-default-connection-limit"></a>De standaardverbindingslimiet verhogen

In .NET wordt via de volgende code de standaardverbindingslimiet (meestal 2 in een clientomgeving of 10 in een serveromgeving) tot 100 verhoogd. Normaal gesproken moet u de waarde instellen op ongeveer het aantal threads dat door uw toepassing wordt gebruikt.  

```csharp
ServicePointManager.DefaultConnectionLimit = 100; //(Or More)  
```

Stel de verbindingslimiet in voordat u verbindingen opent.  

Raadpleeg voor andere programmeertalen de documentatie van die taal om te bepalen hoe de verbindingslimiet moet worden ingesteld.  

Zie voor meer informatie het blogbericht [Webservices: Gelijktijdige verbindingen](/archive/blogs/darrenj/web-services-concurrent-connections).  

### <a name="increase-minimum-number-of-threads"></a>Minimumaantal threads verhogen

Als u synchrone aanroepen gebruikt in combinatie met asynchrone taken, kunt u misschien beter het aantal threads in de threadpool verhogen:

```csharp
ThreadPool.SetMinThreads(100,100); //(Determine the right number for your application)  
```

Zie de methode [ThreadPool.SetMinThreads](/dotnet/api/system.threading.threadpool.setminthreads) voor meer informatie.  

## <a name="unbounded-parallelism"></a>Niet-gebonden parallelle uitvoering

Parallelle uitvoering kan ideaal zijn voor de prestaties, maar wees voorzichtig met het gebruik van een ongebonden parallelle uitvoering, wat betekent dat er geen limiet wordt afgedwongen voor het aantal threads of parallelle aanvragen. Zorg ervoor dat u parallelle aanvragen beperkt tot het uploaden of downloaden van gegevens, om toegang te krijgen tot meerdere partities in hetzelfde opslagaccount of om toegang te krijgen tot meerdere items in dezelfde partitie. Als parallelle uitvoering niet is gebonden, kan uw toepassing de mogelijkheden van het clientapparaat of de schaalbaarheidsdoelen van het opslagaccount overschrijden, wat resulteert in langere latenties en bandbreedtebeperking.  

## <a name="client-libraries-and-tools"></a>Clientbibliotheken en hulpprogramma's

Gebruik altijd de nieuwste clientbibliotheken en hulpprogramma's van Microsoft voor de beste prestaties. Azure Storage-clientbibliotheken zijn beschikbaar in diverse talen. Azure Storage biedt ook ondersteuning voor PowerShell en Azure CLI. Microsoft ontwikkelt deze clientbibliotheken en hulpprogramma's actief met het oog op de prestaties, houdt ze up-to-date met de nieuwste serviceversies en zorgt ervoor dat ze een groot aantal van de bewezen uitvoeringspraktijken intern verwerken.

## <a name="handle-service-errors"></a>Servicefouten afhandelen

Azure Storage retourneert een fout wanneer de service een aanvraag niet kan verwerken. Voor het optimaliseren van de prestaties is het handig om inzicht te hebben in de fouten die door Azure Storage in een bepaald scenario kunnen worden geretourneerd.

### <a name="timeout-and-server-busy-errors"></a>Time-out- en server-bezetfouten

Azure Storage kan uw toepassing beperken als deze de schaalbaarheidslimieten nadert. In sommige gevallen kan Azure Storage mogelijk geen aanvragen verwerken vanwege een tijdelijke omstandigheid. In beide gevallen kan de service de fout 503 (server bezet) of 500 (time-out) retourneren. Deze fouten kunnen zich ook voordoen als de service gegevenspartities opnieuw moet verdelen voor een hogere doorvoer. De clienttoepassing moet meestal de bewerking opnieuw uitvoeren die een van deze fouten heeft veroorzaakt. Als Azure Storage echter uw toepassing beperkt omdat deze de schaalbaarheidsdoelen overschrijdt of als de service om een andere reden zelfs niet aan de aanvraag kan voldoen, kunnen agressieve nieuwe pogingen het probleem erger maken. Het gebruik van een beleid voor opnieuw proberen met exponentieel uitstel wordt aanbevolen. De clientbibliotheken zijn standaard ingesteld op dit gedrag. Uw toepassing kan bijvoorbeeld na 2 seconden een nieuwe poging doen, daarna na 4 seconden, daarna na 10 seconden, daarna na 30 seconden, en vervolgens volledig opgeven. Op deze manier vermindert uw toepassing de belasting van de service aanzienlijk, in plaats van verergerend gedrag te vertonen dat kan leiden tot aanvraagbeperking.  

Bij connectiviteitsfouten kunnen onmiddellijk nieuwe pogingen worden gedaan, omdat ze niet het gevolg zijn van een beperking en naar verwachting tijdelijk zijn.  

### <a name="non-retryable-errors"></a>Fouten waarbij geen nieuwe pogingen mogelijk zijn

Bij de clientbibliotheken is bij het afhandelen van nieuwe pogingen bekend bij welke fouten opnieuw kan worden geprobeerd en welke niet. Als u echter de Azure Storage REST API rechtstreeks aanroept, zijn er enkele fouten waarbij u geen nieuwe pogingen moet ondernemen. Bijvoorbeeld, de fout 400 (ongeldige aanvraag) geeft aan dat de clienttoepassing een aanvraag heeft verzonden die niet kan worden verwerkt omdat deze niet in de verwachte vorm is opgemaakt. Als deze aanvraag opnieuw wordt verzonden, resulteert dit steeds in hetzelfde antwoord, zodat het zinloos is om een nieuwe poging te ondernemen. Als u de REST API Azure Storage rechtstreeks aanroept, moet u rekening houden met mogelijke fouten en of er nieuwe pogingen moeten worden gedaan.

Zie [Status en foutcodes](/rest/api/storageservices/status-and-error-codes2) voor meer informatie over Azure Storage-foutcodes.

## <a name="configuration"></a>Configuratie

In deze sectie worden verschillende snelle configuratie-instellingen weergegeven die u kunt gebruiken om belangrijke verbeteringen in de prestaties van de Table service aan te brengen:

### <a name="use-json"></a>JSON gebruiken

Vanaf Storage-serviceversie 2013-08-15 ondersteunt de Table service het gebruik van JSON in plaats van de op XML gebaseerde AtomPub-indeling voor het overdragen van tabelgegevens. Het gebruik van JSON kan de payloadgrootte tot wel 75% verminderen en kan de prestaties van uw toepassing aanzienlijk verbeteren.

Zie voor meer informatie het bericht [Microsoft Azure-tabellen: Kennismaking met JSON](/archive/blogs/windowsazurestorage/windows-azure-tables-introducing-json) en [Payload-indeling voor Table service-bewerkingen](/rest/api/storageservices/Payload-Format-for-Table-Service-Operations).

### <a name="disable-nagle"></a>Nagle uitschakelen

Het Nagle-algoritme is breed geïmplementeerd in TCP/IP-netwerken om de netwerkprestaties te verbeteren. Het is echter niet optimaal in alle omstandigheden (zoals in uiterst interactieve omgevingen). Het Nagle-algoritme heeft een negatieve invloed op de prestaties van aanvragen voor de Azure Table service, en moet zo mogelijk worden uitgeschakeld.

## <a name="schema"></a>Schema

Hoe u uw gegevens weergeeft en opvraagt, is de belangrijkste factor die van invloed is op de prestaties van de Table service. Hoewel elke toepassing verschilt, wordt in deze sectie een overzicht van enkele algemene bewezen procedures besproken die betrekking hebben op:

- Tabelontwerp
- Efficiënte query's
- Efficiënte updates van gegevens

### <a name="tables-and-partitions"></a>Tabellen en partities

Tabellen worden onderverdeeld in partities. Elke entiteit die is opgeslagen in een partitie deelt dezelfde partitiesleutel en heeft een unieke rijsleutel om deze te identificeren binnen die partitie. Partities bieden voordelen, maar kunnen ook voor schaalbaarheidslimieten zorgen.

- Voordelen: U kunt entiteiten in dezelfde partitie bijwerken in één atomische batchtransactie die maximaal 100 afzonderlijke opslagbewerkingen bevat (limiet voor totale grootte van 4 MB). Uitgaande van het aantal entiteiten dat moet worden opgehaald, kunt u ook de gegevens binnen één partitie efficiënter opvragen dan gegevens die over meerdere partities zijn verdeeld (lees echter verder voor nog meer aanbevelingen voor het opvragen van tabel gegevens).
- Schaalbaarheidslimiet: De toegang tot entiteiten die zijn opgeslagen in één partitie, kan niet gelijkmatig worden verdeeld, omdat partities atomische batchtransacties ondersteunen. Daarom is het schaalbaarheidsdoel voor een afzonderlijke tabelpartitie lager dan voor de Table service als geheel.

Vanwege deze kenmerken van tabellen en partities moet u de volgende ontwerpprincipes aanhouden:

- Zoek gegevens die regelmatig in dezelfde logische werkeenheid in dezelfde partitie door uw clienttoepassing worden bijgewerkt en opgevraagd. Zoek bijvoorbeeld naar gegevens in dezelfde partitie als uw toepassing bezig is met het samenvoegen van schrijfbewerkingen of als u atomische batchbewerkingen uitvoert. Ook kunnen gegevens in één partitie efficiënter worden opgevraagd in één query dan gegevens binnen verschillende partities.
- Zoek gegevens die niet door uw clienttoepassing worden ingevoegd, bijgewerkt of opgevraagd in dezelfde logische werkeenheid (dat wil zeggen, in één query of batchupdate) in afzonderlijke partities. Houd er rekening mee dat er geen limiet is voor het aantal partitiesleutels in één tabel, waardoor het geen probleem is om miljoenen partitiesleutels te hebben. Deze zijn niet van invloed op de prestaties. Als uw toepassing bijvoorbeeld een populaire website is met een gebruikersaanmelding, kan het een goede keuze zijn om de gebruikers-id te gebruiken als partitiesleutel.

#### <a name="hot-partitions"></a>Dynamische partities

Een dynamische partitie is een partitie die een niet-proportioneel percentage van het verkeer naar een account krijgt en waarvoor geen gelijke taakverdeling mogelijk is omdat het gaat om één partitie. In het algemeen worden op een van de volgende twee manieren dynamische partities gemaakt:

#### <a name="append-only-and-prepend-only-patterns"></a>De patronen 'alleen toevoegen' en 'alleen laten voorafgaan'

Het patroon ' alleen toevoegen ' is een patroon waarbij alle (of bijna alle) verkeer naar een bepaalde partitiesleutel toeneemt en afneemt op basis van de actuele tijd. Stel bijvoorbeeld dat uw toepassing de huidige datum als partitiesleutel gebruikt voor logboekgegevens. Dit ontwerp heeft tot gevolg dat alle invoegingen naar de laatste partitie in de tabel worden verzonden, waardoor een goede taakverdeling niet mogelijk is. Als het volume van het verkeer naar die partitie het schaalbaarheidsdoel op partitieniveau overschrijdt, resulteert dit in een beperking. Het is beter om ervoor te zorgen dat verkeer naar meerdere partities wordt verzonden om een taakverdeling mogelijk te maken voor de aanvragen in uw tabel.

#### <a name="high-traffic-data"></a>Gegevens met intensief verkeer

Als uw partitieschema resulteert in een enkele partitie met alleen gegevens die veel meer worden gebruikt dan andere partities, kunt u ook met een beperking te maken krijgen wanneer die partitie het schaalbaarheidsdoel voor één partitie nadert. Het is beter om ervoor te zorgen dat geen enkele partitie de schaalbaarheidsdoelen nadert als gevolg van uw partitieschema.

### <a name="querying"></a>Uitvoeren van query's

In deze sectie worden bewezen procedures beschreven voor het uitvoeren van query's op de Table service.

#### <a name="query-scope"></a>Querybereik

Er zijn verschillende manieren om het bereik van de op te vragen entiteiten op te geven. In de volgende lijst worden alle opties voor het querybereik beschreven.

- **Puntquery's:** : een puntquery haalt precies één entiteit op door zowel de partitiesleutel als de rijsleutel op te geven van de entiteit die moet worden opgehaald. Deze query's zijn efficiënt en u moet ze waar mogelijk gebruiken.
- **Partitiequery's:** Een partitiequery is een query waarmee een set gegevens wordt opgehaald die een gemeenschappelijke partitiesleutel deelt. Normaal gesproken specificeert de query een reeks rijsleutelwaarden of een reeks waarden voor een bepaalde entiteitseigenschap naast een partitiesleutel. Deze query's zijn minder efficiënt dan puntquery's en moeten spaarzaam worden gebruikt.
- **Tabelquery's:** Een tabelquery is een query die een set gegevens ophaalt die geen gemeenschappelijke partitiesleutel deelt. Deze query's zijn niet efficiënt en u moet ze zo mogelijk vermijden.

U kunt in het algemeen het beste scans vermijden (query's die groter zijn dan één entiteit), maar als u moet scannen, kunt u het beste uw gegevens zo indelen dat uw scans de gegevens ophalen die u nodig hebt zonder dat grote hoeveelheden onnodige entiteiten hoeven te worden gescand of geretourneerd.

#### <a name="query-density"></a>Querydichtheid

Een andere belangrijke factor in queryefficiëntie is het aantal entiteiten dat wordt geretourneerd in vergelijking met het aantal gescande entiteiten om de geretourneerde set te vinden. Als uw toepassing een tabelquery uitvoert met een filter voor een eigenschapswaarde die slechts door 1% van de gegevens wordt gedeeld, scant de query 100 entiteiten voor elke entiteit die wordt geretourneerd. De eerder besproken schaalbaarheidsdoelen voor tabellen zijn allemaal gerelateerd aan het aantal gescande entiteiten en niet het aantal geretourneerde entiteiten: met een lage querydichtheid kan de Table service eenvoudig voor vertraging zorgen in uw toepassing omdat deze zo veel entiteiten moet scannen om de entiteit die u zoekt op te halen. Raadpleeg de sectie met de titel [Denormalisatie](#denormalization) voor meer informatie over hoe u beperkingen kunt voorkomen.

#### <a name="limiting-the-amount-of-data-returned"></a>De hoeveelheid geretourneerde gegevens beperken

Wanneer u weet dat een query entiteiten retourneert die u niet nodig hebt in de clienttoepassing, kunt u een filter gebruiken om de grootte van de geretourneerde set te reduceren. Hoewel de entiteiten die niet naar de client worden geretourneerd nog steeds betrekking hebben op de schaalbaarheidslimieten, worden de prestaties van uw toepassingen verbeterd vanwege de gereduceerde payloadgrootte van het netwerk en het verminderde aantal entiteiten dat uw clienttoepassing moet verwerken. Houd er rekening mee dat de schaalbaarheidsdoelen te maken hebben met het aantal gescande entiteiten. Een query die veel entiteiten uitfiltert, kan echter nog steeds beperkingen tot gevolg hebben, zelfs als er slechts enkele entiteiten worden geretourneerd. Zie de sectie met de titel [Querydichtheid](#query-density) voor meer informatie over het efficiënt maken van query's.

Als uw clienttoepassing slechts een beperkt aantal eigenschappen van de entiteiten in uw tabel nodig heeft, kunt u projectie gebruiken om de grootte van de geretourneerde gegevensset te beperken. Net als bij het filteren wordt met projectie de belasting van het netwerk en de clientverwerking verminderd.

#### <a name="denormalization"></a>Denormalisatie

In tegenstelling tot het werken met relationele databases leiden de bewezen procedures voor het efficiënt opvragen van tabelgegevens ertoe dat uw gegevens worden gedenormaliseerd. Dat wil zeggen dat dezelfde gegevens in meerdere entiteiten worden gedupliceerd (één voor elke sleutel die u kunt gebruiken om de gegevens te vinden) om het aantal entiteiten tot het minimum te beperken dat een query moet scannen om de gegevens te vinden die de client nodig heeft, in plaats van een grote aantallen entiteiten te scannen om de gegevens te vinden die nodig zijn voor uw toepassing. Zo wilt u bijvoorbeeld in een e-commercewebsite misschien een bestelling vinden op klant-id (geef mij de bestellingen van deze klant) en op datum (geef mij bestellingen van een bepaalde datum). In Table Storage kunt u het beste de entiteit (of een verwijzing ernaar) twee keer opslaan: eenmaal op tabelnaam, PK en RK om de klant-id eenvoudiger te vinden, en eenmaal om deze gemakkelijker op datum te vinden.  

### <a name="insert-update-and-delete"></a>Invoegen, bijwerken en verwijderen

In deze sectie worden bewezen procedures beschreven voor het wijzigen van query's die zijn opgeslagen in de Table service.  

#### <a name="batching"></a>Batchverwerking

Batchtransacties worden ook wel entiteitsgroepstransacties genoemd in Azure Storage. Alle bewerkingen binnen een entiteitsgroepstransactie moeten op één partitie in één tabel staan. Gebruik waar mogelijk entiteitsgroepstransacties om invoeg-, bijwerk- en verwijderbewerkingen in batches uit te voeren. Het gebruik van entiteitsgroepstransacties vermindert het aantal retouren van uw clienttoepassing naar de server, vermindert het aantal factureerbare transacties (een entiteitsgroepstransactie telt als één transactie voor factureringsdoeleinden en kan maximaal 100 opslagbewerkingen bevatten), en schakelt atomische updates in (alle bewerkingen zijn geslaagd of zijn allemaal mislukt binnen een entiteitsgroepstransactie). Omgevingen met hoge latentie, zoals mobiele apparaten, profiteren aanzienlijk van het gebruik van entiteitsgroepstransacties.  

#### <a name="upsert"></a>Upsert

Gebruik waar mogelijk de tabelbewerkingen voor **Upsert**. Er zijn twee soorten **Upsert**, die beide efficiënter kunnen zijn dan traditionele bewerkingen voor **invoegen** en **bijwerken**:  

- **InsertOrMerge**: Gebruik deze bewerking wanneer u een subset van de eigenschappen van de entiteit wilt uploaden, maar niet zeker weet of de entiteit al bestaat. Als de entiteit bestaat, worden met deze aanroep de eigenschappen die zijn opgenomen in de **Upsert**-bewerking bijgewerkt en blijven alle bestaande eigenschappen ongewijzigd. Als de entiteit niet bestaat, wordt de nieuwe entiteit ingevoegd. Dit is vergelijkbaar met het gebruik van projectie in een query, in zoverre dat u alleen de eigenschappen hoeft te uploaden die worden gewijzigd.
- **InsertOrReplace**: Gebruik deze bewerking wanneer u een geheel nieuwe entiteit wilt uploaden, maar u niet zeker weet of deze al bestaat. Gebruik deze bewerking wanneer u weet dat de zojuist geüploade entiteit volledig juist is, omdat hiermee de oude entiteit volledig wordt overschreven. U wilt bijvoorbeeld de entiteit bijwerken waarin de huidige locatie van een gebruiker is opgeslagen, ongeacht of de toepassing eerder opgeslagen locatiegegevens van de gebruiker bevat. De nieuwe locatie-entiteit is compleet en u hebt dus geen gegevens nodig van een vorige entiteit.

#### <a name="storing-data-series-in-a-single-entity"></a>Gegevensreeksen opslaan in één entiteit

Soms slaat een toepassing een reeks gegevens op die regelmatig allemaal tegelijk moet worden opgehaald: een toepassing kan bijvoorbeeld het CPU-gebruik in de loop van de tijd volgen om een doorlopende grafiek van de gegevens in de afgelopen 24 uur te tekenen. Een manier is om één tabelentiteit per uur te hebben, waarbij elke entiteit een specifiek uur vertegenwoordigt waarin het CPU-gebruik voor dat uur wordt opgeslagen. Voor het uitzetten van deze gegevens moet de toepassing de entiteiten ophalen die de gegevens van de 24 meest recente uren bevatten.  

Het is ook mogelijk dat uw toepassing het CPU-gebruik voor elk uur opslaat als afzonderlijke eigenschap van één entiteit: uw toepassing kan elk uur bijwerken door via één **InsertOrMerge Upsert**-aanroep de waarde voor het meest recente uur bij te werken. Voor het uitzetten van de gegevens hoeft de toepassing slechts één entiteit op te halen in plaats van 24, waardoor u een efficiënte query krijgt. Zie de sectie met de titel [Querybereik](#query-scope) voor meer informatie over de efficiëntie van query's.

#### <a name="storing-structured-data-in-blobs"></a>Gestructureerde gegevens opslaan in blobs

Als u invoegacties in batches uitvoert en vervolgens reeksen entiteiten tegelijk ophaalt, kunt u overwegen om van blobs gebruik te maken in plaats van tabellen. Een goed voorbeeld hiervan is een logboekbestand. U kunt meerdere minuten aan logboeken in batches vastleggen, deze invoegen en vervolgens meerdere minuten aan logboeken tegelijk ophalen. In dit geval zijn de prestaties beter als u blobs gebruikt in plaats van tabellen, omdat u het aantal objecten waarnaar is geschreven of die zijn gelezen aanzienlijk kunt verminderen, en mogelijk ook het aantal aanvragen dat moet worden gedaan.  

## <a name="next-steps"></a>Volgende stappen

- [Schaalbaarheids- en prestatiedoelen voor Table-opslag](scalability-targets.md)
- [Schaalbaarheids- en prestatiedoelen voor standaardopslagaccounts](../common/scalability-targets-standard-account.md?toc=%2fazure%2fstorage%2ftables%2ftoc.json)
- [Status en foutcodes](/rest/api/storageservices/Status-and-Error-Codes2)