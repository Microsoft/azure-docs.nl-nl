---
title: Azure Monitor resource logboeken ondersteunde services en categorieën
description: Verwijzing van Azure Monitor inzicht krijgen in de ondersteunde services en het gebeurtenis schema voor Azure-resource Logboeken.
ms.topic: reference
ms.date: 01/29/2021
ms.openlocfilehash: 9a04d0f470522dd4689d604756ffd25e70c5d456
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102033143"
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
|AccountLogon|AccountLogon|Nee|
|AccountManagement|AccountManagement|Nee|
|DetailTracking|DetailTracking|Nee|
|DirectoryServiceAccess|DirectoryServiceAccess|Nee|
|LogonLogoff|LogonLogoff|Nee|
|ObjectAccess|ObjectAccess|Nee|
|PolicyChange|PolicyChange|Nee|
|PrivilegeUse|PrivilegeUse|Nee|
|SystemSecurity|SystemSecurity|Nee|


## <a name="microsoftanalysisservicesservers"></a>Micro soft. AnalysisServices/servers

|Categorie|Weergave naam categorie|Te exporteren kosten|
|---|---|---|
|Engine|Engine|Nee|
|Service|Service|Nee|


## <a name="microsoftapimanagementservice"></a>Microsoft.ApiManagement/service

|Categorie|Weergave naam categorie|Te exporteren kosten|
|---|---|---|
|Gateway logs|Logboeken gerelateerd aan de ApiManagement-gateway|Nee|


## <a name="microsoftappconfigurationconfigurationstores"></a>Micro soft. AppConfiguration/configurationStores

|Categorie|Weergave naam categorie|Te exporteren kosten|
|---|---|---|
|HttpRequest|HTTP-aanvragen|Ja|


## <a name="microsoftappplatformspring"></a>Micro soft. AppPlatform/lente

|Categorie|Weergave naam categorie|Te exporteren kosten|
|---|---|---|
|ApplicationConsole|Toepassings console|Nee|
|SystemLogs|Systeem logboeken|Nee|


## <a name="microsoftattestationattestationproviders"></a>Microsoft.Attestation/attestationProviders

|Categorie|Weergave naam categorie|Te exporteren kosten|
|---|---|---|
|AuditEvent|Audit event-berichten logboek categorie.|Nee|
|Gamepad|Logboek categorie voor fout berichten.|Nee|
|INF|Informatief berichten logboek categorie.|Nee|
|WRSCH|Waarschuwings bericht categorie.|Nee|


## <a name="microsoftautomationautomationaccounts"></a>Micro soft. Automation/automationAccounts

|Categorie|Weergave naam categorie|Te exporteren kosten|
|---|---|---|
|DscNodeStatus|DSC-knooppunt status|Nee|
|JobLogs|Taak logboeken|Nee|
|JobStreams|Taak stromen|Nee|


## <a name="microsoftbatchbatchaccounts"></a>Microsoft.Bat-CH/batchAccounts

|Categorie|Weergave naam categorie|Te exporteren kosten|
|---|---|---|
|ServiceLog|Service logboeken|Nee|


## <a name="microsoftbatchaiworkspaces"></a>Microsoft.BatchAI/werk ruimten

|Categorie|Weergave naam categorie|Te exporteren kosten|
|---|---|---|
|BaiClusterEvent|BaiClusterEvent|Nee|
|BaiClusterNodeEvent|BaiClusterNodeEvent|Nee|
|BaiJobEvent|BaiJobEvent|Nee|


## <a name="microsoftblockchainblockchainmembers"></a>Microsoft.Blockchain/blockchainMembers

|Categorie|Weergave naam categorie|Te exporteren kosten|
|---|---|---|
|BlockchainApplication|Block Chain-toepassing|Nee|
|FabricOrderer|Fabric-Orderer|Nee|
|FabricPeer|Infrastructuur peer|Nee|
|Proxy|Proxy|Nee|


## <a name="microsoftblockchaincordamembers"></a>Micro soft. Block Chain/cordaMembers

|Categorie|Weergave naam categorie|Te exporteren kosten|
|---|---|---|
|BlockchainApplication|Block Chain-toepassing|Nee|


## <a name="microsoftbotservicebotservices"></a>micro soft. botservice/botservices

|Categorie|Weergave naam categorie|Te exporteren kosten|
|---|---|---|
|BotRequest|Aanvragen van de kanalen naar de bot|Nee|
|DependencyRequest|Aanvragen voor afhankelijkheden|Nee|


## <a name="microsoftcdncdnwebapplicationfirewallpolicies"></a>Micro soft. CDN/cdnwebapplicationfirewallpolicies

|Categorie|Weergave naam categorie|Te exporteren kosten|
|---|---|---|
|WebApplicationFirewallLogs|Web-webtoepassingsbestanden firewall-logboeken|Nee|


## <a name="microsoftcdnprofiles"></a>Microsoft.Cdn/profiles

|Categorie|Weergave naam categorie|Te exporteren kosten|
|---|---|---|
|AzureCdnAccessLog|Azure CDN-toegangs logboek|Nee|
|FrontDoorAccessLog|-Ingang-toegangs logboek|Ja|
|FrontDoorHealthProbeLog|-Ingang Health-test logboek|Ja|
|FrontDoorWebApplicationFirewallLog|-Ingang WebApplicationFirewall-logboek|Ja|


## <a name="microsoftcdnprofilesendpoints"></a>Micro soft. CDN/profielen/eind punten

