---
title: Azure Monitor resource logboeken ondersteunde services en categorieën
description: Verwijzing van Azure Monitor inzicht krijgen in de ondersteunde services en het gebeurtenis schema voor Azure-resource Logboeken.
ms.subservice: logs
ms.topic: reference
ms.date: 12/09/2020
ms.openlocfilehash: aeac069b4e9382867664a82af62e29e72da7585e
ms.sourcegitcommit: c7153bb48ce003a158e83a1174e1ee7e4b1a5461
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/15/2021
ms.locfileid: "98232244"
---
# <a name="supported-categories-for-azure-resource-logs"></a>Ondersteunde categorieën voor Azure-resource logboeken

> [!NOTE]
> Bron logboeken zijn voorheen bekend als Diagnostische logboeken. De naam is in oktober 2019 gewijzigd, omdat de typen logboeken die door Azure Monitor zijn verzameld, meer dan alleen de Azure-resource bevatten.

[Azure monitor bron logboeken](./platform-logs-overview.md) worden logboeken gegenereerd door Azure-Services waarmee de werking van deze services of bronnen wordt beschreven. Alle bron logboeken die beschikbaar zijn via Azure Monitor, delen een gemeen schappelijk schema op het hoogste niveau, met flexibiliteit voor elke service om unieke eigenschappen voor hun eigen gebeurtenissen te verzenden.

Een combi natie van het resource type (beschikbaar in de `resourceId` eigenschap) en de `category` unieke identificatie van een schema. Er is een gemeen schappelijk schema voor alle bron logboeken met servicespecifieke velden en vervolgens toegevoegd voor verschillende logboek categorieën. Zie voor meer informatie [common en service-specifiek schema voor Azure-resource logboeken]()


## <a name="costs"></a>Kosten

