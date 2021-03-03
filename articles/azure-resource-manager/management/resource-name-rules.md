---
title: Naamgevings beperkingen voor resources
description: Hier worden de regels en beperkingen voor het benoemen van Azure-resources weer gegeven.
ms.topic: conceptual
ms.date: 01/27/2021
ms.openlocfilehash: 40e17e518baec40d31918d24ab5b61da741f540a
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101700470"
---
# <a name="naming-rules-and-restrictions-for-azure-resources"></a>Naamgevingsregels en -beperkingen voor Azure-resources

Dit artikel bevat een overzicht van de naamgevings regels en beperkingen voor Azure-resources. Zie [Aanbevolen naamgevings-en label conventies](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging)voor aanbevelingen over het benoemen van resources.

In dit artikel vindt u bronnen op de naam ruimte van de resource provider. Zie [resource providers voor Azure-Services](azure-services-resource-providers.md)voor een lijst met resource providers die overeenkomen met Azure-Services.

Resource namen zijn hoofdletter gevoelig, tenzij specifiek vermeld in de kolom geldige tekens.

In de volgende tabellen verwijst de term alfanumeriek naar:

* **a** t/m **z** (kleine letters)
* **A** t/m **Z** (hoofd letters)
* **0** t/m **9** (cijfers)

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tableFixed"]
> | Entiteit | Bereik | Lengte | Geldige tekens |
> | --- | --- | --- | --- |
> | Server | resourcegroep | 3-63 | Kleine letters en cijfers.<br><br>Beginnen met kleine letters. |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tableFixed"]
> | Entiteit | Bereik | Lengte | Geldige tekens |
> | --- | --- | --- | --- |
> | service | internationaal | 1-50 | Alfanumerieke tekens en afbreek streepjes.<br><br>Begin met letter en sluit af met een alfanumerieke teken. |
> | Service/api's | service | 1-256 | Kan niet gebruiken:<br> `*#&+:<>?` |
> | Service/api's/problemen | api | 1-256 | Kan niet gebruiken:<br> `*#&+:<>?` |
> | Service/api's/problemen/bijlagen | issue | 1-256 | Kan niet gebruiken:<br> `*#&+:<>?` |
> | Service/api's/problemen/opmerkingen | issue | 1-256 | Kan niet gebruiken:<br> `*#&+:<>?` |
> | Service/api's/bewerkingen | api | 1-256 | Kan niet gebruiken:<br> `*#&+:<>?` |
> | Service/api's/bewerkingen/Tags | bewerking | 1-256 | Kan niet gebruiken:<br> `*#&+:<>?` |
> | Service/api's/releases | api | 1-80 | Alfanumeriek, onderstrepings tekens en afbreek streepjes.<br><br>Beginnen en eindigen met een alfanumeriek of onderstrepings teken. |
> | Service/api's/schema's | api | 1-256 | Kan niet gebruiken:<br> `*#&+:<>?` |
> | Service/api's/tagDescriptions | api | 1-256 | Kan niet gebruiken:<br> `*#&+:<>?` |
> | Service/api's/Tags | api | 1-256 | Kan niet gebruiken:<br> `*#&+:<>?` |
> | Service/API-versie-sets | service | 1-256 | Kan niet gebruiken:<br> `*#&+:<>?` |
> | Service-authorizationServers | service | 1-256 | Kan niet gebruiken:<br> `*#&+:<>?` |
> | Service/back-end | service | 1-256 | Kan niet gebruiken:<br> `*#&+:<>?` |
> | Service/certificaten | service | 1-256 | Kan niet gebruiken:<br> `*#&+:<>?` |
> | Service/diagnostische gegevens | service | 1-256 | Kan niet gebruiken:<br> `*#&+:<>?` |
> | service/groepen | service | 1-256 | Kan niet gebruiken:<br> `*#&+:<>?` |
> | service/groepen/gebruikers | group | 1-256 | Kan niet gebruiken:<br> `*#&+:<>?` |
> | Service-identityProviders | service | 1-256 | Kan niet gebruiken:<br> `*#&+:<>?` |
> | Service/logger | service | 1-256 | Kan niet gebruiken:<br> `*#&+:<>?` |
> | Service/meldingen | service | 1-256 | Kan niet gebruiken:<br> `*#&+:<>?` |
> | Service/meldingen/recipientEmails | melding | 1-256 | Kan niet gebruiken:<br> `*#&+:<>?` |
> | Service-openidConnectProviders | service | 1-256 | Kan niet gebruiken:<br> `*#&+:<>?` |
> | Service/beleid | service | 1-256 | Kan niet gebruiken:<br> `*#&+:<>?` |
> | Service/producten | service | 1-256 | Kan niet gebruiken:<br> `*#&+:<>?` |
> | Service/producten/api's | product | 1-256 | Kan niet gebruiken:<br> `*#&+:<>?` |
> | Service/producten/groepen | product | 1-256 | Kan niet gebruiken:<br> `*#&+:<>?` |
> | Service/Producten/Tags | product | 1-256 | Kan niet gebruiken:<br> `*#&+:<>?` |
> | service/eigenschappen | service | 1-256 | Kan niet gebruiken:<br> `*#&+:<>?` |
> | Service/abonnementen | service | 1-256 | Kan niet gebruiken:<br> `*#&+:<>?` |
> | Service/Tags | service | 1-256 | Kan niet gebruiken:<br> `*#&+:<>?` |
> | service/Sjablonen | service | 1-256 | Kan niet gebruiken:<br> `*#&+:<>?` |
> | Service/gebruikers | service | 1-256 | Kan niet gebruiken:<br> `*#&+:<>?` |

## <a name="microsoftappconfiguration"></a>Micro soft. AppConfiguration

> [!div class="mx-tableFixed"]
> | Entiteit | Bereik | Lengte | Geldige tekens |
> | --- | --- | --- | --- |
> | configurationStores | resourcegroep | 5-50 | Alfanumeriek, onderstrepings tekens en afbreek streepjes. |

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | Entiteit | Bereik | Lengte | Geldige tekens |
> | --- | --- | --- | --- |
> | vergren delingen | toewijzings bereik | 1-90 | Alfanumerieken, punten, onderstrepings tekens, afbreek streepjes en haakjes.<br><br>Kan niet eindigen met een periode. |
> | policyAssignments | toewijzings bereik | weergave naam 1-128<br><br>resource naam voor 1-64<br><br>1-24-resource naam in het beheer groeps bereik | De weergave naam mag tekens bevatten.<br><br>De resource naam mag niet bevatten `%` en mag niet eindigen met een punt of spatie. |
> | policyDefinitions | bereik van definitie | weergave naam 1-128<br><br>resource naam voor 1-64 | De weergave naam mag tekens bevatten.<br><br>De resource naam mag niet bevatten `%` en mag niet eindigen met een punt of spatie. |
> | policySetDefinitions | bereik van definitie | weergave naam 1-128<br><br>resource naam voor 1-64<br><br>1-24-resource naam in het beheer groeps bereik | De weergave naam mag tekens bevatten.<br><br>De resource naam mag niet bevatten `%` en mag niet eindigen met een punt of spatie.  |

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!div class="mx-tableFixed"]
> | Entiteit | Bereik | Lengte | Geldige tekens |
> | --- | --- | --- | --- |
> | automationAccounts | & regio van resource groep <br>(Zie opmerking hieronder) | 6-50 | Alfanumerieke tekens en afbreek streepjes.<br><br>Begin met letter en sluit af met een alfanumerieke teken. |
> | automationAccounts/certificaten | Automation-account | 1-128 | Kan niet gebruiken:<br> `<>*%&:\?.+/` <br><br>Kan niet eindigen met een spatie.  |
> | automationAccounts/verbindingen | Automation-account | 1-128 | Kan niet gebruiken:<br> `<>*%&:\?.+/` <br><br>Kan niet eindigen met een spatie. |
> | automationAccounts/referenties | Automation-account | 1-128 | Kan niet gebruiken:<br> `<>*%&:\?.+/` <br><br>Kan niet eindigen met een spatie. |
> | automationAccounts/runbooks | Automation-account | 1-63 | Alfanumeriek, onderstrepings tekens en afbreek streepjes.<br><br>Begin met letter.  |
> | automationAccounts/schema's | Automation-account | 1-128 | Kan niet gebruiken:<br> `<>*%&:\?.+/` <br><br>Kan niet eindigen met een spatie. |
> | automationAccounts/variabelen | Automation-account | 1-128 | Kan niet gebruiken:<br> `<>*%&:\?.+/` <br><br>Kan niet eindigen met een spatie. |
> | automationAccounts/volgers | Automation-account | 1-63 |  Alfanumeriek, onderstrepings tekens en afbreek streepjes.<br><br>Begin met letter. |
> | automationAccounts/webhooks | Automation-account | 1-128 | Kan niet gebruiken:<br> `<>*%&:\?.+/` <br><br>Kan niet eindigen met een spatie. |