|Categorie|Weergave naam categorie|Te exporteren kosten|
|---|---|---|
|CoreAnalytics|Hiermee worden de metrische gegevens van het eind punt opgehaald, bijvoorbeeld band breedte, uitgaand verkeer enzovoort.|Nee|


## <a name="microsoftclassicnetworknetworksecuritygroups"></a>Micro soft. ClassicNetwork/networksecuritygroups

|Categorie|Weergave naam categorie|Te exporteren kosten|
|---|---|---|
|Stroom gebeurtenis van regel voor netwerk beveiligings groep|Stroom gebeurtenis van regel voor netwerk beveiligings groep|Nee|


## <a name="microsoftcognitiveservicesaccounts"></a>Micro soft. CognitiveServices/accounts

|Categorie|Weergave naam categorie|Te exporteren kosten|
|---|---|---|
|Controleren|Auditlogboeken|Nee|
|RequestResponse|Aanvraag-en antwoord logboeken|Nee|
|Tracering|Traceer logboeken|Nee|


## <a name="microsoftcommunicationcommunicationservices"></a>Micro soft. Communication/CommunicationServices

|Categorie|Weergave naam categorie|Te exporteren kosten|
|---|---|---|
|ChatOperational|Operationele chat-logboeken|Nee|
|SMSOperational|Operationele SMS-logboeken|Nee|
|Gebruik|Gebruiks records|Nee|


## <a name="microsoftcontainerregistryregistries"></a>Micro soft. ContainerRegistry/registers

|Categorie|Weergave naam categorie|Te exporteren kosten|
|---|---|---|
|ContainerRegistryLoginEvents|Aanmeldings gebeurtenissen|Nee|
|ContainerRegistryRepositoryEvents|RepositoryEvent-logboeken|Nee|


## <a name="microsoftcontainerservicemanagedclusters"></a>Micro soft. container service/managedClusters

|Categorie|Weergave naam categorie|Te exporteren kosten|
|---|---|---|
|cluster-automatisch schalen|Kubernetes-cluster automatisch schalen|Nee|
|komen|komen|Nee|
|uitvoeren-apiserver|Kubernetes-API-server|Nee|
|uitvoeren-audit|Kubernetes-controle|Nee|
|uitvoeren-audit-admin|Kubernetes audit-beheer logboeken|Nee|
|uitvoeren-Controller-Manager|Kubernetes-controller beheer|Nee|
|uitvoeren-scheduler|Kubernetes scheduler|Nee|


## <a name="microsoftcustomprovidersresourceproviders"></a>Micro soft. CustomProviders/resourceproviders

|Categorie|Weergave naam categorie|Te exporteren kosten|
|---|---|---|
|Audit logs bevat|Controle logboeken voor MiniRP-aanroepen|Nee|


## <a name="microsoftd365customerinsightsinstances"></a>Micro soft. D365CustomerInsights/instances

|Categorie|Weergave naam categorie|Te exporteren kosten|
|---|---|---|
|Controleren|Controlegebeurtenissen|Nee|
|Operationeel|Operationele gebeurtenissen|Nee|


## <a name="microsoftdatabricksworkspaces"></a>Micro soft. Databricks/werk ruimten

|Categorie|Weergave naam categorie|Te exporteren kosten|
|---|---|---|
|accounts|Databricks-accounts|Nee|
|clusters|Databricks-clusters|Nee|
|dbfs|Databricks-bestandssysteem|Nee|
|instancePools|Instantie groepen|Nee|
|functies|Databricks-taken|Nee|
|notebook|Databricks Notebook|Nee|
|geheimen|Databricks geheimen|Nee|
|sqlPermissions|Databricks SQLPermissions|Nee|
|SSH|Databricks SSH|Nee|
|werkruimte|Databricks-werk ruimte|Nee|


## <a name="microsoftdatacollaborationworkspaces"></a>Micro soft. DataCollaboration/werk ruimten

|Categorie|Weergave naam categorie|Te exporteren kosten|
|---|---|---|
|CollaborationAudit|Samenwerkings controle|Ja|
|DataAssets|Gegevensassets|Nee|
|Pipelines|Pipelines|Nee|
|Nota's|Nota's|Nee|
|Scripts|Scripts|Nee|


## <a name="microsoftdatafactoryfactories"></a>Micro soft. DataFactory/fabrieken

|Categorie|Weergave naam categorie|Te exporteren kosten|
|---|---|---|
|ActivityRuns|Logboek voor uitvoering van pijplijn activiteit|Nee|
|PipelineRuns|Logboek voor uitvoering van pijp lijn|Nee|
|SSISIntegrationRuntimeLogs|Runtime-logboeken voor SSIS-integratie|Nee|
|SSISPackageEventMessageContext|Context van SSIS-pakket gebeurtenis bericht|Nee|
|SSISPackageEventMessages|Gebeurtenis berichten van SSIS-pakketten|Nee|
|SSISPackageExecutableStatistics|Uitvoer bare statistieken van SSIS-pakket|Nee|
|SSISPackageExecutionComponentPhases|Fasen van de uitvoering van SSIS-pakket|Nee|
|SSISPackageExecutionDataStatistics|Statistieken van SSIS package exeution-gegevens|Nee|
|TriggerRuns|Trigger uitvoeringen logboek|Nee|


## <a name="microsoftdatalakeanalyticsaccounts"></a>Micro soft. DataLakeAnalytics/accounts

|Categorie|Weergave naam categorie|Te exporteren kosten|
|---|---|---|
|Controleren|Auditlogboeken|Nee|
|Aanvragen|Logboeken aanvragen|Nee|


