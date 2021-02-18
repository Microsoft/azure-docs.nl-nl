---
title: Azure Monitor resource logboeken ondersteunde services en categorieën
description: Verwijzing van Azure Monitor inzicht krijgen in de ondersteunde services en het gebeurtenis schema voor Azure-resource Logboeken.
ms.subservice: logs
ms.topic: reference
ms.date: 01/29/2021
ms.openlocfilehash: 39ff78cd97682096fb284e137868c246dfdd7f14
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100610368"
---
# <a name="supported-categories-for-azure-resource-logs"></a>Ondersteunde categorieën voor Azure-resource logboeken

> [!NOTE]
> Bron logboeken zijn voorheen bekend als Diagnostische logboeken. De naam is in oktober 2019 gewijzigd, omdat de typen logboeken die door Azure Monitor zijn verzameld, meer dan alleen de Azure-resource bevatten.

[Azure monitor bron logboeken](../essentials/platform-logs-overview.md) worden logboeken gegenereerd door Azure-Services waarmee de werking van deze services of bronnen wordt beschreven. Alle bron logboeken die beschikbaar zijn via Azure Monitor, delen een gemeen schappelijk schema op het hoogste niveau, met flexibiliteit voor elke service om unieke eigenschappen voor hun eigen gebeurtenissen te verzenden.

Een combi natie van het resource type (beschikbaar in de `resourceId` eigenschap) en de `category` unieke identificatie van een schema. Er is een gemeen schappelijk schema voor alle bron logboeken met servicespecifieke velden en vervolgens toegevoegd voor verschillende logboek categorieën. Zie voor meer informatie [common en service-specifiek schema voor Azure-resource logboeken]()


## <a name="costs"></a>Kosten

Er zijn kosten verbonden aan het verzenden en opslaan van gegevens in Log Analytics, Azure Storage en/of event hub. U kunt betalen voor de kosten om de gegevens op deze locaties op te halen en deze daar te bewaren.  Bron logboeken zijn één type gegevens dat u naar deze locaties kunt verzenden. 