> [!NOTE]
> Automation-accountnamen zijn uniek per regio en resourcegroep. Namen voor verwijderde Automation-accounts zijn mogelijk niet onmiddellijk beschikbaar.

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | Entiteit | Bereik | Lengte | Geldige tekens |
> | --- | --- | --- | --- |
> | batchAccounts | Region | 3-24 | Kleine letters en cijfers. |
> | batchAccounts/toepassingen | batch-account | 1-64 | Alfanumeriek, onderstrepings tekens en afbreek streepjes. |
> | batchAccounts/certificaten | batch-account | 5-45 | Alfanumeriek, onderstrepings tekens en afbreek streepjes. |
> | batchAccounts/Pools | batch-account | 1-64 | Alfanumeriek, onderstrepings tekens en afbreek streepjes. |

## <a name="microsoftblockchain"></a>Micro soft. Block Chain

> [!div class="mx-tableFixed"]
> | Entiteit | Bereik | Lengte | Geldige tekens |
> | --- | --- | --- | --- |
> | blockchainMembers | internationaal | 2-20 | Kleine letters en cijfers.<br><br>Beginnen met kleine letters. |

## <a name="microsoftbotservice"></a>Micro soft. BotService

> [!div class="mx-tableFixed"]
> | Entiteit | Bereik | Lengte | Geldige tekens |
> | --- | --- | --- | --- |
> | botServices | internationaal | 2-64 |  Alfanumeriek, onderstrepings tekens, punten en afbreek streepjes.<br><br>Begin met alfanumeriek. |
> | botServices/kanalen | bot-service | 2-64 | Alfanumeriek, onderstrepings tekens, punten en afbreek streepjes.<br><br>Begin met alfanumeriek. |
> | botServices/verbindingen | bot-service | 2-64 | Alfanumeriek, onderstrepings tekens, punten en afbreek streepjes.<br><br>Begin met alfanumeriek. |
> | enterpriseChannels | resourcegroep | 2-64 | Alfanumeriek, onderstrepings tekens, punten en afbreek streepjes.<br><br>Begin met alfanumeriek. |

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tableFixed"]
> | Entiteit | Bereik | Lengte | Geldige tekens |
> | --- | --- | --- | --- |
> | Redis | internationaal | 1-63 | Alfanumerieke tekens en afbreek streepjes.<br><br>Beginnen en eindigen met een alfanumerieke teken. Opeenvolgende afbreek streepjes zijn niet toegestaan. |
> | Redis/firewallRules | Redis | 1-256 | Alfanumerieke tekens |

## <a name="microsoftcdn"></a>Micro soft. CDN

> [!div class="mx-tableFixed"]
> | Entiteit | Bereik | Lengte | Geldige tekens |
> | --- | --- | --- | --- |
> | profielen | resourcegroep | 1-260 | Alfanumerieke tekens en afbreek streepjes.<br><br>Beginnen en eindigen met een alfanumerieke teken. |
> | profielen/eind punten | internationaal | 1-50 | Alfanumerieke tekens en afbreek streepjes.<br><br>Beginnen en eindigen met een alfanumerieke teken. |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tableFixed"]
> | Entiteit | Bereik | Lengte | Geldige tekens |
> | --- | --- | --- | --- |
> | certificateOrders | resourcegroep | 3-30 | Alfanumerieke tekens. |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tableFixed"]
> | Entiteit | Bereik | Lengte | Geldige tekens |
> | --- | --- | --- | --- |
> | accounts | resourcegroep | 2-64 | Alfanumerieke tekens en afbreek streepjes.<br><br>Beginnen en eindigen met een alfanumerieke teken. |

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tableFixed"]
> | Entiteit | Bereik | Lengte | Geldige tekens |
> | --- | --- | --- | --- |
> | Availability sets | resourcegroep | 1-80 | Alfanumeriek, onderstrepings tekens, punten en afbreek streepjes.<br><br>Begin met alfanumeriek. Eindigen met een alfanumeriek of onderstrepings teken. |
> | diskEncryptionSets | resourcegroep | 1-80 | Alfanumerieke tekens en onderstrepingen. |
> | cd's | resourcegroep | 1-80 | Alfanumerieke tekens en onderstrepingen. |
> | Galerij | resourcegroep | 1-80 | Alfanumerieke tekens en punten.<br><br>Beginnen en eindigen met een alfanumerieke teken. |
> | galerieën/toepassingen | galerie | 1-80 | Alfanumerieke tekens, afbreek streepjes en punten.<br><br>Beginnen en eindigen met een alfanumerieke teken. |
> | galerieën/toepassingen/versies | toepassing | 32-bits geheel getal | Cijfers en peri Oden. |
> | galerieën/afbeeldingen | galerie | 1-80 | Alfanumeriek, onderstrepings tekens, afbreek streepjes en punten.<br><br>Beginnen en eindigen met een alfanumerieke teken. |
> | galerieën/afbeeldingen/versies | image | 32-bits geheel getal | Cijfers en peri Oden. |
> | images | resourcegroep | 1-80 | Alfanumeriek, onderstrepings tekens, punten en afbreek streepjes.<br><br>Begin met alfanumeriek. Eindigen met een alfanumeriek of onderstrepings teken. |
> | momentopnamen | resourcegroep | 1-80 | Alfanumeriek, onderstrepings tekens, punten en afbreek streepjes.<br><br>Begin met alfanumeriek. Eindigen met een alfanumeriek of onderstrepings teken. |
> | Informatie | resourcegroep | 1-15 (Windows)<br>1-64 (Linux)<br><br>Zie de opmerking hieronder. | Kan geen spatie of deze tekens gebruiken:<br> `\/"'[]:|<>+=;,?*@&_`<br><br>Windows-Vm's kunnen geen punt bevatten of eindigen met een afbreek streepje.<br><br>Linux-Vm's mogen niet eindigen op een punt of afbreek streepje. |
> | virtualMachineScaleSets | resourcegroep | 1-15 (Windows)<br>1-64 (Linux)<br><br>Zie de opmerking hieronder. | Kan geen spatie of deze tekens gebruiken:<br> `\/"'[]:|<>+=;,?*@&`<br><br>Kan niet beginnen met een onderstrepings teken. Kan niet eindigen met een punt of afbreek streepje. |

> [!NOTE]
> Virtuele Azure-machines hebben twee verschillende namen: resource naam en hostnaam. Wanneer u een virtuele machine in de portal maakt, wordt dezelfde waarde gebruikt voor beide namen. De beperkingen in de voor gaande tabel zijn voor de hostnaam. De werkelijke resourcenaam mag niet langer zijn dan 64 tekens.

## <a name="microsoftcontainerinstance"></a>Micro soft. ContainerInstance

