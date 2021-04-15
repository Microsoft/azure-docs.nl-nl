---
title: Naamgevingsbeperkingen voor resources
description: Toont de regels en beperkingen voor de naamgeving van Azure-resources.
ms.topic: conceptual
ms.date: 04/08/2021
ms.openlocfilehash: e260c9055b26d82f2fd2f8458d287a35a838f40f
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107477788"
---
# <a name="naming-rules-and-restrictions-for-azure-resources"></a>Naamgevingsregels en -beperkingen voor Azure-resources

In dit artikel worden naamgevingsregels en -beperkingen voor Azure-resources samengevat. Zie Aanbevolen naamgevings- en tagconventen voor aanbevelingen voor het benoemen [van resources.](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging)

In dit artikel vindt u een lijst met resources op naamruimte van de resourceprovider. Zie Resourceproviders voor Azure-services voor een lijst met de manier waarop [resourceproviders overeenkomen met Azure-services.](azure-services-resource-providers.md)

Resourcenamen zijn niet hoofd- en hoofdtekens, tenzij deze worden vermeld in de kolom met geldige tekens.

In de volgende tabellen verwijst de term alfanumeriek naar:

* **a** tot **z** (kleine letters)
* **A** tot **en met Z** (hoofdletters)
* **0** tot **en met 9** (getallen)

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tableFixed"]
> | Entiteit | Bereik | Lengte | Geldige tekens |
> | --- | --- | --- | --- |
> | Servers | resourcegroep | 3-63 | Kleine letters en cijfers.<br><br>Begin met kleine letters. |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tableFixed"]
> | Entiteit | Bereik | Lengte | Geldige tekens |
> | --- | --- | --- | --- |
> | service | Globale | 1-50 | Alfanumerieke tekens en afbreekstreeken.<br><br>Begin met letter en eind met alfanumeriek. |
> | service / API's | service | 1-256 | Kan niet gebruiken:<br> `*#&+:<>?` |
> | service / API's / problemen | api | 1-256 | Kan niet gebruiken:<br> `*#&+:<>?` |
> | service / API's / problemen / bijlagen | issue | 1-256 | Kan niet gebruiken:<br> `*#&+:<>?` |
> | service / API's / problemen / opmerkingen | issue | 1-256 | Kan niet gebruiken:<br> `*#&+:<>?` |
> | service / API's / bewerkingen | api | 1-256 | Kan niet gebruiken:<br> `*#&+:<>?` |
> | service / API's / bewerkingen / tags | bewerking | 1-256 | Kan niet gebruiken:<br> `*#&+:<>?` |
> | service / API's / releases | api | 1-80 | Alfanumerieke tekens, onderstrepingstekens en afbreekstreepingen.<br><br>Begin en eindigt met alfanumeriek of onderstrepingsteken. |
> | service / API's / schema's | api | 1-256 | Kan niet gebruiken:<br> `*#&+:<>?` |
> | service / apis / tagDescriptions | api | 1-256 | Kan niet gebruiken:<br> `*#&+:<>?` |
> | service / API's / tags | api | 1-256 | Kan niet gebruiken:<br> `*#&+:<>?` |
> | service /api-version-sets | service | 1-256 | Kan niet gebruiken:<br> `*#&+:<>?` |
> | service /authorizationServers | service | 1-256 | Kan niet gebruiken:<br> `*#&+:<>?` |
> | service /back-enden | service | 1-256 | Kan niet gebruiken:<br> `*#&+:<>?` |
> | service /certificaten | service | 1-256 | Kan niet gebruiken:<br> `*#&+:<>?` |
> | service / diagnostische gegevens | service | 1-256 | Kan niet gebruiken:<br> `*#&+:<>?` |
> | service/groepen | service | 1-256 | Kan niet gebruiken:<br> `*#&+:<>?` |
> | service / groepen / gebruikers | group | 1-256 | Kan niet gebruiken:<br> `*#&+:<>?` |
> | service /identityProviders | service | 1-256 | Kan niet gebruiken:<br> `*#&+:<>?` |
> | service/loggers | service | 1-256 | Kan niet gebruiken:<br> `*#&+:<>?` |
> | service /meldingen | service | 1-256 | Kan niet gebruiken:<br> `*#&+:<>?` |
> | service / meldingen / ontvangerEmails | melding | 1-256 | Kan niet gebruiken:<br> `*#&+:<>?` |
> | service / openidConnectProviders | service | 1-256 | Kan niet gebruiken:<br> `*#&+:<>?` |
> | service /beleid | service | 1-256 | Kan niet gebruiken:<br> `*#&+:<>?` |
> | service/producten | service | 1-256 | Kan niet gebruiken:<br> `*#&+:<>?` |
> | service / producten / API's | product | 1-256 | Kan niet gebruiken:<br> `*#&+:<>?` |
> | service / producten / groepen | product | 1-256 | Kan niet gebruiken:<br> `*#&+:<>?` |
> | service / producten / tags | product | 1-256 | Kan niet gebruiken:<br> `*#&+:<>?` |
> | service/eigenschappen | service | 1-256 | Kan niet gebruiken:<br> `*#&+:<>?` |
> | service /abonnementen | service | 1-256 | Kan niet gebruiken:<br> `*#&+:<>?` |
> | service/tags | service | 1-256 | Kan niet gebruiken:<br> `*#&+:<>?` |
> | service/sjablonen | service | 1-256 | Kan niet gebruiken:<br> `*#&+:<>?` |
> | service /gebruikers | service | 1-256 | Kan niet gebruiken:<br> `*#&+:<>?` |

## <a name="microsoftappconfiguration"></a>Microsoft.AppConfiguration

> [!div class="mx-tableFixed"]
> | Entiteit | Bereik | Lengte | Geldige tekens |
> | --- | --- | --- | --- |
> | configurationStores | resourcegroep | 5-50 | Alfanumerieke tekens, onderstrepingstekens en afbreekstreepingen. |

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | Entiteit | Bereik | Lengte | Geldige tekens |
> | --- | --- | --- | --- |
> | Sloten | toewijzingsbereik | 1-90 | Alfanumeriek, punten, onderstrepingstekens, afbreekstreepingstekens en haakjes.<br><br>Kan niet eindigen op punt. |
> | policyAssignments | toewijzingsbereik | Weergavenaam 1-128<br><br>Resourcenaam 1-64<br><br>1-24 resourcenaam op beheergroepbereik | De weergavenaam kan alle tekens bevatten.<br><br>De resourcenaam kan niet worden `%` gebruikt en mag niet eindigen op punt of spatie. |
> | policyDefinitions | definitiebereik | Weergavenaam 1-128<br><br>Resourcenaam 1-64 | De weergavenaam kan alle tekens bevatten.<br><br>De resourcenaam kan niet worden `%` gebruikt en mag niet eindigen op punt of spatie. |
> | policySetDefinitions | definitiebereik | Weergavenaam 1-128<br><br>Resourcenaam 1-64<br><br>1-24 resourcenaam op beheergroepbereik | De weergavenaam kan alle tekens bevatten.<br><br>Resourcenaam kan niet bevatten `%` en kan niet eindigen op punt of spatie.  |

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!div class="mx-tableFixed"]
> | Entiteit | Bereik | Lengte | Geldige tekens |
> | --- | --- | --- | --- |
> | automationAccounts | resourcegroep & regio <br>(Zie de opmerking hieronder) | 6-50 | Alfanumerieke tekens en afbreekstreeken.<br><br>Begin met letter en eindigen met alfanumeriek. |
> | automationAccounts/certificaten | Automation-account | 1-128 | Kan niet gebruiken:<br> `<>*%&:\?.+/` <br><br>Kan niet eindigen met ruimte.  |
> | automationAccounts/verbindingen | Automation-account | 1-128 | Kan niet gebruiken:<br> `<>*%&:\?.+/` <br><br>Kan niet eindigen met ruimte. |
> | automationAccounts /credentials | Automation-account | 1-128 | Kan niet gebruiken:<br> `<>*%&:\?.+/` <br><br>Kan niet eindigen met ruimte. |
> | automationAccounts/runbooks | Automation-account | 1-63 | Alfanumerieke tekens, onderstrepingstekens en afbreekstreepingstekens.<br><br>Begin met letter.  |
> | automationAccounts /schedules | Automation-account | 1-128 | Kan niet gebruiken:<br> `<>*%&:\?.+/` <br><br>Kan niet eindigen met ruimte. |
> | automationAccounts /variables | Automation-account | 1-128 | Kan niet gebruiken:<br> `<>*%&:\?.+/` <br><br>Kan niet eindigen met ruimte. |
> | automationAccounts/watchers | Automation-account | 1-63 |  Alfanumerieke tekens, onderstrepingstekens en afbreekstreepingstekens.<br><br>Begin met letter. |
> | automationAccounts/webhooks | Automation-account | 1-128 | Kan niet gebruiken:<br> `<>*%&:\?.+/` <br><br>Kan niet eindigen met ruimte. |