## <a name="microsoftdatalakestoreaccounts"></a>Micro soft. data Lake Store/accounts

|Categorie|Weergave naam categorie|Te exporteren kosten|
|---|---|---|
|Controleren|Auditlogboeken|Nee|
|Aanvragen|Logboeken aanvragen|Nee|


## <a name="microsoftdatashareaccounts"></a>Microsoft.DataShare/accounts

|Categorie|Weergave naam categorie|Te exporteren kosten|
|---|---|---|
|ReceivedShareSnapshots|Ontvangen moment opnamen van shares|Nee|
|SentShareSnapshots|Verzonden moment opnamen van shares|Nee|
|Shares|Shares|Nee|
|ShareSubscriptions|Abonnementen delen|Nee|


## <a name="microsoftdbformariadbservers"></a>Microsoft.DBforMariaDB/servers

|Categorie|Weergave naam categorie|Te exporteren kosten|
|---|---|---|
|MySqlAuditLogs|Controle logboeken voor MariaDB|Nee|
|MySqlSlowLogs|MariaDB-server logboeken|Nee|


## <a name="microsoftdbformysqlflexibleservers"></a>Microsoft.DBforMySQL/flexibleServers

|Categorie|Weergave naam categorie|Te exporteren kosten|
|---|---|---|
|MySqlAuditLogs|MySQL-controle logboeken|Nee|
|MySqlSlowLogs|Langzame logboeken voor MySQL|Nee|


## <a name="microsoftdbformysqlservers"></a>Microsoft.DBforMySQL/servers

|Categorie|Weergave naam categorie|Te exporteren kosten|
|---|---|---|
|MySqlAuditLogs|MySQL-controle logboeken|Nee|
|MySqlSlowLogs|MySQL-server logboeken|Nee|


## <a name="microsoftdbforpostgresqlflexibleservers"></a>Microsoft.DBforPostgreSQL/flexibleServers

|Categorie|Weergave naam categorie|Te exporteren kosten|
|---|---|---|
|PostgreSQLLogs|PostgreSQL-server logboeken|Nee|


## <a name="microsoftdbforpostgresqlservers"></a>Microsoft.DBforPostgreSQL/servers

|Categorie|Weergave naam categorie|Te exporteren kosten|
|---|---|---|
|PostgreSQLLogs|PostgreSQL-server logboeken|Nee|
|QueryStoreRuntimeStatistics|Runtime statistieken voor PostgreSQL query Store|Nee|
|QueryStoreWaitStatistics|Wacht statistieken voor PostgreSQL query Store|Nee|


## <a name="microsoftdbforpostgresqlserversv2"></a>Micro soft. DBforPostgreSQL/serversv2

|Categorie|Weergave naam categorie|Te exporteren kosten|
|---|---|---|
|PostgreSQLLogs|PostgreSQL-server logboeken|Nee|


## <a name="microsoftdesktopvirtualizationapplicationgroups"></a>Micro soft. DesktopVirtualization/applicationgroups

|Categorie|Weergave naam categorie|Te exporteren kosten|
|---|---|---|
|Controlepunt|Controlepunt|Nee|
|Fout|Fout|Nee|
|Beheer|Beheer|Nee|


## <a name="microsoftdesktopvirtualizationhostpools"></a>Micro soft. DesktopVirtualization/hostpools

|Categorie|Weergave naam categorie|Te exporteren kosten|
|---|---|---|
|AgentHealthStatus|AgentHealthStatus|Nee|
|Controlepunt|Controlepunt|Nee|
|Verbinding|Verbinding|Nee|
|Fout|Fout|Nee|
|HostRegistration|HostRegistration|Nee|
|Beheer|Beheer|Nee|


## <a name="microsoftdesktopvirtualizationworkspaces"></a>Micro soft. DesktopVirtualization/werk ruimten

|Categorie|Weergave naam categorie|Te exporteren kosten|
|---|---|---|
|Controlepunt|Controlepunt|Nee|
|Fout|Fout|Nee|
|Feed|Feed|Nee|
|Beheer|Beheer|Nee|


## <a name="microsoftdeviceselasticpoolsiothubtenants"></a>Micro soft. devices/ElasticPools/IotHubTenants

|Categorie|Weergave naam categorie|Te exporteren kosten|
|---|---|---|
|C2DCommands|C2D-opdrachten|Nee|
|C2DTwinOperations|Dubbele C2D-bewerkingen|Nee|
|Configuraties|Configuraties|Nee|
|Verbindingen|Verbindingen|Nee|
|D2CTwinOperations|D2CTwinOperations|Nee|
|DeviceIdentityOperations|Bewerkingen voor apparaat-Id's|Nee|
|DeviceStreams|Apparaatversleuteling (preview-versie)|Nee|
|DeviceTelemetry|Apparaattelemetrie|Nee|
|DirectMethods|Directe methoden|Nee|
|DistributedTracing|Gedistribueerde tracering (preview-versie)|Nee|
|FileUploadOperations|Bewerkingen voor het uploaden van bestanden|Nee|
|JobsOperations|Taak bewerkingen|Nee|
|Routes|Routes|Nee|
|TwinQueries|Dubbele Query's|Nee|


## <a name="microsoftdevicesiothubs"></a>Microsoft.Devices/IotHubs

