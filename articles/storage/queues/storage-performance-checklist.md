---
title: Controlelijst voor prestaties en schaalbaarheid van Queue Storage - Azure Storage
description: Een controlelijst met bewezen procedures voor het gebruik van Queue Storage in het ontwikkelen van toepassingen met hoge prestaties.
author: tamram
services: storage
ms.author: tamram
ms.date: 10/10/2019
ms.topic: overview
ms.service: storage
ms.subservice: queues
ms.custom: devx-track-csharp
ms.openlocfilehash: 4040a81d5b509ddbdd355953e28721a7c9fccfb8
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97585663"
---
<!-- docutune:casing "Timeout and Server Busy errors" -->

# <a name="performance-and-scalability-checklist-for-queue-storage"></a>Controlelijst voor prestaties en schaalbaarheid van Queue Storage

Microsoft heeft een aantal bewezen procedures ontwikkeld voor het ontwikkelen van hoogwaardige toepassingen met Queue Storage. Deze controlelijst vermeldt de belangrijkste procedures die ontwikkelaars kunnen volgen om de prestaties te optimaliseren. Neem bij het ontwerpen van uw toepassing en tijdens het gehele proces deze procedures in acht.

Azure Storage bevat schaalbaarheids- en prestatiedoelen voor capaciteit, transactiesnelheid en bandbreedte. Zie [Schaalbaarheids- en prestatiedoelen voor standaardopslagaccounts](../common/scalability-targets-standard-account.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json) en [Schaalbaarheids- en prestatiedoelen voor Queue Storage](scalability-targets.md) voor meer informatie over schaalbaarheidsdoelen van Azure Storage.

## <a name="checklist"></a>Controlelijst

In dit artikel worden bewezen procedures voor prestaties georganiseerd in een controlelijst die u kunt volgen tijdens het ontwikkelen van uw Queue Storage-toepassing.