> [!div class="mx-tableFixed"]
> | Entiteit | Bereik | Lengte | Geldige tekens |
> | --- | --- | --- | --- |
> | containerGroups | resourcegroep | 1-63 | Kleine letters, cijfers en afbreek streepjes.<br><br>Kan niet beginnen of eindigen met een afbreek streepje. Opeenvolgende afbreekstreepjes zijn niet toegestaan. |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tableFixed"]
> | Entiteit | Bereik | Lengte | Geldige tekens |
> | --- | --- | --- | --- |
> | registers | internationaal | 5-50 | Alfanumerieke tekens. |
> | registers/buildTasks | registry | 5-50 | Alfanumerieke tekens. |
> | registers/buildTasks/stappen | taak bouwen | 5-50 | Alfanumerieke tekens. |
> | registers/replicaties | registry | 5-50 | Alfanumerieke tekens. |
> | registers/scopeMaps | registry | 5-50 | Alfanumerieke tekens, afbreek streepjes en onderstrepings tekens. |
> | registers/taken | registry | 5-50 | Alfanumerieke tekens, afbreek streepjes en onderstrepings tekens. |
> | registers/tokens | registry | 5-50 | Alfanumerieke tekens, afbreek streepjes en onderstrepings tekens. |
> | registers/webhooks | registry | 5-50 | Alfanumerieke tekens. |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tableFixed"]
> | Entiteit | Bereik | Lengte | Geldige tekens |
> | --- | --- | --- | --- |
> | managedClusters | resourcegroep | 1-63 | Alfanumeriek, onderstrepings tekens en afbreek streepjes.<br><br>Beginnen en eindigen met een alfanumerieke teken. |
> | openShiftManagedClusters | resourcegroep | 1-30 | Alfanumerieke tekens. |

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights

> [!div class="mx-tableFixed"]
> | Entiteit | Bereik | Lengte | Geldige tekens |
> | --- | --- | --- | --- |
> | hubs | resourcegroep | 1-64 | Alfanumerieke tekens.<br><br>Begin met letter.  |
> | hubs/authorizationPolicies | hub | 1-50 | Alfanumeriek, onderstrepings tekens en punten.<br><br>Beginnen en eindigen met een alfanumerieke teken. |
> | hubs/connectors | hub | 1-128 | Alfanumerieke tekens en onderstrepingen.<br><br>Begin met letter. |
> | hubs/connectors/toewijzingen | connector | 1-128 | Alfanumerieke tekens en onderstrepingen.<br><br>Begin met letter. |
> | hubs/interacties | hub | 1-128 | Alfanumerieke tekens en onderstrepingen.<br><br>Begin met letter. |
> | hubs/kpi's | hub | 1-512 | Alfanumerieke tekens en onderstrepingen.<br><br>Begin met letter. |
> | hubs/koppelingen | hub | 1-512 | Alfanumerieke tekens en onderstrepingen.<br><br>Begin met letter. |
> | hubs/voor spellingen | hub | 1-512 | Alfanumerieke tekens en onderstrepingen.<br><br>Begin met letter. |
> | hubs/profielen | hub | 1-128 | Alfanumerieke tekens en onderstrepingen.<br><br>Begin met letter. |
> | hubs/relationshipLinks | hub | 1-512 | Alfanumerieke tekens en onderstrepingen.<br><br>Begin met letter. |
> | hubs/relaties | hub | 1-512 | Alfanumerieke tekens en onderstrepingen.<br><br>Begin met letter. |
> | hubs/roleAssignments | hub | 1-128 | Alfanumerieke tekens en onderstrepingen.<br><br>Begin met letter. |
> | hubs/weer gaven | hub | 1-512 | Alfanumerieke tekens en onderstrepingen.<br><br>Begin met letter. |

## <a name="microsoftcustomproviders"></a>Micro soft. CustomProviders

> [!div class="mx-tableFixed"]
> | Entiteit | Bereik | Lengte | Geldige tekens |
> | --- | --- | --- | --- |
> | lidkoppelingen | resourcegroep | 1-180 | Kan niet gebruiken:<br>`%&\\?/`<br><br>Kan niet eindigen met een punt of spatie. |
> | resourceProviders | resourcegroep | 3-64 | Kan niet gebruiken:<br>`%&\\?/`<br><br>Kan niet eindigen met een punt of spatie. |

## <a name="microsoftdatabox"></a>Micro soft. DataBox

> [!div class="mx-tableFixed"]
> | Entiteit | Bereik | Lengte | Geldige tekens |
> | --- | --- | --- | --- |
> | functies | resourcegroep | 3-24 | Alfanumerieke tekens, afbreek streepjes, onderstrepings tekens en punten. |

## <a name="microsoftdatabricks"></a>Micro soft. Databricks

> [!div class="mx-tableFixed"]
> | Entiteit | Bereik | Lengte | Geldige tekens |
> | --- | --- | --- | --- |
> | workspaces | resourcegroep | 3-30 | Alfanumerieken, onderstrepings tekens en afbreek streepjes |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tableFixed"]
> | Entiteit | Bereik | Lengte | Geldige tekens |
> | --- | --- | --- | --- |
> | factory's | internationaal | 3-63 | Alfanumerieke tekens en afbreek streepjes.<br><br>Beginnen en eindigen met een alfanumerieke teken. |
> | fabrieken/gegevens stromen | standaard | 1-260 | Kan niet gebruiken:<br>`<>*#.%&:\\+?/`<br><br>Begin met alfanumeriek. |
> | fabrieken/gegevens sets | standaard | 1-260 | Kan niet gebruiken:<br>`<>*#.%&:\\+?/`<br><br>Begin met alfanumeriek. |
> | fabrieken/integrationRuntimes | standaard | 3-63 | Alfanumerieke tekens en afbreek streepjes.<br><br>Beginnen en eindigen met een alfanumerieke teken. |
> | fabrieken/linkedservices | standaard | 1-260 | Kan niet gebruiken:<br>`<>*#.%&:\\+?/`<br><br>Begin met alfanumeriek. |
> | fabrieken/pijp lijnen | standaard | 1-260 | Kan niet gebruiken:<br>`<>*#.%&:\\+?/`<br><br>Begin met alfanumeriek. |
> | fabrieken/triggers | standaard | 1-260 | Kan niet gebruiken:<br>`<>*#.%&:\\+?/`<br><br>Begin met alfanumeriek. |
> | fabrieken/triggers/rerunTriggers | activeren | 1-260 | Kan niet gebruiken:<br>`<>*#.%&:\\+?/`<br><br>Begin met alfanumeriek. |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | Entiteit | Bereik | Lengte | Geldige tekens |
> | --- | --- | --- | --- |
> | accounts | internationaal | 3-24 | Kleine letters en cijfers. |
> | accounts/computePolicies | account | 3-60 | Alfanumerieke tekens, afbreek streepjes en onderstrepings tekens. |
> | accounts/dataLakeStoreAccounts | account | 3-24 | Kleine letters en cijfers. |
> | accounts/firewallRules | account | 3-50 | Alfanumerieke tekens, afbreek streepjes en onderstrepings tekens. |
> | accounts/Storage accounts | account | 3-60 | Alfanumerieke tekens, afbreek streepjes en onderstrepings tekens. |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tableFixed"]
> | Entiteit | Bereik | Lengte | Geldige tekens |
> | --- | --- | --- | --- |
> | accounts | internationaal | 3-24 | Kleine letters en cijfers. |
> | accounts/firewallRules | account | 3-50 | Alfanumerieke tekens, afbreek streepjes en onderstrepings tekens. |
> | accounts/virtualNetworkRules | account | 3-50 | Alfanumerieke tekens, afbreek streepjes en onderstrepings tekens. |

## <a name="microsoftdatamigration"></a>Micro soft. DataMigration