Er zijn extra kosten verbonden aan het exporteren van sommige categorieën resource logboeken naar deze locaties. De logboeken met export kosten worden weer gegeven in de onderstaande tabel. Zie de sectie platform logs op de [pagina met Azure monitor prijzen](https://azure.microsoft.com/pricing/details/monitor/)voor meer informatie over deze prijzen.

## <a name="supported-log-categories-per-resource-type"></a>Ondersteunde logboek categorieën per resource type

Hieronder volgt een lijst met de typen logboeken die beschikbaar zijn voor elk resource type. 

Sommige categorieën worden mogelijk alleen ondersteund voor specifieke typen resources. Raadpleeg de resource-specifieke documentatie als u denkt dat er een resource ontbreekt. Bijvoorbeeld: categorieën van micro soft. SQL/servers/data bases zijn niet beschikbaar voor alle typen data bases. Zie [informatie over SQL database diagnostische logboek registratie](../../azure-sql/database/metrics-diagnostic-telemetry-logging-streaming-export-configure.md)voor meer informatie. 

Als u denkt dat er iets ontbreekt, kunt u onder aan dit artikel een GitHub-opmerking openen.
## <a name="microsoftaaddomainservices"></a>Micro soft. AAD/domainServices

|Categorie|Weergave naam categorie|Te exporteren kosten|
|---|---|---|
|AccountLogon|AccountLogon|No|
|AccountManagement|AccountManagement|No|
|DetailTracking|DetailTracking|No|
|DirectoryServiceAccess|DirectoryServiceAccess|No|
|LogonLogoff|LogonLogoff|No|
|ObjectAccess|ObjectAccess|No|
|PolicyChange|PolicyChange|No|
|PrivilegeUse|PrivilegeUse|No|
|SystemSecurity|SystemSecurity|No|


## <a name="microsoftanalysisservicesservers"></a>Micro soft. AnalysisServices/servers

|Categorie|Weergave naam categorie|Te exporteren kosten|
|---|---|---|
|Engine|Engine|No|
|Service|Service|No|


## <a name="microsoftapimanagementservice"></a>Microsoft.ApiManagement/service

|Categorie|Weergave naam categorie|Te exporteren kosten|
|---|---|---|
|Gateway logs|Logboeken gerelateerd aan de ApiManagement-gateway|No|


## <a name="microsoftappconfigurationconfigurationstores"></a>Micro soft. AppConfiguration/configurationStores

|Categorie|Weergave naam categorie|Te exporteren kosten|
|---|---|---|
|HttpRequest|HTTP-aanvragen|Yes|


## <a name="microsoftappplatformspring"></a>Micro soft. AppPlatform/lente

|Categorie|Weergave naam categorie|Te exporteren kosten|
|---|---|---|
|ApplicationConsole|Toepassings console|No|
|SystemLogs|Systeem logboeken|No|


## <a name="microsoftattestationattestationproviders"></a>Microsoft.Attestation/attestationProviders

|Categorie|Weergave naam categorie|Te exporteren kosten|
|---|---|---|
|AuditEvent|Audit event-berichten logboek categorie.|No|
|Gamepad|Logboek categorie voor fout berichten.|No|
|INF|Informatief berichten logboek categorie.|No|
|WRSCH|Waarschuwings bericht categorie.|No|


## <a name="microsoftautomationautomationaccounts"></a>Micro soft. Automation/automationAccounts

|Categorie|Weergave naam categorie|Te exporteren kosten|
|---|---|---|
|DscNodeStatus|DSC-knooppunt status|No|
|JobLogs|Taak logboeken|No|
|JobStreams|Taak stromen|No|


## <a name="microsoftbatchbatchaccounts"></a>Microsoft.Bat-CH/batchAccounts

|Categorie|Weergave naam categorie|Te exporteren kosten|
|---|---|---|
|ServiceLog|Service logboeken|No|


## <a name="microsoftbatchaiworkspaces"></a>Microsoft.BatchAI/werk ruimten

|Categorie|Weergave naam categorie|Te exporteren kosten|
|---|---|---|
|BaiClusterEvent|BaiClusterEvent|No|
|BaiClusterNodeEvent|BaiClusterNodeEvent|No|
|BaiJobEvent|BaiJobEvent|No|


## <a name="microsoftblockchainblockchainmembers"></a>Microsoft.Blockchain/blockchainMembers

|Categorie|Weergave naam categorie|Te exporteren kosten|
|---|---|---|
|BlockchainApplication|Block Chain-toepassing|No|
|FabricOrderer|Fabric-Orderer|No|
|FabricPeer|Infrastructuur peer|No|
|Proxy|Proxy|No|


## <a name="microsoftblockchaincordamembers"></a>Micro soft. Block Chain/cordaMembers

|Categorie|Weergave naam categorie|Te exporteren kosten|
|---|---|---|
|BlockchainApplication|Block Chain-toepassing|No|


## <a name="microsoftbotservicebotservices"></a>micro soft. botservice/botservices

|Categorie|Weergave naam categorie|Te exporteren kosten|
|---|---|---|
|BotRequest|Aanvragen van de kanalen naar de bot|No|
|DependencyRequest|Aanvragen voor afhankelijkheden|No|


## <a name="microsoftcdncdnwebapplicationfirewallpolicies"></a>Micro soft. CDN/cdnwebapplicationfirewallpolicies

|Categorie|Weergave naam categorie|Te exporteren kosten|
|---|---|---|
|WebApplicationFirewallLogs|Web-webtoepassingsbestanden firewall-logboeken|No|


## <a name="microsoftcdnprofiles"></a>Microsoft.Cdn/profiles

|Categorie|Weergave naam categorie|Te exporteren kosten|
|---|---|---|
|AzureCdnAccessLog|Azure CDN-toegangs logboek|No|
|FrontDoorAccessLog|-Ingang-toegangs logboek|Yes|
|FrontDoorHealthProbeLog|-Ingang Health-test logboek|Yes|
|FrontDoorWebApplicationFirewallLog|-Ingang WebApplicationFirewall-logboek|Yes|


## <a name="microsoftcdnprofilesendpoints"></a>Micro soft. CDN/profielen/eind punten

|Categorie|Weergave naam categorie|Te exporteren kosten|
|---|---|---|
|CoreAnalytics|Hiermee worden de metrische gegevens van het eind punt opgehaald, bijvoorbeeld band breedte, uitgaand verkeer enzovoort.|No|


## <a name="microsoftclassicnetworknetworksecuritygroups"></a>Micro soft. ClassicNetwork/networksecuritygroups

|Categorie|Weergave naam categorie|Te exporteren kosten|
|---|---|---|
|Stroom gebeurtenis van regel voor netwerk beveiligings groep|Stroom gebeurtenis van regel voor netwerk beveiligings groep|No|


## <a name="microsoftcognitiveservicesaccounts"></a>Micro soft. CognitiveServices/accounts

|Categorie|Weergave naam categorie|Te exporteren kosten|
|---|---|---|
|Controleren|Auditlogboeken|No|
|RequestResponse|Aanvraag-en antwoord logboeken|No|
|Tracering|Traceer logboeken|No|


## <a name="microsoftcommunicationcommunicationservices"></a>Micro soft. Communication/CommunicationServices

|Categorie|Weergave naam categorie|Te exporteren kosten|
|---|---|---|
|ChatOperational|Operationele chat-logboeken|No|
|SMSOperational|Operationele SMS-logboeken|No|
|Gebruik|Gebruiks records|No|


## <a name="microsoftcontainerregistryregistries"></a>Micro soft. ContainerRegistry/registers

|Categorie|Weergave naam categorie|Te exporteren kosten|
|---|---|---|
|ContainerRegistryLoginEvents|Aanmeldings gebeurtenissen|No|
|ContainerRegistryRepositoryEvents|RepositoryEvent-logboeken|No|


## <a name="microsoftcontainerservicemanagedclusters"></a>Micro soft. container service/managedClusters

|Categorie|Weergave naam categorie|Te exporteren kosten|
|---|---|---|
|cluster-automatisch schalen|Kubernetes-cluster automatisch schalen|No|
|komen|komen|No|
|uitvoeren-apiserver|Kubernetes-API-server|No|
|uitvoeren-audit|Kubernetes-controle|No|
|uitvoeren-audit-admin|Kubernetes audit-beheer logboeken|No|
|uitvoeren-Controller-Manager|Kubernetes-controller beheer|No|
|uitvoeren-scheduler|Kubernetes scheduler|No|


## <a name="microsoftcustomprovidersresourceproviders"></a>Micro soft. CustomProviders/resourceproviders

|Categorie|Weergave naam categorie|Te exporteren kosten|
|---|---|---|
|Audit logs bevat|Controle logboeken voor MiniRP-aanroepen|No|


## <a name="microsoftd365customerinsightsinstances"></a>Micro soft. D365CustomerInsights/instances

|Categorie|Weergave naam categorie|Te exporteren kosten|
|---|---|---|
|Controleren|Controlegebeurtenissen|No|
|Operationeel|Operationele gebeurtenissen|No|


## <a name="microsoftdatabricksworkspaces"></a>Micro soft. Databricks/werk ruimten

|Categorie|Weergave naam categorie|Te exporteren kosten|
|---|---|---|
|accounts|Databricks-accounts|No|
|clusters|Databricks-clusters|No|
|dbfs|Databricks-bestandssysteem|No|
|instancePools|Instantie groepen|No|
|functies|Databricks-taken|No|
|notebook|Databricks Notebook|No|
|geheimen|Databricks geheimen|No|
|sqlPermissions|Databricks SQLPermissions|No|
|SSH|Databricks SSH|No|
|werkruimte|Databricks-werk ruimte|No|


## <a name="microsoftdatacollaborationworkspaces"></a>Micro soft. DataCollaboration/werk ruimten

|Categorie|Weergave naam categorie|Te exporteren kosten|
|---|---|---|
|CollaborationAudit|Samenwerkings controle|Yes|
|DataAssets|Gegevensassets|No|
|Pipelines|Pipelines|No|
|Nota's|Nota's|No|
|Scripts|Scripts|No|


## <a name="microsoftdatafactoryfactories"></a>Micro soft. DataFactory/fabrieken

|Categorie|Weergave naam categorie|Te exporteren kosten|
|---|---|---|
|ActivityRuns|Logboek voor uitvoering van pijplijn activiteit|No|
|PipelineRuns|Logboek voor uitvoering van pijp lijn|No|
|SSISIntegrationRuntimeLogs|Runtime-logboeken voor SSIS-integratie|No|
|SSISPackageEventMessageContext|Context van SSIS-pakket gebeurtenis bericht|No|
|SSISPackageEventMessages|Gebeurtenis berichten van SSIS-pakketten|No|
|SSISPackageExecutableStatistics|Uitvoer bare statistieken van SSIS-pakket|No|
|SSISPackageExecutionComponentPhases|Fasen van de uitvoering van SSIS-pakket|No|
|SSISPackageExecutionDataStatistics|Statistieken van SSIS package exeution-gegevens|No|
|TriggerRuns|Trigger uitvoeringen logboek|No|


## <a name="microsoftdatalakeanalyticsaccounts"></a>Micro soft. DataLakeAnalytics/accounts

|Categorie|Weergave naam categorie|Te exporteren kosten|
|---|---|---|
|Controleren|Auditlogboeken|No|
|Aanvragen|Logboeken aanvragen|No|


## <a name="microsoftdatalakestoreaccounts"></a>Micro soft. data Lake Store/accounts

|Categorie|Weergave naam categorie|Te exporteren kosten|
|---|---|---|
|Controleren|Auditlogboeken|No|
|Aanvragen|Logboeken aanvragen|No|


## <a name="microsoftdatashareaccounts"></a>Microsoft.DataShare/accounts

|Categorie|Weergave naam categorie|Te exporteren kosten|
|---|---|---|
|ReceivedShareSnapshots|Ontvangen moment opnamen van shares|No|
|SentShareSnapshots|Verzonden moment opnamen van shares|No|
|Shares|Shares|No|
|ShareSubscriptions|Abonnementen delen|No|


## <a name="microsoftdbformariadbservers"></a>Microsoft.DBforMariaDB/servers

|Categorie|Weergave naam categorie|Te exporteren kosten|
|---|---|---|
|MySqlAuditLogs|Controle logboeken voor MariaDB|No|
|MySqlSlowLogs|MariaDB-server logboeken|No|


## <a name="microsoftdbformysqlflexibleservers"></a>Microsoft.DBforMySQL/flexibleServers

|Categorie|Weergave naam categorie|Te exporteren kosten|
|---|---|---|
|MySqlAuditLogs|MySQL-controle logboeken|No|
|MySqlSlowLogs|Langzame logboeken voor MySQL|No|


## <a name="microsoftdbformysqlservers"></a>Microsoft.DBforMySQL/servers

|Categorie|Weergave naam categorie|Te exporteren kosten|
|---|---|---|
|MySqlAuditLogs|MySQL-controle logboeken|No|
|MySqlSlowLogs|MySQL-server logboeken|No|


## <a name="microsoftdbforpostgresqlflexibleservers"></a>Microsoft.DBforPostgreSQL/flexibleServers

|Categorie|Weergave naam categorie|Te exporteren kosten|
|---|---|---|
|PostgreSQLLogs|PostgreSQL-server logboeken|No|


## <a name="microsoftdbforpostgresqlservers"></a>Microsoft.DBforPostgreSQL/servers

|Categorie|Weergave naam categorie|Te exporteren kosten|
|---|---|---|
|PostgreSQLLogs|PostgreSQL-server logboeken|No|
|QueryStoreRuntimeStatistics|Runtime statistieken voor PostgreSQL query Store|No|
|QueryStoreWaitStatistics|Wacht statistieken voor PostgreSQL query Store|No|


## <a name="microsoftdbforpostgresqlserversv2"></a>Micro soft. DBforPostgreSQL/serversv2

|Categorie|Weergave naam categorie|Te exporteren kosten|
|---|---|---|
|PostgreSQLLogs|PostgreSQL-server logboeken|No|


## <a name="microsoftdesktopvirtualizationapplicationgroups"></a>Micro soft. DesktopVirtualization/applicationgroups

|Categorie|Weergave naam categorie|Te exporteren kosten|
|---|---|---|
|Controlepunt|Controlepunt|No|
|Fout|Fout|No|
|Beheer|Beheer|No|


## <a name="microsoftdesktopvirtualizationhostpools"></a>Micro soft. DesktopVirtualization/hostpools

|Categorie|Weergave naam categorie|Te exporteren kosten|
|---|---|---|
|AgentHealthStatus|AgentHealthStatus|No|
|Controlepunt|Controlepunt|No|
|Verbinding|Verbinding|No|
|Fout|Fout|No|
|HostRegistration|HostRegistration|No|
|Beheer|Beheer|No|


## <a name="microsoftdesktopvirtualizationworkspaces"></a>Micro soft. DesktopVirtualization/werk ruimten

|Categorie|Weergave naam categorie|Te exporteren kosten|
|---|---|---|
|Controlepunt|Controlepunt|No|
|Fout|Fout|No|
|Feed|Feed|No|
|Beheer|Beheer|No|


## <a name="microsoftdeviceselasticpoolsiothubtenants"></a>Micro soft. devices/ElasticPools/IotHubTenants

|Categorie|Weergave naam categorie|Te exporteren kosten|
|---|---|---|
|C2DCommands|C2D-opdrachten|No|
|C2DTwinOperations|Dubbele C2D-bewerkingen|No|
|Configuraties|Configuraties|No|
|Verbindingen|Verbindingen|No|
|D2CTwinOperations|D2CTwinOperations|No|
|DeviceIdentityOperations|Bewerkingen voor apparaat-Id's|No|
|DeviceStreams|Apparaatversleuteling (preview-versie)|No|
|DeviceTelemetry|Apparaattelemetrie|No|
|DirectMethods|Directe methoden|No|
|DistributedTracing|Gedistribueerde tracering (preview-versie)|No|
|FileUploadOperations|Bewerkingen voor het uploaden van bestanden|No|
|JobsOperations|Taak bewerkingen|No|
|Routes|Routes|No|
|TwinQueries|Dubbele Query's|No|


## <a name="microsoftdevicesiothubs"></a>Microsoft.Devices/IotHubs

|Categorie|Weergave naam categorie|Te exporteren kosten|
|---|---|---|
|C2DCommands|C2D-opdrachten|No|
|C2DTwinOperations|Dubbele C2D-bewerkingen|No|
|Configuraties|Configuraties|No|
|Verbindingen|Verbindingen|No|
|D2CTwinOperations|D2CTwinOperations|No|
|DeviceIdentityOperations|Bewerkingen voor apparaat-Id's|No|
|DeviceStreams|Apparaatversleuteling (preview-versie)|No|
|DeviceTelemetry|Apparaattelemetrie|No|
|DirectMethods|Directe methoden|No|
|DistributedTracing|Gedistribueerde tracering (preview-versie)|No|
|FileUploadOperations|Bewerkingen voor het uploaden van bestanden|No|
|JobsOperations|Taak bewerkingen|No|
|Routes|Routes|No|
|TwinQueries|Dubbele Query's|No|


## <a name="microsoftdevicesprovisioningservices"></a>Micro soft. devices/provisioningServices

|Categorie|Weergave naam categorie|Te exporteren kosten|
|---|---|---|
|DeviceOperations|Bewerkingen voor apparaten|No|
|ServiceOperations|Service bewerkingen|No|


## <a name="microsoftdigitaltwinsdigitaltwinsinstances"></a>Micro soft. DigitalTwins/digitalTwinsInstances

|Categorie|Weergave naam categorie|Te exporteren kosten|
|---|---|---|
|DigitalTwinsOperation|DigitalTwinsOperation|No|
|EventRoutesOperation|EventRoutesOperation|No|
|ModelsOperation|ModelsOperation|No|
|QueryOperation|QueryOperation|No|


## <a name="microsoftdocumentdbdatabaseaccounts"></a>Microsoft.DocumentDB/databaseAccounts

|Categorie|Weergave naam categorie|Te exporteren kosten|
|---|---|---|
|CassandraRequests|CassandraRequests|No|
|ControlPlaneRequests|ControlPlaneRequests|No|
|DataPlaneRequests|DataPlaneRequests|No|
|GremlinRequests|GremlinRequests|No|
|MongoRequests|MongoRequests|No|
|PartitionKeyRUConsumption|PartitionKeyRUConsumption|No|
|PartitionKeyStatistics|PartitionKeyStatistics|No|
|QueryRuntimeStatistics|QueryRuntimeStatistics|No|


## <a name="microsofteventgriddomains"></a>Micro soft. EventGrid/domeinen

|Categorie|Weergave naam categorie|Te exporteren kosten|
|---|---|---|
|DeliveryFailures|Fout logboeken voor bezorging|No|
|PublishFailures|Fout logboeken publiceren|No|


## <a name="microsofteventgridpartnernamespaces"></a>Micro soft. EventGrid/partnerNamespaces

|Categorie|Weergave naam categorie|Te exporteren kosten|
|---|---|---|
|DeliveryFailures|Fout logboeken voor bezorging|No|
|PublishFailures|Fout logboeken publiceren|No|


## <a name="microsofteventgridpartnertopics"></a>Micro soft. EventGrid/partnerTopics

|Categorie|Weergave naam categorie|Te exporteren kosten|
|---|---|---|
|DeliveryFailures|Fout logboeken voor bezorging|No|


## <a name="microsofteventgridsystemtopics"></a>Micro soft. EventGrid/systemTopics

|Categorie|Weergave naam categorie|Te exporteren kosten|
|---|---|---|
|DeliveryFailures|Fout logboeken voor bezorging|No|


## <a name="microsofteventgridtopics"></a>Micro soft. EventGrid/topics

|Categorie|Weergave naam categorie|Te exporteren kosten|
|---|---|---|
|DeliveryFailures|Fout logboeken voor bezorging|No|
|PublishFailures|Fout logboeken publiceren|No|


## <a name="microsofteventhubnamespaces"></a>Microsoft.EventHub/namespaces

|Categorie|Weergave naam categorie|Te exporteren kosten|
|---|---|---|
|ArchiveLogs|Archief logboeken|No|
|AutoScaleLogs|Logboeken automatisch schalen|No|
|CustomerManagedKeyUserLogs|Door de klant beheerde sleutel logboeken|No|
|EventHubVNetConnectionEvent|Logboeken voor VNet/IP-filtering verbindingen|No|
|KafkaCoordinatorLogs|Kafka Coordinator-logboeken|No|
|KafkaUserErrorLogs|Fout logboeken van Kafka-gebruikers|No|
|OperationalLogs|Operationele logboeken|No|


## <a name="microsoftexperimentationexperimentworkspaces"></a>micro soft. experimenten/experimentWorkspaces

|Categorie|Weergave naam categorie|Te exporteren kosten|
|---|---|---|
|Aanvraag|Aanvraag|No|


## <a name="microsofthealthcareapisservices"></a>Microsoft.HealthcareApis/services

|Categorie|Weergave naam categorie|Te exporteren kosten|
|---|---|---|
|Audit logs bevat|Auditlogboeken|No|


## <a name="microsoftinsightsautoscalesettings"></a>micro soft. Insights/autoscalesettings

|Categorie|Weergave naam categorie|Te exporteren kosten|
|---|---|---|
|AutoscaleEvaluations|Evaluaties automatisch schalen|No|
|AutoscaleScaleActions|Schaal acties automatisch schalen|No|


## <a name="microsoftinsightscomponents"></a>Micro soft. Insights/onderdelen

|Categorie|Weergave naam categorie|Te exporteren kosten|
|---|---|---|
|AppAvailabilityResults|Beschikbaarheids resultaten|No|
|AppBrowserTimings|Browser timing|No|
|AppDependencies|Afhankelijkheden|No|
|AppEvents|Gebeurtenissen|No|
|AppExceptions|Uitzonderingen|No|
|AppMetrics|Metrische gegevens|No|
|AppPageViews|Pagina weergaven|No|
|AppPerformanceCounters|Prestatiemeteritems|No|
|AppRequests|Aanvragen|No|
|AppSystemEvents|Systeem gebeurtenissen|No|
|AppTraces|Traceringen|No|


## <a name="microsoftiotspacesgraph"></a>Micro soft. IoTSpaces/Graph

|Categorie|Weergave naam categorie|Te exporteren kosten|
|---|---|---|
|Controleren|Controleren|No|
|Uitgaand verkeer|Uitgaand verkeer|No|
|Inkomend verkeer|Inkomend verkeer|No|
|Operationeel|Operationeel|No|
|Tracering|Tracering|No|
|UserDefinedFunction|UserDefinedFunction|No|


## <a name="microsoftkeyvaultmanagedhsms"></a>micro soft. managedhsms

|Categorie|Weergave naam categorie|Te exporteren kosten|
|---|---|---|
|AuditEvent|Controlegebeurtenis|No|


## <a name="microsoftkeyvaultvaults"></a>Microsoft.KeyVault/vaults

|Categorie|Weergave naam categorie|Te exporteren kosten|
|---|---|---|
|AuditEvent|Auditlogboeken|No|


## <a name="microsoftkustoclusters"></a>Micro soft. Kusto/clusters

|Categorie|Weergave naam categorie|Te exporteren kosten|
|---|---|---|
|Opdracht|Opdracht|No|
|FailedIngestion|Mislukte opname bewerkingen|No|
|IngestionBatching|Opnamebatchverwerking|No|
|Query’s uitvoeren|Query’s uitvoeren|No|
|SucceededIngestion|Geslaagde opname bewerkingen|No|
|TableDetails|Tabel Details|No|
|TableUsageStatistics|Statistieken voor tabel gebruik|No|


## <a name="microsoftlogicintegrationaccounts"></a>Micro soft. Logic/integrationAccounts

|Categorie|Weergave naam categorie|Te exporteren kosten|
|---|---|---|
|IntegrationAccountTrackingEvents|Spoor gebeurtenissen voor integratie account|No|


## <a name="microsoftlogicworkflows"></a>Microsoft.Logic/workflows

|Categorie|Weergave naam categorie|Te exporteren kosten|
|---|---|---|
|WorkflowRuntime|Diagnostische gebeurtenissen voor workflowruntime|No|


## <a name="microsoftmachinelearningservicesworkspaces"></a>Micro soft. MachineLearningServices/werk ruimten

|Categorie|Weergave naam categorie|Te exporteren kosten|
|---|---|---|
|AmlComputeClusterEvent|AmlComputeClusterEvent|No|
|AmlComputeClusterNodeEvent|AmlComputeClusterNodeEvent|No|
|AmlComputeCpuGpuUtilization|AmlComputeCpuGpuUtilization|No|
|AmlComputeJobEvent|AmlComputeJobEvent|No|
|AmlRunStatusChangedEvent|AmlRunStatusChangedEvent|No|


## <a name="microsoftmediamediaservices"></a>Micro soft. Media/Media Services

|Categorie|Weergave naam categorie|Te exporteren kosten|
|---|---|---|
|KeyDeliveryRequests|Aanvragen voor sleutel levering|No|


## <a name="microsoftnetworkapplicationgateways"></a>Micro soft. Network/applicationGateways

|Categorie|Weergave naam categorie|Te exporteren kosten|
|---|---|---|
|ApplicationGatewayAccessLog|Application Gateway Access-logboek|No|
|ApplicationGatewayFirewallLog|Application Gateway firewall-logboek|No|
|ApplicationGatewayPerformanceLog|Prestatie logboek Application Gateway|No|


## <a name="microsoftnetworkazurefirewalls"></a>Micro soft. Network/azurefirewalls

|Categorie|Weergave naam categorie|Te exporteren kosten|
|---|---|---|
|AzureFirewallApplicationRule|Toepassings regel Azure Firewall|No|
|AzureFirewallDnsProxy|DNS-proxy Azure Firewall|No|
|AzureFirewallNetworkRule|Azure Firewall netwerk regel|No|


## <a name="microsoftnetworkbastionhosts"></a>Microsoft.Network/bastionHosts

|Categorie|Weergave naam categorie|Te exporteren kosten|
|---|---|---|
|BastionAuditLogs|Controle logboeken voor Bastion|No|


## <a name="microsoftnetworkexpressroutecircuits"></a>Microsoft.Network/expressRouteCircuits

|Categorie|Weergave naam categorie|Te exporteren kosten|
|---|---|---|
|PeeringRouteLog|Logboeken voor peering route tabel|No|


## <a name="microsoftnetworkfrontdoors"></a>Micro soft. Network/frontdoors

|Categorie|Weergave naam categorie|Te exporteren kosten|
|---|---|---|
|FrontdoorAccessLog|-Ingang-toegangs logboek|No|
|FrontdoorWebApplicationFirewallLog|-Ingang Web Application firewall-logboek|No|


## <a name="microsoftnetworkloadbalancers"></a>Microsoft.Network/loadBalancers

|Categorie|Weergave naam categorie|Te exporteren kosten|
|---|---|---|
|LoadBalancerAlertEvent|Load Balancer waarschuwings gebeurtenissen|No|
|LoadBalancerProbeHealthStatus|Load Balancer test status|No|


## <a name="microsoftnetworknetworksecuritygroups"></a>Micro soft. Network/networksecuritygroups

|Categorie|Weergave naam categorie|Te exporteren kosten|
|---|---|---|
|NetworkSecurityGroupEvent|Gebeurtenis netwerk beveiligings groep|No|
|NetworkSecurityGroupFlowEvent|Stroom gebeurtenis van regel voor netwerk beveiligings groep|No|
|NetworkSecurityGroupRuleCounter|Teller van regel voor netwerk beveiligings groep|No|


## <a name="microsoftnetworkp2svpngateways"></a>Micro soft. Network/p2sVpnGateways

|Categorie|Weergave naam categorie|Te exporteren kosten|
|---|---|---|
|GatewayDiagnosticLog|Diagnostische logboeken van de gateway|No|
|IKEDiagnosticLog|Diagnostische logboeken voor IKE|No|
|P2SDiagnosticLog|Diagnostische logboeken van P2S|No|


## <a name="microsoftnetworkpublicipaddresses"></a>Micro soft. Network/publicIPAddresses

|Categorie|Weergave naam categorie|Te exporteren kosten|
|---|---|---|
|DDoSMitigationFlowLogs|Stroom logboeken van DDoS-oplossings beslissingen|No|
|DDoSMitigationReports|Rapporten met DDoS-oplossingen|No|
|DDoSProtectionNotifications|DDoS-beveiligings meldingen|No|


## <a name="microsoftnetworktrafficmanagerprofiles"></a>Microsoft.Network/trafficManagerProfiles

|Categorie|Weergave naam categorie|Te exporteren kosten|
|---|---|---|
|ProbeHealthStatusEvents|Gebeurtenis met resultaten van Traffic Manager test status|No|


## <a name="microsoftnetworkvirtualnetworkgateways"></a>Microsoft.Network/virtualNetworkGateways

|Categorie|Weergave naam categorie|Te exporteren kosten|
|---|---|---|
|GatewayDiagnosticLog|Diagnostische logboeken van de gateway|No|
|IKEDiagnosticLog|Diagnostische logboeken voor IKE|No|
|P2SDiagnosticLog|Diagnostische logboeken van P2S|No|
|RouteDiagnosticLog|Diagnostische logboeken voor route ring|No|
|TunnelDiagnosticLog|Diagnostische logboeken voor tunnel|No|


## <a name="microsoftnetworkvirtualnetworks"></a>Microsoft.Network/virtualNetworks

|Categorie|Weergave naam categorie|Te exporteren kosten|
|---|---|---|
|VMProtectionAlerts|Waarschuwingen voor VM-beveiliging|No|


## <a name="microsoftnetworkvpngateways"></a>Micro soft. Network/vpnGateways

|Categorie|Weergave naam categorie|Te exporteren kosten|
|---|---|---|
|GatewayDiagnosticLog|Diagnostische logboeken van de gateway|No|
|IKEDiagnosticLog|Diagnostische logboeken voor IKE|No|
|RouteDiagnosticLog|Diagnostische logboeken voor route ring|No|
|TunnelDiagnosticLog|Diagnostische logboeken voor tunnel|No|


## <a name="microsoftnotificationhubsnamespaces"></a>Microsoft.NotificationHubs/namespaces

|Categorie|Weergave naam categorie|Te exporteren kosten|
|---|---|---|
|OperationalLogs|Operationele logboeken|No|


## <a name="microsoftoperationalinsightsworkspaces"></a>Microsoft.OperationalInsights/workspaces

|Categorie|Weergave naam categorie|Te exporteren kosten|
|---|---|---|
|Controleren|Auditlogboeken|No|


## <a name="microsoftpowerbitenants"></a>Micro soft. PowerBI/tenants

|Categorie|Weergave naam categorie|Te exporteren kosten|
|---|---|---|
|Engine|Engine|No|


## <a name="microsoftpowerbitenantsworkspaces"></a>Micro soft. PowerBI/tenants/werk ruimten

|Categorie|Weergave naam categorie|Te exporteren kosten|
|---|---|---|
|Engine|Engine|No|


## <a name="microsoftpowerbidedicatedcapacities"></a>Micro soft. PowerBIDedicated/capaciteiten

|Categorie|Weergave naam categorie|Te exporteren kosten|
|---|---|---|
|Engine|Engine|No|


## <a name="microsoftprojectbabylonaccounts"></a>Micro soft. ProjectBabylon/accounts

|Categorie|Weergave naam categorie|Te exporteren kosten|
|---|---|---|
|ScanStatusLogEvent|ScanStatus|No|


## <a name="microsoftpurviewaccounts"></a>micro soft. controle sfeer liggen/accounts

|Categorie|Weergave naam categorie|Te exporteren kosten|
|---|---|---|
|ScanStatusLogEvent|ScanStatus|No|


## <a name="microsoftrecoveryservicesvaults"></a>Micro soft. Recovery Services/kluizen

|Categorie|Weergave naam categorie|Te exporteren kosten|
|---|---|---|
|AddonAzureBackupAlerts|Azure Backup waarschuwings gegevens van invoeg toepassing|No|
|AddonAzureBackupJobs|Taak gegevens van invoeg toepassing Azure Backup|No|
|AddonAzureBackupPolicy|Azure Backup beleids gegevens van invoeg toepassing|No|
|AddonAzureBackupProtectedInstance|Invoeg toepassing Azure Backup beveiligde instantie gegevens|No|
|AddonAzureBackupStorage|Invoeg Azure Backup opslag gegevens|No|
|AzureBackupReport|Azure Backup rapportage gegevens|No|
|AzureSiteRecoveryEvents|Azure Site Recovery gebeurtenissen|No|
|AzureSiteRecoveryJobs|Azure Site Recovery taken|No|
|AzureSiteRecoveryProtectedDiskDataChurn|Gegevens verloop van beveiligde schijf Azure Site Recovery|No|
|AzureSiteRecoveryRecoveryPoints|Azure Site Recovery herstel punten|No|
|AzureSiteRecoveryReplicatedItems|Gerepliceerde items Azure Site Recovery|No|
|AzureSiteRecoveryReplicationDataUploadRate|Upload frequentie van Azure Site Recovery replicatie gegevens|No|
|AzureSiteRecoveryReplicationStats|Replicatie statistieken Azure Site Recovery|No|
|CoreAzureBackup|Kern Azure Backup gegevens|No|


## <a name="microsoftrelaynamespaces"></a>Micro soft. relay/naam ruimten

|Categorie|Weergave naam categorie|Te exporteren kosten|
|---|---|---|
|HybridConnectionsEvent|HybridConnections-gebeurtenissen|No|
|HybridConnectionsLogs|HybridConnectionsLogs|No|


## <a name="microsoftsearchsearchservices"></a>Micro soft. Search/searchServices

|Categorie|Weergave naam categorie|Te exporteren kosten|
|---|---|---|
|OperationLogs|Bewerkings logboeken|No|


## <a name="microsoftservicebusnamespaces"></a>Microsoft.ServiceBus/namespaces

|Categorie|Weergave naam categorie|Te exporteren kosten|
|---|---|---|
|OperationalLogs|Operationele logboeken|No|


## <a name="microsoftsignalrservicesignalr"></a>Microsoft.SignalRService/SignalR

|Categorie|Weergave naam categorie|Te exporteren kosten|
|---|---|---|
|AllLogs|Logboeken van de Azure signalerings service.|No|


## <a name="microsoftsqlmanagedinstances"></a>Micro soft. SQL/managedInstances

|Categorie|Weergave naam categorie|Te exporteren kosten|
|---|---|---|
|DevOpsOperationsAudit|Audit logboeken voor Devops-bewerkingen|No|
|ResourceUsageStats|Resource gebruiks statistieken|No|
|SQLSecurityAuditEvents|SQL-beveiligings controle gebeurtenis|No|


## <a name="microsoftsqlmanagedinstancesdatabases"></a>Micro soft. SQL/managedInstances/data bases

|Categorie|Weergave naam categorie|Te exporteren kosten|
|---|---|---|
|Fouten|Fouten|No|
|QueryStoreRuntimeStatistics|Runtime statistieken voor query Store|No|
|QueryStoreWaitStatistics|Wacht statistieken voor query Store|No|
|SQLInsights|SQL Insights|No|


## <a name="microsoftsqlserversdatabases"></a>Microsoft.Sql/servers/databases

|Categorie|Weergave naam categorie|Te exporteren kosten|
|---|---|---|
|AutomaticTuning|Automatisch instellen|No|
|Block|Block|No|
|DatabaseWaitStatistics|Data base-wacht statistieken|No|
|Impasses|Impasses|No|
|DevOpsOperationsAudit|Audit logboeken voor Devops-bewerkingen|No|
|DmsWorkers|DMS-werk rollen|No|
|Fouten|Fouten|No|
|ExecRequests|Exec-aanvragen|No|
|QueryStoreRuntimeStatistics|Runtime statistieken voor query Store|No|
|QueryStoreWaitStatistics|Wacht statistieken voor query Store|No|
|RequestSteps|Stappen voor aanvragen|No|
|SQLInsights|SQL Insights|No|
|SqlRequests|SQL-aanvragen|No|
|SQLSecurityAuditEvents|SQL-beveiligings controle gebeurtenis|No|
|Time-outs|Time-outs|No|
|Wacht|Wacht|No|


## <a name="microsoftstoragestorageaccountsblobservices"></a>Micro soft. Storage/Storage accounts/blobServices

|Categorie|Weergave naam categorie|Te exporteren kosten|
|---|---|---|
|StorageDelete|StorageDelete|Yes|
|StorageRead|StorageRead|Yes|
|StorageWrite|StorageWrite|Yes|


## <a name="microsoftstoragestorageaccountsfileservices"></a>Micro soft. Storage/Storage accounts/fileServices

|Categorie|Weergave naam categorie|Te exporteren kosten|
|---|---|---|
|StorageDelete|StorageDelete|Yes|
|StorageRead|StorageRead|Yes|
|StorageWrite|StorageWrite|Yes|


## <a name="microsoftstoragestorageaccountsqueueservices"></a>Micro soft. Storage/Storage accounts/queueServices

|Categorie|Weergave naam categorie|Te exporteren kosten|
|---|---|---|
|StorageDelete|StorageDelete|Yes|
|StorageRead|StorageRead|Yes|
|StorageWrite|StorageWrite|Yes|


## <a name="microsoftstoragestorageaccountstableservices"></a>Micro soft. Storage/Storage accounts/tableServices

|Categorie|Weergave naam categorie|Te exporteren kosten|
|---|---|---|
|StorageDelete|StorageDelete|Yes|
|StorageRead|StorageRead|Yes|
|StorageWrite|StorageWrite|Yes|


## <a name="microsoftstreamanalyticsstreamingjobs"></a>Micro soft. StreamAnalytics/streamingjobs

|Categorie|Weergave naam categorie|Te exporteren kosten|
|---|---|---|
|Ontwerpen|Ontwerpen|No|
|Uitvoering|Uitvoering|No|


## <a name="microsoftsynapseworkspaces"></a>Micro soft. Synapse/werk ruimten

|Categorie|Weergave naam categorie|Te exporteren kosten|
|---|---|---|
|BuiltinSqlReqsEnded|De ingebouwde aanvragen van SQL-groep zijn beëindigd|No|
|GatewayApiRequests|Synapse Gateway-API-aanvragen|No|
|SQLSecurityAuditEvents|SQL-beveiligings controle gebeurtenis|No|
|SynapseRbacOperations|Synapse RBAC-bewerkingen|No|


## <a name="microsoftsynapseworkspacesbigdatapools"></a>Micro soft. Synapse/werk ruimten/bigDataPools

|Categorie|Weergave naam categorie|Te exporteren kosten|
|---|---|---|
|BigDataPoolAppsEnded|Toepassingen voor Big Data-groep gestopt|No|


## <a name="microsoftsynapseworkspacessqlpools"></a>Micro soft. Synapse/werk ruimten/sqlPools

|Categorie|Weergave naam categorie|Te exporteren kosten|
|---|---|---|
|DmsWorkers|DMS-werk rollen|No|
|ExecRequests|Exec-aanvragen|No|
|RequestSteps|Stappen voor aanvragen|No|
|SqlRequests|SQL-aanvragen|No|
|SQLSecurityAuditEvents|SQL-beveiligings controle gebeurtenis|No|
|Wacht|Wacht|No|


## <a name="microsofttimeseriesinsightsenvironments"></a>Micro soft. TimeSeriesInsights/omgevingen

|Categorie|Weergave naam categorie|Te exporteren kosten|
|---|---|---|
|Inkomend verkeer|Inkomend verkeer|No|
|Beheer|Beheer|No|


## <a name="microsofttimeseriesinsightsenvironmentseventsources"></a>Micro soft. TimeSeriesInsights/omgevingen/eventsources

|Categorie|Weergave naam categorie|Te exporteren kosten|
|---|---|---|
|Inkomend verkeer|Inkomend verkeer|No|
|Beheer|Beheer|No|


## <a name="microsoftwebhostingenvironments"></a>micro soft. Web/hostingenvironments

|Categorie|Weergave naam categorie|Te exporteren kosten|
|---|---|---|
|AppServiceEnvironmentPlatformLogs|App Service Environment-platform logboeken|No|


## <a name="microsoftwebsites"></a>micro soft. web/sites

|Categorie|Weergave naam categorie|Te exporteren kosten|
|---|---|---|
|AppServiceAntivirusScanAuditLogs|Audit logboeken voor antivirus Programma's melden|No|
|AppServiceAppLogs|Toepassings logboeken App Service|No|
|AppServiceAuditLogs|Access-controle logboeken|No|
|AppServiceConsoleLogs|App Service-console logboeken|No|
|AppServiceFileAuditLogs|Controle logboeken voor het wijzigen van de site-inhoud|No|
|AppServiceHTTPLogs|HTTP-logboeken|No|
|AppServiceIPSecAuditLogs|Controle logboeken voor IPSecurity|No|
|AppServicePlatformLogs|App Service-platform logboeken|No|
|FunctionAppLogs|Toepassings logboeken van functie|No|


## <a name="microsoftwebsitesslots"></a>micro soft. web/sites/sleuven

|Categorie|Weergave naam categorie|Te exporteren kosten|
|---|---|---|
|AppServiceAntivirusScanAuditLogs|Audit logboeken voor antivirus Programma's melden|No|
|AppServiceAppLogs|Toepassings logboeken App Service|No|
|AppServiceAuditLogs|Access-controle logboeken|No|
|AppServiceConsoleLogs|App Service-console logboeken|No|
|AppServiceFileAuditLogs|Controle logboeken voor het wijzigen van de site-inhoud|No|
|AppServiceHTTPLogs|HTTP-logboeken|No|
|AppServiceIPSecAuditLogs|Controle logboeken voor IPSecurity|No|
|AppServicePlatformLogs|App Service-platform logboeken|No|
|FunctionAppLogs|Toepassings logboeken van functie|No|



## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over bron logboeken](../essentials/platform-logs-overview.md)
* [Resource bron logboeken streamen naar **Event hubs**](./resource-logs.md#send-to-azure-event-hubs)
* [Diagnostische instellingen voor bron logboek wijzigen met behulp van de Azure Monitor REST API](/rest/api/monitor/diagnosticsettings)
* [Logboeken analyseren vanuit Azure Storage met Log Analytics](./resource-logs.md#send-to-log-analytics-workspace)

