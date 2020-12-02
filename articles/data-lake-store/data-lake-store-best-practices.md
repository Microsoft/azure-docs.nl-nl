---
title: Aanbevolen procedures voor het gebruik van Azure Data Lake Storage Gen1 | Microsoft Docs
description: Meer informatie over de aanbevolen procedures voor gegevens opname, datum beveiliging en prestaties met betrekking tot het gebruik van Azure Data Lake Storage Gen1 (voorheen bekend als Azure Data Lake Store)
services: data-lake-store
documentationcenter: ''
author: sachinsbigdata
manager: mtillman
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.date: 06/27/2018
ms.author: sachins
ms.openlocfilehash: 9a5c5f9a4033b70a664071d6077a69f38c905093
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/01/2020
ms.locfileid: "96452221"
---
# <a name="best-practices-for-using-azure-data-lake-storage-gen1"></a>Aanbevolen procedures voor het gebruik van Azure Data Lake Storage Gen1

[!INCLUDE [data-lake-storage-gen1-rename-note.md](../../includes/data-lake-storage-gen1-rename-note.md)]

In dit artikel vindt u informatie over de aanbevolen procedures en overwegingen voor het werken met Azure Data Lake Storage Gen1. In dit artikel vindt u informatie over beveiliging, prestaties, tolerantie en bewaking voor Data Lake Storage Gen1. Voordat Data Lake Storage Gen1 werken met echt big data in services zoals Azure HDInsight was complex. U moest gegevens in meerdere Blob Storage-accounts Shard, zodat de PETA byte-opslag en optimale prestaties op die schaal kunnen worden bereikt. Met Data Lake Storage Gen1 worden de meeste vaste limieten voor grootte en prestaties verwijderd. Er zijn echter nog enkele overwegingen die in dit artikel worden behandeld, zodat u de beste prestaties kunt verkrijgen met Data Lake Storage Gen1.

## <a name="security-considerations"></a>Beveiligingsoverwegingen

Azure Data Lake Storage Gen1 biedt POSIX-toegangs controles en gedetailleerde controles voor Azure Active Directory (Azure AD)-gebruikers,-groepen en-service-principals. Deze toegangs elementen kunnen worden ingesteld op bestaande bestanden en mappen. De toegangs elementen kunnen ook worden gebruikt om standaard waarden te maken die kunnen worden toegepast op nieuwe bestanden of mappen. Wanneer machtigingen zijn ingesteld op bestaande mappen en onderliggende objecten, moeten de machtigingen recursief worden door gegeven aan elk object. Als er grote aantallen bestanden zijn, kan het door geven van de machtigingen lang duren. Hoe lang het duurt, kan variëren tussen 30-50 objecten die per seconde worden verwerkt. Plan daarom de mapstructuur en gebruikers groepen op de juiste manier. Als dat niet het geval is, kan dit leiden tot onverwachte vertragingen en problemen wanneer u met uw gegevens werkt.

Stel dat u een map hebt met onderliggende 100.000-objecten. Als u de ondergrens van 30 objecten die per seconde worden verwerkt, bijwerkt, kan het een uur duren om de machtiging voor de hele map bij te werken. Meer informatie over Data Lake Storage Gen1 Acl's vindt u [in azure data Lake Storage gen1 voor toegangs beheer](data-lake-store-access-control.md). Voor betere prestaties bij het recursief toewijzen van Acl's, kunt u het hulp programma Azure Data Lake Command-Line gebruiken. Het hulp programma maakt meerdere threads en recursieve navigatie logica om snel Acl's toe te passen op miljoenen bestanden. Het hulp programma is beschikbaar voor Linux en Windows, en de [documentatie](https://github.com/Azure/data-lake-adlstool) en [down loads](https://aka.ms/adlstool-download) voor dit hulp programma vindt u op github. Deze dezelfde prestatie verbeteringen kunnen worden ingeschakeld door uw eigen hulpprogram ma's die zijn geschreven met de Data Lake Storage Gen1 [.net](data-lake-store-data-operations-net-sdk.md) -en [Java](data-lake-store-get-started-java-sdk.md) -sdk's.

### <a name="use-security-groups-versus-individual-users"></a>Beveiligings groepen gebruiken versus afzonderlijke gebruikers