> [!div class="mx-tableFixed"]
> | Entiteit | Bereik | Lengte | Geldige tekens |
> | --- | --- | --- | --- |
> | services | resourcegroep | 2-62 | Alfanumerieke tekens, afbreek streepjes, punten en onderstrepings tekens.<br><br>Begin met alfanumeriek. |
> | Services/projecten | service | 2-57 | Alfanumerieke tekens, afbreek streepjes, punten en onderstrepings tekens.<br><br>Begin met alfanumeriek. |

## <a name="microsoftdbformariadb"></a>Micro soft. DBforMariaDB

> [!div class="mx-tableFixed"]
> | Entiteit | Bereik | Lengte | Geldige tekens |
> | --- | --- | --- | --- |
> | Server | internationaal | 3-63 | Kleine letters, afbreek streepjes en cijfers.<br><br>Kan niet beginnen of eindigen met een afbreek streepje. |
> | servers/data bases | Server | 1-63 | Alfanumerieke tekens en afbreek streepjes. |
> | servers/firewallRules | Server | 1-128 | Alfanumerieke tekens, afbreek streepjes en onderstrepings tekens. |
> | servers/virtualNetworkRules | Server | 1-128 | Alfanumerieke tekens en afbreek streepjes. |

## <a name="microsoftdbformysql"></a>Micro soft. DBforMySQL

> [!div class="mx-tableFixed"]
> | Entiteit | Bereik | Lengte | Geldige tekens |
> | --- | --- | --- | --- |
> | Server | internationaal | 3-63 | Kleine letters, afbreek streepjes en cijfers.<br><br>Kan niet beginnen of eindigen met een afbreek streepje. |
> | servers/data bases | Server | 1-63 | Alfanumerieke tekens en afbreek streepjes. |
> | servers/firewallRules | Server | 1-128 | Alfanumerieke tekens, afbreek streepjes en onderstrepings tekens. |
> | servers/virtualNetworkRules | Server | 1-128 | Alfanumerieke tekens en afbreek streepjes. |

## <a name="microsoftdbforpostgresql"></a>Micro soft. DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | Entiteit | Bereik | Lengte | Geldige tekens |
> | --- | --- | --- | --- |
> | Server | internationaal | 3-63 | Kleine letters, afbreek streepjes en cijfers.<br><br>Kan niet beginnen of eindigen met een afbreek streepje. |
> | servers/data bases | Server | 1-63 | Alfanumerieke tekens en afbreek streepjes. |
> | servers/firewallRules | Server | 1-128 | Alfanumerieke tekens, afbreek streepjes en onderstrepings tekens. |
> | servers/virtualNetworkRules | Server | 1-128 | Alfanumerieke tekens en afbreek streepjes. |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | Entiteit | Bereik | Lengte | Geldige tekens |
> | --- | --- | --- | --- |
> | IotHubs | internationaal | 3-50 | Alfanumerieke tekens en afbreek streepjes.<br><br>Kan niet eindigen met een afbreek streepje. |
> | IotHubs/certificaten | IoT-hub | 1-64 | Alfanumerieke tekens, afbreek streepjes, punten en onderstrepings tekens. |
> | IotHubs/eventHubEndpoints/ConsumerGroups | eventHubEndpoints | 1-50 | Alfanumerieke tekens, afbreek streepjes, punten en onderstrepings tekens. |
> | provisioningServices | resourcegroep | 3-64 | Alfanumerieke tekens en afbreek streepjes.<br><br>Eindigen met een alfanumerieke teken. |
> | provisioningServices/certificaten | provisioningServices | 1-64 | Alfanumerieke tekens, afbreek streepjes, punten en onderstrepings tekens. |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tableFixed"]
> | Entiteit | Bereik | Lengte | Geldige tekens |
> | --- | --- | --- | --- |
> | Labs | resourcegroep | 1-50 | Alfanumeriek, onderstrepings tekens en afbreek streepjes. |
> | Labs-customimages | Slab | 1-80 | Alfanumeriek, onderstrepings tekens, afbreek streepjes en haakjes. |
> | Labs/formules | Slab | 1-80 | Alfanumeriek, onderstrepings tekens, afbreek streepjes en haakjes. |
> | Labs-informatie | Slab | 1-15 (Windows)<br>1-64 (Linux) | Alfanumerieke tekens en afbreek streepjes.<br><br>Beginnen en eindigen met een alfanumerieke teken. Kan niet alle cijfers zijn. |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | Entiteit | Bereik | Lengte | Geldige tekens |
> | --- | --- | --- | --- |
> | databaseAccounts | internationaal | 3-44 | Kleine letters, cijfers en afbreek streepjes.<br><br>Begin met kleine letter of cijfer. |

## <a name="microsofteventgrid"></a>Micro soft. EventGrid

> [!div class="mx-tableFixed"]
> | Entiteit | Bereik | Lengte | Geldige tekens |
> | --- | --- | --- | --- |
> | domeinen | resourcegroep | 3-50 | Alfanumerieke tekens en afbreek streepjes. |
> | domeinen/onderwerpen | domein | 3-50 | Alfanumerieke tekens en afbreek streepjes. |
> | eventSubscriptions | resourcegroep | 3-64 | Alfanumerieke tekens en afbreek streepjes. |
> | onderwerp | resourcegroep | 3-50 | Alfanumerieke tekens en afbreek streepjes. |

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | Entiteit | Bereik | Lengte | Geldige tekens |
> | --- | --- | --- | --- |
> | clusters | resourcegroep | 6-50 | Alfanumerieke tekens en afbreek streepjes.<br><br>Begin met letter. Eindigen met een letter of cijfer. |
> | naam ruimten | internationaal | 6-50 | Alfanumerieke tekens en afbreek streepjes.<br><br>Begin met letter. Eindigen met een letter of cijfer. |
> | naam ruimten/AuthorizationRules | naamruimte | 1-50 | Alfanumerieke tekens, punten, afbreek streepjes en onderstrepings tekens.<br><br>Beginnen en eindigen met een letter of cijfer. |
> | naam ruimten/disasterRecoveryConfigs | naamruimte | 1-50 | Alfanumerieke tekens, punten, afbreek streepjes en onderstrepings tekens.<br><br>Beginnen en eindigen met een letter of cijfer. |
> | naam ruimten/Event hubs | naamruimte | 1-50 | Alfanumerieke tekens, punten, afbreek streepjes en onderstrepings tekens.<br><br>Beginnen en eindigen met een letter of cijfer. |
> | naam ruimten/Event hubs/authorizationRules | Event Hub | 1-50 | Alfanumerieke tekens, punten, afbreek streepjes en onderstrepings tekens.<br><br>Beginnen en eindigen met een letter of cijfer. |
> | naam ruimten/Event hubs/consumergroups | Event Hub | 1-50 | Alfanumerieke tekens, punten, afbreek streepjes en onderstrepings tekens.<br><br>Beginnen en eindigen met een letter of cijfer. |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tableFixed"]
> | Entiteit | Bereik | Lengte | Geldige tekens |
> | --- | --- | --- | --- |
> | clusters | internationaal | 3-59 | Alfanumerieke tekens en afbreek streepjes<br><br>Beginnen en eindigen met een letter of cijfer. |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tableFixed"]
> | Entiteit | Bereik | Lengte | Geldige tekens |
> | --- | --- | --- | --- |
> | functies | resourcegroep | 2-64 | Alfanumerieke tekens en afbreek streepjes.<br><br>Begin met letter. |

## <a name="microsoftinsights"></a>Microsoft.Insights

