---
title: Problemen met het indelen van pijp lijnen en triggers in Azure Data Factory oplossen
description: Gebruik verschillende methoden voor het oplossen van problemen met pijp lijn triggers in Azure Data Factory.
author: ssabat
ms.service: data-factory
ms.date: 03/13/2021
ms.topic: troubleshooting
ms.author: susabat
ms.reviewer: susabat
ms.openlocfilehash: 72f2a5eec25b9acc2aedd7b006fe3380141781c8
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2021
ms.locfileid: "105563409"
---
# <a name="troubleshoot-pipeline-orchestration-and-triggers-in-azure-data-factory"></a>Problemen met het indelen van pijp lijnen en triggers in Azure Data Factory oplossen

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Een pijplijnuitvoering in Azure Data Factory definieert een exemplaar van een pijplijnuitvoering. Stel bijvoorbeeld dat u een pijp lijn hebt die wordt uitgevoerd om 8:00 uur, 9:00 uur en 10:00 uur. In dit geval zijn er drie afzonderlijke pijplijn uitvoeringen. Elke pijplijnuitvoering heeft een unieke id. Een run-ID is een Globally Unique Identifier (GUID) die de specifieke pijplijn uitvoering definieert.

Pijplijnuitvoeringen worden doorgaans geïnstantieerd doordat argumenten worden doorgegeven aan parameters die u in de pijplijn definieert. U kunt een pijp lijn ofwel hand matig of via een trigger uitvoeren. Zie [pijp lijnen uitvoeren en triggers in azure Data Factory](concepts-pipeline-execution-triggers.md) voor meer informatie.

## <a name="common-issues-causes-and-solutions"></a>Veelvoorkomende problemen, oorzaken en oplossingen

### <a name="an-azure-functions-app-pipeline-throws-an-error-with-private-endpoint-connectivity"></a>Een Azure Functions app-pijp lijn genereert een fout met de connectiviteit van een persoonlijk eind punt
 
U hebt Data Factory en een Azure-functie-app die wordt uitgevoerd op een persoonlijk eind punt. U probeert een pijp lijn uit te voeren die samenwerkt met de functie-app. U hebt drie verschillende methoden geprobeerd, maar er is een fout ' ongeldige aanvraag ' geretourneerd en de andere twee methoden retour neren ' 103-fout verboden '.

**Oorzaak**

Data Factory biedt momenteel geen ondersteuning voor een privé-eindpunt connector voor functie-apps. Azure Functions weigert aanroepen omdat het is geconfigureerd om alleen verbindingen van een persoonlijke koppeling toe te staan.

**Oplossing**

Maak een **PrivateLinkService** -eind punt en geef de DNS van uw functie-app op.

### <a name="a-pipeline-run-is-canceled-but-the-monitor-still-shows-progress-status"></a>Een pijplijn uitvoering wordt geannuleerd, maar de monitor wordt nog steeds de voortgangs status weer gegeven

**Oorzaak**

Wanneer u de uitvoering van een pijp lijn annuleert, wordt in de pipeline-bewaking vaak nog steeds de voortgangs status weer gegeven. Dit gebeurt vanwege een probleem met de browser cache. Mogelijk hebt u ook niet de juiste controle filters.

**Oplossing**

Vernieuw de browser en pas de juiste bewakings filters toe.
 
### <a name="you-see-a-delimitedtextmorecolumnsthandefined-error-when-copying-a-pipeline"></a>U ziet de fout ' DelimitedTextMoreColumnsThanDefined ' bij het kopiëren van een pijp lijn
 
 **Oorzaak**
 
Als een map die u kopieert bestanden bevat met verschillende schema's, zoals een variabel aantal kolommen, een andere scheidings teken, instellingen voor de aanhalings tekens of een gegeven gegevens probleem, kan de Data Factory pijp lijn deze fout veroorzaken:

`
Operation on target Copy_sks  failed: Failure happened on 'Sink' side.
ErrorCode=DelimitedTextMoreColumnsThanDefined,
'Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,
Message=Error found when processing 'Csv/Tsv Format Text' source '0_2020_11_09_11_43_32.avro' with row number 53: found more columns than expected column count 27.,
Source=Microsoft.DataTransfer.Common,'
`

**Oplossing**

Selecteer de optie voor **binaire kopieën** tijdens het maken van de Kopieer activiteit. Op deze manier kan Data Factory de bestanden niet openen om het schema te lezen, om uw gegevens te kopiëren of te migreren van de ene data Lake naar een andere. In plaats daarvan behandelt Data Factory elk bestand als binair en kopieert u het naar de andere locatie.

### <a name="a-pipeline-run-fails-when-you-reach-the-capacity-limit-of-the-integration-runtime-for-data-flow"></a>Een pijplijn uitvoering mislukt wanneer u de capaciteits limiet van de Integration runtime voor de gegevens stroom bereikt

**Name**

Foutbericht:

`
Type=Microsoft.DataTransfer.Execution.Core.ExecutionException,Message=There are substantial concurrent MappingDataflow executions which is causing failures due to throttling under Integration Runtime 'AutoResolveIntegrationRuntime'.
`

**Oorzaak**