> [!NOTE]
> Automation-accountnamen zijn uniek per regio en resourcegroep. Namen voor verwijderde Automation-accounts zijn mogelijk niet onmiddellijk beschikbaar.

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | Entiteit | Bereik | Lengte | Geldige tekens |
> | --- | --- | --- | --- |
> | batchAccounts | Region | 3-24 | Kleine letters en cijfers. |
> | batchAccounts/toepassingen | batch-account | 1-64 | Alfanumerieke tekens, onderstrepingstekens en afbreekstreepingen. |
> | batchAccounts/certificaten | batch-account | 5-45 | Alfanumerieke tekens, onderstrepingstekens en afbreekstreepingen. |
> | batchAccounts/pools | batch-account | 1-64 | Alfanumerieke tekens, onderstrepingstekens en afbreekstreepingstekens. |

## <a name="microsoftblockchain"></a>Microsoft.Blockchain

> [!div class="mx-tableFixed"]
> | Entiteit | Bereik | Lengte | Geldige tekens |
> | --- | --- | --- | --- |
> | blockchainMembers | Globale | 2-20 | Kleine letters en cijfers.<br><br>Begin met kleine letters. |

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tableFixed"]
> | Entiteit | Bereik | Lengte | Geldige tekens |
> | --- | --- | --- | --- |
> | botServices | Globale | 2-64 |  Alfanumerieke tekens, onderstrepingstekens, punten en afbreekstreepingstekens.<br><br>Begin met alfanumeriek. |
> | botServices/kanalen | botservice | 2-64 | Alfanumerieke tekens, onderstrepingstekens, punten en afbreekstreepingstekens.<br><br>Begin met alfanumeriek. |
> | botServices/verbindingen | botservice | 2-64 | Alfanumerieke tekens, onderstrepingstekens, punten en afbreekstreepingstekens.<br><br>Begin met alfanumeriek. |
> | enterpriseChannels | resourcegroep | 2-64 | Alfanumerieke tekens, onderstrepingstekens, punten en afbreekstreepingen.<br><br>Begin met alfanumeriek. |

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tableFixed"]
> | Entiteit | Bereik | Lengte | Geldige tekens |
> | --- | --- | --- | --- |
> | Redis | Globale | 1-63 | Alfanumerieke tekens en afbreekstreeken.<br><br>Begin en eindigt met alfanumeriek. Opeenvolgende afbreekstreeepten zijn niet toegestaan. |
> | Redis/firewallRules | Redis | 1-256 | Alfanumeriek |

## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tableFixed"]
> | Entiteit | Bereik | Lengte | Geldige tekens |
> | --- | --- | --- | --- |
> | Profielen | resourcegroep | 1-260 | Alfanumerieke tekens en afbreekstreeken.<br><br>Begin en eindigt met alfanumeriek. |
> | profielen/eindpunten | Globale | 1-50 | Alfanumerieke tekens en afbreekstreeken.<br><br>Begin en eindigt met alfanumeriek. |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tableFixed"]
> | Entiteit | Bereik | Lengte | Geldige tekens |
> | --- | --- | --- | --- |
> | certificateOrders | resourcegroep | 3-30 | Alfanumeriek. |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tableFixed"]
> | Entiteit | Bereik | Lengte | Geldige tekens |
> | --- | --- | --- | --- |
> | accounts | resourcegroep | 2-64 | Alfanumerieke tekens en afbreekstree zij.<br><br>Begin en eind met alfanumeriek. |

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tableFixed"]
> | Entiteit | Bereik | Lengte | Geldige tekens |
> | --- | --- | --- | --- |
> | availabilitySets | resourcegroep | 1-80 | Alfanumerieke tekens, onderstrepingstekens, punten en afbreekstreepingstekens.<br><br>Begin met alfanumeriek. Eindigen met alfanumeriek of onderstrepingsteken. |
> | diskEncryptionSets | resourcegroep | 1-80 | Alfanumerieke tekens en onderstrepingstekens. |
> | Schijven | resourcegroep | 1-80 | Alfanumerieke tekens, onderstrepingstekens en afbreekstreepingstekens. |
> | Galeries | resourcegroep | 1-80 | Alfanumerieke tekens en punten.<br><br>Begin en eind met alfanumeriek. |
> | galerieën/toepassingen | galerie | 1-80 | Alfanumerieke tekens, afbreekstreeken en punten.<br><br>Begin en eind met alfanumeriek. |
> | galerieën/toepassingen/versies | toepassing | 32-bits geheel getal | Getallen en punten. |
> | galerieën/afbeeldingen | galerie | 1-80 | Alfanumerieke tekens, onderstrepingstekens, afbreekstreepingen en punten.<br><br>Begin en eindigt met alfanumeriek. |
> | galerieën / afbeeldingen / versies | image | 32-bits geheel getal | Getallen en punten. |
> | images | resourcegroep | 1-80 | Alfanumerieke tekens, onderstrepingstekens, punten en afbreekstreepingen.<br><br>Begin met alfanumeriek. Eindigen met alfanumeriek of onderstrepingsteken. |
> | momentopnamen | resourcegroep | 1-80 | Alfanumerieke tekens, onderstrepingstekens, punten en afbreekstreepingen.<br><br>Begin met alfanumeriek. Eindigen met alfanumeriek of onderstrepingsteken. |
> | virtualMachines | resourcegroep | 1-15 (Windows)<br>1-64 (Linux)<br><br>Zie de opmerking hieronder. | Kan geen spatie of de volgende tekens gebruiken:<br> `~ ! @ # $ % ^ & * ( ) = + _ [ ] { } \ | ; : . ' " , < > / ?`<br><br>Windows-VM's kunnen geen punt bevatten of eindigen met afbreekstreepjes.<br><br>Linux-VM's kunnen niet eindigen met punt of afbreekstreester. |
> | virtualMachineScaleSets | resourcegroep | 1-15 (Windows)<br>1-64 (Linux)<br><br>Zie de opmerking hieronder. | Kan geen spatie of deze tekens gebruiken:<br> `~ ! @ # $ % ^ & * ( ) = + _ [ ] { } \ | ; : . ' " , < > / ?`<br><br>Kan niet beginnen met een onderstrepingsteken. Kan niet eindigen met punt of afbreekstreester. |

> [!NOTE]
> Virtuele Azure-machines hebben twee verschillende namen: resourcenaam en hostnaam. Wanneer u een virtuele machine in de portal maakt, wordt voor beide namen dezelfde waarde gebruikt. De beperkingen in de voorgaande tabel gelden voor de hostnaam. De werkelijke resourcenaam mag niet langer zijn dan 64 tekens.

## <a name="microsoftcommunication"></a>Microsoft.Communication