> [!div class="mx-tableFixed"]
> | Entiteit | Bereik | Lengte | Geldige tekens |
> | --- | --- | --- | --- |
> | actionGroups | resourcegroep | 1-260 | Kan niet gebruiken:<br>`/&%\?` <br><br>Kan niet eindigen met een spatie of een punt.  |
> | materialen | resourcegroep | 1-260 | Kan niet gebruiken:<br>`%&\?/` <br><br>Kan niet eindigen met een spatie of een punt.  |
> | scheduledQueryRules | resourcegroep | 1-260 | Kan niet gebruiken:<br>`*<>%{}&:\\?/#` <br><br>Kan niet eindigen met een spatie of een punt.  |
> | metricAlerts | resourcegroep | 1-260 | Kan niet gebruiken:<br>`*#&+:<>?@%{}\/` <br><br>Kan niet eindigen met een spatie of een punt.  |
> | activityLogAlerts | resourcegroep | 1-260 | Kan niet gebruiken:<br>`<>*%{}&:\\?+/#` <br><br>Kan niet eindigen met een spatie of een punt.  |

## <a name="microsoftiotcentral"></a>Micro soft. IoTCentral

> [!div class="mx-tableFixed"]
> | Entiteit | Bereik | Lengte | Geldige tekens |
> | --- | --- | --- | --- |
> | IoTApps | internationaal | 2-63 | Kleine letters, cijfers en afbreek streepjes.<br><br>Begin met kleine letter of cijfer. |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tableFixed"]
> | Entiteit | Bereik | Lengte | Geldige tekens |
> | --- | --- | --- | --- |
> | kluizen | internationaal | 3-24 | Alfanumerieke tekens en afbreek streepjes.<br><br>Begin met letter. Eindigen met een letter of cijfer. Kan geen opeenvolgende afbreek streepjes bevatten. |
> | kluizen/geheimen | Kluis | 1-127 | Alfanumerieke tekens en afbreek streepjes. |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | Entiteit | Bereik | Lengte | Geldige tekens |
> | --- | --- | --- | --- |
> | clusters | internationaal | 4-22 | Kleine letters en cijfers.<br><br>Begin met letter. |
> | /clusters/data bases | cluster | 1-260 | Alfanumerieke tekens, afbreek streepjes, spaties en punten. |
> | /clusters/data bases/dataConnections | database | 1-40 | Alfanumerieke tekens, afbreek streepjes, spaties en punten. |
> | /clusters/data bases/eventhubconnections | database | 1-40 | Alfanumerieke tekens, afbreek streepjes, spaties en punten. |

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tableFixed"]
> | Entiteit | Bereik | Lengte | Geldige tekens |
> | --- | --- | --- | --- |
> | integrationAccounts | resourcegroep | 1-80 | Alfanumerieke tekens, afbreek streepjes, onderstrepings tekens, punten en haakjes. |
> | integrationAccounts/assembly's | integratieaccount | 1-80 | Alfanumerieke tekens, afbreek streepjes, onderstrepings tekens, punten en haakjes. |
> | integrationAccounts / batchConfigurations | integratieaccount | 1-20 | Alfanumerieke tekens. |
> | integrationAccounts/certificaten | integratieaccount | 1-80 | Alfanumerieke tekens, afbreek streepjes, onderstrepings tekens, punten en haakjes. |
> | integrationAccounts/Maps | integratieaccount | 1-80 | Alfanumerieke tekens, afbreek streepjes, onderstrepings tekens, punten en haakjes. |
> | integrationAccounts/partners | integratieaccount | 1-80 | Alfanumerieke tekens, afbreek streepjes, onderstrepings tekens, punten en haakjes. |
> | integrationAccounts / rosettanetprocessconfigurations | integratieaccount | 1-80 | Alfanumerieke tekens, afbreek streepjes, onderstrepings tekens, punten en haakjes. |
> | integrationAccounts/schema's | integratieaccount | 1-80 | Alfanumerieke tekens, afbreek streepjes, onderstrepings tekens, punten en haakjes. |
> | integrationAccounts/sessies | integratieaccount | 1-80 | Alfanumerieke tekens, afbreek streepjes, onderstrepings tekens, punten en haakjes. |
> | integrationServiceEnvironments | resourcegroep | 1-80 | Alfanumerieke tekens, afbreek streepjes, punten en onderstrepings tekens. |
> | integrationServiceEnvironments/Beheerdeapi's | integratie service omgeving | 1-80 | Alfanumerieke tekens, afbreek streepjes, punten en onderstrepings tekens. |
> | stroom | resourcegroep | 1-80 | Alfanumerieke tekens, afbreek streepjes, onderstrepings tekens, punten en haakjes. |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tableFixed"]
> | Entiteit | Bereik | Lengte | Geldige tekens |
> | --- | --- | --- | --- |
> | commitmentPlans | resourcegroep | 1-260 | Kan niet gebruiken:<br>`<>*%&:?+/\\`<br><br>Kan niet eindigen met een spatie. |
> | webservices | resourcegroep | 1-260 | Kan niet gebruiken:<br>`<>*%&:?+/\\`<br><br>Kan niet eindigen met een spatie. |
> | workspaces | resourcegroep | 1-260 | Kan niet gebruiken:<br>`<>*%&:?+/\\`<br><br>Kan niet eindigen met een spatie. |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | Entiteit | Bereik | Lengte | Geldige tekens |
> | --- | --- | --- | --- |
> | workspaces | resourcegroep | 3-33 | Alfanumerieke tekens en afbreek streepjes. |
> | werk ruimten/reken bewerkingen | werkruimte | 2-16 | Alfanumerieke tekens en afbreek streepjes. |

## <a name="microsoftmanagedidentity"></a>Micro soft. ManagedIdentity

> [!div class="mx-tableFixed"]
> | Entiteit | Bereik | Lengte | Geldige tekens |
> | --- | --- | --- | --- |
> | userAssignedIdentities | resourcegroep | 3-128 | Alfanumerieke tekens, afbreek streepjes en onderstrepings tekens<br><br>Begin met letter of cijfer. |

## <a name="microsoftmaps"></a>Micro soft. Maps

