---
title: Ondersteuning voor het verplaatsen van bewerkingen per resource type
description: Een lijst met de Azure-resource typen die kunnen worden verplaatst naar een nieuwe resource groep of een nieuw abonnement.
ms.topic: conceptual
ms.date: 12/07/2020
ms.openlocfilehash: 226686e51c8b59b6963609a95a2cb3c2cc03d621
ms.sourcegitcommit: 8b4b4e060c109a97d58e8f8df6f5d759f1ef12cf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/07/2020
ms.locfileid: "96780991"
---
# <a name="move-operation-support-for-resources"></a>Ondersteuning voor het verplaatsen van resources voor bronnen

Dit artikel geeft een overzicht van de vraag of een Azure-resource type de verplaatsings bewerking ondersteunt. Het bevat ook informatie over speciale voor waarden die u moet overwegen bij het verplaatsen van een resource.

> [!IMPORTANT]
> In de meeste gevallen kan een onderliggende resource niet onafhankelijk van de bovenliggende resource worden verplaatst. Onderliggende resources hebben een resource type in de indeling van `<resource-provider-namespace>/<parent-resource>/<child-resource>` . `Microsoft.ServiceBus/namespaces/queues`Is bijvoorbeeld een onderliggende bron van `Microsoft.ServiceBus/namespaces` . Wanneer u de bovenliggende resource verplaatst, wordt de onderliggende resource automatisch verplaatst. Als er in dit artikel geen onderliggende resource wordt weer geven, kunt u ervan uitgaan dat deze is verplaatst met de bovenliggende resource. Als de bovenliggende resource geen verplaatsing ondersteunt, kan de onderliggende resource niet worden verplaatst.