> [!div class="mx-tableFixed"]
> | Entiteit | Bereik | Lengte | Geldige tekens |
> | --- | --- | --- | --- |
> | communicationServices | Globale | 1-63 | Alfanumerieke tekens, afbreekstreepingstekens en onderstrepingstekens. |

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tableFixed"]
> | Entiteit | Bereik | Lengte | Geldige tekens |
> | --- | --- | --- | --- |
> | containerGroups | resourcegroep | 1-63 | Kleine letters, cijfers en afbreekstreelopen.<br><br>Kan niet beginnen of eindigen met een koppelteken. Opeenvolgende afbreekstreepjes zijn niet toegestaan. |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tableFixed"]
> | Entiteit | Bereik | Lengte | Geldige tekens |
> | --- | --- | --- | --- |
> | Registers | Globale | 5-50 | Alfanumeriek. |
> | registers /buildTasks | registry | 5-50 | Alfanumeriek. |
> | registers /buildTasks/steps | build-taak | 5-50 | Alfanumeriek. |
> | registers/replicaties | registry | 5-50 | Alfanumeriek. |
> | registers/scopeMaps | registry | 5-50 | Alfanumerieke tekens, afbreekstreepingen en onderstrepingstekens. |
> | registers/taken | registry | 5-50 | Alfanumerieke tekens, afbreekstreepingen en onderstrepingstekens. |
> | registers/tokens | registry | 5-50 | Alfanumerieke tekens, afbreekstreepingstekens en onderstrepingstekens. |
> | registers/webhooks | registry | 5-50 | Alfanumeriek. |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tableFixed"]
> | Entiteit | Bereik | Lengte | Geldige tekens |
> | --- | --- | --- | --- |
> | managedClusters | resourcegroep | 1-63 | Alfanumerieke tekens, onderstrepingstekens en afbreekstreepingstekens.<br><br>Begin en eind met alfanumeriek. |
> | openShiftManagedClusters | resourcegroep | 1-30 | Alfanumeriek. |

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights

> [!div class="mx-tableFixed"]
> | Entiteit | Bereik | Lengte | Geldige tekens |
> | --- | --- | --- | --- |
> | hubs | resourcegroep | 1-64 | Alfanumeriek.<br><br>Begin met letter.  |
> | hubs/authorizationPolicies | hub | 1-50 | Alfanumerieke tekens, onderstrepingstekens en punten.<br><br>Begin en eind met alfanumeriek. |
> | hubs/connectors | hub | 1-128 | Alfanumerieke tekens en onderstrepingstekens.<br><br>Begin met letter. |
> | hubs/connectors/toewijzingen | connector | 1-128 | Alfanumerieke tekens en onderstrepingstekens.<br><br>Begin met letter. |
> | hubs/interacties | hub | 1-128 | Alfanumerieke tekens en onderstrepingstekens.<br><br>Begin met letter. |
> | hubs /KPI | hub | 1-512 | Alfanumerieke tekens en onderstrepingstekens.<br><br>Begin met letter. |
> | hubs/koppelingen | hub | 1-512 | Alfanumerieke tekens en onderstrepingstekens.<br><br>Begin met letter. |
> | hubs/voorspellingen | hub | 1-512 | Alfanumerieke tekens en onderstrepingstekens.<br><br>Begin met letter. |
> | hubs/profielen | hub | 1-128 | Alfanumerieke tekens en onderstrepingstekens.<br><br>Begin met letter. |
> | hubs/relationshipLinks | hub | 1-512 | Alfanumerieke tekens en onderstrepingstekens.<br><br>Begin met letter. |
> | hubs/relaties | hub | 1-512 | Alfanumerieke tekens en onderstrepingstekens.<br><br>Begin met letter. |
> | hubs/roleAssignments | hub | 1-128 | Alfanumerieke tekens en onderstrepingstekens.<br><br>Begin met letter. |
> | hubs/weergaven | hub | 1-512 | Alfanumerieke tekens en onderstrepingstekens.<br><br>Begin met letter. |

## <a name="microsoftcustomproviders"></a>Microsoft.CustomProviders

