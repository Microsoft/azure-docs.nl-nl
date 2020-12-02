---
title: Resource providers door Azure-Services
description: Een lijst met alle naam ruimten van de resource provider voor Azure Resource Manager en toont de Azure-service voor die naam ruimte.
ms.topic: conceptual
ms.date: 12/01/2020
ms.openlocfilehash: faea6badc24d0152e94fc507cafdb15932d1ea3e
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/01/2020
ms.locfileid: "96452624"
---
# <a name="resource-providers-for-azure-services"></a>Resource providers for Azure services (Resourceproviders voor Azure-services)

In dit artikel wordt uitgelegd hoe naam ruimten van de resource provider worden toegewezen aan Azure-Services.

## <a name="match-resource-provider-to-service"></a>Overeenkomende resource provider voor service

De resource providers die zijn gemarkeerd met **-geregistreerd** , worden standaard geregistreerd voor uw abonnement. Zie [registratie](#registration)voor meer informatie.

| Naam ruimte van resource provider | Azure-service |
| --------------------------- | ------------- |
| Micro soft. AAD | [Azure Active Directory Domain Services](../../active-directory-domain-services/index.yml) |
| Micro soft. Addons | baan |
| Micro soft. ADHybridHealthService- [geregistreerd](#registration) | [Azure Active Directory](../../active-directory/index.yml) |
| Microsoft.Advisor | [Azure Advisor](../../advisor/index.yml) |
| Micro soft. AlertsManagement | [Azure Monitor](../../azure-monitor/index.yml) |
| Microsoft.AnalysisServices | [Azure Analysis Services](../../analysis-services/index.yml) |
| Microsoft.ApiManagement | [API Management](../../api-management/index.yml) |
| Micro soft. AppConfiguration | [Azure App Configuration](../../azure-app-configuration/index.yml) |
| Micro soft. AppPlatform | [Azure Spring Cloud](../../spring-cloud/spring-cloud-overview.md) |
| Micro soft. Attestation | Azure Attestation-service |
| Micro soft. autorisatie- [geregistreerd](#registration) | [Azure Resource Manager](../index.yml) |
| Microsoft.Automation | [Automation](../../automation/index.yml) |
| Micro soft. AutonomousSystems | [Autonome systemen](https://www.microsoft.com/ai/autonomous-systems) |
| Micro soft. AVS | [Azure VMware Solution](../../azure-vmware/index.yml) |
| Microsoft.AzureActiveDirectory | [Azure Active Directory B2C](../../active-directory-b2c/index.yml) |
| Micro soft. Azureworden | SQL Server REGI ster |
| Micro soft. AzureStack | baan |
| Micro soft. AzureStackHCI | [Azure Stack HCI](/azure-stack/hci/overview) |
| Microsoft.Batch | [Batch](../../batch/index.yml) |
| Micro soft. facturering- [geregistreerd](#registration) | [Kostenbeheer en facturering](/azure/billing/) |
| Microsoft.BingMaps | [Bing Maps](/BingMaps/#pivot=main&panel=BingMapsAPI) |
| Micro soft. Block Chain | [Azure Blockchain-service](../../blockchain/workbench/index.yml) |
| Micro soft. BlockchainTokens | [Azure Blockchain Tokens](https://azure.microsoft.com/services/blockchain-tokens/) |
| Micro soft. blauw druk | [Azure Blueprints](../../governance/blueprints/index.yml) |
| Micro soft. BotService | [Azure Bot Service](/azure/bot-service/) |
| Microsoft.Cache | [Azure Cache voor Redis](../../azure-cache-for-redis/index.yml) |
| Micro soft. capacity | baan |
| Micro soft. CDN | [Content Delivery Network](../../cdn/index.yml) |
| Microsoft.CertificateRegistration | [App Service certificaten](../../app-service/configure-ssl-certificate.md#import-an-app-service-certificate) |
| Micro soft. ChangeAnalysis | [Azure Monitor](../../azure-monitor/index.yml) |
| Microsoft.ClassicCompute | Klassieke implementatie model virtuele machine |
| Micro soft. ClassicInfrastructureMigrate | Migratie van het klassieke implementatie model |
| Microsoft.ClassicNetwork | Het virtuele netwerk van het klassieke implementatie model |
| Microsoft.ClassicStorage | Klassieke implementatie model opslag |
| Micro soft. ClassicSubscription- [geregistreerd](#registration) | Klassiek implementatiemodel |
| Microsoft.CognitiveServices | [Cognitive Services](../../cognitive-services/index.yml) |
| Micro soft. commerce- [geregistreerd](#registration) | baan |
| Microsoft.Compute | [Virtual Machines](../../virtual-machines/index.yml)<br />[Virtual Machine Scale Sets](../../virtual-machine-scale-sets/index.yml) |
| Micro soft. verbruik- [geregistreerd](#registration) | [Cost Management](/azure/cost-management/) |
| Micro soft. ContainerInstance | [Container Instances](../../container-instances/index.yml) |
| Microsoft.ContainerRegistry | [Container Registry](../../container-registry/index.yml) |
| Microsoft.ContainerService | [Azure Kubernetes Service (AKS)](../../aks/index.yml) |
| Micro soft. CostManagement- [geregistreerd](#registration) | [Cost Management](/azure/cost-management/) |
| Micro soft. CostManagementExports | [Cost Management](/azure/cost-management/) |
| Micro soft. CustomerLockbox | [Klanten-lockbox voor Microsoft Azure](../../security/fundamentals/customer-lockbox-overview.md) |
| Micro soft. CustomProviders | [Aangepaste Azure-providers](../custom-providers/overview.md) |
| Micro soft. DataBox | [Azure Data Box](../../databox/index.yml) |
| Micro soft. DataBoxEdge | [Azure Stack Edge](../../databox-online/azure-stack-edge-overview.md) |
| Micro soft. Databricks | [Azure Databricks](/azure/azure-databricks/) |
| Microsoft.DataCatalog | [Data Catalog](../../data-catalog/index.yml) |
| Microsoft.DataFactory | [Data Factory](../../data-factory/index.yml) |
| Microsoft.DataLakeAnalytics | [Data Lake Analytics](../../data-lake-analytics/index.yml) |
| Microsoft.DataLakeStore | [Azure Data Lake Storage Gen2](../../storage/blobs/data-lake-storage-introduction.md) |
| Micro soft. DataMigration | [Azure Database Migration Service](../../dms/index.yml) |
| Micro soft. DataProtection | Gegevensbeveiliging |
| Micro soft. DataShare | [Azure Data Share](../../data-share/index.yml) |
| Micro soft. DBforMariaDB | [Azure Database for MariaDB](../../mariadb/index.yml) |
| Micro soft. DBforMySQL | [Azure Database for MySQL](../../mysql/index.yml) |
| Micro soft. DBforPostgreSQL | [Azure Database for PostgreSQL](../../postgresql/index.yml) |
| Micro soft. DeploymentManager | [Azure Deployment Manager](../templates/deployment-manager-overview.md) |
| Micro soft. DesktopVirtualization | [Windows Virtual Desktop](../../virtual-desktop/index.yml) |
| Microsoft.Devices | [Azure IoT Hub](../../iot-hub/index.yml)<br />[Azure IoT Hub Device Provisioning Service](../../iot-dps/index.yml) |
| Micro soft. DevOps | [Azure DevOps](/azure/devops/) |
| Micro soft. DevSpaces | [Azure Dev Spaces](../../dev-spaces/index.yml) |
| Microsoft.DevTestLab | [Azure Lab Services](../../lab-services/index.yml) |
| Micro soft. DigitalTwins | [Azure Digital Twins](../../digital-twins/overview.md) |
| Microsoft.DocumentDB | [Azure Cosmos DB](../../cosmos-db/index.yml) |
| Micro soft. DomainRegistration | [App Service](../../app-service/index.yml) |
| Microsoft.DynamicsLcs | [Levenscyclus Services](https://lcs.dynamics.com/Logon/Index ) |
| Micro soft. EnterpriseKnowledgeGraph | Enter prise-kennis grafiek |
| Micro soft. EventGrid | [Event Grid](../../event-grid/index.yml) |
| Microsoft.EventHub | [Event Hubs](../../event-hubs/index.yml) |
| Micro soft. features- [geregistreerd](#registration) | [Azure Resource Manager](../index.yml) |
| Microsoft.GuestConfiguration | [Azure Policy](../../governance/policy/index.yml) |
| Micro soft. HanaOnAzure | [SAP HANA on Azure Large Instances](../../virtual-machines/workloads/sap/hana-overview-architecture.md) |
| Micro soft. HardwareSecurityModules | [Azure Dedicated HSM](../../dedicated-hsm/index.yml) |
| Microsoft.HDInsight | [HDInsight](../../hdinsight/index.yml) |
| Micro soft. HealthcareApis | [Azure-API voor FHIR](../../healthcare-apis/index.yml) |
| Microsoft.HybridCompute | [Azure Arc](../../azure-arc/index.yml) |
| Micro soft. HybridData | [StorSimple](../../storsimple/index.yml) |
| Micro soft. HybridNetwork  | [Zones met persoonlijke randen](../../networking/edge-zones-overview.md) |
| Microsoft.ImportExport | [Azure Import/Export](../../storage/common/storage-import-export-service.md) |
| micro soft. Insights | [Azure Monitor](../../azure-monitor/index.yml) |
| Micro soft. IoTCentral | [Azure IoT Central](../../iot-central/index.yml) |
| Micro soft. IoTSpaces | [Azure Digital Twins](../../digital-twins/index.yml) |
| Microsoft.Intune | [Azure Monitor](../../azure-monitor/index.yml) |
| Microsoft.KeyVault | [Key Vault](../../key-vault/index.yml) |
| Microsoft.Kubernetes | [Azure Kubernetes Service (AKS)](../../aks/index.yml) |
| Micro soft. KubernetesConfiguration | [Azure Kubernetes Service (AKS)](../../aks/index.yml) |
| Microsoft.Kusto | [Azure Data Explorer](/azure/data-explorer/) |
| Micro soft. LabServices | [Azure Lab Services](../../lab-services/index.yml) |
| Microsoft.Logic | [Logic Apps](../../logic-apps/index.yml) |
| Microsoft.MachineLearning | [Machine Learning Studio](../../machine-learning/classic/index.yml) |
| Microsoft.MachineLearningServices | [Azure Machine Learning](../../machine-learning/index.yml) |
| Micro soft. onderhoud | [Onderhoud van Azure](../../virtual-machines/maintenance-control-cli.md) |
| Micro soft. ManagedIdentity | [Beheerde identiteiten voor Azure-resources](../../active-directory/managed-identities-azure-resources/index.yml) |
| Micro soft. ManagedNetwork | Virtuele netwerken die worden beheerd door PaaS Services |
| Micro soft. ManagedServices | [Azure Lighthouse](../../lighthouse/index.yml) |
| Micro soft. Management | [Beheergroepen](../../governance/management-groups/index.yml) |
| Micro soft. Maps | [Azure Maps](../../azure-maps/index.yml) |
| Micro soft. Marketplace | baan |
| Micro soft. MarketplaceApps | baan |
| Micro soft. MarketplaceOrdering- [geregistreerd](#registration) | baan |
| Microsoft.Media | [Media Services](../../media-services/index.yml) |
| Micro soft. Microservices4Spring | [Azure Spring Cloud](../../spring-cloud/spring-cloud-overview.md) |
| Micro soft. migrate | [Azure Migrate](../../migrate/migrate-services-overview.md) |
| Micro soft. MixedReality | [Azure Spatial Anchors](../../spatial-anchors/index.yml) |
| Micro soft. NetApp | [Azure NetApp Files](../../azure-netapp-files/index.yml) |
| Microsoft.Network | [Application Gateway](../../application-gateway/index.yml)<br />[Azure Bastion](../../bastion/index.yml)<br />[Azure DDoS Protection](../../ddos-protection/ddos-protection-overview.md)<br />[Azure DNS](../../dns/index.yml)<br />[Azure ExpressRoute](../../expressroute/index.yml)<br />[Azure Firewall](../../firewall/index.yml)<br />[Azure Front Door Service](../../frontdoor/index.yml)<br />[Azure Private Link](../../private-link/index.yml)<br />[Load Balancer](../../load-balancer/index.yml)<br />[Network Watcher](../../network-watcher/index.yml)<br />[Traffic Manager](../../traffic-manager/index.yml)<br />[Virtual Network](../../virtual-network/index.yml)<br />[Virtual WAN](../../virtual-wan/index.yml)<br />[VPN Gateway](../../vpn-gateway/index.yml)<br /> |
| Micro soft. notebooks | [Azure Notebooks](https://notebooks.azure.com/help/introduction) |
| Microsoft.NotificationHubs | [Notification Hubs](../../notification-hubs/index.yml) |
| Micro soft. ObjectStore | Object opslag |
| Micro soft. OffAzure | [Azure Migrate](../../migrate/migrate-services-overview.md) |
| Microsoft.OperationalInsights | [Azure Monitor](../../azure-monitor/index.yml) |
| Microsoft.OperationsManagement | [Azure Monitor](../../azure-monitor/index.yml) |
| Micro soft. peering | [Azure Peering Service](../../peering-service/index.yml) |
| Microsoft.PolicyInsights | [Azure Policy](../../governance/policy/index.yml) |
| Micro soft. Portal- [geregistreerd](#registration) | [Azure Portal](../../azure-portal/index.yml) |
| Micro soft. PowerBI | [Power BI](/power-bi/power-bi-overview) |
| Micro soft. PowerBIDedicated | [Power BI Embedded](/azure/power-bi-embedded/) |
| Micro soft. PowerPlatform | [Power Platform](/power-platform/) |
| Micro soft. ProjectBabylon | [Azure Data Catalog](../../data-catalog/overview.md) |
| Micro soft. Quantum | [Azure-Quantum](https://azure.microsoft.com/services/quantum/) |
| Microsoft.RecoveryServices | [Azure Site Recovery](../../site-recovery/index.yml) |
| Micro soft. RedHatOpenShift | [Azure Red Hat OpenShift](../../virtual-machines/linux/openshift-get-started.md) |
| Microsoft.Relay | [Azure Relay](../../azure-relay/relay-what-is-it.md) |
| Micro soft. ResourceGraph- [geregistreerd](#registration) | [Azure Resource Graph](../../governance/resource-graph/index.yml) |
| Microsoft.ResourceHealth | [Azure Service Health](../../service-health/index.yml) |
| Micro soft. resources- [geregistreerd](#registration) | [Azure Resource Manager](../index.yml) |
| Micro soft. SaaS | baan |
| Microsoft.Scheduler | [Scheduler](../../scheduler/index.yml) |
| Microsoft.Search | [Azure Cognitive Search](../../search/index.yml) |
| Microsoft.Security | [Beveiligingscentrum](../../security-center/index.yml) |
| Micro soft. SecurityInsights | [Azure Sentinel](../../sentinel/index.yml) |
| Micro soft. SerialConsole- [geregistreerd](#registration) | [Azure Serial console voor Windows](../../virtual-machines/troubleshooting/serial-console-windows.md) |
| Microsoft.ServiceBus | [Service Bus](/azure/service-bus/) |
| Micro soft. ServiceFabric | [Service Fabric](../../service-fabric/index.yml) |
| Micro soft. ServiceFabricMesh | [Service Fabric Mesh](../../service-fabric-mesh/index.yml) |
| Micro soft. Services | baan |
| Micro soft. SignalRService | [Azure SignalR-service](../../azure-signalr/index.yml) |
| Micro soft. SoftwarePlan | Licentie |
| Micro soft. Solutions | [Azure Managed Applications](../managed-applications/index.yml) |
| Microsoft.Sql | [Azure SQL Database](../../azure-sql/database/index.yml)<br /> [Azure SQL Managed Instance](../../azure-sql/managed-instance/index.yml) <br />[Azure Synapse Analytics](/azure/sql-data-warehouse/) |
| Micro soft. SqlVirtualMachine | [SQL Server op virtuele machines in Azure](../../azure-sql/virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md) |
| Microsoft.Storage | [Storage](../../storage/index.yml) |
| Micro soft. StorageCache | [Azure HPC Cache](../../hpc-cache/index.yml) |
| Micro soft. StorageSync | [Storage](../../storage/index.yml) |
| Microsoft.StorSimple | [StorSimple](../../storsimple/index.yml) |
| Microsoft.StreamAnalytics | [Azure Stream Analytics](../../stream-analytics/index.yml) |
| Micro soft. Subscription | baan |
| micro soft. ondersteuning- [geregistreerd](#registration) | baan |
| Micro soft. Synapse | [Azure Synapse Analytics](/azure/sql-data-warehouse/) |
| Micro soft. TimeSeriesInsights | [Azure Time Series Insights](../../time-series-insights/index.yml) |
| Micro soft. token | Token |
| Microsoft.VirtualMachineImages | [Azure Image Builder](../../virtual-machines/linux/image-builder-overview.md) |
| micro soft. Visual Studio | [Azure DevOps](/azure/devops/) |
| Micro soft. VMware | [Azure VMware Solution](../../azure-vmware/index.yml) |
| Micro soft. VMwareCloudSimple | [Azure VMware Solution by CloudSimple](../../vmware-cloudsimple/index.md) |
| Micro soft. VSOnline | [Azure DevOps](/azure/devops/) |
| Microsoft.Web | [App Service](../../app-service/index.yml)<br />[Azure Functions](../../azure-functions/index.yml) |
| Micro soft. WindowsDefenderATP | [Microsoft Defender Advanced Threat Protection](../../security-center/security-center-wdatp.md) |
| Micro soft. WindowsESU | Uitgebreide beveiligings updates |
| Micro soft. WindowsIoT | [Windows 10 IoT Core Services](/windows-hardware/manufacture/iot/iotcoreservicesoverview) |
| Micro soft. WorkloadMonitor | [Azure Monitor](../../azure-monitor/index.yml) |

## <a name="registration"></a>Registratie

De bovenstaande bronnen providers die zijn gemarkeerd met **-geregistreerd** , worden standaard geregistreerd voor uw abonnement. Als u de andere resource providers wilt gebruiken, moet u [deze registreren](resource-providers-and-types.md). Veel bron providers worden echter voor u geregistreerd wanneer u bepaalde acties uitvoert. Als u bijvoorbeeld een bron maakt via de portal, registreert de portal automatisch alle niet-geregistreerde resource providers die nodig zijn. Bij het implementeren van resources via een [Azure Resource Manager sjabloon](../templates/overview.md), worden alle vereiste resource providers ook geregistreerd.

> [!IMPORTANT]
> Registreer alleen een resource provider wanneer u er klaar voor bent om deze te gebruiken. Met de registratie stap kunt u de minimale bevoegdheden binnen uw abonnement behouden. Een kwaadwillende gebruiker kan geen resource providers gebruiken die niet zijn geregistreerd.

## <a name="next-steps"></a>Volgende stappen

Zie [Azure resource providers en-typen](resource-providers-and-types.md)voor meer informatie over resource providers, met inbegrip van het registreren van een resource provider.
