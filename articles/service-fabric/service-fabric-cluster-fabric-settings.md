---
title: Instellingen van Azure Service Fabric cluster wijzigen
description: In dit artikel worden de infrastructuur instellingen en het Fabric-upgrade beleid beschreven dat u kunt aanpassen.
ms.topic: reference
ms.date: 08/30/2019
ms.openlocfilehash: 1f16e89dd1131f6aea64e5e72a342b3b737f3728
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/26/2020
ms.locfileid: "96187218"
---
# <a name="customize-service-fabric-cluster-settings"></a>Instellingen voor Service Fabric-cluster aanpassen
In dit artikel worden de verschillende infrastructuur instellingen voor uw Service Fabric cluster beschreven die u kunt aanpassen. Voor clusters die worden gehost in azure, kunt u instellingen aanpassen via de [Azure Portal](https://portal.azure.com) of met behulp van een Azure Resource Manager sjabloon. Zie [de configuratie van een Azure-cluster upgraden](service-fabric-cluster-config-upgrade-azure.md)voor meer informatie. Voor zelfstandige clusters past u de instellingen aan door de *ClusterConfig.jsin* het bestand bij te werken en een configuratie-upgrade uit te voeren op uw cluster. Zie [de configuratie van een zelfstandig cluster upgraden](service-fabric-cluster-config-upgrade-windows-server.md)voor meer informatie.

Er zijn drie verschillende upgrade beleidsregels:

- **Dynamisch** : wijzigingen in een dynamische configuratie veroorzaken geen processen die worden uitgevoerd door service Fabric processen of uw hostproces processen. 
- **Statisch** : wijzigingen in een statische configuratie zorgt ervoor dat het service Fabric knoop punt opnieuw wordt opgestart om de wijziging te kunnen gebruiken. Services op de knoop punten worden opnieuw opgestart.
- **Getalg** : deze instellingen kunnen niet worden gewijzigd. Als u deze instellingen wilt wijzigen, moet het cluster worden vernietigd en moet er een nieuw cluster worden gemaakt. 

Hier volgt een lijst met infrastructuur instellingen die u kunt aanpassen, geordend op sectie.

## <a name="applicationgatewayhttp"></a>Toepassings Gateway/http

| **Parameter** | **Toegestane waarden** | **Upgrade beleid** | **Uitleg of korte beschrijving** |
| --- | --- | --- | --- |
|ApplicationCertificateValidationPolicy|teken reeks, standaard is ' geen '|Statisch| Hiermee wordt het server certificaat niet gevalideerd; de aanvraag is voltooid. Raadpleeg config ServiceCertificateThumbprints voor de door komma's gescheiden lijst met vinger afdrukken van de externe certificaten die de omgekeerde proxy kan vertrouwen. Raadpleeg config ServiceCommonNameAndIssuer voor de naam van de certificaat houder en de vinger afdruk van de verlener van de externe certificaten die de omgekeerde proxy kan vertrouwen. Zie voor meer informatie [reverse proxy beveiligde verbinding](service-fabric-reverseproxy-configure-secure-communication.md#secure-connection-establishment-between-the-reverse-proxy-and-services). |
|BodyChunkSize |Uint, standaard waarde is 16384 |Dynamisch| Geeft de grootte van voor het segment in bytes dat wordt gebruikt om de hoofd tekst te lezen. |
|CrlCheckingFlag|uint, de standaard waarde is 0x40000000 |Dynamisch| Vlaggen voor validatie van de certificaat keten van de toepassing/service; bijvoorbeeld CRL check 0x10000000 CERT_CHAIN_REVOCATION_CHECK_END_CERT 0x20000000 CERT_CHAIN_REVOCATION_CHECK_CHAIN 0x40000000 CERT_CHAIN_REVOCATION_CHECK_CHAIN_EXCLUDE_ROOT 0x80000000 CERT_CHAIN_REVOCATION_CHECK_CACHE_ONLY instelling op 0 Hiermee wordt de volledige lijst met ondersteunde waarden voor het controleren van de CRL beschreven door dwFlags van CertGetCertificateChain: https://msdn.microsoft.com/library/windows/desktop/aa376078(v=vs.85).aspx  |
|DefaultHttpRequestTimeout |Tijd in seconden. de standaard waarde is 120 |Dynamisch|Geef een tijds duur in seconden op.  Geeft de standaard time-out voor de aanvraag voor de HTTP-aanvragen die worden verwerkt in de http-app-gateway. |
|ForwardClientCertificate|BOOL, default is FALSE|Dynamisch|Als deze eigenschap is ingesteld op False, wordt voor de omgekeerde proxy geen aanvraag voor het client certificaat gemaakt. Als deze eigenschap is ingesteld op True, wordt door de reverse-proxy een aanvraag voor het client certificaat aangevraagd tijdens de TLS-Handshake en wordt de PEM-indelings teken reeks met base64 door gegeven aan de service in een header met de naam X-client-certificaat. de aanvraag kan niet worden uitgevoerd met de juiste status code na het controleren van de certificaat gegevens. Als dit het geval is en de client geen certificaat presenteert, wordt een lege header door de omgekeerde proxy doorgestuurd en kan de service de aanvraag afhandelen. Omgekeerde proxy fungeert als transparante laag. Zie [verificatie van client certificaten instellen](service-fabric-reverseproxy-configure-secure-communication.md#setting-up-client-certificate-authentication-through-the-reverse-proxy)voor meer informatie. |
|GatewayAuthCredentialType |teken reeks, standaard is ' geen ' |Statisch| Hiermee wordt het type beveiligings referenties aangegeven dat moet worden gebruikt bij het eind punt van de http-app-gateway. geldige waarden zijn geen/x509. |
|GatewayX509CertificateFindType |teken reeks, standaard is ' FindByThumbprint ' |Dynamisch| Hiermee wordt aangegeven hoe moet worden gezocht naar het certificaat in het archief dat is opgegeven door de GatewayX509CertificateStoreName ondersteunde waarde: FindByThumbprint; FindBySubjectName. |
|GatewayX509CertificateFindValue | teken reeks, standaard instelling is |Dynamisch| Zoek filter waarde die wordt gebruikt voor het zoeken van het certificaat van de http-app-gateway. Dit certificaat is geconfigureerd op het HTTPS-eind punt en kan ook worden gebruikt om de identiteit van de app te verifiëren, indien nodig door de services. FindValue wordt eerst opgezocht; en als dat niet bestaat; FindValueSecondary wordt opgezocht. |
|GatewayX509CertificateFindValueSecondary | teken reeks, standaard instelling is |Dynamisch|Zoek filter waarde die wordt gebruikt voor het zoeken van het certificaat van de http-app-gateway. Dit certificaat is geconfigureerd op het HTTPS-eind punt en kan ook worden gebruikt om de identiteit van de app te verifiëren, indien nodig door de services. FindValue wordt eerst opgezocht; en als dat niet bestaat; FindValueSecondary wordt opgezocht.|
|GatewayX509CertificateStoreName |teken reeks, standaard is ' My ' |Dynamisch| Naam van X. 509-certificaat archief dat certificaat voor http-app-gateway bevat. |
|HttpRequestConnectTimeout|Time span, standaard waarde is gebruikelijk:: time span:: FromSeconds (5)|Dynamisch|Geef een tijds duur in seconden op.  Geeft de Connect-time-out voor de HTTP-aanvragen die worden verzonden vanuit de http-app-gateway.  |
|IgnoreCrlOfflineError|BOOL, default is TRUE|Dynamisch|Hiermee wordt aangegeven of de CRL-offline fout moet worden genegeerd voor verificatie van de toepassings-of service certificaat. |
|IsEnabled |BOOL, default is False |Statisch| Hiermee wordt de HttpApplicationGateway in-of uitgeschakeld. HttpApplicationGateway is standaard uitgeschakeld en deze configuratie moet worden ingesteld om het in te scha kelen. |
|NumberOfParallelOperations | Uint, standaard waarde is 5000 |Statisch|Aantal lees bewerkingen dat naar de http-server wachtrij moet worden gepost. Hiermee bepaalt u het aantal gelijktijdige aanvragen waaraan de HttpGateway kan voldoen. |
|RemoveServiceResponseHeaders|teken reeks, standaard waarde is "date; Naam|Statisch|Punt komma/door komma's gescheiden lijst met antwoord koppen die worden verwijderd uit het antwoord van de service; voordat u deze naar de client doorstuurt. Als deze is ingesteld op een lege teken reeks; alle kopteksten die door de service zijn geretourneerd, worden door gegeven. dat wil zeggen de datum en server niet overschrijven |
|ResolveServiceBackoffInterval |Tijd in seconden, standaard waarde is 5 |Dynamisch|Geef een tijds duur in seconden op.  Hiermee geeft u het standaard interval voor het maken van een mislukte omzetting van een service opnieuw. |
|SecureOnlyMode|BOOL, default is FALSE|Dynamisch| SecureOnlyMode: True: de omgekeerde proxy wordt alleen doorgestuurd naar services die beveiligde eind punten publiceren. onwaar: omgekeerde proxy kan aanvragen door sturen naar beveiligde en niet-beveiligde eind punten. Zie voor meer informatie een [omgekeerde logica voor het selecteren van proxy-eind punten](service-fabric-reverseproxy-configure-secure-communication.md#endpoint-selection-logic-when-services-expose-secure-as-well-as-unsecured-endpoints).  |
|ServiceCertificateThumbprints|teken reeks, standaard instelling is|Dynamisch|De door komma's gescheiden lijst met vinger afdrukken van de externe certificaten die de omgekeerde proxy kan vertrouwen. Zie voor meer informatie [reverse proxy beveiligde verbinding](service-fabric-reverseproxy-configure-secure-communication.md#secure-connection-establishment-between-the-reverse-proxy-and-services). |

## <a name="applicationgatewayhttpservicecommonnameandissuer"></a>Toepassings Gateway/http/ServiceCommonNameAndIssuer

| **Parameter** | **Toegestane waarden** | **Upgrade beleid** | **Uitleg of korte beschrijving** |
| --- | --- | --- | --- |
|PropertyGroup|X509NameMap, standaard instelling is geen|Dynamisch| De naam van de certificaat houder en de vinger afdruk van de verlener van het externe certificaat dat de reverse proxy kan vertrouwen. Zie voor meer informatie [reverse proxy beveiligde verbinding](service-fabric-reverseproxy-configure-secure-communication.md#secure-connection-establishment-between-the-reverse-proxy-and-services). |

## <a name="backuprestoreservice"></a>BackupRestoreService

| **Parameter** | **Toegestane waarden** | **Upgrade beleid** | **Uitleg of korte beschrijving** |
| --- | --- | --- | --- |
|MinReplicaSetSize|int, standaard is 0|Statisch|De MinReplicaSetSize voor BackupRestoreService |
|PlacementConstraints|teken reeks, standaard instelling is|Statisch|    De PlacementConstraints voor de BackupRestore-service |
|SecretEncryptionCertThumbprint|teken reeks, standaard instelling is|Dynamisch|Vinger afdruk van het x509-certificaat van de geheime versleuteling |
|SecretEncryptionCertX509StoreName|De aanbevolen waarde voor de teken reeks is ' My ' (geen standaard instelling) |    Dynamisch|    Dit geeft het certificaat aan dat moet worden gebruikt voor het versleutelen en ontsleutelen van de cred-naam van het X. 509-certificaat archief dat wordt gebruikt voor het versleutelen van de opslag referenties die worden gebruikt door de back-upherstel service |
|TargetReplicaSetSize|int, standaard is 0|Statisch| De TargetReplicaSetSize voor BackupRestoreService |

## <a name="clustermanager"></a>ClusterManager

| **Parameter** | **Toegestane waarden** | **Upgrade beleid** | **Uitleg of korte beschrijving** |
| --- | --- | --- | --- |
|AllowCustomUpgradeSortPolicies | BOOL, default is False |Dynamisch|Hiermee wordt aangegeven of het sorteer beleid voor aangepaste upgrades wel of niet is toegestaan. Deze functie wordt gebruikt om een upgrade uit te voeren met twee fasen. Service Fabric 6,5 voegt ondersteuning toe voor het opgeven van sorteer beleid voor upgrade domeinen tijdens het upgraden van clusters of toepassingen. Ondersteunde beleids regels zijn numeriek, Lexicographical, ReverseNumeric en ReverseLexicographical. De standaard waarde is numeriek. Om deze functie te kunnen gebruiken, moet de ClusterManager/AllowCustomUpgradeSortPolicies van het cluster manifest worden ingesteld op True als een tweede configuratie-upgrade stap nadat de 6,5-code van SF is voltooid. Het is belang rijk dat dit gebeurt in twee fasen, anders kan de code-upgrade tijdens de eerste upgrade worden verward met de upgrade volgorde.|
|EnableDefaultServicesUpgrade | BOOL, default is False |Dynamisch|Schakel het bijwerken van standaard services tijdens de upgrade van de toepassing in. Standaard beschrijvingen van services worden na de upgrade overschreven. |
|FabricUpgradeHealthCheckInterval |Tijd in seconden, standaard waarde is 60 |Dynamisch|De frequentie van de status controles tijdens een bewaakte infrastructuur upgrade |
|FabricUpgradeStatusPollInterval |Tijd in seconden, standaard waarde is 60 |Dynamisch|De frequentie van polling voor de upgrade status van de infra structuur. Deze waarde bepaalt de frequentie van de update voor elke GetFabricUpgradeProgress-aanroep |
|ImageBuilderTimeoutBuffer |Tijd in seconden, standaard waarde is 3 |Dynamisch|Geef een tijds duur in seconden op. De hoeveelheid tijd die is toegestaan voor het retour neren van specifieke time-outfouten van installatie kopieën naar de client. Als deze buffer te klein is; vervolgens wordt er een time-out opgetreden voor de-server en wordt er een algemene verbindingstime-fout opgehaald. |
|InfrastructureTaskHealthCheckRetryTimeout | Tijd in seconden, standaard waarde is 60 |Dynamisch|Geef een tijds duur in seconden op. De hoeveelheid tijd die nodig is voor het opnieuw proberen van mislukte status controles tijdens het naverwerken van een infrastructuur taak. Als u een geslaagde status controle bekijkt, wordt deze timer opnieuw ingesteld. |
|InfrastructureTaskHealthCheckStableDuration | Tijd in seconden, standaard waarde is 0|Dynamisch| Geef een tijds duur in seconden op. De hoeveelheid tijd die moet worden gecontroleerd op opeenvolgende status controles voordat de naverwerking van een infrastructuur taak is voltooid. Als u een mislukte status controle waarneemt, wordt deze timer opnieuw ingesteld. |
|InfrastructureTaskHealthCheckWaitDuration |Tijd in seconden, standaard waarde is 0|Dynamisch| Geef een tijds duur in seconden op. De hoeveelheid tijd die moet worden gewacht voordat status controles worden gestart na het naverwerken van een infrastructuur taak. |
|InfrastructureTaskProcessingInterval | Tijd in seconden, standaard waarde is 10 |Dynamisch|Geef een tijds duur in seconden op. Het verwerkings interval dat wordt gebruikt door de computer voor de verwerking van de infrastructuur taak. |
|MaxCommunicationTimeout |Tijd in seconden, standaard waarde is 600 |Dynamisch|Geef een tijds duur in seconden op. De maximale time-out voor interne communicatie tussen ClusterManager en andere systeem services (dat wil zeggen: Naming Service; Failover Manager en etc.). Deze time-out moet kleiner zijn dan Global MaxOperationTimeout (aangezien er mogelijk meerdere communicatie tussen systeem onderdelen is voor elke client bewerking). |
|MaxDataMigrationTimeout |Tijd in seconden, standaard waarde is 600 |Dynamisch|Geef een tijds duur in seconden op. De maximale time-out voor herstel bewerkingen voor gegevens migratie nadat een infrastructuur upgrade is uitgevoerd. |
|MaxOperationRetryDelay |Tijd in seconden, standaard waarde is 5|Dynamisch| Geef een tijds duur in seconden op. De maximale vertraging voor interne nieuwe pogingen wanneer er fouten optreden. |
|MaxOperationTimeout |Tijd in seconden, de standaard waarde is MaxValue |Dynamisch| Geef een tijds duur in seconden op. De maximale algemene time-out voor intern verwerkings bewerkingen op ClusterManager. |
|MaxTimeoutRetryBuffer | Tijd in seconden, standaard waarde is 600 |Dynamisch|Geef een tijds duur in seconden op. De maximale time-out voor de bewerking bij een intern nieuwe poging vanwege time-outs `<Original Time out> + <MaxTimeoutRetryBuffer>` . Extra time-out wordt toegevoegd in stappen van MinOperationTimeout. |
|MinOperationTimeout | Tijd in seconden, standaard waarde is 60 |Dynamisch|Geef een tijds duur in seconden op. De minimale algemene time-out voor intern verwerkings bewerkingen op ClusterManager. |
|MinReplicaSetSize |Int, standaard is 3 |Niet toegestaan|De MinReplicaSetSize voor ClusterManager. |
|PlacementConstraints | teken reeks, standaard instelling is |Niet toegestaan|De PlacementConstraints voor ClusterManager. |
|QuorumLossWaitDuration |Tijd in seconden, de standaard waarde is MaxValue |Niet toegestaan| Geef een tijds duur in seconden op. De QuorumLossWaitDuration voor ClusterManager. |
|ReplicaRestartWaitDuration |Tijd in seconden, standaard is (60,0 \* 30)|Niet toegestaan|Geef een tijds duur in seconden op. De ReplicaRestartWaitDuration voor ClusterManager. |
|ReplicaSetCheckTimeoutRollbackOverride |Tijd in seconden, standaard waarde is 1200 |Dynamisch| Geef een tijds duur in seconden op. Als ReplicaSetCheckTimeout is ingesteld op de maximum waarde DWORD; vervolgens wordt deze vervangen door de waarde van deze configuratie voor het terugdraaien. De waarde die wordt gebruikt voor het door sturen wordt nooit overschreven. |
|SkipRollbackUpdateDefaultService | BOOL, default is False |Dynamisch|In CM worden de bijgewerkte standaard services die tijdens het terugdraaien van de upgrade van de toepassing worden teruggezet, overgeslagen. |
|StandByReplicaKeepDuration | Tijd in seconden, standaard is (3600,0 \* 2)|Niet toegestaan|Geef een tijds duur in seconden op. De StandByReplicaKeepDuration voor ClusterManager. |
|TargetReplicaSetSize |Int, standaard is 7 |Niet toegestaan|De TargetReplicaSetSize voor ClusterManager. |
|UpgradeHealthCheckInterval |Tijd in seconden, standaard waarde is 60 |Dynamisch|De frequentie van status controles tijdens een upgrade van bewaakte toepassingen |
|UpgradeStatusPollInterval |Tijd in seconden, standaard waarde is 60 |Dynamisch|De frequentie van polling voor de upgrade status van de toepassing. Deze waarde bepaalt de frequentie van de update voor elke GetApplicationUpgradeProgress-aanroep |
|CompleteClientRequest | BOOL, default is False |Dynamisch| Voltooi de client aanvraag wanneer deze door CM wordt geaccepteerd. |

## <a name="common"></a>Algemeen

| **Parameter** | **Toegestane waarden** | **Upgrade beleid** | **Uitleg of korte beschrijving** |
| --- | --- | --- | --- |
|PerfMonitorInterval |Tijd in seconden, standaard waarde is 1 |Dynamisch|Geef een tijds duur in seconden op. Interval voor prestatie bewaking. Als u instelt op 0 of een negatieve waarde, wordt de bewaking uitgeschakeld. |

## <a name="defragmentationemptynodedistributionpolicy"></a>DefragmentationEmptyNodeDistributionPolicy
| **Parameter** | **Toegestane waarden** |**Upgrade beleid**| **Uitleg of korte beschrijving** |
| --- | --- | --- | --- |
|PropertyGroup|KeyIntegerValueMap, standaard instelling is geen|Dynamisch|Hiermee geeft u op dat de beleids defragmentatie wordt gevolgd bij het leegmaken van knoop punten. Voor een gegeven metrische waarde 0 geeft aan dat SF knoop punten gelijkmatig moet proberen te defragmenteren in de UDs-en Fd's. 1 geeft alleen aan dat de knoop punten moeten worden gedefragmenteerd |

## <a name="defragmentationmetrics"></a>DefragmentationMetrics
| **Parameter** | **Toegestane waarden** |**Upgrade beleid**| **Uitleg of korte beschrijving** |
| --- | --- | --- | --- |
|PropertyGroup|KeyBoolValueMap, standaard instelling is geen|Dynamisch|Bepaalt de set metrische gegevens die voor defragmentatie moet worden gebruikt en niet voor taak verdeling. |

## <a name="defragmentationmetricspercentornumberofemptynodestriggeringthreshold"></a>DefragmentationMetricsPercentOrNumberOfEmptyNodesTriggeringThreshold
| **Parameter** | **Toegestane waarden** |**Upgrade beleid**| **Uitleg of korte beschrijving** |
| --- | --- | --- | --- |
|PropertyGroup|KeyDoubleValueMap, standaard instelling is geen|Dynamisch|Bepaalt het aantal beschik bare knoop punten die nodig zijn om het cluster te defragmenteren door een percentage in het bereik [0,0-1,0] op te geven of het aantal lege knoop punten als getal >= 1,0 |

## <a name="diagnostics"></a>Diagnostiek

| **Parameter** | **Toegestane waarden** | **Upgrade beleid** | **Uitleg of korte beschrijving** |
| --- | --- | --- | --- |
|AdminOnlyHttpAudit |BOOL, default is True | Dynamisch | Sluit HTTP-aanvragen uit die niet van invloed zijn op de status van het cluster van controle. Dat alleen aanvragen van het type GET worden uitgesloten; maar dit is onderhevig aan wijzigingen. |
|AppDiagnosticStoreAccessRequiresImpersonation |BOOL, default is True | Dynamisch |Hiermee wordt aangegeven of imitatie is vereist bij het openen van diagnostische gegevens namens de toepassing. |
|AppEtwTraceDeletionAgeInDays |Int, standaard is 3 | Dynamisch |Aantal dagen waarna oude ETL-bestanden met toepassings-ETW-traceringen worden verwijderd. |
|ApplicationLogsFormatVersion |Int, standaard is 0 | Dynamisch |De versie voor de toepassings Logboeken. Ondersteunde waarden zijn 0 en 1. Versie 1 bevat meer velden uit het ETW-gebeurtenis record dan versie 0. |
|AuditHttpRequests |BOOL, default is False | Dynamisch | Schakel HTTP-controle in of uit. Het doel van de controle is om de activiteiten te zien die zijn uitgevoerd op het cluster. inclusief wie de aanvraag heeft gestart. Houd er rekening mee dat dit een beste logboek registratie voor pogingen is. en tracerings verlies kan optreden. HTTP-aanvragen met verificatie van de gebruiker zijn niet geregistreerd. |
|CaptureHttpTelemetry|BOOL, default is True | Dynamisch | Schakel HTTP-telemetrie in of uit. Het doel van telemetrie is dat Service Fabric telemetrie-gegevens kunnen vastleggen om toekomstige werkzaamheden te plannen en probleem gebieden te identificeren. Telemetrie legt geen persoonlijke gegevens of de hoofd tekst van de aanvraag vast. Alle HTTP-aanvragen worden door telemetrie vastgelegd, tenzij anders is geconfigureerd. |
|ClusterId |Tekenreeks | Dynamisch |De unieke id van het cluster. Deze wordt gegenereerd wanneer het cluster wordt gemaakt. |
|ConsumerInstances |Tekenreeks | Dynamisch |De lijst met DCA-consumenten exemplaren. |
|DiskFullSafetySpaceInMB |Int, standaard waarde is 1024 | Dynamisch |Resterende schijf ruimte in MB om te beschermen tegen gebruik door DCA. |
|EnableCircularTraceSession |BOOL, default is False | Statisch |Vlag geeft aan of circulaire tracerings sessies moeten worden gebruikt. |
|EnablePlatformEventsFileSink |BOOL, default is False | Statisch |Platform gebeurtenissen die naar de schijf worden geschreven, in-of uitschakelen |
|EnableTelemetry |BOOL, default is True | Dynamisch |Hiermee wordt telemetrie in-of uitgeschakeld. |
|FailuresOnlyHttpTelemetry | BOOL, default is False | Dynamisch | Als HTTP-telemetrie-opname is ingeschakeld; alleen mislukte aanvragen vastleggen. Zo kunt u het aantal gebeurtenissen dat wordt gegenereerd voor telemetrie verminderen. |
|HttpTelemetryCapturePercentage | int, standaard waarde is 50 | Dynamisch | Als HTTP-telemetrie-opname is ingeschakeld; Leg alleen een wille keurig percentage van aanvragen vast. Zo kunt u het aantal gebeurtenissen dat wordt gegenereerd voor telemetrie verminderen. |
|MaxDiskQuotaInMB |Int, standaard waarde is 65536 | Dynamisch |Schijf quotum in MB voor Windows Fabric-logboek bestanden. |
|ProducerInstances |Tekenreeks | Dynamisch |De lijst met DCA producer-exemplaren. |

## <a name="dnsservice"></a>DNS
| **Parameter** | **Toegestane waarden** |**Upgrade beleid**| **Uitleg of korte beschrijving** |
| --- | --- | --- | --- |
|EnablePartitionedQuery|BOOL, default is FALSE|Statisch|De vlag voor het inschakelen van ondersteuning voor DNS-query's voor gepartitioneerde Services. De functie is standaard uitgeschakeld. Zie [service Fabric DNS-service](service-fabric-dnsservice.md) voor meer informatie.|
|InstanceCount|int, standaard is-1|Statisch|De standaard waarde is-1, wat betekent dat DNS wordt uitgevoerd op elk knoop punt. OneBox moet dit zijn ingesteld op 1 omdat DNS een bekende poort 53 gebruikt, zodat deze niet meerdere exemplaren op dezelfde computer kan hebben.|
|IsEnabled|BOOL, default is FALSE|Statisch|Hiermee wordt DNS in-of uitgeschakeld. DNS is standaard uitgeschakeld en deze configuratie moet worden ingesteld om het in te scha kelen. |
|PartitionPrefix|teken reeks, standaard is '--'|Statisch|Hiermee wordt de teken reeks waarde voor het partitie voorvoegsel in DNS-query's voor gepartitioneerde services beheerd. De waarde: <ul><li>Moet voldoen aan de RFC-normen, omdat deze deel uitmaakt van een DNS-query.</li><li>Mag geen punt bevatten: '. ', aangezien punt conflicten met het gedrag van DNS-achtervoegsels.</li><li>Mag niet langer zijn dan 5 tekens.</li><li>Kan geen lege teken reeks zijn.</li><li>Als de instelling PartitionPrefix wordt overschreven, moet PartitionSuffix worden overschreven en vice versa.</li></ul>Zie [service Fabric DNS-service](service-fabric-dnsservice.md)voor meer informatie.|
|PartitionSuffix|teken reeks, standaard instelling is|Statisch|Hiermee beheert u de waarde van de teken reeks voor partitie achtervoegsel in DNS-query's voor gepartitioneerde Services. De waarde: <ul><li>Moet voldoen aan de RFC-normen, omdat deze deel uitmaakt van een DNS-query.</li><li>Mag geen punt bevatten: '. ', aangezien punt conflicten met het gedrag van DNS-achtervoegsels.</li><li>Mag niet langer zijn dan 5 tekens.</li><li>Als de instelling PartitionPrefix wordt overschreven, moet PartitionSuffix worden overschreven en vice versa.</li></ul>Zie [service Fabric DNS-service](service-fabric-dnsservice.md)voor meer informatie. |
|RetryTransientFabricErrors|BOOL, default is True|Statisch|De instelling bepaalt de mogelijkheden voor opnieuw proberen bij het aanroepen van Service Fabric-Api's vanuit DNS. Wanneer deze functie is ingeschakeld, worden er Maxi maal drie keer opnieuw geprobeerd als er een tijdelijke fout optreedt.|

## <a name="eventstoreservice"></a>EventStoreService

| **Parameter** | **Toegestane waarden** | **Upgrade beleid** | **Uitleg of korte beschrijving** |
| --- | --- | --- | --- |
|MinReplicaSetSize|int, standaard is 0|Statisch|De MinReplicaSetSize voor de Event Store-service |
|PlacementConstraints|teken reeks, standaard instelling is|Statisch|    De PlacementConstraints voor de Event Store-service |
|TargetReplicaSetSize|int, standaard is 0|Statisch| De TargetReplicaSetSize voor de Event Store-service |

## <a name="fabricclient"></a>FabricClient

| **Parameter** | **Toegestane waarden** | **Upgrade beleid** | **Uitleg of korte beschrijving** |
| --- | --- | --- | --- |
|ConnectionInitializationTimeout |Tijd in seconden, standaard waarde is 2 |Dynamisch|Geef een tijds duur in seconden op. Time-outinterval van de verbinding voor elke keer dat een client verbinding probeert te openen met de gateway.|
|HealthOperationTimeout |Tijd in seconden, standaard waarde is 120 |Dynamisch|Geef een tijds duur in seconden op. De time-out voor een rapport bericht dat naar Health Manager wordt verzonden. |
|HealthReportRetrySendInterval |Tijd in seconden, standaard waarde is 30, minimum is 1 |Dynamisch|Geef een tijds duur in seconden op. Het interval waarmee het rapport onderdeel samengevoegde status rapporten opnieuw verzendt naar Health Manager. |
|HealthReportSendInterval |Tijd in seconden, standaard waarde is 30 |Dynamisch|Geef een tijds duur in seconden op. Het interval waarmee het rapport onderdeel samengevoegde status rapporten verzendt naar Health Manager. |
|KeepAliveIntervalInSeconds |Int, standaard waarde is 20 |Statisch|Het interval waarmee het FabricClient-Trans Port keep-alive-berichten naar de Gateway verzendt. Voor 0; keepAlive is uitgeschakeld. Moet een positieve waarde zijn. |
|MaxFileSenderThreads |Uint, standaard waarde is 10 |Statisch|Het maximum aantal bestanden dat parallel wordt overgedragen. |
|NodeAddresses |teken reeks, standaard instelling is |Statisch|Een verzameling adressen (verbindings reeksen) op verschillende knoop punten die kunnen worden gebruikt om te communiceren met de Naming Service. In eerste instantie maakt de client een wille keurige adres selectie. Als er meer dan één connection string wordt opgegeven en een verbinding mislukt vanwege een communicatie-of time-outfout; de client schakelt over om het volgende adres opeenvolgend te gebruiken. Zie de sectie Naming Service-adres opnieuw proberen voor meer informatie over het opnieuw proberen van semantiek. |
|PartitionLocationCacheLimit |Int, standaard waarde is 100000 |Statisch|Het aantal partities in de cache voor service omzetting (ingesteld op 0 voor geen limiet). |
|RetryBackoffInterval |Tijd in seconden, standaard waarde is 3 |Dynamisch|Geef een tijds duur in seconden op. Het interval voor opnieuw proberen om de bewerking opnieuw uit te voeren. |
|ServiceChangePollInterval |Tijd in seconden, standaard waarde is 120 |Dynamisch|Geef een tijds duur in seconden op. Het interval tussen opeenvolgende polls voor service wijzigingen van de client naar de gateway voor geregistreerde service wijzigings meldingen retour aanroepen. |

## <a name="fabrichost"></a>FabricHost

| **Parameter** | **Toegestane waarden** | **Upgrade beleid** | **Uitleg of korte beschrijving** |
| --- | --- | --- | --- |
|ActivationMaxFailureCount |Int, standaard waarde is 10 |Dynamisch|Dit is het maximum aantal waarvoor het systeem opnieuw moet worden geactiveerd voordat het kan worden uitgevoerd. |
|ActivationMaxRetryInterval |Tijd in seconden, standaard waarde is 300 |Dynamisch|Geef een tijds duur in seconden op. Maximale interval voor opnieuw proberen voor activering. Bij elke doorlopende storing wordt het interval voor nieuwe pogingen berekend als min (ActivationMaxRetryInterval; Aantal doorlopende fouten * ActivationRetryBackoffInterval). |
|ActivationRetryBackoffInterval |Tijd in seconden, standaard waarde is 5 |Dynamisch|Geef een tijds duur in seconden op. Uitstel interval bij elke activerings fout; bij elke continue activerings fout zal het systeem de activering opnieuw proberen voor de MaxActivationFailureCount. Het interval voor nieuwe pogingen bij elke poging is een product van een doorlopende activerings fout en het back-upinterval van de activering. |
|EnableRestartManagement |BOOL, default is False |Dynamisch|Hiermee schakelt u het opnieuw opstarten van de server in. |
|EnableServiceFabricAutomaticUpdates |BOOL, default is False |Dynamisch|Dit is om automatische infrastructuur updates via Windows Update in te scha kelen. |
|EnableServiceFabricBaseUpgrade |BOOL, default is False |Dynamisch|Dit is om de basis update voor de server in te scha kelen. |
|FailureReportingExpeditedReportingIntervalEnabled | BOOL, default is True | Statisch | Maakt snellere upload tarieven mogelijk in DCA wanneer FabricHost zich in de modus voor fout rapportage bevindt. |
|FailureReportingTimeout | Time span, standaard waarde is gebruikelijk:: time span:: FromSeconds (60) | Statisch |Geef een tijds duur in seconden op. Time-out voor DCA-fout rapportage in de case FabricHost stuit op het starten van een vroege fase. | 
|RunDCAOnStartupFailure | BOOL, default is True | Statisch |Hiermee wordt bepaald of DCA wordt gestart voor het uploaden van Logboeken bij het starten van opstart problemen in FabricHost. | 
|StartTimeout |Tijd in seconden, standaard waarde is 300 |Dynamisch|Geef een tijds duur in seconden op. Time-out voor het opstarten van fabricactivationmanager. |
|StopTimeout |Tijd in seconden, standaard waarde is 300 |Dynamisch|Geef een tijds duur in seconden op. De time-out voor de activering van de gehoste service; deactiveren en upgraden. |

## <a name="fabricnode"></a>FabricNode

| **Parameter** | **Toegestane waarden** | **Upgrade beleid** | **Uitleg of korte beschrijving** |
| --- | --- | --- | --- |
|ClientAuthX509FindType |teken reeks, standaard is ' FindByThumbprint ' |Dynamisch|Hiermee wordt aangegeven hoe moet worden gezocht naar het certificaat in het archief dat is opgegeven door de ClientAuthX509StoreName ondersteunde waarde: FindByThumbprint; FindBySubjectName. |
|ClientAuthX509FindValue |teken reeks, standaard instelling is | Dynamisch|Zoek filter waarde die wordt gebruikt om het certificaat voor de standaard beheerdersrol FabricClient te vinden. |
|ClientAuthX509FindValueSecondary |teken reeks, standaard instelling is |Dynamisch|Zoek filter waarde die wordt gebruikt om het certificaat voor de standaard beheerdersrol FabricClient te vinden. |
|ClientAuthX509StoreName |teken reeks, standaard is ' My ' |Dynamisch|De naam van het X. 509-certificaat archief dat het certificaat voor de standaard beheerdersrol FabricClient bevat. |
|ClusterX509FindType |teken reeks, standaard is ' FindByThumbprint ' |Dynamisch|Hiermee wordt aangegeven hoe u zoekt naar een cluster certificaat in het archief dat is opgegeven door ClusterX509StoreName ondersteunde waarden: "FindByThumbprint"; ' FindBySubjectName ' met ' FindBySubjectName '; Wanneer er meerdere overeenkomsten zijn; het abonnement met de achterlopende verloop datum wordt gebruikt. |
|ClusterX509FindValue |teken reeks, standaard instelling is |Dynamisch|Zoek filter waarde die wordt gebruikt voor het zoeken van het cluster certificaat. |
|ClusterX509FindValueSecondary |teken reeks, standaard instelling is |Dynamisch|Zoek filter waarde die wordt gebruikt voor het zoeken van het cluster certificaat. |
|ClusterX509StoreName |teken reeks, standaard is ' My ' |Dynamisch|Naam van X. 509-certificaat archief dat cluster certificaat bevat voor het beveiligen van intra-cluster communicatie. |
|EndApplicationPortRange |Int, standaard is 0 |Statisch|End (niet inclusief) van de toepassings poorten die worden beheerd door het hosting subsysteem. Vereist als EndpointFilteringEnabled is ingesteld op True in hosting. |
|ServerAuthX509FindType |teken reeks, standaard is ' FindByThumbprint ' |Dynamisch|Hiermee wordt aangegeven hoe moet worden gezocht naar het server certificaat in het archief dat is opgegeven door de ServerAuthX509StoreName ondersteunde waarde: FindByThumbprint; FindBySubjectName. |
|ServerAuthX509FindValue |teken reeks, standaard instelling is |Dynamisch|Zoek filter waarde die wordt gebruikt om het server certificaat te vinden. |
|ServerAuthX509FindValueSecondary |teken reeks, standaard instelling is |Dynamisch|Zoek filter waarde die wordt gebruikt om het server certificaat te vinden. |
|ServerAuthX509StoreName |teken reeks, standaard is ' My ' |Dynamisch|De naam van het X. 509-certificaat archief met het server certificaat voor de entrée-service. |
|StartApplicationPortRange |Int, standaard is 0 |Statisch|Starten van de toepassings poorten die worden beheerd door het hosting subsysteem. Vereist als EndpointFilteringEnabled is ingesteld op True in hosting. |
|StateTraceInterval |Tijd in seconden, standaard waarde is 300 |Statisch|Geef een tijds duur in seconden op. Het interval voor het traceren van de knooppunt status op elk knoop punt en voor knoop punten op FM-FMM. |
|UserRoleClientX509FindType |teken reeks, standaard is ' FindByThumbprint ' |Dynamisch|Hiermee wordt aangegeven hoe moet worden gezocht naar het certificaat in het archief dat is opgegeven door de UserRoleClientX509StoreName ondersteunde waarde: FindByThumbprint; FindBySubjectName. |
|UserRoleClientX509FindValue |teken reeks, standaard instelling is |Dynamisch|Zoek filter waarde die wordt gebruikt om het certificaat voor de standaard gebruikersrol FabricClient te vinden. |
|UserRoleClientX509FindValueSecondary |teken reeks, standaard instelling is |Dynamisch|Zoek filter waarde die wordt gebruikt om het certificaat voor de standaard gebruikersrol FabricClient te vinden. |
|UserRoleClientX509StoreName |teken reeks, standaard is ' My ' |Dynamisch|De naam van het X. 509-certificaat archief dat het certificaat voor de standaard gebruikersrol FabricClient bevat. |

## <a name="failovermanager"></a>FailoverManager

| **Parameter** | **Toegestane waarden** | **Upgrade beleid** | **Uitleg of korte beschrijving** |
| --- | --- | --- | --- |
|AllowNodeStateRemovedForSeedNode|BOOL, default is FALSE |Dynamisch|Markering om aan te geven of de knooppunt status voor een Seed-knoop punt mag worden verwijderd |
|BuildReplicaTimeLimit|Time span, standaard waarde is gebruikelijk:: time span:: FromSeconds (3600)|Dynamisch|Geef een tijds duur in seconden op. De tijds limiet voor het bouwen van een stateful replica; waarna een waarschuwings status rapport wordt gestart |
|ClusterPauseThreshold|int, standaard waarde is 1|Dynamisch|Als het aantal knoop punten in het systeem onder deze waarde komt, gaat u naar plaatsing; taak verdeling; en de failover is gestopt. |
|CreateInstanceTimeLimit|Time span, standaard waarde is gebruikelijk:: time span:: FromSeconds (300)|Dynamisch|Geef een tijds duur in seconden op. De tijds limiet voor het maken van een stateless exemplaar; waarna een waarschuwings status rapport wordt gestart |
|ExpectedClusterSize|int, standaard waarde is 1|Dynamisch|Wanneer het cluster voor het eerst wordt opgestart, de FM wacht totdat dit aantal knoop punten zich bemeldt voordat er andere services worden geplaatst. inclusief de systeem services zoals naamgeving. Als u deze waarde verhoogt, neemt de tijd toe waarop een cluster wordt opgestart. maar voor komt dat de vroege knoop punten overbelast zijn en ook de extra zetten die nodig zijn naarmate er meer knoop punten online zijn. Deze waarde moet in het algemeen worden ingesteld op een kleine fractie van de eerste cluster grootte. |
|ExpectedNodeDeactivationDuration|Time span, standaard waarde is gebruikelijk:: time span:: FromSeconds (60,0 \* 30)|Dynamisch|Geef een tijds duur in seconden op. Dit is de verwachte duur voor een knoop punt om de deactivering in te volt ooien. |
|ExpectedNodeFabricUpgradeDuration|Time span, standaard waarde is gebruikelijk:: time span:: FromSeconds (60,0 \* 30)|Dynamisch|Geef een tijds duur in seconden op. Dit is de verwachte duur voor een upgrade van een knoop punt tijdens Windows Fabric upgrade. |
|ExpectedReplicaUpgradeDuration|Time span, standaard waarde is gebruikelijk:: time span:: FromSeconds (60,0 \* 30)|Dynamisch|Geef een tijds duur in seconden op. Dit is de verwachte duur voor alle replica's die moeten worden bijgewerkt op een knoop punt tijdens de upgrade van de toepassing. |
|IsSingletonReplicaMoveAllowedDuringUpgrade|BOOL, default is TRUE|Dynamisch|Indien ingesteld op True; replica's waarvan de grootte van de doel replica is ingesteld op 1, mogen tijdens de upgrade worden verplaatst. |
|MinReplicaSetSize|int, standaard is 3|Niet toegestaan|Dit is de minimale grootte van de replicaset voor de FM. Als het aantal actieve FM-replica's onder deze waarde daalt; de FM weigert wijzigingen aan het cluster totdat ten minste het minimum aantal replica's wordt hersteld |
|PlacementConstraints|teken reeks, standaard instelling is|Niet toegestaan|Eventuele plaatsings beperkingen voor de failover Manager-replica's |
|PlacementTimeLimit|Time span, standaard waarde is gebruikelijk:: time span:: FromSeconds (600)|Dynamisch|Geef een tijds duur in seconden op. De tijds limiet voor het bereiken van het aantal doel replica's; waarna een waarschuwings status rapport wordt gestart |
|QuorumLossWaitDuration |Tijd in seconden, de standaard waarde is MaxValue |Dynamisch|Geef een tijds duur in seconden op. Dit is de maximale duur waarvoor een partitie een status van quorum verlies mag hebben. Als de partitie na deze duur nog steeds in het quorum verloren is gegaan, de partitie wordt hersteld van quorum verlies door de replica's als verloren te beschouwen. Houd er rekening mee dat dit kan leiden tot gegevens verlies. |
|ReconfigurationTimeLimit|Time span, standaard waarde is gebruikelijk:: time span:: FromSeconds (300)|Dynamisch|Geef een tijds duur in seconden op. De tijds limiet voor opnieuw configureren; waarna een waarschuwings status rapport wordt gestart |
|ReplicaRestartWaitDuration|Time span, standaard waarde is gebruikelijk:: time span:: FromSeconds (60,0 \* 30)|Niet toegestaan|Geef een tijds duur in seconden op. Dit is de ReplicaRestartWaitDuration voor de FMService |
| SeedNodeQuorumAdditionalBufferNodes | int, standaard is 0 | Dynamisch | De buffer van Seed-knoop punten die nodig zijn om in te stellen (samen met het quorum van Seed-knoop punten) FM mag een maximum van (totalNumSeedNodes-(seedNodeQuorum + SeedNodeQuorumAdditionalBufferNodes)) Seed-knoop punten toestaan om uit te scha kelen. |
|StandByReplicaKeepDuration|Time span, standaard is gebruikelijk:: time span:: FromSeconds (3600.0 \* 24 \* 7)|Niet toegestaan|Geef een tijds duur in seconden op. Dit is de StandByReplicaKeepDuration voor de FMService |
|TargetReplicaSetSize|int, standaard is 7|Niet toegestaan|Dit is het doel aantal FM-replica's dat Windows Fabric blijft behouden. Een hogere waarde resulteert in een grotere betrouw baarheid van de FM-gegevens; met een kleine prestatie verhouding. |
|UserMaxStandByReplicaCount |Int, standaard waarde is 1 |Dynamisch|Het maximum aantal stand-by replica's dat door het systeem voor gebruikers Services wordt bewaard. |
|UserReplicaRestartWaitDuration |Tijd in seconden, de standaard waarde is 60,0 \* 30 |Dynamisch|Geef een tijds duur in seconden op. Wanneer een persistente replica uitvalt. Windows Fabric wacht totdat de replica een back-up maakt voordat nieuwe vervangende replica's (waarvoor een kopie van de status nodig is) worden gemaakt. |
|UserStandByReplicaKeepDuration |Tijd in seconden, de standaard waarde is 3600,0 \* 24 \* 7 |Dynamisch|Geef een tijds duur in seconden op. Wanneer een persistente replica van de status omlaag wordt weer gegeven; mogelijk is deze al vervangen. Deze timer bepaalt hoe lang de FM de stand-by replica houdt voordat deze wordt genegeerd. |

## <a name="faultanalysisservice"></a>FaultAnalysisService

| **Parameter** | **Toegestane waarden** | **Upgrade beleid** | **Uitleg of korte beschrijving** |
| --- | --- | --- | --- |
|CompletedActionKeepDurationInSeconds | Int, standaard waarde is 604800 |Statisch| Dit is ongeveer hoe lang acties in een Terminal status moeten worden bewaard. Dit is ook afhankelijk van StoredActionCleanupIntervalInSeconds; omdat het werk dat moet worden opgeschoond, alleen op dat interval kan worden uitgevoerd. 604800 is 7 dagen. |
|DataLossCheckPollIntervalInSeconds|int, standaard is 5|Statisch|Dit is de tijd tussen de controles die het systeem uitvoert tijdens het wachten op gegevens verlies. Het aantal keren dat het gegevens verlies nummer wordt gecontroleerd per interne iteratie is DataLossCheckWaitDurationInSeconds/this. |
|DataLossCheckWaitDurationInSeconds|int, standaard waarde is 25|Statisch|De totale tijd; in seconden; het systeem wacht tot er gegevens verloren zijn gegaan. Dit wordt intern gebruikt wanneer de StartPartitionDataLossAsync ()-API wordt aangeroepen. |
|MinReplicaSetSize |Int, standaard is 0 |Statisch|De MinReplicaSetSize voor FaultAnalysisService. |
|PlacementConstraints | teken reeks, standaard instelling is|Statisch| De PlacementConstraints voor FaultAnalysisService. |
|QuorumLossWaitDuration | Tijd in seconden, de standaard waarde is MaxValue |Statisch|Geef een tijds duur in seconden op. De QuorumLossWaitDuration voor FaultAnalysisService. |
|ReplicaDropWaitDurationInSeconds|int, standaard waarde is 600|Statisch|Deze para meter wordt gebruikt wanneer de data-verlies-API wordt aangeroepen. Hiermee wordt bepaald hoe lang het systeem wacht totdat een replica wordt verwijderd nadat replica is verwijderd. |
|ReplicaRestartWaitDuration |Tijd in seconden, standaard waarde is 60 minuten|Statisch|Geef een tijds duur in seconden op. De ReplicaRestartWaitDuration voor FaultAnalysisService. |
|StandByReplicaKeepDuration| Tijd in seconden, standaard is (60 *24* 7) minuten |Statisch|Geef een tijds duur in seconden op. De StandByReplicaKeepDuration voor FaultAnalysisService. |
|StoredActionCleanupIntervalInSeconds | Int, standaard waarde is 3600 |Statisch|Dit is hoe vaak de Store wordt opgeruimd. Alleen acties in een Terminal status; en die ten minste CompletedActionKeepDurationInSeconds geleden zijn voltooid, worden verwijderd. |
|StoredChaosEventCleanupIntervalInSeconds | Int, standaard waarde is 3600 |Statisch|Dit is hoe vaak de Store wordt gecontroleerd op opschoning. Als het aantal gebeurtenissen groter is dan 30000; het opschonen wordt gestart. |
|TargetReplicaSetSize |Int, standaard is 0 |Statisch|NOT_PLATFORM_UNIX_START de TargetReplicaSetSize voor FaultAnalysisService. |

## <a name="federation"></a>Federatie

| **Parameter** | **Toegestane waarden** | **Upgrade beleid** | **Uitleg of korte beschrijving** |
| --- | --- | --- | --- |
|LeaseDuration |Tijd in seconden, standaard waarde is 30 |Dynamisch|De duur dat een lease ten opzichte van een knoop punt en de bijbehorende neighbors. |
|LeaseDurationAcrossFaultDomain |Tijd in seconden, standaard waarde is 30 |Dynamisch|De duur dat een lease ten opzichte van een knoop punt en de bijbehorende neighbors in verschillende fout domeinen. |

## <a name="filestoreservice"></a>File Store service

| **Parameter** | **Toegestane waarden** | **Upgrade beleid** | **Uitleg of korte beschrijving** |
| --- | --- | --- | --- |
|AcceptChunkUpload|BOOL, default is TRUE|Dynamisch|Config om te bepalen of de File Store-service een bestands upload op basis van segmenten accepteert of niet tijdens het kopiëren van het toepassings pakket. |
|AnonymousAccessEnabled | BOOL, default is True |Statisch|Anonieme toegang tot de File Store service-shares in-of uitschakelen. |
|CommonName1Ntlmx509CommonName|teken reeks, standaard instelling is|Statisch| De algemene naam van het x509-certificaat dat wordt gebruikt voor het genereren van HMAC op de CommonName1NtlmPasswordSecret wanneer NTLM-verificatie wordt gebruikt |
|CommonName1Ntlmx509StoreLocation|teken reeks, standaard is ' LocalMachine '|Statisch|De archief locatie van het x509-certificaat dat wordt gebruikt voor het genereren van HMAC op de CommonName1NtlmPasswordSecret wanneer NTLM-verificatie wordt gebruikt |
|CommonName1Ntlmx509StoreName|teken reeks, standaard is ' MY '| Statisch|De archief naam van het x509-certificaat dat wordt gebruikt voor het genereren van HMAC op de CommonName1NtlmPasswordSecret wanneer NTLM-verificatie wordt gebruikt |
|CommonName2Ntlmx509CommonName|teken reeks, standaard instelling is|Statisch|De algemene naam van het x509-certificaat dat wordt gebruikt voor het genereren van HMAC op de CommonName2NtlmPasswordSecret wanneer NTLM-verificatie wordt gebruikt |
|CommonName2Ntlmx509StoreLocation|teken reeks, standaard is ' LocalMachine '| Statisch|De archief locatie van het x509-certificaat dat wordt gebruikt voor het genereren van HMAC op de CommonName2NtlmPasswordSecret wanneer NTLM-verificatie wordt gebruikt |
|CommonName2Ntlmx509StoreName|teken reeks, standaard is ' MY '|Statisch| De archief naam van het x509-certificaat dat wordt gebruikt voor het genereren van HMAC op de CommonName2NtlmPasswordSecret wanneer NTLM-verificatie wordt gebruikt |
|CommonNameNtlmPasswordSecret|SecureString, standaard waarde is gebruikelijk:: SecureString ("")| Statisch|Het wachtwoord geheim dat als seed is gebruikt voor het genereren van hetzelfde wacht woord wanneer NTLM-verificatie wordt gebruikt |
|DiskSpaceHealthReportingIntervalWhenCloseToOutOfDiskSpace |Time span, standaard waarde is gebruikelijk:: time span:: FromMinutes (5)|Dynamisch|Geef een tijds duur in seconden op. Het tijds interval tussen het controleren van de schijf ruimte voor rapportage van de status wanneer de schijf bijna geen ruimte meer heeft. |
|DiskSpaceHealthReportingIntervalWhenEnoughDiskSpace |Time span, standaard waarde is gebruikelijk:: time span:: FromMinutes (15)|Dynamisch|Geef een tijds duur in seconden op. Het tijds interval tussen het controleren van de schijf ruimte voor rapportage van status gebeurtenis wanneer er voldoende ruimte is op de schijf. |
|EnableImageStoreHealthReporting |BOOL, default is TRUE    |Statisch|Config om te bepalen of de status van de bestands archief-service moet worden gerapporteerd. |
|FreeDiskSpaceNotificationSizeInKB|int64, standaard waarde is 25 \* 1024 |Dynamisch|De grootte van de beschik bare schijf ruimte met de status waarschuwing kan optreden. De minimale waarde van deze configuratie-en FreeDiskSpaceNotificationThresholdPercentage-configuratie wordt gebruikt om de verzen ding van de status waarschuwing te bepalen. |
|FreeDiskSpaceNotificationThresholdPercentage|Double, standaard waarde is 0,02 |Dynamisch|Het percentage beschik bare schijf ruimte waaronder de status waarschuwing kan optreden. De minimale waarde van deze configuratie-en FreeDiskSpaceNotificationInMB-configuratie wordt gebruikt om de verzen ding van de status waarschuwing te bepalen. |
|GenerateV1CommonNameAccount| BOOL, default is TRUE|Statisch|Hiermee geeft u op of u een account met een algoritme voor het genereren van gebruikers naam v1 wilt genereren. Vanaf Service Fabric versie 6,1; Er wordt altijd een account met v2-generatie gemaakt. Het v1-account is nood zakelijk voor upgrades van/naar-versies die geen v2-generatie ondersteunen (vóór 6,1).|
|MaxCopyOperationThreads | Uint, standaard waarde is 0 |Dynamisch| Het maximum aantal parallelle bestanden dat secundair kan kopiëren van de primaire. ' 0 ' = = aantal kernen. |
|MaxFileOperationThreads | Uint, standaard waarde is 100 |Statisch| Het maximum aantal parallelle threads dat is toegestaan voor het uitvoeren van FileOperations (kopiëren/verplaatsen) in de primaire. ' 0 ' = = aantal kernen. |
|MaxRequestProcessingThreads | Uint, standaard waarde is 200 |Statisch|Het maximum aantal parallelle threads dat aanvragen in de primaire-thread mag verwerken. ' 0 ' = = aantal kernen. |
|MaxSecondaryFileCopyFailureThreshold | Uint, standaard waarde is 25|Dynamisch|Het maximum aantal nieuwe pogingen voor het kopiëren van bestanden op de secundaire voordat deze wordt opgeteld. |
|MaxStoreOperations | Uint, standaard waarde is 4096 |Statisch|Het maximum aantal parallelle archief transactie bewerkingen dat is toegestaan op de primaire. ' 0 ' = = aantal kernen. |
|NamingOperationTimeout |Tijd in seconden, standaard waarde is 60 |Dynamisch|Geef een tijds duur in seconden op. De time-out voor het uitvoeren van een naam bewerking. |
|PrimaryAccountNTLMPasswordSecret | SecureString, standaard waarde is leeg |Statisch| Het wachtwoord geheim dat als seed is gebruikt voor het genereren van hetzelfde wacht woord wanneer NTLM-verificatie wordt gebruikt. |
|PrimaryAccountNTLMX509StoreLocation | teken reeks, standaard is ' LocalMachine '|Statisch| De archief locatie van het x509-certificaat dat wordt gebruikt voor het genereren van HMAC op de PrimaryAccountNTLMPasswordSecret bij het gebruik van NTLM-verificatie. |
|PrimaryAccountNTLMX509StoreName | teken reeks, standaard is ' MY '|Statisch| De archief naam van het x509-certificaat dat wordt gebruikt voor het genereren van HMAC op de PrimaryAccountNTLMPasswordSecret bij het gebruik van NTLM-verificatie. |
|PrimaryAccountNTLMX509Thumbprint | teken reeks, standaard instelling is|Statisch|De vinger afdruk van het x509-certificaat dat wordt gebruikt voor het genereren van HMAC op de PrimaryAccountNTLMPasswordSecret bij het gebruik van NTLM-verificatie. |
|PrimaryAccountType | teken reeks, standaard instelling is |Statisch|Het primaire account type van de principal voor de toegangs beheer lijst van de File Store service-shares. |
|PrimaryAccountUserName | teken reeks, standaard instelling is |Statisch|De gebruikers naam van het primaire account van de principal voor de toegangs beheer lijst van de File Store service-shares. |
|PrimaryAccountUserPassword | SecureString, standaard waarde is leeg |Statisch|Het primaire account wachtwoord van de principal voor de toegangs beheer lijst van de File Store service-shares. |
|QueryOperationTimeout | Tijd in seconden, standaard waarde is 60 |Dynamisch|Geef een tijds duur in seconden op. De time-out voor het uitvoeren van de query bewerking. |
|SecondaryAccountNTLMPasswordSecret | SecureString, standaard waarde is leeg |Statisch| Het wachtwoord geheim dat als seed is gebruikt voor het genereren van hetzelfde wacht woord wanneer NTLM-verificatie wordt gebruikt. |
|SecondaryAccountNTLMX509StoreLocation | teken reeks, standaard is ' LocalMachine ' |Statisch|De archief locatie van het x509-certificaat dat wordt gebruikt voor het genereren van HMAC op de SecondaryAccountNTLMPasswordSecret bij het gebruik van NTLM-verificatie. |
|SecondaryAccountNTLMX509StoreName | teken reeks, standaard is ' MY ' |Statisch|De archief naam van het x509-certificaat dat wordt gebruikt voor het genereren van HMAC op de SecondaryAccountNTLMPasswordSecret bij het gebruik van NTLM-verificatie. |
|SecondaryAccountNTLMX509Thumbprint | teken reeks, standaard instelling is| Statisch|De vinger afdruk van het x509-certificaat dat wordt gebruikt voor het genereren van HMAC op de SecondaryAccountNTLMPasswordSecret bij het gebruik van NTLM-verificatie. |
|SecondaryAccountType | teken reeks, standaard instelling is|Statisch| Het secundaire account type van de principal voor de toegangs beheer lijst van de File Store service-shares. |
|SecondaryAccountUserName | teken reeks, standaard instelling is| Statisch|De gebruikers naam van het secundaire account van de principal voor de toegangs beheer lijst van de File Store service-shares. |
|SecondaryAccountUserPassword | SecureString, standaard waarde is leeg |Statisch|Het wacht woord van het secundaire account van de principal voor de toegangs beheer lijst van de File Store service-shares. |
|SecondaryFileCopyRetryDelayMilliseconds|uint, standaard waarde is 500|Dynamisch|De vertraging voor het kopiëren van bestanden (in milliseconden).|
|UseChunkContentInTransportMessage|BOOL, default is TRUE|Dynamisch|De vlag voor het gebruik van de nieuwe versie van het upload protocol dat is geïntroduceerd in v 6.4. Deze Protocol versie maakt gebruik van service Fabric-Trans Port voor het uploaden van bestanden naar een archief met installatie kopieën, die betere prestaties levert dan het SMB-protocol dat in vorige versies wordt gebruikt. |

## <a name="healthmanager"></a>HealthManager

| **Parameter** | **Toegestane waarden** | **Upgrade beleid** | **Uitleg of korte beschrijving** |
| --- | --- | --- | --- |
|EnableApplicationTypeHealthEvaluation |BOOL, default is False |Statisch|Cluster status evaluatie beleid: status evaluatie per toepassings type inschakelen. |
|MaxSuggestedNumberOfEntityHealthReports|Int, standaard waarde is 100 |Dynamisch|Het maximum aantal status rapporten dat een entiteit kan hebben voordat de problemen met de status rapportage logica van de watchdog worden verhoogd. Elke status entiteit moet een relatief klein aantal status rapporten hebben. Als het aantal rapporten boven dit nummer komt; Er zijn mogelijk problemen met de implementatie van de watchdog. Een entiteit met te veel rapporten wordt via een waarschuwings status rapport gemarkeerd wanneer de entiteit wordt geëvalueerd. |

## <a name="healthmanagerclusterhealthpolicy"></a>HealthManager/ClusterHealthPolicy

| **Parameter** | **Toegestane waarden** | **Upgrade beleid** | **Uitleg of korte beschrijving** |
| --- | --- | --- | --- |
|ConsiderWarningAsError |BOOL, default is False |Statisch|Cluster status evaluatie beleid: waarschuwingen worden beschouwd als fouten. |
|MaxPercentUnhealthyApplications | Int, standaard is 0 |Statisch|Cluster status evaluatie beleid: Maxi maal percentage beschadigde toepassingen dat is toegestaan voor het cluster in orde. |
|MaxPercentUnhealthyNodes | Int, standaard is 0 |Statisch|Cluster status evaluatie beleid: Maxi maal percentage beschadigde knoop punten dat is toegestaan voor het cluster. |

## <a name="healthmanagerclusterupgradehealthpolicy"></a>HealthManager/ClusterUpgradeHealthPolicy

| **Parameter** | **Toegestane waarden** | **Upgrade beleid** | **Uitleg of korte beschrijving** |
| --- | --- | --- | --- |
|MaxPercentDeltaUnhealthyNodes|int, standaard waarde is 10|Statisch|Status van het evaluatie beleid voor cluster upgrades: Maxi maal percentage Delta beschadigde knoop punten dat is toegestaan voor het cluster in orde |
|MaxPercentUpgradeDomainDeltaUnhealthyNodes|int, standaard waarde is 15|Statisch|Status van het evaluatie beleid voor cluster upgrades: maximum percentage Delta van beschadigde knoop punten in een upgrade domein dat is toegestaan voor het cluster is in orde |

## <a name="hosting"></a>Hosting

| **Parameter** | **Toegestane waarden** | **Upgrade beleid** | **Uitleg of korte beschrijving** |
| --- | --- | --- | --- |
|ActivationMaxFailureCount |Geheel getal, standaard waarde is 10 |Dynamisch|Aantal keer dat de activering van systeem pogingen is mislukt voordat |
|ActivationMaxRetryInterval |Tijd in seconden, standaard waarde is 300 |Dynamisch|Bij elke doorlopende activerings fout probeert het systeem de activering voor Maxi maal ActivationMaxFailureCount. ActivationMaxRetryInterval geeft het wacht tijd interval aan voordat een nieuwe poging wordt gedaan na elke activerings fout |
|ActivationRetryBackoffInterval |Tijd in seconden, standaard waarde is 5 |Dynamisch|Uitstel-interval bij elke activerings fout; Bij elke doorlopende activering wordt de activering door het systeem opnieuw geprobeerd tot de MaxActivationFailureCount. Het interval voor nieuwe pogingen bij elke poging is een product van een doorlopende activerings fout en het back-upinterval van de activering. |
|ActivationTimeout| Time span, standaard waarde is gebruikelijk:: time span:: FromSeconds (180)|Dynamisch| Geef een tijds duur in seconden op. De time-out voor het activeren van toepassingen; deactiveren en upgraden. |
|ApplicationHostCloseTimeout| Time span, standaard waarde is gebruikelijk:: time span:: FromSeconds (120)|Dynamisch| Geef een tijds duur in seconden op. Wanneer Fabric exit wordt gedetecteerd in een zelf geactiveerde processen; FabricRuntime sluit alle replica's in het hostproces proces van de gebruiker. Dit is de time-out voor het sluiten van de bewerking. |
| CnsNetworkPluginCnmUrlPort | wstring, standaard is L "48080" | Statisch | URL-poort van Azure cnm API |
| CnsNetworkPluginCnsUrlPort | wstring, standaard is L "10090" | Statisch | Azure CNS-URL-poort |
|ContainerServiceArguments|teken reeks, standaard is '-H localhost: 2375-H npipe://'|Statisch|Service Fabric (SF) beheert docker daemon (met uitzonde ring van Windows-client computers zoals Win10). Met deze configuratie kan de gebruiker aangepaste argumenten opgeven die moeten worden door gegeven aan docker-daemon wanneer deze wordt gestart. Als er aangepaste argumenten zijn opgegeven, wordt Service Fabric geen ander argument aan docker-Engine door gegeven, met uitzonde ring van het argument--pidfile. Daarom moeten gebruikers niet het argument--pidfile opgeven als onderdeel van hun klant argumenten. De aangepaste argumenten moeten er ook voor zorgen dat docker daemon luistert naar de standaard naam pipe op Windows (of UNIX-domein socket in Linux), zodat de Service Fabric ermee kan communiceren.|
|ContainerServiceLogFileMaxSizeInKb|int, standaard waarde is 32768|Statisch|De maximale bestands grootte van het logboek bestand dat wordt gegenereerd door docker-containers.  Alleen Windows.|
|ContainerImageDownloadTimeout|int, aantal seconden, standaard waarde is 1200 (20 minuten)|Dynamisch|Aantal seconden voordat de installatie kopie naar een time-out kan worden gedownload.|
|ContainerImagesToSkip|teken reeks, afbeeldings namen gescheiden door verticale streep, standaard is ""|Statisch|De naam van een of meer container installatie kopieën die niet moeten worden verwijderd.  Wordt gebruikt met de para meter PruneContainerImages.|
|ContainerServiceLogFileNamePrefix|teken reeks, standaard is ' sfcontainerlogs '|Statisch|Het voor voegsel van de bestands naam voor logboek bestanden die worden gegenereerd door docker-containers.  Alleen Windows.|
|ContainerServiceLogFileRetentionCount|int, standaard waarde is 10|Statisch|Het aantal logboek bestanden dat is gegenereerd door docker-containers voordat logboek bestanden worden overschreven.  Alleen Windows.|
|CreateFabricRuntimeTimeout|Time span, standaard waarde is gebruikelijk:: time span:: FromSeconds (120)|Dynamisch| Geef een tijds duur in seconden op. De time-outwaarde voor de FabricCreateRuntime-aanroep |
|DefaultContainerRepositoryAccountName|teken reeks, standaard instelling is|Statisch|Standaard referenties worden gebruikt in plaats van referenties die zijn opgegeven in ApplicationManifest.xml |
|DefaultContainerRepositoryPassword|teken reeks, standaard instelling is|Statisch|Standaard wachtwoord referenties worden gebruikt in plaats van referenties die zijn opgegeven in ApplicationManifest.xml|
|DefaultContainerRepositoryPasswordType|teken reeks, standaard instelling is|Statisch|Bij een niet-lege teken reeks kan de waarde "versleuteld" of "SecretsStoreRef" zijn.|
|DefaultDnsSearchSuffixEmpty|BOOL, default is FALSE|Statisch|Standaard wordt de service naam toegevoegd aan de SF DNS-naam voor container Services. Deze functie stopt dit gedrag zodat er standaard niets wordt toegevoegd aan de SF DNS-naam in de oplossings routes.|
|DeploymentMaxFailureCount|int, standaard waarde is 20| Dynamisch|De implementatie van de toepassing wordt opnieuw geprobeerd voor DeploymentMaxFailureCount tijden voordat de implementatie van die toepassing op het knoop punt is mislukt.| 
|DeploymentMaxRetryInterval| Time span, standaard waarde is gebruikelijk:: time span:: FromSeconds (3600)|Dynamisch| Geef een tijds duur in seconden op. Het interval voor het maximale aantal nieuwe pogingen voor de implementatie. Bij elke doorlopende storing wordt het interval voor nieuwe pogingen berekend als min (DeploymentMaxRetryInterval; Aantal doorlopende fouten * DeploymentRetryBackoffInterval) |
|DeploymentRetryBackoffInterval| Time span, standaard waarde is gebruikelijk:: time span:: FromSeconds (10)|Dynamisch|Geef een tijds duur in seconden op. Back-outinterval voor de implementatie fout. Bij elke continue implementatie fout zal het systeem de implementatie opnieuw proberen voor de MaxDeploymentFailureCount. Het interval voor nieuwe pogingen is een product van een continue implementatie fout en het uitstel-interval van de implementatie. |
|DisableContainers|BOOL, default is FALSE|Statisch|Configuratie voor het uitschakelen van containers: wordt gebruikt in plaats van DisableContainerServiceStartOnContainerActivatorOpen. deze configuratie is afgeschaft |
|DisableDockerRequestRetry|BOOL, default is FALSE |Dynamisch| SF communiceert standaard met DD (docker dameon) met een time-out van ' DockerRequestTimeout ' voor elke HTTP-aanvraag die ernaar wordt verzonden. Als DD niet binnen deze tijds periode reageert; EB verzendt de aanvraag opnieuw als de bewerking op het hoogste niveau nog steeds resterende tijd heeft.  Met Hyper-v-container; DD neemt soms veel tijd in beslag om de container te openen of te deactiveren. In dergelijke gevallen is de aanvraag een time-out van SF-perspectief en voert SF de bewerking opnieuw uit. Soms lijkt het alsof er meer belasting wordt toegevoegd aan DD. Met deze configuratie kunt u deze nieuwe poging uitschakelen en wachten op DD om te reageren. |
|DnsServerListTwoIps | BOOL, default is FALSE | Statisch | Met deze vlaggen wordt de lokale DNS-server twee keer toegevoegd om tijdelijke problemen op te lossen. |
| DoNotInjectLocalDnsServer | BOOL, default is FALSE | Statisch | Hiermee wordt voor komen dat de runtime het lokale IP-adres als DNS-server voor containers injecteert. |
|EnableActivateNoWindow| BOOL, default is FALSE|Dynamisch| Het geactiveerde proces wordt zonder console op de achtergrond gemaakt. |
|EnableContainerServiceDebugMode|BOOL, default is TRUE|Statisch|Logboek registratie voor docker-containers in-of uitschakelen.  Alleen Windows.|
|EnableDockerHealthCheckIntegration|BOOL, default is TRUE|Statisch|Maakt integratie van docker status controle-gebeurtenissen mogelijk met Service Fabric systeem status rapport |
|EnableProcessDebugging|BOOL, default is FALSE|Dynamisch| Hiermee schakelt u het starten van Application hosts onder fout opsporing in |
|EndpointProviderEnabled| BOOL, default is FALSE|Statisch| Hiermee kunt u eindpunt resources beheren op basis van de infra structuur. Vereist specificatie van het poort bereik voor het begin-en eind toepassings in FabricNode. |
|FabricContainerAppsEnabled| BOOL, default is FALSE|Statisch| |
|FirewallPolicyEnabled|BOOL, default is FALSE|Statisch| Hiermee schakelt u het openen van Firewall poorten voor eindpunt resources met expliciete poorten die zijn opgegeven in ServiceManifest |
|GetCodePackageActivationContextTimeout|Time span, standaard waarde is gebruikelijk:: time span:: FromSeconds (120)|Dynamisch|Geef een tijds duur in seconden op. De time-outwaarde voor de CodePackageActivationContext-aanroepen. Dit is niet van toepassing op ad-hoc Services. |
|GovernOnlyMainMemoryForProcesses|BOOL, default is FALSE|Statisch|Het standaard gedrag van resource governance is om een limiet in te stellen die is opgegeven in MemoryInMB op de hoeveelheid geheugen (RAM + swap) die door het proces wordt gebruikt. Als de limiet wordt overschreden; Er wordt een OutOfMemory-uitzonde ring ontvangen tijdens het proces. Als deze para meter is ingesteld op True; de limiet wordt alleen toegepast op de hoeveelheid RAM-geheugen die door een proces wordt gebruikt. Als deze limiet wordt overschreden; en als deze instelling waar is; vervolgens wordt het hoofd geheugen door het besturings systeem naar de schijf gewisseld. |
|IPProviderEnabled|BOOL, default is FALSE|Statisch|Hiermee kunt u IP-adressen beheren. |
|IsDefaultContainerRepositoryPasswordEncrypted|BOOL, default is FALSE|Statisch|Hiermee wordt aangegeven of de DefaultContainerRepositoryPassword is versleuteld of niet.|
|LinuxExternalExecutablePath|teken reeks, standaard is '/usr/bin/' |Statisch|De primaire map van externe uitvoer bare opdrachten op het knoop punt.|
|NTLMAuthenticationEnabled|BOOL, default is FALSE|Statisch| Biedt ondersteuning voor het gebruik van NTLM door de code pakketten die worden uitgevoerd als andere gebruikers, zodat de processen op verschillende computers veilig kunnen communiceren. |
|NTLMAuthenticationPasswordSecret|SecureString, standaard waarde is gebruikelijk:: SecureString ("")|Statisch|Is versleuteld, dat wordt gebruikt voor het genereren van het wacht woord voor NTLM-gebruikers. Moet worden ingesteld als NTLMAuthenticationEnabled True is. Gevalideerd door de deployer. |
|NTLMSecurityUsersByX509CommonNamesRefreshInterval|Time span, standaard waarde is gebruikelijk:: time span:: FromMinutes (3)|Dynamisch|Geef een tijds duur in seconden op. Omgevings instellingen het periodieke interval waarbij wordt gescand op nieuwe certificaten die moeten worden gebruikt voor File Store service NTLM-configuratie. |
|NTLMSecurityUsersByX509CommonNamesRefreshTimeout|Time span, standaard waarde is gebruikelijk:: time span:: FromMinutes (4)|Dynamisch| Geef een tijds duur in seconden op. De time-out voor het configureren van NTLM-gebruikers met behulp van algemene namen voor certificaten. De NTLM-gebruikers zijn nodig voor File Store service-shares. |
|PruneContainerImages|BOOL, default is FALSE|Dynamisch| Verwijder ongebruikte toepassings container installatie kopieën van knoop punten. Wanneer de registratie van een Application type bij het Service Fabric-cluster ongedaan is gemaakt, worden de container installatie kopieën die door deze toepassing zijn gebruikt, verwijderd op knoop punten waar deze door Service Fabric is gedownload. Het weghalen wordt elk uur uitgevoerd. het kan Maxi maal één uur duren voordat installatie kopieën uit het cluster worden verwijderd.<br>Service Fabric worden nooit installatie kopieën gedownload of verwijderd die niet aan een toepassing zijn gekoppeld.  Niet-gerelateerde installatie kopieën die hand matig of anderszins zijn gedownload, moeten expliciet worden verwijderd.<br>Installatie kopieën die niet moeten worden verwijderd, kunnen worden opgegeven in de para meter ContainerImagesToSkip.| 
|RegisterCodePackageHostTimeout|Time span, standaard waarde is gebruikelijk:: time span:: FromSeconds (120)|Dynamisch| Geef een tijds duur in seconden op. De time-outwaarde voor de FabricRegisterCodePackageHost-synchronisatie aanroep. Dit is alleen van toepassing voor hosts met meerdere code pakketten zoals FWP |
|RequestTimeout|Time span, standaard waarde is gebruikelijk:: time span:: FromSeconds (30)|Dynamisch| Geef een tijds duur in seconden op. Dit vertegenwoordigt de time-out voor de communicatie tussen de toepassingshost van de gebruiker en het infrastructuur proces voor verschillende hosting bewerkingen, zoals de fabrieks registratie; runtime-registratie. |
|RunAsPolicyEnabled| BOOL, default is FALSE|Statisch| Hiermee kunnen code pakketten worden uitgevoerd als andere lokale gebruiker dan de gebruiker waaronder het Fabric-proces wordt uitgevoerd. Als u deze beleids infrastructuur wilt inschakelen, moet deze worden uitgevoerd als systeem of als gebruiker met SeAssignPrimaryTokenPrivilege. |
|ServiceFactoryRegistrationTimeout| Time span, standaard waarde is gebruikelijk:: time span:: FromSeconds (120)|Dynamisch|Geef een tijds duur in seconden op. De time-outwaarde voor de aanroep van het synchronisatie register (stateless/stateful) ServiceFactory |
|ServiceTypeDisableFailureThreshold |Geheel getal, standaard waarde is 1 |Dynamisch|Dit is de drempel waarde voor het aantal mislukte pogingen, waarna FailoverManager (FM) wordt gewaarschuwd om het Service type op dat knoop punt uit te scha kelen en een ander knoop punt te proberen voor plaatsing. |
|ServiceTypeDisableGraceInterval|Time span, standaard waarde is gebruikelijk:: time span:: FromSeconds (30)|Dynamisch|Geef een tijds duur in seconden op. Tijds interval waarna het Service type kan worden uitgeschakeld |
|ServiceTypeRegistrationTimeout |Tijd in seconden, standaard waarde is 300 |Dynamisch|Maximale toegestane tijd voor de registratie van de service type bij de infra structuur |
|UseContainerServiceArguments|BOOL, default is TRUE|Statisch|Deze configuratie vertelt de host om het door geven van para meters (opgegeven in config ContainerServiceArguments) over te slaan naar docker-daemon.|

## <a name="httpgateway"></a>HttpGateway

| **Parameter** | **Toegestane waarden** | **Upgrade beleid** | **Uitleg of korte beschrijving** |
| --- | --- | --- | --- |
|ActiveListeners |Uint, standaard waarde is 50 |Statisch| Aantal lees bewerkingen dat naar de http-server wachtrij moet worden gepost. Hiermee bepaalt u het aantal gelijktijdige aanvragen waaraan de HttpGateway kan voldoen. |
|HttpGatewayHealthReportSendInterval |Tijd in seconden, standaard waarde is 30 |Statisch|Geef een tijds duur in seconden op. Het interval waarmee de http-gateway verzamelde status rapporten naar de Health Manager verzendt. |
|HttpStrictTransportSecurityHeader|teken reeks, standaard instelling is|Dynamisch| Geef de waarde op voor de HTTP-header voor het Trans Port van beveiliging die moet worden opgenomen in elk antwoord dat door de HttpGateway wordt verzonden. Wanneer deze is ingesteld op een lege teken reeks; Deze header wordt niet opgenomen in het antwoord van de gateway.|
|IsEnabled|BOOL, default is False |Statisch| Hiermee wordt de HttpGateway in-of uitgeschakeld. HttpGateway is standaard uitgeschakeld. |
|MaxEntityBodySize |Uint, standaard waarde is 4194304 |Dynamisch|Geeft de maximale grootte van de hoofd tekst die kan worden verwacht van een HTTP-aanvraag. De standaard waarde is 4 MB. Httpgateway voert een aanvraag uit als deze een hoofd tekst heeft > deze waarde. De minimale grootte van het Lees segment is 4096 bytes. Dit moet daarom >= 4096 zijn. |

## <a name="imagestoreservice"></a>ImageStoreService

| **Parameter** | **Toegestane waarden** | **Upgrade beleid** | **Uitleg of korte beschrijving** |
| --- | --- | --- | --- |
|Ingeschakeld |BOOL, default is False |Statisch|De vlag enabled voor ImageStoreService. Standaard: onwaar |
|MinReplicaSetSize | Int, standaard is 3 |Statisch|De MinReplicaSetSize voor ImageStoreService. |
|PlacementConstraints | teken reeks, standaard instelling is |Statisch| De PlacementConstraints voor ImageStoreService. |
|QuorumLossWaitDuration | Tijd in seconden, de standaard waarde is MaxValue |Statisch| Geef een tijds duur in seconden op. De QuorumLossWaitDuration voor ImageStoreService. |
|ReplicaRestartWaitDuration | Tijd in seconden, de standaard waarde is 60,0 \* 30 |Statisch|Geef een tijds duur in seconden op. De ReplicaRestartWaitDuration voor ImageStoreService. |
|StandByReplicaKeepDuration | Tijd in seconden, de standaard waarde is 3600,0 \* 2 |Statisch| Geef een tijds duur in seconden op. De StandByReplicaKeepDuration voor ImageStoreService. |
|TargetReplicaSetSize | Int, standaard is 7 |Statisch|De TargetReplicaSetSize voor ImageStoreService. |

## <a name="ktllogger"></a>KtlLogger

| **Parameter** | **Toegestane waarden** | **Upgrade beleid** | **Uitleg of korte beschrijving** |
| --- | --- | --- | --- |
|AutomaticMemoryConfiguration |Int, standaard waarde is 1 |Dynamisch|Vlag waarmee wordt aangegeven of de geheugen instellingen automatisch en dynamisch moeten worden geconfigureerd. Als dit niet het geval is, worden de configuratie-instellingen van het geheugen rechtstreeks gebruikt en worden ze niet gewijzigd op basis van de systeem voorwaarden. Als dit het geval is, worden de geheugen instellingen automatisch geconfigureerd en kunnen ze worden gewijzigd op basis van de systeem voorwaarden. |
|MaximumDestagingWriteOutstandingInKB | Int, standaard is 0 |Dynamisch|Het aantal KB waarmee het gedeelde logboek kan worden voorgezet vóór het toegewezen logboek. Gebruik 0 om aan te geven dat er geen limiet is.
|SharedLogId |teken reeks, standaard instelling is |Statisch|Unieke GUID voor gedeelde logboek container. Gebruik als standaardpad gebruiken onder Fabric data root. |
|SharedLogPath |teken reeks, standaard instelling is |Statisch|Pad-en bestands naam naar locatie voor het plaatsen van een gedeelde logboek container. Gebruik "" voor het gebruik van het standaardpad onder Fabric data root. |
|SharedLogSizeInMB |Int, standaard waarde is 8192 |Statisch|Het aantal MB dat moet worden toegewezen in de gedeelde logboek container. |
|SharedLogThrottleLimitInPercentUsed|int, standaard is 0 | Statisch | Het percentage van het gebruik van het gedeelde logboek dat beperking veroorzaakt. De waarde moet tussen 0 en 100. De waarde 0 betekent dat de standaard percentage waarde wordt gebruikt. De waarde 100 houdt in dat er geen beperking is. Een waarde tussen 1 en 99 geeft het percentage logboek gebruik aan waarvan de beperking wordt toegepast. Als het gedeelde logboek bijvoorbeeld 10 GB is en de waarde 90 is, treedt er een beperking op wanneer 9 GB zijn wordt gebruikt. U wordt aangeraden de standaard waarde te gebruiken.|
|WriteBufferMemoryPoolMaximumInKB | Int, standaard is 0 |Dynamisch|Het aantal KB waarmee de geheugen groep voor schrijf buffers mag groeien. Gebruik 0 om aan te geven dat er geen limiet is. |
|WriteBufferMemoryPoolMinimumInKB |Int, standaard waarde is 8388608 |Dynamisch|Het aantal KB dat in eerste instantie moet worden toegewezen voor de geheugen groep schrijf buffer. Gebruik 0 om aan te geven dat er geen limiet standaard moet overeenkomen met SharedLogSizeInMB hieronder. |

## <a name="managedidentitytokenservice"></a>ManagedIdentityTokenService
| **Parameter** | **Toegestane waarden** | **Upgrade beleid** | **Uitleg of korte beschrijving** |
| --- | --- | --- | --- |
|IsEnabled|BOOL, default is FALSE|Statisch|Markering voor het beheren van de aanwezigheid en status van de beheerde identiteits token service in het cluster. Dit is een vereiste voor het gebruik van de beheerde identiteits functionaliteit van Service Fabric toepassingen.|

## <a name="management"></a>Beheer

| **Parameter** | **Toegestane waarden** | **Upgrade beleid** | **Uitleg of korte beschrijving** |
| --- | --- | --- | --- |
|AutomaticUnprovisionInterval|Time span, standaard waarde is gebruikelijk:: time span:: FromMinutes (5)|Dynamisch|Geef een tijds duur in seconden op. Het opschoon interval voor het ongedaan maken van de registratie van het toepassings type tijdens het automatisch opschonen van het toepassings type.|
|AzureStorageMaxConnections | Int, standaard waarde is 5000 |Dynamisch|Het maximum aantal gelijktijdige verbindingen met Azure Storage. |
|AzureStorageMaxWorkerThreads | Int, standaard waarde is 25 |Dynamisch|Het maximum aantal worker-threads parallel. |
|AzureStorageOperationTimeout | Tijd in seconden, standaard waarde is 6000 |Dynamisch|Geef een tijds duur in seconden op. Time-out voor het volt ooien van de xstore-bewerking. |
|CleanupApplicationPackageOnProvisionSuccess|BOOL, default is True |Dynamisch|Hiermee wordt het automatisch opruimen van het toepassings pakket bij een geslaagde inrichting in-of uitgeschakeld.
|CleanupUnusedApplicationTypes|BOOL, default is FALSE |Dynamisch|Met deze configuratie als deze functie is ingeschakeld, kan de registratie van ongebruikte toepassings type versies automatisch ongedaan worden gemaakt om de meest recente drie ongebruikte versies over te slaan, waardoor de schijf ruimte die wordt ingen Omen door Image Store De automatische opschoning wordt geactiveerd aan het einde van de inrichting van het specifieke app-type en wordt ook periodiek eenmaal per dag uitgevoerd voor alle toepassings typen. Het aantal ongebruikte versies dat moet worden overgeslagen, kan worden geconfigureerd met behulp van de para meter MaxUnusedAppTypeVersionsToKeep. <br/> *De aanbevolen procedure is om te gebruiken `true` .*
|DisableChecksumValidation | BOOL, default is False |Statisch| Met deze configuratie kan de controlesom validatie tijdens het inrichten van de toepassing worden in-of uitgeschakeld. |
|DisableServerSideCopy | BOOL, default is False |Statisch|Met deze configuratie wordt het exemplaar van het toepassings pakket op de installatie kopie opslag tijdens het inrichten van de toepassing ingeschakeld of uitgeschakeld. |
|ImageCachingEnabled | BOOL, default is True |Statisch|Met deze configuratie kunnen we caching in-of uitschakelen. |
|ImageStoreConnectionString |SecureString |Statisch|Verbindings reeks naar de hoofdmap voor installatie kopie opslag. |
|ImageStoreMinimumTransferBPS | Int, standaard waarde is 1024 |Dynamisch|De minimale overdrachts frequentie tussen het cluster en de installatie kopie opslag. Deze waarde wordt gebruikt om de time-out te bepalen bij het openen van de externe installatie kopie opslag. Wijzig deze waarde alleen als de latentie tussen het cluster en installatie kopie opslag hoog is om meer tijd te bieden voor het downloaden van het cluster vanaf de externe installatie kopie opslag. |
|MaxUnusedAppTypeVersionsToKeep | Int, standaard is 3 |Dynamisch|Deze configuratie definieert het aantal ongebruikte versie van toepassings type dat moet worden overgeslagen voor opschonen. Deze para meter is alleen van toepassing als de para meter CleanupUnusedApplicationTypes is ingeschakeld. <br/>*Algemene best practice is de standaard waarde () te gebruiken `3` . Waarden die kleiner zijn dan 1 zijn niet geldig.*|


## <a name="metricactivitythresholds"></a>MetricActivityThresholds
| **Parameter** | **Toegestane waarden** |**Upgrade beleid**| **Uitleg of korte beschrijving** |
| --- | --- | --- | --- |
|PropertyGroup|KeyIntegerValueMap, standaard instelling is geen|Dynamisch|Bepaalt de set MetricActivityThresholds voor de metrische gegevens in het cluster. De taak verdeling werkt alleen als maxNodeLoad groter is dan MetricActivityThresholds. Voor de metrische gegevens over defragmentatie wordt de hoeveelheid belasting gedefinieerd die gelijk is aan of onder welke Service Fabric het knoop punt is leeg. |

## <a name="metricbalancingthresholds"></a>MetricBalancingThresholds
| **Parameter** | **Toegestane waarden** |**Upgrade beleid**| **Uitleg of korte beschrijving** |
| --- | --- | --- | --- |
|PropertyGroup|KeyDoubleValueMap, standaard instelling is geen|Dynamisch|Bepaalt de set MetricBalancingThresholds voor de metrische gegevens in het cluster. De taak verdeling werkt alleen als maxNodeLoad/minNodeLoad groter is dan MetricBalancingThresholds. Defragmentatie werkt als maxNodeLoad/minNodeLoad in ten minste één FD of UD kleiner is dan MetricBalancingThresholds. |

## <a name="metricloadstickinessforswap"></a>MetricLoadStickinessForSwap
| **Parameter** | **Toegestane waarden** |**Upgrade beleid**| **Uitleg of korte beschrijving** |
| --- | --- | --- | --- |
|PropertyGroup|KeyDoubleValueMap, standaard instelling is geen|Dynamisch|Hiermee bepaalt u het deel van de belasting dat bij de replica hoort wanneer deze wordt omgewisseld, een waarde tussen 0 (laden niet met replica wordt uitgevoerd) en 1 (Load sticks met replica-default) |

## <a name="namingservice"></a>NamingService

| **Parameter** | **Toegestane waarden** | **Upgrade beleid** | **Uitleg of korte beschrijving** |
| --- | --- | --- | --- |
|GatewayServiceDescriptionCacheLimit |Int, standaard is 0 |Statisch|Het maximum aantal vermeldingen in de cache van de LRU-service beschrijving op de naamgevings gateway (ingesteld op 0 voor geen limiet). |
|MaxClientConnections |Int, standaard waarde is 1000 |Dynamisch|Het Maxi maal toegestane aantal client verbindingen per gateway. |
|MaxFileOperationTimeout |Tijd in seconden, standaard waarde is 30 |Dynamisch|Geef een tijds duur in seconden op. De maximale time-out die is toegestaan voor de File Store-service bewerking. Aanvragen die een grotere time-out opgeven, worden geweigerd. |
|MaxIndexedEmptyPartitions |Int, standaard waarde is 1000 |Dynamisch|Het maximum aantal lege partities dat in de cache met meldingen moet worden geïndexeerd voor het synchroniseren van de verbinding met clients. Eventuele lege partities boven dit nummer worden verwijderd uit de index in oplopende volg orde van de opzoek versie. Opnieuw verbinding maken met clients kan nog steeds synchroniseren en gemiste lege partitie-updates ontvangen; maar het synchronisatie protocol wordt duurder. |
|MaxMessageSize |Int, standaard is 4 \* 1024 \* 1024 |Statisch|De maximale bericht grootte voor client knooppunt communicatie bij het gebruik van een naam. DOS-aanvals heffing; de standaard waarde is 4 MB. |
|MaxNamingServiceHealthReports | Int, standaard waarde is 10 |Dynamisch|Het maximum aantal trage bewerkingen waarbij de naamgeving van Store-service rapporten niet in orde is. Als 0; alle trage bewerkingen worden verzonden. |
|MaxOperationTimeout |Tijd in seconden, standaard waarde is 600 |Dynamisch|Geef een tijds duur in seconden op. De maximale time-out die is toegestaan voor client bewerkingen. Aanvragen die een grotere time-out opgeven, worden geweigerd. |
|MaxOutstandingNotificationsPerClient |Int, standaard waarde is 1000 |Dynamisch|Het maximum aantal openstaande meldingen voordat een client registratie geforceerd wordt gesloten door de gateway. |
|MinReplicaSetSize | Int, standaard is 3 |Niet toegestaan| Het minimale aantal Naming Service replica's dat vereist is om te schrijven om een update te volt ooien. Als er minder replica's zijn dan deze die actief zijn in het systeem, weigert het betrouwbaarheids systeem updates voor het Naming Service archief totdat replica's worden hersteld. Deze waarde mag nooit meer zijn dan de TargetReplicaSetSize. |
|PartitionCount |Int, standaard is 3 |Niet toegestaan|Het aantal partities van het Naming Service archief dat moet worden gemaakt. Elke partitie is eigenaar van één partitie sleutel die overeenkomt met de bijbehorende index. partitie sleutels [0; PartitionCount] bestaat. Het verhogen van het aantal Naming Service partities verhoogt de schaal waarop de Naming Service kan worden uitgevoerd door de gemiddelde hoeveelheid gegevens die wordt bewaard door een back-upreplicaset te verminderen. Er zijn kosten verbonden aan het gebruik van resources (aangezien PartitionCount * ReplicaSetSize-service replica's moeten worden behouden).|
|PlacementConstraints | teken reeks, standaard instelling is |Niet toegestaan| De plaatsings beperking voor de Naming Service. |
|QuorumLossWaitDuration | Tijd in seconden, de standaard waarde is MaxValue |Niet toegestaan| Geef een tijds duur in seconden op. Wanneer een Naming Service wordt omgezet in quorum verlies; Deze timer wordt gestart. Wanneer de FM-periode verlopen, worden de replica's als verloren beschouwd. en poging om het quorum te herstellen. Dat kan leiden tot verlies van gegevens. |
|RepairInterval | Tijd in seconden, standaard waarde is 5 |Statisch| Geef een tijds duur in seconden op. Het interval waarin inconsistenties van de naamgeving worden hersteld tussen de eigenaar van de instantie en de naam eigenaar wordt gestart. |
|ReplicaRestartWaitDuration | Tijd in seconden, standaard is (60,0 * 30)|Niet toegestaan| Geef een tijds duur in seconden op. Wanneer een Naming Service replica omlaag gaat; Deze timer wordt gestart. Wanneer de FM-periode verloopt, worden de replica's vervangen die niet beschikbaar zijn (deze worden nog niet beschouwd als verloren). |
|ServiceDescriptionCacheLimit | Int, standaard is 0 |Statisch| Het maximum aantal vermeldingen dat in de LRU-service beschrijvings cache van de naamgevings Store-service wordt bijgehouden (ingesteld op 0 voor geen limiet). |
|ServiceNotificationTimeout |Tijd in seconden, standaard waarde is 30 |Dynamisch|Geef een tijds duur in seconden op. De time-out die wordt gebruikt bij het leveren van service meldingen aan de client. |
|StandByReplicaKeepDuration | Tijd in seconden, de standaard waarde is 3600,0 * 2 |Niet toegestaan| Geef een tijds duur in seconden op. Wanneer een Naming Service replica van een status omlaag terugkomt; mogelijk is deze al vervangen. Deze timer bepaalt hoe lang de FM de stand-by replica houdt voordat deze wordt genegeerd. |
|TargetReplicaSetSize |Int, standaard is 7 |Niet toegestaan|Het aantal replica sets voor elke partitie van het Naming Service archief. Het verhogen van het aantal replica sets verhoogt het betrouwbaarheids niveau voor de informatie in het Naming Service archief; het verminderen van de wijziging waarvan de informatie verloren gaat als gevolg van knooppunt storingen; bij een verhoogde belasting van Windows Fabric en de hoeveelheid tijd die nodig is voor het uitvoeren van updates voor de naam gegevens.|

## <a name="nodebufferpercentage"></a>NodeBufferPercentage
| **Parameter** | **Toegestane waarden** |**Upgrade beleid**| **Uitleg of korte beschrijving** |
| --- | --- | --- | --- |
|PropertyGroup|KeyDoubleValueMap, standaard instelling is geen|Dynamisch|Percentage van capaciteit van knoop punt per metrische naam; wordt gebruikt als een buffer om een vrije plaats op een knoop punt voor de failover-case te hand haven. |

## <a name="nodecapacities"></a>NodeCapacities

| **Parameter** | **Toegestane waarden** | **Upgrade beleid** | **Uitleg of korte beschrijving** |
| --- | --- | --- | --- |
|PropertyGroup |NodeCapacityCollectionMap |Statisch|Een verzameling knooppunt capaciteit voor verschillende metrische gegevens. |

## <a name="nodedomainids"></a>NodeDomainIds

| **Parameter** | **Toegestane waarden** | **Upgrade beleid** | **Uitleg of korte beschrijving** |
| --- | --- | --- | --- |
|PropertyGroup |NodeFaultDomainIdCollection |Statisch|Hierin worden de fout domeinen beschreven waarvan een knoop punt deel uitmaakt. Het fout domein wordt gedefinieerd via een URI die de locatie van het knoop punt in het Data Center beschrijft.  Fout domein-Uri's hebben de indeling FD:/FD/gevolgd door een segment van een URI-pad.|
|UpgradeDomainId |teken reeks, standaard instelling is |Statisch|Beschrijft het upgrade domein waarvan een knoop punt deel uitmaakt. |

## <a name="nodeproperties"></a>NodeProperties

| **Parameter** | **Toegestane waarden** | **Upgrade beleid** | **Uitleg of korte beschrijving** |
| --- | --- | --- | --- |
|PropertyGroup |NodePropertyCollectionMap |Statisch|Een verzameling teken reeks sleutel-waardeparen voor eigenschappen van knoop punten. |

## <a name="paas"></a>PaaS

| **Parameter** | **Toegestane waarden** | **Upgrade beleid** | **Uitleg of korte beschrijving** |
| --- | --- | --- | --- |
|ClusterId |teken reeks, standaard instelling is |Niet toegestaan|X509-certificaat archief dat wordt gebruikt door de infra structuur voor configuratie beveiliging. |

## <a name="performancecounterlocalstore"></a>PerformanceCounterLocalStore

| **Parameter** | **Toegestane waarden** | **Upgrade beleid** | **Uitleg of korte beschrijving** |
| --- | --- | --- | --- |
|Tellers |Tekenreeks | Dynamisch |Een door komma's gescheiden lijst met prestatie meter items die moeten worden verzameld. |
|IsEnabled |BOOL, default is True | Dynamisch |Vlag geeft aan of de prestatie meter verzameling op het lokale knoop punt is ingeschakeld. |
|MaxCounterBinaryFileSizeInMB |Int, standaard waarde is 1 | Dynamisch |Maximale grootte (in MB) voor elk binair bestand voor prestatie meter items. |
|NewCounterBinaryFileCreationIntervalInMinutes |Int, standaard waarde is 10 | Dynamisch |Het maximum interval (in seconden) waarna een nieuw binair bestand voor het prestatie meter item wordt gemaakt. |
|SamplingIntervalInSeconds |Int, standaard waarde is 60 | Dynamisch |Steekproef interval voor prestatie meter items die worden verzameld. |

## <a name="placementandloadbalancing"></a>PlacementAndLoadBalancing

| **Parameter** | **Toegestane waarden** | **Upgrade beleid** | **Uitleg of korte beschrijving** |
| --- | --- | --- | --- |
|AffinityConstraintPriority | Int, standaard is 0 | Dynamisch|Bepaalt de prioriteit van de affiniteits beperking: 0: hard; 1: zacht; negatief: negeren. |
|ApplicationCapacityConstraintPriority | Int, standaard is 0 | Dynamisch|Bepaalt de prioriteit van de capaciteits beperking: 0: hard; 1: zacht; negatief: negeren. |
|AutoDetectAvailableResources|BOOL, default is TRUE|Statisch|Met deze configuratie wordt de automatische detectie geactiveerd van beschik bare bronnen op het knoop punt (CPU en geheugen) wanneer deze configuratie is ingesteld op True, worden de werkelijke capaciteit gelezen en gecorrigeerd als de gebruiker onjuiste knooppunt capaciteit heeft opgegeven of deze niet heeft gedefinieerd als deze configuratie is ingesteld op false. er wordt een waarschuwing weer gegeven dat de gebruiker onjuiste knooppunt capaciteit heeft opgegeven. maar we zullen deze niet corrigeren. Dit betekent dat de gebruiker de capaciteit wil opgeven die is opgegeven als > dan het knoop punt echt heeft of als de capaciteit niet is gedefinieerd. Er wordt een onbeperkte capaciteit aangenomen |
|BalancingDelayAfterNewNode | Tijd in seconden, standaard waarde is 120 |Dynamisch|Geef een tijds duur in seconden op. Start geen taak verdeling binnen deze periode na het toevoegen van een nieuw knoop punt. |
|BalancingDelayAfterNodeDown | Tijd in seconden, standaard waarde is 120 |Dynamisch|Geef een tijds duur in seconden op. Onderhouds activiteiten binnen deze periode niet starten na een gebeurtenis omlaag in een knoop punt. |
|BlockNodeInUpgradeConstraintPriority | Int, standaard is 0 |Dynamisch|Bepaalt de prioriteit van de capaciteits beperking: 0: hard; 1: zacht; negatief: negeren  |
|CapacityConstraintPriority | Int, standaard is 0 | Dynamisch|Bepaalt de prioriteit van de capaciteits beperking: 0: hard; 1: zacht; negatief: negeren. |
|ConsecutiveDroppedMovementsHealthReportLimit | Int, standaard waarde is 20 | Dynamisch|Hiermee definieert u het aantal opeenvolgende keren dat door ResourceBalancer verzonden bewegingen worden verwijderd voordat diagnostische gegevens worden uitgevoerd en status waarschuwingen worden verzonden. Negatief: er zijn geen waarschuwingen verzonden onder deze voor waarde. |
|ConstraintFixPartialDelayAfterNewNode | Tijd in seconden, standaard waarde is 120 |Dynamisch| Geef een tijds duur in seconden op. DDo geen FaultDomain-en upgrade Domain-beperkings schendingen binnen deze periode oplossen na het toevoegen van een nieuw knoop punt. |
|ConstraintFixPartialDelayAfterNodeDown | Tijd in seconden, standaard waarde is 120 |Dynamisch| Geef een tijds duur in seconden op. Herstel de FaultDomain-en upgrade Domain-beperkings schendingen binnen deze periode na een gebeurtenis met een knoop punt. |
|ConstraintViolationHealthReportLimit | Int, standaard waarde is 50 |Dynamisch| Definieert het aantal keren dat de beperking van de replica in strijd is met het permanent ongedaan maken voordat diagnostische gegevens worden uitgevoerd en status rapporten worden verzonden. |
|DetailedConstraintViolationHealthReportLimit | Int, standaard waarde is 200 |Dynamisch| Hiermee definieert u het aantal keren dat de beperking van de replica in strijd is met een niet-verholpen periode voordat diagnostische gegevens worden uitgevoerd en gedetailleerde status rapporten worden verzonden. |
|DetailedDiagnosticsInfoListLimit | Int, standaard waarde is 15 |Dynamisch| Hiermee definieert u het aantal diagnostische vermeldingen (met gedetailleerde informatie) per beperking die moeten worden meegenomen vóór afkap ping in diagnostische gegevens.|
|DetailedNodeListLimit | Int, standaard waarde is 15 |Dynamisch| Hiermee definieert u het aantal knoop punten per beperking dat moet worden meegenomen vóór afkap ping in de niet-geplaatste replica rapporten. |
|DetailedPartitionListLimit | Int, standaard waarde is 15 |Dynamisch| Hiermee definieert u het aantal partities per diagnostische vermelding voor een beperking die moet worden ingevoegd vóór afkap ping in Diagnostics. |
|DetailedVerboseHealthReportLimit | Int, standaard waarde is 200 | Dynamisch|Hiermee definieert u het aantal keren dat een niet-geplaatste replica permanent moet worden geplaatst voordat gedetailleerde status rapporten worden verzonden. |
|EnforceUserServiceMetricCapacities|BOOL, default is FALSE | Statisch |Hiermee schakelt u de beveiliging van infrastructuur services in. Alle gebruikers services bevinden zich in één taak object-cgroup en zijn beperkt tot de opgegeven hoeveelheid resources. Dit moet statisch zijn (opnieuw starten van FabricHost) als het maken en verwijderen van het gebruikers taak object en het instellen van limieten voor het openen van de Fabric-host is voltooid. |
|FaultDomainConstraintPriority | Int, standaard is 0 |Dynamisch| Bepaalt de prioriteit van de fout domein beperking: 0: hard; 1: zacht; negatief: negeren. |
|GlobalMovementThrottleCountingInterval | Tijd in seconden, standaard waarde is 600 |Statisch| Geef een tijds duur in seconden op. Geef de lengte van het voorbije interval op voor het bijhouden van replica bewegingen per domein (gebruikt samen met GlobalMovementThrottleThreshold). Kan worden ingesteld op 0 om globale beperkingen te negeren. |
|GlobalMovementThrottleThreshold | Uint, standaard waarde is 1000 |Dynamisch| Het maximum aantal verplaatsingen dat is toegestaan in de Balancing-fase in het vorige interval dat wordt aangegeven door GlobalMovementThrottleCountingInterval. |
|GlobalMovementThrottleThresholdForBalancing | Uint, standaard waarde is 0 | Dynamisch|Het maximum aantal verplaatsingen dat is toegestaan in de onderverdelings fase in het vorige interval dat wordt aangegeven door GlobalMovementThrottleCountingInterval. 0 geeft aan dat er geen limiet is. |
|GlobalMovementThrottleThresholdForPlacement | Uint, standaard waarde is 0 |Dynamisch| Het maximum aantal verplaatsingen dat is toegestaan in de plaatsings fase in het vorige interval aangegeven door GlobalMovementThrottleCountingInterval. 0 geeft geen limiet aan.|
|GlobalMovementThrottleThresholdPercentage|Double, standaard waarde is 0|Dynamisch|Het maximale aantal totale verplaatsingen dat is toegestaan in de onderverdelings-en plaatsings fasen (uitgedrukt als percentage van het totale aantal replica's in het cluster) in het vorige interval dat wordt aangegeven door GlobalMovementThrottleCountingInterval. 0 geeft aan dat er geen limiet is. Als zowel deze als GlobalMovementThrottleThreshold zijn opgegeven; vervolgens wordt er meer conservatieve limiet gebruikt.|
|GlobalMovementThrottleThresholdPercentageForBalancing|Double, standaard waarde is 0|Dynamisch|Het maximum aantal verplaatsingen dat is toegestaan in de onderverdelings fase (uitgedrukt als percentage van het totale aantal replica's in PLB) in het vorige interval dat wordt aangegeven door GlobalMovementThrottleCountingInterval. 0 geeft aan dat er geen limiet is. Als zowel deze als GlobalMovementThrottleThresholdForBalancing zijn opgegeven; vervolgens wordt er meer conservatieve limiet gebruikt.|
|InBuildThrottlingAssociatedMetric | teken reeks, standaard instelling is |Statisch| De bijbehorende metrische naam voor deze beperking. |
|InBuildThrottlingEnabled | BOOL, default is False |Dynamisch| Bepaal of de beperking in het builden is ingeschakeld. |
|InBuildThrottlingGlobalMaxValue | Int, standaard is 0 |Dynamisch|Het maximale aantal replica's dat in-Build is, is globaal toegestaan. |
|InterruptBalancingForAllFailoverUnitUpdates | BOOL, default is False | Dynamisch|Hiermee wordt bepaald of een type update van een failover-eenheid moet worden onderbroken snelle of langzame uitvoering. Met de opgegeven ' onwaar ' uitvoering van Balancing wordt onderbroken als FailoverUnit: wordt gemaakt/verwijderd. bevat ontbrekende replica's; de locatie van de primaire replica of het gewijzigde aantal replica's is gewijzigd. De uitvoering van Balancing wordt niet onderbroken in andere gevallen: als FailoverUnit: heeft extra replica's; een replica vlag gewijzigd; alleen de partitie versie of andere cases gewijzigd. |
|MinConstraintCheckInterval | Tijd in seconden, standaard waarde is 1 |Dynamisch| Geef een tijds duur in seconden op. Definieert de minimale hoeveelheid tijd die moet worden door gegeven voordat twee opeenvolgende beperkings controles worden afgerond. |
|MinLoadBalancingInterval | Tijd in seconden, standaard waarde is 5 |Dynamisch| Geef een tijds duur in seconden op. Definieert de minimale hoeveelheid tijd die moet worden door gegeven voordat twee opeenvolgende Balancing-afrondingen worden afgerond. |
|MinPlacementInterval | Tijd in seconden, standaard waarde is 1 |Dynamisch| Geef een tijds duur in seconden op. Definieert de minimale hoeveelheid tijd die moet worden door gegeven voordat twee opeenvolgende plaatsing wordt afgerond. |
|MoveExistingReplicaForPlacement | BOOL, default is True |Dynamisch|Instelling die bepaalt of de bestaande replica tijdens de plaatsing moet worden verplaatst. |
|MovementPerPartitionThrottleCountingInterval | Tijd in seconden, standaard waarde is 600 |Statisch| Geef een tijds duur in seconden op. Geef de lengte van het voorbije interval voor het bijhouden van replica bewegingen voor elke partitie (gebruikt samen met MovementPerPartitionThrottleThreshold). |
|MovementPerPartitionThrottleThreshold | Uint, standaard waarde is 50 |Dynamisch| Er wordt geen taak verdeling gerelateerd voor een partitie als het aantal aan de taak verdeling gerelateerde verplaatsingen voor replica's van die partitie is bereikt of overschreden MovementPerFailoverUnitThrottleThreshold in het vorige interval dat wordt aangegeven door MovementPerPartitionThrottleCountingInterval. |
|MoveParentToFixAffinityViolation | BOOL, default is False |Dynamisch| Instelling die bepaalt of bovenliggende replica's kunnen worden verplaatst om de affiniteits beperkingen op te lossen.|
|PartiallyPlaceServices | BOOL, default is True |Dynamisch| Hiermee wordt bepaald of alle service replica's in het cluster "alle of niets" worden geplaatst op basis van een beperkt aantal geschikte knoop punten.|
|PlaceChildWithoutParent | BOOL, default is True | Dynamisch|Instelling die bepaalt of de onderliggende service replica kan worden geplaatst als er geen bovenliggende replica actief is. |
|PlacementConstraintPriority | Int, standaard is 0 | Dynamisch|Bepaalt de prioriteit van de plaatsings beperking: 0: hard; 1: zacht; negatief: negeren. |
|PlacementConstraintValidationCacheSize | Int, standaard waarde is 10000 |Dynamisch| Hiermee wordt de grootte beperkt van de tabel die wordt gebruikt voor snelle validatie en caching van expressies voor de plaatsings beperking. |
|PlacementSearchTimeout | Tijd in seconden, standaard waarde is 0,5 |Dynamisch| Geef een tijds duur in seconden op. Bij het plaatsen van services; Zoek naar de meeste lange waarde voordat een resultaat wordt geretourneerd. |
|PLBRefreshGap | Tijd in seconden, standaard waarde is 1 |Dynamisch| Geef een tijds duur in seconden op. Hiermee definieert u de minimale hoeveelheid tijd die moet worden door gegeven voordat de status van de PLB opnieuw wordt vernieuwd. |
|PreferredLocationConstraintPriority | Int, standaard waarde is 2| Dynamisch|Bepaalt de prioriteit van de voorkeurs locatie beperking: 0: hard; 1: zacht; 2: optimalisatie; negatief: negeren |
|PreferredPrimaryDomainsConstraintPriority| Int, standaard waarde is 1 | Dynamisch| Hiermee wordt de prioriteit van de voorkeurs beperking van het primaire domein bepaald: 0: hard; 1: zacht; negatief: negeren |
|PreferUpgradedUDs|BOOL, default is FALSE|Dynamisch|Hiermee schakelt u logica in en uit die de voor keur geeft aan de al bijgewerkte UDs. Vanaf SF 7,0 wordt de standaard waarde voor deze para meter gewijzigd van waar in ONWAAR.|
|PreventTransientOvercommit | BOOL, default is False | Dynamisch|Bepaalt dat PLB onmiddellijk moet worden geteld voor resources die worden vrijgemaakt door de gestarte verplaatsingen. Standaard; PLB kan verplaatsing initiëren en verplaatsen op hetzelfde knoop punt, waardoor tijdelijke door Voer kan worden gemaakt. Als deze para meter wordt ingesteld op True, voor komt u dat dit soort overdoor voeringen en defragmentatie op aanvraag (ook wel placementWithMove) wordt uitgeschakeld. |
|ScaleoutCountConstraintPriority | Int, standaard is 0 |Dynamisch| Bepaalt de prioriteit van de beperking voor het aantal uitschalen: 0: hard; 1: zacht; negatief: negeren. |
|SubclusteringEnabled|BOOL, default is FALSE | Dynamisch |Subclustering bevestigen bij het berekenen van de standaard afwijking voor de verdeling |
|SubclusteringReportingPolicy| Int, standaard waarde is 1 |Dynamisch|Definieert hoe en als de subclustering status rapporten worden verzonden: 0: niet rapporteren; 1: waarschuwing; 2: OK |
|SwapPrimaryThrottlingAssociatedMetric | teken reeks, standaard instelling is|Statisch| De bijbehorende metrische naam voor deze beperking. |
|SwapPrimaryThrottlingEnabled | BOOL, default is False|Dynamisch| Bepaal of het wisselen van primaire beperking is ingeschakeld. |
|SwapPrimaryThrottlingGlobalMaxValue | Int, standaard is 0 |Dynamisch| Het maximale aantal wissel-primaire replica's dat globaal is toegestaan. |
|TraceCRMReasons |BOOL, default is True |Dynamisch|Hiermee geeft u op of redenen voor door CRM uitgegeven verplaatsingen naar het kanaal operationele gebeurtenissen moeten worden opgespoord. |
|UpgradeDomainConstraintPriority | Int, standaard waarde is 1| Dynamisch|Bepaalt de prioriteit van de beperking van het upgrade domein: 0: hard; 1: zacht; negatief: negeren. |
|UseMoveCostReports | BOOL, default is False | Dynamisch|Hiermee geeft u op dat de LB het kosten element van de functie score moet negeren. Als gevolg van een potentieel groot aantal verplaatsingen voor een betere evenwichtige plaatsing. |
|UseSeparateSecondaryLoad | BOOL, default is True | Dynamisch|Instelling die bepaalt of afzonderlijke belasting moet worden gebruikt voor secundaire replica's. |
|UseSeparateSecondaryMoveCost | BOOL, default is False | Dynamisch|Instelling die bepaalt of afzonderlijke verplaatsings kosten moeten worden gebruikt voor secundaire replica's. |
|ValidatePlacementConstraint | BOOL, default is True |Dynamisch| Hiermee wordt aangegeven of de PlacementConstraint-expressie voor een service wordt gevalideerd wanneer de ServiceDescription van een service wordt bijgewerkt. |
|ValidatePrimaryPlacementConstraintOnPromote| BOOL, default is TRUE |Dynamisch|Hiermee wordt aangegeven of de PlacementConstraint-expressie voor een service wordt geëvalueerd voor de primaire voor keur bij een failover. |
|VerboseHealthReportLimit | Int, standaard waarde is 20 | Dynamisch|Hiermee definieert u het aantal keren dat een replica moet worden uitgevoerd voordat er een status waarschuwing wordt gerapporteerd (als uitgebreide status rapportage is ingeschakeld). |
|NodeLoadsOperationalTracingEnabled | BOOL, default is True |Dynamisch|Configuratie waarmee knooppunt operationele structurele tracering in het gebeurtenis archief kan worden geladen. |
|NodeLoadsOperationalTracingInterval | Time span, standaard waarde is gebruikelijk:: time span:: FromSeconds (20) | Dynamisch|Geef een tijds duur in seconden op. Het interval voor het traceren van knoop punten die worden geladen in het gebeurtenis Archief voor elk service domein. |

## <a name="reconfigurationagent"></a>ReconfigurationAgent

| **Parameter** | **Toegestane waarden** | **Upgrade beleid** | **Uitleg of korte beschrijving** |
| --- | --- | --- | --- |
|ApplicationUpgradeMaxReplicaCloseDuration | Tijd in seconden, standaard waarde is 900 |Dynamisch|Geef een tijds duur in seconden op. De duur waarvoor het systeem wacht voordat de service-hosts worden beëindigd die replica's hebben die tijdens de upgrade van de toepassing zijn vastgelopen.|
|FabricUpgradeMaxReplicaCloseDuration | Tijd in seconden, standaard waarde is 900 |Dynamisch| Geef een tijds duur in seconden op. De duur waarvoor het systeem wacht voordat de service-hosts worden beëindigd die replica's hebben die tijdens de upgrade van de infra structuur zijn vastgelopen. |
|GracefulReplicaShutdownMaxDuration|Time span, standaard waarde is gebruikelijk:: time span:: FromSeconds (120)|Dynamisch|Geef een tijds duur in seconden op. De duur waarvoor het systeem wacht voordat de service-hosts worden afgesloten waarvan de replica's zijn vastgelopen. Als deze waarde is ingesteld op 0, worden replica's niet weer gegeven om te worden gesloten.|
|NodeDeactivationMaxReplicaCloseDuration | Tijd in seconden, standaard waarde is 900 |Dynamisch|Geef een tijds duur in seconden op. De duur waarvoor het systeem wacht voordat de service-hosts worden beëindigd die replica's hebben die tijdens het deactiveren van het knoop punt zijn vastgelopen. |
|PeriodicApiSlowTraceInterval | Tijd in seconden, standaard waarde is 5 minuten |Dynamisch| Geef een tijds duur in seconden op. PeriodicApiSlowTraceInterval definieert het interval waarover trage API-aanroepen worden overschreven door de API-monitor. |
|ReplicaChangeRoleFailureRestartThreshold|int, standaard waarde is 10|Dynamisch| Geheeltallige. Geef het aantal API-fouten op dat is opgetreden tijdens de primaire promotie waarna de actie voor automatisch beperken (replica opnieuw opstarten) wordt toegepast. |
|ReplicaChangeRoleFailureWarningReportThreshold|int, standaard waarde is 2147483647|Dynamisch| Geheeltallige. Geef het aantal API-fouten op dat wordt weer gegeven tijdens de primaire promotie, waarna het waarschuwings status rapport wordt gegenereerd.|
|ServiceApiHealthDuration | Tijd in seconden, standaard waarde is 30 minuten |Dynamisch| Geef een tijds duur in seconden op. ServiceApiHealthDuration definieert hoelang er wordt gewacht tot een service-API wordt uitgevoerd voordat er een melding wordt gegenereerd. |
|ServiceReconfigurationApiHealthDuration | Tijd in seconden, standaard waarde is 30 |Dynamisch| Geef een tijds duur in seconden op. ServiceReconfigurationApiHealthDuration definieert hoelang er wordt gewacht tot een service-API wordt uitgevoerd voordat er een slechte melding wordt gegenereerd. Dit geldt voor API-aanroepen die van invloed zijn op de beschik baarheid.|

## <a name="replication"></a>Replicatie
| **Parameter** | **Toegestane waarden** | **Upgrade beleid**| **Uitleg of korte beschrijving** |
| --- | --- | --- | --- |
|BatchAcknowledgementInterval|Time span, standaard waarde is gebruikelijk:: time span:: FromMilliseconds (15)|Statisch|Geef een tijds duur in seconden op. Bepaalt de hoeveelheid tijd die de Replicator wacht na het ontvangen van een bewerking voordat een bevestiging wordt verzonden. Andere bewerkingen die tijdens deze periode worden ontvangen, zullen hun bevestigingen terugsturen in één bericht-> het netwerk verkeer te verminderen, maar mogelijk de door Voer van de Replicator te verminderen.|
|MaxCopyQueueSize|uint, standaard waarde is 1024|Statisch|Dit is de maximum waarde definieert de aanvankelijke grootte voor de wachtrij die replicatie bewerkingen onderhoudt. Houd er rekening mee dat dit een macht van 2 moet zijn. Als tijdens runtime de wachtrij wordt uitgebreid naar deze grootte, wordt de bewerking tussen de primaire en secundaire replicaties vertraagd.|
|MaxPrimaryReplicationQueueMemorySize|uint, standaard waarde is 0|Statisch|Dit is de maximum waarde van de primaire replicatie wachtrij in bytes.|
|MaxPrimaryReplicationQueueSize|uint, standaard waarde is 1024|Statisch|Dit is het maximum aantal bewerkingen dat kan voor komen in de primaire replicatie wachtrij. Houd er rekening mee dat dit een macht van 2 moet zijn.|
|MaxReplicationMessageSize|uint, standaard waarde is 52428800|Statisch|De maximale bericht grootte van de replicatie bewerkingen. De standaard waarde is 50 MB.|
|MaxSecondaryReplicationQueueMemorySize|uint, standaard waarde is 0|Statisch|Dit is de maximum waarde van de secundaire replicatie wachtrij in bytes.|
|MaxSecondaryReplicationQueueSize|uint, standaard waarde is 2048|Statisch|Dit is het maximum aantal bewerkingen dat kan voor komen in de secundaire replicatie wachtrij. Houd er rekening mee dat dit een macht van 2 moet zijn.|
|QueueHealthMonitoringInterval|Time span, standaard waarde is gebruikelijk:: time span:: FromSeconds (30)|Statisch|Geef een tijds duur in seconden op. Deze waarde bepaalt de tijds periode die door de Replicator wordt gebruikt voor het bewaken van waarschuwings-en fout gebeurtenissen in de wachtrij voor replicatie bewerkingen. Met de waarde ' 0 ' wordt de status controle uitgeschakeld |
|QueueHealthWarningAtUsagePercent|uint, standaard waarde is 80|Statisch|Deze waarde bepaalt het gebruik van de replicatie wachtrij (in procenten) waarna waarschuwing wordt gegeven over hoog wachtrij gebruik. Dit doet u na een respijt interval van QueueHealthMonitoringInterval. Als het gebruik van de wachtrij onder dit percentage ligt in het respijt interval|
|ReplicatorAddress|teken reeks, standaard waarde is ' localhost: 0 '|Statisch|Het eind punt in de vorm van een teken reeks-' IP: poort ' die wordt gebruikt door de Windows Fabric Replicator om verbindingen met andere replica's tot stand te brengen om bewerkingen te kunnen verzenden/ontvangen.|
|ReplicatorListenAddress|teken reeks, standaard waarde is ' localhost: 0 '|Statisch|Het eind punt in de vorm van een teken reeks-' IP: poort ' die wordt gebruikt door de Windows Fabric Replicator om bewerkingen van andere replica's te ontvangen.|
|ReplicatorPublishAddress|teken reeks, standaard waarde is ' localhost: 0 '|Statisch|Het eind punt in de vorm van een teken reeks-' IP: poort ' die wordt gebruikt door de Windows Fabric Replicator om bewerkingen naar andere replica's te verzenden.|
|RetryInterval|Time span, standaard waarde is gebruikelijk:: time span:: FromSeconds (5)|Statisch|Geef een tijds duur in seconden op. Wanneer een bewerking is verbroken of afgekeurd, bepaalt deze timer hoe vaak de Replicator de bewerking opnieuw probeert te verzenden.|

## <a name="resourcemonitorservice"></a>ResourceMonitorService
| **Parameter** | **Toegestane waarden** | **Upgrade beleid**| **Uitleg of korte beschrijving** |
| --- | --- | --- | --- |
|IsEnabled|BOOL, default is FALSE |Statisch|Hiermee wordt bepaald of de service is ingeschakeld in het cluster of niet. |

## <a name="runas"></a>RunAs

| **Parameter** | **Toegestane waarden** | **Upgrade beleid** | **Uitleg of korte beschrijving** |
| --- | --- | --- | --- |
|RunAsAccountName |teken reeks, standaard instelling is |Dynamisch|Hiermee wordt de RunAs-account naam aangegeven. Dit is alleen nodig voor het account type domein gebruiker of ManagedServiceAccount. Geldige waarden zijn "domein\gebruiker" of " user@domain ". |
|RunAsAccountType|teken reeks, standaard instelling is |Dynamisch|Hiermee wordt het account type runas aangegeven. Dit is nodig voor de sectie runas, geldige waarden zijn ' Domain User/Network Service/ManagedServiceAccount/LocalSystem '.|
|RunAsPassword|teken reeks, standaard instelling is |Dynamisch|Hiermee wordt het wacht woord van het runas-account aangegeven. Dit is alleen nodig voor het account type domein gebruiker. |

## <a name="runas_dca"></a>RunAs_DCA

| **Parameter** | **Toegestane waarden** | **Upgrade beleid** | **Uitleg of korte beschrijving** |
| --- | --- | --- | --- |
|RunAsAccountName |teken reeks, standaard instelling is |Dynamisch|Hiermee wordt de RunAs-account naam aangegeven. Dit is alleen nodig voor het account type domein gebruiker of ManagedServiceAccount. Geldige waarden zijn "domein\gebruiker" of " user@domain ". |
|RunAsAccountType|teken reeks, standaard instelling is |Dynamisch|Hiermee wordt het account type runas aangegeven. Dit is nodig voor alle runas-secties geldige waarden zijn "Lokalegebruiker/Domain Service/ManagedServiceAccount/LocalSystem". |
|RunAsPassword|teken reeks, standaard instelling is |Dynamisch|Hiermee wordt het wacht woord van het runas-account aangegeven. Dit is alleen nodig voor het account type domein gebruiker. |

## <a name="runas_fabric"></a>RunAs_Fabric

| **Parameter** | **Toegestane waarden** | **Upgrade beleid** | **Uitleg of korte beschrijving** |
| --- | --- | --- | --- |
|RunAsAccountName |teken reeks, standaard instelling is |Dynamisch|Hiermee wordt de RunAs-account naam aangegeven. Dit is alleen nodig voor het account type domein gebruiker of ManagedServiceAccount. Geldige waarden zijn "domein\gebruiker" of " user@domain ". |
|RunAsAccountType|teken reeks, standaard instelling is |Dynamisch|Hiermee wordt het account type runas aangegeven. Dit is nodig voor alle runas-secties geldige waarden zijn "Lokalegebruiker/Domain Service/ManagedServiceAccount/LocalSystem". |
|RunAsPassword|teken reeks, standaard instelling is |Dynamisch|Hiermee wordt het wacht woord van het runas-account aangegeven. Dit is alleen nodig voor het account type domein gebruiker. |

## <a name="runas_httpgateway"></a>RunAs_HttpGateway

| **Parameter** | **Toegestane waarden** | **Upgrade beleid** | **Uitleg of korte beschrijving** |
| --- | --- | --- | --- |
|RunAsAccountName |teken reeks, standaard instelling is |Dynamisch|Hiermee wordt de RunAs-account naam aangegeven. Dit is alleen nodig voor het account type domein gebruiker of ManagedServiceAccount. Geldige waarden zijn "domein\gebruiker" of " user@domain ". |
|RunAsAccountType|teken reeks, standaard instelling is |Dynamisch|Hiermee wordt het account type runas aangegeven. Dit is nodig voor alle runas-secties geldige waarden zijn "Lokalegebruiker/Domain Service/ManagedServiceAccount/LocalSystem". |
|RunAsPassword|teken reeks, standaard instelling is |Dynamisch|Hiermee wordt het wacht woord van het runas-account aangegeven. Dit is alleen nodig voor het account type domein gebruiker. |

## <a name="security"></a>Beveiliging
| **Parameter** | **Toegestane waarden** |**Upgrade beleid**| **Uitleg of korte beschrijving** |
| --- | --- | --- | --- |
|AADCertEndpointFormat|teken reeks, standaard instelling is|Statisch|AAD-certificaat eindpunt indeling, standaard-Azure-Commercial, opgegeven voor niet-standaard-omgeving, zoals Azure Government "https: \/ /login.microsoftonline.us/ {0} /federationmetadata/2007-06/federationmetadata.xml" |
|AADClientApplication|teken reeks, standaard instelling is|Statisch|Systeem eigen client toepassings naam of ID die infrastructuur clients vertegenwoordigt |
|AADClusterApplication|teken reeks, standaard instelling is|Statisch|Web-API-toepassings naam of-ID die het cluster vertegenwoordigt |
|AADLoginEndpoint|teken reeks, standaard instelling is|Statisch|AAD-aanmeldings eindpunt, standaard-Azure-Commercial, opgegeven voor niet-standaard omgevingen, zoals Azure Government ' https: \/ /login.microsoftonline.us ' |
|AADTenantId|teken reeks, standaard instelling is|Statisch|Tenant-ID (GUID) |
|AcceptExpiredPinnedClusterCertificate|BOOL, default is FALSE|Dynamisch|Vlag waarmee wordt aangegeven of verlopen cluster certificaten moeten worden geaccepteerd die zijn gedeclareerd door de vinger afdruk, alleen van toepassing is op cluster certificaten. zodat het cluster actief blijft. |
|AdminClientCertThumbprints|teken reeks, standaard instelling is|Dynamisch|Vinger afdrukken van certificaten die worden gebruikt door clients in de rol Admin. Dit is een lijst met door komma's gescheiden namen. |
|AADTokenEndpointFormat|teken reeks, standaard instelling is|Statisch|AAD-token-eind punt, standaard-Azure-Commercial, opgegeven voor niet-standaard omgevingen, zoals Azure Government ' https: \/ /login.microsoftonline.us/ {0} ' |
|AdminClientClaims|teken reeks, standaard instelling is|Dynamisch|Alle mogelijke claims die worden verwacht door beheerclients; dezelfde indeling als ClientClaims; deze lijst wordt intern toegevoegd aan ClientClaims. u hoeft ook niet dezelfde vermeldingen aan ClientClaims toe te voegen. |
|AdminClientIdentities|teken reeks, standaard instelling is|Dynamisch|Windows-identiteiten van Fabric-clients in beheerdersrol; wordt gebruikt voor het autoriseren van bevoegde infrastructuur bewerkingen. Het is een door komma's gescheiden lijst. elk item is een domein account naam of groeps naam. Voor het gemak; het account dat wordt uitgevoerd fabric.exe wordt automatisch toegewezen aan de rol Admin. Daarom is groep ServiceFabricAdministrators. |
|AppRunAsAccountGroupX509Folder|teken reeks, standaard waarde is/Home/sfuser/sfusercerts |Statisch|Map waar AppRunAsAccountGroup x509-certificaten en persoonlijke sleutels zich bevinden |
|CertificateExpirySafetyMargin|Time span, standaard is gebruikelijk:: time span:: FromMinutes (43200)|Statisch|Geef een tijds duur in seconden op. Veiligheids marge voor het verlopen van certificaten; status van het certificaat van het status rapport wordt gewijzigd van OK in een waarschuwing wanneer de verval datum dichterbij komt. De standaard waarde is 30 dagen. |
|CertificateHealthReportingInterval|Time span, standaard waarde is gebruikelijk:: time span:: FromSeconds (3600 * 8)|Statisch|Geef een tijds duur in seconden op. Geef het interval voor de certificaat status rapportage op; standaard ingesteld op 8 uur; Als u deze instelling instelt op 0, wordt rapportage van certificaat status uitgeschakeld |
|ClientCertThumbprints|teken reeks, standaard instelling is|Dynamisch|Vinger afdrukken van certificaten die door clients worden gebruikt om te communiceren met het cluster; cluster maakt gebruik van deze machtiging voor binnenkomende verbindingen. Dit is een lijst met door komma's gescheiden namen. |
|ClientClaimAuthEnabled|BOOL, default is FALSE|Statisch|Hiermee wordt aangegeven of op claims gebaseerde verificatie is ingeschakeld op clients. Als u deze waarde instelt, wordt ClientRoleEnabled impliciet ingesteld. |
|ClientClaims|teken reeks, standaard instelling is|Dynamisch|Alle mogelijke claims die worden verwacht van clients om verbinding te maken met de gateway. Dit is een OR-lijst: ClaimsEntry \| \| ClaimsEntry \| \| ClaimsEntry... elke ClaimsEntry is een ' AND '-lijst: claim type = ClaimValue && claim type = ClaimValue && claim type = ClaimValue... |
|ClientIdentities|teken reeks, standaard instelling is|Dynamisch|Windows-identiteiten van FabricClient; naamgevings gateway gebruikt deze om binnenkomende verbindingen te autoriseren. Het is een door komma's gescheiden lijst. elk item is een domein account naam of groeps naam. Voor het gemak; het account dat fabric.exe uitvoert, wordt automatisch toegestaan. Dit zijn dus groeps-ServiceFabricAllowedUsers en ServiceFabricAdministrators. |
|ClientRoleEnabled|BOOL, default is FALSE|Statisch|Hiermee wordt aangegeven of de client functie is ingeschakeld. Als deze eigenschap is ingesteld op True; aan clients worden rollen toegewezen op basis van hun identiteiten. Voor v2; Als u dit inschakelt, kan de client niet in AdminClientCommonNames/AdminClientIdentities alleen alleen-lezen bewerkingen uitvoeren. |
|ClusterCertThumbprints|teken reeks, standaard instelling is|Dynamisch|Vinger afdrukken van certificaten die mogen worden toegevoegd aan het cluster; een lijst met door komma's gescheiden namen. |
|ClusterCredentialType|teken reeks, standaard is ' geen '|Niet toegestaan|Hiermee wordt het type beveiligings referenties aangegeven dat moet worden gebruikt om het cluster te beveiligen. Geldige waarden zijn ' none/x509/Windows ' |
|ClusterIdentities|teken reeks, standaard instelling is|Dynamisch|Windows-identiteiten van cluster knooppunten; wordt gebruikt voor autorisatie van cluster lidmaatschap. Het is een door komma's gescheiden lijst. elk item is een domein account naam of groeps naam |
|ClusterSpn|teken reeks, standaard instelling is|Niet toegestaan|De naam van de service-principal van het cluster; Wanneer infrastructuur als één domein gebruiker (gMSA/domein gebruikers account) wordt uitgevoerd. Het is de SPN van lease-listeners en listeners in fabric.exe: Federation-listeners; interne replicatie-listeners; de listener van de runtime-service en de naam van de gateway-listener. Dit moet leeg blijven als de infrastructuur resources worden uitgevoerd als machine accounts. in dat geval is het verbinden van een side Compute listener-SPN van listener-transport adres. |
|CrlCheckingFlag|uint, de standaard waarde is 0x40000000|Dynamisch|Standaard validatie vlag voor certificaat keten; kan worden overschreven door onderdeel-specifieke vlag; bijvoorbeeld: Federation/X509CertChainFlags 0x10000000 CERT_CHAIN_REVOCATION_CHECK_END_CERT 0x20000000 CERT_CHAIN_REVOCATION_CHECK_CHAIN 0x40000000 CERT_CHAIN_REVOCATION_CHECK_CHAIN_EXCLUDE_ROOT 0x80000000 CERT_CHAIN_REVOCATION_CHECK_CACHE_ONLY instelling op 0 Hiermee wordt de volledige lijst met ondersteunde waarden voor het controleren van de CRL door dwFlags van CertGetCertificateChain beschreven. https://msdn.microsoft.com/library/windows/desktop/aa376078(v=vs.85).aspx |
|CrlDisablePeriod|Time span, standaard waarde is gebruikelijk:: time span:: FromMinutes (15)|Dynamisch|Geef een tijds duur in seconden op. Hoe lang CRL-controle is uitgeschakeld voor een bepaald certificaat nadat er een offline fout is opgetreden; Als de CRL-offline fout kan worden genegeerd. |
|CrlOfflineHealthReportTtl|Time span, standaard waarde is gebruikelijk:: time span:: FromMinutes (1440)|Dynamisch|Geef een tijds duur in seconden op. |
|DisableFirewallRuleForDomainProfile| BOOL, default is TRUE |Statisch| Hiermee wordt aangegeven of de firewall regel niet moet worden ingeschakeld voor het domein profiel |
|DisableFirewallRuleForPrivateProfile| BOOL, default is TRUE |Statisch| Hiermee wordt aangegeven of de firewall regel niet moet worden ingeschakeld voor een persoonlijk profiel | 
|DisableFirewallRuleForPublicProfile| BOOL, default is TRUE | Statisch|Hiermee wordt aangegeven of de firewall regel niet moet worden ingeschakeld voor een openbaar profiel |
| EnforceLinuxMinTlsVersion | BOOL, default is FALSE | Statisch | Indien ingesteld op True; alleen TLS-versie 1.2 + wordt ondersteund.  Indien onwaar; oudere versies van TLS ondersteunen. Alleen van toepassing op Linux |
| EnforcePrevalidationOnSecurityChanges | BOOL, default is FALSE| Dynamisch | Vlag waarmee het gedrag van cluster upgrades wordt bepaald tijdens het detecteren van wijzigingen in de beveiligings instellingen. Als deze eigenschap is ingesteld op ' True ', probeert de upgrade van het cluster ervoor te zorgen dat ten minste één van de certificaten die overeenkomen met een van de presentatie regels, een bijbehorende validatie regel kan door geven. De validatie wordt uitgevoerd voordat de nieuwe instellingen worden toegepast op een wille keurig knoop punt, maar wordt alleen uitgevoerd op het knoop punt dat als host fungeert voor de primaire replica van de Cluster beheer-service op het moment dat de upgrade wordt gestart. De standaard waarde is momenteel ingesteld op ' false '; vanaf versie 7,1 wordt de instelling ingesteld op ' True ' voor nieuwe Azure Service Fabric-clusters.|
|FabricHostSpn| teken reeks, standaard instelling is |Statisch| Principal-naam van de service FabricHost; Wanneer infrastructuur als één domein gebruiker (gMSA/domein gebruikers account) wordt uitgevoerd en FabricHost wordt uitgevoerd onder machine account. Het is de SPN van IPC listener voor FabricHost; Deze moet standaard leeg blijven omdat FabricHost wordt uitgevoerd onder een computer account |
|IgnoreCrlOfflineError|BOOL, default is FALSE|Dynamisch|Of de CRL-offline fout moet worden genegeerd wanneer inkomende client certificaten worden gecontroleerd door de server zijde |
|IgnoreSvrCrlOfflineError|BOOL, default is TRUE|Dynamisch|Hiermee wordt aangegeven of de CRL-offline fout moet worden genegeerd wanneer inkomende server certificaten door client zijde worden geverifieerd. de standaard waarde is True. Aanvallen met ingetrokken server certificaten vereisen compromissen van DNS; moeilijker dan met ingetrokken client certificaten. |
|ServerAuthCredentialType|teken reeks, standaard is ' geen '|Statisch|Hiermee wordt het type beveiligings referenties aangegeven dat moet worden gebruikt om de communicatie tussen FabricClient en het cluster te beveiligen. Geldige waarden zijn ' none/x509/Windows ' |
|ServerCertThumbprints|teken reeks, standaard instelling is|Dynamisch|Vinger afdrukken van server certificaten die worden gebruikt door het cluster om te communiceren met clients; clients gebruiken deze om het cluster te verifiëren. Dit is een lijst met door komma's gescheiden namen. |
|SettingsX509StoreName| teken reeks, standaard is ' MY '| Dynamisch|X509-certificaat archief dat door de infra structuur wordt gebruikt voor configuratie beveiliging |
|UseClusterCertForIpcServerTlsSecurity|BOOL, default is FALSE|Statisch|Of cluster certificaat moet worden gebruikt voor het beveiligen van de TLS-transport eenheid van de IPC-server |
|X509Folder|teken reeks, standaard waarde is/var/lib/waagent|Statisch|Map waar x509-certificaten en persoonlijke sleutels zich bevinden |
|TLS1_2_CipherList| tekenreeks| Statisch|Indien ingesteld op een niet-lege teken reeks; Hiermee wordt de ondersteunde versleutelings lijst voor TLS 1.2 en lager overschreven. Zie de documentatie ' openssl-ciphers ' voor het ophalen van de lijst met ondersteunde coderings lijsten en de lijst indeling voor beeld van een sterke coderings lijst voor TLS 1.2: ' ECDHE-ECDSA-AES256-GCM-SHA384: ECDHE-ECDSA-AES128-GCM-SHA256: ECDHE-RSA-AES256-GCM-SHA384: ECDHE-RSA-AES-128-GCM-SHA256: ECDHE-ECDSA-AES256-CBC-SHA384: ECDHE-ECDSA-AES128-CBC-SHA256: ECDHE-RSA-AES256-CBC-SHA384: ECDHE-RSA-AES128-CBC-SHA256, alleen van toepassing op Linux. |

## <a name="securityadminclientx509names"></a>Beveiligings-AdminClientX509Names

| **Parameter** | **Toegestane waarden** | **Upgrade beleid** | **Uitleg of korte beschrijving** |
| --- | --- | --- | --- |
|PropertyGroup|X509NameMap, standaard instelling is geen|Dynamisch|Dit is een lijst met naam-en waarde-paar. Elk "naam" is de algemene naam van het onderwerp of DnsName van x509-certificaten die zijn gemachtigd voor client bewerkingen van de beheerder. Voor een gegeven ' name ' is een door komma's gescheiden lijst met certificaat vingerafdrukken voor het vastmaken van een uitgever, indien niet leeg is, moet de directe verlener van client certificaten van de beheerder in de lijst staan. |

## <a name="securityclientaccess"></a>Beveiligings-ClientAccess

| **Parameter** | **Toegestane waarden** | **Upgrade beleid** | **Uitleg of korte beschrijving** |
| --- | --- | --- | --- |
|ActivateNode |teken reeks, standaard instelling is "beheerder" |Dynamisch| Beveiligings configuratie voor het activeren van een knoop punt. |
|CancelTestCommand |teken reeks, standaard instelling is "beheerder" |Dynamisch| Hiermee wordt een specifieke TestCommand geannuleerd: als deze zich in de vlucht bevindt. |
|CodePackageControl |teken reeks, standaard instelling is "beheerder" |Dynamisch| Beveiligings configuratie voor het opnieuw starten van code pakketten. |
|CreateApplication |teken reeks, standaard instelling is "beheerder" | Dynamisch|Beveiligings configuratie voor het maken van toepassingen. |
|CreateComposeDeployment|teken reeks, standaard instelling is "beheerder"| Dynamisch|Hiermee maakt u een samenstellende implementatie die wordt beschreven door bestanden samen te stellen |
|CreateGatewayResource|teken reeks, standaard instelling is "beheerder"| Dynamisch|Een gateway resource maken |
|Createnaam |teken reeks, standaard instelling is "beheerder" |Dynamisch|Beveiligings configuratie voor het maken van een naam-URI. |
|CreateNetwork|teken reeks, standaard instelling is "beheerder" |Dynamisch|Hiermee maakt u een container netwerk |
|CreateService |teken reeks, standaard instelling is "beheerder" |Dynamisch| Beveiligings configuratie voor het maken van services. |
|CreateServiceFromTemplate |teken reeks, standaard instelling is "beheerder" |Dynamisch|Beveiligings configuratie voor het maken van Services vanuit een sjabloon. |
|CreateVolume|teken reeks, standaard instelling is "beheerder"|Dynamisch|Hiermee maakt u een volume |
|DeactivateNode |teken reeks, standaard instelling is "beheerder" |Dynamisch| Beveiligings configuratie voor het deactiveren van een knoop punt. |
|DeactivateNodesBatch |teken reeks, standaard instelling is "beheerder" |Dynamisch| Beveiligings configuratie voor het deactiveren van meerdere knoop punten. |
|Verwijderen |teken reeks, standaard instelling is "beheerder" |Dynamisch| Beveiligings configuraties voor het verwijderen van de installatie kopie-client. |
|DeleteApplication |teken reeks, standaard instelling is "beheerder" |Dynamisch| Beveiligings configuratie voor het verwijderen van toepassingen. |
|DeleteComposeDeployment|teken reeks, standaard instelling is "beheerder"| Dynamisch|Hiermee wordt de opstel implementatie verwijderd |
|DeleteGatewayResource|teken reeks, standaard instelling is "beheerder"| Dynamisch|Hiermee verwijdert u een gateway resource |
|Verwijderen |teken reeks, standaard instelling is "beheerder" |Dynamisch|Beveiligings configuratie voor het verwijderen van de naam van een URI. |
|DeleteNetwork|teken reeks, standaard instelling is "beheerder" |Dynamisch|Hiermee verwijdert u een container netwerk |
|Delete service |teken reeks, standaard instelling is "beheerder" |Dynamisch|Beveiligings configuratie voor het verwijderen van de service. |
|DeleteVolume|teken reeks, standaard instelling is "beheerder"|Dynamisch|Hiermee verwijdert u een volume.| 
|EnumerateProperties |teken reeks, standaard is ' \| \| gebruiker beheerder ' | Dynamisch|Beveiligings configuratie voor het inventariseren van naamgevings eigenschappen. |
|EnumerateSubnames |teken reeks, standaard is ' \| \| gebruiker beheerder ' |Dynamisch| Beveiligings configuratie voor het inventariseren van de naam van de URI. |
|FileContent |teken reeks, standaard instelling is "beheerder" |Dynamisch| Beveiligings configuratie voor bestands overdracht van client installatie kopie (extern naar cluster). |
|FileDownload |teken reeks, standaard instelling is "beheerder" |Dynamisch| Beveiligings configuratie voor het initiëren van down loads van client bestanden voor installatie kopieën (extern naar cluster). |
|FinishInfrastructureTask |teken reeks, standaard instelling is "beheerder" |Dynamisch| Beveiligings configuratie voor het volt ooien van infrastructuur taken. |
|GetChaosReport | teken reeks, standaard is ' \| \| gebruiker beheerder ' |Dynamisch| Haalt de status van chaos binnen een bepaald tijds interval op. |
|GetClusterConfiguration | teken reeks, standaard is ' \| \| gebruiker beheerder ' | Dynamisch|Induceert GetClusterConfiguration op een partitie. |
|GetClusterConfigurationUpgradeStatus | teken reeks, standaard is ' \| \| gebruiker beheerder ' |Dynamisch| Induceert GetClusterConfigurationUpgradeStatus op een partitie. |
|GetFabricUpgradeStatus |teken reeks, standaard is ' \| \| gebruiker beheerder ' |Dynamisch| Beveiligings configuratie voor het pollen van de upgrade status van het cluster. |
|GetFolderSize |teken reeks, standaard instelling is "beheerder" |Dynamisch|Beveiligings configuratie voor het ophalen van de mapgrootte van de File Store service |
|GetNodeDeactivationStatus |teken reeks, standaard instelling is "beheerder" |Dynamisch| Beveiligings configuratie voor het controleren van de status van deactiveren. |
|GetNodeTransitionProgress | teken reeks, standaard is ' \| \| gebruiker beheerder ' |Dynamisch| Beveiligings configuratie voor het ophalen van de voortgang van een knooppunt overgangs opdracht. |
|GetPartitionDataLossProgress | teken reeks, standaard is ' \| \| gebruiker beheerder ' | Dynamisch|Hiermee wordt de voortgang van de API-aanroep invoke data verlies opgehaald. |
|GetPartitionQuorumLossProgress | teken reeks, standaard is ' \| \| gebruiker beheerder ' |Dynamisch| Hiermee wordt de voortgang opgehaald voor een aanroep van de API invoke quorum verlies. |
|GetPartitionRestartProgress | teken reeks, standaard is ' \| \| gebruiker beheerder ' |Dynamisch| De voortgang van een restart Partition API-aanroep wordt opgehaald. |
|GetSecrets|teken reeks, standaard instelling is "beheerder"|Dynamisch|Geheime waarden ophalen |
|GetServiceDescription |teken reeks, standaard is ' \| \| gebruiker beheerder ' |Dynamisch| Beveiligings configuratie voor lange polling van service meldingen en het lezen van Service beschrijvingen. |
|GetStagingLocation |teken reeks, standaard instelling is "beheerder" |Dynamisch| Beveiligings configuratie voor het ophalen van de faserings locatie voor de installatie kopie van de client. |
|GetStoreLocation |teken reeks, standaard instelling is "beheerder" |Dynamisch| Beveiligings configuratie voor het ophalen van de locatie van het client Archief voor installatie kopieën. |
|GetUpgradeOrchestrationServiceState|teken reeks, standaard instelling is "beheerder"| Dynamisch|Induceert GetUpgradeOrchestrationServiceState op een partitie |
|GetUpgradesPendingApproval |teken reeks, standaard instelling is "beheerder" |Dynamisch| Induceert GetUpgradesPendingApproval op een partitie. |
|GetUpgradeStatus |teken reeks, standaard is ' \| \| gebruiker beheerder ' |Dynamisch| Beveiligings configuratie voor het navragen van de upgrade status van de toepassing. |
|InternalList |teken reeks, standaard instelling is "beheerder" | Dynamisch|Beveiligings configuratie voor de bewerking van de bestands lijst voor de installatie kopie-client (intern). |
|InvokeContainerApi|teken reeks, standaard instelling is "beheerder"|Dynamisch|Container-API aanroepen |
|InvokeInfrastructureCommand |teken reeks, standaard instelling is "beheerder" |Dynamisch| Beveiligings configuratie voor beheer opdrachten voor taak infrastructuur. |
|InvokeInfrastructureQuery |teken reeks, standaard is ' \| \| gebruiker beheerder ' | Dynamisch|Beveiligings configuratie voor het uitvoeren van query's op infrastructuur taken. |
|Lijst |teken reeks, standaard is ' \| \| gebruiker beheerder ' | Dynamisch|Beveiligings configuratie voor de bewerking van de bestands lijst voor de installatie kopie van de client. |
|MoveNextFabricUpgradeDomain |teken reeks, standaard instelling is "beheerder" |Dynamisch| Beveiligings configuratie voor het hervatten van cluster upgrades met een expliciet upgrade domein. |
|MoveNextUpgradeDomain |teken reeks, standaard instelling is "beheerder" |Dynamisch| Beveiligings configuratie voor het hervatten van toepassings upgrades met een expliciet upgrade domein. |
|MoveReplicaControl |teken reeks, standaard instelling is "beheerder" | Dynamisch|Verplaats de replica. |
|NameExists |teken reeks, standaard is ' \| \| gebruiker beheerder ' | Dynamisch|Beveiligings configuratie voor het benoemen van bewaarde URI-controles. |
|NodeControl |teken reeks, standaard instelling is "beheerder" |Dynamisch| Beveiligings configuratie voor starten; stoppen en opnieuw starten van knoop punten. |
|NodeStateRemoved |teken reeks, standaard instelling is "beheerder" |Dynamisch| De beveiligings configuratie voor de status van het rapportage knooppunt is verwijderd. |
|Ping |teken reeks, standaard is ' \| \| gebruiker beheerder ' |Dynamisch| Beveiligings configuratie voor client pings. |
|PredeployPackageToNode |teken reeks, standaard instelling is "beheerder" |Dynamisch| Predeployment-API. |
|PrefixResolveService |teken reeks, standaard is ' \| \| gebruiker beheerder ' |Dynamisch| Beveiligings configuratie voor de oplossing van het service voorvoegsel op basis van een klacht. |
|PropertyReadBatch |teken reeks, standaard is ' \| \| gebruiker beheerder ' |Dynamisch| Beveiligings configuratie voor lees bewerkingen van de naamgevings eigenschap. |
|PropertyWriteBatch |teken reeks, standaard instelling is "beheerder" |Dynamisch|Beveiligings configuraties voor het benoemen van schrijf bewerkingen voor eigenschappen. |
|ProvisionApplicationType |teken reeks, standaard instelling is "beheerder" |Dynamisch| Beveiligings configuratie voor het inrichten van het toepassings type. |
|ProvisionFabric |teken reeks, standaard instelling is "beheerder" |Dynamisch| Beveiligings configuratie voor het inrichten van MSI en/of cluster manifest. |
|Query’s uitvoeren |teken reeks, standaard is ' \| \| gebruiker beheerder ' |Dynamisch| Beveiligings configuratie voor query's. |
|RecoverPartition |teken reeks, standaard instelling is "beheerder" | Dynamisch|Beveiligings configuratie voor het herstellen van een partitie. |
|RecoverPartitions |teken reeks, standaard instelling is "beheerder" | Dynamisch|Beveiligings configuratie voor het herstellen van partities. |
|RecoverServicePartitions |teken reeks, standaard instelling is "beheerder" |Dynamisch| Beveiligings configuratie voor het herstellen van service partities. |
|RecoverSystemPartitions |teken reeks, standaard instelling is "beheerder" |Dynamisch| Beveiligings configuratie voor het herstellen van systeem service partities. |
|RemoveNodeDeactivations |teken reeks, standaard instelling is "beheerder" |Dynamisch| Beveiligings configuratie voor het terugdraaien van de activering op meerdere knoop punten. |
|ReportFabricUpgradeHealth |teken reeks, standaard instelling is "beheerder" |Dynamisch| Beveiligings configuratie voor het hervatten van cluster upgrades met de huidige voortgang van de upgrade. |
|ReportFault |teken reeks, standaard instelling is "beheerder" |Dynamisch| Beveiligings configuratie voor rapportage fout. |
|ReportHealth |teken reeks, standaard instelling is "beheerder" |Dynamisch| Beveiligings configuratie voor rapportage status. |
|ReportUpgradeHealth |teken reeks, standaard instelling is "beheerder" |Dynamisch| Beveiligings configuratie voor het hervatten van toepassings upgrades met de huidige voortgang van de upgrade. |
|ResetPartitionLoad |teken reeks, standaard is ' \| \| gebruiker beheerder ' |Dynamisch| Beveiligings configuratie voor het opnieuw instellen van de belasting voor een failoverUnit. |
|ResolveNameOwner |teken reeks, standaard is ' \| \| gebruiker beheerder ' | Dynamisch|Beveiligings configuratie voor het omzetten van de naam van de naamgevings-URI-eigenaar. |
|ResolvePartition |teken reeks, standaard is ' \| \| gebruiker beheerder ' | Dynamisch|Beveiligings configuratie voor het oplossen van systeem services. |
|ResolveService |teken reeks, standaard is ' \| \| gebruiker beheerder ' |Dynamisch| Beveiligings configuratie voor service omzetting op basis van een klacht. |
|ResolveSystemService|teken reeks, standaard is ' \| \| gebruiker beheerder '|Dynamisch| Beveiligings configuratie voor het oplossen van systeem services |
|RollbackApplicationUpgrade |teken reeks, standaard instelling is "beheerder" |Dynamisch| Beveiligings configuratie voor het terugdraaien van toepassings upgrades. |
|RollbackFabricUpgrade |teken reeks, standaard instelling is "beheerder" |Dynamisch| Beveiligings configuratie voor het terugdraaien van cluster upgrades. |
|ServiceNotifications |teken reeks, standaard is ' \| \| gebruiker beheerder ' |Dynamisch| Beveiligings configuratie voor service meldingen op basis van gebeurtenissen. |
|SetUpgradeOrchestrationServiceState|teken reeks, standaard instelling is "beheerder"| Dynamisch|Induceert SetUpgradeOrchestrationServiceState op een partitie |
|StartApprovedUpgrades |teken reeks, standaard instelling is "beheerder" |Dynamisch| Induceert StartApprovedUpgrades op een partitie. |
|StartChaos |teken reeks, standaard instelling is "beheerder" |Dynamisch| Start chaos-als dit nog niet is gebeurd. |
|StartClusterConfigurationUpgrade |teken reeks, standaard instelling is "beheerder" |Dynamisch| Induceert StartClusterConfigurationUpgrade op een partitie. |
|StartInfrastructureTask |teken reeks, standaard instelling is "beheerder" | Dynamisch|Beveiligings configuratie voor het starten van infrastructuur taken. |
|StartNodeTransition |teken reeks, standaard instelling is "beheerder" |Dynamisch| Beveiligings configuratie voor het starten van een knooppunt overgang. |
|StartPartitionDataLoss |teken reeks, standaard instelling is "beheerder" |Dynamisch| Veroorzaakt gegevens verlies op een partitie. |
|StartPartitionQuorumLoss |teken reeks, standaard instelling is "beheerder" |Dynamisch| Veroorzaakt quorum verlies op een partitie. |
|StartPartitionRestart |teken reeks, standaard instelling is "beheerder" |Dynamisch| Hiermee worden enkele of alle replica's van een partitie gelijktijdig opnieuw opgestart. |
|StopChaos |teken reeks, standaard instelling is "beheerder" |Dynamisch| Stopt chaos-als deze is gestart. |
|ToggleVerboseServicePlacementHealthReporting | teken reeks, standaard is ' \| \| gebruiker beheerder ' |Dynamisch| Beveiligings configuratie voor het omschakelen van uitgebreide ServicePlacement-HealthReporting. |
|UnprovisionApplicationType |teken reeks, standaard instelling is "beheerder" |Dynamisch| Beveiligings configuratie voor het ongedaan maken van de inrichting van het toepassings type. |
|UnprovisionFabric |teken reeks, standaard instelling is "beheerder" |Dynamisch| Beveiligings configuratie voor het ongedaan maken van de inrichting van MSI en/of cluster-manifest. |
|UnreliableTransportControl |teken reeks, standaard instelling is "beheerder" |Dynamisch| Onbetrouwbaar Trans Port voor het toevoegen en verwijderen van gedrag. |
|UpdateService |teken reeks, standaard instelling is "beheerder" |Dynamisch|Beveiligings configuratie voor service-updates. |
|UpgradeApplication |teken reeks, standaard instelling is "beheerder" |Dynamisch| Beveiligings configuratie voor het starten of onderbreken van upgrades van toepassingen. |
|UpgradeComposeDeployment|teken reeks, standaard instelling is "beheerder"| Dynamisch|Hiermee wordt de opstel implementatie bijgewerkt |
|UpgradeFabric |teken reeks, standaard instelling is "beheerder" |Dynamisch| Beveiligings configuratie voor het starten van cluster upgrades. |
|Uploaden |teken reeks, standaard instelling is "beheerder" | Dynamisch|Beveiligings configuratie voor het uploaden van de installatie kopie van de client. |

## <a name="securityclientcertificateissuerstores"></a>Beveiligings-ClientCertificateIssuerStores

| **Parameter** | **Toegestane waarden** | **Upgrade beleid** | **Uitleg of korte beschrijving** |
| --- | --- | --- | --- |
|PropertyGroup|IssuerStoreKeyValueMap, standaard instelling is geen |Dynamisch|X509-verlener certificaat archieven voor client certificaten; Name = clientIssuerCN; Waarde = door komma's gescheiden lijst met winkels |

## <a name="securityclientx509names"></a>Beveiligings-ClientX509Names

| **Parameter** | **Toegestane waarden** | **Upgrade beleid** | **Uitleg of korte beschrijving** |
| --- | --- | --- | --- |
|PropertyGroup|X509NameMap, standaard instelling is geen|Dynamisch|Dit is een lijst met naam-en waarde-paar. Elk "naam" is de algemene naam van het onderwerp of DnsName van x509-certificaten die zijn gemachtigd voor client bewerkingen. Voor een gegeven ' name ' is een door komma's gescheiden lijst met certificaat vingerafdrukken voor het vastmaken van een uitgever, indien niet leeg is, moet de directe verlener van client certificaten in de lijst staan.|

## <a name="securityclustercertificateissuerstores"></a>Beveiligings-ClusterCertificateIssuerStores

| **Parameter** | **Toegestane waarden** | **Upgrade beleid** | **Uitleg of korte beschrijving** |
| --- | --- | --- | --- |
|PropertyGroup|IssuerStoreKeyValueMap, standaard instelling is geen |Dynamisch|X509-verlener certificaat archieven voor cluster certificaten; Name = clusterIssuerCN; Waarde = door komma's gescheiden lijst met winkels |

## <a name="securityclusterx509names"></a>Beveiligings-ClusterX509Names

| **Parameter** | **Toegestane waarden** | **Upgrade beleid** | **Uitleg of korte beschrijving** |
| --- | --- | --- | --- |
|PropertyGroup|X509NameMap, standaard instelling is geen|Dynamisch|Dit is een lijst met naam-en waarde-paar. Elk "naam" is de algemene naam van het onderwerp of DnsName van x509-certificaten die zijn gemachtigd voor cluster bewerkingen. Voor een gegeven ' name ' is een door komma's gescheiden lijst met certificaat vingerafdrukken voor het vastmaken van een uitgever, indien niet leeg is, moet de directe verlener van cluster certificaten in de lijst staan.|

## <a name="securityservercertificateissuerstores"></a>Beveiligings-ServerCertificateIssuerStores

| **Parameter** | **Toegestane waarden** | **Upgrade beleid** | **Uitleg of korte beschrijving** |
| --- | --- | --- | --- |
|PropertyGroup|IssuerStoreKeyValueMap, standaard instelling is geen |Dynamisch|X509-verlener certificaat archieven voor server certificaten; Name = serverIssuerCN; Waarde = door komma's gescheiden lijst met winkels |

## <a name="securityserverx509names"></a>Beveiligings-ServerX509Names

| **Parameter** | **Toegestane waarden** | **Upgrade beleid** | **Uitleg of korte beschrijving** |
| --- | --- | --- | --- |
|PropertyGroup|X509NameMap, standaard instelling is geen|Dynamisch|Dit is een lijst met naam-en waarde-paar. Elk "naam" is de algemene naam van het onderwerp of DnsName van x509-certificaten die zijn gemachtigd voor Server bewerkingen. Voor een gegeven ' name ' is een door komma's gescheiden lijst met certificaat vingerafdrukken voor het vastmaken van een uitgever, indien niet leeg is, moet de directe verlener van server certificaten in de lijst staan.|

## <a name="setup"></a>Instellen

| **Parameter** | **Toegestane waarden** | **Upgrade beleid** | **Uitleg of korte beschrijving** |
| --- | --- | --- | --- |
|ContainerNetworkName|teken reeks, standaard instelling is| Statisch |De netwerk naam die moet worden gebruikt bij het instellen van een container netwerk.|
|ContainerNetworkSetup|BOOL, default is FALSE (Linux) en de standaard waarde is TRUE (Windows)| Statisch |Hiermee wordt aangegeven of een container netwerk moet worden ingesteld.|
|FabricDataRoot |Tekenreeks | Niet toegestaan |Service Fabric data root directory. De standaard instelling voor Azure is d:\svcfab |
|FabricLogRoot |Tekenreeks | Niet toegestaan |Basismap van het service Fabric-logboek. Hier worden SF-logboeken en-traceringen geplaatst. |
|NodesToBeRemoved|teken reeks, standaard instelling is| Dynamisch |De knoop punten die moeten worden verwijderd als onderdeel van de configuratie-upgrade. (Alleen voor zelfstandige implementaties)|
|ServiceRunAsAccountName |Tekenreeks | Niet toegestaan |De account naam waaronder de Fabric-hostservice moet worden uitgevoerd. |
|SkipContainerNetworkResetOnReboot|BOOL, default is FALSE|Getalg|Hiermee wordt aangegeven of het opnieuw instellen van het container netwerk bij opnieuw opstarten moet worden overgeslagen.|
|SkipFirewallConfiguration |BOOL, default is False | Niet toegestaan |Hiermee geeft u op of firewall instellingen moeten worden ingesteld door het systeem of niet. Dit geldt alleen als u Windows Firewall gebruikt. Als u firewalls van derden gebruikt, moet u de poorten openen zodat het systeem en de toepassingen kunnen worden gebruikt |

## <a name="tokenvalidationservice"></a>TokenValidationService

| **Parameter** | **Toegestane waarden** | **Upgrade beleid** | **Uitleg of korte beschrijving** |
| --- | --- | --- | --- |
|Providers |teken reeks, standaard is ' DSTS ' |Statisch|Door komma's gescheiden lijst met te scha kelen token validatie providers (geldige providers zijn: DSTS; AAD). Op dit moment kan slechts één provider worden ingeschakeld. |

## <a name="traceetw"></a>Tracering/etw

| **Parameter** | **Toegestane waarden** | **Upgrade beleid** | **Uitleg of korte beschrijving** |
| --- | --- | --- | --- |
|Niveau |Int, standaard waarde is 4 | Dynamisch |Het traceren van het niveau etw kan waarden hebben van 1, 2, 3, 4. Om te kunnen worden ondersteund, moet u het tracerings niveau op 4 volgen |

## <a name="transactionalreplicator"></a>TransactionalReplicator

| **Parameter** | **Toegestane waarden** | **Upgrade beleid** | **Uitleg of korte beschrijving** |
| --- | --- | --- | --- |
|BatchAcknowledgementInterval | Tijd in seconden, standaard waarde is 0,015 | Statisch | Geef een tijds duur in seconden op. Bepaalt de hoeveelheid tijd die de Replicator wacht na het ontvangen van een bewerking voordat een bevestiging wordt verzonden. Andere bewerkingen die tijdens deze periode worden ontvangen, zullen hun bevestigingen terugsturen in één bericht-> het netwerk verkeer te verminderen, maar mogelijk de door Voer van de Replicator te verminderen. |
|MaxCopyQueueSize |Uint, standaard waarde is 16384 | Statisch |Dit is de maximum waarde definieert de aanvankelijke grootte voor de wachtrij die replicatie bewerkingen onderhoudt. Houd er rekening mee dat dit een macht van 2 moet zijn. Als tijdens runtime de wachtrij wordt uitgebreid naar deze grootte, wordt de bewerking tussen de primaire en secundaire replicaties vertraagd. |
|MaxPrimaryReplicationQueueMemorySize |Uint, standaard waarde is 0 | Statisch |Dit is de maximum waarde van de primaire replicatie wachtrij in bytes. |
|MaxPrimaryReplicationQueueSize |Uint, standaard waarde is 8192 | Statisch |Dit is het maximum aantal bewerkingen dat kan voor komen in de primaire replicatie wachtrij. Houd er rekening mee dat dit een macht van 2 moet zijn. |
|MaxReplicationMessageSize |Uint, standaard waarde is 52428800 | Statisch | De maximale bericht grootte van de replicatie bewerkingen. De standaard waarde is 50 MB. |
|MaxSecondaryReplicationQueueMemorySize |Uint, standaard waarde is 0 | Statisch |Dit is de maximum waarde van de secundaire replicatie wachtrij in bytes. |
|MaxSecondaryReplicationQueueSize |Uint, standaard waarde is 16384 | Statisch |Dit is het maximum aantal bewerkingen dat kan voor komen in de secundaire replicatie wachtrij. Houd er rekening mee dat dit een macht van 2 moet zijn. |
|ReplicatorAddress |teken reeks, standaard waarde is ' localhost: 0 ' | Statisch | Het eind punt in de vorm van een teken reeks-' IP: poort ' die wordt gebruikt door de Windows Fabric Replicator om verbindingen met andere replica's tot stand te brengen om bewerkingen te kunnen verzenden/ontvangen. |

## <a name="transport"></a>Transport
| **Parameter** | **Toegestane waarden** |**Upgrade beleid** |**Uitleg of korte beschrijving** |
| --- | --- | --- | --- |
|ConnectionOpenTimeout|Time span, standaard waarde is gebruikelijk:: time span:: FromSeconds (60)|Statisch|Geef een tijds duur in seconden op. Er is een time-out opgetreden voor de configuratie van de verbinding voor zowel binnenkomend als geaccepteerde kant (inclusief beveiligings onderhandeling in beveiligde modus) |
|FrameHeaderErrorCheckingEnabled|BOOL, default is TRUE|Statisch|Standaard instelling voor fout controle op frame header in niet-beveiligde modus; de instelling van het onderdeel overschrijft dit. |
|MessageErrorCheckingEnabled|BOOL, default is FALSE|Statisch|Standaard instelling voor fout controle op bericht header en hoofd tekst in niet-beveiligde modus; de instelling van het onderdeel overschrijft dit. |
|ResolveOption|teken reeks, standaard instelling is "niet opgegeven"|Statisch|Hiermee wordt bepaald hoe FQDN wordt opgelost.  Geldige waarden zijn ' niet opgegeven/IPv4/IPv6 '. |
|SendTimeout|Time span, standaard waarde is gebruikelijk:: time span:: FromSeconds (300)|Dynamisch|Geef een tijds duur in seconden op. Verzend time-out voor het detecteren van een vastgelopen verbinding. TCP-fouten rapporten zijn niet betrouwbaar in sommige omgevingen. Dit moet mogelijk worden aangepast op basis van de beschik bare netwerk bandbreedte en grootte van uitgaande gegevens ( \* MaxMessageSize \/ \* SendQueueSizeLimit). |

## <a name="upgradeorchestrationservice"></a>UpgradeOrchestrationService

| **Parameter** | **Toegestane waarden** | **Upgrade beleid** | **Uitleg of korte beschrijving** |
| --- | --- | --- | --- |
|AutoupgradeEnabled | BOOL, default is True |Statisch| Automatische polling en bijwerk actie op basis van een doel status bestand. |
|AutoupgradeInstallEnabled|BOOL, default is FALSE|Statisch|Automatische polling, inrichting en installatie van code-upgrade actie op basis van een bestand met een doel status.|
|GoalStateExpirationReminderInDays|int, standaard waarde is 30|Statisch|Hiermee stelt u het aantal resterende dagen in waarna de herinnering voor de doel status moet worden weer gegeven.|
|MinReplicaSetSize |Int, standaard is 0 |Statisch |De MinReplicaSetSize voor UpgradeOrchestrationService.|
|PlacementConstraints | teken reeks, standaard instelling is |Statisch| De PlacementConstraints voor UpgradeOrchestrationService. |
|QuorumLossWaitDuration | Tijd in seconden, de standaard waarde is MaxValue |Statisch| Geef een tijds duur in seconden op. De QuorumLossWaitDuration voor UpgradeOrchestrationService. |
|ReplicaRestartWaitDuration | Tijd in seconden, standaard waarde is 60 minuten|Statisch| Geef een tijds duur in seconden op. De ReplicaRestartWaitDuration voor UpgradeOrchestrationService. |
|StandByReplicaKeepDuration | Tijd in seconden, de standaard waarde is 60 *24* 7 minuten |Statisch| Geef een tijds duur in seconden op. De StandByReplicaKeepDuration voor UpgradeOrchestrationService. |
|TargetReplicaSetSize |Int, standaard is 0 |Statisch |De TargetReplicaSetSize voor UpgradeOrchestrationService. |
|UpgradeApprovalRequired | BOOL, default is False | Statisch|Instelling voor het maken van een code-upgrade vereist goed keuring van de beheerder voordat u doorgaat. |

## <a name="upgradeservice"></a>UpgradeService

| **Parameter** | **Toegestane waarden** | **Upgrade beleid** | **Uitleg of korte beschrijving** |
| --- | --- | --- | --- |
|BaseUrl | teken reeks, standaard instelling is |Statisch|BaseUrl voor UpgradeService. |
|ClusterId | teken reeks, standaard instelling is |Statisch|ClusterId voor UpgradeService. |
|CoordinatorType | teken reeks, standaard is ' WUTest '|Niet toegestaan|De CoordinatorType voor UpgradeService. |
|MinReplicaSetSize | Int, standaard waarde is 2 |Niet toegestaan| De MinReplicaSetSize voor UpgradeService. |
|OnlyBaseUpgrade | BOOL, default is False |Dynamisch|OnlyBaseUpgrade voor UpgradeService. |
|PlacementConstraints |teken reeks, standaard instelling is |Niet toegestaan|De PlacementConstraints voor de upgrade service. |
|PollIntervalInSeconds|Time span, standaard waarde is gebruikelijk:: time span:: FromSeconds (60) |Dynamisch|Geef een tijds duur in seconden op. Het interval tussen UpgradeService polling voor ARM-beheer bewerkingen. |
|TargetReplicaSetSize | Int, standaard is 3 |Niet toegestaan| De TargetReplicaSetSize voor UpgradeService. |
|TestCabFolder | teken reeks, standaard instelling is |Statisch| TestCabFolder voor UpgradeService. |
|X509FindType | teken reeks, standaard instelling is|Dynamisch| X509FindType voor UpgradeService. |
|X509FindValue | teken reeks, standaard instelling is |Dynamisch| X509FindValue voor UpgradeService. |
|X509SecondaryFindValue | teken reeks, standaard instelling is |Dynamisch| X509SecondaryFindValue voor UpgradeService. |
|X509StoreLocation | teken reeks, standaard instelling is |Dynamisch| X509StoreLocation voor UpgradeService. |
|X509StoreName | teken reeks, standaard is ' My '|Dynamisch|X509StoreName voor UpgradeService. |

## <a name="userservicemetriccapacities"></a>UserServiceMetricCapacities
| **Parameter** | **Toegestane waarden** | **Upgrade beleid** | **Uitleg of korte beschrijving** |
| --- | --- | --- | --- |
|PropertyGroup| UserServiceMetricCapacitiesMap, standaard instelling is geen | Statisch | Een verzameling van de beheer limieten voor gebruikers services-resources moet statisch zijn omdat dit van invloed is op de logica van automatische detectie |

## <a name="next-steps"></a>Volgende stappen
Zie [de configuratie van een Azure-cluster upgraden](service-fabric-cluster-config-upgrade-azure.md) en [de configuratie van een zelfstandig cluster upgraden](service-fabric-cluster-config-upgrade-windows-server.md)voor meer informatie.
