---
title: Controle lijst voor prestaties en schaal baarheid voor Blob Storage-Azure Storage
description: Een controle lijst met bewezen procedures voor het gebruik van Blob Storage in het ontwikkelen van toepassingen met hoge prestaties.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 10/10/2019
ms.author: tamram
ms.subservice: blobs
ms.custom: devx-track-csharp
ms.openlocfilehash: 14da8b6cb695703f1881b6b0b9858772bde386c5
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "95544748"
---
# <a name="performance-and-scalability-checklist-for-blob-storage"></a>Controlelijst voor prestaties en schaalbaarheid voor Blob-opslag

Micro soft heeft een aantal bewezen prak tijken ontwikkeld voor het ontwikkelen van toepassingen met hoge prestaties met Blob Storage. Deze controlelijst vermeldt de belangrijkste procedures die ontwikkelaars kunnen volgen om de prestaties te optimaliseren. Neem bij het ontwerpen van uw toepassing en tijdens het gehele proces deze procedures in acht.

Azure Storage bevat schaalbaarheids- en prestatiedoelen voor capaciteit, transactiesnelheid en bandbreedte. Zie [schaalbaarheids-en prestatie doelen voor standaard opslag accounts](../common/scalability-targets-standard-account.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) en [schaal baarheid en prestatie doelen voor Blob Storage](scalability-targets.md)voor meer informatie over Azure Storage schaalbaarheids doelen.

## <a name="checklist"></a>Controlelijst

In dit artikel worden bewezen procedures voor het uitvoeren van prestaties in een controle lijst georganiseerd die u kunt volgen tijdens het ontwikkelen van uw Blob Storage-toepassing.

