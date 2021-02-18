---
title: Label ondersteuning voor bronnen
description: Hier wordt weer gegeven welke Azure-resource typen tags ondersteunen. Geeft Details voor alle Azure-Services.
ms.topic: conceptual
ms.date: 10/21/2020
ms.openlocfilehash: 73f58dbda0e7c073cf4a8d2e846132a102192e93
ms.sourcegitcommit: 58ff80474cd8b3b30b0e29be78b8bf559ab0caa1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100635911"
---
# <a name="tag-support-for-azure-resources"></a>Ondersteuning voor labels voor Azure-resources
In dit artikel wordt beschreven of een resource type [labels](tag-resources.md)ondersteunt. De kolom met de naam **ondersteunt labels** geeft aan of het resource type een eigenschap voor de tag heeft. De kolom label **in het kosten rapport** geeft aan of dit resource type de tag doorgeeft aan het kosten rapport. U kunt kosten op labels weer geven in de [Cost Management kosten analyse](../../cost-management-billing/costs/group-filter.md) en de gegevens voor de [factuur en dagelijks gebruik van Azure](../../cost-management-billing/manage/download-azure-invoice-daily-usage-date.md).

Als u dezelfde gegevens wilt ophalen als een bestand met door komma's gescheiden waarden, downloadt u [tag-support.csv](https://github.com/tfitzmac/resource-capabilities/blob/master/tag-support.csv).