> [!div class="mx-tableFixed"]
> | Entiteit | Bereik | Lengte | Geldige tekens |
> | --- | --- | --- | --- |
> | Verenigingen | resourcegroep | 1-180 | Kan niet gebruiken:<br>`%&\\?/`<br><br>Kan niet eindigen op punt of spatie. |
> | resourceProviders | resourcegroep | 3-64 | Kan niet gebruiken:<br>`%&\\?/`<br><br>Kan niet eindigen op punt of spatie. |

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tableFixed"]
> | Entiteit | Bereik | Lengte | Geldige tekens |
> | --- | --- | --- | --- |
> | Banen | resourcegroep | 3-24 | Alfanumerieke tekens, afbreekstreepingen, onderstrepingstekens en punten. |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tableFixed"]
> | Entiteit | Bereik | Lengte | Geldige tekens |
> | --- | --- | --- | --- |
> | workspaces | resourcegroep | 3-30 | Alfanumerieke tekens, onderstrepingstekens en afbreekstreepingen |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tableFixed"]
> | Entiteit | Bereik | Lengte | Geldige tekens |
> | --- | --- | --- | --- |
> | Fabrieken | Globale | 3-63 | Alfanumerieke tekens en afbreekstreeken.<br><br>Begin en eind met alfanumeriek. |
> | factories/gegevensstromen | Fabriek | 1-260 | Kan niet gebruiken:<br>`<>*#.%&:\\+?/`<br><br>Begin met alfanumeriek. |
> | factories/gegevenssets | Fabriek | 1-260 | Kan niet gebruiken:<br>`<>*#.%&:\\+?/`<br><br>Begin met alfanumeriek. |
> | factories/integrationRuntimes | Fabriek | 3-63 | Alfanumerieke tekens en afbreekstree zij.<br><br>Begin en eind met alfanumeriek. |
> | factories/linkedservices | Fabriek | 1-260 | Kan niet gebruiken:<br>`<>*#.%&:\\+?/`<br><br>Begin met alfanumeriek. |
> | factories/pipelines | Fabriek | 1-260 | Kan niet gebruiken:<br>`<>*#.%&:\\+?/`<br><br>Begin met alfanumeriek. |
> | factories/triggers | Fabriek | 1-260 | Kan niet gebruiken:<br>`<>*#.%&:\\+?/`<br><br>Begin met alfanumeriek. |
> | factories/triggers /rerunTriggers | activeren | 1-260 | Kan niet gebruiken:<br>`<>*#.%&:\\+?/`<br><br>Begin met alfanumeriek. |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | Entiteit | Bereik | Lengte | Geldige tekens |
> | --- | --- | --- | --- |
> | accounts | Globale | 3-24 | Kleine letters en cijfers. |
> | accounts/computePolicies | account | 3-60 | Alfanumerieke tekens, afbreekstreepingen en onderstrepingstekens. |
> | accounts /dataLakeStoreAccounts | account | 3-24 | Kleine letters en cijfers. |
> | accounts /firewallRules | account | 3-50 | Alfanumerieke tekens, afbreekstreepingstekens en onderstrepingstekens. |
> | accounts/storageAccounts | account | 3-60 | Alfanumerieke tekens, afbreekstreepingstekens en onderstrepingstekens. |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tableFixed"]
> | Entiteit | Bereik | Lengte | Geldige tekens |
> | --- | --- | --- | --- |
> | accounts | Globale | 3-24 | Kleine letters en cijfers. |
> | accounts /firewallRules | account | 3-50 | Alfanumerieke tekens, afbreekstreepingstekens en onderstrepingstekens. |
> | accounts / virtualNetworkRules | account | 3-50 | Alfanumerieke tekens, afbreekstreepingstekens en onderstrepingstekens. |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tableFixed"]
> | Entiteit | Bereik | Lengte | Geldige tekens |
> | --- | --- | --- | --- |
> | services | resourcegroep | 2-62 | Alfanumerieke tekens, afbreekstreepingstekens, punten en onderstrepingstekens.<br><br>Begin met alfanumeriek. |
> | services/projecten | service | 2-57 | Alfanumerieke tekens, afbreekstreepingen, punten en onderstrepingstekens.<br><br>Begin met alfanumeriek. |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tableFixed"]
> | Entiteit | Bereik | Lengte | Geldige tekens |
> | --- | --- | --- | --- |
> | Servers | Globale | 3-63 | Kleine letters, afbreekstreeken en cijfers.<br><br>Kan niet beginnen of eindigen met een koppelteken. |
> | servers/databases | Servers | 1-63 | Alfanumerieke tekens en afbreekstreeken. |
> | servers/firewallRules | Servers | 1-128 | Alfanumerieke tekens, afbreekstreepingen en onderstrepingstekens. |
> | servers /virtualNetworkRules | Servers | 1-128 | Alfanumerieke tekens en afbreekstreeken. |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tableFixed"]
> | Entiteit | Bereik | Lengte | Geldige tekens |
> | --- | --- | --- | --- |
> | Servers | Globale | 3-63 | Kleine letters, afbreekstreelopen en cijfers.<br><br>Kan niet beginnen of eindigen met een koppelteken. |
> | servers/databases | Servers | 1-63 | Alfanumerieke tekens en afbreekstree zij. |
> | servers /firewallRules | Servers | 1-128 | Alfanumerieke tekens, afbreekstreepingstekens en onderstrepingstekens. |
> | servers / virtualNetworkRules | Servers | 1-128 | Alfanumerieke tekens en afbreekstree zij. |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | Entiteit | Bereik | Lengte | Geldige tekens |
> | --- | --- | --- | --- |
> | Servers | Globale | 3-63 | Kleine letters, afbreekstreelopen en cijfers.<br><br>Kan niet beginnen of eindigen met een koppelteken. |
> | servers/databases | Servers | 1-63 | Alfanumerieke tekens en afbreekstreeken. |
> | servers/firewallRules | Servers | 1-128 | Alfanumerieke tekens, afbreekstreepingen en onderstrepingstekens. |
> | servers /virtualNetworkRules | Servers | 1-128 | Alfanumerieke tekens en afbreekstreeken. |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | Entiteit | Bereik | Lengte | Geldige tekens |
> | --- | --- | --- | --- |
> | IotHubs | Globale | 3-50 | Alfanumerieke tekens en afbreekstreeken.<br><br>Kan niet eindigen met afbreekstreetine. |
> | IotHubs/certificaten | IoT-hub | 1-64 | Alfanumerieke tekens, afbreekstreepingen, punten en onderstrepingstekens. |
> | IotHubs / eventHubEndpoints / ConsumerGroups | eventHubEndpoints | 1-50 | Alfanumerieke tekens, afbreekstreepingstekens, punten en onderstrepingstekens. |
> | provisioningServices | resourcegroep | 3-64 | Alfanumerieke tekens en afbreekstree zij.<br><br>Eindigen met alfanumeriek. |
> | provisioningServices/certificaten | provisioningServices | 1-64 | Alfanumerieke tekens, afbreekstreepingstekens, punten en onderstrepingstekens. |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tableFixed"]
> | Entiteit | Bereik | Lengte | Geldige tekens |
> | --- | --- | --- | --- |
> | Labs | resourcegroep | 1-50 | Alfanumerieke tekens, onderstrepingstekens en afbreekstreepingstekens. |
> | labs /customimages | Lab | 1-80 | Alfanumerieke tekens, onderstrepingstekens, afbreekstreepingstekens en haakjes. |
> | labs/formules | Lab | 1-80 | Alfanumerieke tekens, onderstrepingstekens, afbreekstreepingstekens en haakjes. |
> | labs /virtualmachines | Lab | 1-15 (Windows)<br>1-64 (Linux) | Alfanumerieke tekens en afbreekstreeken.<br><br>Begin en eindigt met alfanumeriek. Dit kunnen niet alle getallen zijn. |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | Entiteit | Bereik | Lengte | Geldige tekens |
> | --- | --- | --- | --- |
> | databaseAccounts | Globale | 3-44 | Kleine letters, cijfers en afbreekstreelopen.<br><br>Begin met kleine letters of kleine letters. |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tableFixed"]
> | Entiteit | Bereik | Lengte | Geldige tekens |
> | --- | --- | --- | --- |
> | Domeinen | resourcegroep | 3-50 | Alfanumerieke tekens en afbreekstreeken. |
> | domeinen/onderwerpen | domein | 3-50 | Alfanumerieke tekens en afbreekstreeken. |
> | eventSubscriptions | resourcegroep | 3-64 | Alfanumerieke tekens en afbreekstree zij. |
> | Onderwerpen | resourcegroep | 3-50 | Alfanumerieke tekens en afbreekstree zij. |

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | Entiteit | Bereik | Lengte | Geldige tekens |
> | --- | --- | --- | --- |
> | Clusters | resourcegroep | 6-50 | Alfanumerieke tekens en afbreekstree zij.<br><br>Begin met letter. Eindigen met een letter of cijfer. |
> | Naamruimten | Globale | 6-50 | Alfanumerieke tekens en afbreekstree zij.<br><br>Begin met letter. Eindigen met een letter of cijfer. |
> | naamruimten /AuthorizationRules | naamruimte | 1-50 | Alfanumerieke tekens, punten, afbreekstreepingstekens en onderstrepingstekens.<br><br>Begin en eindigt met een letter of cijfer. |
> | naamruimten /disasterRecoveryConfigs | naamruimte | 1-50 | Alfanumerieke tekens, punten, afbreekstreepingen en onderstrepingstekens.<br><br>Begin en eindigen met een letter of cijfer. |
> | naamruimten/eventhubs | naamruimte | 1-50 | Alfanumerieke tekens, punten, afbreekstreepingen en onderstrepingstekens.<br><br>Begin en eindigen met een letter of cijfer. |
> | naamruimten / eventhubs / authorizationRules | event hub | 1-50 | Alfanumerieke tekens, punten, afbreekstreepingen en onderstrepingstekens.<br><br>Begin en eindigen met een letter of cijfer. |
> | naamruimten / eventhubs / consumergroups | event hub | 1-50 | Alfanumerieke tekens, punten, afbreekstreepingen en onderstrepingstekens.<br><br>Begin en eindigen met een letter of cijfer. |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tableFixed"]
> | Entiteit | Bereik | Lengte | Geldige tekens |
> | --- | --- | --- | --- |
> | Clusters | Globale | 3-59 | Alfanumerieke tekens en afbreekstree zij<br><br>Begin en eindigt met een letter of cijfer. |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tableFixed"]
> | Entiteit | Bereik | Lengte | Geldige tekens |
> | --- | --- | --- | --- |
> | Banen | resourcegroep | 2-64 | Alfanumerieke tekens en afbreekstree zij.<br><br>Begin met letter. |

## <a name="microsoftinsights"></a>Microsoft.Insights

> [!div class="mx-tableFixed"]
> | Entiteit | Bereik | Lengte | Geldige tekens |
> | --- | --- | --- | --- |
> | actionGroups | resourcegroep | 1-260 | Kan niet gebruiken:<br>`/&%\?` <br><br>Kan niet eindigen op spatie of punt.  |
> | Onderdelen | resourcegroep | 1-260 | Kan niet gebruiken:<br>`%&\?/` <br><br>Kan niet eindigen op spatie of punt.  |
> | scheduledQueryRules | resourcegroep | 1-260 | Kan niet gebruiken:<br>`*<>%{}&:\\?/#` <br><br>Kan niet eindigen op spatie of punt.  |
> | metricAlerts | resourcegroep | 1-260 | Kan niet gebruiken:<br>`*#&+:<>?@%{}\/` <br><br>Kan niet eindigen op spatie of punt.  |
> | activityLogAlerts | resourcegroep | 1-260 | Kan niet gebruiken:<br>`<>*%{}&:\\?+/#` <br><br>Kan niet eindigen op spatie of punt.  |

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

