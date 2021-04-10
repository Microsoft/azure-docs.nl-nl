---
title: Ondersteuning voor het verplaatsen van Azure-resources in verschillende regio's
description: Een lijst met de Azure-resource typen die kunnen worden verplaatst over Azure-regio's
author: rayne-wiselman
ms.service: azure-resource-manager
ms.topic: reference
ms.date: 08/25/2020
ms.author: raynew
ms.openlocfilehash: 9229fca9f98aac4ca628c0bb25c13c9ba1989626
ms.sourcegitcommit: edc7dc50c4f5550d9776a4c42167a872032a4151
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105962591"
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
> | domainservices | No | 


## <a name="microsoftaadiam"></a>micro soft. aadiam

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | diagnosticsettings | No |
> | diagnosticsettingscategories | No |
> | privatelinkforazuread | No |
> | tenants |  No |

## <a name="microsoftaddons"></a>micro soft. Addons

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | supportproviders | No |

## <a name="microsoftadhybridhealthservice"></a>Microsoft.ADHybridHealthService

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- | 
> | aadsupportcases | No |
> | addsservices | No | 
> | middelen | No | 
> | anonymousapiusers | No |
> | configuratie | No | 
> | logboeken | No | 
> | rapporten | No | 
> | servicehealthmetrics | No | 
> | services | No | 

## <a name="microsoftadvisor"></a>Microsoft.Advisor

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- | 
> | configuraties | No | 
> | generaterecommendations | No |
> | metagegevens | No |
> | aanbevelingen | No |
> | onderdrukkingen | No | 