|Categorie|Weergave naam categorie|Te exporteren kosten|
|---|---|---|
|C2DCommands|C2D-opdrachten|Nee|
|C2DTwinOperations|Dubbele C2D-bewerkingen|Nee|
|Configuraties|Configuraties|Nee|
|Verbindingen|Verbindingen|Nee|
|D2CTwinOperations|D2CTwinOperations|Nee|
|DeviceIdentityOperations|Bewerkingen voor apparaat-Id's|Nee|
|DeviceStreams|Apparaatversleuteling (preview-versie)|Nee|
|DeviceTelemetry|Apparaattelemetrie|Nee|
|DirectMethods|Directe methoden|Nee|
|DistributedTracing|Gedistribueerde tracering (preview-versie)|Nee|
|FileUploadOperations|Bewerkingen voor het uploaden van bestanden|Nee|
|JobsOperations|Taak bewerkingen|Nee|
|Routes|Routes|Nee|
|TwinQueries|Dubbele Query's|Nee|


## <a name="microsoftdevicesprovisioningservices"></a>Micro soft. devices/provisioningServices

|Categorie|Weergave naam categorie|Te exporteren kosten|
|---|---|---|
|DeviceOperations|Bewerkingen voor apparaten|Nee|
|ServiceOperations|Service bewerkingen|Nee|


## <a name="microsoftdigitaltwinsdigitaltwinsinstances"></a>Micro soft. DigitalTwins/digitalTwinsInstances

|Categorie|Weergave naam categorie|Te exporteren kosten|
|---|---|---|
|DigitalTwinsOperation|DigitalTwinsOperation|Nee|
|EventRoutesOperation|EventRoutesOperation|Nee|
|ModelsOperation|ModelsOperation|Nee|
|QueryOperation|QueryOperation|Nee|


## <a name="microsoftdocumentdbdatabaseaccounts"></a>Microsoft.DocumentDB/databaseAccounts

|Categorie|Weergave naam categorie|Te exporteren kosten|
|---|---|---|
|CassandraRequests|CassandraRequests|Nee|
|ControlPlaneRequests|ControlPlaneRequests|Nee|
|DataPlaneRequests|DataPlaneRequests|Nee|
|GremlinRequests|GremlinRequests|Nee|
|MongoRequests|MongoRequests|Nee|
|PartitionKeyRUConsumption|PartitionKeyRUConsumption|Nee|
|PartitionKeyStatistics|PartitionKeyStatistics|Nee|
|QueryRuntimeStatistics|QueryRuntimeStatistics|Nee|


## <a name="microsofteventgriddomains"></a>Micro soft. EventGrid/domeinen

|Categorie|Weergave naam categorie|Te exporteren kosten|
|---|---|---|
|DeliveryFailures|Fout logboeken voor bezorging|Nee|
|PublishFailures|Fout logboeken publiceren|Nee|


## <a name="microsofteventgridpartnernamespaces"></a>Micro soft. EventGrid/partnerNamespaces

|Categorie|Weergave naam categorie|Te exporteren kosten|
|---|---|---|
|DeliveryFailures|Fout logboeken voor bezorging|Nee|
|PublishFailures|Fout logboeken publiceren|Nee|


## <a name="microsofteventgridpartnertopics"></a>Micro soft. EventGrid/partnerTopics

|Categorie|Weergave naam categorie|Te exporteren kosten|
|---|---|---|
|DeliveryFailures|Fout logboeken voor bezorging|Nee|


## <a name="microsofteventgridsystemtopics"></a>Micro soft. EventGrid/systemTopics

|Categorie|Weergave naam categorie|Te exporteren kosten|
|---|---|---|
|DeliveryFailures|Fout logboeken voor bezorging|Nee|


## <a name="microsofteventgridtopics"></a>Micro soft. EventGrid/topics

|Categorie|Weergave naam categorie|Te exporteren kosten|
|---|---|---|
|DeliveryFailures|Fout logboeken voor bezorging|Nee|
|PublishFailures|Fout logboeken publiceren|Nee|


## <a name="microsofteventhubnamespaces"></a>Microsoft.EventHub/namespaces

|Categorie|Weergave naam categorie|Te exporteren kosten|
|---|---|---|
|ArchiveLogs|Archief logboeken|Nee|
|AutoScaleLogs|Logboeken automatisch schalen|Nee|
|CustomerManagedKeyUserLogs|Door de klant beheerde sleutel logboeken|Nee|
|EventHubVNetConnectionEvent|Logboeken voor VNet/IP-filtering verbindingen|Nee|
|KafkaCoordinatorLogs|Kafka Coordinator-logboeken|Nee|
|KafkaUserErrorLogs|Fout logboeken van Kafka-gebruikers|Nee|
|OperationalLogs|Operationele logboeken|Nee|


## <a name="microsoftexperimentationexperimentworkspaces"></a>micro soft. experimenten/experimentWorkspaces

|Categorie|Weergave naam categorie|Te exporteren kosten|
|---|---|---|
|Aanvraag|Aanvraag|Nee|


## <a name="microsofthealthcareapisservices"></a>Microsoft.HealthcareApis/services

|Categorie|Weergave naam categorie|Te exporteren kosten|
|---|---|---|
|Audit logs bevat|Auditlogboeken|Nee|


## <a name="microsoftinsightsautoscalesettings"></a>micro soft. Insights/autoscalesettings

|Categorie|Weergave naam categorie|Te exporteren kosten|
|---|---|---|
|AutoscaleEvaluations|Evaluaties automatisch schalen|Nee|
|AutoscaleScaleActions|Schaal acties automatisch schalen|Nee|


## <a name="microsoftinsightscomponents"></a>Micro soft. Insights/onderdelen

