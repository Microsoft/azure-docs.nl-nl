---
title: Ondersteuning voor verplaatsen van bewerking op resourcetype
description: Een lijst met de Azure-resourcetypen die kunnen worden verplaatst naar een nieuwe resourcegroep, een nieuw abonnement of een nieuwe regio.
ms.topic: conceptual
ms.date: 04/16/2021
ms.openlocfilehash: c159b6e5f64f3052a6584034aa58b058b1426b16
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107725562"
---
# <a name="move-operation-support-for-resources"></a>Ondersteuning voor verplaatsen van resources

In dit artikel wordt vermeld of een Azure-resourcetype de bewerking voor verplaatsen ondersteunt. Het bevat ook informatie over speciale voorwaarden die u moet overwegen bij het verplaatsen van een resource.

> [!IMPORTANT]
> In de meeste gevallen kan een onderliggende resource niet onafhankelijk van de bovenliggende resource worden verplaatst. Onderliggende resources hebben een resourcetype in de indeling `<resource-provider-namespace>/<parent-resource>/<child-resource>` . is bijvoorbeeld `Microsoft.ServiceBus/namespaces/queues` een onderliggende resource van `Microsoft.ServiceBus/namespaces` . Wanneer u de bovenliggende resource verplaatst, wordt de onderliggende resource automatisch verplaatst. Als u in dit artikel geen onderliggende resource ziet, kunt u ervan uitgaan dat deze is verplaatst met de bovenliggende resource. Als de bovenliggende resource geen ondersteuning biedt voor verplaatsen, kan de onderliggende resource niet worden verplaatst.