Wanneer u werkt met big data in Data Lake Storage Gen1, wordt waarschijnlijk een Service-Principal gebruikt om services als Azure HDInsight te laten werken met de gegevens. Er kunnen echter gevallen zijn waarin individuele gebruikers ook toegang tot de gegevens nodig hebben. In dergelijke gevallen moet u Azure Active Directory [beveiligings groepen](data-lake-store-secure-data.md#create-security-groups-in-azure-active-directory) gebruiken in plaats van afzonderlijke gebruikers toe te wijzen aan mappen en bestanden.

Als aan een beveiligings groep machtigingen zijn toegewezen, moeten er geen updates meer Data Lake Storage Gen1 worden toegevoegd aan of verwijderd uit de groep. Dit zorgt er ook voor dat u de limiet van [32 toegangs-en standaard-acl's](../azure-resource-manager/management/azure-subscription-service-limits.md#data-lake-storage-limits) niet overschrijdt (dit omvat de vier POSIX-acl's die altijd zijn gekoppeld aan elk bestand en elke map: [de gebruiker](data-lake-store-access-control.md#the-owning-user)die eigenaar is, de [groep die eigenaar](data-lake-store-access-control.md#the-owning-group)is, [het masker](data-lake-store-access-control.md#the-mask)en andere).

### <a name="security-for-groups"></a>Beveiliging voor groepen

Zoals besproken: wanneer gebruikers toegang nodig hebben tot Data Lake Storage Gen1, is het raadzaam om Azure Active Directory beveiligings groepen te gebruiken. Een aantal aanbevolen groepen om met te beginnen, kan **ReadOnlyUsers**, **WriteAccessUsers** en **FullAccessUsers** zijn voor de hoofdmap van het account en zelfs voor sleutel submappen. Als er andere groepen gebruikers zijn die later kunnen worden toegevoegd, maar die nog niet zijn geïdentificeerd, kunt u overwegen om dummy beveiligings groepen te maken die toegang tot bepaalde mappen hebben. Wanneer u een beveiligings groep gebruikt, zorgt u ervoor dat u later geen lange verwerkings tijd nodig hebt om nieuwe machtigingen toe te wijzen aan duizenden bestanden.

### <a name="security-for-service-principals"></a>Beveiliging voor service-principals

Azure Active Directory service-principals worden doorgaans door services zoals Azure HDInsight gebruikt voor toegang tot gegevens in Data Lake Storage Gen1. Afhankelijk van de toegangs vereisten voor meerdere werk belastingen, zijn er mogelijk enkele aandachtspunten voor een gegarandeerde beveiliging binnen en buiten de organisatie. Voor veel klanten kan een enkele Azure Active Directory Service-Principal voldoende zijn en kan deze volledige machtigingen hebben in de hoofdmap van het Data Lake Storage Gen1-account. Andere klanten hebben mogelijk meerdere clusters met verschillende service-principals, waarbij één cluster volledige toegang tot de gegevens heeft, en een ander cluster met alleen lees toegang. Net als bij de beveiligings groepen kunt u overwegen om een service-principal te maken voor elk verwacht scenario (lezen, schrijven, volledig) zodra een Data Lake Storage Gen1 account is gemaakt.

### <a name="enable-the-data-lake-storage-gen1-firewall-with-azure-service-access"></a>De Data Lake Storage Gen1 firewall inschakelen met toegang tot de Azure-service

Data Lake Storage Gen1 ondersteunt de mogelijkheid om een firewall in te scha kelen en de toegang alleen te beperken tot Azure-Services. dit wordt aanbevolen voor een kleinere aanvals vector van externe indringers. Firewall kan worden ingeschakeld op het data Lake Storage gen1-account in de Azure portal via **firewall**  >  **firewall inschakelen (ingeschakeld)**  >  **toegang tot opties voor Azure-Services toestaan** .

![Firewall instellingen in Data Lake Storage Gen1](./media/data-lake-store-best-practices/data-lake-store-firewall-setting.png "Firewall instellingen in Data Lake Storage Gen1")

Zodra de firewall is ingeschakeld, hebben alleen Azure-Services, zoals HDInsight, Data Factory, Azure Synapse Analytics, etc. toegang tot Data Lake Storage Gen1. Als gevolg van de interne Network Address Translation die door Azure worden gebruikt, biedt de Data Lake Storage Gen1 firewall geen ondersteuning voor het beperken van specifieke services via IP en is deze alleen bedoeld voor beperkingen van eind punten buiten Azure, zoals on-premises.

## <a name="performance-and-scale-considerations"></a>Overwegingen voor prestaties en schalen

Een van de krach tigste functies van Data Lake Storage Gen1 is dat de vaste limieten voor gegevens doorvoer worden verwijderd. Als u de limieten verwijdert, kunnen klanten hun gegevens grootte en de benodigde prestatie vereisten verg Roten zonder dat ze de gegevens hoeven te Shard. Een van de belangrijkste aandachtspunten voor het optimaliseren van de prestaties van Data Lake Storage Gen1 is dat het het beste wordt uitgevoerd wanneer er parallel is.

### <a name="improve-throughput-with-parallelism"></a>De door Voer verbeteren met parallelisme

Overweeg om 8-12 threads per kern te geven voor de meest optimale door Voer voor lezen/schrijven. Dit wordt veroorzaakt door het blok keren van lees-en schrijf bewerkingen op één thread, en meer threads kunnen een hogere gelijktijdigheid toestaan op de virtuele machine. Zorg ervoor dat het CPU-gebruik van de virtuele machine wordt gecontroleerd om ervoor te zorgen dat de niveaus in orde zijn en dat de parallelle uitvoering kan worden verhoogd.

### <a name="avoid-small-file-sizes"></a>Kleine bestands grootten vermijden

POSIX-machtigingen en-controle in Data Lake Storage Gen1 worden geleverd met een overhead die duidelijk wordt wanneer u met een groot aantal kleine bestanden werkt. Als best practice moet u uw gegevens naar een groter bestand batcheren en duizenden of miljoenen kleine bestanden naar Data Lake Storage Gen1 schrijven. Het voor komen van kleine bestands grootten kan meerdere voor delen bieden, zoals:

* De verificatie controles over meerdere bestanden verlagen
* Beperkte open-bestands verbindingen
* Sneller kopiëren/repliceren
* Minder bestanden om te verwerken bij het bijwerken van de Data Lake Storage Gen1 POSIX-machtigingen

Afhankelijk van de services en werk belastingen die gebruikmaken van de gegevens, is een goede grootte om rekening te houden met 256 MB of hoger. Als de bestands grootte niet kan worden gebatcheerd bij het aangaan van Data Lake Storage Gen1, kunt u een afzonderlijke compressie taak hebben die deze bestanden combineert tot grotere records. Zie [uw gegevensset structureren](data-lake-store-performance-tuning-guidance.md#structure-your-data-set)voor meer informatie en aanbeveling over bestands grootten en het organiseren van de gegevens in data Lake Storage gen1.

### <a name="large-file-sizes-and-potential-performance-impact"></a>Grote bestands grootten en mogelijke gevolgen voor de prestaties

Hoewel Data Lake Storage Gen1 grote bestanden Maxi maal PETA bytes ondersteunt, voor optimale prestaties en afhankelijk van het proces voor het lezen van de gegevens, is het mogelijk niet ideaal voor een gemiddelde van meer dan 2 GB. Wanneer u bijvoorbeeld **Distcp** gebruikt voor het kopiëren van gegevens tussen locaties of verschillende opslag accounts, zijn bestanden het hoogste granulatie niveau dat wordt gebruikt om kaart taken te bepalen. Als u dus 10 bestanden kopieert van 1 TB elke, worden er Maxi maal 10 toewijzingen toegewezen. Als er veel bestanden met toewijzingen zijn toegewezen, werken de toewijzingen in eerste instantie parallel om grote bestanden te verplaatsen. Als de taak echter begint te spoelen, blijven slechts enkele toewijzingen toegewezen en kunt u blijven werken met één toewijzing die aan een groot bestand is toegewezen. Micro soft heeft verbeteringen naar Distcp verzonden om dit probleem op te lossen in toekomstige Hadoop-versies.

Een ander voor beeld waarmee u rekening moet houden bij het gebruik van Azure Data Lake Analytics met Data Lake Storage Gen1. Afhankelijk van de verwerking die door de extractor wordt uitgevoerd, kunnen bestanden die niet kunnen worden gesplitst (bijvoorbeeld XML, JSON) prestaties in beslag nemen wanneer dit groter is dan 2 GB. In gevallen waarin bestanden kunnen worden gesplitst door een extractie (bijvoorbeeld CSV), worden grote bestanden de voor keur.

### <a name="capacity-plan-for-your-workload"></a>Capaciteits plan voor uw workload

Azure Data Lake Storage Gen1 verwijdert de vaste IO-beperkings limieten die worden geplaatst op Blob Storage-accounts. Er zijn echter nog steeds zachte limieten die moeten worden overwogen. De standaard limieten voor inkomend/uitgaand verkeer voldoen aan de behoeften van de meeste scenario's. Als uw werk belasting de limieten moet verg root, kunt u samen werken met micro soft-ondersteuning. Bekijk ook de limieten tijdens de test fase, zodat de i/o-beperkings limieten tijdens de productie niet worden bereikt. Als dat gebeurt, kan het nodig zijn om te wachten op hand matige toename van het technische team van micro soft. Als er sprake is van IO-beperking, retourneert Azure Data Lake Storage Gen1 de fout code 429. in het ideale geval moet er opnieuw worden geprobeerd met een geschikt exponentieel uitstel-beleid.

### <a name="optimize-writes-with-the-data-lake-storage-gen1-driver-buffer"></a>' Schrijfopdrachten ' optimaliseren met de Data Lake Storage Gen1 stuur programma buffer

Voor het optimaliseren van de prestaties en het verminderen van IOPS bij het schrijven naar Data Lake Storage Gen1 vanuit Hadoop, voert u schrijf bewerkingen zo dicht mogelijk bij de buffer grootte van het Data Lake Storage Gen1 stuur programma. Probeer de buffer grootte vóór het leegmaken niet te overschrijden, zoals bij het streamen met behulp van Apache Storm of Spark streaming-workloads. Wanneer u naar Data Lake Storage Gen1 van HDInsight/Hadoop schrijft, is het belang rijk te weten dat Data Lake Storage Gen1 een stuur programma met een buffer van 4 MB heeft. Net als bij veel bestandssysteem Stuur Programma's kan deze buffer hand matig worden leeg gemaakt voordat de grootte van 4 MB wordt bereikt. Als dat niet het geval is, wordt het onmiddellijk naar de opslag leeg gemaakt als de volgende schrijf bewerking de maximale grootte van de buffer overschrijdt. Waar mogelijk moet u een overloop of een significante underrun van de buffer voor komen bij het synchroniseren/leegmaken van beleid per aantal of tijd venster.

## <a name="resiliency-considerations"></a>Overwegingen voor tolerantie

Bij het ontwerpen van een systeem met Data Lake Storage Gen1 of een Cloud service, moet u rekening houden met uw beschikbaarheids vereisten en hoe u kunt reageren op mogelijke onderbrekingen in de service. Een probleem kan worden gelokaliseerd voor het specifieke exemplaar of zelfs voor de hele regio, zodat een plan voor beide belang rijk is. Afhankelijk van de **beoogde herstel tijd** en de **Recovery Point Objective** -sla's voor uw workload, kunt u een meer of minder agressieve strategie kiezen voor hoge Beschik baarheid en herstel na nood gevallen.

### <a name="high-availability-and-disaster-recovery"></a>Hoge beschikbaarheid en herstel na noodgevallen

Hoge Beschik baarheid (HA) en herstel na nood gevallen (DR) kunnen soms samen worden gecombineerd, hoewel elk een iets andere strategie heeft, met name wanneer het gaat om gegevens. Data Lake Storage Gen1 heeft reeds 3x-replicatie op de schermen afgehandeld tegen de beveiliging tegen gelokaliseerde hardwarestoringen. Omdat replicatie tussen regio's echter niet is ingebouwd in, moet u dit zelf beheren. Bij het bouwen van een plan voor HA moet de werk belasting, in het geval van een onderbreking van de service, zo snel mogelijk toegang hebben tot de meest recente gegevens door over te scha kelen naar een afzonderlijk gerepliceerd exemplaar lokaal of in een nieuwe regio.

In een DR-strategie is het belang rijk dat de gegevens die naar een andere regio worden gerepliceerd, worden voor bereid voor het onwaarschijnlijke geval van een onherstelbare storing van een regio. Deze gegevens kunnen in eerste instantie gelijk zijn aan die van de gerepliceerde HA-gegevens. U moet echter ook rekening houden met uw vereisten voor rand gevallen, zoals beschadiging van gegevens, waarbij u mogelijk periodieke moment opnamen wilt maken om terug te vallen op. Afhankelijk van het belang en de grootte van de gegevens, kunt u het beste Delta momentopnamen van 1-, 6-en 24-uurs Peri Oden in het lokale en/of secundaire archief maken op basis van risico toleranties.

Voor gegevens tolerantie met Data Lake Storage Gen1 is het raadzaam om uw gegevens geografisch te repliceren naar een andere regio met een frequentie die voldoet aan uw HA/DR-vereisten, in het ideale geval elk uur. Deze frequentie van replicatie minimaliseert enorme gegevens bewegingen die een concurrerende doorvoer behoeften kunnen hebben met het hoofd systeem en een betere Recovery Point Objective (RPO). Daarnaast moet u voor de toepassing rekening houden met behulp van Data Lake Storage Gen1 om automatisch een failover uit te laten sturen naar het secundaire account via bewakings triggers of de duur van mislukte pogingen, of om ten minste een melding aan beheerders te verzenden voor hand matige tussen komst. Houd er bij het afnemen van een afweging van de failover uit en wacht totdat een service weer online komt. Als de gegevens niet zijn gerepliceerd, kan een failover leiden tot mogelijk gegevens verlies, inconsistentie of complexe samen voeging van de gegevens.

Hieronder ziet u de belangrijkste drie aanbevolen opties voor het indelen van replicatie tussen Data Lake Storage Gen1 accounts en de belangrijkste verschillen tussen beide.

|  |Distcp  |Azure Data Factory  |AdlCopy  |
|---------|---------|---------|---------|
|**Schaal limieten**     | Begrensd door worker-knoop punten        | Beperkt door Max. eenheden voor gegevens verplaatsing in de Cloud        | Gebonden aan Analytics units        |
|**Ondersteunt het kopiëren van verschillen**     |   Ja      | Nee         | Nee         |
|**Ingebouwde indeling**     |  Nee (gebruik Oozie lucht flow of cron-taken)       | Ja        | Nee (gebruik Azure Automation of Windows Task Scheduler)         |
|**Ondersteunde bestands systemen**     | ADL, HDFS, WASB, S3, GS, CFS        |Talloze, Zie [connectors](../data-factory/connector-azure-blob-storage.md).         | ADL naar ADL, WASB op ADL (alleen voor dezelfde regio)        |
|**Ondersteuning voor besturings systeem**     |Elk besturings systeem dat Hadoop uitvoert         | N.v.t.          | Windows 10         |

### <a name="use-distcp-for-data-movement-between-two-locations"></a>Distcp gebruiken voor het verplaatsen van gegevens tussen twee locaties

Kort voor gedistribueerde kopieën is Distcp een Linux-opdracht regel programma dat wordt meegeleverd met Hadoop en waarmee gedistribueerde gegevens tussen twee locaties kunnen worden verplaatst. De twee locaties kunnen Data Lake Storage Gen1, HDFS, WASB of S3 zijn. Dit hulp programma maakt gebruik van MapReduce-taken in een Hadoop-cluster (bijvoorbeeld HDInsight) om uit te schalen op alle knoop punten. Distcp wordt beschouwd als de snelste manier om big data te verplaatsen zonder speciale netwerk compressie-apparaten. Distcp biedt ook een optie om verschillen tussen twee locaties bij te werken, automatische nieuwe pogingen te verwerken, evenals dynamische schaling van compute. Deze aanpak is zeer efficiënt wanneer het gaat om het repliceren van dingen als Hive/Spark-tabellen die veel grote bestanden in één map kunnen hebben en u alleen wilt kopiëren over de gewijzigde gegevens. Daarom is Distcp het meest aanbevolen hulp middel voor het kopiëren van gegevens tussen big data winkels.

Kopieer taken kunnen worden geactiveerd door Apache Oozie-werk stromen met behulp van frequentie-of gegevens triggers, evenals Linux cron-taken. Voor intensieve replicatie taken is het raadzaam om een afzonderlijk HDInsight Hadoop-cluster op te zetten dat specifiek voor de Kopieer taken kan worden afgestemd en geschaald. Dit zorgt ervoor dat het kopiëren van taken geen problemen veroorzaakt met kritieke taken. Als replicatie op een brede frequentie wordt uitgevoerd, kan het cluster zelfs tussen elke taak worden afgesloten. Als er een failover wordt uitgevoerd naar een secundaire regio, zorgt u ervoor dat een ander cluster ook in de secundaire regio wordt weer gegeven om nieuwe gegevens te repliceren naar het primaire Data Lake Storage Gen1-account zodra er een back-up is gemaakt. Zie [Distcp gebruiken om gegevens te kopiëren tussen Azure Storage blobs en data Lake Storage gen1](data-lake-store-copy-data-wasb-distcp.md)voor voor beelden van het gebruik van Distcp.

### <a name="use-azure-data-factory-to-schedule-copy-jobs"></a>Azure Data Factory gebruiken om Kopieer taken te plannen

Azure Data Factory kan ook worden gebruikt voor het plannen van Kopieer taken met behulp van een **Kopieer activiteit** en kan zelfs worden ingesteld op basis van een frequentie via de **wizard kopiëren**. Houd er rekening mee dat Azure Data Factory een limiet van DMUs (Cloud data units) heeft en uiteindelijk de door Voer/Compute voor grote gegevens workloads optekent. Daarnaast biedt Azure Data Factory momenteel geen verschillen tussen de Data Lake Storage Gen1-accounts, waardoor mappen als Hive-tabellen een volledige kopie moeten repliceren. Raadpleeg de [hand leiding voor het afstemmen van de Kopieer activiteit](../data-factory/copy-activity-performance.md) voor meer informatie over het kopiëren met Data Factory.

### <a name="adlcopy"></a>AdlCopy

AdlCopy is een Windows-opdracht regel programma waarmee u gegevens tussen twee Data Lake Storage Gen1 accounts alleen binnen dezelfde regio kunt kopiëren. Het hulp programma AdlCopy biedt een zelfstandige optie of de optie om een Azure Data Lake Analytics account te gebruiken om uw Kopieer taak uit te voeren. Hoewel het oorspronkelijk was gebouwd voor kopieën op aanvraag, in tegens telling tot een robuuste replicatie, biedt het een andere optie om gedistribueerd te kopiëren over Data Lake Storage Gen1-accounts binnen dezelfde regio. Voor betrouw baarheid is het raadzaam om de Premium-Data Lake Analytics optie te gebruiken voor een productiewerk belasting. De zelfstandige versie kan bezette reacties retour neren en heeft beperkte schaal en controle.

Net als Distcp moet de AdlCopy worden georganiseerd door iets als Azure Automation of Windows taak planner. Net als bij Data Factory biedt AdlCopy geen ondersteuning voor het kopiëren van alleen bijgewerkte bestanden, maar worden bestaande bestanden opnieuw gekopieerd en overschreven. Zie [gegevens kopiëren van Azure Storage-blobs naar Data Lake Storage gen1](data-lake-store-copy-data-azure-storage-blob.md)voor meer informatie en voor beelden van het gebruik van AdlCopy.

## <a name="monitoring-considerations"></a>Bewakings overwegingen

Data Lake Storage Gen1 biedt gedetailleerde Diagnostische logboeken en controle. Data Lake Storage Gen1 biedt een aantal elementaire metrische gegevens in de Azure Portal onder het Data Lake Storage Gen1-account en in Azure Monitor. De beschik baarheid van Data Lake Storage Gen1 wordt weer gegeven in de Azure Portal. Deze metrische gegevens worden echter om de zeven minuten vernieuwd en kunnen niet worden opgevraagd via een openbaar belichte API. Als u de meest recente Beschik baarheid van een Data Lake Storage Gen1 account wilt krijgen, moet u uw eigen synthetische tests uitvoeren om Beschik baarheid te valideren. Andere metrische gegevens, zoals het totale opslag gebruik, lees-en schrijf aanvragen en inkomend/uitgaand verkeer, kunnen tot wel 24 uur duren om te vernieuwen. Dit betekent dat meer actuele metrische gegevens hand matig moeten worden berekend via Hadoop-opdracht regel Programma's of door het samen voegen van logboek gegevens. De snelste manier om het meest recente opslag gebruik te verkrijgen, is deze HDFS-opdracht uit te voeren vanaf een Hadoop-cluster knooppunt (bijvoorbeeld hoofd knooppunt):

```console
hdfs dfs -du -s -h adl://<adlsg1_account_name>.azuredatalakestore.net:443/
```

### <a name="export-data-lake-storage-gen1-diagnostics"></a>Data Lake Storage Gen1 diagnostische gegevens exporteren

Een van de snelste manieren om toegang te krijgen tot Doorzoek bare logboeken van Data Lake Storage Gen1 is het inschakelen van logboek verzending naar **log Analytics** onder de Blade **Diagnostische gegevens** voor het data Lake Storage gen1-account. Dit biedt direct toegang tot binnenkomende logboeken met tijd-en inhouds filters, samen met waarschuwings opties (e-mail/webhook) die binnen een interval van vijf tien minuten zijn geactiveerd. Zie voor instructies [toegang tot Diagnostische logboeken voor Azure data Lake Storage gen1](data-lake-store-diagnostic-logs.md).

Voor meer real-time waarschuwingen en meer controle over de locatie van de logboeken, kunt u overwegen om logboeken naar Azure EventHub te exporteren waarbij inhoud afzonderlijk of over een tijd venster kan worden geanalyseerd om realtime meldingen naar een wachtrij te verzenden. Een afzonderlijke toepassing, zoals een [logische app](../connectors/connectors-create-api-azure-event-hubs.md) , kan de waarschuwingen vervolgens gebruiken en aan het juiste kanaal door geven, en meet gegevens verzenden naar controle Hulpprogramma's zoals NewRelic, Datadog of AppDynamics. Als u echter een hulp programma van derden gebruikt, zoals ElasticSearch, kunt u de logboeken exporteren naar Blob Storage en de [Azure Logstash-invoeg toepassing](https://github.com/Azure/azure-diagnostics-tools/tree/master/Logstash/logstash-input-azureblob) gebruiken om de gegevens in uw ElasticSearch, Kibana en Logstash-stack (elk) te verbruiken.

### <a name="turn-on-debug-level-logging-in-hdinsight"></a>Logboek registratie op fout opsporing inschakelen in HDInsight

Als Data Lake Storage Gen1 logboek verzending niet is ingeschakeld, biedt Azure HDInsight ook een manier om [logboek registratie aan client zijde voor data Lake Storage gen1](data-lake-store-performance-tuning-mapreduce.md) via log4j in te scha kelen. U moet de volgende eigenschap instellen in **Ambari**  >  **garens**  >  **config**  >  **Advanced garens-log4j configuraties**:

`log4j.logger.com.microsoft.azure.datalake.store=DEBUG`

Zodra de eigenschap is ingesteld en de knoop punten opnieuw zijn opgestart, worden Data Lake Storage Gen1 diagnostische gegevens naar de GARENs op de knoop punten (/tmp/ \<user\> /Yarn.log) geschreven en worden belang rijke details, zoals fouten of beperking (HTTP 429-fout code), gecontroleerd. Deze informatie kan ook worden bewaakt in Azure Monitor Logboeken of waar logboeken worden verzonden naar op de Blade [Diagnostische gegevens](data-lake-store-diagnostic-logs.md) van het data Lake Storage gen1-account. Het is raadzaam om ten minste logboek registratie aan client zijde in te scha kelen of gebruik te maken van de optie voor het vastleggen van logboeken met Data Lake Storage Gen1 voor operationele zicht baarheid en eenvoudiger fout opsporing.

### <a name="run-synthetic-transactions"></a>Synthetische trans acties uitvoeren

De metrische gegevens van de service beschikbaarheid voor Data Lake Storage Gen1 in het Azure Portal hebben momenteel het venster voor het vernieuwen van 7 minuten. Er kan ook geen query worden uitgevoerd met een openbaar belichte API. Daarom is het raadzaam om een basis toepassing te bouwen die synthetische trans acties Data Lake Storage Gen1 die tot de beschik baarheid van de minuut kunnen leveren. Een voor beeld is het maken van een Webtaak, een logische app of Azure functie-app om een lees-, maak-en update uit te voeren op Data Lake Storage Gen1 en de resultaten te verzenden naar uw bewakings oplossing. De bewerkingen kunnen worden uitgevoerd in een tijdelijke map en vervolgens na de test worden verwijderd. Dit kan elke 30-60 seconden worden uitgevoerd, afhankelijk van de vereisten.

## <a name="directory-layout-considerations"></a>Overwegingen voor Directory-indeling

Wanneer gegevens naar een Data Lake worden gelandd, is het belang rijk om de structuur van de gegevens vooraf te plannen, zodat de beveiliging, partitionering en verwerking effectief kunnen worden gebruikt. Veel van de volgende aanbevelingen kunnen worden gebruikt in combi natie met Azure Data Lake Storage Gen1, Blob Storage of HDFS. Elke workload heeft verschillende vereisten voor de manier waarop de gegevens worden verbruikt, maar hieronder volgen enkele algemene indelingen waarmee u rekening moet houden bij het werken met IoT-en batch-scenario's.

### <a name="iot-structure"></a>IoT-structuur

In IoT-workloads kunnen er grote hoeveel heden gegevens in het gegevens archief worden gelandd dat over talloze producten, apparaten, organisaties en klanten kan worden verspreid. Het is belang rijk om vooraf de Directory-indeling te plannen voor de organisatie, beveiliging en efficiënte verwerking van de gegevens voor gebruikers van de andere stroom. Een algemene sjabloon waarmee u rekening moet houden, kan de volgende indeling hebben:

```console
{Region}/{SubjectMatter(s)}/{yyyy}/{mm}/{dd}/{hh}/
```

Bijvoorbeeld, telemetrie voor een vliegtuig motor binnen het Verenigd Konink rijk kan er als volgt uitzien:

```console
UK/Planes/BA1293/Engine1/2017/08/11/12/
```

Er is een belang rijke reden om de datum toe te voegen aan het einde van de mapstructuur. Als u bepaalde regio's of onderwerpen wilt vergren delen voor gebruikers/groepen, kunt u dit eenvoudig doen met de POSIX-machtigingen. Als er anders een bepaalde beveiligings groep moet worden beperkt om alleen de UK-gegevens of bepaalde abonnementen weer te geven, is de datum structuur voor een afzonderlijke machtiging vereist voor talloze mappen onder elke map uur. Als de datum structuur op de voor grond ligt, wordt het aantal mappen zo exponentieel verhoogd dat er een tijd is opgetreden.

### <a name="batch-jobs-structure"></a>Structuur batch taken

Vanuit een hoog niveau is een veelgebruikte aanpak in batch verwerking het gebruik van gegevens in een map in. Zodra de gegevens zijn verwerkt, plaatst u de nieuwe gegevens in een out-map voor downstream-processen die u wilt gebruiken. Deze mapstructuur is soms ook bedoeld voor taken die verwerking op afzonderlijke bestanden vereisen en mogelijk geen zeer parallelle verwerking vereist voor grote gegevens sets. Net zoals de IoT-structuur die hierboven wordt aanbevolen, heeft een goede Directory structuur de bovenliggende mappen voor zaken als regio en onderwerpen (bijvoorbeeld organisatie, product/producent). Deze structuur helpt bij het beveiligen van de gegevens in uw organisatie en het betere beheer van de gegevens in uw workloads. Denk bovendien na over de datum en tijd in de structuur om een betere organisatie, gefilterde Zoek opdrachten, beveiliging en automatisering in de verwerking mogelijk te maken. Het granulatie niveau voor de datum structuur wordt bepaald door het interval waarop de gegevens worden geüpload of verwerkt, zoals elk uur, dagelijks of zelfs maandelijks.

Soms wordt bestands verwerking niet geslaagd als gevolg van gegevens beschadiging of onverwachte notaties. In dergelijke gevallen kan de mapstructuur van een **/Bad** -map de bestanden naar aanleiding van de nieuwe inspectie verplaatsen. De batch-taak kan ook de rapportage of melding van deze *beschadigde* bestanden verwerken voor hand matige tussen komst. Houd rekening met de volgende sjabloon structuur:

```console
{Region}/{SubjectMatter(s)}/In/{yyyy}/{mm}/{dd}/{hh}/
{Region}/{SubjectMatter(s)}/Out/{yyyy}/{mm}/{dd}/{hh}/
{Region}/{SubjectMatter(s)}/Bad/{yyyy}/{mm}/{dd}/{hh}/
```

Zo ontvangt een marketing bedrijf dagelijks gegevens uittreksels van klant updates van hun clients in Noord-Amerika. Het kan er voor en na het verwerken van het volgende fragment uitzien:

```console
NA/Extracts/ACMEPaperCo/In/2017/08/14/updates_08142017.csv
NA/Extracts/ACMEPaperCo/Out/2017/08/14/processed_updates_08142017.csv
```

In het gemeen schappelijke geval van batch gegevens die rechtstreeks in data bases worden verwerkt, zoals Hive of traditionele SQL-data bases, is er geen behoefte aan een **/in** -of **/out** -map omdat de uitvoer al naar een afzonderlijke map voor de Hive-tabel of externe data base gaat. Dagelijkse uittreksels van klanten zijn bijvoorbeeld in hun respectieve mappen gelandd, en met de indeling van een probleem zoals Azure Data Factory, Apache Oozie of Apache-lucht flow wordt een dagelijkse Hive-of Spark-taak geactiveerd om de gegevens in een Hive-tabel te verwerken en te schrijven.

## <a name="next-steps"></a>Volgende stappen

* [Overzicht van Azure Data Lake Storage Gen1](data-lake-store-overview.md)
* [Access Control in Azure Data Lake Storage Gen1](data-lake-store-access-control.md)
* [Beveiliging in Azure Data Lake Storage Gen1](data-lake-store-security-overview.md)
* [Azure Data Lake Storage Gen1 afstemmen voor prestaties](data-lake-store-performance-tuning-guidance.md)
* [Richt lijnen voor het afstemmen van de prestaties voor het gebruik van HDInsight Spark met Azure Data Lake Storage Gen1](data-lake-store-performance-tuning-spark.md)
* [Richt lijnen voor het afstemmen van de prestaties voor het gebruik van HDInsight Hive met Azure Data Lake Storage Gen1](data-lake-store-performance-tuning-hive.md)
* [HDInsight-clusters maken met Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)
