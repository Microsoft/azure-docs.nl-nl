---
title: Azure Block Chain Service (ABS) bewaken
description: De Azure Block Chain-Service bewaken via Azure Monitor
ms.date: 01/08/2020
ms.topic: how-to
ms.reviewer: v-umha
ms.openlocfilehash: 05147f48c4cde4cc97bf6cc9cae5c8220a389ebd
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100594914"
---
# <a name="monitor-azure-blockchain-service-through-azure-monitor"></a>De Azure Block Chain-Service bewaken via Azure Monitor  

Als klanten Block Chain-scenario's voor de productie kwaliteit uitvoeren op Azure Block Chain Service (ABS), is het van cruciaal belang om de resources te controleren op Beschik baarheid, prestaties en bewerkingen. In dit artikel worden de bewakings gegevens beschreven die worden gegenereerd door de Azure Block Chain-service en hoe een van de verschillende functies en integraties van Azure Monitor kan worden gebruikt voor het analyseren en waarschuwen van, voor het beheren van productie-en test omgevingen.  

## <a name="what-is-azure-monitor"></a>Wat is Azure Monitor?

Met de Azure Block Chain-service worden bewakings gegevens gemaakt met behulp van Azure Monitor. Dit is een volledige stack monitoring-service in azure die een volledige set functies biedt om uw Azure-resources te controleren. Zie voor meer informatie over Azure Monitor [Azure-resources bewaken met Azure monitor](../../azure-monitor/essentials/monitor-azure-resource.md).
 

In de volgende secties vindt u een beschrijving van de specifieke gegevens die zijn verzameld uit de Azure Block Chain-service en vindt u voor beelden voor het configureren van gegevens verzameling en het analyseren van deze gegevens met Azure-hulpprogram ma's.

## <a name="monitor-data-collected-from-azure-blockchain-service"></a>Gegevens controleren die zijn verzameld uit de Azure Block Chain-Service  