Er zijn kosten verbonden aan het verzenden en opslaan van gegevens in Log Analytics, Azure Storage en/of event hub. U kunt betalen voor de kosten om de gegevens op deze locaties op te halen en deze daar te bewaren.  Bron logboeken zijn één type gegevens dat u naar deze locaties kunt verzenden. Er zijn extra [kosten verbonden aan het exporteren van sommige categorieën resource logboeken](https://azure.microsoft.com/pricing/details/monitor/) naar deze locaties, terwijl andere geen export kosten in rekening worden gebracht. De specifieke export kosten worden in de onderstaande tabel weer gegeven.

## <a name="supported-log-categories-per-resource-type"></a>Ondersteunde logboek categorieën per resource type

Hieronder volgt een lijst met de typen logboeken die beschikbaar zijn voor elk resource type. 

Sommige categorieën worden mogelijk alleen ondersteund voor specifieke typen resources. Raadpleeg de resource-specifieke documentatie als u denkt dat er een resource ontbreekt. Bijvoorbeeld: categorieën van micro soft. SQL/servers/data bases zijn niet beschikbaar voor alle typen data bases. Zie [informatie over SQL database diagnostische logboek registratie](../../azure-sql/database/metrics-diagnostic-telemetry-logging-streaming-export-configure.md)voor meer informatie. 

Als er nog steeds iets ontbreekt, kunt u onder aan dit artikel een GitHub-opmerking openen.
## <a name="microsoftanalysisservicesservers"></a>Micro soft. AnalysisServices/servers

Kosten voor export: gratis 

|Categorie |Weergave naam categorie|
|---|---|
|Engine|Engine|
|Service|Service|


## <a name="microsoftapimanagementservice"></a>Microsoft.ApiManagement/service

Kosten voor export: gratis 

|Categorie |Weergave naam categorie|
|---|---|
|Gateway logs|Logboeken gerelateerd aan de ApiManagement-gateway|


## <a name="microsoftappplatformspring"></a>Micro soft. AppPlatform/lente

Kosten voor export: gratis 

|Categorie |Weergave naam categorie|
|---|---|
|ApplicationConsole|Toepassings console|
|SystemLogs|Systeem logboeken|


## <a name="microsoftautomationautomationaccounts"></a>Micro soft. Automation/automationAccounts

Kosten voor export: gratis 

|Categorie |Weergave naam categorie|
|---|---|
|DscNodeStatus|DSC-knooppunt status|
|JobLogs|Taak logboeken|
|JobStreams|Taak stromen|


## <a name="microsoftbatchbatchaccounts"></a>Microsoft.Bat-CH/batchAccounts

Kosten voor export: gratis 

|Categorie |Weergave naam categorie|
|---|---|
|ServiceLog|Service logboeken|


## <a name="microsoftbatchaiworkspaces"></a>Microsoft.BatchAI/werk ruimten

Kosten voor export: gratis 

|Categorie |Weergave naam categorie|
|---|---|
|BaiClusterEvent|BaiClusterEvent|
|BaiClusterNodeEvent|BaiClusterNodeEvent|
|BaiJobEvent|BaiJobEvent|


## <a name="microsoftblockchainblockchainmembers"></a>Microsoft.Blockchain/blockchainMembers

Kosten voor export: gratis 

|Categorie |Weergave naam categorie|
|---|---|
|BlockchainApplication|Block Chain-toepassing|
|FabricOrderer|Fabric-Orderer|
|FabricPeer|Infrastructuur peer|
|Proxy|Proxy|


## <a name="microsoftblockchaincordamembers"></a>Micro soft. Block Chain/cordaMembers

Kosten voor export: gratis 

|Categorie |Weergave naam categorie|
|---|---|
|BlockchainApplication|Block Chain-toepassing|


## <a name="microsoftcdncdnwebapplicationfirewallpolicies"></a>Micro soft. CDN/cdnwebapplicationfirewallpolicies

Kosten voor export: gratis 

|Categorie |Weergave naam categorie|
|---|---|
|WebApplicationFirewallLogs|Web Application firewall-logboeken|


## <a name="microsoftcdnprofiles"></a>Microsoft.Cdn/profiles

Kosten voor export: gratis 

|Categorie |Weergave naam categorie|
|---|---|
|AzureCdnAccessLog|Azure CDN-toegangs logboek|


## <a name="microsoftcdnprofilesendpoints"></a>Micro soft. CDN/profielen/eind punten

Kosten voor export: gratis 

|Categorie |Weergave naam categorie|
|---|---|
|CoreAnalytics|Hiermee worden de metrische gegevens van het eind punt opgehaald, bijvoorbeeld band breedte, uitgaand verkeer enzovoort.|


## <a name="microsoftclassicnetworknetworksecuritygroups"></a>Micro soft. ClassicNetwork/networksecuritygroups

Kosten voor export: gratis 

|Categorie |Weergave naam categorie|
|---|---|
|Stroom gebeurtenis van regel voor netwerk beveiligings groep|Stroom gebeurtenis van regel voor netwerk beveiligings groep|


## <a name="microsoftcognitiveservicesaccounts"></a>Micro soft. CognitiveServices/accounts

Kosten voor export: gratis 

|Categorie |Weergave naam categorie|
|---|---|
|Controleren|Auditlogboeken|
|RequestResponse|Aanvraag-en antwoord logboeken|
|Tracering|Traceer logboeken|


## <a name="microsoftcontainerregistryregistries"></a>Micro soft. ContainerRegistry/registers

Kosten voor export: gratis 

|Categorie |Weergave naam categorie|
|---|---|
|ContainerRegistryLoginEvents|Aanmeldings gebeurtenissen|
|ContainerRegistryRepositoryEvents|RepositoryEvent-logboeken|


## <a name="microsoftcontainerservicemanagedclusters"></a>Micro soft. container service/managedClusters

Kosten voor export: gratis 

|Categorie |Weergave naam categorie|
|---|---|
|cluster-automatisch schalen|Kubernetes-cluster automatisch schalen|
|uitvoeren-apiserver|Kubernetes-API-server|
|uitvoeren-audit|Kubernetes-controle|
|uitvoeren-Controller-Manager|Kubernetes-controller beheer|
|uitvoeren-scheduler|Kubernetes scheduler|


## <a name="microsoftcustomprovidersresourceproviders"></a>Micro soft. CustomProviders/resourceproviders

Kosten voor export: gratis 

|Categorie |Weergave naam categorie|
|---|---|
|Audit logs bevat|Controle logboeken voor MiniRP-aanroepen|


## <a name="microsoftdatabricksworkspaces"></a>Micro soft. Databricks/werk ruimten

Kosten voor export: gratis 

|Categorie |Weergave naam categorie|
|---|---|
|accounts|Databricks-accounts|
|clusters|Databricks-clusters|
|dbfs|Databricks-bestandssysteem|
|instancePools|Instantie groepen|
|functies|Databricks-taken|
|notebook|Databricks Notebook|
|geheimen|Databricks geheimen|
|sqlPermissions|Databricks SQLPermissions|
|SSH|Databricks SSH|
|werkruimte|Databricks-werk ruimte|


## <a name="microsoftdatafactoryfactories"></a>Micro soft. DataFactory/fabrieken

Kosten voor export: gratis 

|Categorie |Weergave naam categorie|
|---|---|
|ActivityRuns|Logboek voor uitvoering van pijplijn activiteit|
|PipelineRuns|Logboek voor uitvoering van pijp lijn|
|TriggerRuns|Trigger uitvoeringen logboek|


## <a name="microsoftdatalakestoreaccounts"></a>Micro soft. data Lake Store/accounts

Kosten voor export: gratis 

|Categorie |Weergave naam categorie|
|---|---|
|Controleren|Auditlogboeken|
|Aanvragen|Logboeken aanvragen|


## <a name="microsoftdatashareaccounts"></a>Microsoft.DataShare/accounts

Kosten voor export: gratis 

|Categorie |Weergave naam categorie|
|---|---|
|ReceivedShareSnapshots|Ontvangen moment opnamen van shares|
|SentShareSnapshots|Verzonden moment opnamen van shares|
|Shares|Shares|
|ShareSubscriptions|Abonnementen delen|


## <a name="microsoftdbformariadbservers"></a>Microsoft.DBforMariaDB/servers

Kosten voor export: gratis 

|Categorie |Weergave naam categorie|
|---|---|
|MySqlAuditLogs|Controle logboeken voor MariaDB|
|MySqlSlowLogs|MariaDB-server logboeken|


## <a name="microsoftdbformysqlflexibleservers"></a>Microsoft.DBforMySQL/flexibleServers

Kosten voor export: gratis 

|Categorie |Weergave naam categorie|
|---|---|
|MySqlAuditLogs|MySQL-controle logboeken|
|MySqlSlowLogs|Langzame logboeken voor MySQL|


## <a name="microsoftdbformysqlservers"></a>Microsoft.DBforMySQL/servers

Kosten voor export: gratis 

|Categorie |Weergave naam categorie|
|---|---|
|MySqlAuditLogs|MySQL-controle logboeken|
|MySqlSlowLogs|MySQL-server logboeken|


## <a name="microsoftdbforpostgresqlflexibleservers"></a>Microsoft.DBforPostgreSQL/flexibleServers

Kosten voor export: gratis 

|Categorie |Weergave naam categorie|
|---|---|
|PostgreSQLLogs|PostgreSQL-server logboeken|


## <a name="microsoftdbforpostgresqlservers"></a>Microsoft.DBforPostgreSQL/servers

Kosten voor export: gratis 

|Categorie |Weergave naam categorie|
|---|---|
|PostgreSQLLogs|PostgreSQL-server logboeken|
|QueryStoreRuntimeStatistics|Runtime statistieken voor PostgreSQL query Store|
|QueryStoreWaitStatistics|Wacht statistieken voor PostgreSQL query Store|


## <a name="microsoftdbforpostgresqlserversv2"></a>Micro soft. DBforPostgreSQL/serversv2

Kosten voor export: gratis 

|Categorie |Weergave naam categorie|
|---|---|
|PostgreSQLLogs|PostgreSQL-server logboeken|


## <a name="microsoftdesktopvirtualizationapplicationgroups"></a>Micro soft. DesktopVirtualization/applicationgroups

Kosten voor export: gratis 

|Categorie |Weergave naam categorie|
|---|---|
|Controlepunt|Controlepunt|
|Fout|Fout|
|Beheer|Beheer|


## <a name="microsoftdesktopvirtualizationhostpools"></a>Micro soft. DesktopVirtualization/hostpools

Kosten voor export: gratis 

|Categorie |Weergave naam categorie|
|---|---|
|Controlepunt|Controlepunt|
|Verbinding|Verbinding|
|Fout|Fout|
|HostRegistration|HostRegistration|
|Beheer|Beheer|


## <a name="microsoftdesktopvirtualizationworkspaces"></a>Micro soft. DesktopVirtualization/werk ruimten

Kosten voor export: gratis 

|Categorie |Weergave naam categorie|
|---|---|
|Controlepunt|Controlepunt|
|Fout|Fout|
|Feed|Feed|
|Beheer|Beheer|


## <a name="microsoftdevicesiothubs"></a>Microsoft.Devices/IotHubs

Kosten voor export: gratis 

|Categorie |Weergave naam categorie|
|---|---|
|C2DCommands|C2D-opdrachten|
|C2DTwinOperations|Dubbele C2D-bewerkingen|
|Configuraties|Configuraties|
|Verbindingen|Verbindingen|
|D2CTwinOperations|D2CTwinOperations|
|DeviceIdentityOperations|Bewerkingen voor apparaat-Id's|
|DeviceStreams|Apparaatversleuteling (preview-versie)|
|DeviceTelemetry|Apparaattelemetrie|
|DirectMethods|Directe methoden|
|DistributedTracing|Gedistribueerde tracering (preview-versie)|
|FileUploadOperations|Bewerkingen voor het uploaden van bestanden|
|JobsOperations|Taak bewerkingen|
|Routes|Routes|
|TwinQueries|Dubbele Query's|


## <a name="microsoftdevicesprovisioningservices"></a>Micro soft. devices/provisioningServices

Kosten voor export: gratis 

|Categorie |Weergave naam categorie|
|---|---|
|DeviceOperations|Bewerkingen voor apparaten|
|ServiceOperations|Service bewerkingen|


## <a name="microsoftdocumentdbdatabaseaccounts"></a>Microsoft.DocumentDB/databaseAccounts

Kosten voor export: gratis 

|Categorie |Weergave naam categorie|
|---|---|
|CassandraRequests|CassandraRequests|
|ControlPlaneRequests|ControlPlaneRequests|
|DataPlaneRequests|DataPlaneRequests|
|GremlinRequests|GremlinRequests|
|MongoRequests|MongoRequests|
|PartitionKeyRUConsumption|PartitionKeyRUConsumption|
|PartitionKeyStatistics|PartitionKeyStatistics|
|QueryRuntimeStatistics|QueryRuntimeStatistics|


## <a name="microsofteventgriddomains"></a>Micro soft. EventGrid/domeinen

Kosten voor export: gratis 

|Categorie |Weergave naam categorie|
|---|---|
|DeliveryFailures|Fout logboeken voor bezorging|
|PublishFailures|Fout logboeken publiceren|


## <a name="microsofteventgridsystemtopics"></a>Micro soft. EventGrid/systemTopics

Kosten voor export: gratis 

|Categorie |Weergave naam categorie|
|---|---|
|DeliveryFailures|Fout logboeken voor bezorging|


## <a name="microsofteventgridtopics"></a>Micro soft. EventGrid/topics

Kosten voor export: gratis 

|Categorie |Weergave naam categorie|
|---|---|
|DeliveryFailures|Fout logboeken voor bezorging|
|PublishFailures|Fout logboeken publiceren|


## <a name="microsofteventhubnamespaces"></a>Microsoft.EventHub/namespaces

Kosten voor export: gratis 

|Categorie |Weergave naam categorie|
|---|---|
|ArchiveLogs|Archief logboeken|
|AutoScaleLogs|Logboeken automatisch schalen|
|CustomerManagedKeyUserLogs|Door de klant beheerde sleutel logboeken|
|EventHubVNetConnectionEvent|Logboeken voor VNet/IP-filtering verbindingen|
|KafkaCoordinatorLogs|Kafka Coordinator-logboeken|
|KafkaUserErrorLogs|Fout logboeken van Kafka-gebruikers|
|OperationalLogs|Operationele logboeken|


## <a name="microsofthealthcareapisservices"></a>Microsoft.HealthcareApis/services

Kosten voor export: gratis 

|Categorie |Weergave naam categorie|
|---|---|
|Audit logs bevat|Auditlogboeken|


## <a name="microsoftinsightsautoscalesettings"></a>Micro soft. Insights/AutoscaleSettings

Kosten voor export: gratis 

|Categorie |Weergave naam categorie|
|---|---|
|AutoscaleEvaluations|Evaluaties automatisch schalen|
|AutoscaleScaleActions|Schaal acties automatisch schalen|


## <a name="microsoftinsightscomponents"></a>Micro soft. Insights/onderdelen

Kosten voor export: gratis 

|Categorie |Weergave naam categorie|
|---|---|
|AppAvailabilityResults|Beschikbaarheids resultaten|
|AppBrowserTimings|Browser timing|
|AppDependencies|Afhankelijkheden|
|AppEvents|Gebeurtenissen|
|AppExceptions|Uitzonderingen|
|AppMetrics|Metrische gegevens|
|AppPageViews|Pagina weergaven|
|AppPerformanceCounters|Prestatiemeteritems|
|AppRequests|Aanvragen|
|AppSystemEvents|Systeem gebeurtenissen|
|AppTraces|Traceringen|


## <a name="microsoftkeyvaultvaults"></a>Microsoft.KeyVault/vaults

Kosten voor export: gratis 

|Categorie |Weergave naam categorie|
|---|---|
|AuditEvent|Auditlogboeken|


## <a name="microsoftkustoclusters"></a>Micro soft. Kusto/clusters

Kosten voor export: gratis 

|Categorie |Weergave naam categorie|
|---|---|
|Opdracht|Opdracht|
|FailedIngestion|Mislukte opname bewerkingen|
|IngestionBatching|Opnamebatchverwerking|
|Query’s uitvoeren|Query’s uitvoeren|
|SucceededIngestion|Geslaagde opname bewerkingen|
|TableDetails|Tabel Details|
|TableUsageStatistics|Statistieken voor tabel gebruik|


## <a name="microsoftlogicintegrationaccounts"></a>Micro soft. Logic/integrationAccounts

Kosten voor export: gratis 

|Categorie |Weergave naam categorie|
|---|---|
|IntegrationAccountTrackingEvents|Spoor gebeurtenissen voor integratie account|


## <a name="microsoftlogicworkflows"></a>Microsoft.Logic/workflows

Kosten voor export: gratis 

|Categorie |Weergave naam categorie|
|---|---|
|WorkflowRuntime|Diagnostische gebeurtenissen voor workflowruntime|


## <a name="microsoftmachinelearningservicesworkspaces"></a>Micro soft. MachineLearningServices/werk ruimten

Kosten voor export: gratis 

|Categorie |Weergave naam categorie|
|---|---|
|AmlComputeClusterEvent|AmlComputeClusterEvent|
|AmlComputeClusterNodeEvent|AmlComputeClusterNodeEvent|
|AmlComputeCpuGpuUtilization|AmlComputeCpuGpuUtilization|
|AmlComputeJobEvent|AmlComputeJobEvent|
|AmlRunStatusChangedEvent|AmlRunStatusChangedEvent|


## <a name="microsoftmediamediaservices"></a>Micro soft. Media/Media Services

Kosten voor export: gratis 

|Categorie |Weergave naam categorie|
|---|---|
|KeyDeliveryRequests|Aanvragen voor sleutel levering|


## <a name="microsoftnetworkapplicationgateways"></a>Micro soft. Network/applicationGateways

Kosten voor export: gratis 

|Categorie |Weergave naam categorie|
|---|---|
|ApplicationGatewayAccessLog|Application Gateway Access-logboek|
|ApplicationGatewayFirewallLog|Application Gateway firewall-logboek|
|ApplicationGatewayPerformanceLog|Prestatie logboek Application Gateway|


## <a name="microsoftnetworkazurefirewalls"></a>Micro soft. Network/azurefirewalls

Kosten voor export: gratis 

|Categorie |Weergave naam categorie|
|---|---|
|AzureFirewallApplicationRule|Toepassings regel Azure Firewall|
|AzureFirewallNetworkRule|Azure Firewall netwerk regel|


## <a name="microsoftnetworkbastionhosts"></a>Microsoft.Network/bastionHosts

Kosten voor export: gratis 

|Categorie |Weergave naam categorie|
|---|---|
|BastionAuditLogs|Controle logboeken voor Bastion|


## <a name="microsoftnetworkexpressroutecircuits"></a>Microsoft.Network/expressRouteCircuits

Kosten voor export: gratis 

|Categorie |Weergave naam categorie|
|---|---|
|PeeringRouteLog|Logboeken voor peering route tabel|


## <a name="microsoftnetworkfrontdoors"></a>Micro soft. Network/frontdoors

Kosten voor export: gratis 

|Categorie |Weergave naam categorie|
|---|---|
|FrontdoorAccessLog|-Ingang-toegangs logboek|
|FrontdoorWebApplicationFirewallLog|-Ingang Web Application firewall-logboek|


## <a name="microsoftnetworkloadbalancers"></a>Microsoft.Network/loadBalancers

Kosten voor export: gratis 

|Categorie |Weergave naam categorie|
|---|---|
|LoadBalancerAlertEvent|Load Balancer waarschuwings gebeurtenissen|
|LoadBalancerProbeHealthStatus|Load Balancer test status|


## <a name="microsoftnetworknetworksecuritygroups"></a>Micro soft. Network/networksecuritygroups

Kosten voor export: gratis 

|Categorie |Weergave naam categorie|
|---|---|
|NetworkSecurityGroupEvent|Gebeurtenis netwerk beveiligings groep|
|NetworkSecurityGroupFlowEvent|Stroom gebeurtenis van regel voor netwerk beveiligings groep|
|NetworkSecurityGroupRuleCounter|Teller van regel voor netwerk beveiligings groep|


## <a name="microsoftnetworkpublicipaddresses"></a>Micro soft. Network/publicIPAddresses

Kosten voor export: gratis 

|Categorie |Weergave naam categorie|
|---|---|
|DDoSMitigationFlowLogs|Stroom logboeken van DDoS-oplossings beslissingen|
|DDoSMitigationReports|Rapporten met DDoS-oplossingen|
|DDoSProtectionNotifications|DDoS-beveiligings meldingen|


## <a name="microsoftnetworktrafficmanagerprofiles"></a>Microsoft.Network/trafficManagerProfiles

Kosten voor export: gratis 

|Categorie |Weergave naam categorie|
|---|---|
|ProbeHealthStatusEvents|Gebeurtenis met resultaten van Traffic Manager test status|


## <a name="microsoftnetworkvirtualnetworkgateways"></a>Microsoft.Network/virtualNetworkGateways

Kosten voor export: gratis 

|Categorie |Weergave naam categorie|
|---|---|
|GatewayDiagnosticLog|Diagnostische logboeken van de gateway|
|IKEDiagnosticLog|Diagnostische logboeken voor IKE|
|P2SDiagnosticLog|Diagnostische logboeken van P2S|
|RouteDiagnosticLog|Diagnostische logboeken voor route ring|
|TunnelDiagnosticLog|Diagnostische logboeken voor tunnel|


## <a name="microsoftnetworkvirtualnetworks"></a>Micro soft. Network/virtualNetworks

Kosten voor export: gratis 

|Categorie |Weergave naam categorie|
|---|---|
|VMProtectionAlerts|Waarschuwingen voor VM-beveiliging|


## <a name="microsoftpowerbidedicatedcapacities"></a>Micro soft. PowerBIDedicated/capaciteiten

Kosten voor export: gratis 

|Categorie |Weergave naam categorie|
|---|---|
|Engine|Engine|


## <a name="microsoftrecoveryservicesvaults"></a>Micro soft. Recovery Services/kluizen

Kosten voor export: gratis 

|Categorie |Weergave naam categorie|
|---|---|
|AddonAzureBackupAlerts|Azure Backup waarschuwings gegevens van invoeg toepassing|
|AddonAzureBackupJobs|Taak gegevens van invoeg toepassing Azure Backup|
|AddonAzureBackupPolicy|Azure Backup beleids gegevens van invoeg toepassing|
|AddonAzureBackupProtectedInstance|Invoeg toepassing Azure Backup beveiligde instantie gegevens|
|AddonAzureBackupStorage|Invoeg Azure Backup opslag gegevens|
|AzureBackupReport|Azure Backup rapportage gegevens|
|AzureSiteRecoveryEvents|Azure Site Recovery gebeurtenissen|
|AzureSiteRecoveryJobs|Azure Site Recovery taken|
|AzureSiteRecoveryProtectedDiskDataChurn|Gegevens verloop van beveiligde schijf Azure Site Recovery|
|AzureSiteRecoveryRecoveryPoints|Azure Site Recovery herstel punten|
|AzureSiteRecoveryReplicatedItems|Gerepliceerde items Azure Site Recovery|
|AzureSiteRecoveryReplicationDataUploadRate|Upload frequentie van Azure Site Recovery replicatie gegevens|
|AzureSiteRecoveryReplicationStats|Replicatie statistieken Azure Site Recovery|
|CoreAzureBackup|Kern Azure Backup gegevens|


## <a name="microsoftrelaynamespaces"></a>Micro soft. relay/naam ruimten

Kosten voor export: gratis 

|Categorie |Weergave naam categorie|
|---|---|
|HybridConnectionsEvent|HybridConnections-gebeurtenissen|


## <a name="microsoftsearchsearchservices"></a>Micro soft. Search/searchServices

Kosten voor export: gratis 

|Categorie |Weergave naam categorie|
|---|---|
|OperationLogs|Bewerkings logboeken|


## <a name="microsoftservicebusnamespaces"></a>Microsoft.ServiceBus/namespaces

Kosten voor export: gratis 

|Categorie |Weergave naam categorie|
|---|---|
|OperationalLogs|Operationele logboeken|


## <a name="microsoftsignalrservicesignalr"></a>Microsoft.SignalRService/SignalR

Kosten voor export: gratis 

|Categorie |Weergave naam categorie|
|---|---|
|AllLogs|Logboeken van de Azure signalerings service.|


## <a name="microsoftsqlmanagedinstances"></a>Micro soft. SQL/managedInstances

Kosten voor export: gratis 

|Categorie |Weergave naam categorie|
|---|---|
|DevOpsOperationsAudit|Audit logboeken voor Devops-bewerkingen|
|ResourceUsageStats|Resource gebruiks statistieken|
|SQLSecurityAuditEvents|SQL-beveiligings controle gebeurtenis|


## <a name="microsoftsqlmanagedinstancesdatabases"></a>Micro soft. SQL/managedInstances/data bases

Kosten voor export: gratis 

|Categorie |Weergave naam categorie|
|---|---|
|Fouten|Fouten|
|QueryStoreRuntimeStatistics|Runtime statistieken voor query Store|
|QueryStoreWaitStatistics|Wacht statistieken voor query Store|
|SQLInsights|SQL Insights|


## <a name="microsoftsqlserversdatabases"></a>Microsoft.Sql/servers/databases

Kosten voor export: gratis 

|Categorie |Weergave naam categorie|
|---|---|
|AutomaticTuning|Automatisch instellen|
|Block|Block|
|DatabaseWaitStatistics|Data base-wacht statistieken|
|Impasses|Impasses|
|DevOpsOperationsAudit|Audit logboeken voor Devops-bewerkingen|
|DmsWorkers|DMS-werk rollen|
|Fouten|Fouten|
|ExecRequests|Exec-aanvragen|
|QueryStoreRuntimeStatistics|Runtime statistieken voor query Store|
|QueryStoreWaitStatistics|Wacht statistieken voor query Store|
|RequestSteps|Stappen voor aanvragen|
|SQLInsights|SQL Insights|
|SqlRequests|SQL-aanvragen|
|SQLSecurityAuditEvents|SQL-beveiligings controle gebeurtenis|
|Time-outs|Time-outs|
|Wacht|Wacht|


## <a name="microsoftstoragestorageaccountsblobservices"></a>Micro soft. Storage/Storage accounts/blobServices

Kosten voor export: betaald zoals beschreven in de sectie platform logs van [Azure monitor-pagina met prijzen.](https://azure.microsoft.com/pricing/details/monitor/) 

|Categorie |Weergave naam categorie|
|---|---|
|StorageDelete|StorageDelete|
|StorageRead|StorageRead|
|StorageWrite|StorageWrite|


## <a name="microsoftstoragestorageaccountsfileservices"></a>Micro soft. Storage/Storage accounts/fileServices

Kosten voor export: betaald zoals beschreven in de sectie platform logs van [Azure monitor-pagina met prijzen.](https://azure.microsoft.com/pricing/details/monitor/) 

|Categorie |Weergave naam categorie|
|---|---|
|StorageDelete|StorageDelete|
|StorageRead|StorageRead|
|StorageWrite|StorageWrite|


## <a name="microsoftstoragestorageaccountsqueueservices"></a>Micro soft. Storage/Storage accounts/queueServices

Kosten voor export: betaald zoals beschreven in de sectie platform logs van [Azure monitor-pagina met prijzen.](https://azure.microsoft.com/pricing/details/monitor/) 
 
|Categorie |Weergave naam categorie|
|---|---|
|StorageDelete|StorageDelete|
|StorageRead|StorageRead|
|StorageWrite|StorageWrite|


## <a name="microsoftstoragestorageaccountstableservices"></a>Micro soft. Storage/Storage accounts/tableServices

Kosten voor export: betaald zoals beschreven in de sectie platform logs van [Azure monitor-pagina met prijzen.](https://azure.microsoft.com/pricing/details/monitor/) 
 
|Categorie |Weergave naam categorie|
|---|---|
|StorageDelete|StorageDelete|
|StorageRead|StorageRead|
|StorageWrite|StorageWrite|


## <a name="microsoftstreamanalyticsstreamingjobs"></a>Micro soft. StreamAnalytics/streamingjobs

Kosten voor export: gratis 

|Categorie |Weergave naam categorie|
|---|---|
|Ontwerpen|Ontwerpen|
|Uitvoering|Uitvoering|


## <a name="microsoftsynapseworkspaces"></a>Micro soft. Synapse/werk ruimten

Kosten voor export: gratis 

|Categorie |Weergave naam categorie|
|---|---|
|BuiltinSqlReqsEnded|De ingebouwde aanvragen van SQL-groep zijn beëindigd|
|GatewayApiRequests|Synapse Gateway-API-aanvragen|
|SQLSecurityAuditEvents|SQL-beveiligings controle gebeurtenis|
|SynapseRbacOperations|Synapse RBAC-bewerkingen|


## <a name="microsoftsynapseworkspacesbigdatapools"></a>Micro soft. Synapse/werk ruimten/bigDataPools

Kosten voor export: gratis 

|Categorie |Weergave naam categorie|
|---|---|
|BigDataPoolAppsEnded|Toepassingen voor Big Data-groep gestopt|


## <a name="microsoftsynapseworkspacessqlpools"></a>Micro soft. Synapse/werk ruimten/sqlPools

Kosten voor export: gratis 

|Categorie |Weergave naam categorie|
|---|---|
|DmsWorkers|DMS-werk rollen|
|ExecRequests|Exec-aanvragen|
|RequestSteps|Stappen voor aanvragen|
|SqlRequests|SQL-aanvragen|
|SQLSecurityAuditEvents|SQL-beveiligings controle gebeurtenis|
|Wacht|Wacht|


## <a name="microsoftwebhostingenvironments"></a>micro soft. Web/hostingenvironments

Kosten voor export: gratis 

|Categorie |Weergave naam categorie|
|---|---|
|AppServiceEnvironmentPlatformLogs|App Service Environment-platform logboeken|


## <a name="microsoftwebsites"></a>micro soft. web/sites

Kosten voor export: gratis 


|Categorie |Weergave naam categorie|
|---|---|
|AppServiceAppLogs|Toepassings logboeken App Service|
|AppServiceAuditLogs|Access-controle logboeken|
|AppServiceConsoleLogs|App Service-console logboeken|
|AppServiceFileAuditLogs|Controle logboeken voor het wijzigen van de site-inhoud|
|AppServiceHTTPLogs|HTTP-logboeken|
|FunctionAppLogs|Toepassings logboeken van functie|


## <a name="microsoftwebsitesslots"></a>micro soft. web/sites/sleuven

Kosten voor export: gratis 


|Categorie |Weergave naam categorie|
|---|---|
|AppServiceAppLogs|Toepassings logboeken App Service|
|AppServiceAuditLogs|Access-controle logboeken|
|AppServiceConsoleLogs|App Service-console logboeken|
|AppServiceFileAuditLogs|Controle logboeken voor het wijzigen van de site-inhoud|
|AppServiceHTTPLogs|HTTP-logboeken|
|FunctionAppLogs|Toepassings logboeken van functie|


## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over bron logboeken](./platform-logs-overview.md)
* [Resource bron logboeken streamen naar **Event hubs**](./resource-logs.md#send-to-azure-event-hubs)
* [Diagnostische instellingen voor bron logboek wijzigen met behulp van de Azure Monitor REST API](/rest/api/monitor/diagnosticsettings)
* [Logboeken analyseren vanuit Azure Storage met Log Analytics](./resource-logs.md#send-to-log-analytics-workspace)

