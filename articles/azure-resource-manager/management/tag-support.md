---
title: Tagondersteuning voor resources
description: Toont welke Azure-resourcetypen tags ondersteunen. Biedt details voor alle Azure-services.
ms.topic: conceptual
ms.date: 04/20/2021
ms.openlocfilehash: b196cae267a8d7dc878f055f6b2d70a3ff6f9313
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107773959"
---
# <a name="tag-support-for-azure-resources"></a>Tagondersteuning voor Azure-resources
In dit artikel wordt beschreven of een resourcetype [tags ondersteunt.](tag-resources.md) De kolom met het label **Ondersteunt tags** geeft aan of het resourcetype een eigenschap voor de tag heeft. In de kolom Tag **in het kostenrapport wordt** aangegeven of dat resourcetype de tag door geeft aan het kostenrapport. U kunt kosten per tag weergeven in de Cost Management [kostenanalyse,](../../cost-management-billing/costs/group-filter.md) de [Azure-factuur en de dagelijkse gebruiksgegevens.](../../cost-management-billing/manage/download-azure-invoice-daily-usage-date.md)

Als u dezelfde gegevens wilt op halen als een bestand met door komma's gescheiden waarden, downloadt [ utag-support.csv](https://github.com/tfitzmac/resource-capabilities/blob/master/tag-support.csv).