Ga naar de naamruimte van een resourceprovider:
> [!div class="op_single_selector"]
> - [Microsoft.AAD](#microsoftaad)
> - [microsoft.aambuam](#microsoftaadiam)
> - [Microsoft.Addons](#microsoftaddons)
> - [Microsoft.ADHybridHealthService](#microsoftadhybridhealthservice)
> - [Microsoft.Advisor](#microsoftadvisor)
> - [Microsoft.AlertsManagement](#microsoftalertsmanagement)
> - [Microsoft.AnalysisServices](#microsoftanalysisservices)
> - [Microsoft.ApiManagement](#microsoftapimanagement)
> - [Microsoft.AppConfiguration](#microsoftappconfiguration)
> - [Microsoft.AppPlatform](#microsoftappplatform)
> - [Microsoft.AppService](#microsoftappservice)
> - [Microsoft.Attestation](#microsoftattestation)
> - [Microsoft.Authorization](#microsoftauthorization)
> - [Microsoft.Automation](#microsoftautomation)
> - [Microsoft.AVS](#microsoftavs)
> - [Microsoft.AzureActiveDirectory](#microsoftazureactivedirectory)
> - [Microsoft.AzureData](#microsoftazuredata)
> - [Microsoft.AzureStack](#microsoftazurestack)
> - [Microsoft.AzureStackHCI](#microsoftazurestackhci)
> - [Microsoft.Batch](#microsoftbatch)
> - [Microsoft.Billing](#microsoftbilling)
> - [Microsoft.BingMaps](#microsoftbingmaps)
> - [Microsoft.BizTalkServices](#microsoftbiztalkservices)
> - [Microsoft.Blockchain](#microsoftblockchain)
> - [Microsoft.BlockchainTokens](#microsoftblockchaintokens)
> - [Microsoft.Blueprint](#microsoftblueprint)
> - [Microsoft.BotService](#microsoftbotservice)
> - [Microsoft.Cache](#microsoftcache)
> - [Microsoft.Capacity](#microsoftcapacity)
> - [Microsoft.Cdn](#microsoftcdn)
> - [Microsoft.CertificateRegistration](#microsoftcertificateregistration)
> - [Microsoft.ClassicCompute](#microsoftclassiccompute)
> - [Microsoft.ClassicInfrastructureMigrate](#microsoftclassicinfrastructuremigrate)
> - [Microsoft.ClassicNetwork](#microsoftclassicnetwork)
> - [Microsoft.ClassicStorage](#microsoftclassicstorage)
> - [Microsoft.ClassicSubscription](#microsoftclassicsubscription)
> - [Microsoft.CognitiveServices](#microsoftcognitiveservices)
> - [Microsoft.Commerce](#microsoftcommerce)
> - [Microsoft.Compute](#microsoftcompute)
> - [Microsoft.Consumption](#microsoftconsumption)
> - [Microsoft.ContainerInstance](#microsoftcontainerinstance)
> - [Microsoft.ContainerRegistry](#microsoftcontainerregistry)
> - [Microsoft.ContainerService](#microsoftcontainerservice)
> - [Microsoft.ContentModerator](#microsoftcontentmoderator)
> - [Microsoft.CortanaAnalytics](#microsoftcortanaanalytics)
> - [Microsoft.CostManagement](#microsoftcostmanagement)
> - [Microsoft.CustomerInsights](#microsoftcustomerinsights)
> - [Microsoft.CustomerLockbox](#microsoftcustomerlockbox)
> - [Microsoft.CustomProviders](#microsoftcustomproviders)
> - [Microsoft.DataBox](#microsoftdatabox)
> - [Microsoft.DataBoxEdge](#microsoftdataboxedge)
> - [Microsoft.Databricks](#microsoftdatabricks)
> - [Microsoft.DataCatalog](#microsoftdatacatalog)
> - [Microsoft.DataConnect](#microsoftdataconnect)
> - [Microsoft.DataExchange](#microsoftdataexchange)
> - [Microsoft.DataFactory](#microsoftdatafactory)
> - [Microsoft.DataLake](#microsoftdatalake)
> - [Microsoft.DataLakeAnalytics](#microsoftdatalakeanalytics)
> - [Microsoft.DataLakeStore](#microsoftdatalakestore)
> - [Microsoft.DataMigration](#microsoftdatamigration)
> - [Microsoft.DataProtection](#microsoftdataprotection)
> - [Microsoft.DataShare](#microsoftdatashare)
> - [Microsoft.DBforMariaDB](#microsoftdbformariadb)
> - [Microsoft.DBforMySQL](#microsoftdbformysql)
> - [Microsoft.DBforPostgreSQL](#microsoftdbforpostgresql)
> - [Microsoft.DeploymentManager](#microsoftdeploymentmanager)
> - [Microsoft.DesktopVirtualization](#microsoftdesktopvirtualization)
> - [Microsoft.Devices](#microsoftdevices)
> - [Microsoft.DevOps](#microsoftdevops)
> - [Microsoft.DevSpaces](#microsoftdevspaces)
> - [Microsoft.DevTestLab](#microsoftdevtestlab)
> - [Microsoft.DigitalTwins](#microsoftdigitaltwins)
> - [Microsoft.DocumentDB](#microsoftdocumentdb)
> - [Microsoft.DomainRegistration](#microsoftdomainregistration)
> - [Microsoft.EnterpriseKnowledgeGraph](#microsoftenterpriseknowledgegraph)
> - [Microsoft.EventGrid](#microsofteventgrid)
> - [Microsoft.EventHub](#microsofteventhub)
> - [Microsoft.Experimentation](#microsoftexperimentation)
> - [Microsoft.Hebto](#microsoftfalcon)
> - [Microsoft.Features](#microsoftfeatures)
> - [Microsoft.Genomics](#microsoftgenomics)
> - [Microsoft.GuestConfiguration](#microsoftguestconfiguration)
> - [Microsoft.HanaOnAzure](#microsofthanaonazure)
> - [Microsoft.HardwareSecurityModules](#microsofthardwaresecuritymodules)
> - [Microsoft.HDInsight](#microsofthdinsight)
> - [Microsoft.HealthcareApis](#microsofthealthcareapis)
> - [Microsoft.HybridCompute](#microsofthybridcompute)
> - [Microsoft.HybridData](#microsofthybriddata)
> - [Microsoft.HybridNetwork](#microsofthybridnetwork)
> - [Microsoft.Keer](#microsofthydra)
> - [Microsoft.ImportExport](#microsoftimportexport)
> - [microsoft.insights](#microsoftinsights)
> - [Microsoft.IoTCentral](#microsoftiotcentral)
> - [Microsoft.IoTSpaces](#microsoftiotspaces)
> - [Microsoft.KeyVault](#microsoftkeyvault)
> - [Microsoft.Kubernetes](#microsoftkubernetes)
> - [Microsoft.KubernetesConfiguration](#microsoftkubernetesconfiguration)
> - [Microsoft.Kusto](#microsoftkusto)
> - [Microsoft.LabServices](#microsoftlabservices)
> - [Microsoft.LocationBasedServices](#microsoftlocationbasedservices)
> - [Microsoft.LocationServices](#microsoftlocationservices)
> - [Microsoft.Logic](#microsoftlogic)
> - [Microsoft.MachineLearning](#microsoftmachinelearning)
> - [Microsoft.MachineLearningCompute](#microsoftmachinelearningcompute)
> - [Microsoft.MachineLearningExperimentation](#microsoftmachinelearningexperimentation)
> - [Microsoft.MachineLearningModelManagement](#microsoftmachinelearningmodelmanagement)
> - [Microsoft.MachineLearningServices](#microsoftmachinelearningservices)
> - [Microsoft.Maintenance](#microsoftmaintenance)
> - [Microsoft.ManagedIdentity](#microsoftmanagedidentity)
> - [Microsoft.ManagedNetwork](#microsoftmanagednetwork)
> - [Microsoft.ManagedServices](#microsoftmanagedservices)
> - [Microsoft.Management](#microsoftmanagement)
> - [Microsoft.Maps](#microsoftmaps)
> - [Microsoft.Marketplace](#microsoftmarketplace)
> - [Microsoft.MarketplaceApps](#microsoftmarketplaceapps)
> - [Microsoft.MarketplaceOrdering](#microsoftmarketplaceordering)
> - [Microsoft.Media](#microsoftmedia)
> - [Microsoft.Microservices4Spring](#microsoftmicroservices4spring)
> - [Microsoft.Migrate](#microsoftmigrate)
> - [Microsoft.MixedReality](#microsoftmixedreality)
> - [Microsoft.NetApp](#microsoftnetapp)
> - [Microsoft.Network](#microsoftnetwork)
> - [Microsoft.NotificationHubs](#microsoftnotificationhubs)
> - [Microsoft.ObjectStore](#microsoftobjectstore)
> - [Microsoft.OffAzure](#microsoftoffazure)
> - [Microsoft.OperationalInsights](#microsoftoperationalinsights)
> - [Microsoft.OperationsManagement](#microsoftoperationsmanagement)
> - [Microsoft.Peering](#microsoftpeering)
> - [Microsoft.PolicyInsights](#microsoftpolicyinsights)
> - [Microsoft.Portal](#microsoftportal)
> - [Microsoft.PowerBI](#microsoftpowerbi)
> - [Microsoft.PowerBIDedicated](#microsoftpowerbidedicated)
> - [Microsoft.ProjectBalkylon](#microsoftprojectbabylon)
> - [Microsoft.ProviderHub](#microsoftproviderhub)
> - [Microsoft.Quantum](#microsoftquantum)
> - [Microsoft.RecoveryServices](#microsoftrecoveryservices)
> - [Microsoft.RedHatOpenShift](#microsoftredhatopenshift)
> - [Microsoft.Relay](#microsoftrelay)
> - [Microsoft.ResourceGraph](#microsoftresourcegraph)
> - [Microsoft.ResourceHealth](#microsoftresourcehealth)
> - [Microsoft.Resources](#microsoftresources)
> - [Microsoft.SaaS](#microsoftsaas)
> - [Microsoft.Search](#microsoftsearch)
> - [Microsoft.Security](#microsoftsecurity)
> - [Microsoft.SecurityInsights](#microsoftsecurityinsights)
> - [Microsoft.SerialConsole](#microsoftserialconsole)
> - [Microsoft.ServerManagement](#microsoftservermanagement)
> - [Microsoft.ServiceBus](#microsoftservicebus)
> - [Microsoft.ServiceFabric](#microsoftservicefabric)
> - [Microsoft.ServiceFabricMesh](#microsoftservicefabricmesh)
> - [Microsoft.Services](#microsoftservices)
> - [Microsoft.SignalRService](#microsoftsignalrservice)
> - [Microsoft.SoftwarePlan](#microsoftsoftwareplan)
> - [Microsoft.Solutions](#microsoftsolutions)
> - [Microsoft.Sql](#microsoftsql)
> - [Microsoft.SqlVirtualMachine](#microsoftsqlvirtualmachine)
> - [Microsoft.Storage](#microsoftstorage)
> - [Microsoft.StorageCache](#microsoftstoragecache)
> - [Microsoft.StorageSync](#microsoftstoragesync)
> - [Microsoft.StorageSyncDev](#microsoftstoragesyncdev)
> - [Microsoft.StorageSyncInt](#microsoftstoragesyncint)
> - [Microsoft.StorSimple](#microsoftstorsimple)
> - [Microsoft.StreamAnalytics](#microsoftstreamanalytics)
> - [Microsoft.StreamAnalyticsExplorer](#microsoftstreamanalyticsexplorer)
> - [Microsoft.Subscription](#microsoftsubscription)
> - [microsoft.support](#microsoftsupport)
> - [Microsoft.Synapse](#microsoftsynapse)
> - [Microsoft.TimeSeriesInsights](#microsofttimeseriesinsights)
> - [Microsoft.Token](#microsofttoken)
> - [Microsoft.VirtualMachineImages](#microsoftvirtualmachineimages)
> - [microsoft.visualstudio](#microsoftvisualstudio)
> - [Microsoft.VMware](#microsoftvmware)
> - [Microsoft.VMwareCloudSimple](#microsoftvmwarecloudsimple)
> - [Microsoft.VnfManager](#microsoftvnfmanager)
> - [Microsoft.VSOnline](#microsoftvsonline)
> - [Microsoft.Web](#microsoftweb)
> - [Microsoft.WindowsESU](#microsoftwindowsesu)
> - [Microsoft.WindowsIoT](#microsoftwindowsiot)
> - [Microsoft.WorkloadBuilder](#microsoftworkloadbuilder)
> - [Microsoft.WorkloadMonitor](#microsoftworkloadmonitor)

## <a name="microsoftaad"></a>Microsoft.AAD

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement | Regio verplaatsen |
> | ------------- | ----------- | ---------- | ----------- |
> | domainservices | Nee | Nee |  Nee |

## <a name="microsoftaadiam"></a>microsoft.aambuam

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement | Regio verplaatsen |
> | ------------- | ----------- | ---------- | ----------- |
> | diagnosticsettings | Nee | Nee | Nee |
> | diagnosticsettingscategories | Nee | Nee | Nee |
> | privatelinkforazuread | Ja | Ja | Nee |
> | Huurders | Ja | Ja | Nee |

## <a name="microsoftaddons"></a>Microsoft.Addons

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement | Regio verplaatsen |
> | ------------- | ----------- | ---------- | ----------- |
> | supportproviders | Nee | Nee | Nee |

## <a name="microsoftadhybridhealthservice"></a>Microsoft.ADHybridHealthService

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement | Regio verplaatsen |
> | ------------- | ----------- | ---------- | ----------- |
> | aadsupportcases | Nee | Nee | Nee |
> | addsservices | Nee | Nee | Nee |
> | Agenten | Nee | Nee | Nee |
> | anonymousapiusers | Nee | Nee | Nee |
> | configuratie | Nee | Nee | Nee |
> | logboeken | Nee | Nee | Nee |
> | rapporten | Nee | Nee | Nee |
> | servicehealthmetrics | Nee | Nee | Nee |
> | services | Nee | Nee | Nee |

## <a name="microsoftadvisor"></a>Microsoft.Advisor

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement | Regio verplaatsen |
> | ------------- | ----------- | ---------- | ----------- |
> | Configuraties | Nee | Nee | Nee |
> | generaterecommendations | Nee | Nee | Nee |
> | metagegevens | Nee | Nee | Nee |
> | aanbevelingen | Nee | Nee | Nee |
> | onderdrukkingen | Nee | Nee | Nee |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement | Regio verplaatsen |
> | ------------- | ----------- | ---------- | ----------- |
> | actionrules | Ja | Ja | Nee |
> | waarschuwingen | Nee | Nee | Nee |
> | waarschuwingenlijst | Nee | Nee | Nee |
> | alertsmetadata | Nee | Nee | Nee |
> | alertssummary | Nee | Nee | Nee |
> | alertssummarylist | Nee | Nee | Nee |
> | smartdetectoralertrules | Ja | Ja | Nee |
> | smartgroups | Nee | Nee | Nee |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement | Regio verplaatsen |
> | ------------- | ----------- | ---------- | ----------- |
> | Servers | Ja | Ja | Nee |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!IMPORTANT]
> Een API Management service die is ingesteld op de verbruiks-SKU, kan niet worden verplaatst.

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement | Regio verplaatsen |
> | ------------- | ----------- | ---------- | ----------- |
> | reportfeedback | Nee | Nee | Nee |
> | service | Ja | Ja | Ja (met behulp van sjabloon) <br/><br/> [Verplaats API Management tussen regio's.](../../api-management/api-management-howto-migrate.md) |

## <a name="microsoftappconfiguration"></a>Microsoft.AppConfiguration

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement | Regio verplaatsen |
> | ------------- | ----------- | ---------- | ----------- |
> | configurationstores | Ja | Ja | Nee |
> | configurationstores /eventgridfilters | Nee | Nee | Nee |

## <a name="microsoftappplatform"></a>Microsoft.AppPlatform

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement | Regio verplaatsen |
> | ------------- | ----------- | ---------- | ----------- |
> | Voorjaar | Ja | Ja | Nee |

## <a name="microsoftappservice"></a>Microsoft.AppService

> [!IMPORTANT]
> Zie [App Service richtlijnen voor verplaatsen.](./move-limitations/app-service-move-limitations.md)

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement | Regio verplaatsen |
> | ------------- | ----------- | ---------- | ----------- |
> | apiapps | Nee | Nee | Ja (met behulp van sjabloon)<br/><br/> [Een app App Service naar een andere regio verplaatsen](../../app-service/manage-move-across-regions.md) |
> | appidentities | Nee | Nee | Nee |
> | Gateways | Nee | Nee | Nee |

## <a name="microsoftattestation"></a>Microsoft.Attestation

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement | Regio verplaatsen |
> | ------------- | ----------- | ---------- | ----------- |
> | attestationproviders | Ja | Ja | Nee |

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement | Regio verplaatsen |
> | ------------- | ----------- | ---------- | ----------- |
> | classicadministrators | Nee | Nee | Nee |
> | dataaliases | Nee | Nee | Nee |
> | dessignments | Nee | Nee | Nee |
> | elevateaccess | Nee | Nee | Nee |
> | finhanroleassignments | Nee | Nee | Nee |
> | Sloten | Nee | Nee | Nee |
> | permissions | Nee | Nee | Nee |
> | beleidsassignments | Nee | Nee | Nee |
> | policydefinitions | Nee | Nee | Nee |
> | policysetdefinitions | Nee | Nee | Nee |
> | privatelinkassociations | Nee | Nee | Nee |
> | resourcemanagementprivatelinks | Nee | Nee | Nee |
> | roleassignments | Nee | Nee | Nee |
> | roleassignmentsusagemetrics | Nee | Nee | Nee |
> | roledefinitions | Nee | Nee | Nee |

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!IMPORTANT]
> Runbooks moeten bestaan in dezelfde resourcegroep als het Automation-account.
>
> Zie Move [your Azure Automation account to another subscription (Uw account Azure Automation naar een ander abonnement) voor meer informatie.](../../automation/how-to/move-account.md?toc=/azure/azure-resource-manager/toc.json)

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement | Regio verplaatsen |
> | ------------- | ----------- | ---------- | ----------- |
> | automationaccounts | Ja | Ja | Ja (met behulp van sjabloon) <br/><br/> [Geo-replicatie gebruiken](../../automation/automation-managing-data.md#geo-replication-in-azure-automation) |
> | automationaccounts/configuraties | Ja | Ja | Nee |
> | automationaccounts/runbooks | Ja | Ja | Nee |

## <a name="microsoftavs"></a>Microsoft.AVS

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement | Regio verplaatsen |
> | ------------- | ----------- | ---------- | ----------- |
> | privéclouds | Ja | Ja | Nee |

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement | Regio verplaatsen |
> | ------------- | ----------- | ---------- | ----------- |
> | b2cdirectories | Ja | Ja | Nee |
> | b2ctenants | Nee | Nee | Nee |

## <a name="microsoftazuredata"></a>Microsoft.AzureData

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement | Regio verplaatsen |
> | ------------- | ----------- | ---------- | ----------- |
> | datacontrollers | Nee | Nee | Nee |
> | hybriddatamanagers | Nee | Nee | Nee |
> | postgresinstances | Nee | Nee | Nee |
> | sqlinstances | Nee | Nee | Nee |
> | sqlmanagedinstances | Nee | Nee | Nee |
> | sqlserverinstances | Nee | Nee | Nee |
> | sqlserverregistrations | Ja | Ja | Nee |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement | Regio verplaatsen |
> | ------------- | ----------- | ---------- | ----------- |
> | cloudmanifestfiles | Nee | Nee | Nee |
> | Registraties | Ja | Ja | Nee |

## <a name="microsoftazurestackhci"></a>Microsoft.AzureStackHCI

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement | Regio verplaatsen |
> | ------------- | ----------- | ---------- | ----------- |
> | Clusters | Nee | Nee | Nee |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement | Regio verplaatsen |
> | ------------- | ----------- | ---------- | ----------- |
> | batchaccounts | Ja | Ja | Batch-accounts kunnen niet rechtstreeks van de ene regio naar de andere worden verplaatst, maar u kunt wel een sjabloon gebruiken om een sjabloon te exporteren, te wijzigen en de sjabloon in de nieuwe regio te implementeren. <br/><br/> Meer informatie over [het verplaatsen van een Batch-account tussen regio's](../../batch/best-practices.md#moving-batch-accounts-across-regions) |

## <a name="microsoftbilling"></a>Microsoft.Billing

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement | Regio verplaatsen |
> | ------------- | ----------- | ---------- | ----------- |
> | factureringsaccounts | Nee | Nee | Nee |
> | factureringsperioden | Nee | Nee | Nee |
> | factureringsgegevens | Nee | Nee | Nee |
> | billingproperty | Nee | Nee | Nee |
> | billingroleassignments | Nee | Nee | Nee |
> | billingroledefinitions | Nee | Nee | Nee |
> | Afdelingen | Nee | Nee | Nee |
> | enrollmentaccounts | Nee | Nee | Nee |
> | Facturen | Nee | Nee | Nee |
> | Transfers | Nee | Nee | Nee |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement | Regio verplaatsen |
> | ------------- | ----------- | ---------- | ----------- |
> | mapapis | Nee | Nee | Nee |

## <a name="microsoftbiztalkservices"></a>Microsoft.BizTalkServices

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement | Regio verplaatsen |
> | ------------- | ----------- | ---------- | ----------- |
> | biztalk | Nee | Nee | Nee |

## <a name="microsoftblockchain"></a>Microsoft.Blockchain

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement | Regio verplaatsen |
> | ------------- | ----------- | ---------- | ----------- |
> | blockchainmembers | Nee | Nee | Nee <br/><br/> Het blockchainnetwerk kan geen knooppunten in verschillende regio's hebben. |
> | cordamembers | Nee | Nee | Nee |
> | Watchers | Nee | Nee | Nee |

## <a name="microsoftblockchaintokens"></a>Microsoft.BlockchainTokens

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement | Regio verplaatsen |
> | ------------- | ----------- | ---------- | ----------- |
> | tokenservices | Nee | Nee | Nee |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement | Regio verplaatsen |
> | ------------- | ----------- | ---------- | ----------- |
> | blueprintassignments | Nee | Nee | Nee |
> | Blauwdrukken | Nee | Nee | Nee |

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement | Regio verplaatsen |
> | ------------- | ----------- | ---------- | ----------- |
> | botservices | Ja | Ja | Nee |

## <a name="microsoftcache"></a>Microsoft.Cache

> [!IMPORTANT]
> Als het Azure Cache voor Redis is geconfigureerd met een virtueel netwerk, kan het exemplaar niet worden verplaatst naar een ander abonnement. Zie [Beperkingen voor het verplaatsen van netwerken.](./move-limitations/networking-move-limitations.md)

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement | Regio verplaatsen |
> | ------------- | ----------- | ---------- | ----------- |
> | Redis | Ja | Ja | Nee |
> | redisenterprise | Nee | Nee | Nee |

## <a name="microsoftcapacity"></a>Microsoft.Capacity

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement | Regio verplaatsen |
> | ------------- | ----------- | ---------- | ----------- |
> | appliedreservations | Nee | Nee | Nee |
> | calculateexchange | Nee | Nee | Nee |
> | calculateprice | Nee | Nee | Nee |
> | calculatepurchaseprice | Nee | Nee | Nee |
> | Catalogi | Nee | Nee | Nee |
> | commercialreservationorders | Nee | Nee | Nee |
> | Exchange | Nee | Nee | Nee |
> | reserveringsorders | Nee | Nee | Nee |
> | Reserveringen | Nee | Nee | Nee |
> | resources | Nee | Nee | Nee |
> | validatereservationorder | Nee | Nee | Nee |

## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement | Regio verplaatsen |
> | ------------- | ----------- | ---------- | ----------- |
> | cdnwebapplicationfirewallmanagedrulesets | Nee | Nee | Nee |
> | cdnwebapplicationfirewallpolicies | Ja | Ja | Nee |
> | edgenodes | Nee | Nee | Nee |
> | Profielen | Ja | Ja | Nee |
> | profielen/eindpunten | Ja | Ja | Nee |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!IMPORTANT]
> Zie [App Service richtlijnen voor verplaatsen.](./move-limitations/app-service-move-limitations.md)

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement | Regio verplaatsen |
> | ------------- | ----------- | ---------- | ----------- |
> | certificateorders | Ja | Ja | Nee |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!IMPORTANT]
> Zie [Richtlijnen voor het verplaatsen van klassieke implementaties.](./move-limitations/classic-model-move-limitations.md) Klassieke implementatieresources kunnen worden verplaatst tussen abonnementen met een bewerking die specifiek is voor dat scenario.

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement | Regio verplaatsen |
> | ------------- | ----------- | ---------- | ----------- |
> | mogelijkheden | Nee | Nee | Nee |
> | Domeinnamen | Ja | Nee | Nee |
> | quotas | Nee | Nee | Nee |
> | resourcetypen | Nee | Nee | Nee |
> | validatesubscriptionmoveavailability | Nee | Nee | Nee |
> | virtualmachines | Ja | Ja | Nee |

## <a name="microsoftclassicinfrastructuremigrate"></a>Microsoft.ClassicInfrastructureMigrate

> [!IMPORTANT]
> Zie [Richtlijnen voor het verplaatsen van klassieke implementaties.](./move-limitations/classic-model-move-limitations.md) Klassieke implementatieresources kunnen worden verplaatst tussen abonnementen met een bewerking die specifiek is voor dat scenario.

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement | Regio verplaatsen |
> | ------------- | ----------- | ---------- | ----------- |
> | classicinfrastructureresources | Nee | Nee | Nee |

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

> [!IMPORTANT]
> Zie [Richtlijnen voor het verplaatsen van klassieke implementaties.](./move-limitations/classic-model-move-limitations.md) Klassieke implementatieresources kunnen worden verplaatst tussen abonnementen met een bewerking die specifiek is voor dat scenario.

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement | Regio verplaatsen |
> | ------------- | ----------- | ---------- | ----------- |
> | mogelijkheden | Nee | Nee | Nee |
> | expressroutecrossconnections | Nee | Nee | Nee |
> | expressroutecrossconnections/peerings | Nee | Nee | Nee |
> | gatewaysupporteddevices | Nee | Nee | Nee |
> | networksecuritygroups | Nee | Nee | Nee |
> | quotas | Nee | Nee | Nee |
> | reservedips | Nee | Nee | Nee |
> | virtualnetworks | Nee | Nee | Nee |

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!IMPORTANT]
> Zie [Richtlijnen voor het verplaatsen van klassieke implementaties.](./move-limitations/classic-model-move-limitations.md) Klassieke implementatieresources kunnen worden verplaatst tussen abonnementen met een bewerking die specifiek is voor dat scenario.

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement | Regio verplaatsen |
> | ------------- | ----------- | ---------- | ----------- |
> | Schijven | Nee | Nee | Nee |
> | images | Nee | Nee | Nee |
> | osimages | Nee | Nee | Nee |
> | osplatformimages | Nee | Nee | Nee |
> | publicimages | Nee | Nee | Nee |
> | quotas | Nee | Nee | Nee |
> | storageaccounts | Ja | Nee | Ja |
> | vmimages | Nee | Nee | Nee |

## <a name="microsoftclassicsubscription"></a>Microsoft.ClassicSubscription

> [!IMPORTANT]
> Zie [Richtlijnen voor het verplaatsen van klassieke implementaties.](./move-limitations/classic-model-move-limitations.md) Klassieke implementatieresources kunnen worden verplaatst tussen abonnementen met een bewerking die specifiek is voor dat scenario.

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement | Regio verplaatsen |
> | ------------- | ----------- | ---------- | ----------- |
> | bewerkingen | Nee | Nee | Nee |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement | Regio verplaatsen |
> | ------------- | ----------- | ---------- | ----------- |
> | accounts | Ja | Ja | Nee |
> | Cognitive Search | **Hangende** | **Hangende** | Ondersteund met handmatige stappen.<br/><br/> Meer informatie [over het verplaatsen van Azure Cognitive Search service naar een andere regio](../../search/search-howto-move-across-regions.md) |

## <a name="microsoftcommerce"></a>Microsoft.Commerce

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement | Regio verplaatsen |
> | ------------- | ----------- | ---------- | ----------- |
> | ratecard | Nee | Nee | Nee |
> | usageaggregates | Nee | Nee | Nee |

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!IMPORTANT]
> Zie [Virtual Machines richtlijnen voor verplaatsen.](./move-limitations/virtual-machines-move-limitations.md)

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement | Regio verplaatsen |
> | ------------- | ----------- | ---------- | ----------- |
> | beschikbaarheidssets | Ja | Ja |  Ja <br/><br/> Gebruik [Azure Resource Mover](../../resource-mover/tutorial-move-region-virtual-machines.md) om beschikbaarheidssets te verplaatsen. |
> | diskaccccccss | Nee | Nee | Nee |
> | diskencryptionsets | Nee | Nee | Nee |
> | Schijven | Ja | Ja | Ja <br/><br/> Gebruik [Azure Resource Mover](../../resource-mover/tutorial-move-region-virtual-machines.md) om Azure-VM's en gerelateerde schijven te verplaatsen. |
> | Galeries | Nee | Nee | Nee |
> | galerieën/afbeeldingen | Nee | Nee | Nee |
> | galerieën/afbeeldingen/versies | Nee | Nee | Nee |
> | hostgroepen | Nee | Nee | Nee |
> | hostgroepen/hosts | Nee | Nee | Nee |
> | images | Ja | Ja | Nee |
> | proximityplacementgroups | Ja | Ja | Nee |
> | restorepointcollections | Nee | Nee | Nee |
> | restorepointcollections/restorepoints | Nee | Nee | Nee |
> | sharedvmextensions | Nee | Nee | Nee |
> | sharedvmimages | Nee | Nee | Nee |
> | sharedvmimages /versions | Nee | Nee | Nee |
> | momentopnamen | Ja | Ja | Nee |
> | sshpublickeys | Nee | Nee | Nee |
> | virtualmachines | Ja | Ja | Ja <br/><br/> Gebruik [Azure Resource Mover](../../resource-mover/tutorial-move-region-virtual-machines.md) azure-VM's te verplaatsen. |
> | virtualmachines/extensies | Ja | Ja | Nee |
> | virtualmachinescalesets | Ja | Ja | Nee |

## <a name="microsoftconsumption"></a>Microsoft.Consumption

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement | Regio verplaatsen |
> | ------------- | ----------- | ---------- | ----------- |
> | aggregatedcost | Nee | Nee | Nee |
> | Saldi | Nee | Nee | Nee |
> | Begrotingen | Nee | Nee | Nee |
> | Kosten | Nee | Nee | Nee |
> | costtags | Nee | Nee | Nee |
> | Credits | Nee | Nee | Nee |
> | events | Nee | Nee | Nee |
> | Prognoses | Nee | Nee | Nee |
> | Veel | Nee | Nee | Nee |
> | Markten | Nee | Nee | Nee |
> | prijzenoverzichten | Nee | Nee | Nee |
> | Producten | Nee | Nee | Nee |
> | reservationdetails | Nee | Nee | Nee |
> | reservationrecommendationdetails | Nee | Nee | Nee |
> | reservationrecommendations | Nee | Nee | Nee |
> | reserveringsummaries | Nee | Nee | Nee |
> | reserveringstransacties | Nee | Nee | Nee |
> | tags | Nee | Nee | Nee |
> | Huurders | Nee | Nee | Nee |
> | Voorwaarden | Nee | Nee | Nee |
> | usagedetails | Nee | Nee | Nee |

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement | Regio verplaatsen |
> | ------------- | ----------- | ---------- | ----------- |
> | containergroepen | Nee | Nee | Nee |
> | serviceassociationlinks | Nee | Nee | Nee |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement | Regio verplaatsen |
> | ------------- | ----------- | ---------- | ----------- |
> | Registers | Ja | Ja | Nee |
> | registers/agentpools | Ja | Ja | Nee |
> | registers /buildtasks | Ja | Ja | Nee |
> | registers/replicaties | Ja | Ja | Nee |
> | registers/taken | Ja | Ja | Nee |
> | registers/webhooks | Ja | Ja | Nee |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement | Regio verplaatsen |
> | ------------- | ----------- | ---------- | ----------- |
> | containerservices | Nee | Nee | Nee |
> | managedclusters | Nee | Nee | Nee |
> | openshiftmanagedclusters | Nee | Nee | Nee |

## <a name="microsoftcontentmoderator"></a>Microsoft.ContentModerator

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement | Regio verplaatsen |
> | ------------- | ----------- | ---------- | ----------- |
> | toepassingen | Nee | Nee | Nee |

## <a name="microsoftcortanaanalytics"></a>Microsoft.CortanaAnalytics

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement | Regio verplaatsen |
> | ------------- | ----------- | ---------- | ----------- |
> | accounts | Nee | Nee | Nee |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement | Regio verplaatsen |
> | ------------- | ----------- | ---------- | ----------- |
> | waarschuwingen | Nee | Nee | Nee |
> | factureringsaccounts | Nee | Nee | Nee |
> | Begrotingen | Nee | Nee | Nee |
> | cloudconnectors | Nee | Nee | Nee |
> | connectoren | Ja | Ja | Nee |
> | Afdelingen | Nee | Nee | Nee |
> | Dimensies | Nee | Nee | Nee |
> | enrollmentaccounts | Nee | Nee | Nee |
> | Export | Nee | Nee | Nee |
> | externalbillingaccounts | Nee | Nee | Nee |
> | forecast | Nee | Nee | Nee |
> | query | Nee | Nee | Nee |
> | registreren | Nee | Nee | Nee |
> | reportconfigs | Nee | Nee | Nee |
> | rapporten | Nee | Nee | Nee |
> | instellingen | Nee | Nee | Nee |
> | showbackrules | Nee | Nee | Nee |
> | Weergaven | Nee | Nee | Nee |

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement | Regio verplaatsen |
> | ------------- | ----------- | ---------- | ----------- |
> | hubs | Nee | Nee | Nee |

## <a name="microsoftcustomerlockbox"></a>Microsoft.CustomerLockbox

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement | Regio verplaatsen |
> | ------------- | ----------- | ---------- | ----------- |
> | requests | Nee | Nee | Nee |

## <a name="microsoftcustomproviders"></a>Microsoft.CustomProviders

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement | Regio verplaatsen |
> | ------------- | ----------- | ---------- | ----------- |
> | Verenigingen | Nee | Nee | Nee |
> | resourceproviders | Ja | Ja | Nee |

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement | Regio verplaatsen |
> | ------------- | ----------- | ---------- | ----------- |
> | Banen | Nee | Nee | Nee |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement | Regio verplaatsen |
> | ------------- | ----------- | ---------- | ----------- |
> | availableskus | Nee | Nee | Nee |
> | databoxedgedevices | Nee | Nee | Nee |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement | Regio verplaatsen |
> | ------------- | ----------- | ---------- | ----------- |
> | workspaces | Nee | Nee | Nee |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement | Regio verplaatsen |
> | ------------- | ----------- | ---------- | ----------- |
> | Catalogi | Ja | Ja | Nee |
> | datacatalogs | Nee | Nee | Nee |

## <a name="microsoftdataconnect"></a>Microsoft.DataConnect

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement | Regio verplaatsen |
> | ------------- | ----------- | ---------- | ----------- |
> | connectionmanagers | Nee | Nee | Nee |

## <a name="microsoftdataexchange"></a>Microsoft.DataExchange

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement | Regio verplaatsen |
> | ------------- | ----------- | ---------- | ----------- |
> | Pakketten | Nee | Nee | Nee |
> | Plannen | Nee | Nee | Nee |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement | Regio verplaatsen |
> | ------------- | ----------- | ---------- | ----------- |
> | gegevensfactory's | Ja | Ja | Nee |
> | Fabrieken | Ja | Ja | Nee |

## <a name="microsoftdatalake"></a>Microsoft.DataLake

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement | Regio verplaatsen |
> | ------------- | ----------- | ---------- | ----------- |
> | datalakeaccounts | Nee | Nee | Nee |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement | Regio verplaatsen |
> | ------------- | ----------- | ---------- | ----------- |
> | accounts | Ja | Ja | Nee |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement | Regio verplaatsen |
> | ------------- | ----------- | ---------- | ----------- |
> | accounts | Ja | Ja | Nee |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement | Regio verplaatsen |
> | ------------- | ----------- | ---------- | ----------- |
> | services | Nee | Nee | Nee |
> | services/projecten | Nee | Nee | Nee |
> | Slots | Nee | Nee | Nee |

## <a name="microsoftdataprotection"></a>Microsoft.DataProtection

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement | Regio verplaatsen |
> | ------------- | ----------- | ---------- | ---------- |
> | backupvaults | Nee | Nee | Nee |

## <a name="microsoftdatashare"></a>Microsoft.DataShare

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement | Regio verplaatsen |
> | ------------- | ----------- | ---------- | ----------- |
> | accounts | Ja | Ja | Nee |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement | Regio verplaatsen |
> | ------------- | ----------- | ---------- | ----------- |
> | Servers | Ja | Ja | U kunt een leesreplica tussen regio's gebruiken om een bestaande server te verplaatsen. [Meer informatie](../../postgresql/howto-move-regions-portal.md).<br/><br/> Als de service is ingericht met geografisch redundante back-upopslag, kunt u geo-herstel gebruiken om te herstellen in andere regio's. [Meer informatie](../../mariadb/concepts-business-continuity.md#recover-from-an-azure-regional-data-center-outage).

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement | Regio verplaatsen |
> | ------------- | ----------- | ---------- | ----------- |
> | flexibleServers | Nee | Nee | Nee |
> | Servers | Ja | Ja | U kunt een leesreplica tussen regio's gebruiken om een bestaande server te verplaatsen. [Meer informatie](../../mysql/howto-move-regions-portal.md).

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement | Regio verplaatsen |
> | ------------- | ----------- | ---------- | ----------- |
> | flexibleServers | Nee | Nee | Nee |
> | servergroepen | Nee | Nee | Nee |
> | Servers | Ja | Ja | U kunt een leesreplica tussen regio's gebruiken om een bestaande server te verplaatsen. [Meer informatie.](../../postgresql/howto-move-regions-portal.md)
> | serversv2 | Ja | Ja | Nee |
> | singleservers | Ja | Ja | Nee |

## <a name="microsoftdeploymentmanager"></a>Microsoft.DeploymentManager

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement | Regio verplaatsen |
> | ------------- | ----------- | ---------- | ----------- |
> | artifactsources | Ja | Ja | Nee |
> | implementaties | Ja | Ja | Nee |
> | servicetopologies | Ja | Ja | Nee |
> | servicetopologies/services | Ja | Ja | Nee |
> | servicetopologies/services/serviceunits | Ja | Ja | Nee |
> | stappen | Ja | Ja | Nee |

## <a name="microsoftdesktopvirtualization"></a>Microsoft.DesktopVirtualization

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement | Regio verplaatsen |
> | ------------- | ----------- | ---------- | ----------- |
> | applicationgroups | Ja | Ja | Nee |
> | hostpools | Ja | Ja | Nee |
> | workspaces | Ja | Ja | Nee |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement | Regio verplaatsen |
> | ------------- | ----------- | ---------- | ----------- |
> | elasticpools | Nee | Nee | Nee. De resource wordt niet blootgesteld. |
> | elasticpools /iothubtenants | Nee | Nee | Nee. De resource wordt niet blootgesteld. |
> | iothubs | Ja | Ja | Ja. [Meer informatie](../../iot-hub/iot-hub-how-to-clone.md) |
> | provisioningservices | Ja | Ja | Nee |

## <a name="microsoftdevops"></a>Microsoft.DevOps

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement | Regio verplaatsen |
> | ------------- | ----------- | ---------- | ----------- |
> | Pijpleidingen | Ja | Ja | Nee |
> | Controllers | **Hangende** | **Hangende** | No |

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement | Regio verplaatsen |
> | ------------- | ----------- | ---------- | ----------- |
> | Controllers | Ja | Ja | Nee |
> | AKS-cluster | **Hangende** | **Hangende** | No<br/><br/> [Meer informatie over](../../dev-spaces/faq.md#can-i-migrate-my-aks-cluster-with-azure-dev-spaces-to-another-region) het verplaatsen naar een andere regio.

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement | Regio verplaatsen |
> | ------------- | ----------- | ---------- | ----------- |
> | labcenters | Nee | Nee | Nee |
> | Labs | Ja | Nee | Nee |
> | labs/omgevingen | Ja | Ja | Nee |
> | labs/servicerunners | Ja | Ja | Nee |
> | labs /virtualmachines | Ja | Nee | Nee |
> | Planningen | Ja | Ja | Nee |

## <a name="microsoftdigitaltwins"></a>Microsoft.DigitalTwins

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement | Regio verplaatsen |
> | ------------- | ----------- | ---------- | ----------- |
> | digitaltwinsinstances | Nee | Nee | Ja, door resources opnieuw te maken in een nieuwe regio. [Meer informatie](../../digital-twins/how-to-move-regions.md) |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement | Regio verplaatsen |
> | ------------- | ----------- | ---------- | ----------- |
> | databaseaccountnames | Nee | Nee | Nee |
> | databaseaccounts | Ja | Ja | Nee |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement | Regio verplaatsen |
> | ------------- | ----------- | ---------- | ----------- |
> | Domeinen | Ja | Ja | Nee |
> | generatessorequest | Nee | Nee | Nee |
> | topleveldomains | Nee | Nee | Nee |
> | validatedomainregistrationinformation | Nee | Nee | Nee |

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft.EnterpriseKnowledgeGraph

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement | Regio verplaatsen |
> | ------------- | ----------- | ---------- | ----------- |
> | services | Ja | Ja | Nee |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement | Regio verplaatsen |
> | ------------- | ----------- | ---------- | ----------- |
> | Domeinen | Ja | Ja | Nee |
> | eventsubscriptions | Nee: kan niet onafhankelijk worden verplaatst, maar automatisch worden verplaatst met geabonneerde resource. | Nee: kan niet onafhankelijk worden verplaatst, maar automatisch worden verplaatst met een geabonneerde resource. | No |
> | extensiontopics | Nee | Nee | Nee |
> | partnernamespaces | Ja | Ja | Nee |
> | partnerregisters | Nee | Nee | Nee |
> | partnertopics | Ja | Ja | Nee |
> | systemtopics | Ja | Ja | Nee |
> | Onderwerpen | Ja | Ja | Nee |
> | onderwerptypen | Nee | Nee | Nee |

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement | Regio verplaatsen |
> | ------------- | ----------- | ---------- | ----------- |
> | Clusters | Ja | Ja | Nee |
> | Naamruimten | Ja | Ja | Ja (met sjabloon)<br/><br/> [Een Event Hub-naamruimte verplaatsen naar een andere regio](../../event-hubs/move-across-regions.md) |
> | sku | Nee | Nee | Nee |

## <a name="microsoftexperimentation"></a>Microsoft.Experimentation

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement | Regio verplaatsen |
> | ------------- | ----------- | ---------- | ----------- |
> | experimentworkspaces | Nee | Nee | Nee |

## <a name="microsoftfalcon"></a>Microsoft.Hebto

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement | Regio verplaatsen |
> | ------------- | ----------- | ---------- | ----------- |
> | Naamruimten | Ja | Ja | Nee |

## <a name="microsoftfeatures"></a>Microsoft.Features

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement | Regio verplaatsen |
> | ------------- | ----------- | ---------- | ----------- |
> | featureproviders | Nee | Nee | Nee |
> | features | Nee | Nee | Nee |
> | providers | Nee | Nee | Nee |
> | subscriptionfeatureregistrations | Nee | Nee | Nee |

## <a name="microsoftgenomics"></a>Microsoft.Genomics

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement | Regio verplaatsen |
> | ------------- | ----------- | ---------- | ----------- |
> | accounts | Nee | Nee | Nee |

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement | Regio verplaatsen |
> | ------------- | ----------- | ---------- | ----------- |
> | automanagedaccounts | Nee | Nee | Nee |
> | automanagedvmconfigurationprofiles | Nee | Nee | Nee |
> | guestconfigurationassignments | Nee | Nee | Nee |
> | Software | Nee | Nee | Nee |
> | softwareupdateprofile | Nee | Nee | Nee |
> | softwareupdates | Nee | Nee | Nee |

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement | Regio verplaatsen |
> | ------------- | ----------- | ---------- | ----------- |
> | werkdomeinen | Nee | Nee | Nee |
> | sapmonitors | Nee | Nee | Nee |

## <a name="microsofthardwaresecuritymodules"></a>Microsoft.HardwareSecurityModules

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement | Regio verplaatsen |
> | ------------- | ----------- | ---------- | ----------- |
> | dedicatedhsms | Nee | Nee | Nee |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!IMPORTANT]
> U kunt HDInsight-clusters verplaatsen naar een nieuw abonnement of een nieuwe resourcegroep. U kunt de netwerkbronnen die zijn gekoppeld aan het HDInsight-cluster (zoals het virtuele netwerk, de NIC of het load balancer) echter niet verplaatsen tussen abonnementen. Bovendien kunt u niet verplaatsen naar een nieuwe resourcegroep een NIC die is gekoppeld aan een virtuele machine voor het cluster.
>
> Wanneer u een HDInsight-cluster verplaatst naar een nieuw abonnement, moet u eerst andere resources verplaatsen (zoals het opslagaccount). Verplaats vervolgens het HDInsight-cluster zelf.

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement | Regio verplaatsen |
> | ------------- | ----------- | ---------- | ----------- |
> | Clusters | Ja | Ja | Nee |

## <a name="microsofthealthcareapis"></a>Microsoft.HealthcareApis

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement | Regio verplaatsen |
> | ------------- | ----------- | ---------- | ----------- |
> | services | Ja | Ja | Nee |

## <a name="microsofthybridcompute"></a>Microsoft.HybridCompute

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement | Regio verplaatsen |
> | ------------- | ----------- | ---------- | ----------- |
> | Machines | Ja | Ja | Nee |
> | machines/extensies | Ja | Ja | Nee |

## <a name="microsofthybriddata"></a>Microsoft.HybridData

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement | Regio verplaatsen |
> | ------------- | ----------- | ---------- | ----------- |
> | datamanagers | Ja | Ja | Nee |

## <a name="microsofthybridnetwork"></a>Microsoft.HybridNetwork

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement | Regio verplaatsen |
> | ------------- | ----------- | ---------- | ----------- |
> | devices | Nee | Nee | Nee |
> | vnfs | Nee | Nee | Nee |

## <a name="microsofthydra"></a>Microsoft.Keer

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement | Regio verplaatsen |
> | ------------- | ----------- | ---------- | ----------- |
> | Onderdelen | Nee | Nee | Nee |
> | networkscopes | Nee | Nee | Nee |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement | Regio verplaatsen |
> | ------------- | ----------- | ---------- | ----------- |
> | Banen | Ja | Ja | Nee |

## <a name="microsoftinsights"></a>Microsoft.Insights

> [!IMPORTANT]
> Zorg ervoor dat de overstap naar een nieuw abonnement de abonnementsquota [niet overschrijdt.](azure-subscription-service-limits.md#azure-monitor-limits)

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement | Regio verplaatsen |
> | ------------- | ----------- | ---------- | ----------- |
> | accounts | **Hangende** | **Hangende** | Nee. [Meer informatie](../../azure-monitor/faq.md#how-do-i-move-an-application-insights-resource-to-a-new-region). |
> | actiegroepen | Ja | Ja | Nee |
> | activitylogalerts | Nee | Nee | Nee |
> | waarschuwingsrules | Ja | Ja | Nee |
> | automatische schaalinstellingen | Ja | Ja | Nee |
> | basislijn | Nee | Nee | Nee |
> | Onderdelen | Ja | Ja | Nee |
> | datacollectionrules | Nee | Nee | Nee |
> | diagnosticsettings | Nee | Nee | Nee |
> | diagnosticsettingscategories | Nee | Nee | Nee |
> | eventcategories | Nee | Nee | Nee |
> | eventtypes | Nee | Nee | Nee |
> | extendeddiagnosticsettings | Nee | Nee | Nee |
> | guestdiagnosticsettings | Nee | Nee | Nee |
> | listmigrationdate | Nee | Nee | Nee |
> | logdefinitions | Nee | Nee | Nee |
> | logprofiles | Nee | Nee | Nee |
> | logboeken | Nee | Nee | Nee |
> | metricalerts | Nee | Nee | Nee |
> | metricbaselines | Nee | Nee | Nee |
> | metricbatch | Nee | Nee | Nee |
> | metricdefinitions | Nee | Nee | Nee |
> | metricnamespaces | Nee | Nee | Nee |
> | metrics | Nee | Nee | Nee |
> | migratealertrules | Nee | Nee | Nee |
> | migratetonewpricingmodel | Nee | Nee | Nee |
> | myworkbooks | Nee | Nee | Nee |
> | notificationgroups | Nee | Nee | Nee |
> | privatelinkscopes | Nee | Nee | Nee |
> | rollbacktolegacypricingmodel | Nee | Nee | Nee |
> | scheduledqueryrules | Ja | Ja | Nee |
> | topologie | Nee | Nee | Nee |
> | transacties | Nee | Nee | Nee |
> | vminsightsonboardingstatussen | Nee | Nee | Nee |
> | webtests | Ja | Ja | Nee |
> | webtests /gettestresultfile | Nee | Nee | Nee |
> | werkmappen | Ja | Ja | Nee |
> | workbooktemplates | Ja | Ja | Nee |

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement | Regio verplaatsen |
> | ------------- | ----------- | ---------- | ----------- |
> | apptemplates | Nee | Nee | Nee |
> | iotapps | Ja | Ja | Nee |

## <a name="microsoftiothub"></a>Microsoft.IotHub

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement | Regio verplaatsen |
> | ------------- | ----------- | ---------- | ----------- |
> | iothub | **Hangende** | **Hangende** | Ja (kloonhub) <br/><br/> [Een IoT-hub klonen naar een andere regio](../../iot-hub/iot-hub-how-to-clone.md) |

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement | Regio verplaatsen |
> | ------------- | ----------- | ---------- | ----------- |
> | Grafiek | Ja | Ja | Nee |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!IMPORTANT]
> Sleutelkluizen die worden gebruikt voor schijfversleuteling kunnen niet worden verplaatst naar een resourcegroep in hetzelfde abonnement of tussen abonnementen.

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement | Regio verplaatsen |
> | ------------- | ----------- | ---------- | ----------- |
> | deletedvaults | Nee | Nee | Nee |
> | hsmpools | Nee | Nee | Nee |
> | managedhsms | Nee | Nee | Nee |
> | Kluizen | Ja | Ja | Nee |

## <a name="microsoftkubernetes"></a>Microsoft.Kubernetes

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement | Regio verplaatsen |
> | ------------- | ----------- | ---------- | ----------- |
> | verbondenclusters | Ja | Ja | Nee |
> | registeredsubscriptions | Nee | Nee | Nee |

## <a name="microsoftkubernetesconfiguration"></a>Microsoft.KubernetesConfiguration

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement | Regio verplaatsen |
> | ------------- | ----------- | ---------- | ----------- |
> | sourcecontrolconfigurations | Nee | Nee | Nee |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement | Regio verplaatsen |
> | ------------- | ----------- | ---------- | ----------- |
> | Clusters | Ja | Ja | Nee |

## <a name="microsoftlabservices"></a>Microsoft.LabServices

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement | Regio verplaatsen |
> | ------------- | ----------- | ---------- | ----------- |
> | labaccounts | Nee | Nee | Nee |
> | gebruikers | Nee | Nee | Nee |

## <a name="microsoftlocationbasedservices"></a>Microsoft.LocationBasedServices

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement | Regio verplaatsen |
> | ------------- | ----------- | ---------- | ----------- |
> | accounts | Nee | Nee | Nee |

## <a name="microsoftlocationservices"></a>Microsoft.LocationServices

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement | Regio verplaatsen |
> | ------------- | ----------- | ---------- | ----------- |
> | accounts | Nee | Nee | Nee, het is een wereldwijde service. |

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement | Regio verplaatsen |
> | ------------- | ----------- | ---------- | ----------- |
> | hostingenvironments | Nee | Nee | Nee |
> | integrationaccounts | Ja | Ja | Nee |
> | integrationserviceenvironments | Ja | Nee | Nee |
> | integrationserviceenvironments /managedapis | Ja | Nee | Nee |
> | geïsoleerde omgevingen | Nee | Nee | Nee |
> | Werkstromen | Ja | Ja | Nee |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement | Regio verplaatsen |
> | ------------- | ----------- | ---------- | ----------- |
> | commitmentplans | Nee | Nee | Nee |
> | Webservices | Ja | Nee | Nee |
> | workspaces | Ja | Ja | Nee |

## <a name="microsoftmachinelearningcompute"></a>Microsoft.MachineLearningCompute

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement | Regio verplaatsen |
> | ------------- | ----------- | ---------- | ----------- |
> | operationalizationclusters | Nee | Nee | Nee |

## <a name="microsoftmachinelearningexperimentation"></a>Microsoft.MachineLearningExperimentation

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement | Regio verplaatsen |
> | ------------- | ----------- | ---------- | ----------- |
> | accounts | Nee | Nee | Nee |
> | teamaccounts | Nee | Nee | Nee |

## <a name="microsoftmachinelearningmodelmanagement"></a>Microsoft.MachineLearningModelManagement

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement | Regio verplaatsen |
> | ------------- | ----------- | ---------- | ----------- |
> | accounts | Nee | Nee | Nee |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement | Regio verplaatsen |
> | ------------- | ----------- | ---------- | ----------- |
> | workspaces | Nee | Nee | Nee |

## <a name="microsoftmaintenance"></a>Microsoft.Maintenance

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement | Regio verplaatsen |
> | ------------- | ----------- | ---------- | ----------- |
> | configurationassignments | Nee | Nee | Ja. [Meer informatie](../../virtual-machines/move-region-maintenance-configuration.md) |
> | onderhoudsconfiguraties | Ja | Ja | Ja. [Meer informatie](../../virtual-machines/move-region-maintenance-configuration-resources.md) |
> | updates | Nee | Nee | Nee |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement | Regio verplaatsen |
> | ------------- | ----------- | ---------- | ----------- |
> | Identiteiten | Nee | Nee | Nee |
> | userassignedidentities | Nee | Nee | Nee |

## <a name="microsoftmanagednetwork"></a>Microsoft.ManagedNetwork

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement | Regio verplaatsen |
> | ------------- | ----------- | ---------- | ----------- |
> | managednetworks | Nee | Nee | Nee |
> | managednetworks/managednetworkgroups | Nee | Nee | Nee |
> | managednetworks/managednetworkpeeringpolicies | Nee | Nee | Nee |
> | melding | Nee | Nee | Nee |

## <a name="microsoftmanagedservices"></a>Microsoft.ManagedServices

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement | Regio verplaatsen |
> | ------------- | ----------- | ---------- | ----------- |
> | marketplaceregistrationdefinitions | Nee | Nee | Nee |
> | registrationassignments | Nee | Nee | Nee |
> | registrationdefinitions | Nee | Nee | Nee |

## <a name="microsoftmanagement"></a>Microsoft.Management

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement | Regio verplaatsen |
> | ------------- | ----------- | ---------- | ----------- |
> | getentities | Nee | Nee | Nee |
> | managementgroups | Nee | Nee | Nee |
> | beheergroepen/instellingen | Nee | Nee | Nee |
> | resources | Nee | Nee | Nee |
> | ades toe te staanbackfill | Nee | Nee | Nee |
> | tenantbackfillstatus | Nee | Nee | Nee |

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement | Regio verplaatsen |
> | ------------- | ----------- | ---------- | ----------- |
> | accounts | Ja | Ja | Nee, Azure Maps is een georuimtelijke service. |
> | accounts / privateatlases | Ja | Ja | Nee |

## <a name="microsoftmarketplace"></a>Microsoft.Marketplace

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement | Regio verplaatsen |
> | ------------- | ----------- | ---------- | ----------- |
> | Biedt | Nee | Nee | Nee |
> | aanbiedingstypen | Nee | Nee | Nee |
> | privategalleryitems | Nee | Nee | Nee |
> | privatestoreclient | Nee | Nee | Nee |
> | privatestores | Nee | Nee | Nee |
> | Producten | Nee | Nee | Nee |
> | Uitgevers | Nee | Nee | Nee |
> | registreren | Nee | Nee | Nee |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement | Regio verplaatsen |
> | ------------- | ----------- | ---------- | ----------- |
> | classicdevservices | Nee | Nee | Nee |

## <a name="microsoftmarketplaceordering"></a>Microsoft.MarketplaceOrdering

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement | Regio verplaatsen |
> | ------------- | ----------- | ---------- | ----------- |
> | Overeenkomsten | Nee | Nee | Nee |
> | offertypes | Nee | Nee | Nee |

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement | Regio verplaatsen |
> | ------------- | ----------- | ---------- | ----------- |
> | mediaservices | Ja | Ja | Nee |
> | mediaservices /liveevents | Ja | Ja | Nee |
> | mediaservices/streaming-eindpunten | Ja | Ja | Nee |

## <a name="microsoftmicroservices4spring"></a>Microsoft.Microservices4Spring

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement | Regio verplaatsen |
> | ------------- | ----------- | ---------- | ----------- |
> | appclusters | Nee | Nee | Nee |

## <a name="microsoftmigrate"></a>Microsoft.Migrate

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement | Regio verplaatsen |
> | ------------- | ----------- | ---------- | ----------- |
> | assessmentprojects | Nee | Nee | Nee |
> | migrateprojects | Nee | Nee | Nee |
> | movecollections | Nee | Nee | Nee |
> | Projecten | Nee | Nee | Nee |

## <a name="microsoftmixedreality"></a>Microsoft.MixedReality

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement | Regio verplaatsen |
> | ------------- | ----------- | ---------- | ---------- |
> | holographicsbroadcastaccounts | Nee | Nee | Nee |
> | objectunderstandingaccounts | Nee | Nee | Nee |
> | remoterenderingaccounts | Ja | Ja | Nee |
> | spatialanchorsaccounts | Ja | Ja | Nee |

## <a name="microsoftnetapp"></a>Microsoft.NetApp

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement | Regio verplaatsen |
> | ------------- | ----------- | ---------- | ----------- |
> | netappaccounts | Nee | Nee | Nee |
> | netappaccounts/capacitypools | Nee | Nee | Nee |
> | netappaccounts/capacitypools/volumes | Nee | Nee | Nee |
> | netappaccounts/capacitypools/volumes/mounttargets | Nee | Nee | Nee |
> | netappaccounts / capacitypools / volumes / momentopnamen | Nee | Nee | Nee |

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!IMPORTANT]
> Zie [Richtlijnen voor het verplaatsen van netwerken.](./move-limitations/networking-move-limitations.md)

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement | Regio verplaatsen |
> | ------------- | ----------- | ---------- | ----------- |
> | applicationgateways | Nee | Nee | Nee |
> | applicationgatewaywebapplicationfirewallpolicies | Nee | Nee | Nee |
> | applicationsecuritygroups | Ja | Ja | Nee |
> | azurefirewalls | Nee | Nee | Nee |
> | bastionhosts | Nee | Nee | Nee |
> | bgpservicecommunities | Nee | Nee | Nee |
> | Verbindingen | Ja | Ja | Nee |
> | ddoscustompolicies | Ja | Ja | Nee |
> | ddosprotectionplans | Nee | Nee | Nee |
> | dnszones | Ja | Ja | Nee |
> | expressroutecircuits | Nee | Nee | Nee |
> | expressroutegateways | Nee | Nee | Nee |
> | expressrouteserviceproviders | Nee | Nee | Nee |
> | firewallbeleid | Ja | Ja | Nee |
> | frontdoors | Nee | Nee | Nee |
> | ipallocations | Ja | Ja | Nee |
> | ipgroups | Ja | Ja | Nee |
> | loadbalancers | Ja - Basis-SKU<br> Ja - Standaard-SKU | Ja - Basis-SKU<br>Nee - Standaard-SKU | Yes <br/><br/> Gebruik [Azure Resource Mover](../../resource-mover/tutorial-move-region-virtual-machines.md) om interne en externe load balancers te verplaatsen. |
> | localnetworkgateways | Ja | Ja | Nee |
> | natgateways | Nee | Nee | Nee |
> | networkexperimentprofiles | Nee | Nee | Nee |
> | networkintentpolicies | Ja | Ja | Nee |
> | networkinterfaces | Ja | Ja | Ja <br/><br/> Gebruik [Azure Resource Mover](../../resource-mover/tutorial-move-region-virtual-machines.md) om NIC's te verplaatsen. |
> | networkprofiles | Nee | Nee | Nee |
> | networksecuritygroups | Ja | Ja | Ja <br/><br/> Gebruik [Azure Resource Mover](../../resource-mover/tutorial-move-region-virtual-machines.md) om netwerkbeveiligingsgroepen (NGS's) te verplaatsen. |
> | networkwatchers | Nee | Nee | Nee |
> | networkwatchers/connectionmonitors | Ja | Nee | Nee |
> | networkwatchers/flowlogs | Ja | Nee | Nee |
> | networkwatchers/pingmeshes | Ja | Nee | Nee |
> | p2svpngateways | Nee | Nee | Nee |
> | privatednszones | Ja | Ja | Nee |
> | privatednszones /virtualnetworklinks | Ja | Ja | Nee |
> | privatednszonesinternal | Nee | Nee | Nee |
> | privateendpointredirectmaps | Nee | Nee | Nee |
> | privateendpoints | Nee | Nee | Nee |
> | privatelinkservices | Nee | Nee | Nee |
> | publicipaddresses | Ja - Basis-SKU<br>Ja - Standaard-SKU | Ja - Basis-SKU<br>Nee - Standaard-SKU | Yes<br/><br/> Gebruik [Azure Resource Mover](../../resource-mover/tutorial-move-region-virtual-machines.md) om openbare IP-adressen te verplaatsen. |
> | publicipprefixes | Ja | Ja | Nee |
> | routefilters | Nee | Nee | Nee |
> | routetabels | Ja | Ja | Nee |
> | securitypartnerproviders | Ja | Ja | Nee |
> | serviceendpointpolicies | Ja | Ja | Nee |
> | trafficmanagergeogramerarchies | Nee | Nee | Nee |
> | trafficmanagerprofiles | Ja | Ja | Nee |
> | trafficmanagerprofiles/heatmaps | Nee | Nee | Nee |
> | trafficmanagerusermetricskeys | Nee | Nee | Nee |
> | virtualhubs | Nee | Nee | Nee |
> | virtualnetworkgateways | Ja | Ja | Nee |
> | virtualnetworks | Ja | Ja | Nee |
> | virtualnetworktaps | Nee | Nee | Nee |
> | virtualrouters | Ja | Ja | Nee |
> | virtualwans | Nee | Nee |
> | vpngateways (Virtual WAN) | Nee | Nee | Nee |
> | vpnserverconfiguraties | Nee | Nee | Nee |
> | vpnsites (Virtual WAN) | Nee | Nee | Nee |

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement | Regio verplaatsen |
> | ------------- | ----------- | ---------- | ----------- |
> | Naamruimten | Ja | Ja | Nee |
> | namespaces/notificationhubs | Ja | Ja | Nee |

## <a name="microsoftobjectstore"></a>Microsoft.ObjectStore

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement | Regio verplaatsen |
> | ------------- | ----------- | ---------- | ----------- |
> | osnamespaces | Ja | Ja | Nee |

## <a name="microsoftoffazure"></a>Microsoft.OffAzure

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement | Regio verplaatsen |
> | ------------- | ----------- | ---------- | ----------- |
> | hypervsites | Nee | Nee | Nee |
> | importeert | Nee | Nee | Nee |
> | serversites | Nee | Nee | Nee |
> | vmwaresites | Nee | Nee | Nee |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!IMPORTANT]
> Zorg ervoor dat de overstap naar een nieuw abonnement de abonnementquota [niet overschrijdt.](azure-subscription-service-limits.md#azure-monitor-limits)
>
> Werkruimten met een gekoppeld Automation-account kunnen niet worden verplaatst. Voordat u een verhuisbewerking start, moet u alle Automation-accounts ontkoppelen.

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement | Regio verplaatsen |
> | ------------- | ----------- | ---------- | ----------- |
> | Clusters | Nee | Nee | Nee |
> | deletedworkspaces | Nee | Nee | Nee |
> | linktargets | Nee | Nee | Nee |
> | storageinsightconfigs | Nee | Nee | Nee |
> | workspaces | Ja | Ja | Nee |

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement | Regio verplaatsen |
> | ------------- | ----------- | ---------- | ----------- |
> | managementassociations | Nee | Nee | Nee |
> | beheerconfiguraties | Ja | Ja | Nee |
> | oplossingen | Ja | Ja | Nee |
> | Weergaven | Ja | Ja | Nee |

## <a name="microsoftpeering"></a>Microsoft.Peering

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement | Regio verplaatsen |
> | ------------- | ----------- | ---------- | ----------- |
> | legacypeerings | Nee | Nee | Nee |
> | peerasns | Nee | Nee | Nee |
> | peeringlocations | Nee | Nee | Nee |
> | peerings | Nee | Nee | Nee |
> | peeringservicecountries | Nee | Nee | Nee |
> | peeringservicelocations | Nee | Nee | Nee |
> | peeringserviceproviders | Nee | Nee | Nee |
> | peeringservices | Nee | Nee | Nee |

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement | Regio verplaatsen |
> | ------------- | ----------- | ---------- | ----------- |
> | policyevents | Nee | Nee | Nee |
> | policystates | Nee | Nee | Nee |
> | policytrackedresources | Nee | Nee | Nee |
> | herstel | Nee | Nee | Nee |

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement | Regio verplaatsen |
> | ------------- | ----------- | ---------- | ----------- |
> |  -consoles | Nee | Nee | Nee |
> | dashboards | Ja | Ja | Nee |
> | usersettings | Nee | Nee | Nee |

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement | Regio verplaatsen |
> | ------------- | ----------- | ---------- | ----------- |
> | werkruimtecollections | Ja | Ja | Nee |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement | Regio verplaatsen |
> | ------------- | ----------- | ---------- | ----------- |
> | Capaciteiten | Ja | Ja | Nee |

## <a name="microsoftprojectbabylon"></a>Microsoft.ProjectMasylon

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement | Regio verplaatsen |
> | ------------- | ----------- | ---------- | ---------- |
> | accounts | Nee | Nee | Nee |

## <a name="microsoftpurview"></a>Microsoft.Purview

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement | Regio verplaatsen |
> | ------------- | ----------- | ---------- | ---------- |
> | accounts | **Hangende** | **Hangende** | No |

## <a name="microsoftproviderhub"></a>Microsoft.ProviderHub

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement | Regio verplaatsen |
> | ------------- | ----------- | ---------- | ----------- |
> | availableaccounts | Nee | Nee | Nee |
> | providerregistrations | Nee | Nee | Nee |
> | implementaties | Nee | Nee | Nee |

## <a name="microsoftquantum"></a>Microsoft.Quantum

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement | Regio verplaatsen |
> | ------------- | ----------- | ---------- | ----------- |
> | workspaces | Nee | Nee | Nee |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!IMPORTANT]
> Zie [Richtlijnen voor het verplaatsen van Recovery Services.](../../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json)

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement | Regio verplaatsen |
> | ------------- | ----------- | ---------- | ----------- |
> | replicationeligibilityresults | Nee | Nee | Nee |
> | Kluizen | Ja | Ja | Nee.<br/><br/> Het verplaatsen van Recovery Services-kluizen Azure Backup tussen Azure-regio's wordt niet ondersteund.<br/><br/> In Recovery Services-kluizen voor Azure Site Recovery kunt u de kluis uitschakelen en [opnieuw maken](../../site-recovery/move-vaults-across-regions.md) in de doelregio. |

## <a name="microsoftredhatopenshift"></a>Microsoft.RedHatOpenShift

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement | Regio verplaatsen |
> | ------------- | ----------- | ---------- | ----------- |
> | openshiftclusters | Nee | Nee | Nee |

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement | Regio verplaatsen |
> | ------------- | ----------- | ---------- | ----------- |
> | Naamruimten | Ja | Ja | Nee |

## <a name="microsoftresourcegraph"></a>Microsoft.ResourceGraph

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement | Regio verplaatsen |
> | ------------- | ----------- | ---------- | ----------- |
> | Query 's | Ja | Ja | Nee |
> | resourcechangedetails | Nee | Nee | Nee |
> | resourcechanges | Nee | Nee | Nee |
> | resources | Nee | Nee | Nee |
> | resourceshistory | Nee | Nee | Nee |
> | subscriptionsstatus | Nee | Nee | Nee |

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement | Regio verplaatsen |
> | ------------- | ----------- | ---------- | ----------- |
> | childresources | Nee | Nee | Nee |
> | opkomendeissues | Nee | Nee | Nee |
> | events | Nee | Nee | Nee |
> | metagegevens | Nee | Nee | Nee |
> | meldingen | Nee | Nee | Nee |

## <a name="microsoftresources"></a>Microsoft.Resources

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement | Regio verplaatsen |
> | ------------- | ----------- | ---------- | ----------- |
> | Implementaties | Nee | Nee | Nee |
> | deploymentscripts | Nee | Nee | Ja<br/><br/>[Resources van Microsoft.Resources verplaatsen naar een nieuwe regio](microsoft-resources-move-regions.md) |
> | deploymentscripts/logboeken | Nee | Nee | Nee |
> | Verwijzigingen | Nee | Nee | Nee |
> | providers | Nee | Nee | Nee |
> | resourcegroepen | Nee | Nee | Nee |
> | resources | Nee | Nee | Nee |
> | Abonnementen | Nee | Nee | Nee |
> | tags | Nee | Nee | Nee |
> | templatespecs | Nee | Nee | Ja<br/><br/>[Resources van Microsoft.Resources verplaatsen naar een nieuwe regio](microsoft-resources-move-regions.md) |
> | templatespecs/versions | Nee | Nee | Nee |
> | Huurders | Nee | Nee | Nee |

## <a name="microsoftsaas"></a>Microsoft.SaaS

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement | Regio verplaatsen |
> | ------------- | ----------- | ---------- | ----------- |
> | toepassingen | Ja | Nee | Nee |
> | saasresources | Nee | Nee | Nee |

## <a name="microsoftsearch"></a>Microsoft.Search

> [!IMPORTANT]
> U kunt verschillende Zoek-resources in verschillende regio's niet in één bewerking verplaatsen. Verplaats ze in plaats daarvan in afzonderlijke bewerkingen.

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement | Regio verplaatsen |
> | ------------- | ----------- | ---------- | ----------- |
> | resourcehealthmetadata | Nee | Nee | Nee |
> | searchservices | Ja | Ja | Nee |

## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement | Regio verplaatsen |
> | ------------- | ----------- | ---------- | ----------- |
> | adaptivenetworkhardenings | Nee | Nee | Nee |
> | advancedthreatprotectionsettings | Nee | Nee | Nee |
> | waarschuwingen | Nee | Nee | Nee |
> | allowedconnections | Nee | Nee | Nee |
> | applicationwhitelistings | Nee | Nee | Nee |
> | assessmentmetadata | Nee | Nee | Nee |
> | evaluaties | Nee | Nee | Nee |
> | autodismismismlertsrules | Nee | Nee | Nee |
> | automatiseringen | Ja | Ja | Nee |
> | autoprovisioningsettings | Nee | Nee | Nee |
> | complianceresults | Nee | Nee | Nee |
> | naleving | Nee | Nee | Nee |
> | datacollectionagents | Nee | Nee | Nee |
> | devicesecuritygroups | Nee | Nee | Nee |
> | discoveredsecuritysolutions | Nee | Nee | Nee |
> | externalsecuritysolutions | Nee | Nee | Nee |
> | informationprotectionpolicies | Nee | Nee | Nee |
> | iotsecuritysolutions | Ja | Ja | Nee |
> | iotsecuritysolutions /analyticsmodels | Nee | Nee | Nee |
> | iotsecuritysolutions / analyticsmodels / aggregatedalerts | Nee | Nee | Nee |
> | iotsecuritysolutions / analyticsmodels / aggregatedrecommendations | Nee | Nee | Nee |
> | jitnetworkaccesspolicies | Nee | Nee | Nee |
> | policies | Nee | Nee | Nee |
> | prijzen | Nee | Nee | Nee |
> | regelgevingstandaarden | Nee | Nee | Nee |
> | regulatorycompliancestandards /regulatorycompliancecontrols | Nee | Nee | Nee |
> | regulatorycompliancestandards / regulatorycompliancecontrols / regulatorycomplianceassessments | Nee | Nee | Nee |
> | beveiligingscontacten | Nee | Nee | Nee |
> | securitysolutions | Nee | Nee | Nee |
> | securitysolutionsreferencedata | Nee | Nee | Nee |
> | beveiligingsstatussen | Nee | Nee | Nee |
> | securitystatusessummaries | Nee | Nee | Nee |
> | servervulnerabilityassessments | Nee | Nee | Nee |
> | instellingen | Nee | Nee | Nee |
> | subbeoordelingen | Nee | Nee | Nee |
> | taken | Nee | Nee | Nee |
> | Topologieën | Nee | Nee | Nee |
> | workspacesettings | Nee | Nee | Nee |

## <a name="microsoftsecurityinsights"></a>Microsoft.SecurityInsights

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement | Regio verplaatsen |
> | ------------- | ----------- | ---------- | ----------- |
> | aggregaties | Nee | Nee | Nee |
> | waarschuwingsrules | Nee | Nee | Nee |
> | alertruletemplates | Nee | Nee | Nee |
> | automationrules | Nee | Nee | Nee |
> | bladwijzers | Nee | Nee | Nee |
> | Gevallen | Nee | Nee | Nee |
> | dataconnectors | Nee | Nee | Nee |
> | entiteiten | Nee | Nee | Nee |
> | entityqueries | Nee | Nee | Nee |
> | incidenten | Nee | Nee | Nee |
> | officeconsents | Nee | Nee | Nee |
> | instellingen | Nee | Nee | Nee |
> | threatintelligence | Nee | Nee | Nee |

## <a name="microsoftserialconsole"></a>Microsoft.SerialConsole

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement | Regio verplaatsen |
> | ------------- | ----------- | ---------- | ----------- |
> | consoleservices | Nee | Nee | Nee |

## <a name="microsoftservermanagement"></a>Microsoft.ServerManagement

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement | Regio verplaatsen |
> | ------------- | ----------- | ---------- | ----------- |
> | Gateways | Nee | Nee | Nee |
> | Knooppunten | Nee | Nee | Nee |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement | Regio verplaatsen |
> | ------------- | ----------- | ---------- | ----------- |
> | Naamruimten | Ja | Ja | Nee |
> | premiummessagingregions | Nee | Nee | Nee |
> | sku | Nee | Nee | Nee |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement | Regio verplaatsen |
> | ------------- | ----------- | ---------- | ----------- |
> | toepassingen | Nee | Nee | Nee |
> | Clusters | Ja | Ja | Nee |
> | containergroepen | Nee | Nee | Nee |
> | containergroupsets | Nee | Nee | Nee |
> | edgeclusters | Nee | Nee | Nee |
> | managedclusters | Nee | Nee | Nee |
> | Netwerken | Nee | Nee | Nee |
> | secretstores | Nee | Nee | Nee |
> | volumes | Nee | Nee | Nee |

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement | Regio verplaatsen |
> | ------------- | ----------- | ---------- | ----------- |
> | toepassingen | Ja | Ja | Nee |
> | containergroepen | Nee | Nee | Nee |
> | Gateways | Ja | Ja | Nee |
> | Netwerken | Ja | Ja | Nee |
> | geheimen | Ja | Ja | Nee |
> | volumes | Ja | Ja | Nee |

## <a name="microsoftservices"></a>Microsoft.Services

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement | Regio verplaatsen |
> | ------------- | ----------- | ---------- | ----------- |
> | implementaties | Nee | Nee | Nee |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement | Regio verplaatsen |
> | ------------- | ----------- | ---------- | ----------- |
> | signalr | Ja | Ja | Nee |

## <a name="microsoftsoftwareplan"></a>Microsoft.SoftwarePlan

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement | Regio verplaatsen |
> | ------------- | ----------- | ---------- | ----------- |
> | hybridusebenefits | Nee | Nee | Nee |

## <a name="microsoftsolutions"></a>Microsoft.Solutions

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement | Regio verplaatsen |
> | ------------- | ----------- | ---------- | ----------- |
> | applicationdefinitions | Nee | Nee | Nee |
> | toepassingen | Nee | Nee | Nee |
> | jitrequests | Nee | Nee | Nee |

## <a name="microsoftsql"></a>Microsoft.Sql

> [!IMPORTANT]
> Een database en server moeten zich in dezelfde resourcegroep. Wanneer u een SQL-server verplaatst, worden ook alle databases verplaatst. Dit gedrag is van toepassing op Azure SQL Database en Azure Synapse Analytics databases.

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement | Regio verplaatsen |
> | ------------- | ----------- | ---------- | ----------- |
> | instancepools | Nee | Nee | Nee |
> | locaties | Ja | Ja | Nee |
> | managedinstances | Nee | Nee | Ja <br/><br/> [Meer informatie over](../../azure-sql/database/move-resources-across-regions.md) het verplaatsen van beheerde exemplaren tussen regio's. |
> | managedinstances /databases | Nee | Nee | Ja |
> | Servers | Ja | Ja |Ja |
> | servers/databases | Ja | Ja | Ja <br/><br/> [Meer informatie over](../../azure-sql/database/move-resources-across-regions.md) het verplaatsen van databases tussen regio's.<br/><br/> [Meer informatie over](../../resource-mover/tutorial-move-region-sql.md) het gebruik van Azure Resource Mover voor het verplaatsen van Azure SQL databases.  |
> | servers / databases / backuplongtermretentionpolicies | Ja | Ja | Nee |
> | servers/elasticpools | Ja | Ja | Ja <br/><br/> [Meer informatie over](../../azure-sql/database/move-resources-across-regions.md) het verplaatsen van elastische pools tussen regio's.<br/><br/> [Meer informatie over](../../resource-mover/tutorial-move-region-sql.md) het gebruik van Azure Resource Mover voor het verplaatsen Azure SQL elastische pools.  |
> | servers/jobaccounts | Ja | Ja | Nee |
> | servers/jobagents | Ja | Ja | Nee |
> | virtualclusters | Ja | Ja | Ja |

## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement | Regio verplaatsen |
> | ------------- | ----------- | ---------- | ----------- |
> | sqlvirtualmachinegroups | Ja | Ja | Nee |
> | sqlvirtualmachines | Ja | Ja | Nee |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement | Regio verplaatsen |
> | ------------- | ----------- | ---------- | ----------- |
> | storageaccounts | Ja | Ja | Ja<br/><br/> [Een account Azure Storage verplaatsen naar een andere regio](../../storage/common/storage-account-move.md) |

## <a name="microsoftstoragecache"></a>Microsoft.StorageCache

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement | Regio verplaatsen |
> | ------------- | ----------- | ---------- | ----------- |
> | Caches | Nee | Nee | Nee |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement | Regio verplaatsen |
> | ------------- | ----------- | ---------- | ----------- |
> | storagesyncservices | Ja | Ja | Nee |

## <a name="microsoftstoragesyncdev"></a>Microsoft.StorageSyncDev

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement | Regio verplaatsen |
> | ------------- | ----------- | ---------- | ----------- |
> | storagesyncservices | Nee | Nee | Nee |

## <a name="microsoftstoragesyncint"></a>Microsoft.StorageSyncInt

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement | Regio verplaatsen |
> | ------------- | ----------- | ---------- | ----------- |
> | storagesyncservices | Nee | Nee | Nee |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement | Regio verplaatsen |
> | ------------- | ----------- | ---------- | ----------- |
> | Managers | Nee | Nee | Nee |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!IMPORTANT]
> Stream Analytics kunnen niet worden verplaatst wanneer ze de status Actief hebben.

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement | Regio verplaatsen |
> | ------------- | ----------- | ---------- | ----------- |
> | Clusters | Nee | Nee | Nee |
> | streamingjobs | Ja | Ja | Nee |

## <a name="microsoftstreamanalyticsexplorer"></a>Microsoft.StreamAnalyticsExplorer

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement | Regio verplaatsen |
> | ------------- | ----------- | ---------- | ----------- |
> | Omgevingen | Nee | Nee | Nee |
> | Exemplaren | Nee | Nee | Nee |

## <a name="microsoftsubscription"></a>Microsoft.Subscription

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement | Regio verplaatsen |
> | ------------- | ----------- | ---------- | ----------- |
> | Abonnementen | Nee | Nee | Nee |

## <a name="microsoftsupport"></a>Microsoft.Support

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement | Regio verplaatsen |
> | ------------- | ----------- | ---------- | ----------- |
> | services | Nee | Nee | Nee |
> | supporttickets | Nee | Nee | Nee |

## <a name="microsoftsynapse"></a>Microsoft.Synapse

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement | Regio verplaatsen |
> | ------------- | ----------- | ---------- | ----------- |
> | workspaces | Nee | Nee | Nee |
> | werkruimten/bigdatapools | Nee | Nee | Nee |
> | werkruimten/sqlpools | Nee | Nee | Nee |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement | Regio verplaatsen |
> | ------------- | ----------- | ---------- | ----------- |
> | Omgevingen | Ja | Ja | Nee |
> | omgevingen /eventsources | Ja | Ja | Nee |
> | omgevingen /referencedatasets | Ja | Ja | Nee |

## <a name="microsofttoken"></a>Microsoft.Token

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement | Regio verplaatsen |
> | ------------- | ----------- | ---------- | ----------- |
> | Winkels | Ja | Ja | Nee |

## <a name="microsoftvirtualmachineimages"></a>Microsoft.VirtualMachineImages

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement | Regio verplaatsen |
> | ------------- | ----------- | ---------- | ----------- |
> | imagetemplates | Nee | Nee | Nee |

## <a name="microsoftvisualstudio"></a>Microsoft.VisualStudio

> [!IMPORTANT]
> Zie Het Azure-abonnement wijzigen dat wordt gebruikt voor facturering als u het abonnement voor Azure DevOps [wilt wijzigen.](/azure/devops/organizations/billing/change-azure-subscription?toc=/azure/azure-resource-manager/toc.json)

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement | Regio verplaatsen |
> | ------------- | ----------- | ---------- | ----------- |
> | account | Nee | Nee | Nee |
> | account/extensie | Nee | Nee | Nee |
> | account/project | Nee | Nee | Nee |

## <a name="microsoftvmware"></a>Microsoft.VMware

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement | Regio verplaatsen |
> | ------------- | ----------- | ---------- | ----------- |
> | arczones | Nee | Nee | Nee |
> | resourcepools | Nee | Nee | Nee |
> | vcenters | Nee | Nee | Nee |
> | virtualmachines | Nee | Nee | Nee |
> | virtualmachinetemplates | Nee | Nee | Nee |
> | virtualnetworks | Nee | Nee | Nee |

## <a name="microsoftvmwarecloudsimple"></a>Microsoft.VMwareCloudSimple

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement | Regio verplaatsen |
> | ------------- | ----------- | ---------- | ----------- |
> | dedicatedcloudnodes | Nee | Nee | Nee |
> | dedicatedcloudservices | Nee | Nee | Nee |
> | virtualmachines | Nee | Nee | Nee |

## <a name="microsoftvnfmanager"></a>Microsoft.VnfManager

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement | Regio verplaatsen |
> | ------------- | ----------- | ---------- | ----------- |
> | devices | Nee | Nee | Nee |
> | vnfs | Nee | Nee | Nee |

## <a name="microsoftvsonline"></a>Microsoft.VSOnline

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement | Regio verplaatsen |
> | ------------- | ----------- | ---------- | ----------- |
> | accounts | Nee | Nee | Nee |
> | Plannen | Nee | Nee | Nee |
> | registeredsubscriptions | Nee | Nee | Nee |

## <a name="microsoftweb"></a>Microsoft.Web

> [!IMPORTANT]
> Zie [App Service richtlijnen voor verplaatsen.](./move-limitations/app-service-move-limitations.md)

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement | Regio verplaatsen |
> | ------------- | ----------- | ---------- | ----------- |
> | availablestacks | Nee | Nee | Nee |
> | factureringsmeters | Nee | Nee | Nee |
> | certificaten | Nee | Ja | Nee |
> | connectiongateways | Ja | Ja | Nee |
> | Verbindingen | Ja | Ja | Nee |
> | customapis | Ja | Ja | Nee |
> | verwijderde sites | Nee | Nee | Nee |
> | deploymentlocations | Nee | Nee | Nee |
> | georegio's | Nee | Nee | Nee |
> | hostingenvironments | Nee | Nee | Nee |
> | kuschapvironments | Ja | Ja | Nee |
> | publishingusers | Nee | Nee | Nee |
> | aanbevelingen | Nee | Nee | Nee |
> | resourcehealthmetadata | Nee | Nee | Nee |
> | runtimes | Nee | Nee | Nee |
> | serverfarms | Ja | Ja | Nee |
> | serverfarms/eventgridfilters | Nee | Nee | Nee |
> | sites | Ja | Ja | Nee |
> | sites / premieraddons | Ja | Ja | Nee |
> | sites/sites | Ja | Ja | Nee |
> | sourcecontrols | Nee | Nee | Nee |
> | staticsites | Nee | Nee | Nee |

## <a name="microsoftwindowsesu"></a>Microsoft.WindowsESU

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement | Regio verplaatsen |
> | ------------- | ----------- | ---------- | ----------- |
> | multipleactivationkeys | Nee | Nee | Nee |

## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement | Regio verplaatsen |
> | ------------- | ----------- | ---------- | ----------- |
> | deviceservices | Nee | Nee | Nee |

## <a name="microsoftworkloadbuilder"></a>Microsoft.WorkloadBuilder

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement | Regio verplaatsen |
> | ------------- | ----------- | ---------- | ----------- |
> | Werkbelasting | Nee | Nee | Nee |

## <a name="microsoftworkloadmonitor"></a>Microsoft.WorkloadMonitor

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement | Regio verplaatsen |
> | ------------- | ----------- | ---------- | ----------- |
> | Onderdelen | Nee | Nee | Nee |
> | componentssummary | Nee | Nee | Nee |
> | monitorinstances | Nee | Nee | Nee |
> | monitorinstancessummary | Nee | Nee | Nee |
> | Monitoren | Nee | Nee | Nee |

## <a name="third-party-services"></a>Services van derden

Services van derden bieden momenteel geen ondersteuning voor de verhuisbewerking.

## <a name="next-steps"></a>Volgende stappen

- Zie Resources verplaatsen naar een nieuwe resourcegroep of een nieuw abonnement voor opdrachten voor het [verplaatsen van resources.](move-resource-group-and-subscription.md)
- [Meer informatie](../../resource-mover/overview.md) over de Resource Mover-service.
- Als u dezelfde gegevens wilt op halen als een bestand met door komma's gescheiden waarden, downloadt [ umove-support-resources.csv](https://github.com/tfitzmac/resource-capabilities/blob/master/move-support-resources.csv).
