---
title: Problemen met het indelen van pijp lijnen en triggers in Azure Data Factory oplossen
description: Gebruik verschillende methoden voor het oplossen van problemen met pijp lijn triggers in Azure Data Factory.
author: ssabat
ms.service: data-factory
ms.date: 12/15/2020
ms.topic: troubleshooting
ms.author: susabat
ms.reviewer: susabat
ms.openlocfilehash: 0e67a316b012eda61607c84edfd8e10d6aa3318d
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/16/2020
ms.locfileid: "97589148"
---
# <a name="troubleshoot-pipeline-orchestration-and-triggers-in-azure-data-factory"></a>Problemen met het indelen van pijp lijnen en triggers in Azure Data Factory oplossen

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Een pijplijnuitvoering in Azure Data Factory definieert een exemplaar van een pijplijnuitvoering. Stel dat u een pijp lijn hebt die wordt uitgevoerd om 8:00 uur, 9:00 uur en 10:00 uur. In dit geval zijn er drie afzonderlijke uitvoeringen van de pijp lijn of pijplijn uitvoeringen. Elke pijplijnuitvoering heeft een unieke id. Een run-ID is een GUID (Globally Unique Identifier) die de specifieke pijplijn uitvoering definieert.

Pijplijnuitvoeringen worden doorgaans geïnstantieerd doordat argumenten worden doorgegeven aan parameters die u in de pijplijn definieert. U kunt een pijplijn handmatig uitvoeren of door middel van een trigger. Raadpleeg de [uitvoering van pijp lijnen en triggers in azure Data Factory](concepts-pipeline-execution-triggers.md) voor meer informatie.

## <a name="common-issues-causes-and-solutions"></a>Veelvoorkomende problemen, oorzaken en oplossingen

### <a name="pipeline-with-azure-function-throws-error-with-private-end-point-connectivity"></a>Pijp lijn met Azure function genereert een fout met persoonlijke eind punt connectiviteit
 
#### <a name="issue"></a>Probleem
Voor sommige context hebt u Data Factory en Azure functie-app uitgevoerd op een privé-eind punt. U probeert een pijp lijn op te halen die samenwerkt met de Azure-functie-app om te werken. U hebt drie verschillende methoden geprobeerd, maar er is een fout geretourneerd `Bad Request` , de andere twee methoden retour neren `103 Error Forbidden` .

#### <a name="cause"></a>Oorzaak 
Data Factory biedt momenteel geen ondersteuning voor een privé-eindpunt connector voor Azure functie-app. Dit moet de reden zijn waarom Azure functie-app de aanroepen weigert, omdat deze zo is geconfigureerd dat alleen verbindingen van een persoonlijke koppeling worden toegestaan.

#### <a name="resolution"></a>Oplossing
U kunt een persoonlijk eind punt van het type **PrivateLinkService** maken en de DNS van uw functie-app opgeven en de verbinding moet werken.

### <a name="pipeline-run-is-killed-but-the-monitor-still-shows-progress-status"></a>De pijplijn uitvoering is beëindigd, maar de monitor bevat nog steeds een voortgangs status

#### <a name="issue"></a>Probleem
Wanneer u een pijplijn uitvoering beëindigt, wordt de voortgangs status in de pipeline-bewaking nog steeds weer gegeven. Dit gebeurt vanwege het probleem in de cache in de browser en u beschikt niet over de juiste filters voor bewaking.

#### <a name="resolution"></a>Oplossing
Vernieuw de browser en pas de juiste filters voor bewaking toe.
 
### <a name="copy-pipeline-failure--found-more-columns-than-expected-column-count-delimitedtextmorecolumnsthandefined"></a>Fout bij kopiëren van pijp lijn-er zijn meer kolommen gevonden dan het verwachte aantal (DelimitedTextMoreColumnsThanDefined)

#### <a name="issue"></a>Probleem  
Als de bestanden onder een bepaalde map die u kopieert bestanden met verschillende schema's bevatten, zoals het variabele aantal kolommen, verschillende scheidings tekens, instellingen voor de aanhalings teken of een gegeven gegevens probleem, wordt de Data Factory pijp lijn beëindigd met de volgende fout:

`
Operation on target Copy_sks  failed: Failure happened on 'Sink' side.
ErrorCode=DelimitedTextMoreColumnsThanDefined,
'Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,
Message=Error found when processing 'Csv/Tsv Format Text' source '0_2020_11_09_11_43_32.avro' with row number 53: found more columns than expected column count 27.,
Source=Microsoft.DataTransfer.Common,'
`