> [!div class="mx-tableFixed"]
> | Entiteit | Bereik | Lengte | Geldige tekens |
> | --- | --- | --- | --- |
> | accounts | resourcegroep | 1-98 (voor de naam van de resource groep en de account naam) | Alfanumeriek, onderstrepings tekens, punten en afbreek streepjes.<br><br>Begin met alfanumeriek. |

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tableFixed"]
> | Entiteit | Bereik | Lengte | Geldige tekens |
> | --- | --- | --- | --- |
> | Media Services | resourcegroep | 3-24 | Kleine letters en cijfers. |
> | Media Services/liveEvents | Media service | 1-32 | Alfanumerieke tekens en afbreek streepjes.<br><br>Begin met alfanumeriek. |
> | Media Services/liveEvents/liveOutputs | Live-gebeurtenis | 1-256 | Alfanumerieke tekens en afbreek streepjes.<br><br>Begin met alfanumeriek. |
> | Media Services/streamingEndpoints | Media service | 1-24 | Alfanumerieke tekens en afbreek streepjes.<br><br>Begin met alfanumeriek. |

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tableFixed"]
> | Entiteit | Bereik | Lengte | Geldige tekens |
> | --- | --- | --- | --- |
> | applicationGateways | resourcegroep | 1-80 | Alfanumeriek, onderstrepings tekens, punten en afbreek streepjes.<br><br>Begin met alfanumeriek. Einde alfanumeriek of onderstrepings teken. |
> | applicationSecurityGroups | resourcegroep | 1-80 | Alfanumeriek, onderstrepings tekens, punten en afbreek streepjes.<br><br>Begin met alfanumeriek. Einde alfanumeriek of onderstrepings teken. |
> | azureFirewalls | resourcegroep | 1-80 | Alfanumeriek, onderstrepings tekens, punten en afbreek streepjes.<br><br>Begin met alfanumeriek. Eindigen met een alfanumeriek of onderstrepings teken. |
> | bastionHosts | resourcegroep | 1-80 | Alfanumeriek, onderstrepings tekens, punten en afbreek streepjes.<br><br>Begin met alfanumeriek. Einde alfanumeriek of onderstrepings teken. |
> | inbel | resourcegroep | 1-80 | Alfanumeriek, onderstrepings tekens, punten en afbreek streepjes.<br><br>Begin met alfanumeriek. Einde alfanumeriek of onderstrepings teken. |
> | dnsZones | resourcegroep | 1-63 tekens<br><br>2 tot 34 labels<br><br>Elk label bestaat uit een reeks tekens gescheiden door een punt. Zo heeft **contoso.com** twee labels. | Elk label mag alfanumerieken, onderstrepings tekens en afbreek streepjes bevatten.<br><br>Elk label wordt gescheiden door een punt. |
> | expressRouteCircuits | resourcegroep | 1-80 | Alfanumeriek, onderstrepings tekens, punten en afbreek streepjes.<br><br>Begin met alfanumeriek. Einde alfanumeriek of onderstrepings teken. |
> | firewallPolicies | resourcegroep | 1-80 | Alfanumeriek, onderstrepings tekens, punten en afbreek streepjes.<br><br>Begin met alfanumeriek. Einde alfanumeriek of onderstrepings teken. |
> | firewallPolicies / ruleGroups | Firewall beleid | 1-80 | Alfanumeriek, onderstrepings tekens, punten en afbreek streepjes.<br><br>Begin met alfanumeriek. Einde alfanumeriek of onderstrepings teken. |
> | frontDoors | internationaal | 5-64 | Alfanumerieke tekens en afbreek streepjes.<br><br>Beginnen en eindigen met een alfanumerieke teken. |
> | frontdoorWebApplicationFirewallPolicies | resourcegroep | 1-128 | Alfanumerieke tekens.<br><br>Begin met letter. |
> | loadBalancers | resourcegroep | 1-80 | Alfanumeriek, onderstrepings tekens, punten en afbreek streepjes.<br><br>Begin met alfanumeriek. Einde alfanumeriek of onderstrepings teken. |
> | loadBalancers/inboundNatRules | load balancer | 1-80 | Alfanumeriek, onderstrepings tekens, punten en afbreek streepjes.<br><br>Begin met alfanumeriek. Einde alfanumeriek of onderstrepings teken. |
> | localNetworkGateways | resourcegroep | 1-80 | Alfanumeriek, onderstrepings tekens, punten en afbreek streepjes.<br><br>Begin met alfanumeriek. Einde alfanumeriek of onderstrepings teken. |
> | networkInterfaces | resourcegroep | 1-80 | Alfanumeriek, onderstrepings tekens, punten en afbreek streepjes.<br><br>Begin met alfanumeriek. Einde alfanumeriek of onderstrepings teken. |
> | networkSecurityGroups | resourcegroep | 1-80 | Alfanumeriek, onderstrepings tekens, punten en afbreek streepjes.<br><br>Begin met alfanumeriek. Einde alfanumeriek of onderstrepings teken. |
> | networkSecurityGroups/securityRules | netwerk beveiligings groep | 1-80 |  Alfanumeriek, onderstrepings tekens, punten en afbreek streepjes.<br><br>Begin met alfanumeriek. Einde alfanumeriek of onderstrepings teken. |
> | networkWatchers | resourcegroep | 1-80 | Alfanumeriek, onderstrepings tekens, punten en afbreek streepjes.<br><br>Begin met alfanumeriek. Einde alfanumeriek of onderstrepings teken. |
> | privateDnsZones | resourcegroep | 1-63 tekens<br><br>2 tot 34 labels<br><br>Elk label bestaat uit een reeks tekens gescheiden door een punt. Zo heeft **contoso.com** twee labels. | Elk label mag alfanumerieken, onderstrepings tekens en afbreek streepjes bevatten.<br><br>Elk label wordt gescheiden door een punt. |
> | privateDnsZones / virtualNetworkLinks | privé-DNS-zone | 1-80 | Alfanumeriek, onderstrepings tekens, punten en afbreek streepjes.<br><br>Begin met alfanumeriek. Einde alfanumeriek of onderstrepings teken. |
> | publicIPAddresses | resourcegroep | 1-80 | Alfanumeriek, onderstrepings tekens, punten en afbreek streepjes.<br><br>Begin met alfanumeriek. Einde alfanumeriek of onderstrepings teken. |
> | publicIPPrefixes | resourcegroep | 1-80 | Alfanumeriek, onderstrepings tekens, punten en afbreek streepjes.<br><br>Begin met alfanumeriek. Einde alfanumeriek of onderstrepings teken. |
> | routeFilters | resourcegroep | 1-80 | Alfanumeriek, onderstrepings tekens, punten en afbreek streepjes.<br><br>Begin met alfanumeriek. Einde alfanumeriek of onderstrepings teken. |
> | routeFilters / routeFilterRules | route filter | 1-80 | Alfanumeriek, onderstrepings tekens, punten en afbreek streepjes.<br><br>Begin met alfanumeriek. Einde alfanumeriek of onderstrepings teken. |
> | routeTables | resourcegroep | 1-80 | Alfanumeriek, onderstrepings tekens, punten en afbreek streepjes.<br><br>Begin met alfanumeriek. Einde alfanumeriek of onderstrepings teken. |
> | routeTables/routes | routetabel | 1-80 | Alfanumeriek, onderstrepings tekens, punten en afbreek streepjes.<br><br>Begin met alfanumeriek. Einde alfanumeriek of onderstrepings teken. |
> | serviceEndpointPolicies | resourcegroep | 1-80 | Alfanumeriek, onderstrepings tekens, punten en afbreek streepjes.<br><br>Begin met alfanumeriek. Einde alfanumeriek of onderstrepings teken. |
> | trafficmanagerprofiles | internationaal | 1-63 | Alfanumerieke tekens, afbreek streepjes en punten.<br><br>Beginnen en eindigen met een alfanumerieke teken. |
> | virtualNetworkGateways | resourcegroep | 1-80 | Alfanumeriek, onderstrepings tekens, punten en afbreek streepjes.<br><br>Begin met alfanumeriek. Einde alfanumeriek of onderstrepings teken. |
> | virtualNetworks | resourcegroep | 2-64 | Alfanumeriek, onderstrepings tekens, punten en afbreek streepjes.<br><br>Begin met alfanumeriek. Einde alfanumeriek of onderstrepings teken. |
> | virtualnetworks/subnetten | virtueel netwerk | 1-80 | Alfanumeriek, onderstrepings tekens, punten en afbreek streepjes.<br><br>Begin met alfanumeriek. Einde alfanumeriek of onderstrepings teken. |
> | virtualNetworks/virtualNetworkPeerings | virtueel netwerk | 1-80 | Alfanumeriek, onderstrepings tekens, punten en afbreek streepjes.<br><br>Begin met alfanumeriek. Einde alfanumeriek of onderstrepings teken. |
> | virtualWans | resourcegroep | 1-80 | Alfanumeriek, onderstrepings tekens, punten en afbreek streepjes.<br><br>Begin met alfanumeriek. Einde alfanumeriek of onderstrepings teken. |
> | vpnGateways | resourcegroep | 1-80 | Alfanumeriek, onderstrepings tekens, punten en afbreek streepjes.<br><br>Begin met alfanumeriek. Einde alfanumeriek of onderstrepings teken. |
> | vpnGateways / vpnConnections | VPN-gateway | 1-80 | Alfanumeriek, onderstrepings tekens, punten en afbreek streepjes.<br><br>Begin met alfanumeriek. Einde alfanumeriek of onderstrepings teken. |
> | vpnSites | resourcegroep | 1-80 | Alfanumeriek, onderstrepings tekens, punten en afbreek streepjes.<br><br>Begin met alfanumeriek. Einde alfanumeriek of onderstrepings teken. |

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | Entiteit | Bereik | Lengte | Geldige tekens |
> | --- | --- | --- | --- |
> | naam ruimten | internationaal | 6-50 | Alfanumerieke tekens en afbreek streepjes<br><br>Begin met letter. Eindigen met een alfanumerieke teken. |
> | naam ruimten/AuthorizationRules | naamruimte | 1-256 | Alfanumerieken, punten, afbreek streepjes en onderstrepings tekens.<br><br>Begin alfanumerieke tekens. |
> | naam ruimten/notification hubs | naamruimte | 1-260 | Alfanumerieken, punten, afbreek streepjes en onderstrepings tekens.<br><br>Begin alfanumerieke tekens. |
> | naam ruimten/notification hubs/AuthorizationRules | Notification hub | 1-256 | Alfanumerieken, punten, afbreek streepjes en onderstrepings tekens.<br><br>Begin alfanumerieke tekens. |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tableFixed"]
> | Entiteit | Bereik | Lengte | Geldige tekens |
> | --- | --- | --- | --- |
> | clusters | resourcegroep | 4-63 | Alfanumerieke tekens en afbreek streepjes.<br><br>Beginnen en eindigen met een alfanumerieke teken. |
> | workspaces | internationaal | 4-63 | Alfanumerieke tekens en afbreek streepjes.<br><br>Beginnen en eindigen met een alfanumerieke teken. |

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tableFixed"]
> | Entiteit | Bereik | Lengte | Geldige tekens |
> | --- | --- | --- | --- |
> | oplossingen | werkruimte | N.v.t. | Voor oplossingen die door micro soft zijn gemaakt, moet de naam in het patroon zijn:<br>`SolutionType(WorkspaceName)`<br><br>Voor oplossingen die door derden zijn ontworpen, moet de naam in het patroon zijn:<br>`SolutionType[WorkspaceName]`<br><br>Een geldige naam is bijvoorbeeld:<br>`AntiMalware(contoso-IT)`<br><br>Het oplossings type is hoofdletter gevoelig. |