## <a name="microsoftalertsmanagement"></a>Micro soft. AlertsManagement

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | actionrules | No | 
> | waarschuwingen | No | 
> | alertslist | No | 
> | alertsmetadata | No | 
> | alertssummary | No | 
> | alertssummarylist | No | 
> | smartdetectoralertrules | No | 
> | smartgroups | No | 

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | Server | No |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | reportfeedback | No |
> | service |  Ja (met behulp van sjabloon) <br/><br/> [API Management verplaatsen tussen regio's](../../api-management/api-management-howto-migrate.md). | 

## <a name="microsoftappconfiguration"></a>Micro soft. AppConfiguration

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | configurationstores | No | 
> | configurationstores / eventgridfilters | No |

## <a name="microsoftappplatform"></a>Micro soft. AppPlatform

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- | 
> | lente | No | 

## <a name="microsoftappservice"></a>Micro soft. AppService

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | apiapps | Ja (met behulp van sjabloon)<br/><br/> [Een App Service-app naar een andere regio verplaatsen](../../app-service/manage-move-across-regions.md) | 
> | appidentities | No | 
> | gateways | No | 

## <a name="microsoftattestation"></a>Micro soft. Attestation

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- | 
> | attestationproviders | No | 

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | classicadministrators | No | 
> | dataaliases | No | 
> | denyassignments | No | 
> | elevateaccess | No | 
> | findorphanroleassignments | No | 
> | vergren delingen | No | 
> | permissions | No | 
> | policyassignments | No | 
> | policydefinitions | No | 
> | policysetdefinitions | No | 
> | privatelinkassociations | No | 
> | resourcemanagementprivatelinks | No | 
> | roleassignments | No | 
> | roleassignmentsusagemetrics | No | 
> | roledefinitions | No | 

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | automationaccounts | Ja (met behulp van sjabloon) <br/><br/> [Geo-replicatie gebruiken](../../automation/automation-managing-data.md#geo-replication-in-azure-automation) |  
> | automationaccounts/configuraties | No | 
> | automationaccounts/runbooks | No | 

## <a name="microsoftavs"></a>Micro soft. AVS

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | Abonnement |
> | ------------- | ----------- | 
> | privateclouds | No | 


## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | b2cdirectories | No | 
> | b2ctenants | No | 

## <a name="microsoftazuredata"></a>Micro soft. Azureworden

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | datacontrollers | No | 
> | hybriddatamanagers | No | 
> | postgresinstances | No | 
> | sqlinstances | No | 
> | sqlmanagedinstances | No |
> | sqlserverinstances | No | 
> | sqlserverregistrations | No |

## <a name="microsoftazurestack"></a>Micro soft. AzureStack

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | cloudmanifestfiles | No |
> | registraties | No | 

## <a name="microsoftazurestackhci"></a>Micro soft. AzureStackHCI

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- | 
> | clusters | No | 

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | batchaccounts |  Batch-accounts kunnen niet rechtstreeks worden verplaatst van de ene regio naar een andere, maar u kunt een sjabloon gebruiken om een sjabloon te exporteren, deze te wijzigen en de sjabloon te implementeren in de nieuwe regio. <br/><br/> Meer informatie over [het verplaatsen van een batch-account in meerdere regio's](../../batch/best-practices.md#moving-batch-accounts-across-regions) |

## <a name="microsoftbilling"></a>Microsoft.Billing

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- | 
> | billingaccounts | No | 
> | billingperiods | No | 
> | billingpermissions | No | 
> | billingproperty | No | 
> | billingroleassignments | No | 
> | billingroledefinitions | No | 
> | afdeling | No | 
> | enrollmentaccounts | No | 
> | factureer | No | 
> | Making | No | 

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | mapapis | No | 

## <a name="microsoftbiztalkservices"></a>Micro soft. BizTalkServices

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | biztalk | No | 

## <a name="microsoftblockchain"></a>Micro soft. Block Chain

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | blockchainmembers | No <br/><br/> Het block chain-netwerk kan geen knoop punten in verschillende regio's hebben. 
> | cordamembers | No |
> | Volg | No | 

## <a name="microsoftblockchaintokens"></a>Micro soft. BlockchainTokens

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | tokenservices | No |


## <a name="microsoftblueprint"></a>Micro soft. blauw druk

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | blueprintassignments | No | 
> | blauw drukken | No |

## <a name="microsoftbotservice"></a>Micro soft. BotService

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | botservices | No | 

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | redis | No | 
> | redisenterprise | No | 

## <a name="microsoftcapacity"></a>Micro soft. capacity

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- | 
> | appliedreservations | No | 
> | calculateexchange | No | 
> | calculateprice | No | 
> | calculatepurchaseprice | No | 
> | catalogi | No | 
> | commercialreservationorders | No | 
> | Exchange | No |
> | reservationorders | No | 
> | ringen | No | 
> | resources | No | 
> | validatereservationorder | No | 

## <a name="microsoftcdn"></a>Micro soft. CDN

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | cdnwebapplicationfirewallpolicies | No |
> | edgenodes | No
> | profielen | No | 
> | profielen/eind punten | No | 

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | certificateorders | No | 


## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | mogelijkheden | No | 
> | domein naam | No |
> | quotas | No | 
> | resourcetypes | No |
> | validatesubscriptionmoveavailability | No | 
> | informatie | No 

## <a name="microsoftclassicinfrastructuremigrate"></a>Micro soft. ClassicInfrastructureMigrate

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- | 
> | classicinfrastructureresources | No | 

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | mogelijkheden | No | 
> | expressroutecrossconnections | No | 
> | expressroutecrossconnections/peerings | No | 
> | gatewaysupporteddevices | No | 
> | networksecuritygroups | No |
> | quotas | No |
> | reservedips | No | 
> | virtualnetworks | No | 

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | cd's | No | 
> | images | No | 
> | osimages | No | 
> | osplatformimages | No | 
> | publicimages | No | 
> | quotas | No | 
> | Storage accounts | Yes |  
> | vmimages | No |

## <a name="microsoftclassicsubscription"></a>Micro soft. ClassicSubscription

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- | 
> | bewerkingen | No | 

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | accounts | No | 
> | Cognitive Search | Wordt ondersteund met hand matige stappen.<br/><br/> Meer informatie over [het verplaatsen van uw Azure Cognitive Search-service naar een andere regio](../../search/search-howto-move-across-regions.md)

## <a name="microsoftcommerce"></a>Microsoft.Commerce

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- | 
> | ratecard | No | 
> | usageaggregates | No | 

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | Availability sets | Yes <br/><br/> Gebruik [Azure resource](../../resource-mover/tutorial-move-region-virtual-machines.md) -overzetten om beschikbaarheids sets te verplaatsen. | 
> | diskaccesses | No |
> | diskencryptionsets | No | 
> | cd's | Yes <br/><br/> Gebruik [Azure resource](../../resource-mover/tutorial-move-region-virtual-machines.md) -overstap om Azure-vm's en gerelateerde schijven te verplaatsen. | 
> | Galerij | No | 
> | galerieën/afbeeldingen | No | 
> | galerieën/afbeeldingen/versies | No | 
> | hostgroups | No | 
> | hostgroups/hosts | No | 
> | images | No | 
> | proximityplacementgroups | No | 
> | restorepointcollections | No | 
> | sharedvmimages | No | 
> | sharedvmimages/versies | No | 
> | momentopnamen | No | 
> | sshpublickeys | No |
> | informatie | Yes <br/><br/> Gebruik [Azure resource](../../resource-mover/tutorial-move-region-virtual-machines.md) -overstap om Azure-vm's te verplaatsen. | 
> | informatie/extensies | No | 
> | virtualmachinescalesets | No | 

## <a name="microsoftconsumption"></a>Micro soft. verbruik

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- | 
> | aggregatedcost | No | 
> | saldi | No | 
> | budgetten | No | 
> | belastingen | No | 
> | costtags | No | 
> | aanvragen | No | 
> | events | No | 
> | Forecast | No | 
> | extra | No | 
> | markt plaatsen | No | 
> | pricesheets | No | 
> | producten | No | 
> | reservationdetails | No | 
> | reservationrecommendationdetails | No | 
> | reservationrecommendations | No | 
> | reservationsummaries | No | 
> | reservationtransactions | No | 
> | tags | No | 
> | tenants | No | 
> | inzake | No | 
> | usagedetails | No | 


## <a name="microsoftcontainerinstance"></a>Micro soft. ContainerInstance

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | containergroups | No | 
> | serviceassociationlinks | No |


## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | registers | No |  
> | registers/agentpools | No | 
> | registers/buildtasks | No |  
> | registers/replicaties | No | 
> | registers/taken | No |  
> | registers/webhooks | No | 

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | containerservices | No |
> | managedclusters | No | 
> | openshiftmanagedclusters | No | 

## <a name="microsoftcontentmoderator"></a>Microsoft.ContentModerator

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | toepassingen | No | 

## <a name="microsoftcortanaanalytics"></a>Micro soft. CortanaAnalytics

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | accounts | No | 

## <a name="microsoftcostmanagement"></a>Micro soft. CostManagement

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | waarschuwingen | No | 
> | billingaccounts | No | 
> | budgetten | No | 
> | cloudconnectors | No | 
> | connectoren | No | 
> | afdeling | No | 
> | hoogte | No | 
> | enrollmentaccounts | No | 
> | dump | No | 
> | externalbillingaccounts | No | 
> | forecast | No | 
> | query | No | 
> | registreren | No | 
> | reportconfigs | No | 
> | rapporten | No | 
> | instellingen | No | 
> | showbackrules | No | 
> | Weergaven | No | 

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | hubs | No |  

## <a name="microsoftcustomerlockbox"></a>Micro soft. CustomerLockbox

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- | 
> | requests | No | 

## <a name="microsoftcustomproviders"></a>Micro soft. CustomProviders

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | lidkoppelingen | No |
> | resourceproviders | No | 

## <a name="microsoftdatabox"></a>Micro soft. DataBox

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | functies | No | 

## <a name="microsoftdataboxedge"></a>Micro soft. DataBoxEdge

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | availableskus | No |
> | databoxedgedevices | No | 

## <a name="microsoftdatabricks"></a>Micro soft. Databricks

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | workspaces | No | 

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | catalogi | No | 
> | datacatalogs | No | 

## <a name="microsoftdataconnect"></a>Micro soft. DataConnect

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | connectionmanagers | No | 

## <a name="microsoftdataexchange"></a>Micro soft. DataExchange

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | Pakketten | No | 
> | plant | No | 

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | datafactories | No | 
> | factory's | No |  

## <a name="microsoftdatalake"></a>Micro soft. DataLake

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | datalakeaccounts | No | 

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | accounts | No | 

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | accounts | No | 

## <a name="microsoftdatamigration"></a>Micro soft. DataMigration

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | services | No | 
> | Services/projecten | No | 
> | sleuf | No | 

## <a name="microsoftdataprotection"></a>Micro soft. DataProtection

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- | ---------- |
> | backupvaults | No | 

## <a name="microsoftdatashare"></a>Micro soft. DataShare

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | accounts | No | 

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
> | servergroups | No | 
> | Server | U kunt een replicatie met meerdere regio's gebruiken om een bestaande server te verplaatsen. [Meer informatie](../../postgresql/howto-move-regions-portal.md).
> | serversv2 | No | 

## <a name="microsoftdeploymentmanager"></a>Micro soft. DeploymentManager

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | artifactsources | No | 
> | implementaties | No |  
> | servicetopologies | No | 
> | servicetopologies/Services | No |  
> | servicetopologies/Services/serviceunits | No | 
> | stappen | No | 


## <a name="microsoftdesktopvirtualization"></a>Micro soft. DesktopVirtualization

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- | 
> | applicationgroups | No | 
> | workspaces | No | 

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | elasticpools | Nee. De resource is niet beschikbaar.
> | elasticpools / iothubtenants | Nee. De resource is niet beschikbaar.
> | iothubs | Ja. [Meer informatie](../../iot-hub/iot-hub-how-to-clone.md)
> | provisioningservices | No | 

## <a name="microsoftdevops"></a>Micro soft. DevOps

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | fungeren | No | 


## <a name="microsoftdevspaces"></a>Micro soft. DevSpaces

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | fungeren | No | 
> | AKS-cluster | No<br/><br/> Meer [informatie](../../dev-spaces/faq.md#can-i-migrate-my-aks-cluster-with-azure-dev-spaces-to-another-region) over verplaatsen naar een andere regio.

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | labcenters | No | 
> | Labs | No | 
> | Labs/omgevingen | No |  
> | Labs-servicerunners | No | 
> | Labs-informatie | No |  
> | schema's | No |  

## <a name="microsoftdigitaltwins"></a>Micro soft. DigitalTwins

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- | 
> | digitaltwinsinstances | Ja, door resources opnieuw te maken in een nieuwe regio. [Meer informatie](../../digital-twins/how-to-move-regions.md) |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | databaseaccounts | No | 
> | databaseaccounts | No | 

## <a name="microsoftdomainregistration"></a>Micro soft. DomainRegistration

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | domeinen | No | 
> | generatessorequest | No | 
> | topleveldomains | No | 
> | validatedomainregistrationinformation | No |

## <a name="microsoftenterpriseknowledgegraph"></a>Micro soft. EnterpriseKnowledgeGraph

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | services | No |  

## <a name="microsofteventgrid"></a>Micro soft. EventGrid

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | domeinen | No | 
> | eventsubscriptions | No |
> | extensiontopics | No | 
> | partnernamespaces | No | 
> | partnerregistrations | No | 
> | partnertopics | No | 
> | systemtopics | No | 
> | onderwerp | No | 
> | topictypes | No | 

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | clusters | No |  
> | naam ruimten | Ja (met sjabloon)<br/><br/> [Een event hub-naam ruimte verplaatsen naar een andere regio](../../event-hubs/move-across-regions.md) | 
> | sku | No |  

## <a name="microsoftexperimentation"></a>Micro soft. experimenten

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- | 
> | experimentworkspaces | No | 

## <a name="microsoftfalcon"></a>Micro soft. Falcon

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- | 
> | naam ruimten | No | 

## <a name="microsoftfeatures"></a>Microsoft.Features

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- | 
> | featureproviders | No | 
> | features | Nee | 
> | providers | No | 
> | subscriptionfeatureregistrations | No | 

## <a name="microsoftgenomics"></a>Micro soft. Genomics

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | accounts | No | 

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- | 
> | automanagedaccounts | No | 
> | automanagedvmconfigurationprofiles | No | 
> | guestconfigurationassignments | No | 
> | software | No | 
> | softwareupdateprofile | No | 
> | softwareupdates | No | 

## <a name="microsofthanaonazure"></a>Micro soft. HanaOnAzure

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | hanainstances | No | 
> | sapmonitors | No |  

## <a name="microsofthardwaresecuritymodules"></a>Micro soft. HardwareSecurityModules

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- | 
> | dedicatedhsms | No | 


## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | clusters | No | 

## <a name="microsofthealthcareapis"></a>Micro soft. HealthcareApis

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | services | No |  

## <a name="microsofthybridcompute"></a>Microsoft.HybridCompute

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | apparaten | No | 
> | computers/uitbrei dingen | No |

## <a name="microsofthybriddata"></a>Micro soft. HybridData

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | datamanagers |  No | 

## <a name="microsofthybridnetwork"></a>Micro soft. HybridNetwork

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- | 
> | devices | No | 
> | vnfs | No | 

## <a name="microsofthydra"></a>Micro soft. Hydra

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- | 
> | materialen | No | 
> | networkscopes | No | 

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | functies |  No | 

## <a name="microsoftinsights"></a>micro soft. Insights

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | accounts | Nee. [Meer informatie](../../azure-monitor/faq.md#how-do-i-move-an-application-insights-resource-to-a-new-region). |
> | actiongroups |  No | 
> | activitylogalerts | No | 
> | alertrules |  No | 
> | autoscalesettings |  No | 
> | basislijn | No |
> | materialen |  No |  
> | datacollectionrules | No | 
> | diagnosticsettings | No | 
> | diagnosticsettingscategories | No | 
> | eventcategories | No | 
> | eventtypes | No | 
> | extendeddiagnosticsettings | No |
> | guestdiagnosticsettings | No | 
> | listmigrationdate | No | 
> | logdefinitions | No | 
> | logprofiles | No | 
> | logboeken | No |
> | metricalerts | No | 
> | metricbaselines | No | 
> | metricbatch | No | 
> | metricdefinitions | No | 
> | metricnamespaces | No | 
> | metrics | No | 
> | migratealertrules | No |
> | migratetonewpricingmodel | No | 
> | myworkbooks | No |
> | notificationgroups | No | 
> | privatelinkscopes | No |
> | rollbacktolegacypricingmodel | No |
> | scheduledqueryrules |  No | 
> | topologie | No |
> | transacties | No |
> | vminsightsonboardingstatuses | No |
> | webtests |  No | 
> | webtesten/gettestresultfile | No |
> | werkmappen |  No |  
> | workbooktemplates | No |


## <a name="microsoftiotcentral"></a>Micro soft. IoTCentral

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | apptemplates | No | 
> | iotapps | No | 



## <a name="microsoftiothub"></a>Microsoft.IotHub

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> |  iothub |  Ja (hub klonen) <br/><br/> [Een IoT-hub klonen naar een andere regio](../../iot-hub/iot-hub-how-to-clone.md)

## <a name="microsoftiotspaces"></a>Micro soft. IoTSpaces

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen |
> | ------------- | ----------- | 
> | Graph | No | 

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | deletedvaults | No |
> | hsmpools | No | 
> | managedhsms | No |
> | kluizen |  No | 

## <a name="microsoftkubernetes"></a>Microsoft.Kubernetes

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- | 
> | connectedclusters | No | 
> | registeredsubscriptions | No | 

## <a name="microsoftkubernetesconfiguration"></a>Micro soft. KubernetesConfiguration

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- | 
> | sourcecontrolconfigurations | No | 

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | clusters |  No |  

## <a name="microsoftlabservices"></a>Micro soft. LabServices

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | labaccounts | No | 
> | gebruikers | No | 

## <a name="microsoftlocationbasedservices"></a>Micro soft. LocationBasedServices

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | accounts | No | 

## <a name="microsoftlocationservices"></a>Micro soft. bestand locationservices

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | accounts | Nee, het is een wereld wijde service.

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | hostingenvironments | No | 
> | integrationaccounts |  No |  
> | integrationserviceenvironments | No | 
> | integrationserviceenvironments/beheerdeapi's | No |
> | isolatedenvironments | No | 
> | stroom |  No |  

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | commitmentplans |  No | 
> | webservices |  No | 
> | workspaces |  No | 

## <a name="microsoftmachinelearningcompute"></a>Micro soft. MachineLearningCompute

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | operationalizationclusters |  No | 

## <a name="microsoftmachinelearningexperimentation"></a>Micro soft. MachineLearningExperimentation

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | accounts | No | 
> | teamaccounts | No | 


## <a name="microsoftmachinelearningmodelmanagement"></a>Micro soft. MachineLearningModelManagement

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | accounts | No | 


## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | workspaces | No | 

## <a name="microsoftmaintenance"></a>Micro soft. onderhoud

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | configurationassignments | Ja. [Meer informatie](../../virtual-machines/move-region-maintenance-configuration.md) | 
> | maintenanceconfigurations | Ja. [Meer informatie](../../virtual-machines/move-region-maintenance-configuration-resources.md) |
> | updates | No | 

## <a name="microsoftmanagedidentity"></a>Micro soft. ManagedIdentity

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | identiteit | No | 
> | userassignedidentities | No | 

## <a name="microsoftmanagednetwork"></a>Micro soft. ManagedNetwork

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- | 
> | managednetworks | No | 
> | managednetworks / managednetworkgroups | No |
> | managednetworks / managednetworkpeeringpolicies | No | 
> | melding | No | 

## <a name="microsoftmanagedservices"></a>Micro soft. ManagedServices

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- | 
> | marketplaceregistrationdefinitions | No | 
> | registrationassignments | No |
> | registrationdefinitions | No | 

## <a name="microsoftmanagement"></a>Micro soft. Management

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- | 
> | getentities | No | 
> | managementgroups | No | 
> | managementgroups/instellingen | No | 
> | resources | No | 
> | starttenantbackfill | No | 
> | tenantbackfillstatus | No | 

## <a name="microsoftmaps"></a>Micro soft. Maps

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | accounts |  Nee, Azure Maps is een georuimtelijke service. 
> | accounts/privateatlases | No

## <a name="microsoftmarketplace"></a>Micro soft. Marketplace

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | over | No | 
> | offertypes | No | 
> | privategalleryitems | No | 
> | privatestoreclient | No | 
> | privatestores | No | 
> | producten | No | 
> | uitgevers | No | 
> | registreren | No | 

## <a name="microsoftmarketplaceapps"></a>Micro soft. MarketplaceApps

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- | 
> | classicdevservices | No | 

## <a name="microsoftmarketplaceordering"></a>Microsoft.MarketplaceOrdering

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- | 
> | gesloten | No | 
> | offertypes | No | 

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | Media Services |  No | 
> | Media Services/liveevents |  No | 
> | Media Services/streamingendpoints |  No | 

## <a name="microsoftmicroservices4spring"></a>Micro soft. Microservices4Spring

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | appclusters | No | 

## <a name="microsoftmigrate"></a>Micro soft. migrate

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | assessmentprojects | No | 
> | migrateprojects | No | 
> | movecollections | No
> | projecten | No | 

## <a name="microsoftmixedreality"></a>Micro soft. MixedReality

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- | ---------- |
> | holographicsbroadcastaccounts | No | 
> | objectunderstandingaccounts | No | 
> | remoterenderingaccounts | No | 
> | spatialanchorsaccounts | No | 

## <a name="microsoftnetapp"></a>Micro soft. NetApp

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | netappaccounts | No | 
> | netappaccounts / capacitypools | No | 
> | netappaccounts/capacitypools/volumes | No | 
> | netappaccounts/capacitypools/volumes/mounttargets | No | 
> | netappaccounts/capacitypools/volumes/moment opnamen | No | 

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | applicationgateways | No |
> | applicationgatewaywebapplicationfirewallpolicies | No | 
> | applicationsecuritygroups |  No |  
> | azurefirewalls |  No |  
> | bastionhosts | No | 
> | bgpservicecommunities | No |
> | inbel |  No | 
> | ddoscustompolicies |  No | 
> | ddosprotectionplans | No | 
> | dnszones |  No | 
> | expressroutecircuits | No | 
> | expressroutegateways | No | 
> | expressrouteserviceproviders | No | 
> | firewallpolicies | No |
> | frontdoors | No | 
> | ipallocations | No |
> | ipgroups | No |
> | loadbalancers | Yes <br/><br/> Gebruik [Azure resource](../../resource-mover/tutorial-move-region-virtual-machines.md) -overstap om interne en externe load balancers te verplaatsen. |
> | localnetworkgateways |  No | 
> | natgateways |  No | 
> | networkexperimentprofiles | No |
> | networkintentpolicies |  No | 
> | networkinterfaces | Yes <br/><br/> Gebruik [Azure resource](../../resource-mover/tutorial-move-region-virtual-machines.md) -overstap om nic's te verplaatsen. | 
> | networkprofiles | No | 
> | networksecuritygroups | Yes <br/><br/> Gebruik [Azure resource](../../resource-mover/tutorial-move-region-virtual-machines.md) -overzetten om netwerk beveiligings groepen (NGSs) te verplaatsen. | 
> | networkwatchers |  No |  
> | networkwatchers / connectionmonitors |  No | 
> | networkwatchers / flowlogs |  No | 
> | networkwatchers / pingmeshes |  No | 
> | p2svpngateways | No | 
> | privatednszones |  No |  
> | privatednszones / virtualnetworklinks | No |
> | privatednszonesinternal | No |
> | privateendpointredirectmaps | No |
> | privateendpoints | No | 
> | privatelinkservices | No | 
> | publicipaddresses | Yes<br/><br/> Gebruik [Azure resource](../../resource-mover/tutorial-move-region-virtual-machines.md) delegering om open bare IP-adressen te verplaatsen. |
> | publicipprefixes | No | 
> | routefilters | No | 
> | routetables |  No | 
> | securitypartnerproviders | No |
> | serviceendpointpolicies |  No | 
> | trafficmanagergeographichierarchies | No | 
> | trafficmanagerprofiles |  No | 
> | trafficmanagerusermetricskeys | No |
> | virtualhubs | No | 
> | virtualnetworkgateways |  No |  
> | virtualnetworks |  No | 
> | virtualnetworktaps | No | 
> | virtualwans | No | 
> | vpngateways (virtueel WAN) | No | 
> | vpnsites (virtueel WAN) | No | 
> | vpnsites (virtueel WAN) | No |


## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | naam ruimten |  No | 
> | naam ruimten/notification hubs |  No |  

## <a name="microsoftobjectstore"></a>Micro soft. ObjectStore

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- | 
> | osnamespaces | No | 

## <a name="microsoftoffazure"></a>Micro soft. OffAzure

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- | 
> | hypervsites | No | 
> | importsites | No | 
> | serversites | No | 
> | vmwaresites | No | 

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | clusters | No | 
> | deletedworkspaces | No | 
> | linktargets | No | 
> | storageinsightconfigs | No |
> | workspaces | No |



## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | managementassociations | No |
> | managementconfigurations |  No | 
> | oplossingen | No |
> | Weergaven |  No | 

## <a name="microsoftpeering"></a>Micro soft. peering

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | legacypeerings | No | 
> | peerasns | No | 
> | peeringlocations | No | 
> | Peerings | No | 
> | peeringservicecountries | No | 
> | peeringservicelocations | No | 
> | peeringserviceproviders | No | 
> | peeringservices | No | 

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- | 
> | policyevents | No | 
> | policystates | No | 
> | policytrackedresources | No | 
> | herstel | No | 

## <a name="microsoftportal"></a>Micro soft. Portal

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> |  -consoles | No |
> | dashboards | No | 
> | usersettings | No | 


## <a name="microsoftpowerbi"></a>Micro soft. PowerBI

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | workspacecollections |  No | 

## <a name="microsoftpowerbidedicated"></a>Micro soft. PowerBIDedicated

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | beschikt |  No | 

## <a name="microsoftpurview"></a>Microsoft.Purview

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | accounts | No | 

## <a name="microsoftproviderhub"></a>Micro soft. ProviderHub

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- | 
> | availableaccounts | No | 
> | providerregistrations | No | 
> | implementaties | No | 

## <a name="microsoftquantum"></a>Micro soft. Quantum

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- | 
> | workspaces | No | 

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | replicationeligibilityresults | No |
> | kluizen | Nee.<br/><br/> Het is niet mogelijk om Recovery Services kluizen voor Azure Backup over Azure-regio's te verplaatsen.<br/><br/> In Recovery Services kluizen voor Azure Site Recovery kunt u de kluis in de doel regio [uitschakelen en opnieuw maken](../../site-recovery/move-vaults-across-regions.md) . | 

## <a name="microsoftredhatopenshift"></a>Micro soft. RedHatOpenShift

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- | 
> | openshiftclusters | No | 

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | naam ruimten |  No | 

## <a name="microsoftresourcegraph"></a>Micro soft. ResourceGraph

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | aanvragen |  No |  
> | resourcechangedetails | No | 
> | resourcechanges | No | 
> | resources | No | 
> | resourceshistory | No | 
> | subscriptionsstatus | No | 

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- | 
> | childresources | No | 
> | emergingissues | No | 
> | events | No | 
> | metagegevens | No | 
> | meldingen | No | 

## <a name="microsoftresources"></a>Microsoft.Resources

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen |
> | ------------- | ----------- |
> | deploymentScripts |  Yes<br/><br/>[Resources van micro soft. resources naar een nieuwe regio verplaatsen](microsoft-resources-move-regions.md) |
> | templateSpecs |  Yes<br/><br/>[Resources van micro soft. resources naar een nieuwe regio verplaatsen](microsoft-resources-move-regions.md) |  

## <a name="microsoftsaas"></a>Micro soft. SaaS

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | toepassingen |  No | 
> | saasresources | No | 


## <a name="microsoftsearch"></a>Microsoft.Search

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | resourcehealthmetadata | No |
> | searchservices |  No | 


## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | adaptivenetworkhardenings | No | 
> | advancedthreatprotectionsettings | No | 
> | waarschuwingen | No | 
> | allowedconnections | No | 
> | applicationwhitelistings | No | 
> | assessmentmetadata | No | 
> | evaluaties | No | 
> | autodismissalertsrules | No | 
> | automatisering | No | 
> | autoprovisioningsettings | No |
> | complianceresults | No | 
> | ingebouwde strengste | No | 
> | datacollectionagents | No | 
> | devicesecuritygroups | No | 
> | discoveredsecuritysolutions | No | 
> | externalsecuritysolutions | No | 
> | informationprotectionpolicies | No | 
> | iotsecuritysolutions | No | 
> | iotsecuritysolutions / analyticsmodels | No | 
> | iotsecuritysolutions / analyticsmodels / aggregatedalerts | No | 
> | iotsecuritysolutions / analyticsmodels / aggregatedrecommendations | No | 
> | jitnetworkaccesspolicies | No | 
> | policies | No | 
> | prijzen | No | 
> | regulatorycompliancestandards | No | 
> | regulatorycompliancestandards / regulatorycompliancecontrols | No | 
> | regulatorycompliancestandards / regulatorycompliancecontrols / regulatorycomplianceassessments | No | 
> | securitycontacts | No | 
> | securitysolutions | No | 
> | securitysolutionsreferencedata | No | 
> | securitystatuses | No | 
> | securitystatusessummaries | No | 
> | servervulnerabilityassessments | No | 
> | instellingen | No | 
> | subevaluaties | No |
> | taken | No | 
> | topologieën | No | 
> | workspacesettings | No | 

## <a name="microsoftsecurityinsights"></a>Micro soft. SecurityInsights

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- | 
> | aggregaties | No | 
> | alertrules | No | 
> | alertruletemplates | No | 
> | automationrules | No |
> | meldingen | No | 
> | dataconnectors | No | 
> | entiteiten | No | 
> | entityqueries | No |
> | incidenten | No | 
> | officeconsents | No | 
> | instellingen | No | 
> | threatintelligence | No | 

## <a name="microsoftserialconsole"></a>Micro soft. SerialConsole

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- | 
> | consoleservices | No | 

## <a name="microsoftservermanagement"></a>Microsoft.ServerManagement

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | gateways | No | 
> | punt | No | 

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | naam ruimten |  No | 
> | premiummessagingregions | No | 
> | sku | No | 

## <a name="microsoftservicefabric"></a>Micro soft. ServiceFabric

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | toepassingen | No | 
> | clusters |  No |  
> | containergroups | No | 
> | containergroupsets | No | 
> | edgeclusters | No | 
> | managedclusters | No |
> | netwerken | No | 
> | secretstores | No | 
> | volumes | No | 

## <a name="microsoftservicefabricmesh"></a>Micro soft. ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | toepassingen |  No | 
> | containergroups | No | 
> | gateways |  No | 
> | netwerken |  No | 
> | geheimen |  No | 
> | volumes |  No |  

## <a name="microsoftservices"></a>Micro soft. Services

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- | 
> | implementaties | No | 

## <a name="microsoftsignalrservice"></a>Micro soft. SignalRService

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | signalr |  No |  

## <a name="microsoftsoftwareplan"></a>Micro soft. SoftwarePlan

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- | 
> | hybridusebenefits | No | 

## <a name="microsoftsolutions"></a>Micro soft. Solutions

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | appliancedefinitions | No | 
> | uitrusting | No | 
> | jitrequests | No | 

## <a name="microsoftsql"></a>Microsoft.Sql

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | instancepools | No | 
> | locaties | No |
> | managedinstances | Yes <br/><br/> Meer [informatie](../../azure-sql/database/move-resources-across-regions.md) over het verplaatsen van beheerde exemplaren tussen regio's. | 
> | managedinstances/data bases | Yes | 
> | Server | Yes | 
> | servers/data bases | Yes <br/><br/> Meer [informatie](../../azure-sql/database/move-resources-across-regions.md) over het verplaatsen van data bases in verschillende regio's.<br/><br/> Meer [informatie](../../resource-mover/tutorial-move-region-sql.md) over het gebruik van Azure resource-overstap voor het verplaatsen van Azure SQL-data bases.  | 
> | servers/elasticpools | Yes <br/><br/> Meer [informatie](../../azure-sql/database/move-resources-across-regions.md) over het verplaatsen van elastische Pools in verschillende regio's.<br/><br/> Meer [informatie](../../resource-mover/tutorial-move-region-sql.md) over het gebruik van Azure resource Move om elastische Azure SQL-Pools te verplaatsen.  | 
> | virtualclusters | Yes | 

## <a name="microsoftsqlvirtualmachine"></a>Micro soft. SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | sqlvirtualmachinegroups |  No |  
> | sqlvirtualmachines |  No |  


## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | Storage accounts | Yes<br/><br/> [Een Azure Storage-account naar een andere regio verplaatsen](../../storage/common/storage-account-move.md) | 

## <a name="microsoftstoragecache"></a>Micro soft. StorageCache

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | caches | No | 

## <a name="microsoftstoragesync"></a>Micro soft. StorageSync

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | storagesyncservices |  No | 

## <a name="microsoftstoragesyncdev"></a>Micro soft. StorageSyncDev

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | storagesyncservices | No | 

## <a name="microsoftstoragesyncint"></a>Micro soft. StorageSyncInt

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | storagesyncservices | No | 

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | leider | No | 

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | clusters | No |
> | streamingjobs |  No |  


## <a name="microsoftstreamanalyticsexplorer"></a>Micro soft. StreamAnalyticsExplorer

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | verschillend | No | 
> | vaak | No | 

## <a name="microsoftsubscription"></a>Micro soft. Subscription

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- | 
> | geabonneerd | No | 

## <a name="microsoftsupport"></a>micro soft. ondersteuning

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- | 
> | services | No | 
> | supporttickets | No | 

## <a name="microsoftsynapse"></a>Micro soft. Synapse

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- | 
> | workspaces | No | 
> | werk ruimten/bigdatapools | No | 
> | werk ruimten/sqlpools | No | 


## <a name="microsofttimeseriesinsights"></a>Micro soft. TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | verschillend |  No | 
> | omgevingen/eventsources |  No |  
> | omgevingen/referencedatasets |  No | 

## <a name="microsofttoken"></a>Micro soft. token

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | opslaat | No | 

## <a name="microsoftvirtualmachineimages"></a>Microsoft.VirtualMachineImages

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | imagetemplates | No | 

## <a name="microsoftvisualstudio"></a>micro soft. Visual Studio

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | account |  No | 
> | account/extensie |  No | 
> | account/project |  No | 

## <a name="microsoftvmware"></a>Micro soft. VMware

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- | 
> | arczones | No | 
> | resourcepools | No | 
> | vCenter | No | 
> | informatie | No | 
> | virtualmachinetemplates | No | 
> | virtualnetworks | No | 

## <a name="microsoftvmwarecloudsimple"></a>Micro soft. VMwareCloudSimple

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | dedicatedcloudnodes | No | 
> | dedicatedcloudservices | No | 
> | informatie | No | 

## <a name="microsoftvnfmanager"></a>Micro soft. VnfManager

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- | 
> | devices | No | 
> | vnfs | No | 

## <a name="microsoftvsonline"></a>Micro soft. VSOnline

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- | 
> | accounts | No | 
> | plant | No | 
> | registeredsubscriptions | No |


## <a name="microsoftweb"></a>Microsoft.Web

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | availablestacks | No | 
> | billingmeters | No | 
> | certificaten | No | 
> | connectiongateways |  No |  
> | inbel |  No |  
> | customapis |  No | 
> | deletedsites | No | 
> | deploymentlocations | No | 
> | georegio's | No | 
> | hostingenvironments | No | 
> | kubeenvironments | No | 
> | publishingusers | No |
> | aanbevelingen | No | 
> | resourcehealthmetadata | No | 
> | Runtimes | No | 
> | server farms | No |  
> | server farms/eventgridfilters | N
> | sites |  No | 
> | sites/premieraddons |  No |  
> | sites/sleuven |  No |  
> | sourcecontrols | No |
> | staticsites | No | 

## <a name="microsoftwindowsesu"></a>Micro soft. WindowsESU

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- | 
> | multipleactivationkeys | No |

## <a name="microsoftwindowsiot"></a>Micro soft. WindowsIoT

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | deviceservices | No | 

## <a name="microsoftworkloadbuilder"></a>Micro soft. WorkloadBuilder

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- | 
> | workloads | No | 

## <a name="microsoftworkloadmonitor"></a>Micro soft. WorkloadMonitor

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- | 
> | materialen | No |
> | componentssummary | No | 
> | monitorinstances | No | 
> | monitorinstancessummary | No | 
> | monitors | No | 
## <a name="third-party-services"></a>Services van derden

Services van derden bieden momenteel geen ondersteuning voor de verplaatsings bewerking.

## <a name="next-steps"></a>Volgende stappen

Meer [informatie](../../resource-mover/overview.md) over de resource-toedrijfings service.