Ga naar de naam ruimte van een resource provider:
> [!div class="op_single_selector"]
> - [Micro soft. AAD](#microsoftaad)
> - [Micro soft. Addons](#microsoftaddons)
> - [Microsoft.ADHybridHealthService](#microsoftadhybridhealthservice)
> - [Microsoft.Advisor](#microsoftadvisor)
> - [Micro soft. AgFoodPlatform](#microsoftagfoodplatform)
> - [Micro soft. AlertsManagement](#microsoftalertsmanagement)
> - [Microsoft.AnalysisServices](#microsoftanalysisservices)
> - [Microsoft.ApiManagement](#microsoftapimanagement)
> - [Micro soft. AppConfiguration](#microsoftappconfiguration)
> - [Micro soft. AppPlatform](#microsoftappplatform)
> - [Micro soft. Attestation](#microsoftattestation)
> - [Microsoft.Authorization](#microsoftauthorization)
> - [Micro soft. automanage](#microsoftautomanage)
> - [Microsoft.Automation](#microsoftautomation)
> - [Micro soft. AVS](#microsoftavs)
> - [Micro soft. Azure. Genève](#microsoftazuregeneva)
> - [Microsoft.AzureActiveDirectory](#microsoftazureactivedirectory)
> - [Micro soft. Azureworden](#microsoftazuredata)
> - [Micro soft. AzureStack](#microsoftazurestack)
> - [Micro soft. AzureStackHCI](#microsoftazurestackhci)
> - [Micro soft. BareMetalInfrastructure](#microsoftbaremetalinfrastructure)
> - [Microsoft.Batch](#microsoftbatch)
> - [Microsoft.Billing](#microsoftbilling)
> - [Microsoft.BingMaps](#microsoftbingmaps)
> - [Micro soft. Block Chain](#microsoftblockchain)
> - [Micro soft. BlockchainTokens](#microsoftblockchaintokens)
> - [Micro soft. blauw druk](#microsoftblueprint)
> - [Micro soft. BotService](#microsoftbotservice)
> - [Microsoft.Cache](#microsoftcache)
> - [Micro soft. capacity](#microsoftcapacity)
> - [Micro soft. CDN](#microsoftcdn)
> - [Microsoft.CertificateRegistration](#microsoftcertificateregistration)
> - [Micro soft. ChangeAnalysis](#microsoftchangeanalysis)
> - [Microsoft.ClassicCompute](#microsoftclassiccompute)
> - [Micro soft. ClassicInfrastructureMigrate](#microsoftclassicinfrastructuremigrate)
> - [Microsoft.ClassicNetwork](#microsoftclassicnetwork)
> - [Microsoft.ClassicStorage](#microsoftclassicstorage)
> - [Micro soft. Codespaces](#microsoftcodespaces)
> - [Microsoft.CognitiveServices](#microsoftcognitiveservices)
> - [Microsoft.Commerce](#microsoftcommerce)
> - [Microsoft.Compute](#microsoftcompute)
> - [Micro soft. ConnectedCache](#microsoftconnectedcache)
> - [Micro soft. verbruik](#microsoftconsumption)
> - [Micro soft. ContainerInstance](#microsoftcontainerinstance)
> - [Microsoft.ContainerRegistry](#microsoftcontainerregistry)
> - [Micro soft. container service](#microsoftcontainerservice)
> - [Micro soft. CostManagement](#microsoftcostmanagement)
> - [Micro soft. CustomerLockbox](#microsoftcustomerlockbox)
> - [Micro soft. CustomProviders](#microsoftcustomproviders)
> - [Micro soft. D365CustomerInsights](#microsoftd365customerinsights)
> - [Micro soft. DataBox](#microsoftdatabox)
> - [Micro soft. DataBoxEdge](#microsoftdataboxedge)
> - [Micro soft. Databricks](#microsoftdatabricks)
> - [Microsoft.DataCatalog](#microsoftdatacatalog)
> - [Microsoft.DataFactory](#microsoftdatafactory)
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
> - [Micro soft. DeviceUpdate](#microsoftdeviceupdate)
> - [Micro soft. DevOps](#microsoftdevops)
> - [Micro soft. DevSpaces](#microsoftdevspaces)
> - [Microsoft.DevTestLab](#microsoftdevtestlab)
> - [Micro soft. DigitalTwins](#microsoftdigitaltwins)
> - [Microsoft.DocumentDB](#microsoftdocumentdb)
> - [Micro soft. DomainRegistration](#microsoftdomainregistration)
> - [Microsoft.DynamicsLcs](#microsoftdynamicslcs)
> - [Micro soft. EnterpriseKnowledgeGraph](#microsoftenterpriseknowledgegraph)
> - [Micro soft. EventGrid](#microsofteventgrid)
> - [Microsoft.EventHub](#microsofteventhub)
> - [Micro soft. experimenten](#microsoftexperimentation)
> - [Micro soft. Falcon](#microsoftfalcon)
> - [Microsoft.Features](#microsoftfeatures)
> - [Micro soft. Gallery](#microsoftgallery)
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
> - [Microsoft.Insights](#microsoftinsights)
> - [Microsoft.Intune](#microsoftintune)
> - [Micro soft. IoTCentral](#microsoftiotcentral)
> - [Micro soft. IoTSpaces](#microsoftiotspaces)
> - [Microsoft.KeyVault](#microsoftkeyvault)
> - [Microsoft.Kubernetes](#microsoftkubernetes)
> - [Micro soft. KubernetesConfiguration](#microsoftkubernetesconfiguration)
> - [Microsoft.Kusto](#microsoftkusto)
> - [Micro soft. LabServices](#microsoftlabservices)
> - [Microsoft.Logic](#microsoftlogic)
> - [Microsoft.MachineLearning](#microsoftmachinelearning)
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
> - [Micro soft. notebooks](#microsoftnotebooks)
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
> - [Micro soft. ScVmm](#microsoftscvmm)
> - [Microsoft.Search](#microsoftsearch)
> - [Microsoft.Security](#microsoftsecurity)
> - [Micro soft. SecurityGraph](#microsoftsecuritygraph)
> - [Micro soft. SecurityInsights](#microsoftsecurityinsights)
> - [Micro soft. SerialConsole](#microsoftserialconsole)
> - [Microsoft.ServiceBus](#microsoftservicebus)
> - [Micro soft. ServiceFabric](#microsoftservicefabric)
> - [Micro soft. ServiceFabricMesh](#microsoftservicefabricmesh)
> - [Micro soft. Services](#microsoftservices)
> - [Micro soft. SignalRService](#microsoftsignalrservice)
> - [Micro soft. enkelvoud](#microsoftsingularity)
> - [Micro soft. SoftwarePlan](#microsoftsoftwareplan)
> - [Micro soft. Solutions](#microsoftsolutions)
> - [Micro soft. SQL](#microsoftsql)
> - [Micro soft. SqlVirtualMachine](#microsoftsqlvirtualmachine)
> - [Microsoft.Storage](#microsoftstorage)
> - [Micro soft. StorageCache](#microsoftstoragecache)
> - [Micro soft. StorageReplication](#microsoftstoragereplication)
> - [Micro soft. StorageSync](#microsoftstoragesync)
> - [Micro soft. StorageSyncDev](#microsoftstoragesyncdev)
> - [Micro soft. StorageSyncInt](#microsoftstoragesyncint)
> - [Microsoft.StorSimple](#microsoftstorsimple)
> - [Microsoft.StreamAnalytics](#microsoftstreamanalytics)
> - [Micro soft. Subscription](#microsoftsubscription)
> - [Micro soft. Synapse](#microsoftsynapse)
> - [Micro soft. TimeSeriesInsights](#microsofttimeseriesinsights)
> - [Micro soft. token](#microsofttoken)
> - [Microsoft.VirtualMachineImages](#microsoftvirtualmachineimages)
> - [Micro soft. VMware](#microsoftvmware)
> - [Micro soft. VMwareCloudSimple](#microsoftvmwarecloudsimple)
> - [Micro soft. VnfManager](#microsoftvnfmanager)
> - [Micro soft. VSOnline](#microsoftvsonline)
> - [Microsoft.Web](#microsoftweb)
> - [Micro soft. WindowsDefenderATP](#microsoftwindowsdefenderatp)
> - [Micro soft. WindowsESU](#microsoftwindowsesu)
> - [Micro soft. WindowsIoT](#microsoftwindowsiot)
> - [Micro soft. WorkloadBuilder](#microsoftworkloadbuilder)
> - [Micro soft. WorkloadMonitor](#microsoftworkloadmonitor)

## <a name="microsoftaad"></a>Micro soft. AAD

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | DomainServices | Ja | Ja |
> | DomainServices / oucontainer | Nee | Nee |

## <a name="microsoftaddons"></a>Micro soft. Addons

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | supportProviders | Nee | Nee |

## <a name="microsoftadhybridhealthservice"></a>Microsoft.ADHybridHealthService

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
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
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | advisorScore | Nee | Nee |
> | configuraties | Nee | Nee |
> | generateRecommendations | Nee | Nee |
> | metagegevens | Nee | Nee |
> | aanbevelingen | Nee | Nee |
> | onderdrukkingen | Nee | Nee |

## <a name="microsoftagfoodplatform"></a>Micro soft. AgFoodPlatform

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | farmBeats | Ja | Ja |

## <a name="microsoftalertsmanagement"></a>Micro soft. AlertsManagement

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | actionRules | Ja | Ja |
> | waarschuwingen | Nee | Nee |
> | alertsList | Nee | Nee |
> | alertsMetaData | Nee | Nee |
> | alertsSummary | Nee | Nee |
> | alertsSummaryList | Nee | Nee |
> | smartDetectorAlertRules | Ja | Ja |
> | smartGroups | Nee | Nee |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | Server | Ja | Ja |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | reportFeedback | Nee | Nee |
> | service | Ja | Ja |
> | validateServiceName | Nee | Nee |

> [!NOTE]
> Azure API Management biedt alleen ondersteuning voor het maken van Maxi maal 15 label naam/waarde-paren voor elke service.

## <a name="microsoftappconfiguration"></a>Micro soft. AppConfiguration

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | configurationStores | Ja | Nee |
> | configurationStores / eventGridFilters | Nee | Nee |
> | configurationStores/-waarden | Nee | Nee |

## <a name="microsoftappplatform"></a>Micro soft. AppPlatform

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | Spring | Ja | Ja |
> | Lente/apps | Nee | Nee |
> | Lente/apps/implementaties | Nee | Nee |

## <a name="microsoftattestation"></a>Micro soft. Attestation

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | attestationProviders | Ja | Ja |
> | defaultProviders | Nee | Nee |

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | accessReviewScheduleDefinitions | Nee | Nee |
> | accessReviewScheduleSettings | Nee | Nee |
> | classicAdministrators | Nee | Nee |
> | dataAliases | Nee | Nee |
> | denyAssignments | Nee | Nee |
> | elevateAccess | Nee | Nee |
> | findOrphanRoleAssignments | Nee | Nee |
> | vergren delingen | Nee | Nee |
> | permissions | Nee | Nee |
> | policyAssignments | Nee | Nee |
> | policyDefinitions | Nee | Nee |
> | policyExemptions | Nee | Nee |
> | policySetDefinitions | Nee | Nee |
> | privateLinkAssociations | Nee | Nee |
> | providerOperations | Nee | Nee |
> | resourceManagementPrivateLinks | Ja | Ja |
> | roleAssignments | Nee | Nee |
> | roleAssignmentsUsageMetrics | Nee | Nee |
> | roleDefinitions | Nee | Nee |

## <a name="microsoftautomanage"></a>Micro soft. automanage

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | accounts | Ja | Ja |
> | configurationProfileAssignments | Nee | Nee |
> | configurationProfilePreferences | Ja | Ja |

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | automationAccounts | Ja | Ja |
> | automationAccounts/configuraties | Ja | Ja |
> | automationAccounts/Jobs | Nee | Nee |
> | automationAccounts / privateEndpointConnectionProxies | Nee | Nee |
> | automationAccounts / privateEndpointConnections | Nee | Nee |
> | automationAccounts / privateLinkResources | Nee | Nee |
> | automationAccounts/runbooks | Ja | Ja |
> | automationAccounts / softwareUpdateConfigurations | Nee | Nee |
> | automationAccounts/webhooks | Nee | Nee |

> [!NOTE]
> Azure Automation biedt alleen ondersteuning voor het maken van Maxi maal 15 label naam/waarde-paren voor elke Automation-resource.

## <a name="microsoftavs"></a>Micro soft. AVS

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | privateClouds | Ja | Ja |
> | privateClouds/addons | Nee | Nee |
> | privateClouds/autorisaties | Nee | Nee |
> | privateClouds/clusters | Nee | Nee |
> | privateClouds / globalReachConnections | Nee | Nee |
> | privateClouds / hcxEnterpriseSites | Nee | Nee |
> | privateClouds / workloadNetworks | Nee | Nee |
> | privateClouds / workloadNetworks / dhcpConfigurations | Nee | Nee |
> | privateClouds/workloadNetworks/gateways | Nee | Nee |
> | privateClouds / workloadNetworks / portMirroringProfiles | Nee | Nee |
> | privateClouds/workloadNetworks/segmenten | Nee | Nee |
> | privateClouds/workloadNetworks/informatie | Nee | Nee |
> | privateClouds / workloadNetworks / vmGroups | Nee | Nee |

## <a name="microsoftazuregeneva"></a>Micro soft. Azure. Genève

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | verschillend | Nee | Nee |
> | omgevingen/accounts | Nee | Nee |
> | omgevingen/accounts/naam ruimten | Nee | Nee |
> | omgevingen/accounts/naam ruimten/configuraties | Nee | Nee |

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | b2cDirectories | Ja | Nee |
> | b2ctenants | Nee | Nee |
> | guestUsages | Ja | Ja |

## <a name="microsoftazuredata"></a>Micro soft. Azureworden

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | dataControllers | Ja | Ja |
> | postgresInstances | Ja | Ja |
> | sqlManagedInstances | Ja | Ja |
> | sqlServerInstances | Ja | Ja |
> | sqlServerRegistrations | Ja | Ja |
> | sqlServerRegistrations / sqlServers | Nee | Nee |

## <a name="microsoftazurestack"></a>Micro soft. AzureStack

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | cloudManifestFiles | Nee | Nee |
> | edgeSubscriptions | Ja | Ja |
> | linkedSubscriptions | Ja | Ja |
> | registraties | Ja | Ja |
> | registraties/customerSubscriptions | Nee | Nee |
> | registraties/producten | Nee | Nee |

## <a name="microsoftazurestackhci"></a>Micro soft. AzureStackHCI

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | clusters | Ja | Ja |

## <a name="microsoftbaremetalinfrastructure"></a>Micro soft. BareMetalInfrastructure

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | bareMetalInstances | Ja | Ja |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | batchAccounts | Ja | Ja |
> | batchAccounts/certificaten | Nee | Nee |
> | batchAccounts/Pools | Nee | Nee |

## <a name="microsoftbilling"></a>Microsoft.Billing

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | billingAccounts | Nee | Nee |
> | billingAccounts/overeenkomsten | Nee | Nee |
> | billingAccounts / billingPermissions | Nee | Nee |
> | billingAccounts / billingProfiles | Nee | Nee |
> | billingAccounts / billingProfiles / billingPermissions | Nee | Nee |
> | billingAccounts / billingProfiles / billingRoleAssignments | Nee | Nee |
> | billingAccounts / billingProfiles / billingRoleDefinitions | Nee | Nee |
> | billingAccounts / billingProfiles / billingSubscriptions | Nee | Nee |
> | billingAccounts / billingProfiles / createBillingRoleAssignment | Nee | Nee |
> | billingAccounts/billingProfiles/klanten | Nee | Nee |
> | billingAccounts/billingProfiles/instructies | Nee | Nee |
> | billingAccounts/billingProfiles/facturen | Nee | Nee |
> | billingAccounts/billingProfiles/facturen/prijzen overzicht | Nee | Nee |
> | billingAccounts/billingProfiles/facturen/trans acties | Nee | Nee |
> | billingAccounts / billingProfiles / invoiceSections | Nee | Nee |
> | billingAccounts / billingProfiles / invoiceSections / billingPermissions | Nee | Nee |
> | billingAccounts / billingProfiles / invoiceSections / billingRoleAssignments | Nee | Nee |
> | billingAccounts / billingProfiles / invoiceSections / billingRoleDefinitions | Nee | Nee |
> | billingAccounts / billingProfiles / invoiceSections / billingSubscriptions | Nee | Nee |
> | billingAccounts / billingProfiles / invoiceSections / createBillingRoleAssignment | Nee | Nee |
> | billingAccounts / billingProfiles / invoiceSections / initiateTransfer | Nee | Nee |
> | billingAccounts/billingProfiles/invoiceSections/Products | Nee | Nee |
> | billingAccounts/billingProfiles/invoiceSections/Products/overdracht | Nee | Nee |
> | billingAccounts/billingProfiles/invoiceSections/Products/updateAutoRenew | Nee | Nee |
> | billingAccounts/billingProfiles/invoiceSections/trans acties | Nee | Nee |
> | billingAccounts/billingProfiles/invoiceSections/transfers | Nee | Nee |
> | billingAccounts / BillingProfiles / patchOperations | Nee | Nee |
> | billingAccounts / billingProfiles / paymentMethods | Nee | Nee |
> | billingAccounts/billingProfiles/beleid | Nee | Nee |
> | billingAccounts/billingProfiles/prijzen overzicht | Nee | Nee |
> | billingAccounts / billingProfiles / pricesheetDownloadOperations | Nee | Nee |
> | billingAccounts/billingProfiles/producten | Nee | Nee |
> | billingAccounts/billingProfiles/reserve ringen | Nee | Nee |
> | billingAccounts/billingProfiles/trans acties | Nee | Nee |
> | billingAccounts / billingProfiles / validateDetachPaymentMethodEligibility | Nee | Nee |
> | billingAccounts / billingRoleAssignments | Nee | Nee |
> | billingAccounts / billingRoleDefinitions | Nee | Nee |
> | billingAccounts / billingSubscriptions | Nee | Nee |
> | billingAccounts/billingSubscriptions/facturen | Nee | Nee |
> | billingAccounts / createBillingRoleAssignment | Nee | Nee |
> | billingAccounts / createInvoiceSectionOperations | Nee | Nee |
> | billingAccounts/klanten | Nee | Nee |
> | billingAccounts/klanten/billingPermissions | Nee | Nee |
> | billingAccounts/klanten/billingSubscriptions | Nee | Nee |
> | billingAccounts/klanten/initiateTransfer | Nee | Nee |
> | billingAccounts/klanten/beleids regels | Nee | Nee |
> | billingAccounts/klanten/producten | Nee | Nee |
> | billingAccounts/klanten/trans acties | Nee | Nee |
> | billingAccounts/klanten/overdrachten | Nee | Nee |
> | billingAccounts/afdelingen | Nee | Nee |
> | billingAccounts/afdelingen/billingPermissions | Nee | Nee |
> | billingAccounts/afdelingen/billingRoleAssignments | Nee | Nee |
> | billingAccounts/afdelingen/billingRoleDefinitions | Nee | Nee |
> | billingAccounts / enrollmentAccounts | Nee | Nee |
> | billingAccounts / enrollmentAccounts / billingPermissions | Nee | Nee |
> | billingAccounts / enrollmentAccounts / billingRoleAssignments | Nee | Nee |
> | billingAccounts / enrollmentAccounts / billingRoleDefinitions | Nee | Nee |
> | billingAccounts/facturen | Nee | Nee |
> | billingAccounts/facturen/trans acties | Nee | Nee |
> | billingAccounts / invoiceSections | Nee | Nee |
> | billingAccounts / invoiceSections / billingSubscriptionMoveOperations | Nee | Nee |
> | billingAccounts / invoiceSections / billingSubscriptions | Nee | Nee |
> | billingAccounts/invoiceSections/billingSubscriptions/overdracht | Nee | Nee |
> | billingAccounts/invoiceSections/verhoogde bevoegdheid | Nee | Nee |
> | billingAccounts / invoiceSections / initiateTransfer | Nee | Nee |
> | billingAccounts / invoiceSections / patchOperations | Nee | Nee |
> | billingAccounts / invoiceSections / productMoveOperations | Nee | Nee |
> | billingAccounts/invoiceSections/producten | Nee | Nee |
> | billingAccounts/invoiceSections/producten/overdracht | Nee | Nee |
> | billingAccounts/invoiceSections/Products/updateAutoRenew | Nee | Nee |
> | billingAccounts/invoiceSections/trans acties | Nee | Nee |
> | billingAccounts/invoiceSections/overdrachten | Nee | Nee |
> | billingAccounts / lineOfCredit | Nee | Nee |
> | billingAccounts / patchOperations | Nee | Nee |
> | billingAccounts / paymentMethods | Nee | Nee |
> | billingAccounts/producten | Nee | Nee |
> | billingAccounts/reserve ringen | Nee | Nee |
> | billingAccounts/trans acties | Nee | Nee |
> | billingPeriods | Nee | Nee |
> | billingPermissions | Nee | Nee |
> | billingProperty | Nee | Nee |
> | billingRoleAssignments | Nee | Nee |
> | billingRoleDefinitions | Nee | Nee |
> | createBillingRoleAssignment | Nee | Nee |
> | afdeling | Nee | Nee |
> | enrollmentAccounts | Nee | Nee |
> | factureer | Nee | Nee |
> | Making | Nee | Nee |
> | overdrachten/acceptTransfer | Nee | Nee |
> | overdrachten/declineTransfer | Nee | Nee |
> | overdrachten/operationStatus | Nee | Nee |
> | overdrachten/validateTransfer | Nee | Nee |
> | validateAddress | Nee | Nee |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | mapApis | Ja | Ja |
> | updateCommunicationPreference | Nee | Nee |

## <a name="microsoftblockchain"></a>Micro soft. Block Chain

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | blockchainMembers | Ja | Ja |
> | cordaMembers | Ja | Ja |
> | Volg | Ja | Ja |

## <a name="microsoftblockchaintokens"></a>Micro soft. BlockchainTokens

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | TokenServices | Ja | Ja |
> | TokenServices / BlockchainNetworks | Nee | Nee |
> | TokenServices/groepen | Nee | Nee |
> | TokenServices/groepen/accounts | Nee | Nee |
> | TokenServices / TokenTemplates | Nee | Nee |

## <a name="microsoftblueprint"></a>Micro soft. blauw druk

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | blueprintAssignments | Nee | Nee |
> | blueprintAssignments / assignmentOperations | Nee | Nee |
> | blueprintAssignments/bewerkingen | Nee | Nee |
> | blauw drukken | Nee | Nee |
> | blauw drukken/artefacten | Nee | Nee |
> | blauw drukken/versies | Nee | Nee |
> | blauw drukken/versies/artefacten | Nee | Nee |

## <a name="microsoftbotservice"></a>Micro soft. BotService

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | botServices | Ja | Ja |
> | botServices/kanalen | Nee | Nee |
> | botServices/verbindingen | Nee | Nee |
> | talen | Nee | Nee |
> | sjablonen | Nee | Nee |

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | Redis | Ja | Ja |
> | Redis/EventGridFilters | Nee | Nee |
> | Redis/privateEndpointConnectionProxies | Nee | Nee |
> | Redis/privateEndpointConnectionProxies/valideren | Nee | Nee |
> | Redis/privateEndpointConnections | Nee | Nee |
> | Redis/privateLinkResources | Nee | Nee |
> | redisEnterprise | Ja | Ja |
> | RedisEnterprise / privateEndpointConnectionProxies | Nee | Nee |
> | RedisEnterprise/privateEndpointConnectionProxies/valideren | Nee | Nee |
> | RedisEnterprise / privateEndpointConnections | Nee | Nee |
> | RedisEnterprise / privateLinkResources | Nee | Nee |

## <a name="microsoftcapacity"></a>Micro soft. capacity

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | appliedReservations | Nee | Nee |
> | autoQuotaIncrease | Nee | Nee |
> | calculateExchange | Nee | Nee |
> | calculatePrice | Nee | Nee |
> | calculatePurchasePrice | Nee | Nee |
> | catalogi | Nee | Nee |
> | commercialReservationOrders | Nee | Nee |
> | Exchange | Nee | Nee |
> | ownReservations | Nee | Nee |
> | placePurchaseOrder | Nee | Nee |
> | reservationOrders | Nee | Nee |
> | reservationOrders / calculateRefund | Nee | Nee |
> | reservationOrders/samen voegen | Nee | Nee |
> | reservationOrders/reserve ringen | Nee | Nee |
> | reservationOrders/reserve ringen/revisies | Nee | Nee |
> | reservationOrders/retour neren | Nee | Nee |
> | reservationOrders/splitsen | Nee | Nee |
> | reservationOrders/swap | Nee | Nee |
> | ringen | Nee | Nee |
> | resourceProviders | Nee | Nee |
> | resources | Nee | Nee |
> | validateReservationOrder | Nee | Nee |

## <a name="microsoftcdn"></a>Micro soft. CDN

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | CdnWebApplicationFirewallManagedRuleSets | Nee | Nee |
> | CdnWebApplicationFirewallPolicies | Ja | Ja |
> | edgenodes | Nee | Nee |
> | profielen | Ja | Ja |
> | profielen/eind punten | Ja | Ja |
> | profielen/eind punten/customdomains | Nee | Nee |
> | profielen/eind punten/origingroups | Nee | Nee |
> | profielen/eind punten/oorsprong | Nee | Nee |
> | validateProbe | Nee | Nee |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | certificateOrders | Ja | Ja |
> | certificateOrders/certificaten | Nee | Nee |
> | validateCertificateRegistrationInformation | Nee | Nee |

## <a name="microsoftchangeanalysis"></a>Micro soft. ChangeAnalysis

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | profiel | Nee | Nee |
> | resourceChanges | Nee | Nee |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | mogelijkheden | Nee | Nee |
> | Domein naam | Nee | Nee |
> | Domein naam/mogelijkheden | Nee | Nee |
> | Domein naam/internalLoadBalancers | Nee | Nee |
> | Domein naam/serviceCertificates | Nee | Nee |
> | Domein naam/sleuven | Nee | Nee |
> | Domein naam/sleuven/rollen | Nee | Nee |
> | Domein naam/sleuven/rollen/metricDefinitions | Nee | Nee |
> | Domein naam/sleuven/rollen/metrieken | Nee | Nee |
> | moveSubscriptionResources | Nee | Nee |
> | operatingSystemFamilies | Nee | Nee |
> | operatingSystems | Nee | Nee |
> | quotas | Nee | Nee |
> | resourceTypes | Nee | Nee |
> | validateSubscriptionMoveAvailability | Nee | Nee |
> | Informatie | Nee | Nee |
> | Informatie/diagnosticSettings | Nee | Nee |
> | Informatie/metricDefinitions | Nee | Nee |
> | Informatie/meet waarden | Nee | Nee |

## <a name="microsoftclassicinfrastructuremigrate"></a>Micro soft. ClassicInfrastructureMigrate

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | classicInfrastructureResources | Nee | Nee |

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | mogelijkheden | Nee | Nee |
> | expressRouteCrossConnections | Nee | Nee |
> | expressRouteCrossConnections/peerings | Nee | Nee |
> | gatewaySupportedDevices | Nee | Nee |
> | networkSecurityGroups | Nee | Nee |
> | quotas | Nee | Nee |
> | reservedIps | Nee | Nee |
> | virtualNetworks | Nee | Nee |
> | virtualNetworks/remoteVirtualNetworkPeeringProxies | Nee | Nee |
> | virtualNetworks/virtualNetworkPeerings | Nee | Nee |

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | mogelijkheden | Nee | Nee |
> | cd's | Nee | Nee |
> | images | Nee | Nee |
> | osImages | Nee | Nee |
> | osPlatformImages | Nee | Nee |
> | publicImages | Nee | Nee |
> | quotas | Nee | Nee |
> | Storage accounts | Nee | Nee |
> | Storage accounts/blobServices | Nee | Nee |
> | Storage accounts/fileServices | Nee | Nee |
> | Storage accounts/metricDefinitions | Nee | Nee |
> | Storage accounts/meet waarden | Nee | Nee |
> | Storage accounts/queueServices | Nee | Nee |
> | Storage accounts/Services | Nee | Nee |
> | Storage accounts/Services/diagnosticSettings | Nee | Nee |
> | Storage accounts/Services/metricDefinitions | Nee | Nee |
> | Storage accounts/Services/metrische gegevens | Nee | Nee |
> | Storage accounts/tableServices | Nee | Nee |
> | Storage accounts/vmImages | Nee | Nee |
> | vmImages | Nee | Nee |

## <a name="microsoftcodespaces"></a>Micro soft. Codespaces

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | plant | Ja | Nee |
> | registeredSubscriptions | Nee | Nee |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | accounts | Ja | Ja |
> | accounts/privateEndpointConnectionProxies | Nee | Nee |
> | accounts/privateEndpointConnections | Nee | Nee |
> | accounts/privateLinkResources | Nee | Nee |

## <a name="microsoftcommerce"></a>Microsoft.Commerce

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | RateCard | Nee | Nee |
> | UsageAggregates | Nee | Nee |

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | Availability sets | Ja | Ja |
> | cloudServices | Ja | Ja |
> | cloudServices / networkInterfaces | Nee | Nee |
> | cloudServices/publicIPAddresses | Nee | Nee |
> | cloudServices / roleInstances | Nee | Nee |
> | cloudServices / roleInstances / networkInterfaces | Nee | Nee |
> | cloudServices/rollen | Nee | Nee |
> | diskAccesses | Ja | Ja |
> | diskEncryptionSets | Ja | Ja |
> | cd's | Ja | Ja |
> | Galerij | Ja | Ja |
> | galerieën/toepassingen | Nee | Nee |
> | galerieën/toepassingen/versies | Nee | Nee |
> | galerieën/afbeeldingen | Nee | Nee |
> | galerieën/afbeeldingen/versies | Nee | Nee |
> | hostGroups | Ja | Ja |
> | hostGroups/hosts | Ja | Ja |
> | images | Ja | Ja |
> | proximityPlacementGroups | Ja | Ja |
> | restorePointCollections | Ja | Ja |
> | restorePointCollections / restorePoints | Nee | Nee |
> | sharedVMExtensions | Ja | Ja |
> | sharedVMExtensions/versies | Nee | Nee |
> | sharedVMImages | Ja | Ja |
> | sharedVMImages/versies | Nee | Nee |
> | momentopnamen | Ja | Ja |
> | sshPublicKeys | Ja | Ja |
> | Informatie | Ja | Ja |
> | Informatie/extensies | Ja | Ja |
> | Informatie/metricDefinitions | Nee | Nee |
> | Informatie/runCommand | Ja | Ja |
> | virtualMachineScaleSets | Ja | Ja |
> | virtualMachineScaleSets/extensies | Nee | Nee |
> | virtualMachineScaleSets/networkInterfaces | Nee | Nee |
> | virtualMachineScaleSets/publicIPAddresses | Ja | Nee |
> | virtualMachineScaleSets/informatie | Nee | Nee |
> | virtualMachineScaleSets/informatie/networkInterfaces | Nee | Nee |

> [!NOTE]
> U kunt geen label toevoegen aan een virtuele machine die is gemarkeerd als gegeneraliseerd. U markeert een virtuele machine als gegeneraliseerd met [set-AzVm-gegeneraliseerd](/powershell/module/Az.Compute/Set-AzVM) of [AZ VM generalize](/cli/azure/vm#az-vm-generalize).

## <a name="microsoftconnectedcache"></a>Micro soft. ConnectedCache

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | CacheNodes | Ja | Ja |

## <a name="microsoftconsumption"></a>Micro soft. verbruik

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | AggregatedCost | Nee | Nee |
> | Tegoeden | Nee | Nee |
> | Budgetten | Nee | Nee |
> | Kosten | Nee | Nee |
> | CostTags | Nee | Nee |
> | aanvragen | Nee | Nee |
> | events | Nee | Nee |
> | Prognoses | Nee | Nee |
> | extra | Nee | Nee |
> | Markt plaatsen | Nee | Nee |
> | Prijzenoverzichten | Nee | Nee |
> | producten | Nee | Nee |
> | ReservationDetails | Nee | Nee |
> | ReservationRecommendationDetails | Nee | Nee |
> | ReservationRecommendations | Nee | Nee |
> | ReservationSummaries | Nee | Nee |
> | ReservationTransactions | Nee | Nee |
> | Tags | Nee | Nee |
> | tenants | Nee | Nee |
> | Termen | Nee | Nee |
> | UsageDetails | Nee | Nee |

## <a name="microsoftcontainerinstance"></a>Micro soft. ContainerInstance

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | containerGroups | Ja | Ja |
> | serviceAssociationLinks | Nee | Nee |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | registers | Ja | Ja |
> | registers/agentPools | Ja | Ja |
> | registers/builds | Nee | Nee |
> | registers/builds/annuleren | Nee | Nee |
> | registers/builds/getLogLink | Nee | Nee |
> | registers/buildTasks | Ja | Ja |
> | registers/buildTasks/stappen | Nee | Nee |
> | registers/eventGridFilters | Nee | Nee |
> | registers/exportPipelines | Nee | Nee |
> | registers/generateCredentials | Nee | Nee |
> | registers/getBuildSourceUploadUrl | Nee | Nee |
> | registers/GetCredentials | Nee | Nee |
> | registers/importImage | Nee | Nee |
> | registers/importPipelines | Nee | Nee |
> | registers/pipelineRuns | Nee | Nee |
> | registers/privateEndpointConnectionProxies | Nee | Nee |
> | registers/privateEndpointConnectionProxies/valideren | Nee | Nee |
> | registers/privateEndpointConnections | Nee | Nee |
> | registers/privateLinkResources | Nee | Nee |
> | registers/queueBuild | Nee | Nee |
> | registers/regenerateCredential | Nee | Nee |
> | registers/regenerateCredentials | Nee | Nee |
> | registers/replicaties | Ja | Ja |
> | registers/uitvoeringen | Nee | Nee |
> | registers/uitvoeringen/annuleren | Nee | Nee |
> | registers/scheduleRun | Nee | Nee |
> | registers/scopeMaps | Nee | Nee |
> | registers/taskRuns | Nee | Nee |
> | registers/taken | Ja | Ja |
> | registers/tokens | Nee | Nee |
> | registers/updatePolicies | Nee | Nee |
> | registers/webhooks | Ja | Ja |
> | registers/webhooks/getCallbackConfig | Nee | Nee |
> | registers/webhooks/ping | Nee | Nee |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | containerServices | Ja | Ja |
> | managedClusters | Ja | Ja |
> | openShiftManagedClusters | Ja | Ja |

## <a name="microsoftcostmanagement"></a>Micro soft. CostManagement

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | Waarschuwingen | Nee | Nee |
> | BillingAccounts | Nee | Nee |
> | Budgetten | Nee | Nee |
> | CloudConnectors | Nee | Nee |
> | Connectors | Ja | Ja |
> | costAllocationRules | Nee | Nee |
> | Afdelingen | Nee | Nee |
> | Dimensies | Nee | Nee |
> | EnrollmentAccounts | Nee | Nee |
> | Exports | Nee | Nee |
> | ExternalBillingAccounts | Nee | Nee |
> | ExternalBillingAccounts/waarschuwingen | Nee | Nee |
> | ExternalBillingAccounts/dimensies | Nee | Nee |
> | ExternalBillingAccounts/prognose | Nee | Nee |
> | ExternalBillingAccounts/query | Nee | Nee |
> | ExternalSubscriptions | Nee | Nee |
> | ExternalSubscriptions/waarschuwingen | Nee | Nee |
> | ExternalSubscriptions/dimensies | Nee | Nee |
> | ExternalSubscriptions/prognose | Nee | Nee |
> | ExternalSubscriptions/query | Nee | Nee |
> | Prognose | Nee | Nee |
> | Inzichten | Nee | Nee |
> | Query’s uitvoeren | Nee | Nee |
> | registreren | Nee | Nee |
> | Reportconfigs | Nee | Nee |
> | Rapporten | Nee | Nee |
> | Instellingen | Nee | Nee |
> | showbackRules | Nee | Nee |
> | Weergaven | Nee | Nee |

## <a name="microsoftcustomerlockbox"></a>Micro soft. CustomerLockbox

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | requests | Nee | Nee |

## <a name="microsoftcustomproviders"></a>Micro soft. CustomProviders

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | lidkoppelingen | Nee | Nee |
> | resourceProviders | Ja | Ja |

## <a name="microsoftd365customerinsights"></a>Micro soft. D365CustomerInsights

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | vaak | Ja | Ja |

## <a name="microsoftdatabox"></a>Micro soft. DataBox

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | functies | Ja | Ja |

## <a name="microsoftdataboxedge"></a>Micro soft. DataBoxEdge

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | DataBoxEdgeDevices | Ja | Ja |

## <a name="microsoftdatabricks"></a>Micro soft. Databricks

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | workspaces | Ja | Ja |
> | werk ruimten/dbWorkspaces | Nee | Nee |
> | werk ruimten/virtualNetworkPeerings | Nee | Nee |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | catalogi | Ja | Ja |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | dataFactories | Ja | Ja |
> | dataFactories / diagnosticSettings | Nee | Nee |
> | dataFactories / metricDefinitions | Nee | Nee |
> | dataFactorySchema | Nee | Nee |
> | factory's | Ja | Ja |
> | fabrieken/integrationRuntimes | Nee | Nee |

> [!NOTE]
> Als u Azure SSIS Integration Runtimes hebt in uw data factory, worden de lopende kosten gelabeld met data factory Tags. Het uitvoeren van Azure-SSIS-integratie-runtime moet worden gestopt en opnieuw worden gestart om nieuwe data factory Tags te kunnen Toep assen op de lopende kosten.

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | accounts | Ja | Ja |
> | accounts/dataLakeStoreAccounts | Nee | Nee |
> | accounts/Storage accounts | Nee | Nee |
> | accounts/Storage accounts/containers | Nee | Nee |
> | accounts/transferAnalyticsUnits | Nee | Nee |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | accounts | Ja | Ja |
> | accounts/eventGridFilters | Nee | Nee |
> | accounts/firewallRules | Nee | Nee |

## <a name="microsoftdatamigration"></a>Micro soft. DataMigration

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | services | Nee | Nee |
> | Services/projecten | Nee | Nee |

## <a name="microsoftdataprotection"></a>Micro soft. DataProtection

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | BackupVaults | Ja | Ja |
> | ResourceOperationGateKeepers | Ja | Ja |

## <a name="microsoftdatashare"></a>Micro soft. DataShare

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | accounts | Ja | Ja |
> | accounts/shares | Nee | Nee |
> | accounts/shares/gegevens sets | Nee | Nee |
> | accounts/shares/uitnodigingen | Nee | Nee |
> | accounts/shares/providersharesubscriptions | Nee | Nee |
> | accounts/shares/synchronizationSettings | Nee | Nee |
> | accounts/sharesubscriptions | Nee | Nee |
> | accounts/sharesubscriptions/consumerSourceDataSets | Nee | Nee |
> | accounts/sharesubscriptions/datasetmappings | Nee | Nee |
> | accounts/sharesubscriptions/triggers | Nee | Nee |

## <a name="microsoftdbformariadb"></a>Micro soft. DBforMariaDB

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | Server | Ja | Ja |
> | servers/adviseurs | Nee | Nee |
> | servers/sleutels | Nee | Nee |
> | servers/privateEndpointConnectionProxies | Nee | Nee |
> | servers/privateEndpointConnections | Nee | Nee |
> | servers/privateLinkResources | Nee | Nee |
> | servers/queryTexts | Nee | Nee |
> | servers/recoverableServers | Nee | Nee |
> | servers/starten | Nee | Nee |
> | servers/stoppen | Nee | Nee |
> | servers/topQueryStatistics | Nee | Nee |
> | servers/virtualNetworkRules | Nee | Nee |
> | servers/waitStatistics | Nee | Nee |

## <a name="microsoftdbformysql"></a>Micro soft. DBforMySQL

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | flexibleServers | Ja | Ja |
> | Server | Ja | Ja |
> | servers/adviseurs | Nee | Nee |
> | servers/sleutels | Nee | Nee |
> | servers/privateEndpointConnectionProxies | Nee | Nee |
> | servers/privateEndpointConnections | Nee | Nee |
> | servers/privateLinkResources | Nee | Nee |
> | servers/queryTexts | Nee | Nee |
> | servers/recoverableServers | Nee | Nee |
> | servers/starten | Nee | Nee |
> | servers/stoppen | Nee | Nee |
> | servers/topQueryStatistics | Nee | Nee |
> | servers/upgrade | Nee | Nee |
> | servers/virtualNetworkRules | Nee | Nee |
> | servers/waitStatistics | Nee | Nee |

## <a name="microsoftdbforpostgresql"></a>Micro soft. DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | flexibleServers | Ja | Ja |
> | serverGroups | Ja | Ja |
> | Server | Ja | Ja |
> | servers/adviseurs | Nee | Nee |
> | servers/sleutels | Nee | Nee |
> | servers/privateEndpointConnectionProxies | Nee | Nee |
> | servers/privateEndpointConnections | Nee | Nee |
> | servers/privateLinkResources | Nee | Nee |
> | servers/queryTexts | Nee | Nee |
> | servers/recoverableServers | Nee | Nee |
> | servers/topQueryStatistics | Nee | Nee |
> | servers/virtualNetworkRules | Nee | Nee |
> | servers/waitStatistics | Nee | Nee |
> | serversv2 | Ja | Ja |

## <a name="microsoftdeploymentmanager"></a>Micro soft. DeploymentManager

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | artifactSources | Ja | Ja |
> | implementaties | Ja | Ja |
> | serviceTopologies | Ja | Ja |
> | serviceTopologies/Services | Ja | Ja |
> | serviceTopologies/Services/serviceUnits | Ja | Ja |
> | stappen | Ja | Ja |

## <a name="microsoftdesktopvirtualization"></a>Micro soft. DesktopVirtualization

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | applicationgroups | Ja | Ja |
> | applicationgroups/toepassingen | Nee | Nee |
> | applicationgroups/Bureau bladen | Nee | Nee |
> | applicationgroups / startmenuitems | Nee | Nee |
> | hostpools | Ja | Ja |
> | hostpools / msixpackages | Nee | Nee |
> | hostpools / sessionhosts | Nee | Nee |
> | hostpools / sessionhosts / usersessions | Nee | Nee |
> | hostpools / usersessions | Nee | Nee |
> | workspaces | Ja | Ja |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | ElasticPools | Ja | Ja |
> | ElasticPools / IotHubTenants | Ja | Ja |
> | ElasticPools/IotHubTenants/securitySettings | Nee | Nee |
> | IotHubs | Ja | Ja |
> | IotHubs/eventGridFilters | Nee | Nee |
> | IotHubs/securitySettings | Nee | Nee |
> | ProvisioningServices | Ja | Ja |
> | gebruik | Nee | Nee |

## <a name="microsoftdeviceupdate"></a>Micro soft. DeviceUpdate

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | accounts | Ja | Ja |
> | accounts/exemplaren | Ja | Ja |

## <a name="microsoftdevops"></a>Micro soft. DevOps

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | pijp lijnen | Ja | Ja |

## <a name="microsoftdevspaces"></a>Micro soft. DevSpaces

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | fungeren | Ja | Ja |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | labcenters | Ja | Ja |
> | Labs | Ja | Ja |
> | Labs/omgevingen | Ja | Ja |
> | Labs-serviceRunners | Ja | Ja |
> | Labs-informatie | Ja | Ja |
> | schema's | Ja | Ja |

## <a name="microsoftdigitaltwins"></a>Micro soft. DigitalTwins

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | digitalTwinsInstances | Ja | Ja |
> | digitalTwinsInstances/eind punten | Nee | Nee |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | databaseAccountNames | Nee | Nee |
> | databaseAccounts | Ja | Ja |
> | restorableDatabaseAccounts | Nee | Nee |

## <a name="microsoftdomainregistration"></a>Micro soft. DomainRegistration

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | domeinen | Ja | Ja |
> | domeinen/domainOwnershipIdentifiers | Nee | Nee |
> | generateSsoRequest | Nee | Nee |
> | topLevelDomains | Nee | Nee |
> | validateDomainRegistrationInformation | Nee | Nee |

## <a name="microsoftdynamicslcs"></a>Microsoft.DynamicsLcs

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | lcsprojects | Nee | Nee |
> | lcsprojects / clouddeployments | Nee | Nee |
> | lcsprojects/connectors | Nee | Nee |

## <a name="microsoftenterpriseknowledgegraph"></a>Micro soft. EnterpriseKnowledgeGraph

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | services | Ja | Ja |

## <a name="microsofteventgrid"></a>Micro soft. EventGrid

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | domeinen | Ja | Ja |
> | domeinen/onderwerpen | Nee | Nee |
> | eventSubscriptions | Nee | Nee |
> | extensionTopics | Nee | Nee |
> | partnerNamespaces | Ja | Ja |
> | partnerNamespaces/eventChannels | Nee | Nee |
> | partnerRegistrations | Ja | Ja |
> | partnerTopics | Ja | Ja |
> | partnerTopics / eventSubscriptions | Nee | Nee |
> | systemTopics | Ja | Ja |
> | systemTopics / eventSubscriptions | Nee | Nee |
> | onderwerp | Ja | Ja |
> | topicTypes | Nee | Nee |

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | clusters | Ja | Ja |
> | naam ruimten | Ja | Ja |
> | naam ruimten/authorizationrules | Nee | Nee |
> | naam ruimten/disasterrecoveryconfigs | Nee | Nee |
> | naam ruimten/Event hubs | Nee | Nee |
> | naam ruimten/Event hubs/authorizationrules | Nee | Nee |
> | naam ruimten/Event hubs/consumergroups | Nee | Nee |
> | naam ruimten/networkrulesets | Nee | Nee |
> | naam ruimten/privateEndpointConnections | Nee | Nee |

## <a name="microsoftexperimentation"></a>Micro soft. experimenten

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | experimentWorkspaces | Ja | Ja |

## <a name="microsoftfalcon"></a>Micro soft. Falcon

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | naam ruimten | Ja | Ja |

## <a name="microsoftfeatures"></a>Microsoft.Features

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | featureProviders | Nee | Nee |
> | features | Nee | Nee |
> | providers | Nee | Nee |
> | subscriptionFeatureRegistrations | Nee | Nee |

## <a name="microsoftgallery"></a>Micro soft. Gallery

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | Inge | Nee | Nee |
> | galleryitems | Nee | Nee |
> | generateartifactaccessuri | Nee | Nee |
> | myareas | Nee | Nee |
> | myareas/gebieden | Nee | Nee |
> | myareas/gebieden/gebieden | Nee | Nee |
> | myareas/gebieden/gebieden/galleryitems | Nee | Nee |
> | myareas/areas/galleryitems | Nee | Nee |
> | myareas / galleryitems | Nee | Nee |
> | registreren | Nee | Nee |
> | resources | Nee | Nee |
> | retrieveresourcesbyid | Nee | Nee |

## <a name="microsoftgenomics"></a>Micro soft. Genomics

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | accounts | Ja | Ja |

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | autoManagedAccounts | Ja | Ja |
> | autoManagedVmConfigurationProfiles | Ja | Ja |
> | configurationProfileAssignments | Nee | Nee |
> | guestConfigurationAssignments | Nee | Nee |
> | software | Nee | Nee |
> | softwareUpdateProfile | Nee | Nee |
> | softwareUpdates | Nee | Nee |

## <a name="microsofthanaonazure"></a>Micro soft. HanaOnAzure

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | hanaInstances | Ja | Ja |
> | sapMonitors | Ja | Ja |

## <a name="microsofthardwaresecuritymodules"></a>Micro soft. HardwareSecurityModules

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | dedicatedHSMs | Ja | Ja |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | clusters | Ja | Ja |
> | clusters/toepassingen | Nee | Nee |

## <a name="microsofthealthcareapis"></a>Micro soft. HealthcareApis

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | services | Ja | Ja |
> | Services/iomtconnectors | Nee | Nee |
> | Services/iomtconnectors/verbindingen | Nee | Nee |
> | Services/iomtconnectors/toewijzingen | Nee | Nee |
> | Services/privateEndpointConnectionProxies | Nee | Nee |
> | Services/privateEndpointConnections | Nee | Nee |
> | Services/privateLinkResources | Nee | Nee |

## <a name="microsofthybridcompute"></a>Microsoft.HybridCompute

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | apparaten | Ja | Ja |
> | machines/assessPatches | Nee | Nee |
> | computers/uitbrei dingen | Ja | Ja |
> | machines/installPatches | Nee | Nee |

## <a name="microsofthybriddata"></a>Micro soft. HybridData

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | dataManagers | Ja | Ja |

## <a name="microsofthybridnetwork"></a>Micro soft. HybridNetwork

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | devices | Ja | Ja |
> | networkFunctions | Ja | Ja |
> | networkFunctionVendors | Nee | Nee |
> | registeredSubscriptions | Nee | Nee |
> | crediteur | Nee | Nee |
> | leveranciers/vendorSkus | Nee | Nee |
> | leveranciers/vendorSkus/previewSubscriptions | Nee | Nee |
> | virtualNetworkFunctions | Ja | Ja |
> | virtualNetworkFunctionVendors | Nee | Nee |

## <a name="microsofthydra"></a>Micro soft. Hydra

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | materialen | Ja | Ja |
> | networkScopes | Ja | Ja |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | functies | Ja | Ja |

## <a name="microsoftinsights"></a>Microsoft.Insights

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | actionGroups | Ja | Ja |
> | activityLogAlerts | Ja | Ja |
> | alertrules | Ja | Ja |
> | autoscalesettings | Ja | Ja |
> | materialen | Ja | Ja |
> | onderdelen/linkedStorageAccounts | Nee | Nee |
> | onderdelen/ProactiveDetectionConfigs | Nee | Nee |
> | diagnosticSettings | Nee | Nee |
> | guestDiagnosticSettings | Ja | Ja |
> | guestDiagnosticSettingsAssociation | Ja | Ja |
> | logprofiles | Ja | Ja |
> | metricAlerts | Ja | Ja |
> | privateLinkScopes | Ja | Ja |
> | privateLinkScopes / privateEndpointConnections | Nee | Nee |
> | privateLinkScopes / scopedResources | Nee | Nee |
> | queryPacks | Ja | Ja |
> | queryPacks/query's | Nee | Nee |
> | scheduledQueryRules | Ja | Ja |
> | webtests | Ja | Ja |
> | werkmappen | Ja | Ja |
> | workbooktemplates | Ja | Ja |

## <a name="microsoftintune"></a>Microsoft.Intune

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | diagnosticSettings | Nee | Nee |
> | diagnosticSettingsCategories | Nee | Nee |

## <a name="microsoftiotcentral"></a>Micro soft. IoTCentral

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | appTemplates | Nee | Nee |
> | IoTApps | Ja | Ja |

## <a name="microsoftiotspaces"></a>Micro soft. IoTSpaces

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | Graph | Ja | Ja |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | deletedVaults | Nee | Nee |
> | hsmPools | Ja | Ja |
> | managedHSMs | Ja | Ja |
> | kluizen | Ja | Ja |
> | kluizen/accessPolicies | Nee | Nee |
> | kluizen/eventGridFilters | Nee | Nee |
> | kluizen/sleutels | Nee | Nee |
> | kluizen/sleutels/versies | Nee | Nee |
> | kluizen/geheimen | Nee | Nee |

## <a name="microsoftkubernetes"></a>Microsoft.Kubernetes

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | connectedClusters | Ja | Ja |
> | registeredSubscriptions | Nee | Nee |

## <a name="microsoftkubernetesconfiguration"></a>Micro soft. KubernetesConfiguration

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | Extensions | Nee | Nee |
> | sourceControlConfigurations | Nee | Nee |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | clusters | Ja | Ja |
> | clusters/attacheddatabaseconfigurations | Nee | Nee |
> | clusters/data bases | Nee | Nee |
> | clusters/data bases/dataConnections | Nee | Nee |
> | clusters/data bases/eventhubconnections | Nee | Nee |
> | clusters/data bases/principalassignments | Nee | Nee |
> | clusters/dataConnections | Nee | Nee |
> | clusters/principalassignments | Nee | Nee |
> | clusters/sharedidentities | Nee | Nee |

## <a name="microsoftlabservices"></a>Micro soft. LabServices

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | labaccounts | Ja | Nee |
> | gebruikers | Nee | Nee |

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | hostingEnvironments | Ja | Ja |
> | integrationAccounts | Ja | Ja |
> | integrationServiceEnvironments | Ja | Ja |
> | integrationServiceEnvironments/Beheerdeapi's | Nee | Nee |
> | isolatedEnvironments | Ja | Ja |
> | stroom | Ja | Ja |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | commitmentPlans | Ja | Ja |
> | webservices | Ja | Ja |
> | Workspaces | Ja | Ja |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | workspaces | Ja | Ja |
> | werk ruimten/batchEndpoints | Ja | Ja |
> | werk ruimten/batchEndpoints/implementaties | Ja | Ja |
> | werk ruimten/codes | Nee | Nee |
> | werk ruimten/codes/versies | Nee | Nee |
> | werk ruimten/reken bewerkingen | Nee | Nee |
> | werk ruimten/gegevens opslag | Nee | Nee |
> | werk ruimten/eventGridFilters | Nee | Nee |
> | werk ruimten/taken | Nee | Nee |
> | werk ruimten/labelingJobs | Nee | Nee |
> | werk ruimten/linkedServices | Nee | Nee |
> | werk ruimten/modellen | Nee | Nee |
> | werk ruimten/modellen/versies | Nee | Nee |
> | werk ruimten/onlineEndpoints | Ja | Ja |
> | werk ruimten/onlineEndpoints/implementaties | Ja | Ja |
 
> [!NOTE]
> Werkruimte Tags worden niet door gegeven aan reken clusters en reken instanties. 

## <a name="microsoftmaintenance"></a>Micro soft. onderhoud

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | applyUpdates | Nee | Nee |
> | configurationAssignments | Nee | Nee |
> | maintenanceConfigurations | Ja | Ja |
> | publicMaintenanceConfigurations | Nee | Nee |
> | updates | Nee | Nee |

## <a name="microsoftmanagedidentity"></a>Micro soft. ManagedIdentity

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | Identiteiten | Nee | Nee |
> | userAssignedIdentities | Ja | Ja |

## <a name="microsoftmanagednetwork"></a>Micro soft. ManagedNetwork

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | managedNetworks | Ja | Ja |
> | managedNetworks / managedNetworkGroups | Ja | Ja |
> | managedNetworks / managedNetworkPeeringPolicies | Ja | Ja |
> | melding | Ja | Ja |

## <a name="microsoftmanagedservices"></a>Micro soft. ManagedServices

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | marketplaceRegistrationDefinitions | Nee | Nee |
> | registrationAssignments | Nee | Nee |
> | registrationDefinitions | Nee | Nee |

## <a name="microsoftmanagement"></a>Micro soft. Management

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | getEntities | Nee | Nee |
> | managementGroups | Nee | Nee |
> | managementGroups/instellingen | Nee | Nee |
> | resources | Nee | Nee |
> | startTenantBackfill | Nee | Nee |
> | tenantBackfillStatus | Nee | Nee |

## <a name="microsoftmaps"></a>Micro soft. Maps

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | accounts | Ja | Ja |
> | accounts/eventGridFilters | Nee | Nee |
> | accounts/privateAtlases | Ja | Ja |

## <a name="microsoftmarketplace"></a>Micro soft. Marketplace

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | macc | Nee | Nee |
> | over | Nee | Nee |
> | offerTypes | Nee | Nee |
> | offerTypes/uitgevers | Nee | Nee |
> | offerTypes/uitgevers/aanbiedingen | Nee | Nee |
> | offerTypes/uitgevers/aanbiedingen/abonnementen | Nee | Nee |
> | offerTypes/uitgevers/aanbiedingen/plannen/overeenkomsten | Nee | Nee |
> | offerTypes/uitgevers/aanbiedingen/plannen/configuraties | Nee | Nee |
> | offerTypes/uitgevers/aanbiedingen/plannen/configuraties/importImage | Nee | Nee |
> | privategalleryitems | Nee | Nee |
> | privateStoreClient | Nee | Nee |
> | privateStores | Nee | Nee |
> | privateStores/aanbiedingen | Nee | Nee |
> | producten | Nee | Nee |
> | uitgevers | Nee | Nee |
> | uitgevers/aanbiedingen | Nee | Nee |
> | uitgevers/aanbiedingen/wijzigingen | Nee | Nee |
> | registreren | Nee | Nee |

## <a name="microsoftmarketplaceapps"></a>Micro soft. MarketplaceApps

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | classicDevServices | Ja | Ja |
> | updateCommunicationPreference | Nee | Nee |

## <a name="microsoftmarketplaceordering"></a>Microsoft.MarketplaceOrdering

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | gesloten | Nee | Nee |
> | offertypes | Nee | Nee |

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | Media Services | Ja | Ja |
> | Media Services/accountFilters | Nee | Nee |
> | Media Services/assets | Nee | Nee |
> | Media Services/assets/assetFilters | Nee | Nee |
> | Media Services/contentKeyPolicies | Nee | Nee |
> | Media Services/eventGridFilters | Nee | Nee |
> | Media Services/liveEventOperations | Nee | Nee |
> | Media Services/liveEvents | Ja | Ja |
> | Media Services/liveEvents/liveOutputs | Nee | Nee |
> | Media Services/liveOutputOperations | Nee | Nee |
> | Media Services/mediaGraphs | Nee | Nee |
> | Media Services/privateEndpointConnectionOperations | Nee | Nee |
> | Media Services/privateEndpointConnectionProxies | Nee | Nee |
> | Media Services/privateEndpointConnections | Nee | Nee |
> | Media Services/streamingEndpointOperations | Nee | Nee |
> | Media Services/streamingEndpoints | Ja | Ja |
> | Media Services/streamingLocators | Nee | Nee |
> | Media Services/streamingPolicies | Nee | Nee |
> | Media Services/trans formaties | Nee | Nee |
> | Media Services/trans formaties/taken | Nee | Nee |

## <a name="microsoftmicroservices4spring"></a>Micro soft. Microservices4Spring

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | appClusters | Ja | Ja |

## <a name="microsoftmigrate"></a>Micro soft. migrate

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | assessmentProjects | Ja | Ja |
> | migrateprojects | Ja | Ja |
> | moveCollections | Ja | Ja |
> | projecten | Ja | Ja |

## <a name="microsoftmixedreality"></a>Micro soft. MixedReality

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | holographicsBroadcastAccounts | Ja | Ja |
> | objectUnderstandingAccounts | Ja | Ja |
> | remoteRenderingAccounts | Ja | Ja |
> | spatialAnchorsAccounts | Ja | Ja |

## <a name="microsoftnetapp"></a>Micro soft. NetApp

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | netAppAccounts | Ja | Nee |
> | netAppAccounts / accountBackups | Nee | Nee |
> | netAppAccounts / capacityPools | Ja | Nee |
> | netAppAccounts/capacityPools/volumes | Ja | Nee |
> | netAppAccounts/capacityPools/volumes/moment opnamen | Nee | Nee |

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | applicationGateways | Ja | Ja |
> | applicationGatewayWebApplicationFirewallPolicies | Ja | Ja |
> | applicationSecurityGroups | Ja | Ja |
> | azureFirewallFqdnTags | Nee | Nee |
> | azureFirewalls | Ja | Nee |
> | bastionHosts | Ja | Nee |
> | bgpServiceCommunities | Nee | Nee |
> | inbel | Ja | Ja |
> | ddosCustomPolicies | Ja | Ja |
> | ddosProtectionPlans | Ja | Ja |
> | dnsOperationStatuses | Nee | Nee |
> | dnszones | Ja | Ja |
> | dnszones/A | Nee | Nee |
> | dnszones/AAAA | Nee | Nee |
> | dnszones/alle | Nee | Nee |
> | dnszones/CAA | Nee | Nee |
> | dnszones/CNAME | Nee | Nee |
> | dnszones/MX | Nee | Nee |
> | dnszones/NS | Nee | Nee |
> | dnszones/PTR | Nee | Nee |
> | dnszones/record sets | Nee | Nee |
> | dnszones/SOA | Nee | Nee |
> | dnszones/SRV | Nee | Nee |
> | dnszones/TXT | Nee | Nee |
> | expressRouteCircuits | Ja | Ja |
> | expressRouteCrossConnections | Ja | Ja |
> | expressRouteGateways | Ja | Ja |
> | expressRoutePorts | Ja | Ja |
> | expressRouteServiceProviders | Nee | Nee |
> | firewallPolicies | Ja | Ja |
> | frontdoors | Ja, maar beperkt (Zie [Opmerking hieronder](#frontdoor)) | Yes |
> | frontdoorWebApplicationFirewallManagedRuleSets | Ja, maar beperkt (Zie [Opmerking hieronder](#frontdoor)) | No |
> | frontdoorWebApplicationFirewallPolicies | Ja, maar beperkt (Zie [Opmerking hieronder](#frontdoor)) | Yes |
> | getDnsResourceReference | Nee | Nee |
> | internalNotify | Nee | Nee |
> | ipGroups | Ja | Ja |
> | loadBalancers | Ja | Ja |
> | localNetworkGateways | Ja | Ja |
> | natGateways | Ja | Ja |
> | networkIntentPolicies | Ja | Ja |
> | networkInterfaces | Ja | Ja |
> | networkProfiles | Ja | Ja |
> | networkSecurityGroups | Ja | Ja |
> | networkWatchers | Ja | Ja |
> | networkWatchers / connectionMonitors | Ja | Nee |
> | networkWatchers / flowLogs | Nee | Nee |
> | networkWatchers/lenzen | Ja | Nee |
> | networkWatchers / pingMeshes | Ja | Nee |
> | p2sVpnGateways | Ja | Ja |
> | privateDnsOperationStatuses | Nee | Nee |
> | privateDnsZones | Ja | Ja |
> | privateDnsZones/A | Nee | Nee |
> | privateDnsZones/AAAA | Nee | Nee |
> | privateDnsZones/alle | Nee | Nee |
> | privateDnsZones/CNAME | Nee | Nee |
> | privateDnsZones/MX | Nee | Nee |
> | privateDnsZones/PTR | Nee | Nee |
> | privateDnsZones/SOA | Nee | Nee |
> | privateDnsZones/SRV | Nee | Nee |
> | privateDnsZones/TXT | Nee | Nee |
> | privateDnsZones / virtualNetworkLinks | Ja | Ja |
> | privateEndpoints | Ja | Ja |
> | privateLinkServices | Ja | Ja |
> | publicIPAddresses | Ja | Ja |
> | publicIPPrefixes | Ja | Ja |
> | routeFilters | Ja | Ja |
> | routeTables | Ja | Ja |
> | serviceEndpointPolicies | Ja | Ja |
> | trafficManagerGeographicHierarchies | Nee | Nee |
> | trafficmanagerprofiles | Ja | Ja |
> | trafficmanagerprofiles/heatMaps | Nee | Nee |
> | trafficManagerUserMetricsKeys | Nee | Nee |
> | virtualHubs | Ja | Ja |
> | virtualNetworkGateways | Ja | Ja |
> | virtualNetworks | Ja | Ja |
> | virtualNetworks/subnetten | Nee | Nee |
> | virtualNetworkTaps | Ja | Ja |
> | virtualWans | Ja | Nee |
> | vpnGateways | Ja | Ja |
> | vpnSites | Ja | Ja |
> | webApplicationFirewallPolicies | Ja | Ja |

<a id="frontdoor"></a>

> [!NOTE]
> Voor de Azure front-deur service kunt u Tags Toep assen bij het maken van de resource, maar het bijwerken of toevoegen van labels wordt momenteel niet ondersteund.


## <a name="microsoftnotebooks"></a>Micro soft. notebooks

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | NotebookProxies | Nee | Nee |

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | naam ruimten | Ja | Nee |
> | naam ruimten/notification hubs | Ja | Nee |

## <a name="microsoftobjectstore"></a>Micro soft. ObjectStore

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | osNamespaces | Ja | Ja |

## <a name="microsoftoffazure"></a>Micro soft. OffAzure

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | HyperVSites | Ja | Ja |
> | ImportSites | Ja | Ja |
> | MasterSites | Ja | Ja |
> | ServerSites | Ja | Ja |
> | VMwareSites | Ja | Ja |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | clusters | Ja | Ja |
> | deletedWorkspaces | Nee | Nee |
> | linkTargets | Nee | Nee |
> | storageInsightConfigs | Nee | Nee |
> | workspaces | Ja | Ja |
> | werk ruimten/dataExports | Nee | Nee |
> | werk ruimten/gegevens bronnen | Nee | Nee |
> | werk ruimten/linkedServices | Nee | Nee |
> | werk ruimten/linkedStorageAccounts | Nee | Nee |
> | werk ruimten/meta gegevens | Nee | Nee |
> | werk ruimten/query | Nee | Nee |
> | werk ruimten/scopedPrivateLinkProxies | Nee | Nee |

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | managementassociations | Nee | Nee |
> | managementconfigurations | Ja | Ja |
> | oplossingen | Ja | Ja |
> | Weergaven | Ja | Ja |

## <a name="microsoftpeering"></a>Micro soft. peering

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | legacyPeerings | Nee | Nee |
> | peerAsns | Nee | Nee |
> | Peerings | Ja | Ja |
> | peeringServiceCountries | Nee | Nee |
> | peeringServiceProviders | Nee | Nee |
> | peeringServices | Ja | Ja |

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | verklaringen | Nee | Nee |
> | policyEvents | Nee | Nee |
> | policyMetadata | Nee | Nee |
> | policyStates | Nee | Nee |
> | policyTrackedResources | Nee | Nee |
> | herstel | Nee | Nee |

## <a name="microsoftportal"></a>Micro soft. Portal

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> |  -consoles | Nee | Nee |
> | dashboards | Ja | Ja |
> | userSettings | Nee | Nee |

## <a name="microsoftpowerbi"></a>Micro soft. PowerBI

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | privateLinkServicesForPowerBI | Ja | Ja |
> | tenants | Ja | Ja |
> | tenants/werk ruimten | Nee | Nee |
> | workspaceCollections | Ja | Ja |

## <a name="microsoftpowerbidedicated"></a>Micro soft. PowerBIDedicated

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | beschikt | Ja | Ja |

## <a name="microsoftprojectbabylon"></a>Micro soft. ProjectBabylon

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | accounts | Ja | Ja |
> | deletedAccounts | Nee | Nee |

## <a name="microsoftproviderhub"></a>Micro soft. ProviderHub

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | providerRegistrations | Nee | Nee |
> | providerRegistrations / defaultRollouts | Nee | Nee |
> | providerRegistrations / resourceTypeRegistrations | Nee | Nee |
> | implementaties | Ja | Ja |

## <a name="microsoftquantum"></a>Micro soft. Quantum

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | Workspaces | Ja | Ja |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | backupProtectedItems | Nee | Nee |
> | kluizen | Ja | Ja |

## <a name="microsoftredhatopenshift"></a>Micro soft. RedHatOpenShift

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | OpenShiftClusters | Ja | Ja |

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | naam ruimten | Ja | Ja |
> | naam ruimten/authorizationrules | Nee | Nee |
> | naam ruimten/hybridconnections | Nee | Nee |
> | naam ruimten/hybridconnections/authorizationrules | Nee | Nee |
> | naam ruimten/privateEndpointConnections | Nee | Nee |
> | naam ruimten/wcfrelays | Nee | Nee |
> | naam ruimten/wcfrelays/authorizationrules | Nee | Nee |

## <a name="microsoftresourcegraph"></a>Micro soft. ResourceGraph

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | aanvragen | Ja | Ja |
> | resourceChangeDetails | Nee | Nee |
> | resourceChanges | Nee | Nee |
> | resources | Nee | Nee |
> | resourcesHistory | Nee | Nee |
> | subscriptionsStatus | Nee | Nee |

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | availabilityStatuses | Nee | Nee |
> | childAvailabilityStatuses | Nee | Nee |
> | childResources | Nee | Nee |
> | emergingissues | Nee | Nee |
> | events | Nee | Nee |
> | impactedResources | Nee | Nee |
> | metagegevens | Nee | Nee |
> | meldingen | Nee | Nee |

## <a name="microsoftresources"></a>Microsoft.Resources

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | calculateTemplateHash | Nee | Nee |
> | implementaties | Ja | Nee |
> | implementaties/bewerkingen | Nee | Nee |
> | deploymentScripts | Ja | Ja |
> | deploymentScripts/logboeken | Nee | Nee |
> | koppelen | Nee | Nee |
> | notifyResourceJobs | Nee | Nee |
> | providers | Nee | Nee |
> | resourceGroups | Ja | Nee |
> | geabonneerd | Ja | Nee |
> | templateSpecs | Ja | Ja |
> | templateSpecs/versies | Ja | Ja |
> | tenants | Nee | Nee |

## <a name="microsoftsaas"></a>Micro soft. SaaS

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | toepassingen | Ja | Ja |
> | saasresources | Nee | Nee |

## <a name="microsoftscvmm"></a>Micro soft. ScVmm

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | Clouds | Ja | Ja |
> | VirtualMachines | Ja | Ja |
> | VirtualMachineTemplates | Ja | Ja |
> | VirtualNetworks | Ja | Ja |
> | vmmservers | Ja | Ja |

## <a name="microsoftsearch"></a>Microsoft.Search

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | resourceHealthMetadata | Nee | Nee |
> | searchServices | Ja | Ja |

## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | adaptiveNetworkHardenings | Nee | Nee |
> | advancedThreatProtectionSettings | Nee | Nee |
> | waarschuwingen | Nee | Nee |
> | alertsSuppressionRules | Nee | Nee |
> | allowedConnections | Nee | Nee |
> | applicationWhitelistings | Nee | Nee |
> | assessmentMetadata | Nee | Nee |
> | evaluaties | Nee | Nee |
> | autoDismissAlertsRules | Nee | Nee |
> | automatisering | Ja | Ja |
> | AutoProvisioningSettings | Nee | Nee |
> | Ingebouwde strengste | Nee | Nee |
> | connectoren | Nee | Nee |
> | dataCollectionAgents | Nee | Nee |
> | deviceSecurityGroups | Nee | Nee |
> | discoveredSecuritySolutions | Nee | Nee |
> | externalSecuritySolutions | Nee | Nee |
> | InformationProtectionPolicies | Nee | Nee |
> | iotDefenderSettings | Nee | Nee |
> | iotSecuritySolutions | Ja | Ja |
> | iotSecuritySolutions / analyticsModels | Nee | Nee |
> | iotSecuritySolutions / analyticsModels / aggregatedAlerts | Nee | Nee |
> | iotSecuritySolutions / analyticsModels / aggregatedRecommendations | Nee | Nee |
> | iotSecuritySolutions / iotAlerts | Nee | Nee |
> | iotSecuritySolutions / iotAlertTypes | Nee | Nee |
> | iotSecuritySolutions / iotRecommendations | Nee | Nee |
> | iotSecuritySolutions / iotRecommendationTypes | Nee | Nee |
> | iotSensors | Nee | Nee |
> | jitNetworkAccessPolicies | Nee | Nee |
> | jitPolicies | Nee | Nee |
> | policies | Nee | Nee |
> | prijzen | Nee | Nee |
> | regulatoryComplianceStandards | Nee | Nee |
> | regulatoryComplianceStandards / regulatoryComplianceControls | Nee | Nee |
> | regulatoryComplianceStandards / regulatoryComplianceControls / regulatoryComplianceAssessments | Nee | Nee |
> | secureScoreControlDefinitions | Nee | Nee |
> | secureScoreControls | Nee | Nee |
> | secureScores | Nee | Nee |
> | secureScores / secureScoreControls | Nee | Nee |
> | securityContacts | Nee | Nee |
> | securitySolutions | Nee | Nee |
> | securitySolutionsReferenceData | Nee | Nee |
> | securityStatuses | Nee | Nee |
> | securityStatusesSummaries | Nee | Nee |
> | serverVulnerabilityAssessments | Nee | Nee |
> | instellingen | Nee | Nee |
> | sqlVulnerabilityAssessments | Nee | Nee |
> | subevaluaties | Nee | Nee |
> | taken | Nee | Nee |
> | topologieën | Nee | Nee |
> | workspaceSettings | Nee | Nee |

## <a name="microsoftsecuritygraph"></a>Micro soft. SecurityGraph

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | diagnosticSettings | Nee | Nee |
> | diagnosticSettingsCategories | Nee | Nee |

## <a name="microsoftsecurityinsights"></a>Micro soft. SecurityInsights

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | aggregaties | Nee | Nee |
> | alertRules | Nee | Nee |
> | alertRuleTemplates | Nee | Nee |
> | automationRules | Nee | Nee |
> | bladwijzers | Nee | Nee |
> | meldingen | Nee | Nee |
> | dataConnectors | Nee | Nee |
> | dataConnectorsCheckRequirements | Nee | Nee |
> | entiteiten | Nee | Nee |
> | entityQueries | Nee | Nee |
> | incidenten | Nee | Nee |
> | officeConsents | Nee | Nee |
> | instellingen | Nee | Nee |
> | threatIntelligence | Nee | Nee |
> | watchlists | Nee | Nee |

## <a name="microsoftserialconsole"></a>Micro soft. SerialConsole

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | consoleServices | Nee | Nee |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | naam ruimten | Ja | Ja |
> | naam ruimten/authorizationrules | Nee | Nee |
> | naam ruimten/disasterrecoveryconfigs | Nee | Nee |
> | naam ruimten/eventgridfilters | Nee | Nee |
> | naam ruimten/networkrulesets | Nee | Nee |
> | naam ruimten/privateEndpointConnections | Nee | Nee |
> | naam ruimten/wacht rijen | Nee | Nee |
> | naam ruimten/wacht rijen/authorizationrules | Nee | Nee |
> | naam ruimten/onderwerpen | Nee | Nee |
> | naam ruimten/onderwerpen/authorizationrules | Nee | Nee |
> | naam ruimten/onderwerpen/abonnementen | Nee | Nee |
> | naam ruimten/onderwerpen/abonnementen/regels | Nee | Nee |
> | premiumMessagingRegions | Nee | Nee |

## <a name="microsoftservicefabric"></a>Micro soft. ServiceFabric

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | toepassingen | Ja | Ja |
> | clusters | Ja | Ja |
> | clusters/toepassingen | Nee | Nee |
> | containerGroups | Ja | Ja |
> | containerGroupSets | Ja | Ja |
> | edgeclusters | Ja | Ja |
> | edgeclusters/toepassingen | Nee | Nee |
> | managedclusters | Ja | Ja |
> | managedclusters / nodetypes | Nee | Nee |
> | netwerken | Ja | Ja |
> | secretstores | Ja | Ja |
> | secretstores/certificaten | Nee | Nee |
> | secretstores/geheimen | Nee | Nee |
> | volumes | Ja | Ja |

## <a name="microsoftservicefabricmesh"></a>Micro soft. ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | toepassingen | Ja | Ja |
> | containerGroups | Ja | Ja |
> | gateways | Ja | Ja |
> | netwerken | Ja | Ja |
> | geheimen | Ja | Ja |
> | volumes | Ja | Ja |

## <a name="microsoftservices"></a>Micro soft. Services

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | providerRegistrations | Nee | Nee |
> | providerRegistrations / resourceTypeRegistrations | Nee | Nee |
> | implementaties | Ja | Ja |

## <a name="microsoftsignalrservice"></a>Micro soft. SignalRService

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | SignalR | Ja | Ja |
> | Signaal sterkte/eventGridFilters | Nee | Nee |

## <a name="microsoftsingularity"></a>Micro soft. enkelvoud

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | accounts | Ja | Ja |
> | accounts/accountQuotaPolicies | Nee | Nee |
> | accounts/groupPolicies | Nee | Nee |
> | accounts/taken | Nee | Nee |
> | accounts/storageContainers | Nee | Nee |

## <a name="microsoftsoftwareplan"></a>Micro soft. SoftwarePlan

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | hybridUseBenefits | Nee | Nee |

## <a name="microsoftsolutions"></a>Micro soft. Solutions

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | applicationDefinitions | Ja | Ja |
> | toepassingen | Ja | Ja |
> | jitRequests | Ja | Ja |


## <a name="microsoftsql"></a>Micro soft. SQL

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | managedInstances | Ja | Ja |
> | managedInstances/data bases | Nee | Nee |
> | managedInstances/data bases/backupShortTermRetentionPolicies | Nee | Nee |
> | managedInstances/data bases/schema's/tabellen/kolommen/sensitivityLabels | Nee | Nee |
> | managedInstances/data bases/vulnerabilityAssessments | Nee | Nee |
> | managedInstances/data bases/vulnerabilityAssessments/Rules/basis lijnen | Nee | Nee |
> | managedInstances / encryptionProtector | Nee | Nee |
> | managedInstances/sleutels | Nee | Nee |
> | managedInstances / restorableDroppedDatabases / backupShortTermRetentionPolicies | Nee | Nee |
> | managedInstances / vulnerabilityAssessments | Nee | Nee |
> | Server | Ja | Ja |
> | servers/beheerders | Nee | Nee |
> | servers/communicationLinks | Nee | Nee |
> | servers/data bases | Ja (Zie [Opmerking hieronder](#sqlnote)) | Yes |
> | servers/encryptionProtector | Nee | Nee |
> | servers/firewallRules | Nee | Nee |
> | servers/sleutels | Nee | Nee |
> | servers/restorableDroppedDatabases | Nee | Nee |
> | servers/serviceobjectives | Nee | Nee |
> | servers/tdeCertificates | Nee | Nee |
> | virtualClusters | Nee | Nee |

<a id="sqlnote"></a>

> [!NOTE]
> De hoofd database ondersteunt geen tags, maar andere data bases, inclusief Azure Synapse Analytics-data bases, ondersteunings Tags. Azure Synapse Analytics-data bases moeten de status actief (niet onderbroken) hebben.

## <a name="microsoftsqlvirtualmachine"></a>Micro soft. SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | SqlVirtualMachineGroups | Ja | Ja |
> | SqlVirtualMachineGroups / AvailabilityGroupListeners | Nee | Nee |
> | SqlVirtualMachines | Ja | Ja |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | deletedAccounts | Nee | Nee |
> | Storage accounts | Ja | Ja |
> | Storage accounts/blobServices | Nee | Nee |
> | Storage accounts/fileServices | Nee | Nee |
> | Storage accounts/queueServices | Nee | Nee |
> | Storage accounts/Services | Nee | Nee |
> | Storage accounts/Services/metricDefinitions | Nee | Nee |
> | Storage accounts/tableServices | Nee | Nee |
> | gebruik | Nee | Nee |

## <a name="microsoftstoragecache"></a>Micro soft. StorageCache

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | caches | Ja | Ja |
> | caches/storageTargets | Nee | Nee |
> | usageModels | Nee | Nee |

## <a name="microsoftstoragereplication"></a>Micro soft. StorageReplication

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | replicationGroups | Nee | Nee |

## <a name="microsoftstoragesync"></a>Micro soft. StorageSync

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | storageSyncServices | Ja | Ja |
> | storageSyncServices/registeredServer | Nee | Nee |
> | storageSyncServices / syncGroups | Nee | Nee |
> | storageSyncServices / syncGroups / cloudEndpoints | Nee | Nee |
> | storageSyncServices / syncGroups / serverEndpoints | Nee | Nee |
> | storageSyncServices/werk stromen | Nee | Nee |

## <a name="microsoftstoragesyncdev"></a>Micro soft. StorageSyncDev

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | storageSyncServices | Ja | Ja |
> | storageSyncServices/registeredServer | Nee | Nee |
> | storageSyncServices / syncGroups | Nee | Nee |
> | storageSyncServices / syncGroups / cloudEndpoints | Nee | Nee |
> | storageSyncServices / syncGroups / serverEndpoints | Nee | Nee |
> | storageSyncServices/werk stromen | Nee | Nee |

## <a name="microsoftstoragesyncint"></a>Micro soft. StorageSyncInt

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | storageSyncServices | Ja | Ja |
> | storageSyncServices/registeredServer | Nee | Nee |
> | storageSyncServices / syncGroups | Nee | Nee |
> | storageSyncServices / syncGroups / cloudEndpoints | Nee | Nee |
> | storageSyncServices / syncGroups / serverEndpoints | Nee | Nee |
> | storageSyncServices/werk stromen | Nee | Nee |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | leider | Ja | Ja |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | clusters | Ja | Ja |
> | clusters/privateEndpoints | Nee | Nee |
> | streamingjobs | Ja (zie opmerking hieronder) | Yes |

> [!NOTE]
> U kunt geen tag toevoegen wanneer streamingjobs wordt uitgevoerd. Stop de resource om een tag toe te voegen.

## <a name="microsoftsubscription"></a>Micro soft. Subscription

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | acceptChangeTenant | Nee | Nee |
> | aliassen | Nee | Nee |
> | annuleren | Nee | Nee |
> | changeTenantRequest | Nee | Nee |
> | changeTenantStatus | Nee | Nee |
> | CreateSubscription | Nee | Nee |
> | inschakelen | Nee | Nee |
> | naam wijzigen | Nee | Nee |
> | SubscriptionDefinitions | Nee | Nee |
> | SubscriptionOperations | Nee | Nee |
> | geabonneerd | Nee | Nee |

## <a name="microsoftsynapse"></a>Micro soft. Synapse

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | privateLinkHubs | Ja | Ja |
> | workspaces | Ja | Ja |
> | werk ruimten/bigDataPools | Ja | Ja |
> | werk ruimten/operationStatuses | Nee | Nee |
> | werk ruimten/sqlDatabases | Ja | Ja |
> | werk ruimten/sqlPools | Ja | Ja |

## <a name="microsofttimeseriesinsights"></a>Micro soft. TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | verschillend | Ja | Nee |
> | omgevingen/accessPolicies | Nee | Nee |
> | omgevingen/eventsources | Ja | Nee |
> | omgevingen/referenceDataSets | Ja | Nee |

## <a name="microsofttoken"></a>Micro soft. token

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | opslaat | Ja | Ja |
> | winkels/accessPolicies | Nee | Nee |
> | winkels/Services | Nee | Nee |
> | archieven/Services/tokens | Nee | Nee |

## <a name="microsoftvirtualmachineimages"></a>Microsoft.VirtualMachineImages

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | imageTemplates | Ja | Ja |
> | imageTemplates / runOutputs | Nee | Nee |

## <a name="microsoftvmware"></a>Micro soft. VMware

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | ArcZones | Ja | Ja |
> | ResourcePools | Ja | Ja |
> | VCenter | Ja | Ja |
> | VirtualMachines | Ja | Ja |
> | VirtualMachineTemplates | Ja | Ja |
> | VirtualNetworks | Ja | Ja |

## <a name="microsoftvmwarecloudsimple"></a>Micro soft. VMwareCloudSimple

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | dedicatedCloudNodes | Ja | Ja |
> | dedicatedCloudServices | Ja | Ja |
> | Informatie | Ja | Ja |

## <a name="microsoftvnfmanager"></a>Micro soft. VnfManager

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | devices | Ja | Ja |
> | registeredSubscriptions | Nee | Nee |
> | crediteur | Nee | Nee |
> | leveranciers/sku's | Nee | Nee |
> | leveranciers/vnfs | Nee | Nee |
> | virtualNetworkFunctionSkus | Nee | Nee |
> | vnfs | Ja | Ja |

## <a name="microsoftvsonline"></a>Micro soft. VSOnline

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | accounts | Ja | Nee |
> | plant | Ja | Nee |
> | registeredSubscriptions | Nee | Nee |

## <a name="microsoftweb"></a>Microsoft.Web

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | apiManagementAccounts | Nee | Nee |
> | apiManagementAccounts / apiAcls | Nee | Nee |
> | apiManagementAccounts/api's | Nee | Nee |
> | apiManagementAccounts/api's/apiAcls | Nee | Nee |
> | apiManagementAccounts/api's/connectionAcls | Nee | Nee |
> | apiManagementAccounts/api's/Connections | Nee | Nee |
> | apiManagementAccounts/api's/Connections/connectionAcls | Nee | Nee |
> | apiManagementAccounts/api's/localizedDefinitions | Nee | Nee |
> | apiManagementAccounts / connectionAcls | Nee | Nee |
> | apiManagementAccounts/verbindingen | Nee | Nee |
> | billingMeters | Nee | Nee |
> | certificaten | Ja | Ja |
> | connectionGateways | Ja | Ja |
> | inbel | Ja | Ja |
> | customApis | Ja | Ja |
> | deletedSites | Nee | Nee |
> | hostingEnvironments | Ja | Ja |
> | hostingEnvironments / eventGridFilters | Nee | Nee |
> | hostingEnvironments / multiRolePools | Nee | Nee |
> | hostingEnvironments / workerPools | Nee | Nee |
> | kubeEnvironments | Ja | Ja |
> | publishingUsers | Nee | Nee |
> | aanbevelingen | Nee | Nee |
> | resourceHealthMetadata | Nee | Nee |
> | Runtimes | Nee | Nee |
> | Server farms | Ja | Ja |
> | Server farms/eventGridFilters | Nee | Nee |
> | Server farms/firstPartyApps | Nee | Nee |
> | Server farms/firstPartyApps/keyVaultSettings | Nee | Nee |
> | sites | Ja | Ja |
> | sites/configuratie  | Nee | Nee |
> | sites/eventGridFilters | Nee | Nee |
> | sites/hostNameBindings | Nee | Nee |
> | sites/networkConfig | Nee | Nee |
> | sites/premieraddons | Ja | Ja |
> | sites/sleuven | Ja | Ja |
> | sites/sleuven/eventGridFilters | Nee | Nee |
> | sites/sleuven/hostNameBindings | Nee | Nee |
> | sites/sleuven/networkConfig | Nee | Nee |
> | sourceControls | Nee | Nee |
> | staticSites | Ja | Ja |
> | subelementid | Nee | Nee |
> | verifyHostingEnvironmentVnet | Nee | Nee |

## <a name="microsoftwindowsdefenderatp"></a>Micro soft. WindowsDefenderATP

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | diagnosticSettings | Nee | Nee |
> | diagnosticSettingsCategories | Nee | Nee |

## <a name="microsoftwindowsesu"></a>Micro soft. WindowsESU

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | multipleActivationKeys | Ja | Ja |

## <a name="microsoftwindowsiot"></a>Micro soft. WindowsIoT

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | DeviceServices | Ja | Ja |

## <a name="microsoftworkloadbuilder"></a>Micro soft. WorkloadBuilder

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | workloads | Ja | Ja |
> | workloads/instanties | Nee | Nee |
> | workloads/versies | Nee | Nee |
> | workloads/versies/artefacten | Nee | Nee |

## <a name="microsoftworkloadmonitor"></a>Micro soft. WorkloadMonitor

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | materialen | Nee | Nee |
> | componentsSummary | Nee | Nee |
> | monitorInstances | Nee | Nee |
> | monitorInstancesSummary | Nee | Nee |
> | monitors | Nee | Nee |
> | notificationSettings | Nee | Nee |

## <a name="next-steps"></a>Volgende stappen

Zie [Tags gebruiken om uw Azure-resources te organiseren](tag-resources.md)voor meer informatie over het Toep assen van tags op resources.