## <a name="microsoftportal"></a>Micro soft. Portal

> [!div class="mx-tableFixed"]
> | Entiteit | Bereik | Lengte | Geldige tekens |
> | --- | --- | --- | --- |
> | dashboards | resourcegroep | 3-160 | Alfanumerieke tekens en afbreek streepjes.<br><br>Als u beperkte tekens wilt gebruiken, voegt u een label toe met de naam **verborgen-titel** en de naam van het dash board dat u wilt gebruiken. In de portal wordt die naam weer gegeven wanneer het dash board wordt weer gegeven. |

## <a name="microsoftpowerbi"></a>Micro soft. PowerBI

> [!div class="mx-tableFixed"]
> | Entiteit | Bereik | Lengte | Geldige tekens |
> | --- | --- | --- | --- |
> | workspaceCollections | regio | 3-63 | Alfanumerieke tekens en afbreek streepjes.<br><br>Kan niet beginnen met een koppel teken. Kan geen opeenvolgende afbreek streepjes gebruiken. |

## <a name="microsoftpowerbidedicated"></a>Micro soft. PowerBIDedicated

> [!div class="mx-tableFixed"]
> | Entiteit | Bereik | Lengte | Geldige tekens |
> | --- | --- | --- | --- |
> | beschikt | regio | 3-63 | Kleine letters of cijfers<br><br>Beginnen met kleine letters. |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tableFixed"]
> | Entiteit | Bereik | Lengte | Geldige tekens |
> | --- | --- | --- | --- |
> | kluizen | resourcegroep | 2-50 | Alfanumerieke tekens en afbreek streepjes.<br><br>Begin met letter. |
> | kluizen/backupPolicies | kluis | 3-150 | Alfanumerieke tekens en afbreek streepjes.<br><br>Begin met letter. Kan niet eindigen met een afbreek streepje. |

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tableFixed"]
> | Entiteit | Bereik | Lengte | Geldige tekens |
> | --- | --- | --- | --- |
> | naam ruimten | internationaal | 6-50 | Alfanumerieke tekens en afbreek streepjes.<br><br>Begin met een letter. Eindigen met een letter of cijfer. |
> | naam ruimten/AuthorizationRules | naamruimte | 1-50 |  Alfanumerieke tekens, punten, afbreek streepjes en onderstrepings tekens.<br><br>Beginnen en eindigen met een alfanumerieke teken. |
> | naam ruimten/HybridConnections | naamruimte | 1-260 | Alfanumerieke tekens, punten, afbreek streepjes, onderstrepings tekens en slashes.<br><br>Beginnen en eindigen met een alfanumerieke teken. |
> | naam ruimten/HybridConnections/authorizationRules | hybride verbinding | 1-50 | Alfanumerieke tekens, punten, afbreek streepjes en onderstrepings tekens.<br><br>Beginnen en eindigen met een alfanumerieke teken. |
> | naam ruimten/WcfRelays | naamruimte | 1-260 | Alfanumerieke tekens, punten, afbreek streepjes, onderstrepings tekens en slashes.<br><br>Beginnen en eindigen met een alfanumerieke teken. |
> | naam ruimten/WcfRelays/authorizationRules | WCF-relay | 1-50 | Alfanumerieke tekens, punten, afbreek streepjes en onderstrepings tekens.<br><br>Beginnen en eindigen met een alfanumerieke teken. |

## <a name="microsoftresources"></a>Microsoft.Resources

> [!div class="mx-tableFixed"]
> | Entiteit | Bereik | Lengte | Geldige tekens |
> | --- | --- | --- | --- |
> | implementaties | resourcegroep | 1-64 | Alfanumeriek, onderstrepings tekens, haakjes, afbreek streepjes en punten. |
> | ResourceGroups | abonnement | 1-90 | Alfanumeriek, onderstreping, haakjes, afbreek streepjes, punten en Unicode-tekens die overeenkomen met de [regex-documentatie](/rest/api/resources/resourcegroups/createorupdate).<br><br>Kan niet eindigen met een punt. |
> | tagName | resource | 1-512 | Kan niet gebruiken:<br>`<>%&\?/` |
> | tagName-tagValues | Label naam | 1-256 | Alle tekens. |
> | templateSpecs | resourcegroep | 1-90 | Alfanumeriek, onderstrepings tekens, haakjes, afbreek streepjes en punten. |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | Entiteit | Bereik | Lengte | Geldige tekens |
> | --- | --- | --- | --- |
> | naam ruimten | internationaal | 6-50 | Alfanumerieke tekens en afbreek streepjes.<br><br>Begin met een letter. Eindigen met een letter of cijfer.<br><br>Zie [naam ruimte maken](/rest/api/servicebus/create-namespace)voor meer informatie. |
> | naam ruimten/AuthorizationRules | naamruimte | 1-50 | Alfanumerieken, punten, afbreek streepjes en onderstrepings tekens.<br><br>Start en eindigt met alphnumeric. |
> | naam ruimten/disasterRecoveryConfigs | internationaal | 6-50 | Alfanumerieke tekens en afbreek streepjes.<br><br>Begin met letter. Eindigen met een alfanumerieke teken. |
> | naam ruimten/migrationConfigurations | naamruimte |  | Moet altijd worden **$default**. |
> | naam ruimten/wacht rijen | naamruimte | 1-260 | Alfanumerieke tekens, punten, afbreek streepjes, onderstrepings tekens en slashes.<br><br>Beginnen en eindigen met een alfanumerieke teken. |
> | naam ruimten/wacht rijen/authorizationRules | wachtrij | 1-50 | Alfanumerieken, punten, afbreek streepjes en onderstrepings tekens.<br><br>Start en eindigt met alphnumeric. |
> | naam ruimten/onderwerpen | naamruimte | 1-260 | Alfanumerieke tekens, punten, afbreek streepjes, onderstrepings tekens en slashes.<br><br>Beginnen en eindigen met een alfanumerieke teken. |
> | naam ruimten/onderwerpen/authorizationRules | onderwerp | 1-50 | Alfanumerieken, punten, afbreek streepjes en onderstrepings tekens.<br><br>Start en eindigt met alphnumeric. |
> | naam ruimten/onderwerpen/abonnementen | onderwerp | 1-50 | Alfanumerieken, punten, afbreek streepjes en onderstrepings tekens.<br><br>Start en eindigt met alphnumeric. |
> | naam ruimten/onderwerpen/abonnementen/regels | abonnement | 1-50 | Alfanumerieken, punten, afbreek streepjes en onderstrepings tekens.<br><br>Start en eindigt met alphnumeric. |