> [!div class="mx-tableFixed"]
> | Entiteit | Bereik | Lengte | Geldige tekens |
> | --- | --- | --- | --- |
> | IoTApps | Globale | 2-63 | Kleine letters, cijfers en afbreekstreelopen.<br><br>Begin met kleine letters of kleine letters. |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tableFixed"]
> | Entiteit | Bereik | Lengte | Geldige tekens |
> | --- | --- | --- | --- |
> | Kluizen | Globale | 3-24 | Alfanumerieke tekens en afbreekstreeken.<br><br>Begin met letter. Eindigen met letter of cijfer. Kan geen opeenvolgende afbreekstreeepten bevatten. |
> | kluizen/geheimen | Kluis | 1-127 | Alfanumerieke tekens en afbreekstree zij. |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | Entiteit | Bereik | Lengte | Geldige tekens |
> | --- | --- | --- | --- |
> | Clusters | Globale | 4-22 | Kleine letters en cijfers.<br><br>Begin met letter. |
> | /clusters/ databases | cluster | 1-260 | Alfanumerieke tekens, afbreekstreeken, spaties en punten. |
> | /clusters / databases / dataConnections | database | 1-40 | Alfanumerieke tekens, afbreekstreeken, spaties en punten. |
> | /clusters / databases / eventhubconnections | database | 1-40 | Alfanumerieke tekens, afbreekstreeken, spaties en punten. |

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tableFixed"]
> | Entiteit | Bereik | Lengte | Geldige tekens |
> | --- | --- | --- | --- |
> | integrationAccounts | resourcegroep | 1-80 | Alfanumerieke tekens, afbreekstreepingstekens, onderstrepingstekens, punten en haakjes. |
> | integrationAccounts/assemblies | integratieaccount | 1-80 | Alfanumerieke tekens, afbreekstreepingstekens, onderstrepingstekens, punten en haakjes. |
> | integrationAccounts/batchConfigurations | integratieaccount | 1-20 | Alfanumeriek. |
> | integrationAccounts/certificates | integratieaccount | 1-80 | Alfanumerieke tekens, afbreekstreepingen, onderstrepingstekens, punten en haakjes. |
> | integrationAccounts/maps | integratieaccount | 1-80 | Alfanumerieke tekens, afbreekstreepingen, onderstrepingstekens, punten en haakjes. |
> | integrationAccounts/partners | integratieaccount | 1-80 | Alfanumerieke tekens, afbreekstreepingen, onderstrepingstekens, punten en haakjes. |
> | integrationAccounts/rosettanetprocessconfigurations | integratieaccount | 1-80 | Alfanumerieke tekens, afbreekstreepingen, onderstrepingstekens, punten en haakjes. |
> | integrationAccounts/schema's | integratieaccount | 1-80 | Alfanumerieke tekens, afbreekstreepingen, onderstrepingstekens, punten en haakjes. |
> | integrationAccounts/sessions | integratieaccount | 1-80 | Alfanumerieke tekens, afbreekstreepingen, onderstrepingstekens, punten en haakjes. |
> | integrationServiceEnvironments | resourcegroep | 1-80 | Alfanumerieke tekens, afbreekstreepingen, punten en onderstrepingstekens. |
> | integrationServiceEnvironments /managedApis | integratieserviceomgeving | 1-80 | Alfanumerieke tekens, afbreekstreepingen, punten en onderstrepingstekens. |
> | Werkstromen | resourcegroep | 1-80 | Alfanumerieke tekens, afbreekstreepingstekens, onderstrepingstekens, punten en haakjes. |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tableFixed"]
> | Entiteit | Bereik | Lengte | Geldige tekens |
> | --- | --- | --- | --- |
> | commitmentPlans | resourcegroep | 1-260 | Kan niet gebruiken:<br>`<>*%&:?+/\\`<br><br>Kan niet eindigen met een spatie. |
> | Webservices | resourcegroep | 1-260 | Kan niet gebruiken:<br>`<>*%&:?+/\\`<br><br>Kan niet eindigen met een spatie. |
> | workspaces | resourcegroep | 1-260 | Kan niet gebruiken:<br>`<>*%&:?+/\\`<br><br>Kan niet eindigen met een spatie. |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | Entiteit | Bereik | Lengte | Geldige tekens |
> | --- | --- | --- | --- |
> | workspaces | resourcegroep | 3-33 | Alfanumerieke tekens en afbreekstree zij. |
> | werkruimten/berekeningen | werkruimte | 2-16 | Alfanumerieke tekens en afbreekstree zij. |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | Entiteit | Bereik | Lengte | Geldige tekens |
> | --- | --- | --- | --- |
> | userAssignedIdentities | resourcegroep | 3-128 | Alfanumerieke tekens, afbreekstreepingstekens en onderstrepingstekens<br><br>Begin met een letter of cijfer. |

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tableFixed"]
> | Entiteit | Bereik | Lengte | Geldige tekens |
> | --- | --- | --- | --- |
> | accounts | resourcegroep | 1-98 (voor resourcegroepnaam en accountnaam) | Alfanumerieke tekens, onderstrepingstekens, punten en afbreekstreepingen.<br><br>Begin met alfanumeriek. |

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tableFixed"]
> | Entiteit | Bereik | Lengte | Geldige tekens |
> | --- | --- | --- | --- |
> | mediaservices | resourcegroep | 3-24 | Kleine letters en cijfers. |
> | mediaservices /liveEvents | Mediaservice | 1-32 | Alfanumerieke tekens en afbreekstreeken.<br><br>Begin met alfanumeriek. |
> | mediaservices / liveEvents / liveOutputs | Livegebeurtenis | 1-256 | Alfanumerieke tekens en afbreekstreeken.<br><br>Begin met alfanumeriek. |
> | mediaservices/streamingEndpoints | Mediaservice | 1-24 | Alfanumerieke tekens en afbreekstree zij.<br><br>Begin met alfanumeriek. |

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tableFixed"]
> | Entiteit | Bereik | Lengte | Geldige tekens |
> | --- | --- | --- | --- |
> | applicationGateways | resourcegroep | 1-80 | Alfanumerieke tekens, onderstrepingstekens, punten en afbreekstreepingstekens.<br><br>Begin met alfanumeriek. Alfanumeriek of onderstrepingsteken beëindigen. |
> | applicationSecurityGroups | resourcegroep | 1-80 | Alfanumerieke tekens, onderstrepingstekens, punten en afbreekstreepingstekens.<br><br>Begin met alfanumeriek. Alfanumeriek of onderstrepingsteken beëindigen. |
> | azureFirewalls | resourcegroep | 1-80 | Alfanumerieke tekens, onderstrepingstekens, punten en afbreekstreepingstekens.<br><br>Begin met alfanumeriek. Eindigen met alfanumeriek of onderstrepingsteken. |
> | bastionHosts | resourcegroep | 1-80 | Alfanumerieke tekens, onderstrepingstekens, punten en afbreekstreepingstekens.<br><br>Begin met alfanumeriek. Alfanumeriek of onderstrepingsteken beëindigen. |
> | Verbindingen | resourcegroep | 1-80 | Alfanumerieke tekens, onderstrepingstekens, punten en afbreekstreepingstekens.<br><br>Begin met alfanumeriek. Alfanumeriek of onderstrepingsteken beëindigen. |
> | dnsZones | resourcegroep | 1-63 tekens<br><br>2 tot 34 labels<br><br>Elk label is een reeks tekens gescheiden door een punt. Een voorbeeld: **contoso.com** heeft twee labels. | Elk label kan alfanumerieke tekens, onderstrepingstekens en afbreekstreepingstekens bevatten.<br><br>Elk label wordt gescheiden door een punt. |
> | expressRouteCircuits | resourcegroep | 1-80 | Alfanumerieke tekens, onderstrepingstekens, punten en afbreekstreepingen.<br><br>Begin met alfanumeriek. Alfanumeriek of onderstrepingsteken beëindigen. |
> | firewallPolicies | resourcegroep | 1-80 | Alfanumerieke tekens, onderstrepingstekens, punten en afbreekstreepingen.<br><br>Begin met alfanumeriek. Alfanumeriek of onderstrepingsteken beëindigen. |
> | firewallPolicies/ruleGroups | firewallbeleid | 1-80 | Alfanumerieke tekens, onderstrepingstekens, punten en afbreekstreepingen.<br><br>Begin met alfanumeriek. Alfanumeriek of onderstrepingsteken beëindigen. |
> | frontDoors | Globale | 5-64 | Alfanumerieke tekens en afbreekstree zij.<br><br>Begin en eind met alfanumeriek. |
> | frontdoorWebApplicationFirewallPolicies | resourcegroep | 1-128 | Alfanumeriek.<br><br>Begin met letter. |
> | loadBalancers | resourcegroep | 1-80 | Alfanumerieke tekens, onderstrepingstekens, punten en afbreekstreepingstekens.<br><br>Begin met alfanumeriek. Alfanumeriek of onderstrepingsteken beëindigen. |
> | loadBalancers/inboundNatRules | load balancer | 1-80 | Alfanumerieke tekens, onderstrepingstekens, punten en afbreekstreepingstekens.<br><br>Begin met alfanumeriek. Alfanumeriek of onderstrepingsteken beëindigen. |
> | localNetworkGateways | resourcegroep | 1-80 | Alfanumerieke tekens, onderstrepingstekens, punten en afbreekstreepingen.<br><br>Begin met alfanumeriek. Alfanumeriek of onderstrepingsteken beëindigen. |
> | networkInterfaces | resourcegroep | 1-80 | Alfanumerieke tekens, onderstrepingstekens, punten en afbreekstreepingen.<br><br>Begin met alfanumeriek. Alfanumeriek of onderstrepingsteken beëindigen. |
> | networkSecurityGroups | resourcegroep | 1-80 | Alfanumerieke tekens, onderstrepingstekens, punten en afbreekstreepingen.<br><br>Begin met alfanumeriek. Alfanumeriek of onderstrepingsteken beëindigen. |
> | networkSecurityGroups/securityRules | netwerkbeveiligingsgroep | 1-80 |  Alfanumerieke tekens, onderstrepingstekens, punten en afbreekstreepingen.<br><br>Begin met alfanumeriek. Alfanumeriek of onderstrepingsteken beëindigen. |
> | networkWatchers | resourcegroep | 1-80 | Alfanumerieke tekens, onderstrepingstekens, punten en afbreekstreepingen.<br><br>Begin met alfanumeriek. Alfanumeriek of onderstrepingsteken beëindigen. |
> | privateDnsZones | resourcegroep | 1-63 tekens<br><br>2 tot 34 labels<br><br>Elk label is een reeks tekens, gescheiden door een punt. Zo heeft **contoso.com** twee labels. | Elk label kan alfanumerieke tekens, onderstrepingstekens en afbreekstreepingstekens bevatten.<br><br>Elk label wordt gescheiden door een punt. |
> | privateDnsZones /virtualNetworkLinks | privé-DNS-zone | 1-80 | Alfanumerieke tekens, onderstrepingstekens, punten en afbreekstreepingstekens.<br><br>Begin met alfanumeriek. Alfanumeriek of onderstrepingsteken beëindigen. |
> | publicIPAddresses | resourcegroep | 1-80 | Alfanumerieke tekens, onderstrepingstekens, punten en afbreekstreepingstekens.<br><br>Begin met alfanumeriek. Alfanumeriek of onderstrepingsteken beëindigen. |
> | publicIPPrefixes | resourcegroep | 1-80 | Alfanumerieke tekens, onderstrepingstekens, punten en afbreekstreepingstekens.<br><br>Begin met alfanumeriek. Alfanumeriek of onderstrepingsteken beëindigen. |
> | routeFilters | resourcegroep | 1-80 | Alfanumerieke tekens, onderstrepingstekens, punten en afbreekstreepingen.<br><br>Begin met alfanumeriek. Alfanumeriek of onderstrepingsteken beëindigen. |
> | routeFilters/routeFilterRules | routefilter | 1-80 | Alfanumerieke tekens, onderstrepingstekens, punten en afbreekstreepingen.<br><br>Begin met alfanumeriek. Alfanumeriek of onderstrepingsteken beëindigen. |
> | routeTables | resourcegroep | 1-80 | Alfanumerieke tekens, onderstrepingstekens, punten en afbreekstreepingen.<br><br>Begin met alfanumeriek. Alfanumeriek of onderstrepingsteken beëindigen. |
> | routeTables /routes | routetabel | 1-80 | Alfanumerieke tekens, onderstrepingstekens, punten en afbreekstreepingen.<br><br>Begin met alfanumeriek. Alfanumeriek of onderstrepingsteken beëindigen. |
> | serviceEndpointPolicies | resourcegroep | 1-80 | Alfanumerieke tekens, onderstrepingstekens, punten en afbreekstreepingen.<br><br>Begin met alfanumeriek. Alfanumeriek of onderstrepingsteken beëindigen. |
> | trafficmanagerprofiles | Globale | 1-63 | Alfanumerieke tekens, afbreekstreeken en punten.<br><br>Begin en eind met alfanumeriek. |
> | virtualNetworkGateways | resourcegroep | 1-80 | Alfanumerieke tekens, onderstrepingstekens, punten en afbreekstreepingstekens.<br><br>Begin met alfanumeriek. Alfanumeriek of onderstrepingsteken beëindigen. |
> | virtualNetworks | resourcegroep | 2-64 | Alfanumerieke tekens, onderstrepingstekens, punten en afbreekstreepingstekens.<br><br>Begin met alfanumeriek. Alfanumeriek of onderstrepingsteken beëindigen. |
> | virtualnetworks/subnetten | virtueel netwerk | 1-80 | Alfanumerieke tekens, onderstrepingstekens, punten en afbreekstreepingstekens.<br><br>Begin met alfanumeriek. Alfanumeriek of onderstrepingsteken beëindigen. |
> | virtualNetworks/virtualNetworkPeerings | virtueel netwerk | 1-80 | Alfanumerieke tekens, onderstrepingstekens, punten en afbreekstreepingen.<br><br>Begin met alfanumeriek. Alfanumeriek of onderstrepingsteken beëindigen. |
> | virtualWans | resourcegroep | 1-80 | Alfanumerieke tekens, onderstrepingstekens, punten en afbreekstreepingen.<br><br>Begin met alfanumeriek. Alfanumeriek of onderstrepingsteken beëindigen. |
> | vpnGateways | resourcegroep | 1-80 | Alfanumerieke tekens, onderstrepingstekens, punten en afbreekstreepingen.<br><br>Begin met alfanumeriek. Alfanumeriek of onderstrepingsteken beëindigen. |
> | vpnGateways /vpnConnections | VPN-gateway | 1-80 | Alfanumerieke tekens, onderstrepingstekens, punten en afbreekstreepingen.<br><br>Begin met alfanumeriek. Alfanumeriek of onderstrepingsteken beëindigen. |
> | vpnSites | resourcegroep | 1-80 | Alfanumerieke tekens, onderstrepingstekens, punten en afbreekstreepingen.<br><br>Begin met alfanumeriek. Alfanumeriek of onderstrepingsteken beëindigen. |

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | Entiteit | Bereik | Lengte | Geldige tekens |
> | --- | --- | --- | --- |
> | Naamruimten | Globale | 6-50 | Alfanumerieke tekens en afbreekstree zij<br><br>Begin met letter. Eindigen met alfanumeriek. |
> | naamruimten /AuthorizationRules | naamruimte | 1-256 | Alfanumerieke tekens, punten, afbreekstreepingstekens en onderstrepingstekens.<br><br>Alfanumeriek starten. |
> | namespaces/notificationHubs | naamruimte | 1-260 | Alfanumerieke tekens, punten, afbreekstreepingstekens en onderstrepingstekens.<br><br>Alfanumeriek starten. |
> | namespaces / notificationHubs / AuthorizationRules | Notification Hub | 1-256 | Alfanumerieke tekens, punten, afbreekstreepingstekens en onderstrepingstekens.<br><br>Alfanumeriek starten. |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tableFixed"]
> | Entiteit | Bereik | Lengte | Geldige tekens |
> | --- | --- | --- | --- |
> | Clusters | resourcegroep | 4-63 | Alfanumerieke tekens en afbreekstreeken.<br><br>Begin en eindigt met alfanumeriek. |
> | workspaces | Globale | 4-63 | Alfanumerieke tekens en afbreekstreeken.<br><br>Begin en eindigt met alfanumeriek. |

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tableFixed"]
> | Entiteit | Bereik | Lengte | Geldige tekens |
> | --- | --- | --- | --- |
> | oplossingen | werkruimte | N.v.t. | Voor oplossingen die door Microsoft zijn geschreven, moet de naam het volgende patroon hebben:<br>`SolutionType(WorkspaceName)`<br><br>Voor oplossingen die zijn geschreven door derden, moet de naam het patroon hebben:<br>`SolutionType[WorkspaceName]`<br><br>Een geldige naam is bijvoorbeeld:<br>`AntiMalware(contoso-IT)`<br><br>Het oplossingstype is casegevoelig. |

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tableFixed"]
> | Entiteit | Bereik | Lengte | Geldige tekens |
> | --- | --- | --- | --- |
> | dashboards | resourcegroep | 3-160 | Alfanumerieke tekens en afbreekstreeken.<br><br>Als u beperkte tekens wilt gebruiken, voegt u een tag met de naam **hidden-title toe** met de dashboardnaam die u wilt gebruiken. In de portal wordt die naam weergegeven wanneer het dashboard wordt weergegeven. |

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