Ga naar de naam ruimte van een resource provider:
> [!div class="op_single_selector"]
> - [Micro soft. AAD](#microsoftaad)
> - [micro soft. aadiam](#microsoftaadiam)
> - [Micro soft. Addons](#microsoftaddons)
> - [Microsoft.ADHybridHealthService](#microsoftadhybridhealthservice)
> - [Microsoft.Advisor](#microsoftadvisor)
> - [Micro soft. AlertsManagement](#microsoftalertsmanagement)
> - [Microsoft.AnalysisServices](#microsoftanalysisservices)
> - [Microsoft.ApiManagement](#microsoftapimanagement)
> - [Micro soft. AppConfiguration](#microsoftappconfiguration)
> - [Micro soft. AppPlatform](#microsoftappplatform)
> - [Micro soft. AppService](#microsoftappservice)
> - [Micro soft. Attestation](#microsoftattestation)
> - [Microsoft.Authorization](#microsoftauthorization)
> - [Microsoft.Automation](#microsoftautomation)
> - [Micro soft. AVS](#microsoftavs)
> - [Microsoft.AzureActiveDirectory](#microsoftazureactivedirectory)
> - [Micro soft. Azureworden](#microsoftazuredata)
> - [Micro soft. AzureStack](#microsoftazurestack)
> - [Micro soft. AzureStackHCI](#microsoftazurestackhci)
> - [Microsoft.Batch](#microsoftbatch)
> - [Microsoft.Billing](#microsoftbilling)
> - [Microsoft.BingMaps](#microsoftbingmaps)
> - [Micro soft. BizTalkServices](#microsoftbiztalkservices)
> - [Micro soft. Block Chain](#microsoftblockchain)
> - [Micro soft. BlockchainTokens](#microsoftblockchaintokens)
> - [Micro soft. blauw druk](#microsoftblueprint)
> - [Micro soft. BotService](#microsoftbotservice)
> - [Microsoft.Cache](#microsoftcache)
> - [Micro soft. capacity](#microsoftcapacity)
> - [Micro soft. CDN](#microsoftcdn)
> - [Microsoft.CertificateRegistration](#microsoftcertificateregistration)
> - [Microsoft.ClassicCompute](#microsoftclassiccompute)
> - [Micro soft. ClassicInfrastructureMigrate](#microsoftclassicinfrastructuremigrate)
> - [Microsoft.ClassicNetwork](#microsoftclassicnetwork)
> - [Microsoft.ClassicStorage](#microsoftclassicstorage)
> - [Micro soft. ClassicSubscription](#microsoftclassicsubscription)
> - [Microsoft.CognitiveServices](#microsoftcognitiveservices)
> - [Microsoft.Commerce](#microsoftcommerce)
> - [Microsoft.Compute](#microsoftcompute)
> - [Micro soft. verbruik](#microsoftconsumption)
> - [Micro soft. ContainerInstance](#microsoftcontainerinstance)
> - [Microsoft.ContainerRegistry](#microsoftcontainerregistry)
> - [Micro soft. container service](#microsoftcontainerservice)
> - [Microsoft.ContentModerator](#microsoftcontentmoderator)
> - [Micro soft. CortanaAnalytics](#microsoftcortanaanalytics)
> - [Micro soft. CostManagement](#microsoftcostmanagement)
> - [Microsoft.CustomerInsights](#microsoftcustomerinsights)
> - [Micro soft. CustomerLockbox](#microsoftcustomerlockbox)
> - [Micro soft. CustomProviders](#microsoftcustomproviders)
> - [Micro soft. DataBox](#microsoftdatabox)
> - [Micro soft. DataBoxEdge](#microsoftdataboxedge)
> - [Micro soft. Databricks](#microsoftdatabricks)
> - [Microsoft.DataCatalog](#microsoftdatacatalog)
> - [Micro soft. DataConnect](#microsoftdataconnect)
> - [Micro soft. DataExchange](#microsoftdataexchange)
> - [Microsoft.DataFactory](#microsoftdatafactory)
> - [Micro soft. DataLake](#microsoftdatalake)
> - [Microsoft.DataLakeAnalytics](#microsoftdatalakeanalytics)
> - [Microsoft.DataLakeStore](#microsoftdatalakestore)
> - [Micro soft. DataMigration](#microsoftdatamigration)
> - [Micro soft. DataProtection](#microsoftdataprotection)
> - [Micro soft. DataShare](#microsoftdatashare)
> - [Micro soft. DBforMariaDB](#microsoftdbformariadb)
> - [Micro soft. DBforMySQL](#microsoftdbformysql)
> - [Micro soft. DBforPostgreSQL](#microsoftdbforpostgresql)
> - [Micro soft. DeploymentManager](#microsoftdeploymentmanager)
> - [Micro soft. DesktopVirtualization](#microsoftdesktopvirtualization)
> - [Microsoft.Devices](#microsoftdevices)
> - [Micro soft. DevOps](#microsoftdevops)
> - [Micro soft. DevSpaces](#microsoftdevspaces)
> - [Microsoft.DevTestLab](#microsoftdevtestlab)
> - [Micro soft. DigitalTwins](#microsoftdigitaltwins)
> - [Microsoft.DocumentDB](#microsoftdocumentdb)
> - [Micro soft. DomainRegistration](#microsoftdomainregistration)
> - [Micro soft. EnterpriseKnowledgeGraph](#microsoftenterpriseknowledgegraph)
> - [Micro soft. EventGrid](#microsofteventgrid)
> - [Microsoft.EventHub](#microsofteventhub)
> - [Micro soft. experimenten](#microsoftexperimentation)
> - [Micro soft. Falcon](#microsoftfalcon)
> - [Microsoft.Features](#microsoftfeatures)
> - [Micro soft. Genomics](#microsoftgenomics)
> - [Microsoft.GuestConfiguration](#microsoftguestconfiguration)
> - [Micro soft. HanaOnAzure](#microsofthanaonazure)
> - [Micro soft. HardwareSecurityModules](#microsofthardwaresecuritymodules)
> - [Microsoft.HDInsight](#microsofthdinsight)
> - [Micro soft. HealthcareApis](#microsofthealthcareapis)
> - [Microsoft.HybridCompute](#microsofthybridcompute)
> - [Micro soft. HybridData](#microsofthybriddata)
> - [Micro soft. HybridNetwork](#microsofthybridnetwork)
> - [Micro soft. Hydra](#microsofthydra)
> - [Microsoft.ImportExport](#microsoftimportexport)
> - [micro soft. Insights](#microsoftinsights)
> - [Micro soft. IoTCentral](#microsoftiotcentral)
> - [Micro soft. IoTSpaces](#microsoftiotspaces)
> - [Microsoft.KeyVault](#microsoftkeyvault)
> - [Microsoft.Kubernetes](#microsoftkubernetes)
> - [Micro soft. KubernetesConfiguration](#microsoftkubernetesconfiguration)
> - [Microsoft.Kusto](#microsoftkusto)
> - [Micro soft. LabServices](#microsoftlabservices)
> - [Micro soft. LocationBasedServices](#microsoftlocationbasedservices)
> - [Micro soft. bestand locationservices](#microsoftlocationservices)
> - [Microsoft.Logic](#microsoftlogic)
> - [Microsoft.MachineLearning](#microsoftmachinelearning)
> - [Micro soft. MachineLearningCompute](#microsoftmachinelearningcompute)
> - [Micro soft. MachineLearningExperimentation](#microsoftmachinelearningexperimentation)
> - [Micro soft. MachineLearningModelManagement](#microsoftmachinelearningmodelmanagement)
> - [Microsoft.MachineLearningServices](#microsoftmachinelearningservices)
> - [Micro soft. onderhoud](#microsoftmaintenance)
> - [Micro soft. ManagedIdentity](#microsoftmanagedidentity)
> - [Micro soft. ManagedNetwork](#microsoftmanagednetwork)
> - [Micro soft. ManagedServices](#microsoftmanagedservices)
> - [Micro soft. Management](#microsoftmanagement)
> - [Micro soft. Maps](#microsoftmaps)
> - [Micro soft. Marketplace](#microsoftmarketplace)
> - [Micro soft. MarketplaceApps](#microsoftmarketplaceapps)
> - [Microsoft.MarketplaceOrdering](#microsoftmarketplaceordering)
> - [Microsoft.Media](#microsoftmedia)
> - [Micro soft. Microservices4Spring](#microsoftmicroservices4spring)
> - [Micro soft. migrate](#microsoftmigrate)
> - [Micro soft. MixedReality](#microsoftmixedreality)
> - [Micro soft. NetApp](#microsoftnetapp)
> - [Microsoft.Network](#microsoftnetwork)
> - [Microsoft.NotificationHubs](#microsoftnotificationhubs)
> - [Micro soft. ObjectStore](#microsoftobjectstore)
> - [Micro soft. OffAzure](#microsoftoffazure)
> - [Microsoft.OperationalInsights](#microsoftoperationalinsights)
> - [Microsoft.OperationsManagement](#microsoftoperationsmanagement)
> - [Micro soft. peering](#microsoftpeering)
> - [Microsoft.PolicyInsights](#microsoftpolicyinsights)
> - [Micro soft. Portal](#microsoftportal)
> - [Micro soft. PowerBI](#microsoftpowerbi)
> - [Micro soft. PowerBIDedicated](#microsoftpowerbidedicated)
> - [Micro soft. ProjectBabylon](#microsoftprojectbabylon)
> - [Micro soft. ProviderHub](#microsoftproviderhub)
> - [Micro soft. Quantum](#microsoftquantum)
> - [Microsoft.RecoveryServices](#microsoftrecoveryservices)
> - [Micro soft. RedHatOpenShift](#microsoftredhatopenshift)
> - [Microsoft.Relay](#microsoftrelay)
> - [Micro soft. ResourceGraph](#microsoftresourcegraph)
> - [Microsoft.ResourceHealth](#microsoftresourcehealth)
> - [Microsoft.Resources](#microsoftresources)
> - [Micro soft. SaaS](#microsoftsaas)
> - [Microsoft.Search](#microsoftsearch)
> - [Microsoft.Security](#microsoftsecurity)
> - [Micro soft. SecurityInsights](#microsoftsecurityinsights)
> - [Micro soft. SerialConsole](#microsoftserialconsole)
> - [Microsoft.ServerManagement](#microsoftservermanagement)
> - [Microsoft.ServiceBus](#microsoftservicebus)
> - [Micro soft. ServiceFabric](#microsoftservicefabric)
> - [Micro soft. ServiceFabricMesh](#microsoftservicefabricmesh)
> - [Micro soft. Services](#microsoftservices)
> - [Micro soft. SignalRService](#microsoftsignalrservice)
> - [Micro soft. SoftwarePlan](#microsoftsoftwareplan)
> - [Micro soft. Solutions](#microsoftsolutions)
> - [Microsoft.Sql](#microsoftsql)
> - [Micro soft. SqlVirtualMachine](#microsoftsqlvirtualmachine)
> - [Micro soft. Storage](#microsoftstorage)
> - [Micro soft. StorageCache](#microsoftstoragecache)
> - [Micro soft. StorageSync](#microsoftstoragesync)
> - [Micro soft. StorageSyncDev](#microsoftstoragesyncdev)
> - [Micro soft. StorageSyncInt](#microsoftstoragesyncint)
> - [Microsoft.StorSimple](#microsoftstorsimple)
> - [Microsoft.StreamAnalytics](#microsoftstreamanalytics)
> - [Micro soft. StreamAnalyticsExplorer](#microsoftstreamanalyticsexplorer)
> - [Micro soft. Subscription](#microsoftsubscription)
> - [micro soft. ondersteuning](#microsoftsupport)
> - [Micro soft. Synapse](#microsoftsynapse)
> - [Micro soft. TimeSeriesInsights](#microsofttimeseriesinsights)
> - [Micro soft. token](#microsofttoken)
> - [Microsoft.VirtualMachineImages](#microsoftvirtualmachineimages)
> - [micro soft. Visual Studio](#microsoftvisualstudio)
> - [Micro soft. VMware](#microsoftvmware)
> - [Micro soft. VMwareCloudSimple](#microsoftvmwarecloudsimple)
> - [Micro soft. VnfManager](#microsoftvnfmanager)
> - [Micro soft. VSOnline](#microsoftvsonline)
> - [Microsoft.Web](#microsoftweb)
> - [Micro soft. WindowsESU](#microsoftwindowsesu)
> - [Micro soft. WindowsIoT](#microsoftwindowsiot)
> - [Micro soft. WorkloadBuilder](#microsoftworkloadbuilder)
> - [Micro soft. WorkloadMonitor](#microsoftworkloadmonitor)

## <a name="microsoftaad"></a>Micro soft. AAD

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | domainservices | Nee | Nee |

## <a name="microsoftaadiam"></a>micro soft. aadiam

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | diagnosticsettings | Nee | Nee |
> | diagnosticsettingscategories | Nee | Nee |
> | privatelinkforazuread | Ja | Ja |
> | tenants | Ja | Ja |

## <a name="microsoftaddons"></a>Micro soft. Addons

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | supportproviders | Nee | Nee |

## <a name="microsoftadhybridhealthservice"></a>Microsoft.ADHybridHealthService

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | aadsupportcases | Nee | Nee |
> | addsservices | Nee | Nee |
> | middelen | Nee | Nee |
> | anonymousapiusers | Nee | Nee |
> | configuratie | Nee | Nee |
> | logboeken | Nee | Nee |
> | rapporten | Nee | Nee |
> | servicehealthmetrics | Nee | Nee |
> | services | Nee | Nee |

## <a name="microsoftadvisor"></a>Microsoft.Advisor

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | configuraties | Nee | Nee |
> | generaterecommendations | Nee | Nee |
> | metagegevens | Nee | Nee |
> | aanbevelingen | Nee | Nee |
> | onderdrukkingen | Nee | Nee |

## <a name="microsoftalertsmanagement"></a>Micro soft. AlertsManagement

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | actionrules | Ja | Ja |
> | waarschuwingen | Nee | Nee |
> | alertslist | Nee | Nee |
> | alertsmetadata | Nee | Nee |
> | alertssummary | Nee | Nee |
> | alertssummarylist | Nee | Nee |
> | smartdetectoralertrules | Ja | Ja |
> | smartgroups | Nee | Nee |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | Server | Ja | Ja |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!IMPORTANT]
> Een API Management-service die is ingesteld op de verbruiks-SKU, kan niet worden verplaatst.

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | reportfeedback | Nee | Nee |
> | service | Ja | Ja |

## <a name="microsoftappconfiguration"></a>Micro soft. AppConfiguration

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | configurationstores | Ja | Ja |
> | configurationstores / eventgridfilters | Nee | Nee |

## <a name="microsoftappplatform"></a>Micro soft. AppPlatform

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | lente | Ja | Ja |

## <a name="microsoftappservice"></a>Micro soft. AppService

> [!IMPORTANT]
> Zie [app service richt lijnen voor verplaatsen](./move-limitations/app-service-move-limitations.md).

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | apiapps | Nee | Nee |
> | appidentities | Nee | Nee |
> | gateways | Nee | Nee |

## <a name="microsoftattestation"></a>Micro soft. Attestation

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | attestationproviders | Ja | Ja |

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | classicadministrators | Nee | Nee |
> | dataaliases | Nee | Nee |
> | denyassignments | Nee | Nee |
> | elevateaccess | Nee | Nee |
> | findorphanroleassignments | Nee | Nee |
> | vergren delingen | Nee | Nee |
> | permissions | Nee | Nee |
> | policyassignments | Nee | Nee |
> | policydefinitions | Nee | Nee |
> | policysetdefinitions | Nee | Nee |
> | privatelinkassociations | Nee | Nee |
> | resourcemanagementprivatelinks | Nee | Nee |
> | roleassignments | Nee | Nee |
> | roleassignmentsusagemetrics | Nee | Nee |
> | roledefinitions | Nee | Nee |

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!IMPORTANT]
> Runbooks moeten zich in dezelfde resource groep bevinden als het Automation-account.
>
> Zie [uw Azure Automation-account naar een ander abonnement verplaatsen](../../automation/how-to/move-account.md?toc=/azure/azure-resource-manager/toc.json)voor meer informatie.

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | automationaccounts | Ja | Ja |
> | automationaccounts/configuraties | Ja | Ja |
> | automationaccounts/runbooks | Ja | Ja |

## <a name="microsoftavs"></a>Micro soft. AVS

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | privateclouds | Ja | Ja |

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | b2cdirectories | Ja | Ja |
> | b2ctenants | Nee | Nee |

## <a name="microsoftazuredata"></a>Micro soft. Azureworden

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | datacontrollers | Nee | Nee |
> | hybriddatamanagers | Nee | Nee |
> | postgresinstances | Nee | Nee |
> | sqlinstances | Nee | Nee |
> | sqlmanagedinstances | Nee | Nee |
> | sqlserverinstances | Nee | Nee |
> | sqlserverregistrations | Ja | Ja |

## <a name="microsoftazurestack"></a>Micro soft. AzureStack

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | cloudmanifestfiles | Nee | Nee |
> | registraties | Ja | Ja |

## <a name="microsoftazurestackhci"></a>Micro soft. AzureStackHCI

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | clusters | Nee | Nee |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | batchaccounts | Ja | Ja |

## <a name="microsoftbilling"></a>Microsoft.Billing

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | billingaccounts | Nee | Nee |
> | billingperiods | Nee | Nee |
> | billingpermissions | Nee | Nee |
> | billingproperty | Nee | Nee |
> | billingroleassignments | Nee | Nee |
> | billingroledefinitions | Nee | Nee |
> | afdeling | Nee | Nee |
> | enrollmentaccounts | Nee | Nee |
> | factureer | Nee | Nee |
> | Making | Nee | Nee |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | mapapis | Nee | Nee |

## <a name="microsoftbiztalkservices"></a>Micro soft. BizTalkServices

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | biztalk | Nee | Nee |

## <a name="microsoftblockchain"></a>Micro soft. Block Chain

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | blockchainmembers | Nee | Nee |
> | cordamembers | Nee | Nee |
> | Volg | Nee | Nee |

## <a name="microsoftblockchaintokens"></a>Micro soft. BlockchainTokens

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | tokenservices | Nee | Nee |

## <a name="microsoftblueprint"></a>Micro soft. blauw druk

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | blueprintassignments | Nee | Nee |
> | blauw drukken | Nee | Nee |

## <a name="microsoftbotservice"></a>Micro soft. BotService

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | botservices | Ja | Ja |

## <a name="microsoftcache"></a>Microsoft.Cache

> [!IMPORTANT]
> Als de Azure-cache voor redis-exemplaar is geconfigureerd met een virtueel netwerk, kan het exemplaar niet worden verplaatst naar een ander abonnement. Zie [beperkingen](./move-limitations/networking-move-limitations.md)voor het verplaatsen van netwerken.

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | redis | Ja | Ja |
> | redisenterprise | Nee | Nee |

## <a name="microsoftcapacity"></a>Micro soft. capacity

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | appliedreservations | Nee | Nee |
> | calculateexchange | Nee | Nee |
> | calculateprice | Nee | Nee |
> | calculatepurchaseprice | Nee | Nee |
> | catalogi | Nee | Nee |
> | commercialreservationorders | Nee | Nee |
> | Exchange | Nee | Nee |
> | reservationorders | Nee | Nee |
> | ringen | Nee | Nee |
> | resources | Nee | Nee |
> | validatereservationorder | Nee | Nee |

## <a name="microsoftcdn"></a>Micro soft. CDN

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | cdnwebapplicationfirewallmanagedrulesets | Nee | Nee |
> | cdnwebapplicationfirewallpolicies | Ja | Ja |
> | edgenodes | Nee | Nee |
> | profielen | Ja | Ja |
> | profielen/eind punten | Ja | Ja |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!IMPORTANT]
> Zie [app service richt lijnen voor verplaatsen](./move-limitations/app-service-move-limitations.md).

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | certificateorders | Ja | Ja |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!IMPORTANT]
> Zie [richt lijnen](./move-limitations/classic-model-move-limitations.md)voor het verplaatsen van de klassieke implementatie. Klassieke implementatie resources kunnen worden verplaatst tussen abonnementen met een bewerking die specifiek is voor dat scenario.

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | mogelijkheden | Nee | Nee |
> | domein naam | Ja | Nee |
> | quotas | Nee | Nee |
> | resourcetypes | Nee | Nee |
> | validatesubscriptionmoveavailability | Nee | Nee |
> | informatie | Ja | Ja |

## <a name="microsoftclassicinfrastructuremigrate"></a>Micro soft. ClassicInfrastructureMigrate

> [!IMPORTANT]
> Zie [richt lijnen](./move-limitations/classic-model-move-limitations.md)voor het verplaatsen van de klassieke implementatie. Klassieke implementatie resources kunnen worden verplaatst tussen abonnementen met een bewerking die specifiek is voor dat scenario.

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | classicinfrastructureresources | Nee | Nee |

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

> [!IMPORTANT]
> Zie [richt lijnen](./move-limitations/classic-model-move-limitations.md)voor het verplaatsen van de klassieke implementatie. Klassieke implementatie resources kunnen worden verplaatst tussen abonnementen met een bewerking die specifiek is voor dat scenario.

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | mogelijkheden | Nee | Nee |
> | expressroutecrossconnections | Nee | Nee |
> | expressroutecrossconnections/peerings | Nee | Nee |
> | gatewaysupporteddevices | Nee | Nee |
> | networksecuritygroups | Nee | Nee |
> | quotas | Nee | Nee |
> | reservedips | Nee | Nee |
> | virtualnetworks | Nee | Nee |

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!IMPORTANT]
> Zie [richt lijnen](./move-limitations/classic-model-move-limitations.md)voor het verplaatsen van de klassieke implementatie. Klassieke implementatie resources kunnen worden verplaatst tussen abonnementen met een bewerking die specifiek is voor dat scenario.

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | cd's | Nee | Nee |
> | images | Nee | Nee |
> | osimages | Nee | Nee |
> | osplatformimages | Nee | Nee |
> | publicimages | Nee | Nee |
> | quotas | Nee | Nee |
> | Storage accounts | Ja | Nee |
> | vmimages | Nee | Nee |

## <a name="microsoftclassicsubscription"></a>Micro soft. ClassicSubscription

> [!IMPORTANT]
> Zie [richt lijnen](./move-limitations/classic-model-move-limitations.md)voor het verplaatsen van de klassieke implementatie. Klassieke implementatie resources kunnen worden verplaatst tussen abonnementen met een bewerking die specifiek is voor dat scenario.

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | bewerkingen | Nee | Nee |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | accounts | Ja | Ja |

## <a name="microsoftcommerce"></a>Microsoft.Commerce

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | ratecard | Nee | Nee |
> | usageaggregates | Nee | Nee |

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!IMPORTANT]
> Zie [virtual machines richt lijnen voor verplaatsen](./move-limitations/virtual-machines-move-limitations.md).

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | Availability sets | Ja | Ja |
> | diskaccesses | Nee | Nee |
> | diskencryptionsets | Nee | Nee |
> | cd's | Ja | Ja |
> | Galerij | Nee | Nee |
> | galerieën/afbeeldingen | Nee | Nee |
> | galerieën/afbeeldingen/versies | Nee | Nee |
> | hostgroups | Nee | Nee |
> | hostgroups/hosts | Nee | Nee |
> | images | Ja | Ja |
> | proximityplacementgroups | Ja | Ja |
> | restorepointcollections | Nee | Nee |
> | restorepointcollections / restorepoints | Nee | Nee |
> | sharedvmextensions | Nee | Nee |
> | sharedvmimages | Nee | Nee |
> | sharedvmimages/versies | Nee | Nee |
> | momentopnamen | Ja | Ja |
> | sshpublickeys | Nee | Nee |
> | informatie | Ja | Ja |
> | informatie/extensies | Ja | Ja |
> | virtualmachinescalesets | Ja | Ja |

## <a name="microsoftconsumption"></a>Micro soft. verbruik

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | aggregatedcost | Nee | Nee |
> | saldi | Nee | Nee |
> | budgetten | Nee | Nee |
> | belastingen | Nee | Nee |
> | costtags | Nee | Nee |
> | aanvragen | Nee | Nee |
> | events | Nee | Nee |
> | Forecast | Nee | Nee |
> | extra | Nee | Nee |
> | markt plaatsen | Nee | Nee |
> | pricesheets | Nee | Nee |
> | producten | Nee | Nee |
> | reservationdetails | Nee | Nee |
> | reservationrecommendationdetails | Nee | Nee |
> | reservationrecommendations | Nee | Nee |
> | reservationsummaries | Nee | Nee |
> | reservationtransactions | Nee | Nee |
> | tags | Nee | Nee |
> | tenants | Nee | Nee |
> | inzake | Nee | Nee |
> | usagedetails | Nee | Nee |

## <a name="microsoftcontainerinstance"></a>Micro soft. ContainerInstance

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | containergroups | Nee | Nee |
> | serviceassociationlinks | Nee | Nee |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | registers | Ja | Ja |
> | registers/agentpools | Ja | Ja |
> | registers/buildtasks | Ja | Ja |
> | registers/replicaties | Ja | Ja |
> | registers/taken | Ja | Ja |
> | registers/webhooks | Ja | Ja |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | containerservices | Nee | Nee |
> | managedclusters | Nee | Nee |
> | openshiftmanagedclusters | Nee | Nee |

## <a name="microsoftcontentmoderator"></a>Microsoft.ContentModerator

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | toepassingen | Nee | Nee |

## <a name="microsoftcortanaanalytics"></a>Micro soft. CortanaAnalytics

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | accounts | Nee | Nee |

## <a name="microsoftcostmanagement"></a>Micro soft. CostManagement

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | waarschuwingen | Nee | Nee |
> | billingaccounts | Nee | Nee |
> | budgetten | Nee | Nee |
> | cloudconnectors | Nee | Nee |
> | connectoren | Ja | Ja |
> | afdeling | Nee | Nee |
> | hoogte | Nee | Nee |
> | enrollmentaccounts | Nee | Nee |
> | dump | Nee | Nee |
> | externalbillingaccounts | Nee | Nee |
> | forecast | Nee | Nee |
> | query | Nee | Nee |
> | registreren | Nee | Nee |
> | reportconfigs | Nee | Nee |
> | rapporten | Nee | Nee |
> | instellingen | Nee | Nee |
> | showbackrules | Nee | Nee |
> | Weergaven | Nee | Nee |

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | hubs | Nee | Nee |

## <a name="microsoftcustomerlockbox"></a>Micro soft. CustomerLockbox

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | requests | Nee | Nee |

## <a name="microsoftcustomproviders"></a>Micro soft. CustomProviders

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | lidkoppelingen | Nee | Nee |
> | resourceproviders | Ja | Ja |

## <a name="microsoftdatabox"></a>Micro soft. DataBox

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | functies | Nee | Nee |

## <a name="microsoftdataboxedge"></a>Micro soft. DataBoxEdge

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | availableskus | Nee | Nee |
> | databoxedgedevices | Ja | Ja |

## <a name="microsoftdatabricks"></a>Micro soft. Databricks

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | workspaces | Nee | Nee |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | catalogi | Ja | Ja |
> | datacatalogs | Nee | Nee |

## <a name="microsoftdataconnect"></a>Micro soft. DataConnect

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | connectionmanagers | Nee | Nee |

## <a name="microsoftdataexchange"></a>Micro soft. DataExchange

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | Pakketten | Nee | Nee |
> | plant | Nee | Nee |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | datafactories | Ja | Ja |
> | factory's | Ja | Ja |

## <a name="microsoftdatalake"></a>Micro soft. DataLake

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | datalakeaccounts | Nee | Nee |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | accounts | Ja | Ja |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | accounts | Ja | Ja |

## <a name="microsoftdatamigration"></a>Micro soft. DataMigration

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | services | Nee | Nee |
> | Services/projecten | Nee | Nee |
> | sleuf | Nee | Nee |

## <a name="microsoftdataprotection"></a>Micro soft. DataProtection

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | backupvaults | Nee | Nee |

## <a name="microsoftdatashare"></a>Micro soft. DataShare

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | accounts | Ja | Ja |

## <a name="microsoftdbformariadb"></a>Micro soft. DBforMariaDB

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | Server | Ja | Ja |

## <a name="microsoftdbformysql"></a>Micro soft. DBforMySQL

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | flexibleServers | Ja | Ja |
> | Server | Ja | Ja |

## <a name="microsoftdbforpostgresql"></a>Micro soft. DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | flexibleServers | Ja | Ja |
> | servergroups | Nee | Nee |
> | Server | Ja | Ja |
> | serversv2 | Ja | Ja |
> | singleservers | Ja | Ja |

## <a name="microsoftdeploymentmanager"></a>Micro soft. DeploymentManager

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | artifactsources | Ja | Ja |
> | implementaties | Ja | Ja |
> | servicetopologies | Ja | Ja |
> | servicetopologies/Services | Ja | Ja |
> | servicetopologies/Services/serviceunits | Ja | Ja |
> | stappen | Ja | Ja |

## <a name="microsoftdesktopvirtualization"></a>Micro soft. DesktopVirtualization

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | applicationgroups | Ja | Ja |
> | hostpools | Ja | Ja |
> | workspaces | Ja | Ja |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | elasticpools | Nee | Nee |
> | elasticpools / iothubtenants | Nee | Nee |
> | iothubs | Ja | Ja |
> | provisioningservices | Ja | Ja |

## <a name="microsoftdevops"></a>Micro soft. DevOps

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | pijp lijnen | Ja | Ja |

## <a name="microsoftdevspaces"></a>Micro soft. DevSpaces

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | fungeren | Ja | Ja |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | labcenters | Nee | Nee |
> | Labs | Ja | Nee |
> | Labs/omgevingen | Ja | Ja |
> | Labs-servicerunners | Ja | Ja |
> | Labs-informatie | Ja | Nee |
> | schema's | Ja | Ja |

## <a name="microsoftdigitaltwins"></a>Micro soft. DigitalTwins

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | digitaltwinsinstances | Nee | Nee |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | databaseaccountnames | Nee | Nee |
> | databaseaccounts | Ja | Ja |

## <a name="microsoftdomainregistration"></a>Micro soft. DomainRegistration

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | domeinen | Ja | Ja |
> | generatessorequest | Nee | Nee |
> | topleveldomains | Nee | Nee |
> | validatedomainregistrationinformation | Nee | Nee |

## <a name="microsoftenterpriseknowledgegraph"></a>Micro soft. EnterpriseKnowledgeGraph

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | services | Ja | Ja |

## <a name="microsofteventgrid"></a>Micro soft. EventGrid

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | domeinen | Ja | Ja |
> | eventsubscriptions | Nee, kan niet onafhankelijk worden verplaatst, maar automatisch verplaatst met een geabonneerde resource. | Nee, kan niet onafhankelijk worden verplaatst, maar automatisch verplaatst met een geabonneerde resource. |
> | extensiontopics | Nee | Nee |
> | partnernamespaces | Ja | Ja |
> | partnerregistrations | Nee | Nee |
> | partnertopics | Ja | Ja |
> | systemtopics | Ja | Ja |
> | onderwerp | Ja | Ja |
> | topictypes | Nee | Nee |

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | clusters | Ja | Ja |
> | naam ruimten | Ja | Ja |
> | sku | Nee | Nee |

## <a name="microsoftexperimentation"></a>Micro soft. experimenten

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | experimentworkspaces | Nee | Nee |

## <a name="microsoftfalcon"></a>Micro soft. Falcon

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | naam ruimten | Ja | Ja |

## <a name="microsoftfeatures"></a>Microsoft.Features

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | featureproviders | Nee | Nee |
> | features | Nee | Nee |
> | providers | Nee | Nee |
> | subscriptionfeatureregistrations | Nee | Nee |

## <a name="microsoftgenomics"></a>Micro soft. Genomics

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | accounts | Nee | Nee |

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | automanagedaccounts | Nee | Nee |
> | automanagedvmconfigurationprofiles | Nee | Nee |
> | guestconfigurationassignments | Nee | Nee |
> | software | Nee | Nee |
> | softwareupdateprofile | Nee | Nee |
> | softwareupdates | Nee | Nee |

## <a name="microsofthanaonazure"></a>Micro soft. HanaOnAzure

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | hanainstances | Nee | Nee |
> | sapmonitors | Nee | Nee |

## <a name="microsofthardwaresecuritymodules"></a>Micro soft. HardwareSecurityModules

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | dedicatedhsms | Nee | Nee |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!IMPORTANT]
> U kunt HDInsight-clusters verplaatsen naar een nieuw abonnement of een nieuwe resource groep. U kunt echter niet overstappen op de netwerk resources die zijn gekoppeld aan het HDInsight-cluster (zoals het virtuele netwerk, de NIC of de load balancer). Daarnaast kunt u niet verplaatsen naar een nieuwe resource groep een NIC die is gekoppeld aan een virtuele machine voor het cluster.
>
> Wanneer u een HDInsight-cluster naar een nieuw abonnement verplaatst, moet u eerst andere resources (zoals het opslag account) verplaatsen. Verplaats vervolgens het HDInsight-cluster zelf.

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | clusters | Ja | Ja |

## <a name="microsofthealthcareapis"></a>Micro soft. HealthcareApis

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | services | Ja | Ja |

## <a name="microsofthybridcompute"></a>Microsoft.HybridCompute

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | apparaten | Ja | Ja |
> | computers/uitbrei dingen | Ja | Ja |

## <a name="microsofthybriddata"></a>Micro soft. HybridData

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | datamanagers | Ja | Ja |

## <a name="microsofthybridnetwork"></a>Micro soft. HybridNetwork

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | devices | Nee | Nee |
> | vnfs | Nee | Nee |

## <a name="microsofthydra"></a>Micro soft. Hydra

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | materialen | Nee | Nee |
> | networkscopes | Nee | Nee |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | functies | Ja | Ja |

## <a name="microsoftinsights"></a>micro soft. Insights

> [!IMPORTANT]
> Zorg ervoor dat het verplaatsen naar een nieuw abonnement geen [abonnements quota's](azure-subscription-service-limits.md#azure-monitor-limits)overschrijdt.

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | actiongroups | Ja | Ja |
> | activitylogalerts | Nee | Nee |
> | alertrules | Ja | Ja |
> | autoscalesettings | Ja | Ja |
> | basislijn | Nee | Nee |
> | materialen | Ja | Ja |
> | datacollectionrules | Nee | Nee |
> | diagnosticsettings | Nee | Nee |
> | diagnosticsettingscategories | Nee | Nee |
> | eventcategories | Nee | Nee |
> | eventtypes | Nee | Nee |
> | extendeddiagnosticsettings | Nee | Nee |
> | guestdiagnosticsettings | Nee | Nee |
> | listmigrationdate | Nee | Nee |
> | logdefinitions | Nee | Nee |
> | logprofiles | Nee | Nee |
> | logboeken | Nee | Nee |
> | metricalerts | Nee | Nee |
> | metricbaselines | Nee | Nee |
> | metricbatch | Nee | Nee |
> | metricdefinitions | Nee | Nee |
> | metricnamespaces | Nee | Nee |
> | metrics | Nee | Nee |
> | migratealertrules | Nee | Nee |
> | migratetonewpricingmodel | Nee | Nee |
> | myworkbooks | Nee | Nee |
> | notificationgroups | Nee | Nee |
> | privatelinkscopes | Nee | Nee |
> | rollbacktolegacypricingmodel | Nee | Nee |
> | scheduledqueryrules | Ja | Ja |
> | topologie | Nee | Nee |
> | transacties | Nee | Nee |
> | vminsightsonboardingstatuses | Nee | Nee |
> | webtests | Ja | Ja |
> | webtesten/gettestresultfile | Nee | Nee |
> | werkmappen | Ja | Ja |
> | workbooktemplates | Ja | Ja |

## <a name="microsoftiotcentral"></a>Micro soft. IoTCentral

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | apptemplates | Nee | Nee |
> | iotapps | Ja | Ja |

## <a name="microsoftiotspaces"></a>Micro soft. IoTSpaces

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | Graph | Ja | Ja |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!IMPORTANT]
> Sleutel kluizen die worden gebruikt voor schijf versleuteling, kunnen niet worden verplaatst naar een resource groep in hetzelfde abonnement of tussen abonnementen.

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | deletedvaults | Nee | Nee |
> | hsmpools | Nee | Nee |
> | managedhsms | Nee | Nee |
> | kluizen | Ja | Ja |

## <a name="microsoftkubernetes"></a>Microsoft.Kubernetes

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | connectedclusters | Ja | Ja |
> | registeredsubscriptions | Nee | Nee |

## <a name="microsoftkubernetesconfiguration"></a>Micro soft. KubernetesConfiguration

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | sourcecontrolconfigurations | Nee | Nee |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | clusters | Ja | Ja |

## <a name="microsoftlabservices"></a>Micro soft. LabServices

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | labaccounts | Nee | Nee |
> | gebruikers | Nee | Nee |

## <a name="microsoftlocationbasedservices"></a>Micro soft. LocationBasedServices

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | accounts | Nee | Nee |

## <a name="microsoftlocationservices"></a>Micro soft. bestand locationservices

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | accounts | Nee | Nee |

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | hostingenvironments | Nee | Nee |
> | integrationaccounts | Ja | Ja |
> | integrationserviceenvironments | Ja | Nee |
> | integrationserviceenvironments/beheerdeapi's | Ja | Nee |
> | isolatedenvironments | Nee | Nee |
> | stroom | Ja | Ja |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | commitmentplans | Nee | Nee |
> | webservices | Ja | Nee |
> | workspaces | Ja | Ja |

## <a name="microsoftmachinelearningcompute"></a>Micro soft. MachineLearningCompute

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | operationalizationclusters | Nee | Nee |

## <a name="microsoftmachinelearningexperimentation"></a>Micro soft. MachineLearningExperimentation

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | accounts | Nee | Nee |
> | teamaccounts | Nee | Nee |

## <a name="microsoftmachinelearningmodelmanagement"></a>Micro soft. MachineLearningModelManagement

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | accounts | Nee | Nee |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | workspaces | Nee | Nee |

## <a name="microsoftmaintenance"></a>Micro soft. onderhoud

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | configurationassignments | Nee | Nee |
> | maintenanceconfigurations | Ja | Ja |
> | updates | Nee | Nee |

## <a name="microsoftmanagedidentity"></a>Micro soft. ManagedIdentity

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | identiteit | Nee | Nee |
> | userassignedidentities | Nee | Nee |

## <a name="microsoftmanagednetwork"></a>Micro soft. ManagedNetwork

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | managednetworks | Nee | Nee |
> | managednetworks / managednetworkgroups | Nee | Nee |
> | managednetworks / managednetworkpeeringpolicies | Nee | Nee |
> | melding | Nee | Nee |

## <a name="microsoftmanagedservices"></a>Micro soft. ManagedServices

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | marketplaceregistrationdefinitions | Nee | Nee |
> | registrationassignments | Nee | Nee |
> | registrationdefinitions | Nee | Nee |

## <a name="microsoftmanagement"></a>Micro soft. Management

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | getentities | Nee | Nee |
> | managementgroups | Nee | Nee |
> | managementgroups/instellingen | Nee | Nee |
> | resources | Nee | Nee |
> | starttenantbackfill | Nee | Nee |
> | tenantbackfillstatus | Nee | Nee |

## <a name="microsoftmaps"></a>Micro soft. Maps

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | accounts | Ja | Ja |
> | accounts/privateatlases | Ja | Ja |

## <a name="microsoftmarketplace"></a>Micro soft. Marketplace

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | over | Nee | Nee |
> | offertypes | Nee | Nee |
> | privategalleryitems | Nee | Nee |
> | privatestoreclient | Nee | Nee |
> | privatestores | Nee | Nee |
> | producten | Nee | Nee |
> | uitgevers | Nee | Nee |
> | registreren | Nee | Nee |

## <a name="microsoftmarketplaceapps"></a>Micro soft. MarketplaceApps

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | classicdevservices | Nee | Nee |

## <a name="microsoftmarketplaceordering"></a>Microsoft.MarketplaceOrdering

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | gesloten | Nee | Nee |
> | offertypes | Nee | Nee |

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | Media Services | Ja | Ja |
> | Media Services/liveevents | Ja | Ja |
> | Media Services/streamingendpoints | Ja | Ja |

## <a name="microsoftmicroservices4spring"></a>Micro soft. Microservices4Spring

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | appclusters | Nee | Nee |

## <a name="microsoftmigrate"></a>Micro soft. migrate

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | assessmentprojects | Nee | Nee |
> | migrateprojects | Nee | Nee |
> | movecollections | Nee | Nee |
> | projecten | Nee | Nee |

## <a name="microsoftmixedreality"></a>Micro soft. MixedReality

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | holographicsbroadcastaccounts | Nee | Nee |
> | objectunderstandingaccounts | Nee | Nee |
> | remoterenderingaccounts | Ja | Ja |
> | spatialanchorsaccounts | Ja | Ja |

## <a name="microsoftnetapp"></a>Micro soft. NetApp

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | netappaccounts | Nee | Nee |
> | netappaccounts / capacitypools | Nee | Nee |
> | netappaccounts/capacitypools/volumes | Nee | Nee |

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!IMPORTANT]
> Zie [richt lijnen](./move-limitations/networking-move-limitations.md)voor het verplaatsen van netwerken.

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | applicationgateways | Nee | Nee |
> | applicationgatewaywebapplicationfirewallpolicies | Nee | Nee |
> | applicationsecuritygroups | Ja | Ja |
> | azurefirewalls | Nee | Nee |
> | bastionhosts | Nee | Nee |
> | bgpservicecommunities | Nee | Nee |
> | inbel | Ja | Ja |
> | ddoscustompolicies | Ja | Ja |
> | ddosprotectionplans | Nee | Nee |
> | dnszones | Ja | Ja |
> | expressroutecircuits | Nee | Nee |
> | expressroutegateways | Nee | Nee |
> | expressrouteserviceproviders | Nee | Nee |
> | firewallpolicies | Ja | Ja |
> | frontdoors | Nee | Nee |
> | ipallocations | Ja | Ja |
> | ipgroups | Ja | Ja |
> | loadbalancers | Ja, basis-SKU<br> Ja, standaard-SKU | Ja, basis-SKU<br>Geen standaard-SKU |
> | localnetworkgateways | Ja | Ja |
> | natgateways | Nee | Nee |
> | networkexperimentprofiles | Nee | Nee |
> | networkintentpolicies | Ja | Ja |
> | networkinterfaces | Ja | Ja |
> | networkprofiles | Nee | Nee |
> | networksecuritygroups | Ja | Ja |
> | networkwatchers | Ja | Nee |
> | networkwatchers / connectionmonitors | Ja | Nee |
> | networkwatchers / flowlogs | Ja | Nee |
> | networkwatchers / pingmeshes | Ja | Nee |
> | p2svpngateways | Nee | Nee |
> | privatednszones | Ja | Ja |
> | privatednszones / virtualnetworklinks | Ja | Ja |
> | privatednszonesinternal | Nee | Nee |
> | privateendpointredirectmaps | Nee | Nee |
> | privateendpoints | Nee | Nee |
> | privatelinkservices | Nee | Nee |
> | publicipaddresses | Ja, basis-SKU<br>Ja, standaard-SKU | Ja, basis-SKU<br>Geen standaard-SKU |
> | publicipprefixes | Ja | Ja |
> | routefilters | Nee | Nee |
> | routetables | Ja | Ja |
> | securitypartnerproviders | Ja | Ja |
> | serviceendpointpolicies | Ja | Ja |
> | trafficmanagergeographichierarchies | Nee | Nee |
> | trafficmanagerprofiles | Ja | Ja |
> | trafficmanagerprofiles/Heatmaps | Nee | Nee |
> | trafficmanagerusermetricskeys | Nee | Nee |
> | virtualhubs | Nee | Nee |
> | virtualnetworkgateways | Ja | Ja |
> | virtualnetworks | Ja | Ja |
> | virtualnetworktaps | Nee | Nee |
> | virtualrouters | Ja | Ja |
> | virtualwans | Nee | Nee |
> | vpngateways (virtueel WAN) | Nee | Nee |
> | vpnserverconfigurations | Nee | Nee |
> | vpnsites (virtueel WAN) | Nee | Nee |

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | naam ruimten | Ja | Ja |
> | naam ruimten/notification hubs | Ja | Ja |

## <a name="microsoftobjectstore"></a>Micro soft. ObjectStore

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | osnamespaces | Ja | Ja |

## <a name="microsoftoffazure"></a>Micro soft. OffAzure

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | hypervsites | Nee | Nee |
> | importsites | Nee | Nee |
> | serversites | Nee | Nee |
> | vmwaresites | Nee | Nee |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!IMPORTANT]
> Zorg ervoor dat het verplaatsen naar een nieuw abonnement geen [abonnements quota's](azure-subscription-service-limits.md#azure-monitor-limits)overschrijdt.
>
> U kunt geen werk ruimten verplaatsen die een gekoppeld Automation-account hebben. Zorg ervoor dat u alle Automation-accounts ontkoppelt voordat u begint met een Verplaats bewerking.

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | clusters | Nee | Nee |
> | deletedworkspaces | Nee | Nee |
> | linktargets | Nee | Nee |
> | storageinsightconfigs | Nee | Nee |
> | workspaces | Ja | Ja |

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | managementassociations | Nee | Nee |
> | managementconfigurations | Ja | Ja |
> | oplossingen | Ja | Ja |
> | Weergaven | Ja | Ja |

## <a name="microsoftpeering"></a>Micro soft. peering

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | legacypeerings | Nee | Nee |
> | peerasns | Nee | Nee |
> | peeringlocations | Nee | Nee |
> | Peerings | Nee | Nee |
> | peeringservicecountries | Nee | Nee |
> | peeringservicelocations | Nee | Nee |
> | peeringserviceproviders | Nee | Nee |
> | peeringservices | Nee | Nee |

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | policyevents | Nee | Nee |
> | policystates | Nee | Nee |
> | policytrackedresources | Nee | Nee |
> | herstel | Nee | Nee |

## <a name="microsoftportal"></a>Micro soft. Portal

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> |  -consoles | Nee | Nee |
> | dashboards | Ja | Ja |
> | usersettings | Nee | Nee |

## <a name="microsoftpowerbi"></a>Micro soft. PowerBI

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | workspacecollections | Ja | Ja |

## <a name="microsoftpowerbidedicated"></a>Micro soft. PowerBIDedicated

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | beschikt | Ja | Ja |

## <a name="microsoftprojectbabylon"></a>Micro soft. ProjectBabylon

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | accounts | Nee | Nee |

## <a name="microsoftproviderhub"></a>Micro soft. ProviderHub

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | availableaccounts | Nee | Nee |
> | providerregistrations | Nee | Nee |
> | implementaties | Nee | Nee |

## <a name="microsoftquantum"></a>Micro soft. Quantum

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | workspaces | Nee | Nee |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!IMPORTANT]
> Zie [Recovery Services richt lijnen voor verplaatsen](../../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json).

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | replicationeligibilityresults | Nee | Nee |
> | kluizen | Ja | Ja |

## <a name="microsoftredhatopenshift"></a>Micro soft. RedHatOpenShift

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | openshiftclusters | Nee | Nee |

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | naam ruimten | Ja | Ja |

## <a name="microsoftresourcegraph"></a>Micro soft. ResourceGraph

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | aanvragen | Ja | Ja |
> | resourcechangedetails | Nee | Nee |
> | resourcechanges | Nee | Nee |
> | resources | Nee | Nee |
> | resourceshistory | Nee | Nee |
> | subscriptionsstatus | Nee | Nee |

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | childresources | Nee | Nee |
> | emergingissues | Nee | Nee |
> | events | Nee | Nee |
> | metagegevens | Nee | Nee |
> | meldingen | Nee | Nee |

## <a name="microsoftresources"></a>Microsoft.Resources

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | implementaties | Nee | Nee |
> | deploymentscripts | Nee | Nee |
> | deploymentscripts/logboeken | Nee | Nee |
> | koppelen | Nee | Nee |
> | providers | Nee | Nee |
> | ResourceGroups | Nee | Nee |
> | resources | Nee | Nee |
> | geabonneerd | Nee | Nee |
> | tags | Nee | Nee |
> | templatespecs | Nee | Nee |
> | templatespecs/versies | Nee | Nee |
> | tenants | Nee | Nee |

## <a name="microsoftsaas"></a>Micro soft. SaaS

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | toepassingen | Ja | Nee |
> | saasresources | Nee | Nee |

## <a name="microsoftsearch"></a>Microsoft.Search

> [!IMPORTANT]
> U kunt niet meerdere Zoek resources in verschillende regio's in één bewerking verplaatsen. Verplaats deze in plaats daarvan in afzonderlijke bewerkingen.

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | resourcehealthmetadata | Nee | Nee |
> | searchservices | Ja | Ja |

## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | adaptivenetworkhardenings | Nee | Nee |
> | advancedthreatprotectionsettings | Nee | Nee |
> | waarschuwingen | Nee | Nee |
> | allowedconnections | Nee | Nee |
> | applicationwhitelistings | Nee | Nee |
> | assessmentmetadata | Nee | Nee |
> | evaluaties | Nee | Nee |
> | autodismissalertsrules | Nee | Nee |
> | automatisering | Ja | Ja |
> | autoprovisioningsettings | Nee | Nee |
> | complianceresults | Nee | Nee |
> | ingebouwde strengste | Nee | Nee |
> | datacollectionagents | Nee | Nee |
> | devicesecuritygroups | Nee | Nee |
> | discoveredsecuritysolutions | Nee | Nee |
> | externalsecuritysolutions | Nee | Nee |
> | informationprotectionpolicies | Nee | Nee |
> | iotsecuritysolutions | Ja | Ja |
> | iotsecuritysolutions / analyticsmodels | Nee | Nee |
> | iotsecuritysolutions / analyticsmodels / aggregatedalerts | Nee | Nee |
> | iotsecuritysolutions / analyticsmodels / aggregatedrecommendations | Nee | Nee |
> | jitnetworkaccesspolicies | Nee | Nee |
> | policies | Nee | Nee |
> | prijzen | Nee | Nee |
> | regulatorycompliancestandards | Nee | Nee |
> | regulatorycompliancestandards / regulatorycompliancecontrols | Nee | Nee |
> | regulatorycompliancestandards / regulatorycompliancecontrols / regulatorycomplianceassessments | Nee | Nee |
> | securitycontacts | Nee | Nee |
> | securitysolutions | Nee | Nee |
> | securitysolutionsreferencedata | Nee | Nee |
> | securitystatuses | Nee | Nee |
> | securitystatusessummaries | Nee | Nee |
> | servervulnerabilityassessments | Nee | Nee |
> | instellingen | Nee | Nee |
> | subevaluaties | Nee | Nee |
> | taken | Nee | Nee |
> | topologieën | Nee | Nee |
> | workspacesettings | Nee | Nee |

## <a name="microsoftsecurityinsights"></a>Micro soft. SecurityInsights

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | aggregaties | Nee | Nee |
> | alertrules | Nee | Nee |
> | alertruletemplates | Nee | Nee |
> | automationrules | Nee | Nee |
> | bladwijzers | Nee | Nee |
> | meldingen | Nee | Nee |
> | dataconnectors | Nee | Nee |
> | entiteiten | Nee | Nee |
> | entityqueries | Nee | Nee |
> | incidenten | Nee | Nee |
> | officeconsents | Nee | Nee |
> | instellingen | Nee | Nee |
> | threatintelligence | Nee | Nee |

## <a name="microsoftserialconsole"></a>Micro soft. SerialConsole

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | consoleservices | Nee | Nee |

## <a name="microsoftservermanagement"></a>Microsoft.ServerManagement

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | gateways | Nee | Nee |
> | punt | Nee | Nee |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | naam ruimten | Ja | Ja |
> | premiummessagingregions | Nee | Nee |
> | sku | Nee | Nee |

## <a name="microsoftservicefabric"></a>Micro soft. ServiceFabric

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | toepassingen | Nee | Nee |
> | clusters | Ja | Ja |
> | containergroups | Nee | Nee |
> | containergroupsets | Nee | Nee |
> | edgeclusters | Nee | Nee |
> | managedclusters | Nee | Nee |
> | netwerken | Nee | Nee |
> | secretstores | Nee | Nee |
> | volumes | Nee | Nee |

## <a name="microsoftservicefabricmesh"></a>Micro soft. ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | toepassingen | Ja | Ja |
> | containergroups | Nee | Nee |
> | gateways | Ja | Ja |
> | netwerken | Ja | Ja |
> | geheimen | Ja | Ja |
> | volumes | Ja | Ja |

## <a name="microsoftservices"></a>Micro soft. Services

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | implementaties | Nee | Nee |

## <a name="microsoftsignalrservice"></a>Micro soft. SignalRService

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | signalr | Ja | Ja |

## <a name="microsoftsoftwareplan"></a>Micro soft. SoftwarePlan

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | hybridusebenefits | Nee | Nee |

## <a name="microsoftsolutions"></a>Micro soft. Solutions

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | applicationdefinitions | Nee | Nee |
> | toepassingen | Nee | Nee |
> | jitrequests | Nee | Nee |

## <a name="microsoftsql"></a>Microsoft.Sql

> [!IMPORTANT]
> Een Data Base en server moeten zich in dezelfde resource groep bestaan. Wanneer u een SQL-Server verplaatst, worden alle bijbehorende data bases ook verplaatst. Dit gedrag is van toepassing op Azure SQL Database en Azure Synapse Analytics-data bases.

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | instancepools | Nee | Nee |
> | locaties | Ja | Ja |
> | managedinstances | Nee | Nee |
> | Server | Ja | Ja |
> | servers/data bases | Ja | Ja |
> | servers/data bases/backuplongtermretentionpolicies | Ja | Ja |
> | servers/elasticpools | Ja | Ja |
> | servers/jobaccounts | Ja | Ja |
> | servers/jobagents | Ja | Ja |
> | virtualclusters | Ja | Ja |

## <a name="microsoftsqlvirtualmachine"></a>Micro soft. SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | sqlvirtualmachinegroups | Ja | Ja |
> | sqlvirtualmachines | Ja | Ja |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | Storage accounts | Ja | Ja |

## <a name="microsoftstoragecache"></a>Micro soft. StorageCache

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | caches | Nee | Nee |

## <a name="microsoftstoragesync"></a>Micro soft. StorageSync

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | storagesyncservices | Ja | Ja |

## <a name="microsoftstoragesyncdev"></a>Micro soft. StorageSyncDev

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | storagesyncservices | Nee | Nee |

## <a name="microsoftstoragesyncint"></a>Micro soft. StorageSyncInt

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | storagesyncservices | Nee | Nee |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | leider | Nee | Nee |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!IMPORTANT]
> Stream Analytics taken kunnen niet worden verplaatst wanneer de status actief is.

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | clusters | Nee | Nee |
> | streamingjobs | Ja | Ja |

## <a name="microsoftstreamanalyticsexplorer"></a>Micro soft. StreamAnalyticsExplorer

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | verschillend | Nee | Nee |
> | vaak | Nee | Nee |

## <a name="microsoftsubscription"></a>Micro soft. Subscription

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | geabonneerd | Nee | Nee |

## <a name="microsoftsupport"></a>micro soft. ondersteuning

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | services | Nee | Nee |
> | supporttickets | Nee | Nee |

## <a name="microsoftsynapse"></a>Micro soft. Synapse

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | workspaces | Ja | Ja |
> | werk ruimten/bigdatapools | Ja | Ja |
> | werk ruimten/sqlpools | Ja | Ja |

## <a name="microsofttimeseriesinsights"></a>Micro soft. TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | verschillend | Ja | Ja |
> | omgevingen/eventsources | Ja | Ja |
> | omgevingen/referencedatasets | Ja | Ja |

## <a name="microsofttoken"></a>Micro soft. token

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | opslaat | Ja | Ja |

## <a name="microsoftvirtualmachineimages"></a>Microsoft.VirtualMachineImages

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | imagetemplates | Nee | Nee |

## <a name="microsoftvisualstudio"></a>micro soft. Visual Studio

> [!IMPORTANT]
> Als u het abonnement voor Azure DevOps wilt wijzigen, raadpleegt u [het Azure-abonnement wijzigen dat wordt gebruikt voor facturering](/azure/devops/organizations/billing/change-azure-subscription?toc=/azure/azure-resource-manager/toc.json).

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | account | Nee | Nee |
> | account/extensie | Nee | Nee |
> | account/project | Nee | Nee |

## <a name="microsoftvmware"></a>Micro soft. VMware

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | arczones | Nee | Nee |
> | resourcepools | Nee | Nee |
> | vCenter | Nee | Nee |
> | informatie | Nee | Nee |
> | virtualmachinetemplates | Nee | Nee |
> | virtualnetworks | Nee | Nee |

## <a name="microsoftvmwarecloudsimple"></a>Micro soft. VMwareCloudSimple

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | dedicatedcloudnodes | Nee | Nee |
> | dedicatedcloudservices | Nee | Nee |
> | informatie | Nee | Nee |

## <a name="microsoftvnfmanager"></a>Micro soft. VnfManager

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | devices | Nee | Nee |
> | vnfs | Nee | Nee |

## <a name="microsoftvsonline"></a>Micro soft. VSOnline

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | accounts | Nee | Nee |
> | plant | Nee | Nee |
> | registeredsubscriptions | Nee | Nee |

## <a name="microsoftweb"></a>Microsoft.Web

> [!IMPORTANT]
> Zie [app service richt lijnen voor verplaatsen](./move-limitations/app-service-move-limitations.md).

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | availablestacks | Nee | Nee |
> | billingmeters | Nee | Nee |
> | certificaten | Nee | Ja |
> | connectiongateways | Ja | Ja |
> | inbel | Ja | Ja |
> | customapis | Ja | Ja |
> | deletedsites | Nee | Nee |
> | deploymentlocations | Nee | Nee |
> | georegio's | Nee | Nee |
> | hostingenvironments | Nee | Nee |
> | kubeenvironments | Ja | Ja |
> | publishingusers | Nee | Nee |
> | aanbevelingen | Nee | Nee |
> | resourcehealthmetadata | Nee | Nee |
> | Runtimes | Nee | Nee |
> | server farms | Ja | Ja |
> | server farms/eventgridfilters | Nee | Nee |
> | sites | Ja | Ja |
> | sites/premieraddons | Ja | Ja |
> | sites/sleuven | Ja | Ja |
> | sourcecontrols | Nee | Nee |
> | staticsites | Nee | Nee |

## <a name="microsoftwindowsesu"></a>Micro soft. WindowsESU

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | multipleactivationkeys | Nee | Nee |

## <a name="microsoftwindowsiot"></a>Micro soft. WindowsIoT

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | deviceservices | Nee | Nee |

## <a name="microsoftworkloadbuilder"></a>Micro soft. WorkloadBuilder

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | workloads | Nee | Nee |

## <a name="microsoftworkloadmonitor"></a>Micro soft. WorkloadMonitor

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | materialen | Nee | Nee |
> | componentssummary | Nee | Nee |
> | monitorinstances | Nee | Nee |
> | monitorinstancessummary | Nee | Nee |
> | monitors | Nee | Nee |

## <a name="third-party-services"></a>Services van derden

Services van derden bieden momenteel geen ondersteuning voor de verplaatsings bewerking.

## <a name="next-steps"></a>Volgende stappen

Zie [resources verplaatsen naar een nieuwe resource groep of een nieuw abonnement](move-resource-group-and-subscription.md)voor opdrachten voor het verplaatsen van resources.

Als u dezelfde gegevens wilt ophalen als een bestand met door komma's gescheiden waarden, downloadt u [move-support-resources.csv](https://github.com/tfitzmac/resource-capabilities/blob/master/move-support-resources.csv).