|Categorie|Weergave naam categorie|Te exporteren kosten|
|---|---|---|
|AppAvailabilityResults|Beschikbaarheids resultaten|Nee|
|AppBrowserTimings|Browser timing|Nee|
|AppDependencies|Afhankelijkheden|Nee|
|AppEvents|Gebeurtenissen|Nee|
|AppExceptions|Uitzonderingen|Nee|
|AppMetrics|Metrische gegevens|Nee|
|AppPageViews|Pagina weergaven|Nee|
|AppPerformanceCounters|Prestatiemeteritems|Nee|
|AppRequests|Aanvragen|Nee|
|AppSystemEvents|Systeem gebeurtenissen|Nee|
|AppTraces|Traceringen|Nee|


## <a name="microsoftiotspacesgraph"></a>Micro soft. IoTSpaces/Graph

|Categorie|Weergave naam categorie|Te exporteren kosten|
|---|---|---|
|Controleren|Controleren|Nee|
|Uitgaand verkeer|Uitgaand verkeer|Nee|
|Inkomend verkeer|Inkomend verkeer|Nee|
|Operationeel|Operationeel|Nee|
|Tracering|Tracering|Nee|
|UserDefinedFunction|UserDefinedFunction|Nee|


## <a name="microsoftkeyvaultmanagedhsms"></a>micro soft. managedhsms

|Categorie|Weergave naam categorie|Te exporteren kosten|
|---|---|---|
|AuditEvent|Controlegebeurtenis|Nee|


## <a name="microsoftkeyvaultvaults"></a>Microsoft.KeyVault/vaults

|Categorie|Weergave naam categorie|Te exporteren kosten|
|---|---|---|
|AuditEvent|Auditlogboeken|Nee|


## <a name="microsoftkustoclusters"></a>Micro soft. Kusto/clusters

|Categorie|Weergave naam categorie|Te exporteren kosten|
|---|---|---|
|Opdracht|Opdracht|Nee|
|FailedIngestion|Mislukte opname bewerkingen|Nee|
|IngestionBatching|Opnamebatchverwerking|Nee|
|Query’s uitvoeren|Query’s uitvoeren|Nee|
|SucceededIngestion|Geslaagde opname bewerkingen|Nee|
|TableDetails|Tabel Details|Nee|
|TableUsageStatistics|Statistieken voor tabel gebruik|Nee|


## <a name="microsoftlogicintegrationaccounts"></a>Micro soft. Logic/integrationAccounts

|Categorie|Weergave naam categorie|Te exporteren kosten|
|---|---|---|
|IntegrationAccountTrackingEvents|Spoor gebeurtenissen voor integratie account|Nee|


## <a name="microsoftlogicworkflows"></a>Microsoft.Logic/workflows

|Categorie|Weergave naam categorie|Te exporteren kosten|
|---|---|---|
|WorkflowRuntime|Diagnostische gebeurtenissen voor workflowruntime|Nee|


## <a name="microsoftmachinelearningservicesworkspaces"></a>Micro soft. MachineLearningServices/werk ruimten

|Categorie|Weergave naam categorie|Te exporteren kosten|
|---|---|---|
|AmlComputeClusterEvent|AmlComputeClusterEvent|Nee|
|AmlComputeClusterNodeEvent|AmlComputeClusterNodeEvent|Nee|
|AmlComputeCpuGpuUtilization|AmlComputeCpuGpuUtilization|Nee|
|AmlComputeJobEvent|AmlComputeJobEvent|Nee|
|AmlRunStatusChangedEvent|AmlRunStatusChangedEvent|Nee|


## <a name="microsoftmediamediaservices"></a>Micro soft. Media/Media Services

|Categorie|Weergave naam categorie|Te exporteren kosten|
|---|---|---|
|KeyDeliveryRequests|Aanvragen voor sleutel levering|Nee|


## <a name="microsoftnetworkapplicationgateways"></a>Micro soft. Network/applicationGateways

|Categorie|Weergave naam categorie|Te exporteren kosten|
|---|---|---|
|ApplicationGatewayAccessLog|Application Gateway Access-logboek|Nee|
|ApplicationGatewayFirewallLog|Application Gateway firewall-logboek|Nee|
|ApplicationGatewayPerformanceLog|Prestatie logboek Application Gateway|Nee|


## <a name="microsoftnetworkazurefirewalls"></a>Micro soft. Network/azurefirewalls

|Categorie|Weergave naam categorie|Te exporteren kosten|
|---|---|---|
|AzureFirewallApplicationRule|Toepassings regel Azure Firewall|Nee|
|AzureFirewallDnsProxy|DNS-proxy Azure Firewall|Nee|
|AzureFirewallNetworkRule|Azure Firewall netwerk regel|Nee|


## <a name="microsoftnetworkbastionhosts"></a>Microsoft.Network/bastionHosts

|Categorie|Weergave naam categorie|Te exporteren kosten|
|---|---|---|
|BastionAuditLogs|Controle logboeken voor Bastion|Nee|


## <a name="microsoftnetworkexpressroutecircuits"></a>Microsoft.Network/expressRouteCircuits

|Categorie|Weergave naam categorie|Te exporteren kosten|
|---|---|---|
|PeeringRouteLog|Logboeken voor peering route tabel|Nee|


## <a name="microsoftnetworkfrontdoors"></a>Micro soft. Network/frontdoors

|Categorie|Weergave naam categorie|Te exporteren kosten|
|---|---|---|
|FrontdoorAccessLog|-Ingang-toegangs logboek|Nee|
|FrontdoorWebApplicationFirewallLog|-Ingang Web Application firewall-logboek|Nee|