> [!div class="mx-tableFixed"]
> | Entiteit | Bereik | Lengte | Geldige tekens |
> | --- | --- | --- | --- |
> | workspaceCollections | regio | 3-63 | Alfanumerieke tekens en afbreekstreeken.<br><br>Kan niet beginnen met afbreekstreetine. Kan geen opeenvolgende afbreekstreeepten gebruiken. |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tableFixed"]
> | Entiteit | Bereik | Lengte | Geldige tekens |
> | --- | --- | --- | --- |
> | Capaciteiten | regio | 3-63 | Kleine letters of cijfers<br><br>Begin met kleine letters. |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tableFixed"]
> | Entiteit | Bereik | Lengte | Geldige tekens |
> | --- | --- | --- | --- |
> | Kluizen | resourcegroep | 2-50 | Alfanumerieke tekens en afbreekstree zij.<br><br>Begin met letter. |
> | kluizen/backupPolicies | kluis | 3-150 | Alfanumerieke tekens en afbreekstree zij.<br><br>Begin met letter. Kan niet eindigen met afbreekstreetine. |

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tableFixed"]
> | Entiteit | Bereik | Lengte | Geldige tekens |
> | --- | --- | --- | --- |
> | Naamruimten | Globale | 6-50 | Alfanumerieke tekens en afbreekstree zij.<br><br>Begin met een letter. Eindigen met een letter of cijfer. |
> | naamruimten /AuthorizationRules | naamruimte | 1-50 |  Alfanumerieke tekens, punten, afbreekstreepingen en onderstrepingstekens.<br><br>Begin en eindigt met alfanumeriek. |
> | naamruimten / HybridConnections | naamruimte | 1-260 | Alfanumerieke tekens, punten, afbreekstreepingen, onderstrepingstekens en slashes.<br><br>Begin en eindigt met alfanumeriek. |
> | naamruimten / HybridConnections/authorizationRules | hybride verbinding | 1-50 | Alfanumerieke tekens, punten, afbreekstreepingen en onderstrepingstekens.<br><br>Begin en eindigt met alfanumeriek. |
> | naamruimten / WcfRelays | naamruimte | 1-260 | Alfanumerieke tekens, punten, afbreekstreepingen, onderstrepingstekens en slashes.<br><br>Begin en eindigt met alfanumeriek. |
> | naamruimten / WcfRelays / authorizationRules | Wcf Relay | 1-50 | Alfanumerieke tekens, punten, afbreekstreepingen en onderstrepingstekens.<br><br>Begin en eind met alfanumeriek. |