De Azure Block Chain-service verzamelt dezelfde soort bewakings gegevens als andere Azure-resources, die worden beschreven in gegevens van Azure-resources [bewaken](../../azure-monitor/essentials/monitor-azure-resource.md#monitoring-data) . Zie [Azure Block Chain service data Reference bewaken](#monitor-azure-blockchain-service-data-reference) voor een gedetailleerde Naslag informatie over de logboeken en metrische gegevens die door de Azure Block Chain-service zijn gemaakt.

De overzichts pagina in de Azure Portal voor elke resource van een Azure Block Chain service-lid bevat een korte weer gave van de trans acties, met inbegrip van de verwerkte aanvragen en geverwerkte blokken. Sommige van deze gegevens worden automatisch verzameld en beschikbaar voor analyse wanneer u de resource van de Azure Block Chain service-lid maakt, terwijl u extra gegevens verzameling kunt inschakelen met aanvullende configuratie.

## <a name="diagnostic-settings"></a>Diagnostische instellingen  

De metrische gegevens van het platform en het activiteiten logboek worden automatisch verzameld, maar u moet een diagnostische instelling maken om bron logboeken te verzamelen of deze buiten Azure Monitor door te sturen. Zie [Diagnostische instelling maken voor het verzamelen van platform logboeken en metrische gegevens in azure](../../azure-monitor/essentials/diagnostic-settings.md) voor het gedetailleerde proces voor het maken van een diagnostische instelling met behulp van de Azure Portal, CLI of Power shell.

Wanneer u een diagnostische instelling maakt, geeft u op welke categorieën logboeken u wilt verzamelen. De categorieën voor de Azure Block Chain-service worden hieronder weer gegeven.

**Block Chain-proxy logboeken** : Selecteer de categorie als u de NGNIX-proxy logboeken wilt bewaken. Alle details van de klant transacties zijn beschikbaar voor het doel van de controle en de fout opsporing.  

**Block Chain-toepassings logboeken** : Selecteer de categorie om logboeken op te halen van de Block Chain-toepassing die door de beheerde service wordt gehost. Bijvoorbeeld: voor een ABS-Quorum-lid zijn deze logboeken de logboeken van quorum zelf.  

**Metrische aanvragen**: Selecteer de optie voor het verzamelen van metrische gegevens van Azure Cosmos DB naar de doelen in de diagnostische instelling, die automatisch wordt verzameld in de metrische waarden van Azure. Verzamelen van metrische gegevens met resource logboeken voor het analyseren van beide soorten gegevens en het verzenden van metrische gegevens buiten Azure Monitor.

## <a name="analyze-metric-data"></a>Metrische gegevens analyseren  

U kunt de metrische gegevens voor de Azure Block Chain-service analyseren met metrische gegevens Verkenner door te navigeren naar het tabblad metrische gegevens onder bewaking in de Blade ABS-resource. Zie [aan de slag met Azure Metrics Explorer](../../azure-monitor/essentials/metrics-getting-started.md) voor meer informatie over het gebruik van het hulp programma. De volledige metrische gegevens voor de Azure Block Chain-service bevinden zich in de standaard gegevens van de naam ruimte Azure Block Chain service.

U kunt **knooppunt** dimensie gebruiken bij het toevoegen van een filter of het splitsen van metrische gegevens, wat in principe meet waarden per transactie knooppunten en validatie knooppunten van het lid ABS levert.

## <a name="analyze-log-data"></a>Logboekgegevens analyseren

Hier volgen enkele query's die u kunt invoeren in de zoek balk van het logboek om u te helpen bij het bewaken van de leden van de Azure Block Chain-service. Deze query's werken met de [nieuwe taal](../../azure-monitor/logs/log-query-overview.md).

Als u de fout voorwaarden in de Block Chain-toepassings logboeken wilt opvragen, gebruikt u de onderstaande query:

```
BlockchainApplicationLog | where BlockchainMessage contains "ERROR" or BlockchainMessage contains "fatal"

```

Als u de fout voorwaarden in de Block Chain-proxy logboeken wilt opvragen, gebruikt u de onderstaande query  


```
BlockchainProxyLog
| filter Code != 200
| limit 500

```
U kunt de tijd filters die beschikbaar zijn in azure-Logboeken gebruiken om de query te filteren op een specifiek tijds bereik.

## <a name="monitor-azure-blockchain-service-data-reference"></a>Naslag informatie over Azure Block Chain service-gegevens bewaken  

Dit artikel bevat een verwijzing naar logboek-en metrische gegevens die zijn verzameld voor het analyseren van de prestaties en beschik baarheid van de Azure Block Chain-service.  

### <a name="resource-logs"></a>Resourcelogboeken

Alle bron logboeken delen een gemeen schappelijk schema op het hoogste niveau met enkele unieke eigenschappen die specifiek zijn voor de Block Chain-service. U kunt verwijzen naar het artikel op het [hoogste niveau bron logboeken](../../azure-monitor/essentials/resource-logs-schema.md#top-level-common-schema)van de Azure Block Chain-service. Hieronder vindt u meer informatie over de specifieke eigenschappen  

De volgende tabel geeft een lijst van de eigenschappen voor Azure Block Chain-proxy Logboeken wanneer deze worden verzameld in Azure Monitor Logboeken of Azure Storage.  


| Naam van eigenschap  | Description |
|:---|:---|
| tijd | De datum en tijd (UTC) waarop de bewerking plaatsvond. |
| resourceID  | De Azure Block Chain-Service resource waarvoor logboeken zijn ingeschakeld.  |
| category  |Voor de Azure Block Chain-service zijn de mogelijke waarden **Proxylogs** en **Applicationlogs**. |
| operationName  | De naam van de bewerking die door deze gebeurtenis wordt vertegenwoordigd.   |
| Logboek niveau  | De Azure Block Chain-service maakt standaard het logboek niveau van **informatie** .   |
| NodeLocation  | Azure-regio waar het block Chain-lid wordt geïmplementeerd.  |
| BlockchainNodeName  | De naam van het knoop punt van het Azure Block Chain-service-lid waarop de bewerking wordt uitgevoerd.   |
| EthMethod  | De methode, die wordt aangeroepen door het onderliggende Block Chain-protocol, kan in quorum worden eth_sendTransactions, eth_getBlockByNumber enz.  |
| Agent  | De gebruikers agent die optreedt namens een gebruiker, zoals de webbrowser Mozilla, Edge, enzovoort. Voor beelden van de waarden zijn: ' Mozilla/5.0 (Linux x64) node.js/8.16.0 V8/6.2.414.77 '  |
| Code   | HTTP-fout codes. Normaal gesp roken 4XX en 5XX de fout voorwaarden.  |
| NodeHost  | De DNS-naam van het knoop punt.   |
| RequestMethodName | De HTTP-methode met de naam, de mogelijke waarden die hier worden weer gegeven, zijn voor het maken van leden, het ophalen van Details van het bestaande lid, verwijderen voor het lid verwijderen, PATCH voor het bijwerken van het lid.   |
| BlockchainMemberName  | De lidnaam van de Azure Block Chain-service die door de gebruiker wordt gegeven.  |
| Consortium | Naam van het consortium zoals gegeven door de gebruiker.   |
| Extern  | Het IP-adres van de client waar de aanvraag vandaan komt.  |
| RequestSize  | Grootte van de aanvraag die in bytes is gemaakt.  |
| RequestTime  | De duur van de aanvraag in milliseconden.|




De volgende tabel bevat de eigenschappen voor Azure Block Chain-toepassings Logboeken.


| Naam van eigenschap  | Description |
|:---|:---|
| tijd | De datum en tijd (UTC) waarop de bewerking plaatsvond. |
| resourceID  | De Azure Block Chain-Service resource waarvoor logboeken zijn ingeschakeld.|
| category  |Voor de Azure Block Chain-service zijn de mogelijke waarden **Proxylogs** en **Applicationlogs**.  |
| operationName  | De naam van de bewerking die door deze gebeurtenis wordt vertegenwoordigd.   |
| Logboek niveau  | De Azure Block Chain-service maakt standaard het logboek niveau van **informatie** .   |
| NodeLocation  | Azure-regio waar het block Chain-lid wordt geïmplementeerd.  |
| BlockchainNodeName  | De naam van het knoop punt van het Azure Block Chain-service-lid waarop de bewerking wordt uitgevoerd.   |
| BlockchainMessage    | Dit veld bevat het block Chain-toepassings logboek dat de logboeken voor gegevens zonder opmaak vormt. Voor ABS-quorum zouden er quorum logboeken zijn. Het bevat informatie over het type logboek vermelding dat er informatie is, fout, waarschuwing en een teken reeks die meer informatie geven over de uitgevoerde actie.   |
| TenantID    | De regiospecifieke Tenant van de Azure Block Chain-service. De indeling van dit veld is https://westlake-rp-prod . <region> .. cloudapp.azure.com waarbij regio de Azure-regio van het geïmplementeerde lid aangeeft.       |
| SourceSystem   | Het systeem vult de logboeken in dit geval **Azure**.    |



### <a name="metrics"></a>Metrische gegevens

In de volgende tabellen worden de platform gegevens weer gegeven die zijn verzameld voor de Azure Block Chain-service. Alle metrische gegevens worden opgeslagen in de standaard gegevens van de naam ruimte **Azure Block Chain Service** .

Zie [Azure monitor ondersteunde metrische gegevens](../../azure-monitor/essentials/metrics-supported.md)voor een lijst met alle Azure monitor ondersteunde metrische gegevens (inclusief de Azure Block Chain-Service).

### <a name="blockchain-metrics"></a>Metrische gegevens van Block Chain

De volgende tabel bevat de lijst met block Chain-metrische gegevens die worden verzameld voor de resource van de Azure Block Chain service-lid.


| Naam van metrische gegevens | Eenheid  |  Type aggregatie| Description   |
|---|---|---|---|
| Trans acties in behandeling   | Count  |  Gemiddeld | Het aantal trans acties dat wacht om te worden ook niet gebruikt.   |
| Verwerkte blokken   | Count  | Sum  |  Het aantal blokken dat in elk tijds interval wordt verwerkt. Op dit moment is de blok grootte 5 seconden. in een minuut worden 12 blokken en 60 blokken in vijf minuten in elk knoop punt verwerkt.   |
|Verwerkte trans acties    | Count  | Sum  | Het aantal trans acties dat in een blok is verwerkt.    |
|Trans acties in de wachtrij    |  Count | Gemiddeld  | Het aantal trans acties dat niet direct kan worden ook niet gebruikt. Het kan zijn dat ze niet in de juiste volg orde zijn aangekomen en dat de volgende trans actie in de toekomst wordt gewacht. Het kan ook zijn dat twee trans acties hetzelfde nummer hebben als één keer (nonce) en dezelfde aardgas waarde, dus de tweede kan niet ook niet gebruikt zijn.   |

### <a name="connection-metrics"></a>Verbindings gegevens  

De volgende tabel geeft een lijst van de verschillende metrische verbindings gegevens die worden verzameld voor de resource van de Azure Block Chain service-lid. Dit zijn de metrische gegevens van de NGINX-proxy.


| Naam van metrische gegevens | Eenheid  |  Type aggregatie| Description |
|---|---|---|---|
| Geaccepteerde verbindingen   | Count  |  Sum | Het totale aantal geaccepteerde client verbindingen.   |
| Actieve verbindingen  | Count  | Gemiddeld  |  Het huidige aantal actieve client verbindingen, inclusief wachtende verbindingen.    |
|Afgehandelde verbindingen    | Count  | Sum  | Het totale aantal verwerkte verbindingen. Over het algemeen is de parameter waarde hetzelfde als geaccepteerde verbindingen, tenzij sommige resource limieten zijn bereikt.     |
|Verwerkte aanvragen     |  Count | Sum  | Het totale aantal client aanvragen.  |


### <a name="performance-metrics"></a>Metrische gegevens voor prestaties

De volgende tabel bevat de prestatie gegevens die worden verzameld voor elk van de knoop punten van de Azure Block Chain-lid-resource.  


| Naam van metrische gegevens | Eenheid  |  Type aggregatie| Description   |
|---|---|---|---|
| Percentage CPU-gebruik   | Percentage  |  Max | Het percentage van het CPU-gebruik.     |
| I/o gelezen bytes   | Kilo bytes   | Sum  |  De som van i/o-Lees bewerkingen op alle knoop punten van de Block chain.      |
|I/o-schrijf bytes     | Kilo bytes   | Sum  | De som van i/o-schrijf bewerkingen op alle knoop punten van de Block chain.     |
|Geheugen limiet       |  Gigabytes   | Gemiddeld    | Maxi maal beschikbaar geheugen voor het block chain-proces per knoop punt. |
|Geheugengebruik     | Gigabytes  |  Gemiddeld | De hoeveelheid geheugen die gemiddeld op alle knoop punten wordt gebruikt.  |
| Percentage geheugen gebruik     | Percentage   | Gemiddeld  |  Het percentage van het geheugen dat gemiddeld op alle knoop punten wordt gebruikt.       |
|Opslag gebruik      | Gigabytes   | Gemiddeld  | De hoeveelheid opslag die gemiddeld per knoop punt wordt gebruikt.       |


## <a name="next-steps"></a>Volgende stappen

Meer informatie over [block chain data manager](./data-manager.md) voor het vastleggen en transformeren van Block Chain-gegevens naar Azure Event grid.
