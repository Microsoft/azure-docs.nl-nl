---
title: bestand opnemen
description: bestand opnemen
services: data-factory
author: chez-charlie
ms.service: data-factory
ms.topic: include
ms.date: 11/16/2020
ms.author: chez
ms.custom: include file
ms.openlocfilehash: 10aa9b06af439fe701c53ef736ec691167560f95
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102109345"
---
Azure Data Factory is een service met meerdere tenants met de volgende standaardlimieten om ervoor te zorgen dat klantabonnementen worden beschermd tegen elkaars werkbelastingen. Neem contact op met de ondersteuning als u de limieten wilt verhogen tot de maximumwaarde voor uw abonnement.

### <a name="version-2"></a>Versie 2

| Resource | Standaardlimiet | Maximumaantal |
| -------- | ------------- | ------------- |
| Data factory's in een Azure-abonnement | 800 | 800 |
| Het totale aantal entiteiten, zoals pijplijnen, gegevenssets, triggers, gekoppelde services, privé-eindpunten en integratieruntimes, binnen een data factory | 5\.000 | [Neem contact op met ondersteuning](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Totale aantal CPU-kernen voor Azure-SSIS IR voor één abonnement | 256 | [Neem contact op met ondersteuning](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Gelijktijdige pijplijnuitvoeringen per data factory die worden gedeeld tussen alle pijplijnen in de factory | 10.000  | 10.000 |
| Gelijktijdige uitvoeringen van externe activiteiten per abonnement per [Azure Integration Runtime-regio](../articles/data-factory/concepts-integration-runtime.md#azure-ir-location)<br><small>Externe activiteiten worden beheerd op integratieruntime, maar worden uitgevoerd op gekoppelde services, waaronder Databricks, opgeslagen procedure, HDInsights, Web en overige. Deze limiet is niet van toepassing op zelf-hostende IR.</small> | 3000 | 3000 |
| Gelijktijdige uitvoeringen van pijplijnactiviteit per abonnement per [Azure Integration Runtime-regio](../articles/data-factory/concepts-integration-runtime.md#azure-ir-location) <br><small>Pijplijnactiviteiten worden uitgevoerd op integratieruntime, waaronder Lookup, GetMetadata en Delete. Deze limiet is niet van toepassing op zelf-hostende IR.</small> | 1000 | 1000                                                        |
| Gelijktijdige creatiebewerkingen per abonnement per [Azure Integration Runtime-regio](../articles/data-factory/concepts-integration-runtime.md#azure-ir-location)<br><small>Waaronder testverbinding, bladeren in mappenlijst en tabellijst, gegevens vooraf bekijken. Deze limiet is niet van toepassing op zelf-hostende IR.</small> | 200 | 200                                                          |
| Gelijktijdige verbruik van gegevensintegratie-eenheden<sup>1</sup> per abonnement per [Azure Integration Runtime-regio](../articles/data-factory/concepts-integration-runtime.md#integration-runtime-location)| Regiogroep 1<sup>2</sup>: 6.000<br>Regiogroep 2<sup>2</sup>: 3000<br>Regiogroep 3<sup>2</sup>: 1.500 | Regiogroep 1<sup>2</sup>: 6.000<br/>Regiogroep 2<sup>2</sup>: 3000<br/>Regiogroep 3<sup>2</sup>: 1.500 |
| Maximum aantal activiteiten per pijplijn, inclusief interne activiteiten voor containers | 40 | 40 |
| Maximum aantal gekoppelde integratieruntimes dat kan worden gemaakt voor één zelf-hostende IR | 100 | [Neem contact op met ondersteuning](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Maximum aantal parameters per pijplijn | 50 | 50 |
| ForEach-items | 100.000 | 100.000 |
| Parallelle ForEach-uitvoering | 20 | 50 |
| Maximum aantal uitvoeringen in de wachtrij per pijplijn | 100 | 100 |
| Tekens per expressie | 8\.192 | 8\.192 |
| Minimum aantal intervallen voor tumblingvenstertrigger | 15 min | 15 min |
| Maximale time-out voor uitvoeringen van pijplijnactiviteit | 7 dagen | 7 dagen |
| Bytes per object voor pijplijnobjecten<sup>3</sup> | 200 kB | 200 kB |
| Bytes per object voor gegevensset en gekoppelde serviceobjecten<sup>3</sup> | 100 kB | 2000 kB |
| Bytes per payload voor elke uitvoering van activiteit<sup>4</sup> | 896 KB | 896 KB |
| Gegevensintegratie-eenheden<sup>1</sup> per uitvoering van de kopieeractiviteit | 256 | 256 |
| API-aanroepen schrijven | 1200/u | 1200/u<br/><br/> Deze limiet wordt opgelegd door Azure Resource Manager, niet door Azure Data Factory. |
| API-aanroepen lezen | 12.500/u | 12.500/u<br/><br/> Deze limiet wordt opgelegd door Azure Resource Manager, niet door Azure Data Factory. |
| Bewakingsquery's per minuut | 1000 | 1000 |
| Maximale tijd voor foutopsporingssessie van gegevensstroom | 8 uur | 8 uur |
| Gelijktijdig aantal gegevensstromen per integratieruntime | 50 | [Neem contact op met ondersteuning](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Gelijktijdig aantal foutopsporingssessies van gegevensstroom per gebruiker per factory | 3 | 3 |
| Azure IR TTL-limiet voor gegevensstroom | 4 uur |  4 uur |

<sup>1</sup> De gegevensintegratie-eenheid (DIU) wordt gebruikt in een kopieerbewerking van de cloud naar de cloud. Zie [Gegevensintegratie-eenheden (versie 2)](../articles/data-factory/copy-activity-performance.md#data-integration-units) voor meer informatie. Zie [Prijzen voor Azure Data Factory](https://azure.microsoft.com/pricing/details/data-factory/)voor meer informatie over facturering.

<sup>2</sup> [Azure Integration Runtime](../articles/data-factory/concepts-integration-runtime.md#azure-integration-runtime) is [algemeen beschikbaar](https://azure.microsoft.com/global-infrastructure/services/) om de gegevensnaleving, efficiëntie en verminderde kosten voor uitgaand netwerkverkeer te garanderen. 

| Regiogroep | Regio's |
| -------- | ------ |
| Regiogroep 1 | Verenigde Staten, VS-Oost, VS-Oost 2, Europa-noord, Europa-west, VS-West, VS-West 2 |
| Regiogroep 2 | Australië-oost, Australië-zuidoost, Brazilië-zuid, Centraal-India, Japan-Oost, Noord-Centraal VS, Zuid-Centraal VS, Zuidoost-Azië, VS-West-Centraal |
| Regiogroep 3 | Andere regio's |

<sup>3</sup> Pijplijn-, gegevensset- en gekoppelde serviceobjecten vertegenwoordigen een logische groepering van uw werkbelasting. De limieten voor deze objecten hebben geen betrekking op de hoeveelheid gegevens die u kunt verplaatsen en verwerken met Azure Data Factory. Data Factory is ontworpen om petabytes aan gegevens te kunnen verwerken.

<sup>4</sup> De payload voor elke uitvoering van activiteit omvat de activiteitsconfiguratie, de bijbehorende gegevensset(s) en configuratie van gekoppelde service(s) en een klein deel systeemeigenschappen die per activiteitstype zijn gegenereerd. De limieten voor de grootte van deze payload hebben geen betrekking op de hoeveelheid gegevens die u kunt verplaatsen en verwerken met Azure Data Factory. Meer informatie over de [symptomen en aanbevelingen](../articles/data-factory/data-factory-troubleshoot-guide.md#payload-is-too-large) als u dit limiet bereikt.

### <a name="version-1"></a>Versie 1

| **Resource** | **Standaardlimiet** | **Maximumlimiet** |
| --- | --- | --- |
| Pijplijnen in een data factory |2500 |[Neem contact op met ondersteuning](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Gegevenssets binnen een data factory |5\.000 |[Neem contact op met ondersteuning](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Gelijktijdige segmenten per gegevensset |10 |10 |
| Bytes per object voor pijplijnobjecten<sup>1</sup> |200 kB |200 kB |
| Bytes per object voor gegevensset en gekoppelde serviceobjecten<sup>1</sup> |100 kB |2000 kB |
| Azure HDInsight-clusterkernen op aanvraag in een abonnement<sup>2</sup> |60 |[Neem contact op met ondersteuning](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Eenheden voor cloudgegevensverplaatsing per uitvoering van een kopieeractiviteit<sup>3</sup> |32 |32 |
| Aantal pogingen voor uitvoeringen van pijplijnactiviteit |1000 |MaxInt (32 bits) |

<sup>1</sup> Pijplijn-, gegevensset- en gekoppelde serviceobjecten vertegenwoordigen een logische groepering van uw werkbelasting. De limieten voor deze objecten hebben geen betrekking op de hoeveelheid gegevens die u kunt verplaatsen en verwerken met Azure Data Factory. Data Factory is ontworpen om petabytes aan gegevens te kunnen verwerken.

<sup>2</sup> HDInsight-kernen op aanvraag worden toegewezen uit het abonnement dat de data factory bevat. Als gevolg hiervan is de vorige limiet de door Data Factory afgedwongen kernlimiet voor HDInsight-kernen op aanvraag. Deze verschilt van de kernlimiet die is gekoppeld aan uw Azure-abonnement.

<sup>3</sup> De cloudgegevensverplaatsingseenheid (DMU) voor versie 1 wordt gebruikt in een kopieerbewerking van de cloud naar de cloud. Meer informatie vindt u in [Cloudgegevensverplaatsingseenheden (versie 1)](../articles/data-factory/v1/data-factory-copy-activity-performance.md#cloud-data-movement-units). Zie [Prijzen voor Azure Data Factory](https://azure.microsoft.com/pricing/details/data-factory/) voor meer informatie over facturering.

| **Resource** | **Standaardondergrens** | **Minimumlimiet** |
| --- | --- | --- |
| Planningsinterval |15 minuten |15 minuten |
| Interval tussen nieuwe pogingen |1 seconde |1 seconde |
| Time-outwaarde voor opnieuw proberen |1 seconde |1 seconde |

#### <a name="web-service-call-limits"></a>Aanroeplimieten voor webservices
Azure Resource Manager heeft limieten voor API-aanroepen. U kunt API-aanroepen maken met een snelheid binnen de [Azure Resource Manager API-limieten](../articles/azure-resource-manager/management/azure-subscription-service-limits.md#resource-group-limits).