#### <a name="resolution"></a>Oplossing
Selecteer de optie "binaire kopie" tijdens het maken van de Gegevens kopiëren activiteit. Op deze manier wordt voor het bulksgewijs kopiëren of migreren van uw gegevens van een Data Lake naar een andere, met een **binaire** optie, Data Factory de bestanden niet geopend om het schema te lezen, maar worden elk bestand als binair beschouwd en naar de andere locatie gekopieerd.

### <a name="pipeline-run-fails-when-capacity-limit-of-integration-runtime-is-reached"></a>De pijplijn uitvoering mislukt wanneer de capaciteits limiet van Integration runtime is bereikt

#### <a name="issue"></a>Probleem
Foutbericht:

`
Type=Microsoft.DataTransfer.Execution.Core.ExecutionException,Message=There are substantial concurrent MappingDataflow executions which is causing failures due to throttling under Integration Runtime 'AutoResolveIntegrationRuntime'.
`

De fout duidt op de beperking van per Integration runtime, die momenteel 50 is. Raadpleeg de [limieten](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#version-2) voor meer informatie.

Als u een grote hoeveelheid gegevens stroom met dezelfde Integration runtime tegelijk uitvoert, kan dit type fout optreden.

#### <a name="resolution"></a>Oplossing 
- Scheid deze pijp lijnen voor een andere trigger tijd om uit te voeren.
- Maak een nieuwe Integration runtime en splits deze pijp lijnen op meerdere integratie-Runtimes.

### <a name="how-to-monitor-pipeline-failures-on-regular-interval"></a>Pijp lijn fouten controleren op een regel matig interval

#### <a name="issue"></a>Probleem
Het is vaak nodig om Data Factory pijp lijnen in intervallen te bewaken, bijvoorbeeld 5 minuten. U kunt de pijplijn uitvoeringen opvragen en filteren vanuit een data factory met behulp van het eind punt. 

#### <a name="recommendation"></a>Aanbeveling
1. Stel een Azure Logic-app in om elke vijf minuten een query uit te zoeken op alle mislukte pijp lijnen.
2. Vervolgens kunt u incidenten melden aan ons ticket systeem op basis van de [QueryByFactory](https://docs.microsoft.com/rest/api/datafactory/pipelineruns/querybyfactory).

#### <a name="reference"></a>Referentie
- [Extern: meldingen verzenden van Data Factory](https://www.mssqltips.com/sqlservertip/5962/send-notifications-from-an-azure-data-factory-pipeline--part-2/)

### <a name="how-to-handle-activity-level-errors-and-failures-in-pipelines"></a>Fouten en storingen op activiteiten niveau in pijp lijnen afhandelen

#### <a name="issue"></a>Probleem
Met Azure Data Factory indeling wordt voorwaardelijke logica toegestaan en kunnen gebruikers verschillende paden maken op basis van de resultaten van een vorige activiteit. Hiermee kunnen vier voorwaardelijke paden worden ingesteld: "na geslaagd (standaard geslaagd)", "bij fout", "na voltooiing" en "bij overs Laan". Het gebruik van verschillende paden is toegestaan.

Azure Data Factory definieert een geslaagde en mislukte uitvoering van de pijp lijn als volgt:

- Evalueer het resultaat voor alle activiteiten op knooppunt niveau. Als een Leaf-activiteit is overgeslagen, evalueren we de bovenliggende activiteit in plaats daarvan.
- Het resultaat van de pijp lijn is geslaagd als en alleen als alle bladeren slagen.

#### <a name="recommendation"></a>Aanbeveling
- Implementeer controles op activiteiten niveau na [het afhandelen van pijp lijn fouten en fouten](https://techcommunity.microsoft.com/t5/azure-data-factory/understanding-pipeline-failures-and-error-handling/ba-p/1630459).
- Gebruik Azure Logic app voor het bewaken van pijp lijnen met regel matige intervallen na [query door DataFactory]( https://docs.microsoft.com/rest/api/datafactory/pipelineruns/querybyfactory).

## <a name="next-steps"></a>Volgende stappen

Probeer deze bronnen voor meer informatie over probleem oplossing:

*  [Data Factory Blog](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Data Factory functie aanvragen](https://feedback.azure.com/forums/270578-data-factory)
*  [Azure-video's](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Microsoft Q&A-vragenpagina](/answers/topics/azure-data-factory.html)
*  [Twitter-informatie over Data Factory](https://twitter.com/hashtag/DataFactory)