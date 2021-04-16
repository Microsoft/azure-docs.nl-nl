---
title: Modus voor volledige verwijdering
description: Laat zien hoe resourcetypen het verwijderen van de volledige modus in Azure Resource Manager verwerken.
ms.topic: conceptual
ms.date: 04/16/2021
ms.openlocfilehash: 6986f600274beaaa67f2f6ce64cbc3d0ceaf322e
ms.sourcegitcommit: d3bcd46f71f578ca2fd8ed94c3cdabe1c1e0302d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107576057"
---
# <a name="deletion-of-azure-resources-for-complete-mode-deployments"></a>Verwijdering van Azure-resources voor implementaties in de volledige modus

In dit artikel wordt beschreven hoe resourcetypen het verwijderen verwerken wanneer ze zich niet in een sjabloon die in de volledige modus is geïmplementeerd, verwerken.

De resourcetypen die zijn gemarkeerd **met Ja,** worden verwijderd wanneer het type zich niet in de sjabloon die is geïmplementeerd met de volledige modus.

De resourcetypen die zijn **gemarkeerd met Nee,** worden niet automatisch verwijderd wanneer ze niet in de sjabloon staan; Ze worden echter verwijderd als de bovenliggende resource wordt verwijderd. Zie implementatiemodi voor een volledige beschrijving [van Azure Resource Manager gedrag.](deployment-modes.md)

Als u implementeert naar [meer dan één resourcegroep in](./deploy-to-resource-group.md)een sjabloon, komen resources in de resourcegroep die is opgegeven in de implementatiebewerking in aanmerking om te worden verwijderd. Resources in de secundaire resourcegroepen worden niet verwijderd.

De resources worden vermeld op naamruimte van de resourceprovider. Zie Resourceproviders voor Azure-services als u de naamruimte van een resourceprovider wilt vergelijken met de naam van [de Azure-service.](../management/azure-services-resource-providers.md)