## <a name="microsoftresources"></a>Microsoft.Resources

> [!div class="mx-tableFixed"]
> | Entiteit | Bereik | Lengte | Geldige tekens |
> | --- | --- | --- | --- |
> | Implementaties | resourcegroep | 1-64 | Alfanumerieke tekens, onderstrepingstekens, haakjes, afbreekstreeën en punten. |
> | resourcegroepen | abonnement | 1-90 | Alfanumerieke tekens, onderstrepingstekens, haakjes, afbreekstreepingstekens, punten en unicode-tekens die overeenkomen met de [regex-documentatie](/rest/api/resources/resourcegroups/createorupdate).<br><br>Kan niet eindigen met punt. |
> | tagNames | resource | 1-512 | Kan niet gebruiken:<br>`<>%&\?/` |
> | tagNames/tagValues | tagnaam | 1-256 | Alle tekens. |
> | templateSpecs | resourcegroep | 1-90 | Alfanumerieke tekens, onderstrepingstekens, haakjes, afbreekstreeën en punten. |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | Entiteit | Bereik | Lengte | Geldige tekens |
> | --- | --- | --- | --- |
> | Naamruimten | Globale | 6-50 | Alfanumerieke tekens en afbreekstree zij.<br><br>Begin met een letter. Eindigen met een letter of cijfer.<br><br>Zie Naamruimte maken [voor meer informatie.](/rest/api/servicebus/create-namespace) |
> | naamruimten /AuthorizationRules | naamruimte | 1-50 | Alfanumerieke tekens, punten, afbreekstreepingen en onderstrepingstekens.<br><br>Begin en eindigt met alphnumeric. |
> | naamruimten /disasterRecoveryConfigs | Globale | 6-50 | Alfanumerieke tekens en afbreekstreeken.<br><br>Begin met letter. Eindigen met alfanumeriek. |
> | naamruimten /migrationConfigurations | naamruimte |  | Moet altijd worden **$default**. |
> | naamruimten/wachtrijen | naamruimte | 1-260 | Alfanumerieke tekens, punten, afbreekstreepingen, onderstrepingstekens en slashes.<br><br>Begin en eindigt met alfanumeriek. |
> | naamruimten / wachtrijen / authorizationRules | wachtrij | 1-50 | Alfanumerieke tekens, punten, afbreekstreepingstekens en onderstrepingstekens.<br><br>Begin en eind met alphnumeric. |
> | naamruimten/onderwerpen | naamruimte | 1-260 | Alfanumerieke tekens, punten, afbreekstreepingstekens, onderstrepingstekens en slashes.<br><br>Begin en eind met alfanumeriek. |
> | naamruimten / onderwerpen / authorizationRules | onderwerp | 1-50 | Alfanumerieke tekens, punten, afbreekstreepingstekens en onderstrepingstekens.<br><br>Begin en eind met alphnumeric. |
> | naamruimten/onderwerpen/abonnementen | onderwerp | 1-50 | Alfanumerieke tekens, punten, afbreekstreepingstekens en onderstrepingstekens.<br><br>Begin en eind met alphnumeric. |
> | naamruimten / onderwerpen / abonnementen / regels | abonnement | 1-50 | Alfanumerieke tekens, punten, afbreekstreepingstekens en onderstrepingstekens.<br><br>Begin en eind met alphnumeric. |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tableFixed"]
> | Entiteit | Bereik | Lengte | Geldige tekens |
> | --- | --- | --- | --- |
> | Clusters | regio | 4-23 | Kleine letters, cijfers en afbreekstreelopen.<br><br>Begin met kleine letters. Eindigen met kleine letters of kleine letters. |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tableFixed"]
> | Entiteit | Bereik | Lengte | Geldige tekens |
> | --- | --- | --- | --- |
> | signalR | Globale | 3-63 | Alfanumerieke tekens en afbreekstreeken.<br><br>Begin met letter. Eindigen met letter of cijfer.  |