| Gereed | Categorie | Ontwerpoverwegingen |
|--|--|--|
| &nbsp; | Schaalbaarheidsdoelen | [Kunt u uw toepassing zo ontwerpen dat deze niet meer dan het maximale aantal opslagaccounts gebruikt?](#maximum-number-of-storage-accounts) |
| &nbsp; | Schaalbaarheidsdoelen | [Wilt u niet de capaciteits- en transactielimieten benaderen?](#capacity-and-transaction-targets) |
| &nbsp; | Netwerken | [Hebben apparaten aan de clientzijde voldoende bandbreedte en lage latentie om de benodigde prestaties te verwezenlijken?](#throughput) |
| &nbsp; | Netwerken | [Hebben apparaten aan de clientzijde een netwerkkoppeling van hoge kwaliteit?](#link-quality) |
| &nbsp; | Netwerken | [Bevindt de clienttoepassing zich in dezelfde regio als de opslagaccount?](#location) |
| &nbsp; | Directe clienttoegang | [Maakt u gebruik van Shared Access Signatures (SAS) en Cross-Origin Resource Sharing (CORS) om directe toegang tot Azure Storage te krijgen?](#sas-and-cors) |
| &nbsp; | .NET-configuratie | [Gebruikt u .NET Core 2.1 of hoger voor optimale prestaties?](#use-net-core) |
| &nbsp; | .NET-configuratie | [Hebt u uw client geconfigureerd voor het gebruik van een voldoende aantal gelijktijdige verbindingen?](#increase-default-connection-limit) |
| &nbsp; | .NET-configuratie | [Hebt u voor .NET-toepassingen .NET geconfigureerd voor het gebruik van een voldoende aantal threads?](#increase-the-minimum-number-of-threads) |
| &nbsp; | Parallelle uitvoering | [Hebt u ervoor gezorgd dat parallelle uitvoering op de juiste wijze is gebonden, zodat de functionaliteit van uw client niet wordt overbelast of de schaalbaarheidsdoelen worden genaderd?](#unbounded-parallelism) |
| &nbsp; | Hulpprogramma's | [Gebruikt u de nieuwste versies van door Microsoft meegeleverde clientbibliotheken en hulpprogramma's?](#client-libraries-and-tools) |
| &nbsp; | Nieuwe pogingen | [Gebruikt u een beleid voor opnieuw proberen met exponentieel uitstel voor het beperken van fouten en time-outs?](#timeout-and-server-busy-errors) |
| &nbsp; | Nieuwe pogingen | [Voorkomt uw toepassing nieuwe pogingen voor fouten waarbij geen nieuwe pogingen mogelijk zijn?](#non-retryable-errors) |
| &nbsp; | Configuratie | [Hebt u het algoritme van Nagle uitgeschakeld om de prestaties van kleine aanvragen te verbeteren?](#disable-nagles-algorithm) |
| &nbsp; | Berichtgrootte | [Zijn uw berichten compact om de prestaties van de wachtrij te verbeteren?](#message-size) |
| &nbsp; | Bulksgewijs ophalen | [Haalt u meerdere berichten tegelijk op met één GET-bewerking?](#batch-retrieval) |
| &nbsp; | Pollingfrequentie | [Voert u vaak genoeg polling uit om de waargenomen latentie van uw toepassing te verminderen?](#queue-polling-interval) |
| &nbsp; | Updatebericht | [Voert u een bewerking voor bericht bijwerken uit om de voortgang op te slaan bij de verwerking van berichten, zodat u kunt voorkomen dat u het hele bericht opnieuw moet verwerken wanneer een fout zich voordoet?](#perform-an-update-message-operation) |
| &nbsp; | Architectuur | [Gebruikt u wachtrijen om uw hele toepassing schaalbaarder te maken, door workloads die lang duren buiten het kritieke pad te houden en vervolgens onafhankelijk te schalen?](#application-architecture) |

## <a name="scalability-targets"></a>Schaalbaarheidsdoelen

Als uw toepassing een van de schaalbaarheidsdoelen benadert of overschrijdt, kunnen er meer latenties in of beperkingen voor transacties optreden. Wanneer Azure Storage uw toepassing beperkt, begint de service met het retourneren van de foutcodes 503 (`Server Busy`) of 500 (`Operation Timeout`). Het vermijden van deze fouten door binnen de grenzen van de schaalbaarheidsdoelen te blijven vormt een belangrijk onderdeel van het verbeteren van de prestaties van uw toepassing.

Zie [Schaalbaarheids- en prestatiedoelen voor Azure Storage](./scalability-targets.md#scale-targets-for-queue-storage) voor meer informatie over de schaalbaarheidsdoelen voor Queue Storage.

### <a name="maximum-number-of-storage-accounts"></a>Maximumaantal opslagaccounts

Als u het maximumaantal opslagaccounts nadert dat is toegestaan voor een bepaalde combinatie van abonnement/regio, maakt u dan gebruik van meerdere opslagaccounts om in een shard op te slaan, waarmee de hoeveelheid inkomend/uitgaand verkeer, het aantal I/O-bewerkingen per seconde (IOPS) of de capaciteit wordt verhoogd? In dit scenario raadt Microsoft u aan gebruik te maken van verhoogde limieten voor opslagaccounts om zo mogelijk het aantal opslagaccounts te beperken dat is vereist voor uw workload. Neem contact op met [Ondersteuning voor Azure](https://azure.microsoft.com/support/options/) om verhoogde limieten voor uw opslagaccount aan te vragen. Zie [Grotere opslagaccounts met een hogere schaal](https://azure.microsoft.com/blog/announcing-larger-higher-scale-storage-accounts/) voor meer informatie.

### <a name="capacity-and-transaction-targets"></a>Capaciteits- en transactiedoelen

Als uw toepassing de schaalbaarheidsdoelen voor één opslagaccount nadert, kunt u een van de volgende benaderingen overwegen:

- Als de schaalbaarheidsdoelen voor wachtrijen niet voldoende zijn voor uw toepassing, moet u meerdere wachtrijen gebruiken en berichten over deze wachtrijen verdelen.
- Bekijk de workload die ervoor zorgt dat uw toepassing het schaalbaarheidsdoel benadert of overschrijdt. Kunt u het op een andere manier ontwerpen waardoor u minder bandbreedte of capaciteit of minder transacties gebruikt?
- Als uw toepassing een van de schaalbaarheidsdoelen moet overschrijden, maakt u meerdere opslagaccounts en partitioneert u uw toepassingsgegevens over deze meerdere opslagaccounts. Als u dit patroon gebruikt, moet u ervoor zorgen dat u uw toepassing zo ontwerpt, dat u in de toekomst meer opslagaccounts kunt toevoegen voor de taakverdeling. Opslagaccounts zelf hebben geen andere kosten dan uw gebruik in termen van opgeslagen gegevens, gemaakte transacties of overgedragen gegevens.
- Als uw toepassing de bandbreedtedoelen nadert, kunt u de gegevens aan de clientzijde comprimeren om de bandbreedte te verminderen die nodig is om de gegevens naar Azure Storage te verzenden. Hoewel met het comprimeren van gegevens bandbreedte kan worden bespaard en de netwerkprestaties worden verbeterd, kan dit ook negatieve gevolgen voor de prestaties hebben. Evalueer de invloed op de prestaties van de extra verwerkingsvereisten voor de compressie en decompressie van gegevens aan de clientzijde. Houd er rekening mee dat het door het opslaan van gecomprimeerde gegevens lastiger wordt om problemen op te lossen, aangezien het lastiger kan zijn om de gegevens te bekijken met behulp van standaardhulpprogramma's.
- Als uw toepassing de schaalbaarheidsdoelen nadert, moet u ervoor zorgen dat u een exponentieel uitstel gebruikt voor nieuwe pogingen. U kunt het beste voorkomen dat u de schaalbaarheidsdoelen bereikt door de aanbevelingen te implementeren die in dit artikel worden beschreven. Als u echter een exponentieel uitstel gebruikt voor nieuwe pogingen, voorkomt u dat uw toepassing snel opnieuw pogingen onderneemt, waarmee de beperkingen ernstiger kunnen worden. Raadpleeg de sectie [Time-out- en server-bezetfouten](#timeout-and-server-busy-errors) voor meer informatie.

## <a name="networking"></a>Netwerken

De beperkingen van het fysieke netwerk van de toepassing kunnen een grote invloed hebben op de prestaties. In de volgende secties worden enkele beperkingen beschreven die gebruikers kunnen tegenkomen.

### <a name="client-network-capability"></a>Netwerkmogelijkheden van de client

De bandbreedte en de kwaliteit van de netwerkkoppeling spelen een belangrijke rol in prestaties van toepassingen, zoals beschreven in de volgende secties.

#### <a name="throughput"></a>Doorvoer

Voor bandbreedte is het probleem vaak de mogelijkheden van de client. Grotere Azure-exemplaren hebben NIC's met een grotere capaciteit. U moet dus overwegen een grotere exemplaar of meer VM's te gebruiken als u hogere netwerklimieten voor één computer nodig hebt. Als u Azure Storage opent vanuit een on-premises toepassing, geldt dezelfde regel: zorg ervoor dat u inzicht krijgt in de netwerkmogelijkheden van het clientapparaat en de netwerkverbinding met de Azure Storage-locatie en verbeter deze, indien nodig. U kunt ook uw toepassing zo ontwerpen dat deze binnen deze mogelijkheden kan werken.

#### <a name="link-quality"></a>Kwaliteit van de koppeling

Net als bij elk netwerkgebruik moet u er rekening mee houden dat de netwerkomstandigheden die fouten en pakketverlies veroorzaken, ervoor zorgen dat de effectieve doorvoer wordt vertraagd. Het gebruik van WireShark of Network Monitor kan helpen bij het vaststellen van dit probleem.

### <a name="location"></a>Locatie

Wanneer u de client dicht bij de server plaatst, levert dit in een gedistribueerde omgeving de beste prestaties op. Als u toegang wilt tot Azure Storage met de laagste latentie, bevindt de beste locatie voor uw client zich in dezelfde Azure-regio. Als u bijvoorbeeld een Azure-web-app hebt die gebruikmaakt van Azure Storage, plaatst u deze beide binnen één regio, zoals VS - west of Azië - zuidoost. Wanneer u resources bij elkaar plaatst, vermindert u de latentie en de kosten, omdat het bandbreedtegebruik binnen één regio gratis is.

Als clienttoepassingen toegang krijgen tot Azure Storage, maar niet worden gehost in Azure, zoals apps voor mobiele apparaten of on-premises bedrijfsservices, kan de latentie worden verminderd door het opslagaccount in een regio in de buurt van die clients te plaatsen. Als uw clients breed worden gedistribueerd (bijvoorbeeld sommige in Noord-Amerika en sommige in Europa), kunt u een opslagaccount per regio gebruiken. Deze aanpak is eenvoudiger te implementeren als de gegevens die de app opslaat specifiek zijn voor afzonderlijke gebruikers en als er geen replicatie van gegevens nodig is tussen opslagaccounts.

## <a name="sas-and-cors"></a>SAS en CORS

Stel dat u code moet machtigen zoals JavaScript dat wordt uitgevoerd in de webbrowser van een gebruiker of in een app op een mobiele telefoon om toegang te krijgen tot gegevens in Azure Storage. Een aanpak is om een servicetoepassing te compileren die als een proxy fungeert. Het apparaat van de gebruiker wordt geverifieerd bij de service, die op zijn beurt toegang tot Azure Storage-resources verleent. Op deze manier kunt u voorkomen dat de sleutels van uw opslagaccount op onveilige apparaten worden weergegeven. Met deze benadering wordt echter een aanzienlijke overhead op de servicetoepassing geplaatst, omdat alle gegevens die worden overgedragen tussen het apparaat van de gebruiker en Azure Storage via de servicetoepassing moeten worden doorgegeven.

U kunt voorkomen dat een servicetoepassing wordt gebruikt als een proxy voor Azure Storage met behulp van Shared Access Signatures (SAS). Met SAS kunt u het apparaat van uw gebruiker in staat stellen om aanvragen rechtstreeks bij Azure Storage in te dienen met behulp van een beperkt toegangstoken. Als een gebruiker bijvoorbeeld een foto wil uploaden naar uw toepassing, kan uw servicetoepassing een SAS genereren en deze verzenden naar het apparaat van de gebruiker. Het SAS-token kan toestemming geven om naar een Azure Storage-resource te schrijven gedurende een opgegeven tijdsinterval, waarna het SAS-token verloopt. Zie [Beperkte toegang verlenen tot Azure Storage-resources via SAS (Shared Access Signatures)](../common/storage-sas-overview.md) voor meer informatie over SAS.

Normaal gesproken staat een webbrowser geen JavaScript toe op een pagina die wordt gehost door een website op het ene domein om bepaalde bewerkingen, zoals schrijfbewerkingen, uit te voeren op het andere domein. Op basis van dit beleid, dat bekend staat als 'beleid voor zelfde oorsprong', wordt voorkomen dat een schadelijk script op één pagina de toegang tot gegevens op een andere webpagina kan verkrijgen. Hetzelfde 'beleid voor zelfde oorsprong' kan echter een beperking zijn bij het compileren van een oplossing in de cloud. Cross-Origin Resource Sharing (CORS) is een browserfunctie waarmee het doeldomein van het brondomein afkomstige aanvragen kan doorgeven aan een vertrouwde browser.

Stel bijvoorbeeld dat een webtoepassing die in Azure wordt uitgevoerd een aanvraag voor een resource indient bij een Azure Storage-account. De webtoepassing is het brondomein en het opslagaccount is het doeldomein. U kunt CORS voor elk van de Azure Storage-services configureren om met de webbrowser aanvragen door te geven vanuit het brondomein dat worden vertrouwd door Azure Storage. Zie [CORS-ondersteuning (cross-origin resource sharing) voor Azure Storage](/rest/api/storageservices/Cross-Origin-Resource-Sharing--CORS--Support-for-the-Azure-Storage-Services) voor meer informatie over CORS.

Met SAS en CORS kunt u voorkomen dat uw webtoepassing onnodig wordt belast.

## <a name="net-configuration"></a>.NET-configuratie

Als u het .NET Framework gebruikt, worden in deze sectie verschillende snelle configuratie-instellingen weergegeven die u kunt gebruiken om belangrijke verbeteringen in de prestaties aan te brengen. Als u andere talen gebruikt, controleert u of er soortgelijke concepten van toepassing zijn in de taal die u hebt gekozen.

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

### <a name="increase-the-minimum-number-of-threads"></a>Minimum aantal threads verhogen

Als u synchrone aanroepen gebruikt in combinatie met asynchrone taken, kunt u misschien beter het aantal threads in de threadpool verhogen:

```csharp
ThreadPool.SetMinThreads(100,100); //(Determine the right number for your application)  
```

Zie de methode [`ThreadPool.SetMinThreads`](/dotnet/api/system.threading.threadpool.setminthreads) voor meer informatie.

## <a name="unbounded-parallelism"></a>Niet-gebonden parallelle uitvoering

Parallelle uitvoering kan ideaal zijn voor de prestaties, maar wees voorzichtig met het gebruik van een ongebonden parallelle uitvoering, wat betekent dat er geen limiet wordt afgedwongen voor het aantal threads of parallelle aanvragen. Zorg ervoor dat u parallelle aanvragen beperkt tot het uploaden of downloaden van gegevens, om toegang te krijgen tot meerdere partities in hetzelfde opslagaccount of om toegang te krijgen tot meerdere items in dezelfde partitie. Als parallelle uitvoering niet is gebonden, kan uw toepassing de mogelijkheden van het clientapparaat of de schaalbaarheidsdoelen van het opslagaccount overschrijden, wat resulteert in langere latenties en bandbreedtebeperking.

## <a name="client-libraries-and-tools"></a>Clientbibliotheken en hulpprogramma's

Gebruik altijd de nieuwste clientbibliotheken en hulpprogramma's van Microsoft voor de beste prestaties. Azure Storage-clientbibliotheken zijn beschikbaar in diverse talen. Azure Storage biedt ook ondersteuning voor PowerShell en Azure CLI. Microsoft ontwikkelt deze clientbibliotheken en hulpprogramma's actief met het oog op de prestaties, houdt ze up-to-date met de nieuwste serviceversies en zorgt ervoor dat ze een groot aantal van de bewezen uitvoeringspraktijken intern verwerken. Zie de referentiedocumentatie bij [Azure Storage](./reference.md) voor meer informatie.

## <a name="handle-service-errors"></a>Servicefouten afhandelen

Azure Storage retourneert een fout wanneer de service een aanvraag niet kan verwerken. Voor het optimaliseren van de prestaties is het handig om inzicht te hebben in de fouten die door Azure Storage in een bepaald scenario kunnen worden geretourneerd.

### <a name="timeout-and-server-busy-errors"></a>Time-out- en server-bezetfouten

Azure Storage kan uw toepassing beperken als deze de schaalbaarheidslimieten nadert. In sommige gevallen kan Azure Storage mogelijk geen aanvragen verwerken vanwege een tijdelijke omstandigheid. In beide gevallen kan de service een fout 503 (`Server Busy`) of 500 (`Timeout`) retourneren. Deze fouten kunnen zich ook voordoen als de service gegevenspartities opnieuw moet verdelen voor een hogere doorvoer. De clienttoepassing moet meestal de bewerking opnieuw uitvoeren die een van deze fouten heeft veroorzaakt. Als Azure Storage echter uw toepassing beperkt omdat deze de schaalbaarheidsdoelen overschrijdt of als de service om een andere reden zelfs niet aan de aanvraag kan voldoen, kunnen agressieve nieuwe pogingen het probleem erger maken. Het gebruik van een beleid voor opnieuw proberen met exponentieel uitstel wordt aanbevolen. De clientbibliotheken zijn standaard ingesteld op dit gedrag. Uw toepassing kan bijvoorbeeld na 2 seconden een nieuwe poging doen, daarna na 4 seconden, daarna na 10 seconden, daarna na 30 seconden, en vervolgens volledig opgeven. Op deze manier vermindert uw toepassing de belasting van de service aanzienlijk, in plaats van verergerend gedrag te vertonen dat kan leiden tot aanvraagbeperking.

Bij connectiviteitsfouten kunnen onmiddellijk nieuwe pogingen worden gedaan, omdat ze niet het gevolg zijn van een beperking en naar verwachting tijdelijk zijn.

### <a name="non-retryable-errors"></a>Fouten waarbij geen nieuwe pogingen mogelijk zijn

Bij de clientbibliotheken is bij het afhandelen van nieuwe pogingen bekend bij welke fouten opnieuw kan worden geprobeerd en welke niet. Als u echter de Azure Storage REST API rechtstreeks aanroept, zijn er enkele fouten waarbij u geen nieuwe pogingen moet ondernemen. De fout 400 (`Bad Request`) geeft bijvoorbeeld aan dat de clienttoepassing een aanvraag heeft verzonden die niet kan worden verwerkt omdat deze niet in de verwachte vorm is opgemaakt. Als deze aanvraag opnieuw wordt verzonden, resulteert dit steeds in hetzelfde antwoord, zodat het zinloos is om een nieuwe poging te ondernemen. Als u de REST API Azure Storage rechtstreeks aanroept, moet u rekening houden met mogelijke fouten en of er nieuwe pogingen moeten worden gedaan.

Zie [Status en foutcodes](/rest/api/storageservices/status-and-error-codes2) voor meer informatie over Azure Storage-foutcodes.

## <a name="disable-nagles-algorithm"></a>Algoritme van Nagle uitschakelen

Het Nagle-algoritme is breed geïmplementeerd in TCP/IP-netwerken om de netwerkprestaties te verbeteren. Het is echter niet optimaal in alle omstandigheden (zoals in uiterst interactieve omgevingen). Het algoritme van Nagle heeft een negatieve invloed op de prestaties van aanvragen voor Azure Table Storage, en moet zo mogelijk worden uitgeschakeld.

## <a name="message-size"></a>Berichtgrootte

Naarmate de berichtgrootte toeneemt, nemen de wachtrijprestaties en schaalbaarheid af. Plaats in een bericht alleen informatie die de ontvanger daadwerkelijk nodig heeft.

## <a name="batch-retrieval"></a>Bulksgewijs ophalen

U kunt in één bewerking maximaal 32 berichten in een wachtrij ophalen. Door bulksgewijs berichten op te halen, hoeft u minder vaak terug naar de clienttoepassing. Dit is met name handig voor omgevingen met een hoge latentie, zoals mobiele apparaten.

## <a name="queue-polling-interval"></a>Polling-interval voor de wachtrij

Bij de meeste toepassingen wordt polling naar berichten in een wachtrij uitgevoerd. Dit kan al snel een van de grootste transactiebronnen voor die toepassingen worden. Wees verstandig bij het selecteren van uw polling-interval: door te vaak polling uit te voeren, is de kans groot dat de schaalbaarheidsdoelen voor de wachtrij worden bereikt voor uw toepassing. Maar met 200.000 transacties à USD 0,01 (ten tijde van dit schrijven) zou één processorpolling per seconde gedurende één maand goedkoper zijn dan 15 cent, dus de kosten zijn doorgaans geen factor die invloed heeft op het gekozen polling-interval.

Zie [Prijzen voor Azure Storage](https://azure.microsoft.com/pricing/details/storage/) voor de actuele prijsgegevens.

## <a name="perform-an-update-message-operation"></a>Een bewerking voor bericht bijwerken uitvoeren

U kunt een bewerking voor bericht bijwerken gebruiken om de onzichtbaarheidstime-out te verhogen of om de statusgegevens van een bericht bij te werken. Soms is deze benadering efficiënter dan een werkstroom die een taak van de ene aan de andere wachtrij doorgeeft bij elke voltooide stap van de taak. Uw toepassing kan de taakstatus bij het bericht opslaan en vervolgens doorwerken, in plaats van steeds het bericht voor de volgende stap opnieuw in de wachtrij te plaatsen wanneer er weer een stap is voltooid. Vergeet niet dat elke bewerking voor bericht bijwerken meetelt voor het schaalbaarheidsdoel.

## <a name="application-architecture"></a>Toepassingsarchitectuur

Gebruik wachtrijen om de architectuur van uw toepassing schaalbaar te maken. Hieronder volgt een aantal manieren waarop u uw toepassing met behulp van wachtrijen schaalbaarder kunt maken:

- U kunt wachtrijen gebruiken om achterstanden voor verwerking te verzamelen en de workloads in uw toepassing soepeler laten verlopen. U kunt bijvoorbeeld aanvragen van gebruikers in een wachtrij plaatsen om eerst werkzaamheden uit te voeren die veel van de processor vergen, zoals het aanpassen van de grootte van geüploade afbeeldingen.
- U kunt wachtrijen gebruiken om de koppeling van onderdelen van uw toepassing te verbreken, zodat u de schaal van die onderdelen onafhankelijk kunt aanpassen. Met een webfront kunt u bijvoorbeeld enquêteresultaten van gebruikers in een wachtrij plaatsen, zodat u ze later kunt analyseren en opslaan. U kunt meer werkrolexemplaren toevoegen om de gegevens in de wachtrij naar wens te verwerken.

## <a name="next-steps"></a>Volgende stappen

- [Schaalbaarheids- en prestatiedoelen voor Queue Storage](scalability-targets.md)
- [Schaalbaarheids- en prestatiedoelen voor standaardopslagaccounts](../common/scalability-targets-standard-account.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)
- [Status en foutcodes](/rest/api/storageservices/Status-and-Error-Codes2)