> [!NOTE]
> Gebruik altijd de [what-if-bewerking voordat](template-deploy-what-if.md) u een sjabloon in de volledige modus implementeert. What-if laat zien welke resources worden gemaakt, verwijderd of gewijzigd. Gebruik what-if om te voorkomen dat resources per ongeluk worden verwijderd.

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
> - [Microsoft.Hier](#microsoftfalcon)
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
> - [Microsoft.Wordt](#microsofthydra)
> - [Microsoft.ImportExport](#microsoftimportexport)
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
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | DomainServices | Yes |
> | DomainServices/oucontainer | No |

## <a name="microsoftaddons"></a>Microsoft.Addons

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | supportProviders | No |

## <a name="microsoftadhybridhealthservice"></a>Microsoft.ADHybridHealthService

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | aadsupportcases | No |
> | addsservices | No |
> | Agenten | No |
> | anonymousapiusers | No |
> | configuratie | No |
> | logboeken | No |
> | rapporten | No |
> | servicehealthmetrics | No |
> | services | No |

## <a name="microsoftadvisor"></a>Microsoft.Advisor

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | advisorScore | No |
> | Configuraties | No |
> | generateRecommendations | No |
> | metagegevens | No |
> | aanbevelingen | No |
> | onderdrukkingen | No |

## <a name="microsoftagfoodplatform"></a>Microsoft.AgPlatform

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | farmBeats | Yes |
> | farmBeats /eventGridFilters | No |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | actionRules | Yes |
> | waarschuwingen | No |
> | alertsList | No |
> | alertsMetaData | No |
> | alertsSummary | No |
> | alertsSummaryList | No |
> | migrateFromSmartDetection | No |
> | resourceHealthAlertRules | Yes |
> | smartDetectorAlertRules | Yes |
> | smartGroups | No |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | Servers | Yes |

## <a name="microsoftanybuild"></a>Microsoft.AnyBuild

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | Clusters | Yes |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | deletedServices | No |
> | getDomainOwnershipIdentifier | No |
> | reportFeedback | No |
> | service | Yes |
> | validateServiceName | No |

## <a name="microsoftappassessment"></a>Microsoft.AppAssessment

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | migrateProjects | Yes |
> | migrateProjects/assessments | No |
> | migrateProjects/assessments/assessedApplications | No |
> | migrateProjects/assessments/assessedApplications /machines | No |
> | migrateProjects/assessments/assessedMachines | No |
> | migrateProjects/assessments /assessedMachines/applications | No |
> | migrateProjects/assessments /machinesToAssess | No |
> | migrateProjects/sites | No |
> | migrateProjects /sites/applianceConfigurations | No |
> | migrateProjects /sites/machines | No |
> | osVersions | No |

## <a name="microsoftappconfiguration"></a>Microsoft.AppConfiguration

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | configurationStores | Yes |
> | configurationStores/eventGridFilters | No |
> | configurationStores/keyValues | No |

## <a name="microsoftappplatform"></a>Microsoft.AppPlatform

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | Spring | Yes |
> | Spring/apps | No |
> | Spring/apps/implementaties | No |

## <a name="microsoftattestation"></a>Microsoft.Attestation

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | attestationProviders | Yes |
> | defaultProviders | No |

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | accessReviewScheduleDefinitions | No |
> | accessReviewScheduleSettings | No |
> | classicAdministrators | No |
> | dataAliases | No |
> | dataPolicyManifests | No |
> | denyAssignments | No |
> | elevateAccess | No |
> | findOrphanRoleAssignments | No |
> | Sloten | No |
> | permissions | No |
> | policyAssignments | No |
> | policyDefinitions | No |
> | policyExemptions | No |
> | policySetDefinitions | No |
> | privateLinkAssociations | No |
> | providerOperations | No |
> | resourceManagementPrivateLinks | Yes |
> | roleAssignmentApprovals | No |
> | roleAssignments | No |
> | roleAssignmentScheduleInstances | No |
> | roleAssignmentScheduleRequests | No |
> | roleAssignmentSchedules | No |
> | roleAssignmentsUsageMetrics | No |
> | roleDefinitions | No |
> | roleEligibilityScheduleInstances | No |
> | roleEligibilityScheduleRequests | No |
> | roleEligibilitySchedules | No |
> | roleManagementPolicies | No |
> | roleManagementPolicyAssignments | No |

## <a name="microsoftautomanage"></a>Microsoft.Automanage

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | accounts | Yes |
> | configurationProfileAssignments | No |
> | configurationProfilePreferences | Yes |

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | automationAccounts | Yes |
> | automationAccounts/configuraties | Yes |
> | automationAccounts/jobs | No |
> | automationAccounts/privateEndpointConnectionProxies | No |
> | automationAccounts/privateEndpointConnections | No |
> | automationAccounts/privateLinkResources | No |
> | automationAccounts/runbooks | Yes |
> | automationAccounts/softwareUpdateConfigurations | No |
> | automationAccounts/webhooks | No |

## <a name="microsoftavs"></a>Microsoft.AVS

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | privateClouds | Yes |
> | privateClouds/addons | No |
> | privateClouds/autorisaties | No |
> | privateClouds/cloudLinks | No |
> | privateClouds/clusters | No |
> | privateClouds/clusters/gegevensstores | No |
> | privateClouds/globalReachConnections | No |
> | privateClouds / hcxEnterpriseSites | No |
> | privateClouds/scriptExecutions | No |
> | privateClouds/scriptPackages | No |
> | privateClouds/scriptPackages/scriptCmdlets | No |
> | privateClouds/workloadNetworks | No |
> | privateClouds/workloadNetworks/dhcpConfigurations | No |
> | privateClouds/workloadNetworks/dnsServices | No |
> | privateClouds/workloadNetworks/dnsZones | No |
> | privateClouds/workloadNetworks/gateways | No |
> | privateClouds/workloadNetworks/portMirroringProfiles | No |
> | privateClouds/workloadNetworks/segments | No |
> | privateClouds/workloadNetworks/virtualMachines | No |
> | privateClouds/workloadNetworks/vmGroups | No |

## <a name="microsoftazuregeneva"></a>Microsoft.Azure.Genève

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | Omgevingen | No |
> | omgevingen /accounts | No |
> | omgevingen / accounts / naamruimten | No |
> | omgevingen / accounts / naamruimten / configuraties | No |

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | b2cDirectories | Yes |
> | b2ctenants | No |
> | guestUsages | Yes |

## <a name="microsoftazurearcdata"></a>Microsoft.AzureArcData

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | dataControllers | Yes |
> | dataWarehouseInstances | Yes |
> | postgresInstances | Yes |
> | sqlManagedInstances | Yes |
> | sqlServerInstances | Yes |

## <a name="microsoftazurecis"></a>Microsoft.AzureCIS

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | autopilotEnvironments | Yes |

## <a name="microsoftazuredata"></a>Microsoft.AzureData

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | sqlServerRegistrations | Yes |
> | sqlServerRegistrations / sqlServers | No |

## <a name="microsoftazuresphere"></a>Microsoft.AzureSphere

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | Catalogi | Yes |
> | catalogi/producten | Yes |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | cloudManifestFiles | No |
> | edgeSubscriptions | Yes |
> | linkedSubscriptions | Yes |
> | Registraties | Yes |
> | registraties /klantAbonnementen | No |
> | registraties/producten | No |

## <a name="microsoftazurestackhci"></a>Microsoft.AzureStackHCI

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | Clusters | Yes |
> | galleryImages | Yes |
> | networkInterfaces | Yes |
> | virtualHardDisks | Yes |
> | virtualMachines | Yes |
> | virtualNetworks | Yes |

## <a name="microsoftbaremetalinfrastructure"></a>Microsoft.BareMetalInfrastructure

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | bareMetalInstances | Yes |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | batchAccounts | Yes |
> | batchAccounts/certificaten | No |
> | batchAccounts/pools | No |

## <a name="microsoftbilling"></a>Microsoft.Billing

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | billingAccounts | No |
> | billingAccounts/overeenkomsten | No |
> | billingAccounts/billingPermissions | No |
> | billingAccounts/billingProfiles | No |
> | billingAccounts/billingProfiles/billingPermissions | No |
> | billingAccounts/billingProfiles/billingRoleAssignments | No |
> | billingAccounts/billingProfiles/billingRoleDefinitions | No |
> | billingAccounts/billingProfiles/billingSubscriptions | No |
> | billingAccounts / billingProfiles / createBillingRoleAssignment | No |
> | billingAccounts/billingProfiles /customers | No |
> | billingAccounts/billingProfiles/instructies | No |
> | billingAccounts/billingProfiles/invoices | No |
> | billingAccounts / billingProfiles / facturen / prijzenoverzicht | No |
> | billingAccounts / billingProfiles / facturen / transacties | No |
> | billingAccounts/billingProfiles/invoiceSections | No |
> | billingAccounts / billingProfiles / invoiceSections / billingPermissions | No |
> | billingAccounts / billingProfiles / invoiceSections / billingRoleAssignments | No |
> | billingAccounts / billingProfiles / invoiceSections / billingRoleDefinitions | No |
> | billingAccounts / billingProfiles / invoiceSections / billingSubscriptions | No |
> | billingAccounts / billingProfiles / invoiceSections / createBillingRoleAssignment | No |
> | billingAccounts / billingProfiles / invoiceSections / initiateTransfer | No |
> | billingAccounts / billingProfiles / invoiceSections / products | No |
> | billingAccounts / billingProfiles / invoiceSections / products / transfer | No |
> | billingAccounts / billingProfiles / invoiceSections / products / updateAutoRenew | No |
> | billingAccounts / billingProfiles / invoiceSections / transacties | No |
> | billingAccounts / billingProfiles / invoiceSections / transfers | No |
> | billingAccounts / billingProfiles / invoiceSections / validateDeleteInvoiceSectionEligibility | No |
> | billingAccounts/BillingProfiles/patchOperations | No |
> | billingAccounts/billingProfiles/paymentMethods | No |
> | billingAccounts/billingProfiles/policies | No |
> | billingAccounts/billingProfiles/pricesheet | No |
> | billingAccounts / billingProfiles / pricesheetDownloadOperations | No |
> | billingAccounts/billingProfiles/products | No |
> | billingAccounts/billingProfiles/reservations | No |
> | billingAccounts/billingProfiles/transactions | No |
> | billingAccounts/billingProfiles /validateDeleteBillingProfileEligibility | No |
> | billingAccounts/billingProfiles/validateDetachPaymentMethodEligibility | No |
> | billingAccounts/billingRoleAssignments | No |
> | billingAccounts/billingRoleDefinitions | No |
> | billingAccounts/billingSubscriptions | No |
> | billingAccounts/billingSubscriptions /elevateRole | No |
> | billingAccounts/billingAbonnementen/facturen | No |
> | billingAccounts /createBillingRoleAssignment | No |
> | billingAccounts/createInvoiceSectionOperations | No |
> | billingAccounts/klanten | No |
> | billingAccounts /customers/billingPermissions | No |
> | billingAccounts /customers/billingSubscriptions | No |
> | billingAccounts /customers /initiateTransfer | No |
> | billingAccounts / klanten / beleid | No |
> | billingAccounts/klanten/producten | No |
> | billingAccounts / klanten / transacties | No |
> | billingAccounts / klanten / overdrachten | No |
> | billingAccounts/afdelingen | No |
> | billingAccounts/afdelingen/billingPermissions | No |
> | billingAccounts/afdelingen/billingRoleAssignments | No |
> | billingAccounts / afdelingen / billingRoleDefinitions | No |
> | billingAccounts/afdelingen/factureringAbonnementen | No |
> | billingAccounts/enrollmentAccounts | No |
> | billingAccounts/enrollmentAccounts/billingPermissions | No |
> | billingAccounts/enrollmentAccounts/billingRoleAssignments | No |
> | billingAccounts/enrollmentAccounts/billingRoleDefinitions | No |
> | billingAccounts/enrollmentAccounts/billingSubscriptions | No |
> | billingAccounts/facturen | No |
> | billingAccounts/facturen/transacties | No |
> | billingAccounts/facturen/transactionSummary | No |
> | billingAccounts/invoiceSections | No |
> | billingAccounts/invoiceSections / billingSubscriptionMoveOperations | No |
> | billingAccounts/invoiceSections/billingSubscriptions | No |
> | billingAccounts/ invoiceSections / billingSubscriptions / transfer | No |
> | billingAccounts/invoiceSections/elevate | No |
> | billingAccounts/invoiceSections /initiateTransfer | No |
> | billingAccounts/invoiceSections/patchOperations | No |
> | billingAccounts/invoiceSections / productMoveOperations | No |
> | billingAccounts/invoiceSections /products | No |
> | billingAccounts/invoiceSections / products / transfer | No |
> | billingAccounts/invoiceSections / products / updateAutoRenew | No |
> | billingAccounts/invoiceSections/transactions | No |
> | billingAccounts/invoiceSections/transfers | No |
> | billingAccounts/lineOfCredit | No |
> | billingAccounts/patchOperations | No |
> | billingAccounts/crediteurenOverage | No |
> | billingAccounts/paymentMethods | No |
> | billingAccounts/payNow | No |
> | billingAccounts/producten | No |
> | billingAccounts/reserveringen | No |
> | billingAccounts/transacties | No |
> | billingPeriods | No |
> | billingPermissions | No |
> | billingProperty | No |
> | billingRoleAssignments | No |
> | billingRoleDefinitions | No |
> | createBillingRoleAssignment | No |
> | Afdelingen | No |
> | enrollmentAccounts | No |
> | Facturen | No |
> | Promoties | No |
> | Transfers | No |
> | transfers/acceptTransfer | No |
> | transfers/declineTransfer | No |
> | transfers /operationStatus | No |
> | transfers/validateTransfer | No |
> | validateAddress | No |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | mapApis | Yes |
> | updateCommunicationPreference | No |

## <a name="microsoftblockchain"></a>Microsoft.Blockchain

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | blockchainMembers | Yes |
> | cordaMembers | Yes |
> | Watchers | Yes |

## <a name="microsoftblockchaintokens"></a>Microsoft.BlockchainTokens

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | TokenServices | Yes |
> | TokenServices/BlockchainNetworks | No |
> | TokenServices/groepen | No |
> | TokenServices / groepen / accounts | No |
> | TokenServices/TokenTemplates | No |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | blueprintAssignments | No |
> | blueprintAssignments /assignmentOperations | No |
> | blueprintAssignments /operations | No |
> | Blauwdrukken | No |
> | blauwdrukken/artefacten | No |
> | blauwdrukken/versies | No |
> | blauwdrukken/versies/artefacten | No |

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | botServices | Yes |
> | botServices/kanalen | No |
> | botServices/verbindingen | No |
> | hostSettings | No |
> | talen | No |
> | sjablonen | No |

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | Redis | Yes |
> | Redis/EventGridFilters | No |
> | Redis /privateEndpointConnectionProxies | No |
> | Redis / privateEndpointConnectionProxies / valideren | No |
> | Redis/privateEndpointConnections | No |
> | Redis/privateLinkResources | No |
> | redisEnterprise | Yes |
> | redisEnterprise /databases | No |
> | RedisEnterprise / privateEndpointConnectionProxies | No |
> | RedisEnterprise / privateEndpointConnectionProxies / valideren | No |
> | RedisEnterprise /privateEndpointConnections | No |
> | RedisEnterprise /privateLinkResources | No |

## <a name="microsoftcapacity"></a>Microsoft.Capacity

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | appliedReservations | No |
> | autoQuotaIncrease | No |
> | calculateExchange | No |
> | calculatePrice | No |
> | calculatePurchasePrice | No |
> | Catalogi | No |
> | commercialReservationOrders | No |
> | Exchange | No |
> | ownReservations | No |
> | placePurchaseOrder | No |
> | reservationOrders | No |
> | reservationOrders/calculateRefund | No |
> | reservationOrders / samenvoegen | No |
> | reservationOrders/reserveringen | No |
> | reservationOrders/reserveringen/revisies | No |
> | reservationOrders/return | No |
> | reservationOrders/split | No |
> | reserveringOrders/wisselen | No |
> | Reserveringen | No |
> | resourceProviders | No |
> | resources | No |
> | validateReservationOrder | No |

## <a name="microsoftcascade"></a>Microsoft.Cascade

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | sites | Yes |

## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | CdnWebApplicationFirewallManagedRuleSets | No |
> | CdnWebApplicationFirewallPolicies | Yes |
> | edgenodes | No |
> | Profielen | Yes |
> | profielen /afdendpoints | Yes |
> | profielen / afdendpoints / routes | No |
> | profielen/aangepastedomeinen | No |
> | profielen/eindpunten | Yes |
> | profielen / eindpunten / aangepastedomeinen | No |
> | profielen / eindpunten / origingroups | No |
> | profielen / eindpunten / oorsprongen | No |
> | profielen/origingroups | No |
> | profielen/ origingroups/origins | No |
> | profielen/regelsets | No |
> | profielen / regelsets / regels | No |
> | profielen/geheimen | No |
> | profielen/beveiligingsbeleid | No |
> | validateProbe | No |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | certificateOrders | Yes |
> | certificateOrders/certificates | No |
> | validateCertificateRegistrationInformation | No |

## <a name="microsoftchangeanalysis"></a>Microsoft.ChangeAnalysis

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | Wijzigingen | No |
> | profiel | No |
> | resourceChanges | No |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | mogelijkheden | No |
> | Domeinnamen | Yes |
> | domainNames /capabilities | No |
> | domainNames /internalLoadBalancers | No |
> | domainNames/serviceCertificates | No |
> | domainNames/slots | No |
> | domainNames/slots/roles | No |
> | domainNames / slots / roles / metricDefinitions | No |
> | domainNames / slots / rollen / metrische gegevens | No |
> | moveSubscriptionResources | No |
> | operatingSystemFamilies | No |
> | operatingSystems | No |
> | quotas | No |
> | resourceTypes | No |
> | validateSubscriptionMoveAvailability | No |
> | virtualMachines | Yes |
> | virtualMachines/diagnosticSettings | No |
> | virtualMachines/metricDefinitions | No |
> | virtualMachines/metrische gegevens | No |

## <a name="microsoftclassicinfrastructuremigrate"></a>Microsoft.ClassicInfrastructureMigrate

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | classicInfrastructureResources | No |

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | mogelijkheden | No |
> | expressRouteCrossConnections | No |
> | expressRouteCrossConnections/peerings | No |
> | gatewaySupportedDevices | No |
> | networkSecurityGroups | Yes |
> | quotas | No |
> | reservedIps | Yes |
> | virtualNetworks | Yes |
> | virtualNetworks / remoteVirtualNetworkPeeringProxies | No |
> | virtualNetworks/virtualNetworkPeerings | No |

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | mogelijkheden | No |
> | Schijven | No |
> | images | No |
> | osImages | No |
> | osPlatformImages | No |
> | publicImages | No |
> | quotas | No |
> | storageAccounts | Yes |
> | storageAccounts/blobServices | No |
> | storageAccounts/fileServices | No |
> | storageAccounts/metricDefinitions | No |
> | storageAccounts/metrische gegevens | No |
> | storageAccounts/queueServices | No |
> | storageAccounts/services | No |
> | storageAccounts / services / diagnosticSettings | No |
> | storageAccounts / services / metricDefinitions | No |
> | storageAccounts / services / metrische gegevens | No |
> | storageAccounts/tableServices | No |
> | storageAccounts/vmImages | No |
> | vmImages | No |

## <a name="microsoftclusterstor"></a>Microsoft.ClusterStor

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | Knooppunten | Yes |

## <a name="microsoftcodespaces"></a>Microsoft.Codespaces

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | Plannen | Yes |
> | registeredSubscriptions | No |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | accounts | Yes |
> | accounts / privateEndpointConnectionProxies | No |
> | accounts / privateEndpointConnections | No |
> | accounts / privateLinkResources | No |

## <a name="microsoftcommerce"></a>Microsoft.Commerce

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | RateCard | No |
> | UsageAggregates | No |

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | availabilitySets | Yes |
> | cloudServices | Yes |
> | cloudServices/networkInterfaces | No |
> | cloudServices/publicIPAddresses | No |
> | cloudServices/roleInstances | No |
> | cloudServices / roleInstances / networkInterfaces | No |
> | cloudServices/rollen | No |
> | diskAccccss | Yes |
> | diskEncryptionSets | Yes |
> | Schijven | Yes |
> | Galeries | Yes |
> | galerieën/toepassingen | No |
> | galerieën/toepassingen/versies | No |
> | galerieën/afbeeldingen | Yes |
> | galerieën / afbeeldingen / versies | Yes |
> | hostGroups | Yes |
> | hostGroepen/hosts | Yes |
> | images | Yes |
> | proximityPlacementGroups | Yes |
> | restorePointCollections | Yes |
> | restorePointCollections/restorePoints | No |
> | sharedVMExtensions | Yes |
> | sharedVMExtensions /versions | No |
> | sharedVMImages | Yes |
> | sharedVMImages /versions | No |
> | momentopnamen | Yes |
> | sshPublicKeys | Yes |
> | virtualMachines | Yes |
> | virtualMachines/extensies | Yes |
> | virtualMachines/metricDefinitions | No |
> | virtualMachines/runCommands | Yes |
> | virtualMachineScaleSets | Yes |
> | virtualMachineScaleSets/extensions | No |
> | virtualMachineScaleSets/networkInterfaces | No |
> | virtualMachineScaleSets/ publicIPAddresses | No |
> | virtualMachineScaleSets /virtualMachines | No |
> | virtualMachineScaleSets / virtualMachines / networkInterfaces | No |

## <a name="microsoftconnectedcache"></a>Microsoft.ConnectedCache

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | CacheNodes | Yes |

## <a name="microsoftconnectedvehicle"></a>Microsoft.ConnectedVehicle

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | platformAccounts | Yes |
> | registeredSubscriptions | No |

## <a name="microsoftconnectedvmwarevsphere"></a>Microsoft.ConnectedVMwarevSphere

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | ResourcePools | Yes |
> | VCenters | Yes |
> | VCenters/InventoryItems | No |
> | VirtualMachines | Yes |
> | VirtualMachines /Extensions | Yes |
> | VirtualMachines/GuestAgents | No |
> | VirtualMachines /HybridIdentityMetadata | No |
> | VirtualMachineTemplates | Yes |
> | VirtualNetworks | Yes |

## <a name="microsoftconsumption"></a>Microsoft.Consumption

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | AggregatedCost | No |
> | Tegoeden | No |
> | Budgetten | No |
> | Kosten | No |
> | CostTags | No |
> | Credits | No |
> | events | No |
> | Prognoses | No |
> | Veel | No |
> | Markten | No |
> | Prijzenoverzichten | No |
> | Producten | No |
> | ReservationDetails | No |
> | ReservationRecommendationDetails | No |
> | ReservationRecommendations | No |
> | ReserveringSummaries | No |
> | ReservationTransactions | Nee |
> | Tags | Nee |
> | Huurders | No |
> | Termen | No |
> | UsageDetails | No |

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | containerGroups | Yes |
> | serviceAssociationLinks | No |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | Registers | Yes |
> | registers/agentPools | Yes |
> | registers/builds | No |
> | registers / builds / annuleren | No |
> | registers / builds / getLogLink | No |
> | registers /buildTasks | Yes |
> | registers / buildTasks / stappen | No |
> | registers /connectedRegistries | No |
> | registers /connectedRegistries /deactiveren | No |
> | registers / eventGridFilters | No |
> | registers /exportPipelines | No |
> | registers /generateCredentials | No |
> | registers / getBuildSourceUploadUrl | No |
> | registers / GetCredentials | No |
> | registers /importImage | No |
> | registers /importPipelines | No |
> | registers/pipelineRuns | No |
> | registers / privateEndpointConnectionProxies | No |
> | registers / privateEndpointConnectionProxies / valideren | No |
> | registers /privateEndpointConnections | No |
> | registers /privateLinkResources | No |
> | registers/queueBuild | No |
> | registers / regenerateCredential | No |
> | registers /regenerateCredentials | No |
> | registers/replicaties | Yes |
> | registers /wordt uitgevoerd | No |
> | registers / wordt uitgevoerd / annuleren | No |
> | registers /scheduleRun | No |
> | registers/scopeMaps | No |
> | registers/taskRuns | No |
> | registers/taken | Yes |
> | registers/tokens | No |
> | registers/updatePolicies | No |
> | registers/webhooks | Yes |
> | registers / webhooks / getCallbackConfig | No |
> | registers/webhooks/ping | No |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | containerServices | Yes |
> | managedClusters | Yes |
> | ManagedClusters/eventGridFilters | No |
> | openShiftManagedClusters | Yes |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | Waarschuwingen | No |
> | BillingAccounts | No |
> | Budgetten | No |
> | CloudConnectors | No |
> | Connectors | Yes |
> | costAllocationRules | No |
> | Afdelingen | No |
> | Dimensies | No |
> | EnrollmentAccounts | No |
> | Exports | No |
> | ExternalBillingAccounts | No |
> | ExternalBillingAccounts/Waarschuwingen | No |
> | ExternalBillingAccounts/Dimensies | No |
> | ExternalBillingAccounts/Forecast | No |
> | ExternalBillingAccounts/query | No |
> | ExternalSubscriptions | No |
> | ExternalSubscriptions /Alerts | No |
> | ExternalSubscriptions /Dimensions | No |
> | ExternalSubscriptions /Forecast | No |
> | ExternalSubscriptions /Query | No |
> | fetchPrices | No |
> | Prognose | No |
> | GenerateDetailedCostReport | No |
> | GenerateReservationDetailsReport | No |
> | Inzichten | No |
> | Query | No |
> | registreren | No |
> | Reportconfigs | No |
> | Rapporten | No |
> | ScheduledActions | No |
> | Instellingen | No |
> | showbackRules | No |
> | Weergaven | No |

## <a name="microsoftcustomerlockbox"></a>Microsoft.CustomerLockbox

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | DisableLockbox | No |
> | EnableLockbox | No |
> | requests | No |
> | TenantOptedIn | No |

## <a name="microsoftcustomproviders"></a>Microsoft.CustomProviders

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | Verenigingen | No |
> | resourceProviders | Yes |

## <a name="microsoftd365customerinsights"></a>Microsoft.D365CustomerInsights

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | Exemplaren | Yes |

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | Banen | Yes |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | DataBoxEdgeDevices | Yes |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | workspaces | Yes |
> | werkruimten /dbWorkspaces | No |
> | werkruimten / virtualNetworkPeerings | No |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | Catalogi | Yes |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | dataFactories | Yes |
> | dataFactories/diagnosticSettings | No |
> | dataFactories/metricDefinitions | No |
> | dataFactorySchema | No |
> | Fabrieken | Yes |
> | factories/integrationRuntimes | No |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | accounts | Yes |
> | accounts /dataLakeStoreAccounts | No |
> | accounts/storageAccounts | No |
> | accounts / storageAccounts / containers | No |
> | accounts/ transferAnalyticsUnits | No |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | accounts | Yes |
> | accounts / eventGridFilters | No |
> | accounts /firewallRules | No |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | DatabaseMigrations | No |
> | services | Yes |
> | services/projecten | Yes |
> | SqlMigrationServices | Yes |

## <a name="microsoftdataprotection"></a>Microsoft.DataProtection

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | BackupVaults | Yes |
> | ResourceGuards | Yes |

## <a name="microsoftdatashare"></a>Microsoft.DataShare

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | accounts | Yes |
> | accounts/shares | No |
> | accounts / shares / gegevenssets | No |
> | accounts / shares / uitnodigingen | No |
> | accounts / shares / providersharesubscriptions | No |
> | accounts / shares / synchronizationSettings | No |
> | accounts/sharesubscriptions | No |
> | accounts / sharesubscriptions / consumerSourceDataSets | No |
> | accounts / sharesubscriptions / datasetmappings | No |
> | accounts / sharesubscriptions / triggers | No |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | Servers | Yes |
> | servers /advisors | No |
> | servers/sleutels | No |
> | servers / privateEndpointConnectionProxies | No |
> | servers /privateEndpointConnections | No |
> | servers /privateLinkResources | No |
> | servers /queryTexts | No |
> | servers /recoverableServers | No |
> | servers / resetQueryPerformanceInsightData | No |
> | servers / starten | No |
> | servers / stoppen | No |
> | servers /topQueryStatistics | No |
> | servers /virtualNetworkRules | No |
> | servers /waitStatistics | No |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | flexibleServers | Yes |
> | Servers | Yes |
> | servers /advisors | No |
> | servers/sleutels | No |
> | servers / privateEndpointConnectionProxies | No |
> | servers /privateEndpointConnections | No |
> | servers / privateLinkResources | No |
> | servers /queryTexts | No |
> | servers / recoverableServers | No |
> | servers / resetQueryPerformanceInsightData | No |
> | servers / starten | No |
> | servers / stoppen | No |
> | servers / topQueryStatistics | No |
> | servers / upgrade | No |
> | servers / virtualNetworkRules | No |
> | servers /waitStatistics | No |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | flexibleServers | Yes |
> | serverGroups | Yes |
> | serverGroupsv2 | Yes |
> | Servers | Yes |
> | servers /advisors | No |
> | servers/sleutels | No |
> | servers / privateEndpointConnectionProxies | No |
> | servers / privateEndpointConnections | No |
> | servers / privateLinkResources | No |
> | servers /queryTexts | No |
> | servers /recoverableServers | No |
> | servers / resetQueryPerformanceInsightData | No |
> | servers /topQueryStatistics | No |
> | servers /virtualNetworkRules | No |
> | servers /waitStatistics | No |
> | serversv2 | Yes |

## <a name="microsoftdeploymentmanager"></a>Microsoft.DeploymentManager

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | artifactSources | Yes |
> | implementaties | Yes |
> | serviceTopologies | Yes |
> | serviceTopologies/services | Yes |
> | serviceTopologies /services/serviceUnits | Yes |
> | stappen | Yes |

## <a name="microsoftdesktopvirtualization"></a>Microsoft.DesktopVirtualization

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | applicationgroups | Yes |
> | applicationgroups/applications | No |
> | applicationgroups/desktops | No |
> | applicationgroups/startmenuitems | No |
> | hostpools | Yes |
> | hostpools /msixpackages | No |
> | hostpools/sessionhosts | No |
> | hostpools / sessionhosts / usersessions | No |
> | hostpools /usersessions | No |
> | scalingPlans | Yes |
> | workspaces | Yes |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | ElasticPools | Yes |
> | ElasticPools/IotHubTenants | Yes |
> | ElasticPools/IotHubTenants/securitySettings | No |
> | IotHubs | Yes |
> | IotHubs /eventGridFilters | No |
> | IotHubs/securitySettings | No |
> | ProvisioningServices | Yes |
> | gebruik | No |

## <a name="microsoftdeviceupdate"></a>Microsoft.DeviceUpdate

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | accounts | Yes |
> | accounts/exemplaren | Yes |
> | registeredSubscriptions | No |

## <a name="microsoftdevops"></a>Microsoft.DevOps

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | Pijpleidingen | Yes |

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | Controllers | Yes |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | labcenters | Yes |
> | Labs | Yes |
> | labs/omgevingen | Yes |
> | labs /serviceRunners | Yes |
> | labs /virtualMachines | Yes |
> | Planningen | Yes |

## <a name="microsoftdigitaltwins"></a>Microsoft.DigitalTwins

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | digitalTwinsInstances | Yes |
> | digitalTwinsInstances /eindpunten | No |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | cassandraClusters | Yes |
> | databaseAccountNames | No |
> | databaseAccounts | Yes |
> | restorableDatabaseAccounts | No |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | Domeinen | Yes |
> | domeinen /domeinEigenaarschapIdentifiers | No |
> | generateSsoRequest | No |
> | topLevelDomains | No |
> | validateDomainRegistrationInformation | No |

## <a name="microsoftdynamicslcs"></a>Microsoft.DynamicsLcs

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | lcsprojects | No |
> | lcsprojects/clouddeployments | No |
> | lcsprojects/connectors | No |

## <a name="microsoftedgeorder"></a>Microsoft.EdgeOrder

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | Adressen | Yes |
> | orderCollections | Yes |
> | orders | Yes |
> | productFamiliesMetadata | No |

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft.EnterpriseKnowledgeGraph

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | services | Yes |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | Domeinen | Yes |
> | domeinen/onderwerpen | No |
> | eventSubscriptions | No |
> | extensionTopics | No |
> | partnerNamespaces | Yes |
> | partnerNamespaces / eventChannels | No |
> | partnerRegisters | Yes |
> | partnerTopics | Yes |
> | partnerTopics /eventSubscriptions | No |
> | systemTopics | Yes |
> | systemTopics /eventSubscriptions | No |
> | Onderwerpen | Yes |
> | topicTypes | No |

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | Clusters | Yes |
> | Naamruimten | Yes |
> | naamruimten/authorizationrules | No |
> | naamruimten /disasterrecoveryconfigs | No |
> | naamruimten/eventhubs | No |
> | naamruimten / eventhubs / authorizationrules | No |
> | naamruimten / eventhubs / consumergroups | No |
> | naamruimten/networkrulesets | No |
> | naamruimten / privateEndpointConnections | No |

## <a name="microsoftexperimentation"></a>Microsoft.Experimentation

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | experimentWorkspaces | Yes |

## <a name="microsoftfalcon"></a>Microsoft.Hier

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | Naamruimten | Yes |

## <a name="microsoftfeatures"></a>Microsoft.Features

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | featureConfigurations | No |
> | featureProviderNamespaces | No |
> | featureProviders | No |
> | features | Nee |
> | providers | No |
> | subscriptionFeatureRegistrations | No |

## <a name="microsoftgallery"></a>Microsoft.Gallery

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | Inschrijven | No |
> | galleryitems | No |
> | generateartifactaccessuri | No |
> | myareas | No |
> | myareas/areas | No |
> | myareas /areas/areas | No |
> | myareas /areas/areas /galleryitems | No |
> | myareas /areas /galleryitems | No |
> | myareas/galleryitems | No |
> | registreren | No |
> | resources | No |
> | retrieveresourcesbyid | No |

## <a name="microsoftgenomics"></a>Microsoft.Genomics

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | accounts | Yes |

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | autoManagedAccounts | Yes |
> | autoManagedVmConfigurationProfiles | Yes |
> | configurationProfileAssignments | No |
> | guestConfigurationAssignments | No |
> | Software | No |
> | softwareUpdateProfile | No |
> | softwareUpdates | No |

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | hanaInstances | Yes |
> | sapMonitors | Yes |

## <a name="microsofthardwaresecuritymodules"></a>Microsoft.HardwareSecurityModules

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | dedicatedHSMs | Yes |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | clusterPools | Yes |
> | clusterPools/clusters | Yes |
> | Clusters | Yes |
> | clusters/toepassingen | No |

## <a name="microsofthealthbot"></a>Microsoft.HealthBot

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | healthBots | Yes |

## <a name="microsofthealthcareapis"></a>Microsoft.HealthcareApis

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | services | Yes |
> | services /iomtconnectors | No |
> | services / iomtconnectors / verbindingen | No |
> | services / iomtconnectors / toewijzingen | No |
> | services / privateEndpointConnectionProxies | No |
> | services /privateEndpointConnections | No |
> | services /privateLinkResources | No |
> | workspaces | Yes |
> | werkruimten/dicomservices | Yes |

## <a name="microsofthybridcompute"></a>Microsoft.HybridCompute

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | Machines | Yes |
> | machines/assessPatches | No |
> | machines/extensies | Yes |
> | machines/installPatches | No |
> | machines/privateLinkScopes | No |
> | privateLinkScopes | Yes |
> | privateLinkScopes/privateEndpointConnectionProxies | No |
> | privateLinkScopes/privateEndpointConnections | No |

## <a name="microsofthybriddata"></a>Microsoft.HybridData

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | dataManagers | Yes |

## <a name="microsofthybridnetwork"></a>Microsoft.HybridNetwork

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | devices | Yes |
> | networkfunctions | Yes |
> | networkFunctionVendors | No |
> | registeredSubscriptions | No |
> | Leveranciers | No |
> | Leveranciers/vendorskus | No |
> | Leveranciers/vendorskus/previewsabonnementen | No |
> | virtualNetworkFunctions | Yes |
> | virtualNetworkFunctionVendors | No |

## <a name="microsofthydra"></a>Microsoft.Keer

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | Onderdelen | Yes |
> | networkScopes | Yes |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | Banen | Yes |

## <a name="microsoftintune"></a>Microsoft.Intune

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | diagnosticSettings | No |
> | diagnosticSettingsCategories | No |

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | appTemplates | No |
> | IoTApps | Yes |

## <a name="microsoftiotsecurity"></a>Microsoft.IoTSecurity

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | defenderSettings | No |

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | Graph | Yes |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | deletedManagedHSMs | No |
> | deletedVaults | No |
> | hsmPools | Yes |
> | managedHSMs | Yes |
> | Kluizen | Yes |
> | kluizen/accessPolicies | No |
> | kluizen/eventGridFilters | No |
> | kluizen/sleutels | No |
> | kluizen/sleutels/versies | No |
> | kluizen/geheimen | No |

## <a name="microsoftkubernetes"></a>Microsoft.Kubernetes

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | connectedClusters | Yes |
> | registeredSubscriptions | No |

## <a name="microsoftkubernetesconfiguration"></a>Microsoft.KubernetesConfiguration

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | Extensies | No |
> | sourceControlConfigurations | No |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | Clusters | Yes |
> | clusters /attacheddatabaseconfigurations | No |
> | clusters/databases | No |
> | clusters / databases / gegevensverbindingen | No |
> | clusters / databases / eventhubconnections | No |
> | clusters / databases / principalassignments | No |
> | clusters / databases / scripts | No |
> | clusters/gegevensverbindingen | No |
> | clusters /principalassignments | No |
> | clusters /sharedidentities | No |

## <a name="microsoftlabservices"></a>Microsoft.LabServices

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | labaccounts | Yes |
> | labplans | Yes |
> | Labs | Yes |
> | gebruikers | No |

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | hostingEnvironments | Yes |
> | integrationAccounts | Yes |
> | integrationServiceEnvironments | Yes |
> | integrationServiceEnvironments / managedApis | Yes |
> | isolatedEnvironments | Yes |
> | Werkstromen | Yes |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | commitmentPlans | Yes |
> | Webservices | Yes |
> | Workspaces | Yes |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | modelinventories | Yes |
> | virtualclusters | Yes |
> | workspaces | Yes |
> | werkruimten /batchEndpoints | Yes |
> | werkruimten / batchEndpoints / implementaties | Yes |
> | werkruimten / batchEndpoints / implementaties / taken | No |
> | werkruimten / batchEndpoints / taken | No |
> | werkruimten/codes | No |
> | werkruimten / codes / versies | No |
> | werkruimten/berekeningen | No |
> | werkruimten/gegevens | No |
> | werkruimten/gegevensstores | No |
> | werkruimten/omgevingen | No |
> | werkruimten / eventGridFilters | No |
> | werkruimten/taken | No |
> | werkruimten /labelenJobs | No |
> | werkruimten /linkedServices | No |
> | werkruimten/modellen | No |
> | werkruimten/modellen/versies | No |
> | werkruimten /onlineEndpoints | Yes |
> | werkruimten / onlineEndpoints / implementaties | Yes |

## <a name="microsoftmaintenance"></a>Microsoft.Maintenance

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | applyUpdates | No |
> | configurationAssignments | No |
> | maintenanceConfigurations | Yes |
> | publicMaintenanceConfigurations | No |
> | updates | No |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | Identiteiten | No |
> | userAssignedIdentities | Yes |

## <a name="microsoftmanagednetwork"></a>Microsoft.ManagedNetwork

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | managedNetworks | Yes |
> | managedNetworks/managedNetworkGroups | Yes |
> | managedNetworks /managedNetworkPeeringPolicies | Yes |
> | melding | Yes |

## <a name="microsoftmanagedservices"></a>Microsoft.ManagedServices

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | marketplaceRegistrationDefinitions | No |
> | registrationAssignments | No |
> | registrationDefinitions | No |

## <a name="microsoftmanagement"></a>Microsoft.Management

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | getEntities | No |
> | managementGroups | No |
> | managementGroups/settings | No |
> | resources | No |
> | startTenantBackfill | No |
> | tenantBackfillStatus | No |

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | accounts | Yes |
> | accounts /makers | Yes |
> | accounts / eventGridFilters | No |
> | accounts /privateAtlases | Yes |

## <a name="microsoftmarketplace"></a>Microsoft.Marketplace

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | macc | No |
> | Biedt | No |
> | offerTypes | No |
> | offerTypes/uitgevers | No |
> | offerTypes/uitgevers/aanbiedingen | No |
> | offerTypes / uitgevers / aanbiedingen / abonnementen | No |
> | offerTypes / uitgevers / aanbiedingen / abonnementen / overeenkomsten | No |
> | offerTypes / uitgevers / aanbiedingen / abonnementen / configuraties | No |
> | offerTypes / uitgevers / aanbiedingen / abonnementen / configuraties / importImage | No |
> | privategalleryitems | No |
> | privateStoreClient | No |
> | privateStores | No |
> | privateStores/AdminRequestApprovals | No |
> | privateStores/aanbiedingen | No |
> | privateStores /offers/acknowledgeNotification | No |
> | privateStores/queryNotificationsState | No |
> | privateStores/RequestApprovals | No |
> | privateStores/requestApprovals /query | No |
> | privateStores/requestApprovals /2019Plan | No |
> | Producten | No |
> | Uitgevers | No |
> | uitgevers/aanbiedingen | No |
> | uitgevers/aanbiedingen/aanpassingen | No |
> | registreren | No |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | classicDevServices | Yes |
> | updateCommunicationPreference | No |

## <a name="microsoftmarketplaceordering"></a>Microsoft.MarketplaceOrdering

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | Overeenkomsten | No |
> | aanbiedingstypen | No |

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | mediaservices | Yes |
> | mediaservices/accountFilters | No |
> | mediaservices/assets | No |
> | mediaservices / assets / assetFilters | No |
> | mediaservices /contentKeyPolicies | No |
> | mediaservices / eventGridFilters | No |
> | mediaservices/graphInstances | No |
> | mediaservices/graphTopologies | No |
> | mediaservices / liveEventOperations | No |
> | mediaservices /liveEvents | Yes |
> | mediaservices / liveEvents / liveOutputs | No |
> | mediaservices /liveOutputOperations | No |
> | mediaservices /mediaGraphs | No |
> | mediaservices / privateEndpointConnectionOperations | No |
> | mediaservices / privateEndpointConnectionProxies | No |
> | mediaservices /privateEndpointConnections | No |
> | mediaservices/streamingEndpointOperations | No |
> | mediaservices/streamingEndpoints | Yes |
> | mediaservices/streamingLocators | No |
> | mediaservices/streamingPolicies | No |
> | mediaservices/transformaties | No |
> | mediaservices / transformeert / taken | No |
> | videoAnalyzers | Yes |
> | videoAnalyzers/edgeModules | No |

## <a name="microsoftmicroservices4spring"></a>Microsoft.Microservices4Spring

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | appClusters | Yes |

## <a name="microsoftmigrate"></a>Microsoft.Migrate

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | assessmentProjects | Yes |
> | migrateprojects | Yes |
> | moveCollections | Yes |
> | Projecten | Yes |

## <a name="microsoftmixedreality"></a>Microsoft.MixedReality

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | holographicsBroadcastAccounts | Yes |
> | objectAnchorsAccounts | Yes |
> | objectUnderstandingAccounts | Yes |
> | remoteRenderingAccounts | Yes |
> | spatialAnchorsAccounts | Yes |

## <a name="microsoftmobilenetwork"></a>Microsoft.MobileNetwork

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | Netwerken | Yes |
> | netwerken/sites | Yes |
> | packetCores | Yes |
> | Sims | Yes |
> | : / simProfiles | Yes |

## <a name="microsoftnetapp"></a>Microsoft.NetApp

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | netAppAccounts | Yes |
> | netAppAccounts/accountBackups | No |
> | netAppAccounts/capacityPools | Yes |
> | netAppAccounts/capacityPools/volumes | Yes |
> | netAppAccounts / capacityPools / volumes / momentopnamen | No |
> | netAppAccounts/volumeGroups | No |
## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | applicationGateways | Yes |
> | applicationGatewayWebApplicationFirewallPolicies | Yes |
> | applicationSecurityGroups | Yes |
> | azureFirewallFqdnTags | No |
> | azureFirewalls | Yes |
> | bastionHosts | Yes |
> | bgpServiceCommunities | No |
> | Verbindingen | Yes |
> | ddosCustomPolicies | Yes |
> | ddosProtectionPlans | Yes |
> | dnsOperationStatuses | No |
> | dnszones | Yes |
> | dnszones / A | No |
> | dnszones / AAAA | No |
> | dnszones / alle | No |
> | dnszones / CAA | No |
> | dnszones / CNAME | No |
> | dnszones / MX | No |
> | dnszones / NS | No |
> | dnszones /PTR | No |
> | dnszones /recordsets | No |
> | dnszones / SOA | No |
> | dnszones / SRV | No |
> | dnszones / TXT | No |
> | expressRouteCircuits | Yes |
> | expressRouteCrossConnections | Yes |
> | expressRouteGateways | Yes |
> | expressRoutePorts | Yes |
> | expressRouteServiceProviders | No |
> | firewallPolicies | Yes |
> | frontdoors | Yes |
> | frontdoorWebApplicationFirewallManagedRuleSets | No |
> | frontdoorWebApplicationFirewallPolicies | Yes |
> | getDnsResourceReference | No |
> | internalNotify | No |
> | ipGroups | Yes |
> | loadBalancers | Yes |
> | localNetworkGateways | Yes |
> | natGateways | Yes |
> | networkIntentPolicies | Yes |
> | networkInterfaces | Yes |
> | networkProfiles | Yes |
> | networkSecurityGroups | Yes |
> | networkWatchers | Yes |
> | networkWatchers/connectionMonitors | Yes |
> | networkWatchers/flowLogs | Yes |
> | networkWatchers/lenses | Yes |
> | networkWatchers/pingMeshes | Yes |
> | p2sVpnGateways | Yes |
> | privateDnsOperationStatuses | No |
> | privateDnsZones | Yes |
> | privateDnsZones /A | No |
> | privateDnsZones/AAAA | No |
> | privateDnsZones /all | No |
> | privateDnsZones /CNAME | No |
> | privateDnsZones / MX | No |
> | privateDnsZones/PTR | No |
> | privateDnsZones/SOA | No |
> | privateDnsZones/SRV | No |
> | privateDnsZones /TXT | No |
> | privateDnsZones /virtualNetworkLinks | Yes |
> | privateEndpoints | Yes |
> | privateLinkServices | Yes |
> | publicIPAddresses | Yes |
> | publicIPPrefixes | Yes |
> | routeFilters | Yes |
> | routeTables | Yes |
> | serviceEndpointPolicies | Yes |
> | trafficManagerGeographicHierarchies | No |
> | trafficmanagerprofiles | Yes |
> | trafficmanagerprofiles/heatMaps | No |
> | trafficManagerUserMetricsKeys | No |
> | virtualHubs | Yes |
> | virtualNetworkGateways | Yes |
> | virtualNetworks | Yes |
> | virtualNetworks/subnetten | No |
> | virtualNetworkTaps | Yes |
> | virtualWans | Yes |
> | vpnGateways | Yes |
> | vpnSites | Yes |
> | webApplicationFirewallPolicies | Yes |

## <a name="microsoftnotebooks"></a>Microsoft.Notebooks

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | NotebookProxies | No |

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | Naamruimten | Yes |
> | namespaces/notificationHubs | Yes |

## <a name="microsoftobjectstore"></a>Microsoft.ObjectStore

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | osNamespaces | Yes |

## <a name="microsoftoffazure"></a>Microsoft.OffAzure

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | HyperVSites | Yes |
> | ImportSites | Yes |
> | MasterSites | Yes |
> | ServerSites | Yes |
> | VMwareSites | Yes |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | Clusters | Yes |
> | deletedWorkspaces | No |
> | linkTargets | No |
> | querypacks | Yes |
> | storageInsightConfigs | No |
> | workspaces | Yes |
> | werkruimten/gegevensExports | No |
> | werkruimten/dataSources | No |
> | werkruimten /linkedServices | No |
> | workspaces / linkedStorageAccounts | No |
> | werkruimten/metagegevens | No |
> | werkruimten/query | No |
> | werkruimten / scopedPrivateLinkProxies | No |
> | werkruimten /storageInsightConfigs | No |
> | werkruimten/tabellen | No |

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | beheerkoppelingen | No |
> | beheerconfiguraties | Yes |
> | oplossingen | Yes |
> | Weergaven | Yes |

## <a name="microsoftpeering"></a>Microsoft.Peering

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | cdnPeeringPrefixes | No |
> | legacyPeerings | No |
> | peerAsns | No |
> | peerings | Yes |
> | peeringServiceCountries | No |
> | peeringServiceProviders | No |
> | peeringServices | Yes |

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | Getuigschriften | No |
> | eventGridFilters | No |
> | policyEvents | No |
> | policyMetadata | No |
> | policyStates | No |
> | policyTrackedResources | No |
> | herstel | No |

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> |  -consoles | No |
> | dashboards | Yes |
> | tenantconfiguraties | No |
> | userSettings | No |

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | privateLinkServicesForPowerBI | Yes |
> | Huurders | Yes |
> | tenants/werkruimten | No |
> | workspaceCollections | Yes |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | autoScaleVCores | Yes |
> | Capaciteiten | Yes |

## <a name="microsoftpowerplatform"></a>Microsoft.PowerPlatform

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | enterprisePolicies | Yes |

## <a name="microsoftprojectbabylon"></a>Microsoft.ProjectMasylon

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | accounts | Yes |
> | deletedAccounts | No |

## <a name="microsoftproviderhub"></a>Microsoft.ProviderHub

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | providerRegistrations | No |
> | providerRegistrations /customRollouts | No |
> | providerRegistrations /defaultRollouts | No |
> | providerRegistrations / resourceTypeRegistrations | No |

## <a name="microsoftpurview"></a>Microsoft.Purview

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | accounts | Yes |
> | deletedAccounts | No |
> | getDefaultAccount | No |
> | removeDefaultAccount | No |
> | setDefaultAccount | No |

## <a name="microsoftquantum"></a>Microsoft.Quantum

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | Workspaces | Yes |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | backupProtectedItems | No |
> | Kluizen | Yes |

## <a name="microsoftredhatopenshift"></a>Microsoft.RedHatOpenShift

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | OpenShiftClusters | Yes |

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | Naamruimten | Yes |
> | naamruimten/authorizationrules | No |
> | naamruimten /hybride verbindingen | No |
> | naamruimten / hybridconnections / authorizationrules | No |
> | naamruimten / privateEndpointConnections | No |
> | naamruimten /wcfrelays | No |
> | naamruimten / wcfrelays / authorizationrules | No |

## <a name="microsoftresourceconnector"></a>Microsoft.ResourceConnector

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | Apparaten | Yes |

## <a name="microsoftresourcegraph"></a>Microsoft.ResourceGraph

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | Query 's | Yes |
> | resourceChangeDetails | No |
> | resourceChanges | No |
> | resources | No |
> | resourcesHistory | No |
> | subscriptionsStatus | No |

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | availabilityStatuses | No |
> | childAvailabilityStatuses | No |
> | childResources | No |
> | opkomendeissues | No |
> | events | No |
> | impactedResources | No |
> | metagegevens | No |

## <a name="microsoftresources"></a>Microsoft.Resources

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | Implementaties | No |
> | implementaties/bewerkingen | No |
> | deploymentScripts | Yes |
> | deploymentScripts/logboeken | No |
> | Verwijzigingen | No |
> | providers | No |
> | resourceGroups | No |
> | Abonnementen | No |
> | templateSpecs | Yes |
> | templateSpecs/versions | Yes |
> | Huurders | No |

## <a name="microsoftsaas"></a>Microsoft.SaaS

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | toepassingen | Yes |
> | resources | Yes |
> | saasresources | No |

## <a name="microsoftscvmm"></a>Microsoft.ScVmm

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | Wolken | Yes |
> | VirtualMachines | Yes |
> | VirtualMachineTemplates | Yes |
> | VirtualNetworks | Yes |
> | vmmservers | Yes |

## <a name="microsoftsearch"></a>Microsoft.Search

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | resourceHealthMetadata | No |
> | searchServices | Yes |

## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | adaptiveNetworkHardenings | No |
> | advancedThreatProtectionSettings | No |
> | waarschuwingen | No |
> | waarschuwingenSuppressionRules | No |
> | allowedConnections | No |
> | applicationWhitelistings | No |
> | assessmentMetadata | No |
> | evaluaties | No |
> | autoDismissAlertsRules | No |
> | automatiseringen | Yes |
> | AutoProvisioningSettings | No |
> | Naleving | No |
> | connectoren | No |
> | dataCollectionAgents | No |
> | devices | No |
> | deviceSecurityGroups | No |
> | discoveredSecuritySolutions | No |
> | externalSecuritySolutions | No |
> | InformationProtectionPolicies | No |
> | opnameInstellingen | No |
> | Inzichten | No |
> | iotAlerts | No |
> | iotAlertTypes | No |
> | iotDefenderSettings | No |
> | iotRecommendations | No |
> | iotRecommendationTypes | No |
> | iotSecuritySolutions | Yes |
> | iotSecuritySolutions /analyticsModels | No |
> | iotSecuritySolutions / analyticsModels / aggregatedAlerts | No |
> | iotSecuritySolutions / analyticsModels / aggregatedRecommendations | No |
> | iotSecuritySolutions / iotAlerts | No |
> | iotSecuritySolutions / iotAlertTypes | No |
> | iotSecuritySolutions / iotRecommendations | No |
> | iotSecuritySolutions / iotRecommendationTypes | No |
> | iotSensors | No |
> | iotSites | No |
> | jitNetworkAccessPolicies | No |
> | jitPolicies | No |
> | onPremiseIotSensors | No |
> | policies | No |
> | prijzen | No |
> | regulatoryComplianceStandards | No |
> | regulatoryComplianceStandards /regulatoryComplianceControls | No |
> | regulatoryComplianceStandards / regulatoryComplianceControls / regulatoryComplianceAssessments | No |
> | secureScoreControlDefinitions | No |
> | secureScoreControls | No |
> | secureScores | No |
> | secureScores/secureScoreControls | No |
> | securityContacts | No |
> | securitySolutions | No |
> | securitySolutionsReferenceData | No |
> | securityStatuses | No |
> | securityStatusesSummaries | No |
> | serverVulnerabilityAssessments | No |
> | instellingen | No |
> | sqlVulnerabilityAssessments | No |
> | subbeoordelingen | No |
> | taken | No |
> | Topologieën | No |
> | workspaceSettings | No |

## <a name="microsoftsecuritygraph"></a>Microsoft.SecurityGraph

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | diagnosticSettings | No |
> | diagnosticSettingsCategories | No |

## <a name="microsoftsecurityinsights"></a>Microsoft.SecurityInsights

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | aggregaties | No |
> | alertRules | No |
> | alertRuleTemplates | No |
> | automationRules | No |
> | bladwijzers | No |
> | Gevallen | No |
> | dataConnectors | No |
> | dataConnectorsCheckRequirements | No |
> | Verrijking | No |
> | entiteiten | No |
> | entityQueries | No |
> | entityQueryTemplates | No |
> | incidenten | No |
> | officeConsents | No |
> | instellingen | No |
> | threatIntelligence | No |
> | watchlists | No |

## <a name="microsoftserialconsole"></a>Microsoft.SerialConsole

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | consoleServices | No |
> | serialPorts | No |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | Naamruimten | Yes |
> | naamruimten/authorizationrules | No |
> | naamruimten /disasterrecoveryconfigs | No |
> | naamruimten /eventgridfilters | No |
> | naamruimten/networkrulesets | No |
> | naamruimten / privateEndpointConnections | No |
> | naamruimten/wachtrijen | No |
> | naamruimten/wachtrijen/authorizationrules | No |
> | naamruimten/onderwerpen | No |
> | naamruimten / onderwerpen / authorizationrules | No |
> | naamruimten/onderwerpen/abonnementen | No |
> | naamruimten / onderwerpen / abonnementen / regels | No |
> | premiumMessagingRegions | No |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | toepassingen | Yes |
> | Clusters | Yes |
> | clusters/toepassingen | No |
> | containerGroups | Yes |
> | containerGroupSets | Yes |
> | edgeclusters | Yes |
> | edgeclusters/toepassingen | No |
> | managedclusters | Yes |
> | managedclusters/toepassingen | No |
> | managedclusters/toepassingen/services | No |
> | managedclusters/applicationTypes | No |
> | managedclusters/applicationTypes/versions | No |
> | managedclusters/nodetypes | No |
> | Netwerken | Yes |
> | secretstores | Yes |
> | secretstores /certificates | No |
> | secretstores /secrets | No |
> | volumes | Yes |

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | toepassingen | Yes |
> | containerGroups | Yes |
> | Gateways | Yes |
> | Netwerken | Yes |
> | geheimen | Yes |
> | volumes | Yes |

## <a name="microsoftservicelinker"></a>Microsoft.ServiceLinker

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | linkers | No |

## <a name="microsoftservices"></a>Microsoft.Services

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | providerRegistrations | No |
> | providerRegistrations / resourceTypeRegistrations | No |
> | implementaties | Yes |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | SignalR | Yes |
> | SignalR/eventGridFilters | No |
> | WebPubSub | Yes |

## <a name="microsoftsingularity"></a>Microsoft.Singularity

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | accounts | Yes |
> | accounts /accountQuotaPolicies | No |
> | accounts /groupPolicies | No |
> | accounts/taken | No |
> | accounts /storageContainers | No |
> | images | No |

## <a name="microsoftsoftwareplan"></a>Microsoft.SoftwarePlan

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | hybridUseBenefits | No |

## <a name="microsoftsolutions"></a>Microsoft.Solutions

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | applicationDefinitions | Yes |
> | toepassingen | Yes |
> | jitRequests | Yes |

## <a name="microsoftsql"></a>Microsoft.SQL

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | managedInstances | Yes |
> | managedInstances /databases | Yes |
> | managedInstances / databases / backupShortTermRetentionPolicies | No |
> | managedInstances / databases / schema's / tabellen / kolommen / sensitivityLabels | No |
> | managedInstances / databases / vulnerabilityAssessments | No |
> | managedInstances / databases / vulnerabilityAssessments / regels / basislijnen | No |
> | managedInstances /encryptionProtector | No |
> | managedInstances /keys | No |
> | managedInstances / restorableDroppedDatabases / backupShortTermRetentionPolicies | No |
> | managedInstances/vulnerabilityAssessments | No |
> | Servers | Yes |
> | servers/beheerders | No |
> | servers/communicationLinks | No |
> | servers/databases | Yes |
> | servers /encryptionProtector | No |
> | servers /firewallRules | No |
> | servers/sleutels | No |
> | servers / restorableDroppedDatabases | No |
> | servers/serviceobjectives | No |
> | servers / tdeCertificates | No |
> | virtualClusters | No |

## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | SqlVirtualMachineGroups | Yes |
> | SqlVirtualMachineGroups/AvailabilityGroupListeners | No |
> | SqlVirtualMachines | Yes |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | deletedAccounts | No |
> | storageAccounts | Yes |
> | storageAccounts/blobServices | No |
> | storageAccounts/fileServices | No |
> | storageAccounts/queueServices | No |
> | storageAccounts/services | No |
> | storageAccounts / services / metricDefinitions | No |
> | storageAccounts/tableServices | No |
> | gebruik | No |

## <a name="microsoftstoragecache"></a>Microsoft.StorageCache

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | amlFilesystems | Yes |
> | Caches | Yes |
> | caches/storageTargets | No |
> | usageModels | No |

## <a name="microsoftstoragereplication"></a>Microsoft.StorageReplication

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | replicationGroups | No |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | storageSyncServices | Yes |
> | storageSyncServices/registeredServers | No |
> | storageSyncServices/syncGroups | No |
> | storageSyncServices / syncGroups / cloudEndpoints | No |
> | storageSyncServices / syncGroups / serverEndpoints | No |
> | storageSyncServices/workflows | No |

## <a name="microsoftstoragesyncdev"></a>Microsoft.StorageSyncDev

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | storageSyncServices | Yes |
> | storageSyncServices/registeredServers | No |
> | storageSyncServices/syncGroups | No |
> | storageSyncServices / syncGroups / cloudEndpoints | No |
> | storageSyncServices / syncGroups / serverEndpoints | No |
> | storageSyncServices/workflows | No |

## <a name="microsoftstoragesyncint"></a>Microsoft.StorageSyncInt

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | storageSyncServices | Yes |
> | storageSyncServices/registeredServers | No |
> | storageSyncServices/syncGroups | No |
> | storageSyncServices/syncGroups/cloudEndpoints | No |
> | storageSyncServices/syncGroups/serverEndpoints | No |
> | storageSyncServices/workflows | No |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | Managers | Yes |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | Clusters | Yes |
> | clusters /privateEndpoints | No |
> | streamingjobs | Yes |

## <a name="microsoftsubscription"></a>Microsoft.Subscription

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | acceptChangeTenant | No |
> | acceptOwnership | No |
> | acceptOwnershipStatus | No |
> | Aliassen | No |
> | annuleren | No |
> | changeTenantRequest | No |
> | changeTenantStatus | No |
> | CreateSubscription | No |
> | inschakelen | No |
> | policies | No |
> | naam wijzigen | No |
> | SubscriptionDefinitions | No |
> | SubscriptionOperations | No |
> | Abonnementen | No |

## <a name="microsoftsynapse"></a>Microsoft.Synapse

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | privateLinkHubs | Yes |
> | workspaces | Yes |
> | werkruimten /bigDataPools | Yes |
> | werkruimten/operationStatuses | No |
> | werkruimten /sqlDatabases | Yes |
> | werkruimten /sqlPools | Yes |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | Omgevingen | Yes |
> | omgevingen /accessPolicies | No |
> | omgevingen /eventsources | Yes |
> | omgevingen / privateEndpointConnectionProxies | No |
> | omgevingen / privateEndpointConnections | No |
> | omgevingen / privateLinkResources | No |
> | omgevingen /referenceDataSets | Yes |

## <a name="microsofttoken"></a>Microsoft.Token

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | Winkels | Yes |
> | stores/accessPolicies | No |
> | stores/services | No |
> | winkels / services / tokens | No |

## <a name="microsoftvirtualmachineimages"></a>Microsoft.VirtualMachineImages

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | imageTemplates | Yes |
> | imageTemplates /runOutputs | No |

## <a name="microsoftvmware"></a>Microsoft.VMware

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | ArcZones | Yes |
> | ResourcePools | Yes |
> | VCenters | Yes |
> | VCenters/InventoryItems | No |
> | virtualmachines | Yes |
> | VirtualMachineTemplates | Yes |
> | VirtualNetworks | Yes |

## <a name="microsoftvmwarecloudsimple"></a>Microsoft.VMwareCloudSimple

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | dedicatedCloudNodes | Yes |
> | dedicatedCloudServices | Yes |
> | virtualMachines | Yes |

## <a name="microsoftvnfmanager"></a>Microsoft.VnfManager

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | devices | Yes |
> | registeredSubscriptions | No |
> | Leveranciers | No |
> | leveranciers/SKU's | No |
> | vendors /vnfs | No |
> | virtualNetworkFunctionSkus | No |
> | vnfs | Yes |

## <a name="microsoftvsonline"></a>Microsoft.VSOnline

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | accounts | Yes |
> | Plannen | Yes |
> | registeredSubscriptions | No |

## <a name="microsoftweb"></a>Microsoft.Web

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | apiManagementAccounts | No |
> | apiManagementAccounts / apiAcls | No |
> | apiManagementAccounts/apis | No |
> | apiManagementAccounts / apis / apiAcls | No |
> | apiManagementAccounts / apis / connectionAcls | No |
> | apiManagementAccounts / apis / verbindingen | No |
> | apiManagementAccounts / apis / connections / connectionAcls | No |
> | apiManagementAccounts / apis / localizedDefinitions | No |
> | apiManagementAccounts/connectionAcls | No |
> | apiManagementAccounts /connections | No |
> | billingMeters | No |
> | certificaten | Yes |
> | connectionGateways | Yes |
> | Verbindingen | Yes |
> | customApis | Yes |
> | deletedSites | No |
> | functionAppStacks | No |
> | generateGithubAccessTokenForAppserviceCLI | No |
> | hostingEnvironments | Yes |
> | hostingEnvironments / eventGridFilters | No |
> | hostingEnvironments / multiRolePools | No |
> | hostingEnvironments /workerPools | No |
> | kubeEnvironments | Yes |
> | publishingUsers | No |
> | aanbevelingen | No |
> | resourceHealthMetadata | No |
> | runtimes | No |
> | serverFarms | Yes |
> | serverFarms/eventGridFilters | No |
> | serverFarms/firstPartyApps | No |
> | serverFarms / firstPartyApps / keyVaultSettings | No |
> | sites | Yes |
> | sites/config  | No |
> | sites / eventGridFilters | No |
> | sites /hostNameBindings | No |
> | sites / networkConfig | No |
> | sites /premieraddons | Yes |
> | sites/sites | Yes |
> | sites / sites / eventGridFilters | No |
> | sites / sites / hostNameBindings | No |
> | sites/ sites / networkConfig | No |
> | sourceControls | No |
> | staticSites | Yes |
> | Valideren | No |
> | verifyHostingEnvironmentVnet | No |
> | webAppStacks | No |

## <a name="microsoftwindowsdefenderatp"></a>Microsoft.WindowsDefenderATP

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | diagnosticSettings | No |
> | diagnosticSettingsCategories | No |

## <a name="microsoftwindowsesu"></a>Microsoft.WindowsESU

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | multipleActivationKeys | Yes |

## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | DeviceServices | Yes |

## <a name="microsoftworkloadbuilder"></a>Microsoft.WorkloadBuilder

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | migrationAgents | Yes |
> | Werkbelasting | Yes |
> | workloads/exemplaren | No |
> | workloads/versies | No |
> | workloads/versies/artefacten | No |

## <a name="microsoftworkloadmonitor"></a>Microsoft.WorkloadMonitor

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | Monitoren | Nee |

## <a name="next-steps"></a>Volgende stappen

Als u dezelfde gegevens wilt op halen als een bestand met door komma's gescheiden waarden, downloadt [ ucomplete-mode-deletion.csv](https://github.com/tfitzmac/resource-capabilities/blob/master/complete-mode-deletion.csv).