## <a name="microsoftsql"></a>Microsoft.Sql

> [!div class="mx-tableFixed"]
> | Entiteit | Bereik | Lengte | Geldige tekens |
> | --- | --- | --- | --- |
> | managedInstances | Globale | 1-63 | Kleine letters, cijfers en afbreekstreelopen.<br><br>Kan niet beginnen of eindigen met een koppelteken. <br><br> Mag geen speciale tekens bevatten, zoals `@` . |
> | Servers | Globale | 1-63 | Kleine letters, cijfers en afbreekstreelopen.<br><br>Kan niet beginnen of eindigen met een koppelteken. |
> | servers/beheerders | server |  | Moet `ActiveDirectory` zijn. |
> | servers/databases | server | 1-128 | Kan niet gebruiken:<br>`<>*%&:\/?`<br><br>Kan niet eindigen met punt of spatie. |
> | servers / databases / syncGroups | database | 1-150 | Alfanumerieke tekens, afbreekstreepingstekens en onderstrepingstekens. |
> | servers/elasticPools | server | 1-128 | Kan niet gebruiken:<br>`<>*%&:\/?`<br><br>Kan niet eindigen met punt of spatie. |
> | servers /failoverGroups | Globale | 1-63 | Kleine letters, cijfers en afbreekstreelopen.<br><br>Kan niet beginnen of eindigen met een koppelteken. |
> | servers /firewallRules | server | 1-128 | Kan niet gebruiken:<br>`<>*%&:;\/?`<br><br>Kan niet eindigen met punt. |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | Entiteit | Bereik | Lengte | Geldige tekens |
> | --- | --- | --- | --- |
> | storageAccounts | Globale | 3-24 | Kleine letters en cijfers. |
> | storageAccounts/blobServices | opslagaccount |  | Moet `default` zijn. |
> | storageAccounts / blobServices / containers | opslagaccount | 3-63 | Kleine letters, cijfers en afbreekstreelopen.<br><br>Begin met kleine letters of kleine letters. Kan geen opeenvolgende afbreekstreeepten gebruiken. |
> | storageAccounts/fileServices | opslagaccount |  | Moet `default` zijn. |
> | storageAccounts/fileServices/shares | opslagaccount | 3-63 | Kleine letters, cijfers en afbreekstreelopen.<br><br>Kan niet beginnen of eindigen met een koppelteken. Kan geen opeenvolgende afbreekstreeepten gebruiken. |
> | storageAccounts/managementPolicies | opslagaccount |  | Moet `default` zijn. |
> | blob | container | 1-1024 | ALLE URL-tekens, hoofd- en hoofdtekens |
> | wachtrij | opslagaccount | 3-63 | Kleine letters, cijfers en afbreekstreelopen.<br><br>Kan niet beginnen of eindigen met een koppelteken. Kan geen opeenvolgende afbreekstreeepten gebruiken. |
> | tabel | opslagaccount | 3-63 | Alfanumeriek.<br><br>Begin met letter. |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tableFixed"]
> | Entiteit | Bereik | Lengte | Geldige tekens |
> | --- | --- | --- | --- |
> | storageSyncServices | resourcegroep | 1-260 | Alfanumerieke tekens, spaties, punten, afbreekstreepingstekens en onderstrepingstekens.<br><br>Kan niet eindigen met punt of spatie. |
> | storageSyncServices/syncGroups | opslagsynchronisatieservice | 1-260 | Alfanumerieke tekens, spaties, punten, afbreekstreepingstekens en onderstrepingstekens.<br><br>Kan niet eindigen met punt of spatie. |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tableFixed"]
> | Entiteit | Bereik | Lengte | Geldige tekens |
> | --- | --- | --- | --- |
> | Managers | resourcegroep | 2-50 | Alfanumerieke tekens en afbreekstree zij.<br><br>Begin met letter. Eindigen met alfanumeriek. |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!div class="mx-tableFixed"]
> | Entiteit | Bereik | Lengte | Geldige tekens |
> | --- | --- | --- | --- |
> | streamingjobs | resourcegroep | 3-63 | Alfanumerieke tekens, afbreekstreepingstekens en onderstrepingstekens. |
> | streamingjobs/functies | streaming-taak | 3-63 | Alfanumerieke tekens, afbreekstreepingen en onderstrepingstekens. |
> | streamingjobs/invoer | streaming-taak | 3-63 | Alfanumerieke tekens, afbreekstreepingen en onderstrepingstekens. |
> | streamingjobs/outputs | streaming-taak | 3-63 | Alfanumerieke tekens, afbreekstreepingen en onderstrepingstekens. |
> | streamingjobs/transformaties | streaming-taak | 3-63 | Alfanumerieke tekens, afbreekstreepingen en onderstrepingstekens. |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | Entiteit | Bereik | Lengte | Geldige tekens |
> | --- | --- | --- | --- |
> | Omgevingen | resourcegroep | 1-90 | Kan niet gebruiken:<br>`'<>%&:\?/#` |
> | omgevingen /accessPolicies | omgeving | 1-90 | Kan niet gebruiken:<br> `'<>%&:\?/#` |
> | omgevingen / eventSources | omgeving | 1-90 | Kan niet gebruiken:<br>`'<>%&:\?/#` |
> | omgevingen /referenceDataSets | omgeving | 3-63 | Alfanumeriek |

## <a name="microsoftweb"></a>Microsoft.Web

> [!div class="mx-tableFixed"]
> | Entiteit | Bereik | Lengte | Geldige tekens |
> | --- | --- | --- | --- |
> | certificaten | resourcegroep | 1-260 | Kan niet gebruiken:<br>`/` <br><br>Kan niet eindigen op spatie of punt.  | 
> | serverfarms | resourcegroep | 1-40 | Alfanumerieke tekens en afbreekstree zij. |
> | sites | globaal of per domein. Zie de opmerking hieronder. | 2-60 | Bevat alfanumerieke tekens en afbreekstreeepten.<br><br>Kan niet beginnen of eindigen met een koppelteken. |
> | sites/sites | site | 2-59 | Alfanumerieke tekens en afbreekstree zij. |

> [!NOTE]
> Een website moet een wereldwijd unieke URL hebben. Wanneer u een website maakt die gebruikmaakt van een hostingplan, is de URL `http://<app-name>.azurewebsites.net` . De naam van de app moet wereldwijd uniek zijn. Wanneer u een website maakt die gebruikmaakt van een App Service Environment, moet de app-naam uniek zijn binnen het domein voor [de App Service Environment.](../../app-service/environment/using-an-ase.md#app-access) In beide gevallen is de URL van de site wereldwijd uniek.
>
> Azure Functions heeft dezelfde naamgevingsregels en -beperkingen als Microsoft.Web/sites.

## <a name="next-steps"></a>Volgende stappen

Zie Ready: Recommended naming and tagging conventions (Gereed: aanbevolen naamgevings- en [tagconventmen)](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging)voor aanbevelingen voor het benoemen van resources.