Ga naar de naamruimte van een resourceprovider:
> [!div class="op_single_selector"]
> - [Microsoft.AAD](#microsoftaad)
> - [Microsoft.Addons](#microsoftaddons)
> - [Microsoft.ADHybridHealthService](#microsoftadhybridhealthservice)
> - [Microsoft.Advisor](#microsoftadvisor)
> - [Microsoft.AgPlatform](#microsoftagfoodplatform)
> - [Microsoft.AlertsManagement](#microsoftalertsmanagement)
> - [Microsoft.AnalysisServices](#microsoftanalysisservices)
> - [Microsoft.AnyBuild](#microsoftanybuild)
> - [Microsoft.ApiManagement](#microsoftapimanagement)
> - [Microsoft.AppAssessment](#microsoftappassessment)
> - [Microsoft.AppConfiguration](#microsoftappconfiguration)
> - [Microsoft.AppPlatform](#microsoftappplatform)
> - [Microsoft.Attestation](#microsoftattestation)
> - [Microsoft.Authorization](#microsoftauthorization)
> - [Microsoft.Automanage](#microsoftautomanage)
> - [Microsoft.Automation](#microsoftautomation)
> - [Microsoft.AVS](#microsoftavs)
> - [Microsoft.Azure.Genève](#microsoftazuregeneva)
> - [Microsoft.AzureActiveDirectory](#microsoftazureactivedirectory)
> - [Microsoft.AzureArcData](#microsoftazurearcdata)
> - [Microsoft.AzureCIS](#microsoftazurecis)
> - [Microsoft.AzureData](#microsoftazuredata)
> - [Microsoft.AzureSphere](#microsoftazuresphere)
> - [Microsoft.AzureStack](#microsoftazurestack)
> - [Microsoft.AzureStackHCI](#microsoftazurestackhci)
> - [Microsoft.BareMetalInfrastructure](#microsoftbaremetalinfrastructure)
> - [Microsoft.Batch](#microsoftbatch)
> - [Microsoft.Billing](#microsoftbilling)
> - [Microsoft.BingMaps](#microsoftbingmaps)
> - [Microsoft.Blockchain](#microsoftblockchain)
> - [Microsoft.BlockchainTokens](#microsoftblockchaintokens)
> - [Microsoft.Blueprint](#microsoftblueprint)
> - [Microsoft.BotService](#microsoftbotservice)
> - [Microsoft.Cache](#microsoftcache)
> - [Microsoft.Capacity](#microsoftcapacity)
> - [Microsoft.Cascade](#microsoftcascade)
> - [Microsoft.Cdn](#microsoftcdn)
> - [Microsoft.CertificateRegistration](#microsoftcertificateregistration)
> - [Microsoft.ChangeAnalysis](#microsoftchangeanalysis)
> - [Microsoft.ClassicCompute](#microsoftclassiccompute)
> - [Microsoft.ClassicInfrastructureMigrate](#microsoftclassicinfrastructuremigrate)
> - [Microsoft.ClassicNetwork](#microsoftclassicnetwork)
> - [Microsoft.ClassicStorage](#microsoftclassicstorage)
> - [Microsoft.ClusterStor](#microsoftclusterstor)
> - [Microsoft.Codespaces](#microsoftcodespaces)
> - [Microsoft.CognitiveServices](#microsoftcognitiveservices)
> - [Microsoft.Commerce](#microsoftcommerce)
> - [Microsoft.Compute](#microsoftcompute)
> - [Microsoft.ConnectedCache](#microsoftconnectedcache)
> - [Microsoft.ConnectedVehicle](#microsoftconnectedvehicle)
> - [Microsoft.ConnectedVMwarevSphere](#microsoftconnectedvmwarevsphere)
> - [Microsoft.Consumption](#microsoftconsumption)
> - [Microsoft.ContainerInstance](#microsoftcontainerinstance)
> - [Microsoft.ContainerRegistry](#microsoftcontainerregistry)
> - [Microsoft.ContainerService](#microsoftcontainerservice)
> - [Microsoft.CostManagement](#microsoftcostmanagement)
> - [Microsoft.CustomerLockbox](#microsoftcustomerlockbox)
> - [Microsoft.CustomProviders](#microsoftcustomproviders)
> - [Microsoft.D365CustomerInsights](#microsoftd365customerinsights)
> - [Microsoft.DataBox](#microsoftdatabox)
> - [Microsoft.DataBoxEdge](#microsoftdataboxedge)
> - [Microsoft.Databricks](#microsoftdatabricks)
> - [Microsoft.DataCatalog](#microsoftdatacatalog)
> - [Microsoft.DataFactory](#microsoftdatafactory)
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
> - [Microsoft.DeviceUpdate](#microsoftdeviceupdate)
> - [Microsoft.DevOps](#microsoftdevops)
> - [Microsoft.DevSpaces](#microsoftdevspaces)
> - [Microsoft.DevTestLab](#microsoftdevtestlab)
> - [Microsoft.DigitalTwins](#microsoftdigitaltwins)
> - [Microsoft.DocumentDB](#microsoftdocumentdb)
> - [Microsoft.DomainRegistration](#microsoftdomainregistration)
> - [Microsoft.DynamicsLcs](#microsoftdynamicslcs)
> - [Microsoft.EdgeOrder](#microsoftedgeorder)
> - [Microsoft.EnterpriseKnowledgeGraph](#microsoftenterpriseknowledgegraph)
> - [Microsoft.EventGrid](#microsofteventgrid)
> - [Microsoft.EventHub](#microsofteventhub)
> - [Microsoft.Experimentation](#microsoftexperimentation)
> - [Microsoft.Hebto](#microsoftfalcon)
> - [Microsoft.Features](#microsoftfeatures)
> - [Microsoft.Gallery](#microsoftgallery)
> - [Microsoft.Genomics](#microsoftgenomics)
> - [Microsoft.GuestConfiguration](#microsoftguestconfiguration)
> - [Microsoft.HanaOnAzure](#microsofthanaonazure)
> - [Microsoft.HardwareSecurityModules](#microsofthardwaresecuritymodules)
> - [Microsoft.HDInsight](#microsofthdinsight)
> - [Microsoft.HealthBot](#microsofthealthbot)
> - [Microsoft.HealthcareApis](#microsofthealthcareapis)
> - [Microsoft.HybridCompute](#microsofthybridcompute)
> - [Microsoft.HybridData](#microsofthybriddata)
> - [Microsoft.HybridNetwork](#microsofthybridnetwork)
> - [Microsoft.Keer](#microsofthydra)
> - [Microsoft.ImportExport](#microsoftimportexport)
> - [Microsoft.Insights](#microsoftinsights)
> - [Microsoft.Intune](#microsoftintune)
> - [Microsoft.IoTCentral](#microsoftiotcentral)
> - [Microsoft.IoTSecurity](#microsoftiotsecurity)
> - [Microsoft.IoTSpaces](#microsoftiotspaces)
> - [Microsoft.KeyVault](#microsoftkeyvault)
> - [Microsoft.Kubernetes](#microsoftkubernetes)
> - [Microsoft.KubernetesConfiguration](#microsoftkubernetesconfiguration)
> - [Microsoft.Kusto](#microsoftkusto)
> - [Microsoft.LabServices](#microsoftlabservices)
> - [Microsoft.Logic](#microsoftlogic)
> - [Microsoft.MachineLearning](#microsoftmachinelearning)
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
> - [Microsoft.MobileNetwork](#microsoftmobilenetwork)
> - [Microsoft.NetApp](#microsoftnetapp)
> - [Microsoft.Network](#microsoftnetwork)
> - [Microsoft.Notebooks](#microsoftnotebooks)
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
> - [Microsoft.PowerPlatform](#microsoftpowerplatform)
> - [Microsoft.ProjectMasylon](#microsoftprojectbabylon)
> - [Microsoft.ProviderHub](#microsoftproviderhub)
> - [Microsoft.Purview](#microsoftpurview)
> - [Microsoft.Quantum](#microsoftquantum)
> - [Microsoft.RecoveryServices](#microsoftrecoveryservices)
> - [Microsoft.RedHatOpenShift](#microsoftredhatopenshift)
> - [Microsoft.Relay](#microsoftrelay)
> - [Microsoft.ResourceConnector](#microsoftresourceconnector)
> - [Microsoft.ResourceGraph](#microsoftresourcegraph)
> - [Microsoft.ResourceHealth](#microsoftresourcehealth)
> - [Microsoft.Resources](#microsoftresources)
> - [Microsoft.SaaS](#microsoftsaas)
> - [Microsoft.ScVmm](#microsoftscvmm)
> - [Microsoft.Search](#microsoftsearch)
> - [Microsoft.Security](#microsoftsecurity)
> - [Microsoft.SecurityGraph](#microsoftsecuritygraph)
> - [Microsoft.SecurityInsights](#microsoftsecurityinsights)
> - [Microsoft.SerialConsole](#microsoftserialconsole)
> - [Microsoft.ServiceBus](#microsoftservicebus)
> - [Microsoft.ServiceFabric](#microsoftservicefabric)
> - [Microsoft.ServiceFabricMesh](#microsoftservicefabricmesh)
> - [Microsoft.ServiceLinker](#microsoftservicelinker)
> - [Microsoft.Services](#microsoftservices)
> - [Microsoft.SignalRService](#microsoftsignalrservice)
> - [Microsoft.Singularity](#microsoftsingularity)
> - [Microsoft.SoftwarePlan](#microsoftsoftwareplan)
> - [Microsoft.Solutions](#microsoftsolutions)
> - [Microsoft.SQL](#microsoftsql)
> - [Microsoft.SqlVirtualMachine](#microsoftsqlvirtualmachine)
> - [Microsoft.Storage](#microsoftstorage)
> - [Microsoft.StorageCache](#microsoftstoragecache)
> - [Microsoft.StorageReplication](#microsoftstoragereplication)
> - [Microsoft.StorageSync](#microsoftstoragesync)
> - [Microsoft.StorageSyncDev](#microsoftstoragesyncdev)
> - [Microsoft.StorageSyncInt](#microsoftstoragesyncint)
> - [Microsoft.StorSimple](#microsoftstorsimple)
> - [Microsoft.StreamAnalytics](#microsoftstreamanalytics)
> - [Microsoft.Subscription](#microsoftsubscription)
> - [Microsoft.Synapse](#microsoftsynapse)
> - [Microsoft.TimeSeriesInsights](#microsofttimeseriesinsights)
> - [Microsoft.Token](#microsofttoken)
> - [Microsoft.VirtualMachineImages](#microsoftvirtualmachineimages)
> - [Microsoft.VMware](#microsoftvmware)
> - [Microsoft.VMwareCloudSimple](#microsoftvmwarecloudsimple)
> - [Microsoft.VnfManager](#microsoftvnfmanager)
> - [Microsoft.VSOnline](#microsoftvsonline)
> - [Microsoft.Web](#microsoftweb)
> - [Microsoft.WindowsDefenderATP](#microsoftwindowsdefenderatp)
> - [Microsoft.WindowsESU](#microsoftwindowsesu)
> - [Microsoft.WindowsIoT](#microsoftwindowsiot)
> - [Microsoft.WorkloadBuilder](#microsoftworkloadbuilder)
> - [Microsoft.WorkloadMonitor](#microsoftworkloadmonitor)

## <a name="microsoftaad"></a>Microsoft.AAD

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | DomainServices | Ja | Ja |
> | DomainServices /oucontainer | Nee | Nee |

## <a name="microsoftaddons"></a>Microsoft.Addons

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | supportProviders | Nee | Nee |

## <a name="microsoftadhybridhealthservice"></a>Microsoft.ADHybridHealthService

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | aadsupportcases | Nee | Nee |
> | addsservices | Nee | Nee |
> | Agenten | Nee | Nee |
> | anonymousapiusers | Nee | Nee |
> | configuratie | Nee | Nee |
> | logboeken | Nee | Nee |
> | rapporten | Nee | Nee |
> | servicehealthmetrics | Nee | Nee |
> | services | Nee | Nee |

## <a name="microsoftadvisor"></a>Microsoft.Advisor

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | advisorScore | Nee | Nee |
> | Configuraties | Nee | Nee |
> | generateRecommendations | Nee | Nee |
> | metagegevens | Nee | Nee |
> | aanbevelingen | Nee | Nee |
> | onderdrukkingen | Nee | Nee |

## <a name="microsoftagfoodplatform"></a>Microsoft.AgPlatform

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | farmBeats | Ja | Ja |
> | farmBeats /eventGridFilters | Nee | Nee |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | actionRules | Ja | Ja |
> | waarschuwingen | Nee | Nee |
> | alertsList | Nee | Nee |
> | alertsMetaData | Nee | Nee |
> | alertsSummary | Nee | Nee |
> | alertsSummaryList | Nee | Nee |
> | migrateFromSmartDetection | Nee | Nee |
> | resourceHealthAlertRules | Ja | Ja |
> | smartDetectorAlertRules | Ja | Ja |
> | smartGroups | Nee | Nee |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | Servers | Ja | Ja |

## <a name="microsoftanybuild"></a>Microsoft.AnyBuild

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | Clusters | Ja | Ja |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | deletedServices | Nee | Nee |
> | getDomainOwnershipIdentifier | Nee | Nee |
> | reportFeedback | Nee | Nee |
> | service | Ja | Ja |
> | validateServiceName | Nee | Nee |

> [!NOTE]
> Azure API Management alleen ondersteuning voor het maken van maximaal 15 tagnaam/-waardeparen voor elke service.

## <a name="microsoftappassessment"></a>Microsoft.AppAssessment

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | migrateProjects | Ja | Ja |
> | migrateProjects/assessments | Nee | Nee |
> | migrateProjects/assessments/assessedApplications | Nee | Nee |
> | migrateProjects/assessments/assessedApplications /machines | Nee | Nee |
> | migrateProjects/assessments/assessedMachines | Nee | Nee |
> | migrateProjects/assessments /assessedMachines/applications | Nee | Nee |
> | migrateProjects/assessments /machinesToAssess | Nee | Nee |
> | migrateProjects/sites | Nee | Nee |
> | migrateProjects/sites/applianceConfigurations | Nee | Nee |
> | migrateProjects /sites/machines | Nee | Nee |
> | osVersions | Nee | Nee |

## <a name="microsoftappconfiguration"></a>Microsoft.AppConfiguration

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | configurationStores | Ja | Nee |
> | configurationStores/eventGridFilters | Nee | Nee |
> | configurationStores/keyValues | Nee | Nee |

## <a name="microsoftappplatform"></a>Microsoft.AppPlatform

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | Spring | Ja | Ja |
> | Spring/apps | Nee | Nee |
> | Spring/apps/implementaties | Nee | Nee |

## <a name="microsoftattestation"></a>Microsoft.Attestation

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | attestationProviders | Ja | Ja |
> | defaultProviders | Nee | Nee |

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | accessReviewScheduleDefinitions | Nee | Nee |
> | accessReviewScheduleSettings | Nee | Nee |
> | classicAdministrators | Nee | Nee |
> | dataAliases | Nee | Nee |
> | dataPolicyManifests | Nee | Nee |
> | denyAssignments | Nee | Nee |
> | elevateAccess | Nee | Nee |
> | findOrphanRoleAssignments | Nee | Nee |
> | Sloten | Nee | Nee |
> | permissions | Nee | Nee |
> | policyAssignments | Nee | Nee |
> | policyDefinitions | Nee | Nee |
> | policyExemptions | Nee | Nee |
> | policySetDefinitions | Nee | Nee |
> | privateLinkAssociations | Nee | Nee |
> | providerOperations | Nee | Nee |
> | resourceManagementPrivateLinks | Ja | Ja |
> | roleAssignmentApprovals | Nee | Nee |
> | roleAssignments | Nee | Nee |
> | roleAssignmentScheduleInstances | Nee | Nee |
> | roleAssignmentScheduleRequests | Nee | Nee |
> | roleAssignmentSchedules | Nee | Nee |
> | roleAssignmentsUsageMetrics | Nee | Nee |
> | roleDefinitions | Nee | Nee |
> | roleEligibilityScheduleInstances | Nee | Nee |
> | roleEligibilityScheduleRequests | Nee | Nee |
> | roleEligibilitySchedules | Nee | Nee |
> | roleManagementPolicies | Nee | Nee |
> | roleManagementPolicyAssignments | Nee | Nee |

## <a name="microsoftautomanage"></a>Microsoft.Automanage

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | accounts | Ja | Ja |
> | configurationProfileAssignments | Nee | Nee |
> | configurationProfilePreferences | Ja | Ja |

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | automationAccounts | Ja | Ja |
> | automationAccounts/configuraties | Ja | Ja |
> | automationAccounts/jobs | Nee | Nee |
> | automationAccounts/privateEndpointConnectionProxies | Nee | Nee |
> | automationAccounts/privateEndpointConnections | Nee | Nee |
> | automationAccounts/privateLinkResources | Nee | Nee |
> | automationAccounts/runbooks | Ja | Ja |
> | automationAccounts/softwareUpdateConfigurations | Nee | Nee |
> | automationAccounts/webhooks | Nee | Nee |

> [!NOTE]
> Azure Automation biedt alleen ondersteuning voor het maken van maximaal 15 tagnaam/-waardeparen voor elke Automation-resource.

## <a name="microsoftavs"></a>Microsoft.AVS

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | privateClouds | Ja | Ja |
> | privateClouds/addons | Nee | Nee |
> | privateClouds/autorisaties | Nee | Nee |
> | privateClouds/cloudLinks | Nee | Nee |
> | privateClouds/clusters | Nee | Nee |
> | privateClouds/clusters/gegevensstores | Nee | Nee |
> | privateClouds/globalReachConnections | Nee | Nee |
> | privateClouds /hcxEnterpriseSites | Nee | Nee |
> | privateClouds/scriptExecutions | Nee | Nee |
> | privateClouds/scriptPackages | Nee | Nee |
> | privateClouds/scriptPackages/scriptCmdlets | Nee | Nee |
> | privateClouds/workloadNetworks | Nee | Nee |
> | privateClouds/workloadNetworks/dhcpConfigurations | Nee | Nee |
> | privateClouds/workloadNetworks/dnsServices | Nee | Nee |
> | privateClouds/workloadNetworks/dnsZones | Nee | Nee |
> | privateClouds/workloadNetworks/gateways | Nee | Nee |
> | privateClouds/workloadNetworks/portMirroringProfiles | Nee | Nee |
> | privateClouds/workloadNetworks/segments | Nee | Nee |
> | privateClouds/workloadNetworks/virtualMachines | Nee | Nee |
> | privateClouds/workloadNetworks/vmGroups | Nee | Nee |

## <a name="microsoftazuregeneva"></a>Microsoft.Azure.Genève

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | Omgevingen | Nee | Nee |
> | omgevingen/accounts | Nee | Nee |
> | omgevingen / accounts / naamruimten | Nee | Nee |
> | omgevingen / accounts / naamruimten / configuraties | Nee | Nee |

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | b2cDirectories | Ja | Nee |
> | b2ctenants | Nee | Nee |
> | guestUsages | Ja | Ja |

## <a name="microsoftazurearcdata"></a>Microsoft.AzureArcData

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | dataControllers | Ja | Ja |
> | dataWarehouseInstances | Ja | Ja |
> | postgresInstances | Ja | Ja |
> | sqlManagedInstances | Ja | Ja |
> | sqlServerInstances | Ja | Ja |

## <a name="microsoftazurecis"></a>Microsoft.AzureCIS

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | autopilotEnvironments | Ja | Ja |

## <a name="microsoftazuredata"></a>Microsoft.AzureData

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | sqlServerRegistrations | Ja | Ja |
> | sqlServerRegistrations / sqlServers | Nee | Nee |

## <a name="microsoftazuresphere"></a>Microsoft.AzureSphere

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | Catalogi | Ja | Ja |
> | catalogi/producten | Ja | Ja |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | cloudManifestFiles | Nee | Nee |
> | edgeSubscriptions | Ja | Ja |
> | linkedSubscriptions | Ja | Ja |
> | Registraties | Ja | Ja |
> | registraties /customerSubscriptions | Nee | Nee |
> | registraties/producten | Nee | Nee |

## <a name="microsoftazurestackhci"></a>Microsoft.AzureStackHCI

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | Clusters | Ja | Ja |
> | galleryImages | Ja | Ja |
> | networkInterfaces | Ja | Ja |
> | virtualHardDisks | Ja | Ja |
> | virtualMachines | Ja | Ja |
> | virtualNetworks | Ja | Ja |

## <a name="microsoftbaremetalinfrastructure"></a>Microsoft.BareMetalInfrastructure

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | bareMetalInstances | Ja | Ja |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | batchAccounts | Ja | Ja |
> | batchAccounts/certificaten | Nee | Nee |
> | batchAccounts/pools | Nee | Nee |

## <a name="microsoftbilling"></a>Microsoft.Billing

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | billingAccounts | Nee | Nee |
> | billingAccounts/overeenkomsten | Nee | Nee |
> | billingAccounts/billingPermissions | Nee | Nee |
> | billingAccounts/billingProfiles | Nee | Nee |
> | billingAccounts/billingProfiles/billingPermissions | Nee | Nee |
> | billingAccounts/billingProfiles/billingRoleAssignments | Nee | Nee |
> | billingAccounts/billingProfiles/billingRoleDefinitions | Nee | Nee |
> | billingAccounts/billingProfiles/billingSubscriptions | Nee | Nee |
> | billingAccounts / billingProfiles / createBillingRoleAssignment | Nee | Nee |
> | billingAccounts/billingProfiles /customers | Nee | Nee |
> | billingAccounts/billingProfiles/instructies | Nee | Nee |
> | billingAccounts/billingProfiles/invoices | Nee | Nee |
> | billingAccounts / billingProfiles / facturen / prijzenoverzicht | Nee | Nee |
> | billingAccounts/billingProfiles/facturen/transacties | Nee | Nee |
> | billingAccounts/billingProfiles/invoiceSections | Nee | Nee |
> | billingAccounts / billingProfiles / invoiceSections / billingPermissions | Nee | Nee |
> | billingAccounts / billingProfiles / invoiceSections / billingRoleAssignments | Nee | Nee |
> | billingAccounts / billingProfiles / invoiceSections / billingRoleDefinitions | Nee | Nee |
> | billingAccounts / billingProfiles / invoiceSections / billingSubscriptions | Nee | Nee |
> | billingAccounts / billingProfiles / invoiceSections / createBillingRoleAssignment | Nee | Nee |
> | billingAccounts / billingProfiles / invoiceSections / initiateTransfer | Nee | Nee |
> | billingAccounts / billingProfiles / invoiceSections / products | Nee | Nee |
> | billingAccounts / billingProfiles / invoiceSections / products / transfer | Nee | Nee |
> | billingAccounts / billingProfiles / invoiceSections / products / updateAutoRenew | Nee | Nee |
> | billingAccounts/ billingProfiles / invoiceSections / transactions | Nee | Nee |
> | billingAccounts / billingProfiles / invoiceSections / transfers | Nee | Nee |
> | billingAccounts / billingProfiles / invoiceSections / validateDeleteInvoiceSectionEligibility | Nee | Nee |
> | billingAccounts/BillingProfiles/patchOperations | Nee | Nee |
> | billingAccounts/billingProfiles/paymentMethods | Nee | Nee |
> | billingAccounts/billingProfiles/policies | Nee | Nee |
> | billingAccounts/billingProfiles/pricesheet | Nee | Nee |
> | billingAccounts / billingProfiles / pricesheetDownloadOperations | Nee | Nee |
> | billingAccounts/billingProfiles/products | Nee | Nee |
> | billingAccounts/billingProfiles/reservations | Nee | Nee |
> | billingAccounts/billingProfiles/transactions | Nee | Nee |
> | billingAccounts/billingProfiles/validateDeleteBillingProfileEligibility | Nee | Nee |
> | billingAccounts/billingProfiles/validateDetachPaymentMethodEligibility | Nee | Nee |
> | billingAccounts/billingRoleAssignments | Nee | Nee |
> | billingAccounts/billingRoleDefinitions | Nee | Nee |
> | billingAccounts/billingSubscriptions | Nee | Nee |
> | billingAccounts/billingSubscriptions /elevateRole | Nee | Nee |
> | billingAccounts/billingAbonnementen/facturen | Nee | Nee |
> | billingAccounts /createBillingRoleAssignment | Nee | Nee |
> | billingAccounts/createInvoiceSectionOperations | Nee | Nee |
> | billingAccounts/klanten | Nee | Nee |
> | billingAccounts /customers/billingPermissions | Nee | Nee |
> | billingAccounts /customers/billingSubscriptions | Nee | Nee |
> | billingAccounts /customers /initiateTransfer | Nee | Nee |
> | billingAccounts / klanten / beleid | Nee | Nee |
> | billingAccounts/klanten/producten | Nee | Nee |
> | billingAccounts / klanten / transacties | Nee | Nee |
> | billingAccounts / klanten / overdrachten | Nee | Nee |
> | billingAccounts/afdelingen | Nee | Nee |
> | billingAccounts/afdelingen/billingPermissions | Nee | Nee |
> | billingAccounts/afdelingen/billingRoleAssignments | Nee | Nee |
> | billingAccounts/afdelingen/billingRoleDefinitions | Nee | Nee |
> | billingAccounts/afdelingen/factureringAbonnementen | Nee | Nee |
> | billingAccounts/enrollmentAccounts | Nee | Nee |
> | billingAccounts/enrollmentAccounts/billingPermissions | Nee | Nee |
> | billingAccounts/enrollmentAccounts/billingRoleAssignments | Nee | Nee |
> | billingAccounts/enrollmentAccounts/billingRoleDefinitions | Nee | Nee |
> | billingAccounts/enrollmentAccounts/billingSubscriptions | Nee | Nee |
> | billingAccounts/facturen | Nee | Nee |
> | billingAccounts/facturen/transacties | Nee | Nee |
> | billingAccounts/facturen/transactionSummary | Nee | Nee |
> | billingAccounts/invoiceSections | Nee | Nee |
> | billingAccounts/invoiceSections / billingSubscriptionMoveOperations | Nee | Nee |
> | billingAccounts/invoiceSections/billingSubscriptions | Nee | Nee |
> | billingAccounts/ invoiceSections / billingSubscriptions / transfer | Nee | Nee |
> | billingAccounts/invoiceSections/elevate | Nee | Nee |
> | billingAccounts/invoiceSections / initiateTransfer | Nee | Nee |
> | billingAccounts/invoiceSections/patchOperations | Nee | Nee |
> | billingAccounts/invoiceSections / productMoveOperations | Nee | Nee |
> | billingAccounts/invoiceSections/products | Nee | Nee |
> | billingAccounts/ invoiceSections / products / transfer | Nee | Nee |
> | billingAccounts/invoiceSections / products / updateAutoRenew | Nee | Nee |
> | billingAccounts/invoiceSections/transactions | Nee | Nee |
> | billingAccounts/invoiceSections/transfers | Nee | Nee |
> | billingAccounts/lineOfCredit | Nee | Nee |
> | billingAccounts/patchOperations | Nee | Nee |
> | billingAccounts/crediteurenOverage | Nee | Nee |
> | billingAccounts/paymentMethods | Nee | Nee |
> | billingAccounts/payNow | Nee | Nee |
> | billingAccounts/producten | Nee | Nee |
> | billingAccounts/reserveringen | Nee | Nee |
> | billingAccounts/transacties | Nee | Nee |
> | billingPeriods | Nee | Nee |
> | billingPermissions | Nee | Nee |
> | billingProperty | Nee | Nee |
> | billingRoleAssignments | Nee | Nee |
> | billingRoleDefinitions | Nee | Nee |
> | createBillingRoleAssignment | Nee | Nee |
> | Afdelingen | Nee | Nee |
> | enrollmentAccounts | Nee | Nee |
> | Facturen | Nee | Nee |
> | Promoties | Nee | Nee |
> | Transfers | Nee | Nee |
> | transfers /acceptTransfer | Nee | Nee |
> | transfers/declineTransfer | Nee | Nee |
> | transfers /operationStatus | Nee | Nee |
> | transfers/validateTransfer | Nee | Nee |
> | validateAddress | Nee | Nee |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | mapApis | Ja | Ja |
> | updateCommunicationPreference | Nee | Nee |

## <a name="microsoftblockchain"></a>Microsoft.Blockchain

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | blockchainMembers | Ja | Ja |
> | cordaMembers | Ja | Ja |
> | Watchers | Ja | Ja |

## <a name="microsoftblockchaintokens"></a>Microsoft.BlockchainTokens

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | TokenServices | Ja | Ja |
> | TokenServices /BlockchainNetworks | Nee | Nee |
> | TokenServices/groepen | Nee | Nee |
> | TokenServices/groepen/accounts | Nee | Nee |
> | TokenServices/TokenTemplates | Nee | Nee |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | blueprintAssignments | Nee | Nee |
> | blueprintAssignments /assignmentOperations | Nee | Nee |
> | blueprintAssignments /operations | Nee | Nee |
> | Blauwdrukken | Nee | Nee |
> | blauwdrukken/artefacten | Nee | Nee |
> | blauwdrukken/versies | Nee | Nee |
> | blauwdrukken/versies/artefacten | Nee | Nee |

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | botServices | Ja | Ja |
> | botServices/kanalen | Nee | Nee |
> | botServices/verbindingen | Nee | Nee |
> | hostSettings | Nee | Nee |
> | talen | Nee | Nee |
> | sjablonen | Nee | Nee |

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | Redis | Ja | Ja |
> | Redis/EventGridFilters | Nee | Nee |
> | Redis/privateEndpointConnectionProxies | Nee | Nee |
> | Redis / privateEndpointConnectionProxies / valideren | Nee | Nee |
> | Redis/privateEndpointConnections | Nee | Nee |
> | Redis/privateLinkResources | Nee | Nee |
> | redisEnterprise | Ja | Ja |
> | redisEnterprise /databases | Nee | Nee |
> | RedisEnterprise / privateEndpointConnectionProxies | Nee | Nee |
> | RedisEnterprise / privateEndpointConnectionProxies / valideren | Nee | Nee |
> | RedisEnterprise /privateEndpointConnections | Nee | Nee |
> | RedisEnterprise/privateLinkResources | Nee | Nee |

## <a name="microsoftcapacity"></a>Microsoft.Capacity

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | appliedReservations | Nee | Nee |
> | autoQuotaIncrease | Nee | Nee |
> | calculateExchange | Nee | Nee |
> | calculatePrice | Nee | Nee |
> | calculatePurchasePrice | Nee | Nee |
> | Catalogi | Nee | Nee |
> | commercialReservationOrders | Nee | Nee |
> | Exchange | Nee | Nee |
> | ownReservations | Nee | Nee |
> | placePurchaseOrder | Nee | Nee |
> | reservationOrders | Nee | Nee |
> | reservationOrders/calculateRefund | Nee | Nee |
> | reservationOrders /merge | Nee | Nee |
> | reservationOrders/reservations | Nee | Nee |
> | reservationOrders/reserveringen/revisies | Nee | Nee |
> | reservationOrders/return | Nee | Nee |
> | reservationOrders/split | Nee | Nee |
> | reserveringOrders/wisselen | Nee | Nee |
> | Reserveringen | Nee | Nee |
> | resourceProviders | Nee | Nee |
> | resources | Nee | Nee |
> | validateReservationOrder | Nee | Nee |

## <a name="microsoftcascade"></a>Microsoft.Cascade

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | sites | Ja | Ja |

## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | CdnWebApplicationFirewallManagedRuleSets | Nee | Nee |
> | CdnWebApplicationFirewallPolicies | Ja | Ja |
> | edgenodes | Nee | Nee |
> | Profielen | Ja | Ja |
> | profielen /afdendpoints | Ja | Ja |
> | profielen / afdendpoints / routes | Nee | Nee |
> | profielen/aangepastedomeinen | Nee | Nee |
> | profielen/eindpunten | Ja | Ja |
> | profielen / eindpunten / customdomains | Nee | Nee |
> | profielen / eindpunten / origingroups | Nee | Nee |
> | profielen/eindpunten/oorsprongen | Nee | Nee |
> | profielen/origingroups | Nee | Nee |
> | profielen /origingroups/origins | Nee | Nee |
> | profielen/regelsets | Nee | Nee |
> | profielen / regelsets / regels | Nee | Nee |
> | profielen/geheimen | Nee | Nee |
> | profielen/beveiligingsbeleid | Nee | Nee |
> | validateProbe | Nee | Nee |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | certificateOrders | Ja | Ja |
> | certificateOrders/certificates | Nee | Nee |
> | validateCertificateRegistrationInformation | Nee | Nee |

## <a name="microsoftchangeanalysis"></a>Microsoft.ChangeAnalysis

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | Wijzigingen | Nee | Nee |
> | profiel | Nee | Nee |
> | resourceChanges | Nee | Nee |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | mogelijkheden | Nee | Nee |
> | Domeinnamen | Nee | Nee |
> | domainNames/capabilities | Nee | Nee |
> | domainNames/internalLoadBalancers | Nee | Nee |
> | domainNames/serviceCertificates | Nee | Nee |
> | domainNames/slots | Nee | Nee |
> | domainNames/slots/roles | Nee | Nee |
> | domainNames / slots / roles / metricDefinitions | Nee | Nee |
> | domainNames / slots / rollen / metrische gegevens | Nee | Nee |
> | moveSubscriptionResources | Nee | Nee |
> | operatingSystemFamilies | Nee | Nee |
> | operatingSystems | Nee | Nee |
> | quotas | Nee | Nee |
> | resourceTypes | Nee | Nee |
> | validateSubscriptionMoveAvailability | Nee | Nee |
> | virtualMachines | Nee | Nee |
> | virtualMachines/diagnosticSettings | Nee | Nee |
> | virtualMachines/metricDefinitions | Nee | Nee |
> | virtualMachines/metrische gegevens | Nee | Nee |

## <a name="microsoftclassicinfrastructuremigrate"></a>Microsoft.ClassicInfrastructureMigrate

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | classicInfrastructureResources | Nee | Nee |

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | mogelijkheden | Nee | Nee |
> | expressRouteCrossConnections | Nee | Nee |
> | expressRouteCrossConnections/peerings | Nee | Nee |
> | gatewaySupportedDevices | Nee | Nee |
> | networkSecurityGroups | Nee | Nee |
> | quotas | Nee | Nee |
> | reservedIps | Nee | Nee |
> | virtualNetworks | Nee | Nee |
> | virtualNetworks / remoteVirtualNetworkPeeringProxies | Nee | Nee |
> | virtualNetworks/virtualNetworkPeerings | Nee | Nee |

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | mogelijkheden | Nee | Nee |
> | Schijven | Nee | Nee |
> | images | Nee | Nee |
> | osImages | Nee | Nee |
> | osPlatformImages | Nee | Nee |
> | publicImages | Nee | Nee |
> | quotas | Nee | Nee |
> | storageAccounts | Nee | Nee |
> | storageAccounts/blobServices | Nee | Nee |
> | storageAccounts/fileServices | Nee | Nee |
> | storageAccounts/metricDefinitions | Nee | Nee |
> | storageAccounts/metrische gegevens | Nee | Nee |
> | storageAccounts/queueServices | Nee | Nee |
> | storageAccounts/services | Nee | Nee |
> | storageAccounts / services / diagnosticSettings | Nee | Nee |
> | storageAccounts / services / metricDefinitions | Nee | Nee |
> | storageAccounts / services / metrische gegevens | Nee | Nee |
> | storageAccounts/tableServices | Nee | Nee |
> | storageAccounts/vmImages | Nee | Nee |
> | vmImages | Nee | Nee |

## <a name="microsoftclusterstor"></a>Microsoft.ClusterStor

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | Knooppunten | Ja | Ja |

## <a name="microsoftcodespaces"></a>Microsoft.Codespaces

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | Plannen | Ja | Nee |
> | registeredSubscriptions | Nee | Nee |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | accounts | Ja | Ja |
> | accounts / privateEndpointConnectionProxies | Nee | Nee |
> | accounts /privateEndpointConnections | Nee | Nee |
> | accounts /privateLinkResources | Nee | Nee |

## <a name="microsoftcommerce"></a>Microsoft.Commerce

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | RateCard | Nee | Nee |
> | UsageAggregates | Nee | Nee |

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | availabilitySets | Ja | Ja |
> | cloudServices | Ja | Ja |
> | cloudServices/networkInterfaces | Nee | Nee |
> | cloudServices/publicIPAddresses | Nee | Nee |
> | cloudServices/roleInstances | Nee | Nee |
> | cloudServices / roleInstances / networkInterfaces | Nee | Nee |
> | cloudServices/rollen | Nee | Nee |
> | diskAccccss | Ja | Ja |
> | diskEncryptionSets | Ja | Ja |
> | Schijven | Ja | Ja |
> | Galeries | Ja | Ja |
> | galerieën/toepassingen | Nee | Nee |
> | galerieën / toepassingen / versies | Nee | Nee |
> | galerieën/afbeeldingen | Nee | Nee |
> | galerieën/afbeeldingen/versies | Nee | Nee |
> | hostGroups | Ja | Ja |
> | hostGroups/hosts | Ja | Ja |
> | images | Ja | Ja |
> | proximityPlacementGroups | Ja | Ja |
> | restorePointCollections | Ja | Ja |
> | restorePointCollections/restorePoints | Nee | Nee |
> | sharedVMExtensions | Ja | Ja |
> | sharedVMExtensions /versions | Nee | Nee |
> | sharedVMImages | Ja | Ja |
> | sharedVMImages /versions | Nee | Nee |
> | momentopnamen | Ja | Ja |
> | sshPublicKeys | Ja | Ja |
> | virtualMachines | Ja | Ja |
> | virtualMachines/extensies | Ja | Ja |
> | virtualMachines/metricDefinitions | Nee | Nee |
> | virtualMachines/runCommands | Ja | Ja |
> | virtualMachineScaleSets | Ja | Ja |
> | virtualMachineScaleSets/extensions | Nee | Nee |
> | virtualMachineScaleSets/networkInterfaces | Nee | Nee |
> | virtualMachineScaleSets/publicIPAddresses | Ja | Nee |
> | virtualMachineScaleSets /virtualMachines | Nee | Nee |
> | virtualMachineScaleSets /virtualMachines/networkInterfaces | Nee | Nee |

> [!NOTE]
> U kunt geen tag toevoegen aan een virtuele machine die is gemarkeerd als gegeneraliseerd. U markeert een virtuele machine als ge generaliseerd [met Set-AzVm -Generalized](/powershell/module/Az.Compute/Set-AzVM) of [az vm generalize](/cli/azure/vm#az_vm_generalize).

## <a name="microsoftconnectedcache"></a>Microsoft.ConnectedCache

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | CacheNodes | Ja | Ja |

## <a name="microsoftconnectedvehicle"></a>Microsoft.ConnectedVehicle

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | platformAccounts | Ja | Ja |
> | registeredSubscriptions | Nee | Nee |

## <a name="microsoftconnectedvmwarevsphere"></a>Microsoft.ConnectedVMwarevSphere

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | ResourcePools | Ja | Ja |
> | VCenters | Ja | Ja |
> | VCenters/InventoryItems | Nee | Nee |
> | VirtualMachines | Ja | Ja |
> | VirtualMachines /Extensions | Ja | Ja |
> | VirtualMachines/GuestAgents | Nee | Nee |
> | VirtualMachines /HybridIdentityMetadata | Nee | Nee |
> | VirtualMachineTemplates | Ja | Ja |
> | VirtualNetworks | Ja | Ja |

## <a name="microsoftconsumption"></a>Microsoft.Consumption

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | AggregatedCost | Nee | Nee |
> | Tegoeden | Nee | Nee |
> | Budgetten | Nee | Nee |
> | Kosten | Nee | Nee |
> | CostTags | Nee | Nee |
> | Credits | Nee | Nee |
> | events | Nee | Nee |
> | Prognoses | Nee | Nee |
> | Veel | Nee | Nee |
> | Markten | Nee | Nee |
> | Prijzenoverzichten | Nee | Nee |
> | Producten | Nee | Nee |
> | ReservationDetails | Nee | Nee |
> | ReservationRecommendationDetails | Nee | Nee |
> | ReservationRecommendations | Nee | Nee |
> | ReserveringSummaries | Nee | Nee |
> | ReservationTransactions | Nee | Nee |
> | Tags | Nee | Nee |
> | Huurders | Nee | Nee |
> | Termen | Nee | Nee |
> | UsageDetails | Nee | Nee |

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | containerGroups | Ja | Ja |
> | serviceAssociationLinks | Nee | Nee |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | Registers | Ja | Ja |
> | registers/agentPools | Ja | Ja |
> | registers/builds | Nee | Nee |
> | registers / builds / annuleren | Nee | Nee |
> | registers / builds / getLogLink | Nee | Nee |
> | registers /buildTasks | Ja | Ja |
> | registers / buildTasks / stappen | Nee | Nee |
> | registers /connectedRegistries | Nee | Nee |
> | registers / connectedRegistries / deactiveren | Nee | Nee |
> | registers / eventGridFilters | Nee | Nee |
> | registers /exportPipelines | Nee | Nee |
> | registers /generateCredentials | Nee | Nee |
> | registers / getBuildSourceUploadUrl | Nee | Nee |
> | registers / GetCredentials | Nee | Nee |
> | registers /importImage | Nee | Nee |
> | registers /importPipelines | Nee | Nee |
> | registers/pipelineRuns | Nee | Nee |
> | registers / privateEndpointConnectionProxies | Nee | Nee |
> | registers / privateEndpointConnectionProxies / valideren | Nee | Nee |
> | registers /privateEndpointConnections | Nee | Nee |
> | registers /privateLinkResources | Nee | Nee |
> | registers /queueBuild | Nee | Nee |
> | registers /regenerateCredential | Nee | Nee |
> | registers /regenerateCredentials | Nee | Nee |
> | registers/replicaties | Ja | Ja |
> | registers /wordt uitgevoerd | Nee | Nee |
> | registers / wordt uitgevoerd / annuleren | Nee | Nee |
> | registers /scheduleRun | Nee | Nee |
> | registers/scopeMaps | Nee | Nee |
> | registers/taskRuns | Nee | Nee |
> | registers/taken | Ja | Ja |
> | registers/tokens | Nee | Nee |
> | registers /updatePolicies | Nee | Nee |
> | registers/webhooks | Ja | Ja |
> | registers / webhooks / getCallbackConfig | Nee | Nee |
> | registers /webhooks/ping | Nee | Nee |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | containerServices | Ja | Ja |
> | managedClusters | Ja | Ja |
> | ManagedClusters/eventGridFilters | Nee | Nee |
> | openShiftManagedClusters | Ja | Ja |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
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
> | ExternalBillingAccounts/Alerts | Nee | Nee |
> | ExternalBillingAccounts/Dimensies | Nee | Nee |
> | ExternalBillingAccounts/Forecast | Nee | Nee |
> | ExternalBillingAccounts/Query | Nee | Nee |
> | ExternalSubscriptions | Nee | Nee |
> | ExternalSubscriptions /Alerts | Nee | Nee |
> | ExternalSubscriptions /Dimensions | Nee | Nee |
> | ExternalSubscriptions /Forecast | Nee | Nee |
> | ExternalSubscriptions /Query | Nee | Nee |
> | fetchPrices | Nee | Nee |
> | Prognose | Nee | Nee |
> | GenerateDetailedCostReport | Nee | Nee |
> | GenerateReservationDetailsReport | Nee | Nee |
> | Inzichten | Nee | Nee |
> | Query | Nee | Nee |
> | registreren | Nee | Nee |
> | Rapportconfigs | Nee | Nee |
> | Rapporten | Nee | Nee |
> | ScheduledActions | Nee | Nee |
> | Instellingen | Nee | Nee |
> | showbackRules | Nee | Nee |
> | Weergaven | Nee | Nee |

## <a name="microsoftcustomerlockbox"></a>Microsoft.CustomerLockbox

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | DisableLockbox | Nee | Nee |
> | EnableLockbox | Nee | Nee |
> | requests | Nee | Nee |
> | TenantOptedIn | Nee | Nee |

## <a name="microsoftcustomproviders"></a>Microsoft.CustomProviders

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | Verenigingen | Nee | Nee |
> | resourceProviders | Ja | Ja |

## <a name="microsoftd365customerinsights"></a>Microsoft.D365CustomerInsights

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | Exemplaren | Ja | Ja |

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | Banen | Ja | Ja |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | DataBoxEdgeDevices | Ja | Ja |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | workspaces | Ja | Ja |
> | werkruimten / dbWorkspaces | Nee | Nee |
> | werkruimten / virtualNetworkPeerings | Nee | Nee |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | Catalogi | Ja | Ja |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | dataFactories | Ja | Ja |
> | dataFactories/diagnosticSettings | Nee | Nee |
> | dataFactories/metricDefinitions | Nee | Nee |
> | dataFactorySchema | Nee | Nee |
> | Fabrieken | Ja | Ja |
> | factories/integrationRuntimes | Nee | Nee |

> [!NOTE]
> Als u Azure-SSIS-integratieruntimes in uw data factory, worden de lopende kosten gelabeld met data factory tags. Het uitvoeren van Azure-SSIS Integration Runtimes moet worden gestopt en opnieuw worden gestart om nieuwe data factory-tags toe te voegen aan de lopende kosten.

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | accounts | Ja | Ja |
> | accounts /dataLakeStoreAccounts | Nee | Nee |
> | accounts/storageAccounts | Nee | Nee |
> | accounts / storageAccounts / containers | Nee | Nee |
> | accounts /transferAnalyticsUnits | Nee | Nee |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | accounts | Ja | Ja |
> | accounts / eventGridFilters | Nee | Nee |
> | accounts/firewallRules | Nee | Nee |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | DatabaseMigrations | Nee | Nee |
> | services | Ja | Ja |
> | services/projecten | Ja | Ja |
> | SqlMigrationServices | Ja | Ja |

## <a name="microsoftdataprotection"></a>Microsoft.DataProtection

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | BackupVaults | Ja | Ja |
> | ResourceGuards | Ja | Ja |

## <a name="microsoftdatashare"></a>Microsoft.DataShare

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | accounts | Ja | Ja |
> | accounts/shares | Nee | Nee |
> | accounts / shares / gegevenssets | Nee | Nee |
> | accounts / shares / uitnodigingen | Nee | Nee |
> | accounts / shares / providersharesubscriptions | Nee | Nee |
> | accounts / shares / synchronizationSettings | Nee | Nee |
> | accounts/sharesubscriptions | Nee | Nee |
> | accounts /sharesubscriptions / consumerSourceDataSets | Nee | Nee |
> | accounts / sharesubscriptions / datasetmappings | Nee | Nee |
> | accounts / sharesubscriptions / triggers | Nee | Nee |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | Servers | Ja | Ja |
> | servers /advisors | Nee | Nee |
> | servers/sleutels | Nee | Nee |
> | servers / privateEndpointConnectionProxies | Nee | Nee |
> | servers /privateEndpointConnections | Nee | Nee |
> | servers /privateLinkResources | Nee | Nee |
> | servers/queryTexts | Nee | Nee |
> | servers /recoverableServers | Nee | Nee |
> | servers / resetQueryPerformanceInsightData | Nee | Nee |
> | servers / starten | Nee | Nee |
> | servers / stoppen | Nee | Nee |
> | servers /topQueryStatistics | Nee | Nee |
> | servers / virtualNetworkRules | Nee | Nee |
> | servers/waitStatistics | Nee | Nee |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | flexibleServers | Ja | Ja |
> | Servers | Ja | Ja |
> | servers /advisors | Nee | Nee |
> | servers/sleutels | Nee | Nee |
> | servers / privateEndpointConnectionProxies | Nee | Nee |
> | servers / privateEndpointConnections | Nee | Nee |
> | servers / privateLinkResources | Nee | Nee |
> | servers /queryTexts | Nee | Nee |
> | servers / recoverableServers | Nee | Nee |
> | servers / resetQueryPerformanceInsightData | Nee | Nee |
> | servers / starten | Nee | Nee |
> | servers / stoppen | Nee | Nee |
> | servers / topQueryStatistics | Nee | Nee |
> | servers / upgrade | Nee | Nee |
> | servers / virtualNetworkRules | Nee | Nee |
> | servers /waitStatistics | Nee | Nee |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | flexibleServers | Ja | Ja |
> | serverGroups | Ja | Ja |
> | serverGroupsv2 | Ja | Ja |
> | Servers | Ja | Ja |
> | servers /advisors | Nee | Nee |
> | servers/sleutels | Nee | Nee |
> | servers / privateEndpointConnectionProxies | Nee | Nee |
> | servers /privateEndpointConnections | Nee | Nee |
> | servers /privateLinkResources | Nee | Nee |
> | servers/queryTexts | Nee | Nee |
> | servers /recoverableServers | Nee | Nee |
> | servers / resetQueryPerformanceInsightData | Nee | Nee |
> | servers /topQueryStatistics | Nee | Nee |
> | servers / virtualNetworkRules | Nee | Nee |
> | servers/waitStatistics | Nee | Nee |
> | serversv2 | Ja | Ja |

## <a name="microsoftdeploymentmanager"></a>Microsoft.DeploymentManager

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | artifactSources | Ja | Ja |
> | implementaties | Ja | Ja |
> | serviceTopologies | Ja | Ja |
> | serviceTopologies/services | Ja | Ja |
> | serviceTopologies / services / serviceUnits | Ja | Ja |
> | stappen | Ja | Ja |

## <a name="microsoftdesktopvirtualization"></a>Microsoft.DesktopVirtualization

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | applicationgroups | Ja | Ja |
> | applicationgroups/applications | Nee | Nee |
> | applicationgroups/desktops | Nee | Nee |
> | applicationgroups/startmenuitems | Nee | Nee |
> | hostpools | Ja | Ja |
> | hostpools /msixpackages | Nee | Nee |
> | hostpools/sessionhosts | Nee | Nee |
> | hostpools / sessionhosts / usersessions | Nee | Nee |
> | hostpools/usersessions | Nee | Nee |
> | scalingPlans | Ja | Ja |
> | workspaces | Ja | Ja |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | ElasticPools | Ja | Ja |
> | ElasticPools/IotHubTenants | Ja | Ja |
> | ElasticPools/IotHubTenants/securitySettings | Nee | Nee |
> | IotHubs | Ja | Ja |
> | IotHubs / eventGridFilters | Nee | Nee |
> | IotHubs/securitySettings | Nee | Nee |
> | ProvisioningServices | Ja | Ja |
> | gebruik | Nee | Nee |

## <a name="microsoftdeviceupdate"></a>Microsoft.DeviceUpdate

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | accounts | Ja | Ja |
> | accounts/exemplaren | Ja | Ja |
> | registeredSubscriptions | Nee | Nee |

## <a name="microsoftdevops"></a>Microsoft.DevOps

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | Pijpleidingen | Ja | Ja |

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | Controllers | Ja | Ja |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | labcenters | Ja | Ja |
> | Labs | Ja | Ja |
> | labs/omgevingen | Ja | Ja |
> | labs /serviceRunners | Ja | Ja |
> | labs / virtualMachines | Ja | Ja |
> | Planningen | Ja | Ja |

## <a name="microsoftdigitaltwins"></a>Microsoft.DigitalTwins

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | digitalTwinsInstances | Ja | Ja |
> | digitalTwinsInstances/eindpunten | Nee | Nee |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | cassandraClusters | Ja | Ja |
> | databaseAccountNames | Nee | Nee |
> | databaseAccounts | Ja | Ja |
> | restorableDatabaseAccounts | Nee | Nee |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | Domeinen | Ja | Ja |
> | domeinen /domainOwnershipIdentifiers | Nee | Nee |
> | generateSsoRequest | Nee | Nee |
> | topLevelDomains | Nee | Nee |
> | validateDomainRegistrationInformation | Nee | Nee |

## <a name="microsoftdynamicslcs"></a>Microsoft.DynamicsLcs

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | lcsprojects | Nee | Nee |
> | lcsprojects/clouddeployments | Nee | Nee |
> | lcsprojects/connectors | Nee | Nee |

## <a name="microsoftedgeorder"></a>Microsoft.EdgeOrder

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | Adressen | Ja | Ja |
> | orderCollections | Ja | Ja |
> | orders | Ja | Ja |
> | productFamiliesMetadata | Nee | Nee |

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft.EnterpriseKnowledgeGraph

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | services | Ja | Ja |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | Domeinen | Ja | Ja |
> | domeinen/onderwerpen | Nee | Nee |
> | eventSubscriptions | Nee | Nee |
> | extensionTopics | Nee | Nee |
> | partnerNamespaces | Ja | Ja |
> | partnerNamespaces / eventChannels | Nee | Nee |
> | partnerRegisters | Ja | Ja |
> | partnerTopics | Ja | Ja |
> | partnerTopics /eventSubscriptions | Nee | Nee |
> | systemTopics | Ja | Ja |
> | systemTopics /eventSubscriptions | Nee | Nee |
> | Onderwerpen | Ja | Ja |
> | topicTypes | Nee | Nee |

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | Clusters | Ja | Ja |
> | Naamruimten | Ja | Ja |
> | naamruimten/authorizationrules | Nee | Nee |
> | naamruimten /disasterrecoveryconfigs | Nee | Nee |
> | naamruimten /eventhubs | Nee | Nee |
> | naamruimten / eventhubs / authorizationrules | Nee | Nee |
> | naamruimten / eventhubs / consumergroups | Nee | Nee |
> | naamruimten/networkrulesets | Nee | Nee |
> | naamruimten /privateEndpointConnections | Nee | Nee |

## <a name="microsoftexperimentation"></a>Microsoft.Experimentation

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | experimentWorkspaces | Ja | Ja |

## <a name="microsoftfalcon"></a>Microsoft.Hado

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | Naamruimten | Ja | Ja |

## <a name="microsoftfeatures"></a>Microsoft.Features

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | featureConfigurations | Nee | Nee |
> | featureProviderNamespaces | Nee | Nee |
> | featureProviders | Nee | Nee |
> | features | Nee | Nee |
> | providers | Nee | Nee |
> | subscriptionFeatureRegistrations | Nee | Nee |

## <a name="microsoftgallery"></a>Microsoft.Gallery

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | Inschrijven | Nee | Nee |
> | galleryitems | Nee | Nee |
> | generateartifactaccessuri | Nee | Nee |
> | myareas | Nee | Nee |
> | myareas/areas | Nee | Nee |
> | myareas /areas/areas | Nee | Nee |
> | myareas /areas/areas /galleryitems | Nee | Nee |
> | myareas /areas /galleryitems | Nee | Nee |
> | myareas/galleryitems | Nee | Nee |
> | registreren | Nee | Nee |
> | resources | Nee | Nee |
> | retrieveresourcesbyid | Nee | Nee |

## <a name="microsoftgenomics"></a>Microsoft.Genomics

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | accounts | Ja | Ja |

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | autoManagedAccounts | Ja | Ja |
> | autoManagedVmConfigurationProfiles | Ja | Ja |
> | configurationProfileAssignments | Nee | Nee |
> | guestConfigurationAssignments | Nee | Nee |
> | Software | Nee | Nee |
> | softwareUpdateProfile | Nee | Nee |
> | softwareUpdates | Nee | Nee |

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | hanaInstances | Ja | Ja |
> | sapMonitors | Ja | Ja |

## <a name="microsofthardwaresecuritymodules"></a>Microsoft.HardwareSecurityModules

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | dedicatedHSMs | Ja | Ja |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | clusterPools | Ja | Ja |
> | clusterPools/clusters | Ja | Ja |
> | Clusters | Ja | Ja |
> | clusters/toepassingen | Nee | Nee |

## <a name="microsofthealthbot"></a>Microsoft.HealthBot

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | healthBots | Ja | Ja |

## <a name="microsofthealthcareapis"></a>Microsoft.HealthcareApis

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | services | Ja | Ja |
> | services /iomtconnectors | Nee | Nee |
> | services / iomtconnectors / verbindingen | Nee | Nee |
> | services / iomtconnectors / toewijzingen | Nee | Nee |
> | services / privateEndpointConnectionProxies | Nee | Nee |
> | services / privateEndpointConnections | Nee | Nee |
> | services / privateLinkResources | Nee | Nee |
> | workspaces | Ja | Ja |
> | werkruimten /dicomservices | Ja | Ja |

## <a name="microsofthybridcompute"></a>Microsoft.HybridCompute

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | Machines | Ja | Ja |
> | machines/assessPatches | Nee | Nee |
> | machines/extensies | Ja | Ja |
> | machines /installPatches | Nee | Nee |
> | machines / privateLinkScopes | Nee | Nee |
> | privateLinkScopes | Ja | Ja |
> | privateLinkScopes/privateEndpointConnectionProxies | Nee | Nee |
> | privateLinkScopes/privateEndpointConnections | Nee | Nee |

## <a name="microsofthybriddata"></a>Microsoft.HybridData

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | dataManagers | Ja | Ja |

## <a name="microsofthybridnetwork"></a>Microsoft.HybridNetwork

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | devices | Ja | Ja |
> | networkfunctions | Ja | Ja |
> | networkFunctionVendors | Nee | Nee |
> | registeredSubscriptions | Nee | Nee |
> | Leveranciers | Nee | Nee |
> | Leveranciers/vendorskus | Nee | Nee |
> | Leveranciers/vendorskus/previewsabonnementen | Nee | Nee |
> | virtualNetworkFunctions | Ja | Ja |
> | virtualNetworkFunctionVendors | Nee | Nee |

## <a name="microsofthydra"></a>Microsoft.Hier

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | Onderdelen | Ja | Ja |
> | networkScopes | Ja | Ja |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | Banen | Ja | Ja |

## <a name="microsoftinsights"></a>Microsoft.Insights

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | actionGroups | Ja | Ja |
> | activityLogAlerts | Ja | Ja |
> | waarschuwingsrules | Ja | Ja |
> | automatische schaalsets | Ja | Ja |
> | Onderdelen | Ja | Ja |
> | onderdelen / linkedStorageAccounts | Nee | Nee |
> | onderdelen / ProactiveDetectionConfigs | Nee | Nee |
> | diagnosticSettings | Nee | Nee |
> | guestDiagnosticSettings | Ja | Ja |
> | guestDiagnosticSettingsAssociation | Ja | Ja |
> | logprofiles | Ja | Ja |
> | metricAlerts | Ja | Ja |
> | privateLinkScopes | Ja | Ja |
> | privateLinkScopes/privateEndpointConnections | Nee | Nee |
> | privateLinkScopes/scopedResources | Nee | Nee |
> | queryPacks | Ja | Ja |
> | queryPacks /query's | Nee | Nee |
> | scheduledQueryRules | Ja | Ja |
> | webtests | Ja | Ja |
> | werkmappen | Ja | Ja |
> | workbooktemplates | Ja | Ja |

## <a name="microsoftintune"></a>Microsoft.Intune

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | diagnosticSettings | Nee | Nee |
> | diagnosticSettingsCategories | Nee | Nee |

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | appTemplates | Nee | Nee |
> | IoTApps | Ja | Ja |

## <a name="microsoftiotsecurity"></a>Microsoft.IoTSecurity

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | defenderSettings | Nee | Nee |

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | Graph | Ja | Ja |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | deletedManagedHSMs | Nee | Nee |
> | deletedVaults | Nee | Nee |
> | hsmPools | Ja | Ja |
> | managedHSMs | Ja | Ja |
> | Kluizen | Ja | Ja |
> | kluizen/accessPolicies | Nee | Nee |
> | kluizen /eventGridFilters | Nee | Nee |
> | kluizen/sleutels | Nee | Nee |
> | kluizen/sleutels/versies | Nee | Nee |
> | kluizen/geheimen | Nee | Nee |

## <a name="microsoftkubernetes"></a>Microsoft.Kubernetes

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | connectedClusters | Ja | Ja |
> | registeredSubscriptions | Nee | Nee |

## <a name="microsoftkubernetesconfiguration"></a>Microsoft.KubernetesConfiguration

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | Extensies | Nee | Nee |
> | sourceControlConfigurations | Nee | Nee |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | Clusters | Ja | Ja |
> | clusters /attacheddatabaseconfigurations | Nee | Nee |
> | clusters/databases | Nee | Nee |
> | clusters / databases / gegevensverbindingen | Nee | Nee |
> | clusters/ databases / eventhubconnections | Nee | Nee |
> | clusters / databases / principalassignments | Nee | Nee |
> | clusters / databases / scripts | Nee | Nee |
> | clusters/gegevensverbindingen | Nee | Nee |
> | clusters/principalassignments | Nee | Nee |
> | clusters /sharedidentities | Nee | Nee |

## <a name="microsoftlabservices"></a>Microsoft.LabServices

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | labaccounts | Ja | Nee |
> | labplans | Ja | Ja |
> | Labs | Ja | Ja |
> | gebruikers | Nee | Nee |

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | hostingEnvironments | Ja | Ja |
> | integrationAccounts | Ja | Ja |
> | integrationServiceEnvironments | Ja | Ja |
> | integrationServiceEnvironments / managedApis | Nee | Nee |
> | isolatedEnvironments | Ja | Ja |
> | Werkstromen | Ja | Ja |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | commitmentPlans | Ja | Ja |
> | Webservices | Ja | Ja |
> | Workspaces | Ja | Ja |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | modelinventories | Ja | Ja |
> | virtualclusters | Ja | Ja |
> | workspaces | Ja | Ja |
> | werkruimten /batchEndpoints | Ja | Ja |
> | werkruimten / batchEndpoints / implementaties | Ja | Ja |
> | werkruimten / batchEndpoints / implementaties / taken | Nee | Nee |
> | werkruimten / batchEndpoints / taken | Nee | Nee |
> | werkruimten/codes | Nee | Nee |
> | werkruimten / codes / versies | Nee | Nee |
> | werkruimten/berekeningen | Nee | Nee |
> | werkruimten/gegevens | Nee | Nee |
> | werkruimten/gegevensstores | Nee | Nee |
> | werkruimten/omgevingen | Nee | Nee |
> | werkruimten / eventGridFilters | Nee | Nee |
> | werkruimten/taken | Nee | Nee |
> | werkruimten /labelenJobs | Nee | Nee |
> | werkruimten /linkedServices | Nee | Nee |
> | werkruimten/modellen | Nee | Nee |
> | werkruimten/modellen/versies | Nee | Nee |
> | werkruimten /onlineEndpoints | Ja | Ja |
> | werkruimten / online Eindpunten / implementaties | Ja | Ja |

> [!NOTE]
> Werkruimtetags worden niet doorgegeven aan rekenclusters en reken-exemplaren.

## <a name="microsoftmaintenance"></a>Microsoft.Maintenance

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | applyUpdates | Nee | Nee |
> | configurationAssignments | Nee | Nee |
> | maintenanceConfigurations | Ja | Ja |
> | publicMaintenanceConfigurations | Nee | Nee |
> | updates | Nee | Nee |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | Identiteiten | Nee | Nee |
> | userAssignedIdentities | Ja | Ja |

## <a name="microsoftmanagednetwork"></a>Microsoft.ManagedNetwork

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | managedNetworks | Ja | Ja |
> | managedNetworks/managedNetworkGroups | Ja | Ja |
> | managedNetworks /managedNetworkPeeringPolicies | Ja | Ja |
> | melding | Ja | Ja |

## <a name="microsoftmanagedservices"></a>Microsoft.ManagedServices

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | marketplaceRegistrationDefinitions | Nee | Nee |
> | registrationAssignments | Nee | Nee |
> | registrationDefinitions | Nee | Nee |

## <a name="microsoftmanagement"></a>Microsoft.Management

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | getEntities | Nee | Nee |
> | managementGroups | Nee | Nee |
> | managementGroups/settings | Nee | Nee |
> | resources | Nee | Nee |
> | startTenantBackfill | Nee | Nee |
> | tenantBackfillStatus | Nee | Nee |

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | accounts | Ja | Ja |
> | accounts /makers | Ja | Ja |
> | accounts / eventGridFilters | Nee | Nee |
> | accounts /privateAtlases | Ja | Ja |

## <a name="microsoftmarketplace"></a>Microsoft.Marketplace

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | macc | Nee | Nee |
> | Biedt | Nee | Nee |
> | offerTypes | Nee | Nee |
> | offerTypes /publishers | Nee | Nee |
> | offerTypes/uitgevers/aanbiedingen | Nee | Nee |
> | offerTypes / uitgevers / aanbiedingen / abonnementen | Nee | Nee |
> | offerTypes / uitgevers / aanbiedingen / abonnementen / overeenkomsten | Nee | Nee |
> | offerTypes / uitgevers / aanbiedingen / abonnementen / configuraties | Nee | Nee |
> | offerTypes / uitgevers / aanbiedingen / abonnementen / configuraties / importImage | Nee | Nee |
> | privategalleryitems | Nee | Nee |
> | privateStoreClient | Nee | Nee |
> | privateStores | Nee | Nee |
> | privateStores/AdminRequestApprovals | Nee | Nee |
> | privateStores/aanbiedingen | Nee | Nee |
> | privateStores /offers/acknowledgeNotification | Nee | Nee |
> | privateStores/queryNotificationsState | Nee | Nee |
> | privateStores/RequestApprovals | Nee | Nee |
> | privateStores/requestApprovals /query | Nee | Nee |
> | privateStores/requestApprovals /2019Plan | Nee | Nee |
> | Producten | Nee | Nee |
> | Uitgevers | Nee | Nee |
> | uitgevers/aanbiedingen | Nee | Nee |
> | uitgevers/aanbiedingen/aanpassingen | Nee | Nee |
> | registreren | Nee | Nee |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | classicDevServices | Ja | Ja |
> | updateCommunicationPreference | Nee | Nee |

## <a name="microsoftmarketplaceordering"></a>Microsoft.MarketplaceOrdering

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | Overeenkomsten | Nee | Nee |
> | offertypes | Nee | Nee |

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | mediaservices | Ja | Ja |
> | mediaservices/accountFilters | Nee | Nee |
> | mediaservices/assets | Nee | Nee |
> | mediaservices / assets / assetFilters | Nee | Nee |
> | mediaservices /contentKeyPolicies | Nee | Nee |
> | mediaservices / eventGridFilters | Nee | Nee |
> | mediaservices/graphInstances | Nee | Nee |
> | mediaservices/graphTopologies | Nee | Nee |
> | mediaservices / liveEventOperations | Nee | Nee |
> | mediaservices /liveEvents | Ja | Ja |
> | mediaservices / liveEvents / liveOutputs | Nee | Nee |
> | mediaservices / liveOutputOperations | Nee | Nee |
> | mediaservices /mediaGraphs | Nee | Nee |
> | mediaservices / privateEndpointConnectionOperations | Nee | Nee |
> | mediaservices / privateEndpointConnectionProxies | Nee | Nee |
> | mediaservices / privateEndpointConnections | Nee | Nee |
> | mediaservices /streamingEndpointOperations | Nee | Nee |
> | mediaservices/streamingEndpoints | Ja | Ja |
> | mediaservices/streamingLocators | Nee | Nee |
> | mediaservices/streamingPolicies | Nee | Nee |
> | mediaservices/transformaties | Nee | Nee |
> | mediaservices / transformeert / taken | Nee | Nee |
> | videoAnalyzers | Ja | Ja |
> | videoAnalyzers/edgeModules | Nee | Nee |

## <a name="microsoftmicroservices4spring"></a>Microsoft.Microservices4Spring

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | appClusters | Ja | Ja |

## <a name="microsoftmigrate"></a>Microsoft.Migrate

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | assessmentProjects | Ja | Ja |
> | migrateprojects | Ja | Ja |
> | moveCollections | Ja | Ja |
> | Projecten | Ja | Ja |

## <a name="microsoftmixedreality"></a>Microsoft.MixedReality

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | holographicsBroadcastAccounts | Ja | Ja |
> | objectAnchorsAccounts | Ja | Ja |
> | objectUnderstandingAccounts | Ja | Ja |
> | remoteRenderingAccounts | Ja | Ja |
> | spatialAnchorsAccounts | Ja | Ja |

## <a name="microsoftmobilenetwork"></a>Microsoft.MobileNetwork

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | Netwerken | Ja | Ja |
> | netwerken/sites | Ja | Ja |
> | packetCores | Ja | Ja |
> | Sims | Ja | Ja |
> | : / simProfiles | Ja | Ja |

## <a name="microsoftnetapp"></a>Microsoft.NetApp

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | netAppAccounts | Ja | Nee |
> | netAppAccounts/accountBackups | Nee | Nee |
> | netAppAccounts/capacityPools | Ja | Nee |
> | netAppAccounts/capacityPools/volumes | Ja | Nee |
> | netAppAccounts/capacityPools/volumes/momentopnamen | Nee | Nee |
> | netAppAccounts/volumeGroups | Nee | Nee |

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | applicationGateways | Ja | Ja |
> | applicationGatewayWebApplicationFirewallPolicies | Ja | Ja |
> | applicationSecurityGroups | Ja | Ja |
> | azureFirewallFqdnTags | Nee | Nee |
> | azureFirewalls | Ja | Nee |
> | bastionHosts | Ja | Nee |
> | bgpServiceCommunities | Nee | Nee |
> | Verbindingen | Ja | Ja |
> | ddosCustomPolicies | Ja | Ja |
> | ddosProtectionPlans | Ja | Ja |
> | dnsOperationStatuses | Nee | Nee |
> | dnszones | Ja | Ja |
> | dnszones / A | Nee | Nee |
> | dnszones / AAAA | Nee | Nee |
> | dnszones / alle | Nee | Nee |
> | dnszones /CAA | Nee | Nee |
> | dnszones / CNAME | Nee | Nee |
> | dnszones / MX | Nee | Nee |
> | dnszones / NS | Nee | Nee |
> | dnszones/PTR | Nee | Nee |
> | dnszones/recordsets | Nee | Nee |
> | dnszones / SOA | Nee | Nee |
> | dnszones / SRV | Nee | Nee |
> | dnszones / TXT | Nee | Nee |
> | expressRouteCircuits | Ja | Ja |
> | expressRouteCrossConnections | Ja | Ja |
> | expressRouteGateways | Ja | Ja |
> | expressRoutePorts | Ja | Ja |
> | expressRouteServiceProviders | Nee | Nee |
> | firewallPolicies | Ja | Ja |
> | frontdoors | Ja, maar beperkt (zie [opmerking hieronder](#frontdoor)) | Yes |
> | frontdoorWebApplicationFirewallManagedRuleSets | Ja, maar beperkt (zie [opmerking hieronder](#frontdoor)) | No |
> | frontdoorWebApplicationFirewallPolicies | Ja, maar beperkt (zie [opmerking hieronder](#frontdoor)) | Yes |
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
> | networkWatchers/connectionMonitors | Ja | Nee |
> | networkWatchers/flowLogs | Ja | Nee |
> | networkWatchers/lenses | Ja | Nee |
> | networkWatchers/pingMeshes | Ja | Nee |
> | p2sVpnGateways | Ja | Ja |
> | privateDnsOperationStatuses | Nee | Nee |
> | privateDnsZones | Ja | Ja |
> | privateDnsZones /A | Nee | Nee |
> | privateDnsZones / AAAA | Nee | Nee |
> | privateDnsZones /all | Nee | Nee |
> | privateDnsZones/CNAME | Nee | Nee |
> | privateDnsZones / MX | Nee | Nee |
> | privateDnsZones/PTR | Nee | Nee |
> | privateDnsZones/SOA | Nee | Nee |
> | privateDnsZones/SRV | Nee | Nee |
> | privateDnsZones/TXT | Nee | Nee |
> | privateDnsZones /virtualNetworkLinks | Ja | Ja |
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
> Voor Azure Front Door Service kunt u tags toepassen bij het maken van de resource, maar het bijwerken of toevoegen van tags wordt momenteel niet ondersteund.


## <a name="microsoftnotebooks"></a>Microsoft.Notebooks

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | NotebookProxies | Nee | Nee |

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | Naamruimten | Ja | Nee |
> | namespaces/notificationHubs | Ja | Nee |

## <a name="microsoftobjectstore"></a>Microsoft.ObjectStore

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | osNamespaces | Ja | Ja |

## <a name="microsoftoffazure"></a>Microsoft.OffAzure

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | HyperVSites | Ja | Ja |
> | ImportSites | Ja | Ja |
> | MasterSites | Ja | Ja |
> | ServerSites | Ja | Ja |
> | VMwareSites | Ja | Ja |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | Clusters | Ja | Ja |
> | deletedWorkspaces | Nee | Nee |
> | linkTargets | Nee | Nee |
> | querypacks | Ja | Ja |
> | storageInsightConfigs | Nee | Nee |
> | workspaces | Ja | Ja |
> | werkruimten/gegevensExports | Nee | Nee |
> | werkruimten/dataSources | Nee | Nee |
> | werkruimten /linkedServices | Nee | Nee |
> | workspaces / linkedStorageAccounts | Nee | Nee |
> | werkruimten/metagegevens | Nee | Nee |
> | werkruimten/query | Nee | Nee |
> | werkruimten / scopedPrivateLinkProxies | Nee | Nee |
> | werkruimten /storageInsightConfigs | Nee | Nee |
> | werkruimten/tabellen | Nee | Nee |

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | beheerkoppelingen | Nee | Nee |
> | beheerconfiguraties | Ja | Ja |
> | oplossingen | Ja | Ja |
> | Weergaven | Ja | Ja |

## <a name="microsoftpeering"></a>Microsoft.Peering

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | cdnPeeringPrefixes | Nee | Nee |
> | legacyPeerings | Nee | Nee |
> | peerAsns | Nee | Nee |
> | peerings | Ja | Ja |
> | peeringServiceCountries | Nee | Nee |
> | peeringServiceProviders | Nee | Nee |
> | peeringServices | Ja | Ja |

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | Getuigschriften | Nee | Nee |
> | eventGridFilters | Nee | Nee |
> | policyEvents | Nee | Nee |
> | policyMetadata | Nee | Nee |
> | policyStates | Nee | Nee |
> | policyTrackedResources | Nee | Nee |
> | herstel | Nee | Nee |

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> |  -consoles | Nee | Nee |
> | dashboards | Ja | Ja |
> | tenantconfiguraties | Nee | Nee |
> | userSettings | Nee | Nee |

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | privateLinkServicesForPowerBI | Ja | Ja |
> | Huurders | Ja | Ja |
> | tenants/werkruimten | Nee | Nee |
> | workspaceCollections | Ja | Ja |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | autoScaleVCores | Ja | Ja |
> | Capaciteiten | Ja | Ja |

## <a name="microsoftpowerplatform"></a>Microsoft.PowerPlatform

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | enterprisePolicies | Ja | Ja |

## <a name="microsoftprojectbabylon"></a>Microsoft.ProjectMasylon

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | accounts | Ja | Ja |
> | deletedAccounts | Nee | Nee |

## <a name="microsoftproviderhub"></a>Microsoft.ProviderHub

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | providerRegistrations | Nee | Nee |
> | providerRegistrations /customRollouts | Nee | Nee |
> | providerRegistrations /defaultRollouts | Nee | Nee |
> | providerRegistrations /resourceTypeRegistrations | Nee | Nee |

## <a name="microsoftpurview"></a>Microsoft.Purview

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | accounts | Ja | Ja |
> | deletedAccounts | Nee | Nee |
> | getDefaultAccount | Nee | Nee |
> | removeDefaultAccount | Nee | Nee |
> | setDefaultAccount | Nee | Nee |

## <a name="microsoftquantum"></a>Microsoft.Quantum

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | Workspaces | Ja | Ja |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | backupProtectedItems | Nee | Nee |
> | Kluizen | Ja | Ja |

## <a name="microsoftredhatopenshift"></a>Microsoft.RedHatOpenShift

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | OpenShiftClusters | Ja | Ja |

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | Naamruimten | Ja | Ja |
> | naamruimten/authorizationrules | Nee | Nee |
> | naamruimten /hybridconnections | Nee | Nee |
> | naamruimten / hybridconnections / authorizationrules | Nee | Nee |
> | naamruimten / privateEndpointConnections | Nee | Nee |
> | naamruimten /wcfrelays | Nee | Nee |
> | naamruimten / wcfrelays / authorizationrules | Nee | Nee |

## <a name="microsoftresourceconnector"></a>Microsoft.ResourceConnector

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | Apparaten | Ja | Ja |

## <a name="microsoftresourcegraph"></a>Microsoft.ResourceGraph

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | Query 's | Ja | Ja |
> | resourceChangeDetails | Nee | Nee |
> | resourceChanges | Nee | Nee |
> | resources | Nee | Nee |
> | resourcesHistory | Nee | Nee |
> | subscriptionsStatus | Nee | Nee |

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | availabilityStatuses | Nee | Nee |
> | childAvailabilityStatuses | Nee | Nee |
> | childResources | Nee | Nee |
> | opkomendeissues | Nee | Nee |
> | events | Nee | Nee |
> | impactedResources | Nee | Nee |
> | metagegevens | Nee | Nee |

## <a name="microsoftresources"></a>Microsoft.Resources

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | Implementaties | Ja | Nee |
> | implementaties/bewerkingen | Nee | Nee |
> | deploymentScripts | Ja | Ja |
> | deploymentScripts/logboeken | Nee | Nee |
> | Verwijzigingen | Nee | Nee |
> | providers | Nee | Nee |
> | resourceGroups | Ja | Nee |
> | Abonnementen | Ja | Nee |
> | templateSpecs | Ja | Ja |
> | templateSpecs/versions | Ja | Ja |
> | Huurders | Nee | Nee |

## <a name="microsoftsaas"></a>Microsoft.SaaS

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | toepassingen | Ja | Ja |
> | resources | Ja | Ja |
> | saasresources | Nee | Nee |

## <a name="microsoftscvmm"></a>Microsoft.ScVmm

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | Wolken | Ja | Ja |
> | VirtualMachines | Ja | Ja |
> | VirtualMachineTemplates | Ja | Ja |
> | VirtualNetworks | Ja | Ja |
> | vmmservers | Ja | Ja |

## <a name="microsoftsearch"></a>Microsoft.Search

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | resourceHealthMetadata | Nee | Nee |
> | searchServices | Ja | Ja |

## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | adaptiveNetworkHardenings | Nee | Nee |
> | advancedThreatProtectionSettings | Nee | Nee |
> | waarschuwingen | Nee | Nee |
> | waarschuwingenSuppressionRules | Nee | Nee |
> | allowedConnections | Nee | Nee |
> | applicationWhitelistings | Nee | Nee |
> | assessmentMetadata | Nee | Nee |
> | evaluaties | Nee | Nee |
> | autoDismissAlertsRules | Nee | Nee |
> | automatiseringen | Ja | Ja |
> | AutoProvisioningSettings | Nee | Nee |
> | Naleving | Nee | Nee |
> | connectoren | Nee | Nee |
> | dataCollectionAgents | Nee | Nee |
> | devices | Nee | Nee |
> | deviceSecurityGroups | Nee | Nee |
> | discoveredSecuritySolutions | Nee | Nee |
> | externalSecuritySolutions | Nee | Nee |
> | InformationProtectionPolicies | Nee | Nee |
> | opnameInstellingen | Nee | Nee |
> | Inzichten | Nee | Nee |
> | iotAlerts | Nee | Nee |
> | iotAlertTypes | Nee | Nee |
> | iotDefenderSettings | Nee | Nee |
> | iotRecommendations | Nee | Nee |
> | iotRecommendationTypes | Nee | Nee |
> | iotSecuritySolutions | Ja | Ja |
> | iotSecuritySolutions /analyticsModels | Nee | Nee |
> | iotSecuritySolutions / analyticsModels / aggregatedAlerts | Nee | Nee |
> | iotSecuritySolutions / analyticsModels / aggregatedRecommendations | Nee | Nee |
> | iotSecuritySolutions / iotAlerts | Nee | Nee |
> | iotSecuritySolutions / iotAlertTypes | Nee | Nee |
> | iotSecuritySolutions / iotRecommendations | Nee | Nee |
> | iotSecuritySolutions / iotRecommendationTypes | Nee | Nee |
> | iotSensors | Nee | Nee |
> | iotSites | Nee | Nee |
> | jitNetworkAccessPolicies | Nee | Nee |
> | jitPolicies | Nee | Nee |
> | onPremiseIotSensors | Nee | Nee |
> | policies | Nee | Nee |
> | prijzen | Nee | Nee |
> | regulatoryComplianceStandards | Nee | Nee |
> | regulatoryComplianceStandards /regulatoryComplianceControls | Nee | Nee |
> | regulatoryComplianceStandards /regulatoryComplianceControls/regulatoryComplianceAssessments | Nee | Nee |
> | secureScoreControlDefinitions | Nee | Nee |
> | secureScoreControls | Nee | Nee |
> | secureScores | Nee | Nee |
> | secureScores/secureScoreControls | Nee | Nee |
> | securityContacts | Nee | Nee |
> | securitySolutions | Nee | Nee |
> | securitySolutionsReferenceData | Nee | Nee |
> | securityStatuses | Nee | Nee |
> | securityStatusesSummaries | Nee | Nee |
> | serverVulnerabilityAssessments | Nee | Nee |
> | instellingen | Nee | Nee |
> | sqlVulnerabilityAssessments | Nee | Nee |
> | subbeoordelingen | Nee | Nee |
> | taken | Nee | Nee |
> | Topologieën | Nee | Nee |
> | workspaceSettings | Nee | Nee |

## <a name="microsoftsecuritygraph"></a>Microsoft.SecurityGraph

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | diagnosticSettings | Nee | Nee |
> | diagnosticSettingsCategories | Nee | Nee |

## <a name="microsoftsecurityinsights"></a>Microsoft.SecurityInsights

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | aggregaties | Nee | Nee |
> | alertRules | Nee | Nee |
> | alertRuleTemplates | Nee | Nee |
> | automationRules | Nee | Nee |
> | bladwijzers | Nee | Nee |
> | Gevallen | Nee | Nee |
> | dataConnectors | Nee | Nee |
> | dataConnectorsCheckRequirements | Nee | Nee |
> | Verrijking | Nee | Nee |
> | entiteiten | Nee | Nee |
> | entityQueries | Nee | Nee |
> | entityQueryTemplates | Nee | Nee |
> | incidenten | Nee | Nee |
> | officeConsents | Nee | Nee |
> | instellingen | Nee | Nee |
> | threatIntelligence | Nee | Nee |
> | watchlists | Nee | Nee |

## <a name="microsoftserialconsole"></a>Microsoft.SerialConsole

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | consoleServices | Nee | Nee |
> | serialPorts | Nee | Nee |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | Naamruimten | Ja | Ja |
> | naamruimten/authorizationrules | Nee | Nee |
> | naamruimten /disasterrecoveryconfigs | Nee | Nee |
> | naamruimten /eventgridfilters | Nee | Nee |
> | naamruimten/networkrulesets | Nee | Nee |
> | naamruimten /privateEndpointConnections | Nee | Nee |
> | naamruimten/wachtrijen | Nee | Nee |
> | naamruimten/wachtrijen/authorizationrules | Nee | Nee |
> | naamruimten/onderwerpen | Nee | Nee |
> | naamruimten / onderwerpen / authorizationrules | Nee | Nee |
> | naamruimten/onderwerpen/abonnementen | Nee | Nee |
> | naamruimten / onderwerpen / abonnementen / regels | Nee | Nee |
> | premiumMessagingRegions | Nee | Nee |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | toepassingen | Ja | Ja |
> | Clusters | Ja | Ja |
> | clusters/toepassingen | Nee | Nee |
> | containerGroups | Ja | Ja |
> | containerGroupSets | Ja | Ja |
> | edgeclusters | Ja | Ja |
> | edgeclusters/toepassingen | Nee | Nee |
> | managedclusters | Ja | Ja |
> | managedclusters/toepassingen | Nee | Nee |
> | managedclusters / toepassingen / services | Nee | Nee |
> | managedclusters/applicationTypes | Nee | Nee |
> | managedclusters /applicationTypes/versions | Nee | Nee |
> | managedclusters/nodetypes | Nee | Nee |
> | Netwerken | Ja | Ja |
> | secretstores | Ja | Ja |
> | secretstores /certificates | Nee | Nee |
> | secretstores /secrets | Nee | Nee |
> | volumes | Ja | Ja |

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | toepassingen | Ja | Ja |
> | containerGroups | Ja | Ja |
> | Gateways | Ja | Ja |
> | Netwerken | Ja | Ja |
> | geheimen | Ja | Ja |
> | volumes | Ja | Ja |

## <a name="microsoftservicelinker"></a>Microsoft.ServiceLinker

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | linkers | Nee | Nee |

## <a name="microsoftservices"></a>Microsoft.Services

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | providerRegistrations | Nee | Nee |
> | providerRegistrations /resourceTypeRegistrations | Nee | Nee |
> | implementaties | Ja | Ja |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | SignalR | Ja | Ja |
> | SignalR/eventGridFilters | Nee | Nee |
> | WebPubSub | Ja | Ja |

## <a name="microsoftsingularity"></a>Microsoft.Singularity

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | accounts | Ja | Ja |
> | accounts /accountQuotaPolicies | Nee | Nee |
> | accounts /groupPolicies | Nee | Nee |
> | accounts/taken | Nee | Nee |
> | accounts /storageContainers | Nee | Nee |
> | images | Nee | Nee |

## <a name="microsoftsoftwareplan"></a>Microsoft.SoftwarePlan

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | hybridUseBenefits | Nee | Nee |

## <a name="microsoftsolutions"></a>Microsoft.Solutions

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | applicationDefinitions | Ja | Ja |
> | toepassingen | Ja | Ja |
> | jitRequests | Ja | Ja |


## <a name="microsoftsql"></a>Microsoft.SQL

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | longtermRetentionManagedInstance / longtermRetentionDatabase / longtermRetentionBackup | Nee | Nee |
> | longtermRetentionServer / longtermRetentionDatabase / longtermRetentionBackup | Nee | Nee |
> | managedInstances | Ja | Ja |
> | managedInstances /databases | Nee | Nee |
> | managedInstances / databases / backupShortTermRetentionPolicies | Nee | Nee |
> | managedInstances / databases / schema's / tabellen / kolommen / sensitivityLabels | Nee | Nee |
> | managedInstances / databases / vulnerabilityAssessments | Nee | Nee |
> | managedInstances / databases / vulnerabilityAssessments / regels / basislijnen | Nee | Nee |
> | managedInstances /encryptionProtector | Nee | Nee |
> | managedInstances /keys | Nee | Nee |
> | managedInstances / restorableDroppedDatabases / backupShortTermRetentionPolicies | Nee | Nee |
> | managedInstances/vulnerabilityAssessments | Nee | Nee |
> | Servers | Ja | Ja |
> | servers/beheerders | Nee | Nee |
> | servers/communicationLinks | Nee | Nee |
> | servers/databases | Ja (zie [opmerking hieronder](#sqlnote)) | Yes |
> | servers /encryptionProtector | Nee | Nee |
> | servers/firewallRules | Nee | Nee |
> | servers/sleutels | Nee | Nee |
> | servers /restorableDroppedDatabases | Nee | Nee |
> | servers/serviceobjectives | Nee | Nee |
> | servers /tdeCertificates | Nee | Nee |
> | virtualClusters | Ja | Ja |

<a id="sqlnote"></a>

> [!NOTE]
> De hoofddatabase biedt geen ondersteuning voor tags, maar andere databases, Azure Synapse Analytics databases, ondersteunen tags. Azure Synapse Analytics databases moeten de status Actief (niet Onderbroken) hebben.

## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | SqlVirtualMachineGroups | Ja | Ja |
> | SqlVirtualMachineGroups/AvailabilityGroupListeners | Nee | Nee |
> | SqlVirtualMachines | Ja | Ja |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | deletedAccounts | Nee | Nee |
> | storageAccounts | Ja | Ja |
> | storageAccounts/blobServices | Nee | Nee |
> | storageAccounts/fileServices | Nee | Nee |
> | storageAccounts/queueServices | Nee | Nee |
> | storageAccounts/services | Nee | Nee |
> | storageAccounts / services / metricDefinitions | Nee | Nee |
> | storageAccounts/tableServices | Nee | Nee |
> | gebruik | Nee | Nee |

## <a name="microsoftstoragecache"></a>Microsoft.StorageCache

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | amlFilesystems | Ja | Ja |
> | Caches | Ja | Ja |
> | caches/storageTargets | Nee | Nee |
> | usageModels | Nee | Nee |

## <a name="microsoftstoragereplication"></a>Microsoft.StorageReplication

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | replicationGroups | Nee | Nee |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | storageSyncServices | Ja | Ja |
> | storageSyncServices/registeredServers | Nee | Nee |
> | storageSyncServices/syncGroups | Nee | Nee |
> | storageSyncServices / syncGroups / cloudEndpoints | Nee | Nee |
> | storageSyncServices / syncGroups / serverEndpoints | Nee | Nee |
> | storageSyncServices/workflows | Nee | Nee |

## <a name="microsoftstoragesyncdev"></a>Microsoft.StorageSyncDev

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | storageSyncServices | Ja | Ja |
> | storageSyncServices/registeredServers | Nee | Nee |
> | storageSyncServices/syncGroups | Nee | Nee |
> | storageSyncServices/syncGroups/cloudEndpoints | Nee | Nee |
> | storageSyncServices/syncGroups/serverEndpoints | Nee | Nee |
> | storageSyncServices/workflows | Nee | Nee |

## <a name="microsoftstoragesyncint"></a>Microsoft.StorageSyncInt

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | storageSyncServices | Ja | Ja |
> | storageSyncServices/registeredServers | Nee | Nee |
> | storageSyncServices/syncGroups | Nee | Nee |
> | storageSyncServices/syncGroups/cloudEndpoints | Nee | Nee |
> | storageSyncServices/syncGroups/serverEndpoints | Nee | Nee |
> | storageSyncServices/workflows | Nee | Nee |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | Managers | Ja | Ja |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | Clusters | Ja | Ja |
> | clusters/privateEndpoints | Nee | Nee |
> | streamingjobs | Ja (zie opmerking hieronder) | Yes |

> [!NOTE]
> U kunt geen tag toevoegen wanneer streamingjobs wordt uitgevoerd. Stop de resource om een tag toe te voegen.

## <a name="microsoftsubscription"></a>Microsoft.Subscription

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | acceptChangeTenant | Nee | Nee |
> | acceptOwnership | Nee | Nee |
> | acceptOwnershipStatus | Nee | Nee |
> | Aliassen | Nee | Nee |
> | annuleren | Nee | Nee |
> | changeTenantRequest | Nee | Nee |
> | changeTenantStatus | Nee | Nee |
> | CreateSubscription | Nee | Nee |
> | inschakelen | Nee | Nee |
> | policies | Nee | Nee |
> | naam wijzigen | Nee | Nee |
> | SubscriptionDefinitions | Nee | Nee |
> | SubscriptionOperations | Nee | Nee |
> | Abonnementen | Nee | Nee |

## <a name="microsoftsynapse"></a>Microsoft.Synapse

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | privateLinkHubs | Ja | Ja |
> | workspaces | Ja | Ja |
> | werkruimten /bigDataPools | Ja | Ja |
> | werkruimten /operationStatuses | Nee | Nee |
> | werkruimten / sqlDatabases | Ja | Ja |
> | werkruimten /sqlPools | Ja | Ja |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | Omgevingen | Ja | Nee |
> | omgevingen /accessPolicies | Nee | Nee |
> | omgevingen /eventsources | Ja | Nee |
> | omgevingen / privateEndpointConnectionProxies | Nee | Nee |
> | omgevingen / privateEndpointConnections | Nee | Nee |
> | omgevingen / privateLinkResources | Nee | Nee |
> | omgevingen / referenceDataSets | Ja | Nee |

## <a name="microsofttoken"></a>Microsoft.Token

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | Winkels | Ja | Ja |
> | stores/accessPolicies | Nee | Nee |
> | stores/services | Nee | Nee |
> | winkels / services / tokens | Nee | Nee |

## <a name="microsoftvirtualmachineimages"></a>Microsoft.VirtualMachineImages

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | imageTemplates | Ja | Ja |
> | imageTemplates /runOutputs | Nee | Nee |

## <a name="microsoftvmware"></a>Microsoft.VMware

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | ArcZones | Ja | Ja |
> | ResourcePools | Ja | Ja |
> | VCenters | Ja | Ja |
> | VCenters/InventoryItems | Nee | Nee |
> | virtualmachines | Ja | Ja |
> | VirtualMachineTemplates | Ja | Ja |
> | VirtualNetworks | Ja | Ja |

## <a name="microsoftvmwarecloudsimple"></a>Microsoft.VMwareCloudSimple

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | dedicatedCloudNodes | Ja | Ja |
> | dedicatedCloudServices | Ja | Ja |
> | virtualMachines | Ja | Ja |

## <a name="microsoftvnfmanager"></a>Microsoft.VnfManager

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | devices | Ja | Ja |
> | registeredSubscriptions | Nee | Nee |
> | Leveranciers | Nee | Nee |
> | leveranciers/SKU's | Nee | Nee |
> | vendors /vnfs | Nee | Nee |
> | virtualNetworkFunctionSkus | Nee | Nee |
> | vnfs | Ja | Ja |

## <a name="microsoftvsonline"></a>Microsoft.VSOnline

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | accounts | Ja | Nee |
> | Plannen | Ja | Nee |
> | registeredSubscriptions | Nee | Nee |

## <a name="microsoftweb"></a>Microsoft.Web

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | apiManagementAccounts | Nee | Nee |
> | apiManagementAccounts / apiAcls | Nee | Nee |
> | apiManagementAccounts/apis | Nee | Nee |
> | apiManagementAccounts / apis / apiAcls | Nee | Nee |
> | apiManagementAccounts / apis / connectionAcls | Nee | Nee |
> | apiManagementAccounts / apis / verbindingen | Nee | Nee |
> | apiManagementAccounts / apis / connections / connectionAcls | Nee | Nee |
> | apiManagementAccounts / apis / localizedDefinitions | Nee | Nee |
> | apiManagementAccounts/connectionAcls | Nee | Nee |
> | apiManagementAccounts /connections | Nee | Nee |
> | billingMeters | Nee | Nee |
> | certificaten | Ja | Ja |
> | connectionGateways | Ja | Ja |
> | Verbindingen | Ja | Ja |
> | customApis | Ja | Ja |
> | deletedSites | Nee | Nee |
> | functionAppStacks | Nee | Nee |
> | generateGithubAccessTokenForAppserviceCLI | Nee | Nee |
> | hostingEnvironments | Ja | Ja |
> | hostingEnvironments / eventGridFilters | Nee | Nee |
> | hostingEnvironments / multiRolePools | Nee | Nee |
> | hostingEnvironments /workerPools | Nee | Nee |
> | kubeEnvironments | Ja | Ja |
> | publishingUsers | Nee | Nee |
> | aanbevelingen | Nee | Nee |
> | resourceHealthMetadata | Nee | Nee |
> | runtimes | Nee | Nee |
> | serverFarms | Ja | Ja |
> | serverFarms/eventGridFilters | Nee | Nee |
> | serverFarms/firstPartyApps | Nee | Nee |
> | serverFarms / firstPartyApps / keyVaultSettings | Nee | Nee |
> | sites | Ja | Ja |
> | sites /config  | Nee | Nee |
> | sites / eventGridFilters | Nee | Nee |
> | sites /hostNameBindings | Nee | Nee |
> | sites / networkConfig | Nee | Nee |
> | sites /premieraddons | Ja | Ja |
> | sites/sites | Ja | Ja |
> | sites / sites / eventGridFilters | Nee | Nee |
> | sites/ sites / hostNameBindings | Nee | Nee |
> | sites/ sites / networkConfig | Nee | Nee |
> | sourceControls | Nee | Nee |
> | staticSites | Ja | Ja |
> | Valideren | Nee | Nee |
> | verifyHostingEnvironmentVnet | Nee | Nee |
> | webAppStacks | Nee | Nee |

## <a name="microsoftwindowsdefenderatp"></a>Microsoft.WindowsDefenderATP

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | diagnosticSettings | Nee | Nee |
> | diagnosticSettingsCategories | Nee | Nee |

## <a name="microsoftwindowsesu"></a>Microsoft.WindowsESU

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | multipleActivationKeys | Ja | Ja |

## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | DeviceServices | Ja | Ja |

## <a name="microsoftworkloadbuilder"></a>Microsoft.WorkloadBuilder

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | migrationAgents | Ja | Ja |
> | Werkbelasting | Ja | Ja |
> | workloads/exemplaren | Nee | Nee |
> | workloads/versies | Nee | Nee |
> | workloads/versies/artefacten | Nee | Nee |

## <a name="microsoftworkloadmonitor"></a>Microsoft.WorkloadMonitor

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | Monitoren | Nee | Nee |

## <a name="next-steps"></a>Volgende stappen

Zie Tags gebruiken om uw Azure-resources te organiseren voor meer informatie over het toepassen van [tags op resources.](tag-resources.md)