## <a name="microsoftservicefabric"></a>Micro soft. ServiceFabric

> [!div class="mx-tableFixed"]
> | Entiteit | Bereik | Lengte | Geldige tekens |
> | --- | --- | --- | --- |
> | clusters | regio | 4-23 | Kleine letters, cijfers en afbreek streepjes.<br><br>Beginnen met kleine letters. Eindigen met kleine letter of cijfer. |

## <a name="microsoftsignalrservice"></a>Micro soft. SignalRService

> [!div class="mx-tableFixed"]
> | Entiteit | Bereik | Lengte | Geldige tekens |
> | --- | --- | --- | --- |
> | signalR | internationaal | 3-63 | Alfanumerieke tekens en afbreek streepjes.<br><br>Begin met letter. Eindigen met een letter of cijfer.  |

## <a name="microsoftsql"></a>Microsoft.Sql

> [!div class="mx-tableFixed"]
> | Entiteit | Bereik | Lengte | Geldige tekens |
> | --- | --- | --- | --- |
> | managedInstances | internationaal | 1-63 | Kleine letters, cijfers en afbreek streepjes.<br><br>Kan niet beginnen of eindigen met een afbreek streepje. <br><br> Geen speciale tekens, zoals `@` . |
> | Server | internationaal | 1-63 | Kleine letters, cijfers en afbreek streepjes.<br><br>Kan niet beginnen of eindigen met een afbreek streepje. |
> | servers/beheerders | server |  | Moet zijn `ActiveDirectory` . |
> | servers/data bases | server | 1-128 | Kan niet gebruiken:<br>`<>*%&:\/?`<br><br>Kan niet eindigen met een punt of spatie. |
> | servers/data bases/syncGroups | database | 1-150 | Alfanumerieke tekens, afbreek streepjes en onderstrepings tekens. |
> | servers/elasticPools | server | 1-128 | Kan niet gebruiken:<br>`<>*%&:\/?`<br><br>Kan niet eindigen met een punt of spatie. |
> | servers/failoverGroups | internationaal | 1-63 | Kleine letters, cijfers en afbreek streepjes.<br><br>Kan niet beginnen of eindigen met een afbreek streepje. |
> | servers/firewallRules | server | 1-128 | Kan niet gebruiken:<br>`<>*%&:;\/?`<br><br>Kan niet eindigen met een punt. |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | Entiteit | Bereik | Lengte | Geldige tekens |
> | --- | --- | --- | --- |
> | Storage accounts | internationaal | 3-24 | Kleine letters en cijfers. |
> | Storage accounts/blobServices | opslagaccount |  | Moet zijn `default` . |
> | Storage accounts/blobServices/containers | opslagaccount | 3-63 | Kleine letters, cijfers en afbreek streepjes.<br><br>Begin met kleine letter of cijfer. Kan geen opeenvolgende afbreek streepjes gebruiken. |
> | Storage accounts/fileServices | opslagaccount |  | Moet zijn `default` . |
> | Storage accounts/fileServices/shares | opslagaccount | 3-63 | Kleine letters, cijfers en afbreek streepjes.<br><br>Kan niet beginnen of eindigen met een afbreek streepje. Kan geen opeenvolgende afbreek streepjes gebruiken. |
> | Storage accounts/managementPolicies | opslagaccount |  | Moet zijn `default` . |
> | blob | container | 1-1024 | Wille keurige URL-tekens, hoofdletter gevoelig |
> | wachtrij | opslagaccount | 3-63 | Kleine letters, cijfers en afbreek streepjes.<br><br>Kan niet beginnen of eindigen met een afbreek streepje. Kan geen opeenvolgende afbreek streepjes gebruiken. |
> | tabel | opslagaccount | 3-63 | Alfanumerieke tekens.<br><br>Begin met letter. |

## <a name="microsoftstoragesync"></a>Micro soft. StorageSync

> [!div class="mx-tableFixed"]
> | Entiteit | Bereik | Lengte | Geldige tekens |
> | --- | --- | --- | --- |
> | storageSyncServices | resourcegroep | 1-260 | Alfanumerieke tekens, spaties, punten, afbreek streepjes en onderstrepingen.<br><br>Kan niet eindigen met een punt of spatie. |
> | storageSyncServices / syncGroups | opslag synchronisatie service | 1-260 | Alfanumerieke tekens, spaties, punten, afbreek streepjes en onderstrepingen.<br><br>Kan niet eindigen met een punt of spatie. |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tableFixed"]
> | Entiteit | Bereik | Lengte | Geldige tekens |
> | --- | --- | --- | --- |
> | leider | resourcegroep | 2-50 | Alfanumerieke tekens en afbreek streepjes.<br><br>Begin met letter. Eindigen met een alfanumerieke teken. |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!div class="mx-tableFixed"]
> | Entiteit | Bereik | Lengte | Geldige tekens |
> | --- | --- | --- | --- |
> | streamingjobs | resourcegroep | 3-63 | Alfanumerieke tekens, afbreek streepjes en onderstrepings tekens. |
> | streamingjobs/functions | streaming-taak | 3-63 | Alfanumerieke tekens, afbreek streepjes en onderstrepings tekens. |
> | streamingjobs/invoer | streaming-taak | 3-63 | Alfanumerieke tekens, afbreek streepjes en onderstrepings tekens. |
> | streamingjobs/uitvoer | streaming-taak | 3-63 | Alfanumerieke tekens, afbreek streepjes en onderstrepings tekens. |
> | streamingjobs/trans formaties | streaming-taak | 3-63 | Alfanumerieke tekens, afbreek streepjes en onderstrepings tekens. |

## <a name="microsofttimeseriesinsights"></a>Micro soft. TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | Entiteit | Bereik | Lengte | Geldige tekens |
> | --- | --- | --- | --- |
> | verschillend | resourcegroep | 1-90 | Kan niet gebruiken:<br>`'<>%&:\?/#` |
> | omgevingen/accessPolicies | omgeving | 1-90 | Kan niet gebruiken:<br> `'<>%&:\?/#` |
> | omgevingen/eventSources | omgeving | 1-90 | Kan niet gebruiken:<br>`'<>%&:\?/#` |
> | omgevingen/referenceDataSets | omgeving | 3-63 | Alfanumerieke tekens |

## <a name="microsoftweb"></a>Microsoft.Web

> [!div class="mx-tableFixed"]
> | Entiteit | Bereik | Lengte | Geldige tekens |
> | --- | --- | --- | --- |
> | certificaten | resourcegroep | 1-260 | Kan niet gebruiken:<br>`/` <br><br>Kan niet eindigen met een spatie of een punt.  | 
> | server farms | resourcegroep | 1-40 | Alfanumerieke tekens en afbreek streepjes. |
> | sites | internationaal | 2-60 | Bevat alfanumerieke tekens en afbreek streepjes.<br><br>Kan niet beginnen of eindigen met een afbreek streepje. |
> | sites/sleuven | site | 2-59 | Alfanumerieke tekens en afbreek streepjes. |

> [!NOTE]
> Azure Functions heeft dezelfde naamgevings regels en beperkingen als micro soft. web/sites.

## <a name="next-steps"></a>Volgende stappen

Zie [Ready: Recommended name and Tags Conventions](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging)(Engelstalig) voor aanbevelingen voor het benoemen van resources.