## <a name="microsoftnetworkloadbalancers"></a>Microsoft.Network/loadBalancers

|Categorie|Weergave naam categorie|Te exporteren kosten|
|---|---|---|
|LoadBalancerAlertEvent|Load Balancer waarschuwings gebeurtenissen|Nee|
|LoadBalancerProbeHealthStatus|Load Balancer test status|Nee|


## <a name="microsoftnetworknetworksecuritygroups"></a>Micro soft. Network/networksecuritygroups

|Categorie|Weergave naam categorie|Te exporteren kosten|
|---|---|---|
|NetworkSecurityGroupEvent|Gebeurtenis netwerk beveiligings groep|Nee|
|NetworkSecurityGroupFlowEvent|Stroom gebeurtenis van regel voor netwerk beveiligings groep|Nee|
|NetworkSecurityGroupRuleCounter|Teller van regel voor netwerk beveiligings groep|Nee|


## <a name="microsoftnetworkp2svpngateways"></a>Micro soft. Network/p2sVpnGateways

|Categorie|Weergave naam categorie|Te exporteren kosten|
|---|---|---|
|GatewayDiagnosticLog|Diagnostische logboeken van de gateway|Nee|
|IKEDiagnosticLog|Diagnostische logboeken voor IKE|Nee|
|P2SDiagnosticLog|Diagnostische logboeken van P2S|Nee|


## <a name="microsoftnetworkpublicipaddresses"></a>Micro soft. Network/publicIPAddresses

|Categorie|Weergave naam categorie|Te exporteren kosten|
|---|---|---|
|DDoSMitigationFlowLogs|Stroom logboeken van DDoS-oplossings beslissingen|Nee|
|DDoSMitigationReports|Rapporten met DDoS-oplossingen|Nee|
|DDoSProtectionNotifications|DDoS-beveiligings meldingen|Nee|


## <a name="microsoftnetworktrafficmanagerprofiles"></a>Microsoft.Network/trafficManagerProfiles

|Categorie|Weergave naam categorie|Te exporteren kosten|
|---|---|---|
|ProbeHealthStatusEvents|Gebeurtenis met resultaten van Traffic Manager test status|Nee|


## <a name="microsoftnetworkvirtualnetworkgateways"></a>Microsoft.Network/virtualNetworkGateways

|Categorie|Weergave naam categorie|Te exporteren kosten|
|---|---|---|
|GatewayDiagnosticLog|Diagnostische logboeken van de gateway|Nee|
|IKEDiagnosticLog|Diagnostische logboeken voor IKE|Nee|
|P2SDiagnosticLog|Diagnostische logboeken van P2S|Nee|
|RouteDiagnosticLog|Diagnostische logboeken voor route ring|Nee|
|TunnelDiagnosticLog|Diagnostische logboeken voor tunnel|Nee|


## <a name="microsoftnetworkvirtualnetworks"></a>Microsoft.Network/virtualNetworks

|Categorie|Weergave naam categorie|Te exporteren kosten|
|---|---|---|
|VMProtectionAlerts|Waarschuwingen voor VM-beveiliging|Nee|


## <a name="microsoftnetworkvpngateways"></a>Micro soft. Network/vpnGateways

|Categorie|Weergave naam categorie|Te exporteren kosten|
|---|---|---|
|GatewayDiagnosticLog|Diagnostische logboeken van de gateway|Nee|
|IKEDiagnosticLog|Diagnostische logboeken voor IKE|Nee|
|RouteDiagnosticLog|Diagnostische logboeken voor route ring|Nee|
|TunnelDiagnosticLog|Diagnostische logboeken voor tunnel|Nee|


## <a name="microsoftnotificationhubsnamespaces"></a>Microsoft.NotificationHubs/namespaces

|Categorie|Weergave naam categorie|Te exporteren kosten|
|---|---|---|
|OperationalLogs|Operationele logboeken|Nee|


## <a name="microsoftoperationalinsightsworkspaces"></a>Microsoft.OperationalInsights/workspaces

|Categorie|Weergave naam categorie|Te exporteren kosten|
|---|---|---|
|Controleren|Auditlogboeken|Nee|


## <a name="microsoftpowerbitenants"></a>Micro soft. PowerBI/tenants

|Categorie|Weergave naam categorie|Te exporteren kosten|
|---|---|---|
|Engine|Engine|Nee|


## <a name="microsoftpowerbitenantsworkspaces"></a>Micro soft. PowerBI/tenants/werk ruimten

|Categorie|Weergave naam categorie|Te exporteren kosten|
|---|---|---|
|Engine|Engine|Nee|


## <a name="microsoftpowerbidedicatedcapacities"></a>Micro soft. PowerBIDedicated/capaciteiten

|Categorie|Weergave naam categorie|Te exporteren kosten|
|---|---|---|
|Engine|Engine|Nee|


## <a name="microsoftprojectbabylonaccounts"></a>Micro soft. ProjectBabylon/accounts

|Categorie|Weergave naam categorie|Te exporteren kosten|
|---|---|---|
|ScanStatusLogEvent|ScanStatus|Nee|


## <a name="microsoftpurviewaccounts"></a>micro soft. controle sfeer liggen/accounts

|Categorie|Weergave naam categorie|Te exporteren kosten|
|---|---|---|
|ScanStatusLogEvent|ScanStatus|Nee|


## <a name="microsoftrecoveryservicesvaults"></a>Micro soft. Recovery Services/kluizen

