---
title: Ondersteuning voor het verplaatsen van Azure-resources in verschillende regio's
description: Een lijst met de Azure-resource typen die kunnen worden verplaatst over Azure-regio's
author: rayne-wiselman
ms.service: azure-resource-manager
ms.topic: reference
ms.date: 08/25/2020
ms.author: raynew
ms.openlocfilehash: 18d4d84462d528b718d784ff6a16ecf990ed0d20
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100094012"
---
# <a name="support-for-moving-azure-resources-across-regions"></a>Ondersteuning voor het verplaatsen van Azure-resources in verschillende regio's

In dit artikel wordt bevestigd of een Azure-resource type wordt ondersteund voor het verplaatsen naar een andere Azure-regio. 

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
> - [Microsoft.Purview](#microsoftpurview)
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
> - [Microsoft.Storage](#microsoftstorage)
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
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | domainservices | Nee | 


## <a name="microsoftaadiam"></a>micro soft. aadiam

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | diagnosticsettings | Nee |
> | diagnosticsettingscategories | Nee |
> | privatelinkforazuread | Nee |
> | tenants |  Nee |

## <a name="microsoftaddons"></a>micro soft. Addons

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | supportproviders | Nee |

## <a name="microsoftadhybridhealthservice"></a>Microsoft.ADHybridHealthService

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- | 
> | aadsupportcases | Nee |
> | addsservices | Nee | 
> | middelen | Nee | 
> | anonymousapiusers | Nee |
> | configuratie | Nee | 
> | logboeken | Nee | 
> | rapporten | Nee | 
> | servicehealthmetrics | Nee | 
> | services | Nee | 

## <a name="microsoftadvisor"></a>Microsoft.Advisor

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- | 
> | configuraties | Nee | 
> | generaterecommendations | Nee |
> | metagegevens | Nee |
> | aanbevelingen | Nee |
> | onderdrukkingen | Nee | 

## <a name="microsoftalertsmanagement"></a>Micro soft. AlertsManagement

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | actionrules | Nee | 
> | waarschuwingen | Nee | 
> | alertslist | Nee | 
> | alertsmetadata | Nee | 
> | alertssummary | Nee | 
> | alertssummarylist | Nee | 
> | smartdetectoralertrules | Nee | 
> | smartgroups | Nee | 

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | Server | Nee |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | reportfeedback | Nee |
> | service |  Ja (met behulp van sjabloon) <br/><br/> [API Management verplaatsen tussen regio's](../../api-management/api-management-howto-migrate.md). | 

## <a name="microsoftappconfiguration"></a>Micro soft. AppConfiguration

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | configurationstores | Nee | 
> | configurationstores / eventgridfilters | Nee |

## <a name="microsoftappplatform"></a>Micro soft. AppPlatform

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- | 
> | lente | Nee | 

## <a name="microsoftappservice"></a>Micro soft. AppService

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | apiapps | Ja (met behulp van sjabloon)<br/><br/> [Een App Service-app naar een andere regio verplaatsen](../../app-service/manage-move-across-regions.md) | 
> | appidentities | Nee | 
> | gateways | Nee | 

## <a name="microsoftattestation"></a>Micro soft. Attestation

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- | 
> | attestationproviders | Nee | 

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | classicadministrators | Nee | 
> | dataaliases | Nee | 
> | denyassignments | Nee | 
> | elevateaccess | Nee | 
> | findorphanroleassignments | Nee | 
> | vergren delingen | Nee | 
> | permissions | Nee | 
> | policyassignments | Nee | 
> | policydefinitions | Nee | 
> | policysetdefinitions | Nee | 
> | privatelinkassociations | Nee | 
> | resourcemanagementprivatelinks | Nee | 
> | roleassignments | Nee | 
> | roleassignmentsusagemetrics | Nee | 
> | roledefinitions | Nee | 

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | automationaccounts | Ja (met behulp van sjabloon) <br/><br/> [Geo-replicatie gebruiken](../../automation/automation-managing-data.md#geo-replication-in-azure-automation) |  
> | automationaccounts/configuraties | Nee | 
> | automationaccounts/runbooks | Nee | 

## <a name="microsoftavs"></a>Micro soft. AVS

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | Abonnement |
> | ------------- | ----------- | 
> | privateclouds | Nee | 


## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | b2cdirectories | Nee | 
> | b2ctenants | Nee | 

## <a name="microsoftazuredata"></a>Micro soft. Azureworden

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | datacontrollers | Nee | 
> | hybriddatamanagers | Nee | 
> | postgresinstances | Nee | 
> | sqlinstances | Nee | 
> | sqlmanagedinstances | Nee |
> | sqlserverinstances | Nee | 
> | sqlserverregistrations | Nee |

## <a name="microsoftazurestack"></a>Micro soft. AzureStack

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | cloudmanifestfiles | Nee |
> | registraties | Nee | 

## <a name="microsoftazurestackhci"></a>Micro soft. AzureStackHCI

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- | 
> | clusters | Nee | 

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | batchaccounts |  Batch-accounts kunnen niet rechtstreeks worden verplaatst van de ene regio naar een andere, maar u kunt een sjabloon gebruiken om een sjabloon te exporteren, deze te wijzigen en de sjabloon te implementeren in de nieuwe regio. <br/><br/> Meer informatie over [het verplaatsen van een batch-account in meerdere regio's](../../batch/best-practices.md#moving-batch-accounts-across-regions) |

## <a name="microsoftbilling"></a>Microsoft.Billing

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- | 
> | billingaccounts | Nee | 
> | billingperiods | Nee | 
> | billingpermissions | Nee | 
> | billingproperty | Nee | 
> | billingroleassignments | Nee | 
> | billingroledefinitions | Nee | 
> | afdeling | Nee | 
> | enrollmentaccounts | Nee | 
> | factureer | Nee | 
> | Making | Nee | 

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | mapapis | Nee | 

## <a name="microsoftbiztalkservices"></a>Micro soft. BizTalkServices

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | biztalk | Nee | 

## <a name="microsoftblockchain"></a>Micro soft. Block Chain

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | blockchainmembers | Nee <br/><br/> Het block chain-netwerk kan geen knoop punten in verschillende regio's hebben. 
> | cordamembers | Nee |
> | Volg | Nee | 

## <a name="microsoftblockchaintokens"></a>Micro soft. BlockchainTokens

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | tokenservices | Nee |


## <a name="microsoftblueprint"></a>Micro soft. blauw druk

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | blueprintassignments | Nee | 
> | blauw drukken | Nee |

## <a name="microsoftbotservice"></a>Micro soft. BotService

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | botservices | Nee | 

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | redis | Nee | 
> | redisenterprise | Nee | 

## <a name="microsoftcapacity"></a>Micro soft. capacity

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- | 
> | appliedreservations | Nee | 
> | calculateexchange | Nee | 
> | calculateprice | Nee | 
> | calculatepurchaseprice | Nee | 
> | catalogi | Nee | 
> | commercialreservationorders | Nee | 
> | Exchange | Nee |
> | reservationorders | Nee | 
> | ringen | Nee | 
> | resources | Nee | 
> | validatereservationorder | Nee | 

## <a name="microsoftcdn"></a>Micro soft. CDN

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | cdnwebapplicationfirewallpolicies | Nee |
> | edgenodes | Nee
> | profielen | Nee | 
> | profielen/eind punten | Nee | 

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | certificateorders | Nee | 


## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | mogelijkheden | Nee | 
> | domein naam | Ja | Nee |
> | quotas | Nee | 
> | resourcetypes | Nee |
> | validatesubscriptionmoveavailability | Nee | 
> | informatie | Nee 

## <a name="microsoftclassicinfrastructuremigrate"></a>Micro soft. ClassicInfrastructureMigrate

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- | 
> | classicinfrastructureresources | Nee | 

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | mogelijkheden | Nee | 
> | expressroutecrossconnections | Nee | 
> | expressroutecrossconnections/peerings | Nee | 
> | gatewaysupporteddevices | Nee | 
> | networksecuritygroups | Nee |
> | quotas | Nee |
> | reservedips | Nee | 
> | virtualnetworks | Nee | 

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | cd's | Nee | 
> | images | Nee | 
> | osimages | Nee | 
> | osplatformimages | Nee | 
> | publicimages | Nee | 
> | quotas | Nee | 
> | Storage accounts | Ja |  
> | vmimages | Nee |

## <a name="microsoftclassicsubscription"></a>Micro soft. ClassicSubscription

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- | 
> | bewerkingen | Nee | 

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | accounts | Nee | 
> | Cognitive Search | Wordt ondersteund met hand matige stappen.<br/><br/> Meer informatie over [het verplaatsen van uw Azure Cognitive Search-service naar een andere regio](../../search/search-howto-move-across-regions.md)

## <a name="microsoftcommerce"></a>Microsoft.Commerce

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- | 
> | ratecard | Nee | 
> | usageaggregates | Nee | 

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | Availability sets | Ja <br/><br/> Gebruik [Azure resource](../../resource-mover/tutorial-move-region-virtual-machines.md) -overzetten om beschikbaarheids sets te verplaatsen. | 
> | diskaccesses | Nee |
> | diskencryptionsets | Nee | 
> | cd's | Ja <br/><br/> Gebruik [Azure resource](../../resource-mover/tutorial-move-region-virtual-machines.md) -overstap om Azure-vm's en gerelateerde schijven te verplaatsen. | 
> | Galerij | Nee | 
> | galerieën/afbeeldingen | Nee | 
> | galerieën/afbeeldingen/versies | Nee | 
> | hostgroups | Nee | 
> | hostgroups/hosts | Nee | 
> | images | Nee | 
> | proximityplacementgroups | Nee | 
> | restorepointcollections | Nee | 
> | sharedvmimages | Nee | 
> | sharedvmimages/versies | Nee | 
> | momentopnamen | Nee | 
> | sshpublickeys | Nee |
> | informatie | Ja <br/><br/> Gebruik [Azure resource](../../resource-mover/tutorial-move-region-virtual-machines.md) -overstap om Azure-vm's te verplaatsen. | 
> | informatie/extensies | Nee | 
> | virtualmachinescalesets | Nee | 

## <a name="microsoftconsumption"></a>Micro soft. verbruik

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- | 
> | aggregatedcost | Nee | 
> | saldi | Nee | 
> | budgetten | Nee | 
> | belastingen | Nee | 
> | costtags | Nee | 
> | aanvragen | Nee | 
> | events | Nee | 
> | Forecast | Nee | 
> | extra | Nee | 
> | markt plaatsen | Nee | 
> | pricesheets | Nee | 
> | producten | Nee | 
> | reservationdetails | Nee | 
> | reservationrecommendationdetails | Nee | 
> | reservationrecommendations | Nee | 
> | reservationsummaries | Nee | 
> | reservationtransactions | Nee | 
> | tags | Nee | 
> | tenants | Nee | 
> | inzake | Nee | 
> | usagedetails | Nee | 


## <a name="microsoftcontainerinstance"></a>Micro soft. ContainerInstance

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | containergroups | Nee | 
> | serviceassociationlinks | Nee |


## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | registers | Nee |  
> | registers/agentpools | Nee | 
> | registers/buildtasks | Nee |  
> | registers/replicaties | Nee | 
> | registers/taken | Nee |  
> | registers/webhooks | Nee | 

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | containerservices | Nee |
> | managedclusters | Nee | 
> | openshiftmanagedclusters | Nee | 

## <a name="microsoftcontentmoderator"></a>Microsoft.ContentModerator

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | toepassingen | Nee | 

## <a name="microsoftcortanaanalytics"></a>Micro soft. CortanaAnalytics

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | accounts | Nee | 

## <a name="microsoftcostmanagement"></a>Micro soft. CostManagement

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | waarschuwingen | Nee | 
> | billingaccounts | Nee | 
> | budgetten | Nee | 
> | cloudconnectors | Nee | 
> | connectoren | Nee | 
> | afdeling | Nee | 
> | hoogte | Nee | 
> | enrollmentaccounts | Nee | 
> | dump | Nee | 
> | externalbillingaccounts | Nee | 
> | forecast | Nee | 
> | query | Nee | 
> | registreren | Nee | 
> | reportconfigs | Nee | 
> | rapporten | Nee | 
> | instellingen | Nee | 
> | showbackrules | Nee | 
> | Weergaven | Nee | 

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | hubs | Nee |  

## <a name="microsoftcustomerlockbox"></a>Micro soft. CustomerLockbox

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- | 
> | requests | Nee | 

## <a name="microsoftcustomproviders"></a>Micro soft. CustomProviders

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | lidkoppelingen | Nee |
> | resourceproviders | Nee | 

## <a name="microsoftdatabox"></a>Micro soft. DataBox

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | functies | Nee | 

## <a name="microsoftdataboxedge"></a>Micro soft. DataBoxEdge

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | availableskus | Nee |
> | databoxedgedevices | Nee | 

## <a name="microsoftdatabricks"></a>Micro soft. Databricks

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | workspaces | Nee | 

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | catalogi | Nee | 
> | datacatalogs | Nee | 

## <a name="microsoftdataconnect"></a>Micro soft. DataConnect

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | connectionmanagers | Nee | 

## <a name="microsoftdataexchange"></a>Micro soft. DataExchange

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | Pakketten | Nee | 
> | plant | Nee | 

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | datafactories | Nee | 
> | factory's | Nee |  

## <a name="microsoftdatalake"></a>Micro soft. DataLake

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | datalakeaccounts | Nee | 

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | accounts | Nee | 

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | accounts | Nee | 

## <a name="microsoftdatamigration"></a>Micro soft. DataMigration

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | services | Nee | 
> | Services/projecten | Nee | 
> | sleuf | Nee | 

## <a name="microsoftdataprotection"></a>Micro soft. DataProtection

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- | ---------- |
> | backupvaults | Nee | 

## <a name="microsoftdatashare"></a>Micro soft. DataShare

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | accounts | Nee | 

## <a name="microsoftdbformariadb"></a>Micro soft. DBforMariaDB

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | Server | U kunt een replicatie met meerdere regio's gebruiken om een bestaande server te verplaatsen. [Meer informatie](../../postgresql/howto-move-regions-portal.md).<br/><br/> Als de service is ingericht met geografisch redundante back-upopslag, kunt u geo-herstel gebruiken om in andere regio's te herstellen. [Meer informatie](../../mariadb/concepts-business-continuity.md#recover-from-an-azure-regional-data-center-outage).

## <a name="microsoftdbformysql"></a>Micro soft. DBforMySQL

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | Server | U kunt een replicatie met meerdere regio's gebruiken om een bestaande server te verplaatsen. [Meer informatie](../../mysql/howto-move-regions-portal.md).

## <a name="microsoftdbforpostgresql"></a>Micro soft. DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | servergroups | Nee | 
> | Server | U kunt een replicatie met meerdere regio's gebruiken om een bestaande server te verplaatsen. [Meer informatie](../../postgresql/howto-move-regions-portal.md).
> | serversv2 | Nee | 

## <a name="microsoftdeploymentmanager"></a>Micro soft. DeploymentManager

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | artifactsources | Nee | 
> | implementaties | Nee |  
> | servicetopologies | Nee | 
> | servicetopologies/Services | Nee |  
> | servicetopologies/Services/serviceunits | Nee | 
> | stappen | Nee | 


## <a name="microsoftdesktopvirtualization"></a>Micro soft. DesktopVirtualization

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- | 
> | applicationgroups | Nee | 
> | workspaces | Nee | 

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | elasticpools | Nee. De resource is niet beschikbaar.
> | elasticpools / iothubtenants | Nee. De resource is niet beschikbaar.
> | iothubs | Ja. [Meer informatie](../../iot-hub/iot-hub-how-to-clone.md)
> | provisioningservices | Nee | 

## <a name="microsoftdevops"></a>Micro soft. DevOps

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | fungeren | Nee | 


## <a name="microsoftdevspaces"></a>Micro soft. DevSpaces

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | fungeren | Nee | 
> | AKS-cluster | Nee<br/><br/> Meer [informatie](../../dev-spaces/faq.md#can-i-migrate-my-aks-cluster-with-azure-dev-spaces-to-another-region) over verplaatsen naar een andere regio.

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | labcenters | Nee | 
> | Labs | Nee | 
> | Labs/omgevingen | Nee |  
> | Labs-servicerunners | Nee | 
> | Labs-informatie | Nee |  
> | schema's | Nee |  

## <a name="microsoftdigitaltwins"></a>Micro soft. DigitalTwins

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- | 
> | digitaltwinsinstances | Ja, door resources opnieuw te maken in een nieuwe regio. [Meer informatie](../../digital-twins/how-to-move-regions.md) |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | databaseaccounts | Nee | 
> | databaseaccounts | Nee | 

## <a name="microsoftdomainregistration"></a>Micro soft. DomainRegistration

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | domeinen | Nee | 
> | generatessorequest | Nee | 
> | topleveldomains | Nee | 
> | validatedomainregistrationinformation | Nee |

## <a name="microsoftenterpriseknowledgegraph"></a>Micro soft. EnterpriseKnowledgeGraph

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | services | Nee |  

## <a name="microsofteventgrid"></a>Micro soft. EventGrid

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | domeinen | Nee | 
> | eventsubscriptions | Nee |
> | extensiontopics | Nee | 
> | partnernamespaces | Nee | 
> | partnerregistrations | Nee | 
> | partnertopics | Nee | 
> | systemtopics | Nee | 
> | onderwerp | Nee | 
> | topictypes | Nee | 

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | clusters | Nee |  
> | naam ruimten | Ja (met sjabloon)<br/><br/> [Een event hub-naam ruimte verplaatsen naar een andere regio](../../event-hubs/move-across-regions.md) | 
> | sku | Nee |  

## <a name="microsoftexperimentation"></a>Micro soft. experimenten

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- | 
> | experimentworkspaces | Nee | 

## <a name="microsoftfalcon"></a>Micro soft. Falcon

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- | 
> | naam ruimten | Nee | 

## <a name="microsoftfeatures"></a>Microsoft.Features

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- | 
> | featureproviders | Nee | 
> | features | Nee | 
> | providers | Nee | 
> | subscriptionfeatureregistrations | Nee | 

## <a name="microsoftgenomics"></a>Micro soft. Genomics

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | accounts | Nee | 

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- | 
> | automanagedaccounts | Nee | 
> | automanagedvmconfigurationprofiles | Nee | 
> | guestconfigurationassignments | Nee | 
> | software | Nee | 
> | softwareupdateprofile | Nee | 
> | softwareupdates | Nee | 

## <a name="microsofthanaonazure"></a>Micro soft. HanaOnAzure

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | hanainstances | Nee | 
> | sapmonitors | Nee |  

## <a name="microsofthardwaresecuritymodules"></a>Micro soft. HardwareSecurityModules

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- | 
> | dedicatedhsms | Nee | 


## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | clusters | Nee | 

## <a name="microsofthealthcareapis"></a>Micro soft. HealthcareApis

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | services | Nee |  

## <a name="microsofthybridcompute"></a>Microsoft.HybridCompute

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | apparaten | Nee | 
> | computers/uitbrei dingen | Nee |

## <a name="microsofthybriddata"></a>Micro soft. HybridData

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | datamanagers |  Nee | 

## <a name="microsofthybridnetwork"></a>Micro soft. HybridNetwork

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- | 
> | devices | Nee | 
> | vnfs | Nee | 

## <a name="microsofthydra"></a>Micro soft. Hydra

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- | 
> | materialen | Nee | 
> | networkscopes | Nee | 

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | functies |  Nee | 

## <a name="microsoftinsights"></a>micro soft. Insights

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | accounts | Nee. [Meer informatie](../../azure-monitor/faq.md#how-do-i-move-an-application-insights-resource-to-a-new-region).
> | actiongroups |  Nee | 
> | activitylogalerts | Nee | 
> | alertrules |  Nee | 
> | autoscalesettings |  Nee | 
> | basislijn | Nee |
> | materialen |  Nee |  
> | datacollectionrules | Nee | 
> | diagnosticsettings | Nee | 
> | diagnosticsettingscategories | Nee | 
> | eventcategories | Nee | 
> | eventtypes | Nee | 
> | extendeddiagnosticsettings | Nee | |
> | guestdiagnosticsettings | Nee | 
> | listmigrationdate | Nee | 
> | logdefinitions | Nee | 
> | logprofiles | Nee | 
> | logboeken | Nee | Nee |
> | metricalerts | Nee | 
> | metricbaselines | Nee | 
> | metricbatch | Nee | 
> | metricdefinitions | Nee | 
> | metricnamespaces | Nee | 
> | metrics | Nee | 
> | migratealertrules | Nee |
> | migratetonewpricingmodel | Nee | 
> | myworkbooks | Nee |
> | notificationgroups | Nee | 
> | privatelinkscopes | Nee |
> | rollbacktolegacypricingmodel | Nee |
> | scheduledqueryrules |  Nee | 
> | topologie | Nee |
> | transacties | Nee |
> | vminsightsonboardingstatuses | Nee |
> | webtests |  Nee | 
> | webtesten/gettestresultfile | Nee |
> | werkmappen |  Nee |  
> | workbooktemplates | Nee |


## <a name="microsoftiotcentral"></a>Micro soft. IoTCentral

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | apptemplates | Nee | 
> | iotapps | Nee | 



## <a name="microsoftiothub"></a>Microsoft.IotHub

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> |  iothub |  Ja (hub klonen) <br/><br/> [Een IoT-hub klonen naar een andere regio](../../iot-hub/iot-hub-how-to-clone.md)

## <a name="microsoftiotspaces"></a>Micro soft. IoTSpaces

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen |
> | ------------- | ----------- | 
> | Graph | Nee | 

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | deletedvaults | Nee |
> | hsmpools | Nee | 
> | managedhsms | Nee |
> | kluizen |  Nee | 

## <a name="microsoftkubernetes"></a>Microsoft.Kubernetes

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- | 
> | connectedclusters | Nee | 
> | registeredsubscriptions | Nee | 

## <a name="microsoftkubernetesconfiguration"></a>Micro soft. KubernetesConfiguration

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- | 
> | sourcecontrolconfigurations | Nee | 

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | clusters |  Nee |  

## <a name="microsoftlabservices"></a>Micro soft. LabServices

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | labaccounts | Nee | 
> | gebruikers | Nee | 

## <a name="microsoftlocationbasedservices"></a>Micro soft. LocationBasedServices

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | accounts | Nee | 

## <a name="microsoftlocationservices"></a>Micro soft. bestand locationservices

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | accounts | Nee, het is een wereld wijde service.

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | hostingenvironments | Nee | 
> | integrationaccounts |  Nee |  
> | integrationserviceenvironments | Nee | 
> | integrationserviceenvironments/beheerdeapi's | Nee |
> | isolatedenvironments | Nee | 
> | stroom |  Nee |  

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | commitmentplans |  Nee | 
> | webservices |  Nee | 
> | workspaces |  Nee | 

## <a name="microsoftmachinelearningcompute"></a>Micro soft. MachineLearningCompute

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | operationalizationclusters |  Nee | 

## <a name="microsoftmachinelearningexperimentation"></a>Micro soft. MachineLearningExperimentation

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | accounts | Nee | 
> | teamaccounts | Nee | 


## <a name="microsoftmachinelearningmodelmanagement"></a>Micro soft. MachineLearningModelManagement

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | accounts | Nee | 


## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | workspaces | Nee | 

## <a name="microsoftmaintenance"></a>Micro soft. onderhoud

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | configurationassignments | Ja. [Meer informatie](../../virtual-machines/move-region-maintenance-configuration.md) | 
> | maintenanceconfigurations | Ja. [Meer informatie](../../virtual-machines/move-region-maintenance-configuration-resources.md) |
> | updates | Nee | 

## <a name="microsoftmanagedidentity"></a>Micro soft. ManagedIdentity

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | identiteit | Nee | 
> | userassignedidentities | Nee | 

## <a name="microsoftmanagednetwork"></a>Micro soft. ManagedNetwork

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- | 
> | managednetworks | Nee | 
> | managednetworks / managednetworkgroups | Nee |
> | managednetworks / managednetworkpeeringpolicies | Nee | 
> | melding | Nee | 

## <a name="microsoftmanagedservices"></a>Micro soft. ManagedServices

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- | 
> | marketplaceregistrationdefinitions | Nee | 
> | registrationassignments | Nee |
> | registrationdefinitions | Nee | 

## <a name="microsoftmanagement"></a>Micro soft. Management

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- | 
> | getentities | Nee | 
> | managementgroups | Nee | 
> | managementgroups/instellingen | Nee | 
> | resources | Nee | 
> | starttenantbackfill | Nee | 
> | tenantbackfillstatus | Nee | 

## <a name="microsoftmaps"></a>Micro soft. Maps

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | accounts |  Nee, Azure Maps is een georuimtelijke service. 
> | accounts/privateatlases | Nee

## <a name="microsoftmarketplace"></a>Micro soft. Marketplace

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | over | Nee | 
> | offertypes | Nee | 
> | privategalleryitems | Nee | 
> | privatestoreclient | Nee | 
> | privatestores | Nee | 
> | producten | Nee | 
> | uitgevers | Nee | 
> | registreren | Nee | 

## <a name="microsoftmarketplaceapps"></a>Micro soft. MarketplaceApps

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- | 
> | classicdevservices | Nee | 

## <a name="microsoftmarketplaceordering"></a>Microsoft.MarketplaceOrdering

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- | 
> | gesloten | Nee | 
> | offertypes | Nee | 

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | Media Services |  Nee | 
> | Media Services/liveevents |  Nee | 
> | Media Services/streamingendpoints |  Nee | 

## <a name="microsoftmicroservices4spring"></a>Micro soft. Microservices4Spring

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | appclusters | Nee | 

## <a name="microsoftmigrate"></a>Micro soft. migrate

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | assessmentprojects | Nee | 
> | migrateprojects | Nee | 
> | movecollections | Nee
> | projecten | Nee | 

## <a name="microsoftmixedreality"></a>Micro soft. MixedReality

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- | ---------- |
> | holographicsbroadcastaccounts | Nee | 
> | objectunderstandingaccounts | Nee | 
> | remoterenderingaccounts | Nee | 
> | spatialanchorsaccounts | Nee | 

## <a name="microsoftnetapp"></a>Micro soft. NetApp

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | netappaccounts | Nee | 
> | netappaccounts / capacitypools | Nee | 
> | netappaccounts/capacitypools/volumes | Nee | 
> | netappaccounts/capacitypools/volumes/mounttargets | Nee | 
> | netappaccounts/capacitypools/volumes/moment opnamen | Nee | 

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | applicationgateways | Nee |
> | applicationgatewaywebapplicationfirewallpolicies | Nee | 
> | applicationsecuritygroups |  Nee |  
> | azurefirewalls |  Nee |  
> | bastionhosts | Nee | 
> | bgpservicecommunities | Nee |
> | inbel |  Nee | 
> | ddoscustompolicies |  Nee | 
> | ddosprotectionplans | Nee | 
> | dnszones |  Nee | 
> | expressroutecircuits | Nee | 
> | expressroutegateways | Nee | 
> | expressrouteserviceproviders | Nee | 
> | firewallpolicies | Nee |
> | frontdoors | Nee | 
> | ipallocations | Nee |
> | ipgroups | Nee |
> | loadbalancers | Ja <br/><br/> Gebruik [Azure resource](../../resource-mover/tutorial-move-region-virtual-machines.md) -overstap om interne en externe load balancers te verplaatsen. |
> | localnetworkgateways |  Nee | 
> | natgateways |  Nee | 
> | networkexperimentprofiles | Nee |
> | networkintentpolicies |  Nee | 
> | networkinterfaces | Ja <br/><br/> Gebruik [Azure resource](../../resource-mover/tutorial-move-region-virtual-machines.md) -overstap om nic's te verplaatsen. | 
> | networkprofiles | Nee | 
> | networksecuritygroups | Ja <br/><br/> Gebruik [Azure resource](../../resource-mover/tutorial-move-region-virtual-machines.md) -overzetten om netwerk beveiligings groepen (NGSs) te verplaatsen. | 
> | networkwatchers |  Nee |  
> | networkwatchers / connectionmonitors |  Nee | 
> | networkwatchers / flowlogs |  Nee | 
> | networkwatchers / pingmeshes |  Nee | 
> | p2svpngateways | Nee | 
> | privatednszones |  Nee |  
> | privatednszones / virtualnetworklinks | Nee |> | privatednszonesinternal | Nee |
> | privateendpointredirectmaps | Nee |
> | privateendpoints | Nee | 
> | privatelinkservices | Nee | 
> | publicipaddresses | Ja<br/><br/> Gebruik [Azure resource](../../resource-mover/tutorial-move-region-virtual-machines.md) delegering om open bare IP-adressen te verplaatsen. |
> | publicipprefixes | Nee | 
> | routefilters | Nee | 
> | routetables |  Nee | 
> | securitypartnerproviders | Nee |
> | serviceendpointpolicies |  Nee | 
> | trafficmanagergeographichierarchies | Nee | 
> | trafficmanagerprofiles |  Nee | 
> | trafficmanagerusermetricskeys | Nee |
> | virtualhubs | Nee | 
> | virtualnetworkgateways |  Nee |  
> | virtualnetworks |  Nee | 
> | virtualnetworktaps | Nee | 
> | virtualwans | Nee | 
> | vpngateways (virtueel WAN) | Nee | 
> | vpnsites (virtueel WAN) | Nee | 
> | vpnsites (virtueel WAN) | Nee |


## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | naam ruimten |  Nee | 
> | naam ruimten/notification hubs |  Nee |  

## <a name="microsoftobjectstore"></a>Micro soft. ObjectStore

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- | 
> | osnamespaces | Nee | 

## <a name="microsoftoffazure"></a>Micro soft. OffAzure

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- | 
> | hypervsites | Nee | 
> | importsites | Nee | 
> | serversites | Nee | 
> | vmwaresites | Nee | 

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | clusters | Nee | 
> | deletedworkspaces | Nee | 
> | linktargets | Nee | 
> | storageinsightconfigs | Nee |
> | workspaces | Nee |



## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | managementassociations | Nee |
> | managementconfigurations |  Nee | 
> | oplossingen | Nee |
> | Weergaven |  Nee | 

## <a name="microsoftpeering"></a>Micro soft. peering

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | legacypeerings | Nee | 
> | peerasns | Nee | 
> | peeringlocations | Nee | 
> | Peerings | Nee | 
> | peeringservicecountries | Nee | 
> | peeringservicelocations | Nee | 
> | peeringserviceproviders | Nee | 
> | peeringservices | Nee | 

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- | 
> | policyevents | Nee | 
> | policystates | Nee | 
> | policytrackedresources | Nee | 
> | herstel | Nee | 

## <a name="microsoftportal"></a>Micro soft. Portal

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> |  -consoles | Nee |
> | dashboards | Nee | 
> | usersettings | Nee | 


## <a name="microsoftpowerbi"></a>Micro soft. PowerBI

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | workspacecollections |  Nee | 

## <a name="microsoftpowerbidedicated"></a>Micro soft. PowerBIDedicated

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | beschikt |  Nee | 

## <a name="microsoftpurview"></a>Microsoft.Purview

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | accounts | Nee | 

## <a name="microsoftproviderhub"></a>Micro soft. ProviderHub

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- | 
> | availableaccounts | Nee | 
> | providerregistrations | Nee | 
> | implementaties | Nee | 

## <a name="microsoftquantum"></a>Micro soft. Quantum

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- | 
> | workspaces | Nee | 

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | replicationeligibilityresults | Nee |
> | kluizen | Nee.<br/><br/> Het is niet mogelijk om Recovery Services kluizen voor Azure Backup over Azure-regio's te verplaatsen.<br/><br/> In Recovery Services kluizen voor Azure Site Recovery kunt u de kluis in de doel regio [uitschakelen en opnieuw maken](../../site-recovery/move-vaults-across-regions.md) . | 

## <a name="microsoftredhatopenshift"></a>Micro soft. RedHatOpenShift

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- | 
> | openshiftclusters | Nee | 

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | naam ruimten |  Nee | 

## <a name="microsoftresourcegraph"></a>Micro soft. ResourceGraph

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | aanvragen |  Nee |  
> | resourcechangedetails | Nee | 
> | resourcechanges | Nee | 
> | resources | Nee | 
> | resourceshistory | Nee | 
> | subscriptionsstatus | Nee | 

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- | 
> | childresources | Nee | 
> | emergingissues | Nee | 
> | events | Nee | 
> | metagegevens | Nee | 
> | meldingen | Nee | 

## <a name="microsoftresources"></a>Microsoft.Resources

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen |
> | ------------- | ----------- |
> | deploymentScripts |  Ja<br/><br/>[Resources van micro soft. resources naar een nieuwe regio verplaatsen](microsoft-resources-move-regions.md) |
> | templateSpecs |  Ja<br/><br/>[Resources van micro soft. resources naar een nieuwe regio verplaatsen](microsoft-resources-move-regions.md) |  

## <a name="microsoftsaas"></a>Micro soft. SaaS

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | toepassingen |  Nee | 
> | saasresources | Nee | 


## <a name="microsoftsearch"></a>Microsoft.Search

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | resourcehealthmetadata | Nee |
> | searchservices |  Nee | 


## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | adaptivenetworkhardenings | Nee | 
> | advancedthreatprotectionsettings | Nee | 
> | waarschuwingen | Nee | 
> | allowedconnections | Nee | 
> | applicationwhitelistings | Nee | 
> | assessmentmetadata | Nee | 
> | evaluaties | Nee | 
> | autodismissalertsrules | Nee | 
> | automatisering | Nee | 
> | autoprovisioningsettings | Nee |
> | complianceresults | Nee | 
> | ingebouwde strengste | Nee | 
> | datacollectionagents | Nee | 
> | devicesecuritygroups | Nee | 
> | discoveredsecuritysolutions | Nee | 
> | externalsecuritysolutions | Nee | 
> | informationprotectionpolicies | Nee | 
> | iotsecuritysolutions | Nee | 
> | iotsecuritysolutions / analyticsmodels | Nee | 
> | iotsecuritysolutions / analyticsmodels / aggregatedalerts | Nee | 
> | iotsecuritysolutions / analyticsmodels / aggregatedrecommendations | Nee | 
> | jitnetworkaccesspolicies | Nee | 
> | policies | Nee | 
> | prijzen | Nee | 
> | regulatorycompliancestandards | Nee | 
> | regulatorycompliancestandards / regulatorycompliancecontrols | Nee | 
> | regulatorycompliancestandards / regulatorycompliancecontrols / regulatorycomplianceassessments | Nee | 
> | securitycontacts | Nee | 
> | securitysolutions | Nee | 
> | securitysolutionsreferencedata | Nee | 
> | securitystatuses | Nee | 
> | securitystatusessummaries | Nee | 
> | servervulnerabilityassessments | Nee | 
> | instellingen | Nee | 
> | subevaluaties | Nee |
> | taken | Nee | 
> | topologieën | Nee | 
> | workspacesettings | Nee | 

## <a name="microsoftsecurityinsights"></a>Micro soft. SecurityInsights

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- | 
> | aggregaties | Nee | 
> | alertrules | Nee | 
> | alertruletemplates | Nee | 
> | automationrules | Nee |
> | meldingen | Nee | 
> | dataconnectors | Nee | 
> | entiteiten | Nee | 
> | entityqueries | Nee |
> | incidenten | Nee | 
> | officeconsents | Nee | 
> | instellingen | Nee | 
> | threatintelligence | Nee | 

## <a name="microsoftserialconsole"></a>Micro soft. SerialConsole

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- | 
> | consoleservices | Nee | 

## <a name="microsoftservermanagement"></a>Microsoft.ServerManagement

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | gateways | Nee | 
> | punt | Nee | 

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | naam ruimten |  Nee | 
> | premiummessagingregions | Nee | 
> | sku | Nee | 

## <a name="microsoftservicefabric"></a>Micro soft. ServiceFabric

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | toepassingen | Nee | 
> | clusters |  Nee |  
> | containergroups | Nee | 
> | containergroupsets | Nee | 
> | edgeclusters | Nee | 
> | managedclusters | Nee |
> | netwerken | Nee | 
> | secretstores | Nee | 
> | volumes | Nee | 

## <a name="microsoftservicefabricmesh"></a>Micro soft. ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | toepassingen |  Nee | 
> | containergroups | Nee | 
> | gateways |  Nee | 
> | netwerken |  Nee | 
> | geheimen |  Nee | 
> | volumes |  Nee |  

## <a name="microsoftservices"></a>Micro soft. Services

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- | 
> | implementaties | Nee | 

## <a name="microsoftsignalrservice"></a>Micro soft. SignalRService

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | signalr |  Nee |  

## <a name="microsoftsoftwareplan"></a>Micro soft. SoftwarePlan

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- | 
> | hybridusebenefits | Nee | 

## <a name="microsoftsolutions"></a>Micro soft. Solutions

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | appliancedefinitions | Nee | 
> | uitrusting | Nee | 
> | jitrequests | Nee | 

## <a name="microsoftsql"></a>Microsoft.Sql

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | instancepools | Nee | 
> | locaties | Nee |
> | managedinstances | Ja <br/><br/> Meer [informatie](../../azure-sql/database/move-resources-across-regions.md) over het verplaatsen van beheerde exemplaren tussen regio's. | 
> | managedinstances/data bases | Ja | 
> | Server | Ja | 
> | servers/data bases | Ja <br/><br/> Meer [informatie](../../azure-sql/database/move-resources-across-regions.md) over het verplaatsen van data bases in verschillende regio's.<br/><br/> Meer [informatie](../../resource-mover/tutorial-move-region-sql.md) over het gebruik van Azure resource-overstap voor het verplaatsen van Azure SQL-data bases.  | 
> | servers/elasticpools | Ja <br/><br/> Meer [informatie](../../azure-sql/database/move-resources-across-regions.md) over het verplaatsen van elastische Pools in verschillende regio's.<br/><br/> Meer [informatie](../../resource-mover/tutorial-move-region-sql.md) over het gebruik van Azure resource Move om elastische Azure SQL-Pools te verplaatsen.  | 
> | virtualclusters | Ja | 

## <a name="microsoftsqlvirtualmachine"></a>Micro soft. SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | sqlvirtualmachinegroups |  Nee |  
> | sqlvirtualmachines |  Nee |  


## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | Storage accounts | Ja<br/><br/> [Een Azure Storage-account naar een andere regio verplaatsen](../../storage/common/storage-account-move.md) | 

## <a name="microsoftstoragecache"></a>Micro soft. StorageCache

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | caches | Nee | 

## <a name="microsoftstoragesync"></a>Micro soft. StorageSync

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | storagesyncservices |  Nee | 

## <a name="microsoftstoragesyncdev"></a>Micro soft. StorageSyncDev

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | storagesyncservices | Nee | 

## <a name="microsoftstoragesyncint"></a>Micro soft. StorageSyncInt

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | storagesyncservices | Nee | 

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | leider | Nee | 

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | clusters | Nee |
> | streamingjobs |  Nee |  


## <a name="microsoftstreamanalyticsexplorer"></a>Micro soft. StreamAnalyticsExplorer

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | verschillend | Nee | 
> | vaak | Nee | 

## <a name="microsoftsubscription"></a>Micro soft. Subscription

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- | 
> | geabonneerd | Nee | 

## <a name="microsoftsupport"></a>micro soft. ondersteuning

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- | 
> | services | Nee | 
> | supporttickets | Nee | 

## <a name="microsoftsynapse"></a>Micro soft. Synapse

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- | 
> | workspaces | Nee | 
> | werk ruimten/bigdatapools | Nee | 
> | werk ruimten/sqlpools | Nee | 


## <a name="microsofttimeseriesinsights"></a>Micro soft. TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | verschillend |  Nee | 
> | omgevingen/eventsources |  Nee |  
> | omgevingen/referencedatasets |  Nee | 

## <a name="microsofttoken"></a>Micro soft. token

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | opslaat | Nee | 

## <a name="microsoftvirtualmachineimages"></a>Microsoft.VirtualMachineImages

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | imagetemplates | Nee | 

## <a name="microsoftvisualstudio"></a>micro soft. Visual Studio

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | account |  Nee | 
> | account/extensie |  Nee | 
> | account/project |  Nee | 

## <a name="microsoftvmware"></a>Micro soft. VMware

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- | 
> | arczones | Nee | 
> | resourcepools | Nee | 
> | vCenter | Nee | 
> | informatie | Nee | 
> | virtualmachinetemplates | Nee | 
> | virtualnetworks | Nee | 

## <a name="microsoftvmwarecloudsimple"></a>Micro soft. VMwareCloudSimple

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | dedicatedcloudnodes | Nee | 
> | dedicatedcloudservices | Nee | 
> | informatie | Nee | 

## <a name="microsoftvnfmanager"></a>Micro soft. VnfManager

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- | 
> | devices | Nee | 
> | vnfs | Nee | 

## <a name="microsoftvsonline"></a>Micro soft. VSOnline

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- | 
> | accounts | Nee | 
> | plant | Nee | 
> | registeredsubscriptions | Nee |


## <a name="microsoftweb"></a>Microsoft.Web

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | availablestacks | Nee | 
> | billingmeters | Nee | 
> | certificaten | Nee | 
> | connectiongateways |  Nee |  
> | inbel |  Nee |  
> | customapis |  Nee | 
> | deletedsites | Nee | 
> | deploymentlocations | Nee | 
> | georegio's | Nee | 
> | hostingenvironments | Nee | 
> | kubeenvironments | Nee | 
> | publishingusers | Nee |
> | aanbevelingen | Nee | 
> | resourcehealthmetadata | Nee | 
> | Runtimes | Nee | 
> | server farms | Nee |  
> | server farms/eventgridfilters | N
> | sites |  Nee | 
> | sites/premieraddons |  Nee |  
> | sites/sleuven |  Nee |  
> | sourcecontrols | Nee |
> | staticsites | Nee | 

## <a name="microsoftwindowsesu"></a>Micro soft. WindowsESU

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- | 
> | multipleactivationkeys | Nee |

## <a name="microsoftwindowsiot"></a>Micro soft. WindowsIoT

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | deviceservices | Nee | 

## <a name="microsoftworkloadbuilder"></a>Micro soft. WorkloadBuilder

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- | 
> | workloads | Nee | 

## <a name="microsoftworkloadmonitor"></a>Micro soft. WorkloadMonitor

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- | 
> | materialen | Nee |
> | componentssummary | Nee | 
> | monitorinstances | Nee | 
> | monitorinstancessummary | Nee | 
> | monitors | Nee | 
## <a name="third-party-services"></a>Services van derden

Services van derden bieden momenteel geen ondersteuning voor de verplaatsings bewerking.

## <a name="next-steps"></a>Volgende stappen

Meer [informatie](../../resource-mover/overview.md) over de resource-toedrijfings service.