| Gereed | Categorie | Ontwerpoverwegingen |
| --- | --- | --- |
| &nbsp; |Schaalbaarheidsdoelen |[Kunt u uw toepassing zo ontwerpen dat deze niet meer dan het maximale aantal opslagaccounts gebruikt?](#maximum-number-of-storage-accounts) |
| &nbsp; |Schaalbaarheidsdoelen |[Wilt u niet de capaciteits- en transactielimieten benaderen?](#capacity-and-transaction-targets) |
| &nbsp; |Schaalbaarheidsdoelen |[Is een groot aantal clients gelijktijdig toegang tot één BLOB?](#multiple-clients-accessing-a-single-blob-concurrently) |
| &nbsp; |Schaalbaarheidsdoelen |[Blijft uw toepassing binnen de schaalbaarheids doelen voor één BLOB?](#bandwidth-and-operations-per-blob) |
| &nbsp; |Partitionering |[Is uw naamgevings Conventie ontworpen om een betere taak verdeling te bieden?](#partitioning) |
| &nbsp; |Netwerken |[Hebben apparaten aan de clientzijde voldoende bandbreedte en lage latentie om de benodigde prestaties te verwezenlijken?](#throughput) |
| &nbsp; |Netwerken |[Hebben apparaten aan de clientzijde een netwerkkoppeling van hoge kwaliteit?](#link-quality) |
| &nbsp; |Netwerken |[Bevindt de clienttoepassing zich in dezelfde regio als de opslagaccount?](#location) |
| &nbsp; |Directe clienttoegang |[Maakt u gebruik van Shared Access Signatures (SAS) en Cross-Origin Resource Sharing (CORS) om directe toegang tot Azure Storage te krijgen?](#sas-and-cors) |
| &nbsp; |Caching |[Worden de gegevens in de toepassing opgeslagen die regel matig worden geopend en zelden worden gewijzigd?](#reading-data) |
| &nbsp; |Caching |[Worden de updates voor de toepassing in de cache opgeslagen op de client en vervolgens geüpload in grotere sets?](#uploading-data-in-batches) |
| &nbsp; |.NET-configuratie |[Gebruikt u .NET Core 2.1 of hoger voor optimale prestaties?](#use-net-core) |
| &nbsp; |.NET-configuratie |[Hebt u uw client geconfigureerd voor het gebruik van een voldoende aantal gelijktijdige verbindingen?](#increase-default-connection-limit) |
| &nbsp; |.NET-configuratie |[Hebt u voor .NET-toepassingen .NET geconfigureerd voor het gebruik van een voldoende aantal threads?](#increase-minimum-number-of-threads) |
| &nbsp; |Parallelle uitvoering |[Hebt u ervoor gezorgd dat parallelle uitvoering op de juiste wijze is gebonden, zodat de functionaliteit van uw client niet wordt overbelast of de schaalbaarheidsdoelen worden genaderd?](#unbounded-parallelism) |
| &nbsp; |Hulpprogramma's |[Gebruikt u de nieuwste versies van door Microsoft meegeleverde clientbibliotheken en hulpprogramma's?](#client-libraries-and-tools) |
| &nbsp; |Nieuwe pogingen |[Gebruikt u een beleid voor opnieuw proberen met exponentieel uitstel voor het beperken van fouten en time-outs?](#timeout-and-server-busy-errors) |
| &nbsp; |Nieuwe pogingen |[Voorkomt uw toepassing nieuwe pogingen voor fouten waarbij geen nieuwe pogingen mogelijk zijn?](#non-retryable-errors) |
| &nbsp; |Blobs kopiëren |[Worden de blobs op de meest efficiënte manier gekopieerd?](#blob-copy-apis) |
| &nbsp; |Blobs kopiëren |[Gebruikt u de meest recente versie van AzCopy voor bulk Kopieer bewerkingen?](#use-azcopy) |
| &nbsp; |Blobs kopiëren |[Gebruikt u de Azure Data Box-serie voor het importeren van grote hoeveel heden gegevens?](#use-azure-data-box) |
| &nbsp; |Inhouds distributie |[Gebruikt u een CDN voor inhouds distributie?](#content-distribution) |
| &nbsp; |Meta gegevens gebruiken |[Slaat u veelgebruikte meta gegevens over blobs op in hun meta gegevens?](#use-metadata) |
| &nbsp; |Snel uploaden |[Als u probeert een BLOB snel te uploaden, kunt u blokken parallel uploaden?](#upload-one-large-blob-quickly) |
| &nbsp; |Snel uploaden |[Kunt u blobs parallel uploaden wanneer u een groot aantal blobs wilt uploaden?](#upload-many-blobs-quickly) |
| &nbsp; |Blob-type |[Gebruikt u pagina-blobs of blok-blobs wanneer dit van toepassing is?](#choose-the-correct-type-of-blob) |

## <a name="scalability-targets"></a>Schaalbaarheidsdoelen

Als uw toepassing een van de schaalbaarheidsdoelen benadert of overschrijdt, kunnen er meer latenties in of beperkingen voor transacties optreden. Wanneer Azure Storage uw toepassing beperkt, begint de service met het retourneren van de foutcodes 503 (server bezet) of 500 (time-out voor bewerking). Het vermijden van deze fouten door binnen de grenzen van de schaalbaarheidsdoelen te blijven vormt een belangrijk onderdeel van het verbeteren van de prestaties van uw toepassing.

Zie [Schaalbaarheids- en prestatiedoelen voor Azure Storage](../queues/scalability-targets.md#scale-targets-for-queue-storage) voor meer informatie over de schaalbaarheidsdoelen voor de Queue-service.

### <a name="maximum-number-of-storage-accounts"></a>Maximumaantal opslagaccounts

Als u het maximum aantal opslag accounts nadert dat is toegestaan voor een bepaalde combi natie van abonnement/regio, evalueert u uw scenario en bepaalt u of een van de volgende voor waarden van toepassing is:

- Gebruikt u opslag accounts voor het opslaan van niet-beheerde schijven en het toevoegen van die schijven aan uw virtuele machines (Vm's)? Voor dit scenario raadt micro soft aan om beheerde schijven te gebruiken. Beheerde schijven kunnen automatisch worden geschaald en zonder dat u afzonderlijke opslag accounts hoeft te maken en beheren. Zie [Introduction to Azure Managed disks](../../virtual-machines/managed-disks-overview.md) (Engelstalig) voor meer informatie
- Gebruikt u één opslag account per klant, voor het doel van gegevens isolatie? Voor dit scenario raadt micro soft aan om een BLOB-container te gebruiken voor elke klant, in plaats van een hele opslag account. Azure Storage kunt u nu Azure-rollen per container toewijzen. Zie [de Azure Portal gebruiken om een Azure-rol toe te wijzen voor toegang tot Blob-en wachtrij gegevens](../common/storage-auth-aad-rbac-portal.md)voor meer informatie.
- Gebruikt u meerdere opslag accounts om Shard uit te breiden, uitvoer, I/O-bewerkingen per seconde (IOPS) of capaciteit? In dit scenario raadt Microsoft u aan gebruik te maken van verhoogde limieten voor opslagaccounts om zo mogelijk het aantal opslagaccounts te beperken dat is vereist voor uw workload. Neem contact op met [Ondersteuning voor Azure](https://azure.microsoft.com/support/options/) om verhoogde limieten voor uw opslagaccount aan te vragen. Zie [Grotere opslagaccounts met een hogere schaal](https://azure.microsoft.com/blog/announcing-larger-higher-scale-storage-accounts/) voor meer informatie.

### <a name="capacity-and-transaction-targets"></a>Capaciteits- en transactiedoelen

Als uw toepassing de schaalbaarheidsdoelen voor één opslagaccount nadert, kunt u een van de volgende benaderingen overwegen:  

- Als uw toepassing het doel van de trans actie opneemt, kunt u gebruikmaken van blok-Blob Storage-accounts, die zijn geoptimaliseerd voor hoge transactie tarieven en lage en consistente latentie. Zie [Overzicht van Azure-opslagaccount](../common/storage-account-overview.md) voor meer informatie.
- Bekijk de workload die ervoor zorgt dat uw toepassing het schaalbaarheidsdoel benadert of overschrijdt. Kunt u het op een andere manier ontwerpen waardoor u minder bandbreedte of capaciteit of minder transacties gebruikt?
- Als uw toepassing een van de schaalbaarheidsdoelen moet overschrijden, maakt u meerdere opslagaccounts en partitioneert u uw toepassingsgegevens over deze meerdere opslagaccounts. Als u dit patroon gebruikt, moet u ervoor zorgen dat u uw toepassing zo ontwerpt, dat u in de toekomst meer opslagaccounts kunt toevoegen voor de taakverdeling. Opslagaccounts zelf hebben geen andere kosten dan uw gebruik in termen van opgeslagen gegevens, gemaakte transacties of overgedragen gegevens.
- Als uw toepassing de bandbreedtedoelen nadert, kunt u de gegevens aan de clientzijde comprimeren om de bandbreedte te verminderen die nodig is om de gegevens naar Azure Storage te verzenden.
    Hoewel met het comprimeren van gegevens bandbreedte kan worden bespaard en de netwerkprestaties worden verbeterd, kan dit ook negatieve gevolgen voor de prestaties hebben. Evalueer de invloed op de prestaties van de extra verwerkingsvereisten voor de compressie en decompressie van gegevens aan de clientzijde. Houd er rekening mee dat het door het opslaan van gecomprimeerde gegevens lastiger wordt om problemen op te lossen, aangezien het lastiger kan zijn om de gegevens te bekijken met behulp van standaardhulpprogramma's.
- Als uw toepassing de schaalbaarheidsdoelen nadert, moet u ervoor zorgen dat u een exponentieel uitstel gebruikt voor nieuwe pogingen. U kunt het beste voorkomen dat u de schaalbaarheidsdoelen bereikt door de aanbevelingen te implementeren die in dit artikel worden beschreven. Als u echter een exponentieel uitstel gebruikt voor nieuwe pogingen, voorkomt u dat uw toepassing snel opnieuw pogingen onderneemt, waarmee de beperkingen ernstiger kunnen worden. Zie de sectie met de titel [Time-out- en server-bezetfouten](#timeout-and-server-busy-errors) voor meer informatie.

### <a name="multiple-clients-accessing-a-single-blob-concurrently"></a>Meerdere clients hebben gelijktijdig toegang tot een enkele BLOB

Als u een groot aantal clients tegelijkertijd toegang tot één BLOB wilt geven, moet u rekening houden met zowel een BLOB als een schaal baarheid voor het opslag account. Het exacte aantal clients dat toegang heeft tot één blob, varieert afhankelijk van factoren zoals het aantal clients dat de BLOB tegelijk aanvraagt, de grootte van de BLOB en netwerk omstandigheden.

Als de Blob kan worden gedistribueerd via een CDN, zoals afbeeldingen of Video's die van een website worden bediend, kunt u een CDN gebruiken. Zie de sectie getiteld [Content Distribution](#content-distribution)(Engelstalig) voor meer informatie.

In andere scenario's, zoals weten schappelijke simulaties waarin de gegevens vertrouwelijk zijn, hebt u twee opties. De eerste is om de toegang van uw werk belasting te spreiden, zodat de BLOB gedurende een bepaalde tijd toegankelijk is en tegelijkertijd wordt geopend. U kunt de BLOB ook tijdelijk kopiëren naar meerdere opslag accounts om het totale aantal IOPS per Blob en andere opslag accounts te verhogen. De resultaten variëren afhankelijk van het gedrag van uw toepassing. Zorg er dus voor dat u gelijktijdigheids patronen tijdens het ontwerpen test.

### <a name="bandwidth-and-operations-per-blob"></a>Band breedte en bewerkingen per BLOB

Eén BLOB ondersteunt Maxi maal 500 aanvragen per seconde. Als u meerdere clients hebt die dezelfde BLOB moeten lezen en u deze limiet kunt overschrijden, kunt u overwegen een blok-Blob-opslag account te gebruiken. Een blok-Blob-opslag account biedt een hogere aanvraag frequentie of I/O-bewerkingen per seconde (IOPS).

U kunt ook een CDN (Content Delivery Network) gebruiken, zoals Azure CDN om bewerkingen op de BLOB te distribueren. Zie [Azure CDN Overview](../../cdn/cdn-overview.md)voor meer informatie over Azure CDN.  

## <a name="partitioning"></a>Partitionering

Meer informatie over hoe Azure Storage uw BLOB-gegevens partitioneert, is handig voor het verbeteren van de prestaties. Azure Storage kunnen gegevens sneller in één partitie leveren dan gegevens die meerdere partities omspannen. Als u de blobs op de juiste wijze benoemt, kunt u de efficiëntie van Lees aanvragen verbeteren.

Blob-opslag maakt gebruik van een partitie schema op basis van bereik voor schalen en taak verdeling. Elke BLOB heeft een partitie sleutel die bestaat uit de volledige naam van de BLOB (account + container + blob). De partitie sleutel wordt gebruikt voor het partitioneren van BLOB-gegevens in bereiken. De bereiken worden vervolgens gelijkmatig verdeeld over de Blob-opslag.

Partitioneren op basis van een bereik houdt in dat naam conventies die gebruikmaken van lexicale ordening (bijvoorbeeld *mypayroll*, *myperformance*, *myemployees*, enzovoort) of tijds tempels (*log20160101*, *log20160102*, *log20160102* enzovoort), meer zullen leiden tot de partities die zich op dezelfde partitie server bevinden. , totdat meer belasting nodig is, moeten ze worden opgesplitst in kleinere bereiken. Door co-locaties op dezelfde partitie server te plaatsen, verbetert u de prestaties, zodat een belang rijk onderdeel van de prestatie verbetering de naam blobs biedt op een manier die ze het meest effectief organiseert.

Alle blobs in een container kunnen bijvoorbeeld worden bediend door één server totdat de belasting voor deze blobs verdere herverdeling van de partitielay-bereiken vereist. Op dezelfde manier kan een groep met zeer bewaarde accounts met hun namen die zijn gerangschikt in lexicale volg orde worden bediend door één server totdat de belasting van een of meer van deze accounts moet worden verdeeld over meerdere partitie servers.

Elke bewerking voor taak verdeling kan van invloed zijn op de latentie van opslag aanroepen tijdens de bewerking. De mogelijkheid van de service om een plotselinge hoeveelheid verkeer naar een partitie af te handelen, wordt beperkt door de schaal baarheid van een enkele partitie server totdat de taak verdeling wordt geactiveerd en het partitie sleutel bereik opnieuw wordt gebalanceerd.

U kunt een aantal aanbevolen procedures volgen om de frequentie van dergelijke bewerkingen te verminderen.  

- Gebruik, indien mogelijk, Blob of blok grootten van meer dan 4 MiB voor standaard opslag accounts en meer dan 256 KiB voor Premium Storage-accounts. Grotere BLOB-of blok grootten activeren automatisch blok-blobs met hoge door voer. Blok-blobs met hoge door Voer bieden hoogwaardige opname die niet wordt beïnvloed door de naamgeving van partities.
- Bekijk de naamgevings Conventie die u gebruikt voor accounts, containers, blobs, tabellen en wacht rijen. Overweeg het vooraf bepalen van namen van accounts, containers of blobs met een hash met drie cijfers met een hash-functie die het beste bij uw behoeften past.
- Als u uw gegevens indeelt met behulp van tijds tempels of numerieke id's, moet u ervoor zorgen dat u geen gebruik maakt van een patroon voor alleen toevoegen (of alleen laten voorafgaan door). Deze patronen zijn niet geschikt voor een partitie systeem op basis van een bereik. Deze patronen kunnen leiden tot al het verkeer naar één partitie en beperken het systeem van doel treffende taak verdeling.

    Als u bijvoorbeeld dagelijkse bewerkingen hebt die gebruikmaken van een blob met een tijds tempel zoals *jjjmmdd*, wordt al het verkeer voor die dagelijkse bewerking omgeleid naar één blob, die wordt bediend door één partitie server. Bepaal of de limieten per Blob en de limieten per partitie aan uw behoeften voldoen en overweeg deze bewerking zo nodig te verbreken in meerdere blobs. En als u tijdreeks gegevens in uw tabellen opslaat, kan al het verkeer worden omgeleid naar het laatste deel van de sleutel naam ruimte. Als u numerieke Id's gebruikt, moet u het voor voegsel van de ID met een hash met drie cijfers. Als u tijds tempels gebruikt, moet u een voor voegsel van de tijds tempel maken met de seconden waarde, bijvoorbeeld *ssyyyymmdd*. Als uw toepassing een lijst-en query bewerking uitvoert, kiest u een hash-functie waarmee het aantal query's wordt beperkt. In sommige gevallen kan een wille keurig voor voegsel voldoende zijn.
  
- Zie [Azure Storage: een Maxi maal beschik bare Cloud opslag service met sterke consistentie](https://sigops.org/sosp/sosp11/current/2011-Cascais/printable/11-calder.pdf)voor meer informatie over het partitie schema dat wordt gebruikt in azure Storage.

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

Gebruik een Content Delivery Network, zoals Azure CDN, voor een brede distributie van blob-inhoud. Zie [Azure CDN](../../cdn/cdn-overview.md)voor meer informatie over Azure CDN.  

## <a name="sas-and-cors"></a>SAS en CORS

Stel dat u code moet machtigen zoals JavaScript dat wordt uitgevoerd in de webbrowser van een gebruiker of in een app op een mobiele telefoon om toegang te krijgen tot gegevens in Azure Storage. Een aanpak is om een servicetoepassing te compileren die als een proxy fungeert. Het apparaat van de gebruiker wordt geverifieerd bij de service, die op zijn beurt toegang tot Azure Storage-resources verleent. Op deze manier kunt u voorkomen dat de sleutels van uw opslagaccount op onveilige apparaten worden weergegeven. Met deze benadering wordt echter een aanzienlijke overhead op de servicetoepassing geplaatst, omdat alle gegevens die worden overgedragen tussen het apparaat van de gebruiker en Azure Storage via de servicetoepassing moeten worden doorgegeven.

U kunt voorkomen dat een servicetoepassing wordt gebruikt als een proxy voor Azure Storage met behulp van Shared Access Signatures (SAS). Met SAS kunt u het apparaat van uw gebruiker in staat stellen om aanvragen rechtstreeks bij Azure Storage in te dienen met behulp van een beperkt toegangstoken. Als een gebruiker bijvoorbeeld een foto wil uploaden naar uw toepassing, kan uw servicetoepassing een SAS genereren en deze verzenden naar het apparaat van de gebruiker. Het SAS-token kan toestemming geven om naar een Azure Storage-resource te schrijven gedurende een opgegeven tijdsinterval, waarna het SAS-token verloopt. Zie [Beperkte toegang verlenen tot Azure Storage-resources via SAS (Shared Access Signatures)](../common/storage-sas-overview.md) voor meer informatie over SAS.  

Normaal gesproken staat een webbrowser geen JavaScript toe op een pagina die wordt gehost door een website op het ene domein om bepaalde bewerkingen, zoals schrijfbewerkingen, uit te voeren op het andere domein. Op basis van dit beleid, dat bekend staat als 'beleid voor zelfde oorsprong', wordt voorkomen dat een schadelijk script op één pagina de toegang tot gegevens op een andere webpagina kan verkrijgen. Hetzelfde 'beleid voor zelfde oorsprong' kan echter een beperking zijn bij het compileren van een oplossing in de cloud. Cross-Origin Resource Sharing (CORS) is een browserfunctie waarmee het doeldomein van het brondomein afkomstige aanvragen kan doorgeven aan een vertrouwde browser.

Stel bijvoorbeeld dat een webtoepassing die in Azure wordt uitgevoerd een aanvraag voor een resource indient bij een Azure Storage-account. De webtoepassing is het brondomein en het opslagaccount is het doeldomein. U kunt CORS voor elk van de Azure Storage-services configureren om met de webbrowser aanvragen door te geven vanuit het brondomein dat worden vertrouwd door Azure Storage. Zie [CORS-ondersteuning (cross-origin resource sharing) voor Azure Storage](/rest/api/storageservices/Cross-Origin-Resource-Sharing--CORS--Support-for-the-Azure-Storage-Services) voor meer informatie over CORS.  
  
Met SAS en CORS kunt u voorkomen dat uw webtoepassing onnodig wordt belast.  

## <a name="caching"></a>Caching

Caching speelt een belang rijke rol bij de prestaties. In de volgende secties worden aanbevolen procedures voor caching besproken.

### <a name="reading-data"></a>Lezen van de gegevens

Over het algemeen is het lezen van gegevens één keer de voor keur. Bekijk het voor beeld van een webtoepassing die een 50 MiB-BLOB heeft opgehaald van de Azure Storage die als inhoud moet fungeren voor een gebruiker. In het ideale geval wordt de BLOB lokaal in de cache opgeslagen en wordt de versie van de cache voor volgende gebruikers aanvragen opgehaald.

Een manier om te voor komen dat een BLOB wordt opgehaald als deze niet is gewijzigd sinds de cache is opgeslagen, is door de GET-bewerking te kwalificeren met een voorwaardelijke header voor wijzigings tijd. Als het tijdstip van de laatste wijziging na het tijdstip waarop de BLOB in de cache is opgeslagen, wordt de BLOB opgehaald en opnieuw in de cache geplaatst. Anders wordt de BLOB in de cache opgehaald voor optimale prestaties.

U kunt ook besluiten uw toepassing te ontwerpen om ervan uit te gaan dat de BLOB gedurende een korte periode ongewijzigd blijft nadat u deze hebt opgehaald. In dit geval hoeft de toepassing niet te controleren of de BLOB tijdens dat interval is gewijzigd.

Configuratie gegevens, opzoek gegevens en andere gegevens die vaak door de toepassing worden gebruikt, zijn goede kandidaten voor caching.  

Zie voor meer informatie over het gebruik van voorwaardelijke kopteksten [voorwaardelijke kopteksten voor BLOB service bewerkingen opgeven](/rest/api/storageservices/specifying-conditional-headers-for-blob-service-operations).  

### <a name="uploading-data-in-batches"></a>Gegevens uploaden in batches

In sommige scenario's kunt u gegevens lokaal samen voegen, en deze vervolgens periodiek uploaden in een batch in plaats van elk stukje gegevens onmiddellijk te uploaden. Stel bijvoorbeeld dat een webtoepassing een logboek bestand met activiteiten bijhoudt. De toepassing kan gegevens van elke activiteit uploaden als deze wordt uitgevoerd in een tabel (waarvoor veel opslag bewerkingen nodig zijn), of de gegevens van de activiteit kan worden opgeslagen in een lokaal logboek bestand en vervolgens periodiek alle activiteit details uploaden als een bestand met scheidings tekens naar een blob. Als elke logboek vermelding 1 KB groot is, kunt u duizenden vermeldingen uploaden in één trans actie. Eén trans actie ondersteunt het uploaden van een BLOB van Maxi maal 64 MiB-grootte. De ontwikkelaar van de toepassing moet ontwerpen voor de mogelijkheid van client apparaten of upload fouten. Als de gegevens van de activiteit moeten worden gedownload gedurende een tijds interval in plaats van voor één activiteit, wordt het gebruik van Blob Storage aanbevolen voor tabel opslag.

## <a name="net-configuration"></a>.NET-configuratie

Als u het .NET Framework gebruikt, worden in deze sectie verschillende snelle configuratie-instellingen weergegeven die u kunt gebruiken om belangrijke verbeteringen in de prestaties aan te brengen.  Als u andere talen gebruikt, controleert u of er soortgelijke concepten van toepassing zijn in de taal die u hebt gekozen.  

### <a name="use-net-core"></a>.NET Core gebruiken

Ontwikkel uw Azure Storage-toepassingen met .NET Core 2.1 of hoger om te profiteren van de betere prestaties. Het gebruik van .NET Core 3.x wordt aanbevolen als dat mogelijk is.

Raadpleeg de volgende blogberichten voor meer informatie over betere prestaties in .NET Core:

- [Prestatieverbeteringen in .NET Core 3.0](https://devblogs.microsoft.com/dotnet/performance-improvements-in-net-core-3-0/)
- [Prestatieverbeteringen in .NET Core 2.1](https://devblogs.microsoft.com/dotnet/performance-improvements-in-net-core-2-1/)

### <a name="increase-default-connection-limit"></a>De standaardverbindingslimiet verhogen

In .NET verhoogt de volgende code de standaard verbindings limiet (meestal twee in een client omgeving of tien in een server omgeving) tot 100. Normaal gesproken moet u de waarde instellen op ongeveer het aantal threads dat door uw toepassing wordt gebruikt. Stel de verbindingslimiet in voordat u verbindingen opent.

```csharp
ServicePointManager.DefaultConnectionLimit = 100; //(Or More)  
```

Zie de documentatie voor meer informatie over het instellen van de verbindings limiet voor andere programmeer talen.  

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

## <a name="copying-and-moving-blobs"></a>Blobs kopiëren en verplaatsen

Azure Storage biedt een aantal oplossingen voor het kopiëren en verplaatsen van blobs in een opslag account, tussen opslag accounts en tussen on-premises systemen en de Cloud. In deze sectie worden enkele van deze opties beschreven, wat betreft de gevolgen voor de prestaties. Zie [een Azure-oplossing voor gegevens overdracht kiezen](../common/storage-choose-data-transfer-solution.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)voor informatie over het efficiënt overdragen van gegevens naar of van Blob Storage.

### <a name="blob-copy-apis"></a>BLOB Copy-Api's

Als u blobs wilt kopiëren tussen opslag accounts, gebruikt u de bewerking [blok keren vanaf URL](/rest/api/storageservices/put-block-from-url) . Met deze bewerking worden gegevens synchroon gekopieerd van een URL-bron naar een blok-blob. Het gebruik van de `Put Block from URL` bewerking kan de vereiste band breedte aanzienlijk verminderen wanneer u gegevens migreert tussen opslag accounts. Omdat de Kopieer bewerking plaatsvindt aan de kant van de service, hoeft u de gegevens niet te downloaden en opnieuw te uploaden.

Als u gegevens wilt kopiëren binnen hetzelfde opslag account, gebruikt u de bewerking [BLOB kopiëren](/rest/api/storageservices/Copy-Blob) . Het kopiëren van gegevens binnen hetzelfde opslag account wordt meestal snel voltooid.  

### <a name="use-azcopy"></a>AzCopy gebruiken

Het AzCopy-opdracht regel programma is een eenvoudige en efficiënte optie voor bulk overdracht van blobs naar, van en tussen opslag accounts. AzCopy is geoptimaliseerd voor dit scenario en kan hoge overdrachts snelheden bezorgen. AzCopy versie 10 gebruikt de `Put Block From URL` bewerking voor het kopiëren van BLOB-gegevens over opslag accounts. Zie voor meer informatie [kopiëren of verplaatsen van gegevens naar Azure Storage met behulp van AzCopy V10 toevoegen](../common/storage-use-azcopy-v10.md).  

### <a name="use-azure-data-box"></a>Azure Data Box gebruiken

Voor het importeren van grote hoeveel heden gegevens in Blob Storage kunt u de Azure Data Box-serie gebruiken voor offline overdrachten. Door micro soft geleverde Data Box-apparaten zijn een goede keuze om grote hoeveel heden gegevens naar Azure te verplaatsen wanneer u beperkt bent door tijd, netwerk beschikbaarheid of kosten. Zie de [documentatie van Azure DataBox](../../databox/index.yml)voor meer informatie.

## <a name="content-distribution"></a>Inhouds distributie

Soms moet een toepassing dezelfde inhoud aanbieden aan veel gebruikers (bijvoorbeeld een product demo video die wordt gebruikt op de start pagina van een website), die zich in dezelfde of meerdere regio's bevindt. In dit scenario gebruikt u een Content Delivery Network (CDN), zoals Azure CDN om blob-inhoud geografisch te distribueren. In tegens telling tot een Azure Storage-account dat bestaat in één regio en die geen inhoud met lage latentie kan leveren aan andere regio's, Azure CDN servers gebruiken in meerdere data centers over de hele wereld. Daarnaast kan een CDN veel hogere uitwijkings limieten ondersteunen dan één opslag account.  

Zie [Azure CDN](../../cdn/cdn-overview.md)voor meer informatie over Azure CDN.

## <a name="use-metadata"></a>Meta gegevens gebruiken

Het Blob service ondersteunt HEAD-aanvragen, die BLOB-eigenschappen of meta gegevens kunnen bevatten. Als uw toepassing bijvoorbeeld de EXIF-gegevens (wissel bare afbeeldings indeling) van een foto nodig heeft, kan deze de foto ophalen en uitpakken. Om band breedte te besparen en de prestaties te verbeteren, kan uw toepassing de EXIF-gegevens opslaan in de meta gegevens van de BLOB wanneer de toepassing de foto uploadt. U kunt vervolgens de EXIF-gegevens in meta gegevens ophalen met behulp van een HEAD-aanvraag. Het ophalen van alleen meta gegevens en niet de volledige inhoud van de BLOB bespaart aanzienlijke band breedte en vermindert de verwerkings tijd die nodig is om de EXIF-gegevens te extra heren. Houd er rekening mee dat 8 KiB van meta gegevens per Blob kan worden opgeslagen.  

## <a name="upload-blobs-quickly"></a>Snel blobs uploaden

Als u blobs snel wilt uploaden, moet u eerst bepalen of u een of meer blobs wilt uploaden. Gebruik de onderstaande richt lijnen om te bepalen welke methode het meest geschikt is voor uw scenario.  

### <a name="upload-one-large-blob-quickly"></a>Snel één grote BLOB uploaden

Als u snel een enkele grote BLOB wilt uploaden, kan een client toepassing de blokken of pagina's parallel uploaden, waarbij de mindful van de schaalbaarheids doelen voor afzonderlijke blobs en het opslag account als geheel worden bijgewerkt. De Azure Storage-client bibliotheken ondersteunen gelijktijdig uploaden. U kunt bijvoorbeeld de volgende eigenschappen gebruiken om het aantal gelijktijdige aanvragen op te geven dat is toegestaan in .NET of Java. Client bibliotheken voor andere ondersteunde talen bieden vergelijk bare opties.

- Voor .NET, stelt u de eigenschap [BlobRequestOptions. ParallelOperationThreadCount](/dotnet/api/microsoft.azure.storage.blob.blobrequestoptions.paralleloperationthreadcount) in.
- Voor Java/Android roept u de methode [BlobRequestOptions. setConcurrentRequestCount (Final integer concurrentRequestCount)](/java/api/com.microsoft.azure.storage.blob.blobrequestoptions.setconcurrentrequestcount) aan.

### <a name="upload-many-blobs-quickly"></a>Snel veel blobs uploaden

Als u snel een groot aantal blobs wilt uploaden, uploadt u blobs parallel. Gelijktijdig uploaden is sneller dan het uploaden van één BLOB tegelijk met parallelle blok uploads, omdat de upload wordt gespreid over meerdere partities van de opslag service. AzCopy voert standaard gelijktijdig uploads uit en wordt aanbevolen voor dit scenario. Zie [aan de slag met AzCopy](../common/storage-use-azcopy-v10.md)voor meer informatie.  

## <a name="choose-the-correct-type-of-blob"></a>Het juiste type BLOB kiezen

Azure Storage ondersteunt blok-blobs, toevoeg-blobs en pagina-blobs. Voor een bepaald gebruiks scenario is uw keuze van het type blob van invloed op de prestaties en schaal baarheid van uw oplossing.

Blok-blobs zijn geschikt wanneer u grote hoeveel heden gegevens efficiënt wilt uploaden. Een client toepassing die bijvoorbeeld Foto's of Video's uploadt naar Blob Storage, zou blok-blobs als doel zouden hebben.

Toevoeg-blobs zijn vergelijkbaar met blok-blobs, omdat ze bestaan uit blokken. Wanneer u een toevoeg-BLOB wijzigt, worden blokken alleen toegevoegd aan het einde van de blob. Toevoeg-blobs zijn handig voor scenario's zoals logboek registratie, wanneer een toepassing gegevens moet toevoegen aan een bestaande blob.

Pagina-blobs zijn geschikt als de toepassing wille keurige schrijf bewerkingen op de gegevens moet uitvoeren. Virtuele-machine schijven van Azure worden bijvoorbeeld opgeslagen als pagina-blobs. Zie voor meer informatie [over blok-blobs, toevoeg-blobs en pagina-blobs](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs).  

## <a name="next-steps"></a>Volgende stappen

- [Schaalbaarheids- en prestatiedoelen voor Blob-opslag](scalability-targets.md)
- [Schaalbaarheids- en prestatiedoelen voor standaardopslagaccounts](../common/scalability-targets-standard-account.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
- [Status en foutcodes](/rest/api/storageservices/Status-and-Error-Codes2)