|Categorie|Weergave naam categorie|Te exporteren kosten|
|---|---|---|
|AddonAzureBackupAlerts|Azure Backup waarschuwings gegevens van invoeg toepassing|Nee|
|AddonAzureBackupJobs|Taak gegevens van invoeg toepassing Azure Backup|Nee|
|AddonAzureBackupPolicy|Azure Backup beleids gegevens van invoeg toepassing|Nee|
|AddonAzureBackupProtectedInstance|Invoeg toepassing Azure Backup beveiligde instantie gegevens|Nee|
|AddonAzureBackupStorage|Invoeg Azure Backup opslag gegevens|Nee|
|AzureBackupReport|Azure Backup rapportage gegevens|Nee|
|AzureSiteRecoveryEvents|Azure Site Recovery gebeurtenissen|Nee|
|AzureSiteRecoveryJobs|Azure Site Recovery taken|Nee|
|AzureSiteRecoveryProtectedDiskDataChurn|Gegevens verloop van beveiligde schijf Azure Site Recovery|Nee|
|AzureSiteRecoveryRecoveryPoints|Azure Site Recovery herstel punten|Nee|
|AzureSiteRecoveryReplicatedItems|Gerepliceerde items Azure Site Recovery|Nee|
|AzureSiteRecoveryReplicationDataUploadRate|Upload frequentie van Azure Site Recovery replicatie gegevens|Nee|
|AzureSiteRecoveryReplicationStats|Replicatie statistieken Azure Site Recovery|Nee|
|CoreAzureBackup|Kern Azure Backup gegevens|Nee|


## <a name="microsoftrelaynamespaces"></a>Micro soft. relay/naam ruimten

|Categorie|Weergave naam categorie|Te exporteren kosten|
|---|---|---|
|HybridConnectionsEvent|HybridConnections-gebeurtenissen|Nee|
|HybridConnectionsLogs|HybridConnectionsLogs|Nee|


## <a name="microsoftsearchsearchservices"></a>Micro soft. Search/searchServices

|Categorie|Weergave naam categorie|Te exporteren kosten|
|---|---|---|
|OperationLogs|Bewerkings logboeken|Nee|


## <a name="microsoftservicebusnamespaces"></a>Microsoft.ServiceBus/namespaces

|Categorie|Weergave naam categorie|Te exporteren kosten|
|---|---|---|
|OperationalLogs|Operationele logboeken|Nee|


## <a name="microsoftsignalrservicesignalr"></a>Microsoft.SignalRService/SignalR

|Categorie|Weergave naam categorie|Te exporteren kosten|
|---|---|---|
|AllLogs|Logboeken van de Azure signalerings service.|Nee|


## <a name="microsoftsqlmanagedinstances"></a>Micro soft. SQL/managedInstances

|Categorie|Weergave naam categorie|Te exporteren kosten|
|---|---|---|
|DevOpsOperationsAudit|Audit logboeken voor Devops-bewerkingen|Nee|
|ResourceUsageStats|Resource gebruiks statistieken|Nee|
|SQLSecurityAuditEvents|SQL-beveiligings controle gebeurtenis|Nee|


## <a name="microsoftsqlmanagedinstancesdatabases"></a>Micro soft. SQL/managedInstances/data bases

|Categorie|Weergave naam categorie|Te exporteren kosten|
|---|---|---|
|Fouten|Fouten|Nee|
|QueryStoreRuntimeStatistics|Runtime statistieken voor query Store|Nee|
|QueryStoreWaitStatistics|Wacht statistieken voor query Store|Nee|
|SQLInsights|SQL Insights|Nee|


## <a name="microsoftsqlserversdatabases"></a>Microsoft.Sql/servers/databases

|Categorie|Weergave naam categorie|Te exporteren kosten|
|---|---|---|
|AutomaticTuning|Automatisch instellen|Nee|
|Block|Block|Nee|
|DatabaseWaitStatistics|Data base-wacht statistieken|Nee|
|Impasses|Impasses|Nee|
|DevOpsOperationsAudit|Audit logboeken voor Devops-bewerkingen|Nee|
|DmsWorkers|DMS-werk rollen|Nee|
|Fouten|Fouten|Nee|
|ExecRequests|Exec-aanvragen|Nee|
|QueryStoreRuntimeStatistics|Runtime statistieken voor query Store|Nee|
|QueryStoreWaitStatistics|Wacht statistieken voor query Store|Nee|
|RequestSteps|Stappen voor aanvragen|Nee|
|SQLInsights|SQL Insights|Nee|
|SqlRequests|SQL-aanvragen|Nee|
|SQLSecurityAuditEvents|SQL-beveiligings controle gebeurtenis|Nee|
|Time-outs|Time-outs|Nee|
|Wacht|Wacht|Nee|


## <a name="microsoftstoragestorageaccountsblobservices"></a>Micro soft. Storage/Storage accounts/blobServices

|Categorie|Weergave naam categorie|Te exporteren kosten|
|---|---|---|
|StorageDelete|StorageDelete|Ja|
|StorageRead|StorageRead|Ja|
|StorageWrite|StorageWrite|Ja|


## <a name="microsoftstoragestorageaccountsfileservices"></a>Micro soft. Storage/Storage accounts/fileServices

|Categorie|Weergave naam categorie|Te exporteren kosten|
|---|---|---|
|StorageDelete|StorageDelete|Ja|
|StorageRead|StorageRead|Ja|
|StorageWrite|StorageWrite|Ja|


## <a name="microsoftstoragestorageaccountsqueueservices"></a>Micro soft. Storage/Storage accounts/queueServices