U hebt de capaciteits limiet van de Integration runtime bereikt. U kunt een grote hoeveelheid gegevens stroom uitvoeren met behulp van dezelfde Integration runtime op hetzelfde moment. Zie [Azure-abonnement en service limieten, quota's en beperkingen](../azure-resource-manager/management/azure-subscription-service-limits.md#version-2) voor meer informatie.

**Oplossing**
 
- Voer uw pijp lijnen uit op verschillende tijdstippen van de trigger.
- Maak een nieuwe Integration runtime en Splits uw pijp lijnen op meerdere integratie-Runtimes.

### <a name="how-to-perform-activity-level-errors-and-failures-in-pipelines"></a>Fouten en storingen op activiteit niveau uitvoeren in pijp lijnen

**Oorzaak**

Met Azure Data Factory indeling wordt voorwaardelijke logica toegestaan en kunnen gebruikers verschillende paden maken op basis van het resultaat van een vorige activiteit. Er kunnen vier voorwaardelijke paden worden ingesteld: wanneer dit is **gelukt** (standaard geslaagd), na het **mislukken** van de **voltooiing** en **bij overs Laan**. 

Azure Data Factory evalueert het resultaat van alle activiteiten op Leaf-niveau. De resultaten van de pijp lijn slagen alleen als alle bladeren slagen. Als een Leaf-activiteit is overgeslagen, evalueren we de bovenliggende activiteit in plaats daarvan. 

**Oplossing**

* Implementeer controles op activiteit niveau door [te volgen hoe er pijp lijn fouten en-fouten worden afgehandeld](https://techcommunity.microsoft.com/t5/azure-data-factory/understanding-pipeline-failures-and-error-handling/ba-p/1630459).
* Gebruik Azure Logic Apps voor het bewaken van pijp lijnen met regel matige intervallen na het [uitvoeren van een query op Factory](/rest/api/datafactory/pipelineruns/querybyfactory).
* [Pijp lijn visueel bewaken](./monitor-visually.md)

### <a name="how-to-monitor-pipeline-failures-in-regular-intervals"></a>Pijp lijn fouten met regel matige intervallen bewaken

**Oorzaak**

Mogelijk moet u de mislukte Data Factory pijp lijnen in intervallen controleren, bijvoorbeeld 5 minuten. U kunt de pijp lijn uitvoeringen opvragen en filteren vanuit een data factory met behulp van het eind punt. 

**Oplossing**
* U kunt een Azure Logic-app zo instellen dat elke 5 minuten een query wordt uitgevoerd op alle mislukte pijp lijnen, zoals wordt beschreven in [query op Factory](/rest/api/datafactory/pipelineruns/querybyfactory). Vervolgens kunt u incidenten rapporteren aan uw ticket systeem.
* [Pijp lijn visueel bewaken](./monitor-visually.md)

### <a name="degree-of-parallelism--increase-does-not-result-in-higher-throughput"></a>De mate van parallellisme toename resulteert niet in een hogere door Voer

**Oorzaak** 

De mate van parallelle uitvoering in *foreach* is in feite de maximale mate van parallelle uitvoering. Er kan niet worden gegarandeerd dat er op hetzelfde moment een specifiek aantal uitvoeringen wordt uitgevoerd, maar deze para meter garandeert echter nooit meer dan de ingestelde waarde. U ziet dit als een limiet voor gebruik bij het beheren van gelijktijdige toegang tot uw bronnen en Sinks.

Bekende feiten over *foreach*
 * Foreach heeft een eigenschap met de naam batch Count (n), waarbij de standaard waarde 20 is en het maximum 50 is.
 * Het aantal batches, n, wordt gebruikt om n wacht rijen samen te stellen. Later bespreken we enkele details over hoe deze wacht rijen worden samengesteld.
 * Elke wachtrij wordt opeenvolgend uitgevoerd, maar u kunt meerdere wacht rijen tegelijk uitvoeren.
 * De wacht rijen worden vooraf gemaakt. Dit betekent dat er tijdens de runtime geen herverdeling van de wacht rijen is.
 * U hebt op elk moment Maxi maal één item per wachtrij verwerken. Dit betekent dat de meeste n items op een bepaald moment worden verwerkt.
 * De totale verwerkings tijd van foreach is gelijk aan de verwerkings tijd van de langste wachtrij. Dit betekent dat de foreach-activiteit afhankelijk is van de manier waarop de wacht rijen worden samengesteld.
 
**Oplossing**

 * Gebruik geen *SetVariable* -activiteit in *voor elke* die parallel wordt uitgevoerd.
 * Rekening houdend met de manier waarop de wacht rijen zijn gebouwd, kan de klant de foreach-prestaties verbeteren door meerdere *foreachs* in te stellen waarbij elke foreach items met een vergelijk bare verwerkings tijd bevat. Zo zorgt u ervoor dat lange uitvoeringen parallel op een opeenvolgende volg orde worden verwerkt.

 ### <a name="pipeline-status-is-queued-or-stuck-for-a-long-time"></a>Pijplijn status wordt gedurende lange tijd in de wachtrij geplaatst of vastgelopen
 
 **Oorzaak**
 
 Dit kan gebeuren om verschillende redenen, zoals het terugverdienen van gelijktijdigheids limieten, service storingen, netwerk fouten, enzovoort.
 
 **Oplossing**
 
* Gelijktijdigheids limiet: als uw pijp lijn een gelijktijdigheids beleid heeft, controleert u of er geen oude pijplijn uitvoeringen worden uitgevoerd. De Maxi maal toegestane pijplijn gelijktijdigheid in Azure Data Factory is 10 pijp lijnen. 
* Bewakings limieten: Ga naar het ADF-schrijf doek, selecteer uw pijp lijn en bepaal of er een gelijktijdigheids eigenschap aan is toegewezen. Als dat het geval is, gaat u naar de weer gave bewaking en controleert u of er niets is in de afgelopen 45 dagen die worden uitgevoerd. Als er een bewerking wordt uitgevoerd, kunt u deze annuleren en de nieuwe uitvoering van de pijp lijn starten.
* Tijdelijke problemen: het is mogelijk dat de uitvoering werd beïnvloed door een tijdelijk netwerk probleem, referentie fouten, storingen van de services, enzovoort.  Als dit het geval is, heeft Azure Data Factory een intern herstel proces dat alle uitvoeringen bewaakt en worden gestart wanneer er iets verkeerd is gegaan. Dit proces gebeurt elk één uur, dus als de uitvoering langer dan een uur duurt, kunt u een ondersteunings aanvraag maken.
 
### <a name="longer-start-up-times-for-activities-in-adf-copy-and-data-flow"></a>Langere start tijden voor activiteiten in ADF-kopie en gegevens stroom

**Oorzaak**

Dit kan gebeuren als u geen tijd hebt geïmplementeerd voor een Live-functie voor gegevens stromen of geoptimaliseerde SHIR.

**Oplossing**

* Als elke kopieeractiviteit maximaal 2 minuten duurt om te starten, en het probleem zich voornamelijk voordoet bij een VNet-koppeling (in tegenstelling tot een Azure IR), kan dit een prestatieprobleem bij het kopiëren zijn. Als u de stappen voor het oplossen van problemen wilt bekijken, gaat u naar [Kopieer prestaties verbeteren.](./copy-activity-performance-troubleshooting.md)
* U kunt de functie time to Live gebruiken om de start tijd van het cluster voor gegevens stroom activiteiten te verlagen. Controleer Integration Runtime van de [gegevens stroom.](./control-flow-execute-data-flow-activity.md#data-flow-integration-runtime)

 ### <a name="hitting-capacity-issues-in-shirself-hosted-integration-runtime"></a>Problemen met de capaciteit van de SHIR (zelf-Hostend Integration Runtime)
 
 **Oorzaak**
 
Dit kan gebeuren als u SHIR niet hebt geschaald volgens uw werk belasting.

**Oplossing**

* Als er een probleem met de capaciteit van SHIR optreedt, voert u een upgrade uit voor de virtuele machine om het knoop punt te verg Roten om de activiteiten te salderen Als er een fout bericht wordt weer gegeven over een zelf-hostende IR-fout of-fout, een zelf-hostende IR-upgrade of zelf-hostende IR-verbindings problemen, die een lange wachtrij kunnen genereren, gaat u naar [problemen met zelf-hostende Integration runtime oplossen.](./self-hosted-integration-runtime-troubleshoot-guide.md)

### <a name="error-messages-due-to-long-queues-for-adf-copy-and-data-flow"></a>Fout berichten als gevolg van lange wacht rijen voor de ADF-kopie en de gegevens stroom

**Oorzaak**

Lange wachtrij-gerelateerde fout berichten kunnen om verschillende redenen worden weer gegeven. 

**Oplossing**
* Als u een fout bericht van een wille keurige bron of bestemming ontvangt via connectors, waarmee u een lange wachtrij kunt genereren, gaat u naar [Connector Troubleshooting Guide (Engelstalig).](./connector-troubleshoot-guide.md)
* Als u een fout bericht ontvangt over het toewijzen van gegevens stroom, waardoor een lange wachtrij kan worden gegenereerd, gaat u naar de gids voor het [oplossen van problemen met gegevens stromen.](./data-flow-troubleshoot-guide.md)
* Als u een fout bericht ontvangt over andere activiteiten, zoals Databricks, aangepaste activiteiten of HDI, die een lange wachtrij kunnen genereren, gaat u naar [activiteiten gids voor het oplossen van problemen.](./data-factory-troubleshoot-guide.md)
* Als er een fout bericht wordt weer gegeven over het uitvoeren van SSIS-pakketten, die een lange wachtrij kan genereren, gaat u naar de [probleemoplossings gids voor het oplossen van problemen met het Azure-SSIS-pakket](./ssis-integration-runtime-ssis-activity-faq.md) en de [richt lijnen](./ssis-integration-runtime-management-troubleshoot.md) voor het oplossen van Integration runtime


## <a name="next-steps"></a>Volgende stappen

Probeer deze bronnen voor meer informatie over probleem oplossing:

*  [Data Factory Blog](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Data Factory functie aanvragen](https://feedback.azure.com/forums/270578-data-factory)
*  [Azure-video's](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Microsoft Q&A-vragenpagina](/answers/topics/azure-data-factory.html)
*  [Twitter-informatie over Data Factory](https://twitter.com/hashtag/DataFactory)