|Categorie|Weergave naam categorie|Te exporteren kosten|
|---|---|---|
|StorageDelete|StorageDelete|Ja|
|StorageRead|StorageRead|Ja|
|StorageWrite|StorageWrite|Ja|


## <a name="microsoftstoragestorageaccountstableservices"></a>Micro soft. Storage/Storage accounts/tableServices

|Categorie|Weergave naam categorie|Te exporteren kosten|
|---|---|---|
|StorageDelete|StorageDelete|Ja|
|StorageRead|StorageRead|Ja|
|StorageWrite|StorageWrite|Ja|


## <a name="microsoftstreamanalyticsstreamingjobs"></a>Micro soft. StreamAnalytics/streamingjobs

|Categorie|Weergave naam categorie|Te exporteren kosten|
|---|---|---|
|Ontwerpen|Ontwerpen|Nee|
|Uitvoering|Uitvoering|Nee|


## <a name="microsoftsynapseworkspaces"></a>Micro soft. Synapse/werk ruimten

|Categorie|Weergave naam categorie|Te exporteren kosten|
|---|---|---|
|BuiltinSqlReqsEnded|De ingebouwde aanvragen van SQL-groep zijn beëindigd|Nee|
|GatewayApiRequests|Synapse Gateway-API-aanvragen|Nee|
|SQLSecurityAuditEvents|SQL-beveiligings controle gebeurtenis|Nee|
|SynapseRbacOperations|Synapse RBAC-bewerkingen|Nee|


## <a name="microsoftsynapseworkspacesbigdatapools"></a>Micro soft. Synapse/werk ruimten/bigDataPools

|Categorie|Weergave naam categorie|Te exporteren kosten|
|---|---|---|
|BigDataPoolAppsEnded|Toepassingen voor Big Data-groep gestopt|Nee|


## <a name="microsoftsynapseworkspacessqlpools"></a>Micro soft. Synapse/werk ruimten/sqlPools

|Categorie|Weergave naam categorie|Te exporteren kosten|
|---|---|---|
|DmsWorkers|DMS-werk rollen|Nee|
|ExecRequests|Exec-aanvragen|Nee|
|RequestSteps|Stappen voor aanvragen|Nee|
|SqlRequests|SQL-aanvragen|Nee|
|SQLSecurityAuditEvents|SQL-beveiligings controle gebeurtenis|Nee|
|Wacht|Wacht|Nee|


## <a name="microsofttimeseriesinsightsenvironments"></a>Micro soft. TimeSeriesInsights/omgevingen

|Categorie|Weergave naam categorie|Te exporteren kosten|
|---|---|---|
|Inkomend verkeer|Inkomend verkeer|Nee|
|Beheer|Beheer|Nee|


## <a name="microsofttimeseriesinsightsenvironmentseventsources"></a>Micro soft. TimeSeriesInsights/omgevingen/eventsources

|Categorie|Weergave naam categorie|Te exporteren kosten|
|---|---|---|
|Inkomend verkeer|Inkomend verkeer|Nee|
|Beheer|Beheer|Nee|


## <a name="microsoftwebhostingenvironments"></a>micro soft. Web/hostingenvironments

|Categorie|Weergave naam categorie|Te exporteren kosten|
|---|---|---|
|AppServiceEnvironmentPlatformLogs|App Service Environment-platform logboeken|Nee|


## <a name="microsoftwebsites"></a>micro soft. web/sites

|Categorie|Weergave naam categorie|Te exporteren kosten|
|---|---|---|
|AppServiceAntivirusScanAuditLogs|Audit logboeken voor antivirus Programma's melden|Nee|
|AppServiceAppLogs|Toepassings logboeken App Service|Nee|
|AppServiceAuditLogs|Access-controle logboeken|Nee|
|AppServiceConsoleLogs|App Service-console logboeken|Nee|
|AppServiceFileAuditLogs|Controle logboeken voor het wijzigen van de site-inhoud|Nee|
|AppServiceHTTPLogs|HTTP-logboeken|Nee|
|AppServiceIPSecAuditLogs|Controle logboeken voor IPSecurity|Nee|
|AppServicePlatformLogs|App Service-platform logboeken|Nee|
|FunctionAppLogs|Toepassings logboeken van functie|Nee|


## <a name="microsoftwebsitesslots"></a>micro soft. web/sites/sleuven

|Categorie|Weergave naam categorie|Te exporteren kosten|
|---|---|---|
|AppServiceAntivirusScanAuditLogs|Audit logboeken voor antivirus Programma's melden|Nee|
|AppServiceAppLogs|Toepassings logboeken App Service|Nee|
|AppServiceAuditLogs|Access-controle logboeken|Nee|
|AppServiceConsoleLogs|App Service-console logboeken|Nee|
|AppServiceFileAuditLogs|Controle logboeken voor het wijzigen van de site-inhoud|Nee|
|AppServiceHTTPLogs|HTTP-logboeken|Nee|
|AppServiceIPSecAuditLogs|Controle logboeken voor IPSecurity|Nee|
|AppServicePlatformLogs|App Service-platform logboeken|Nee|
|FunctionAppLogs|Toepassings logboeken van functie|Nee|



## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over bron logboeken](../essentials/platform-logs-overview.md)
* [Resource bron logboeken streamen naar **Event hubs**](./resource-logs.md#send-to-azure-event-hubs)
* [Diagnostische instellingen voor bron logboek wijzigen met behulp van de Azure Monitor REST API](/rest/api/monitor/diagnosticsettings)
* [Logboeken analyseren vanuit Azure Storage met Log Analytics](./resource-logs.md#send-to-log-analytics-workspace)

