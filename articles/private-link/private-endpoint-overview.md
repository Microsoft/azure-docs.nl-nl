---
title: Wat is een Azure-privé-eindpunt?
description: Meer informatie over azure-privé-eindpunt
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: conceptual
ms.date: 06/18/2020
ms.author: allensu
ms.openlocfilehash: a12f0c2e8ff5987a14b56ef12d49b8350cc1b3aa
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107501770"
---
# <a name="what-is-azure-private-endpoint"></a>Wat is een privé-eindpunt van Azure?

Een privé-eindpunt in Azure is een netwerkinterface waarmee u privé en veilig verbinding maakt met een service die door Azure Private Link mogelijk wordt gemaakt. Private Endpoint maakt gebruik van een privé-IP-adres van uw VNet, waarbij de service effectief in uw VNet wordt geplaatst. De service kan een Azure-service zijn, zoals Azure Storage, Azure Cosmos DB, SQL, enzovoort, of uw [eigen Private Link Service.](private-link-service-overview.md)
  
## <a name="private-endpoint-properties"></a>Eigenschappen van privé-eindpunten 
 Een privé-eindpunt geeft de volgende eigenschappen op: 


|Eigenschap  |Beschrijving |
|---------|---------|
|Name    |    Een unieke naam binnen de resourcegroep.      |
|Subnet    |  Het subnet voor het implementeren en toewijzen van privé-IP-adressen vanuit een virtueel netwerk. Zie de sectie Beperkingen in dit artikel voor vereisten voor subnetten.         |
|Private Link Resource    |   De private link-resource om verbinding te maken met behulp van de resource-id of alias, uit de lijst met beschikbare typen. Er wordt een unieke netwerk-id gegenereerd voor al het verkeer dat naar deze resource wordt verzonden.       |
|Subresource van doel   |      De subresource om verbinding te maken. Elk resourcetype voor een privékoppeling heeft verschillende opties om te selecteren op basis van voorkeur.    |
|Methode voor verbindingsgoedkeuring    |  Automatisch of handmatig. Op basis van machtigingen voor op rollen gebaseerd toegangsbeheer van Azure (Azure RBAC) kan uw privé-eindpunt automatisch worden goedgekeurd. Als u verbinding probeert te maken met een Private Link-resource zonder Azure RBAC, gebruikt u de handmatige methode om de eigenaar van de resource toe te staan de verbinding goed te keuren.        |
|Aanvraagbericht     |  U kunt een bericht opgeven voor aangevraagde verbindingen die handmatig moeten worden goedgekeurd. Dit bericht kan worden gebruikt om een specifieke aanvraag te identificeren.        |
|Verbindingsstatus   |   Een alleen-lezen eigenschap die aangeeft of het privé-eindpunt actief is. Alleen privé-eindpunten met een goedgekeurde status kunnen worden gebruikt om verkeer te verzenden. Aanvullende beschikbare staten: <br>-**Goedgekeurd:** de verbinding is automatisch of handmatig goedgekeurd en kan worden gebruikt.</br><br>-**In** behandeling: de verbinding is handmatig gemaakt en is in afwachting van goedkeuring door de resource-eigenaar van de Private Link.</br><br>-**Geweigerd:** de verbinding is geweigerd door de resource-eigenaar van de Private Link.</br><br>-**Verbinding verbroken:** de verbinding is verwijderd door de resource-eigenaar van de Private Link. Het privé-eindpunt wordt informatief en moet worden verwijderd voor opschoning. </br>|

Hier zijn enkele belangrijke details over privé-eindpunten: 
- Privé-eindpunt maakt connectiviteit mogelijk tussen de consumenten van hetzelfde VNet, regionaal peered VNets, wereldwijd peered VNets en on-premises met behulp van [VPN](https://azure.microsoft.com/services/vpn-gateway/) of [Express Route](https://azure.microsoft.com/services/expressroute/) en services powered by Private Link.
 
- Netwerkverbindingen kunnen alleen worden geïnitieerd door clients die verbinding maken met het privé-eindpunt. Serviceproviders hebben geen routeringsconfiguratie om verbindingen met serviceverbruikers te initiëren. Verbindingen kunnen slechts in één richting tot stand worden brengen.

- Bij het maken van een privé-eindpunt wordt ook een alleen-lezen netwerkinterface gemaakt voor de levenscyclus van de resource. Aan de interface worden dynamisch privé-IP-adressen toegewezen vanuit het subnet dat is toegewezen aan de Private Link-resource. De waarde van het privé-IP-adres blijft ongewijzigd voor de hele levenscyclus van het privé-eindpunt.
 
- Het privé-eindpunt moet worden geïmplementeerd in dezelfde regio en hetzelfde abonnement als het virtuele netwerk. 
 
- De private link-resource kan worden geïmplementeerd in een andere regio dan het virtuele netwerk en het privé-eindpunt.
 
- Er kunnen meerdere privé-eindpunten worden gemaakt met behulp van dezelfde Private Link-resource. Voor één netwerk dat gebruik maakt van een algemene DNS-serverconfiguratie, wordt aanbevolen één privé-eindpunt te gebruiken voor een bepaalde privékoppelingsresource om dubbele vermeldingen of conflicten in DNS-resolutie te voorkomen. 
 
- Er kunnen meerdere privé-eindpunten worden gemaakt in dezelfde of verschillende subnetten binnen hetzelfde virtuele netwerk. Er gelden limieten voor het aantal privé-eindpunten dat u in een abonnement kunt maken. Zie Azure-limieten [voor meer informatie.](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits)

- Het abonnement van de Private Link-resource moet ook worden geregistreerd bij de resourceprovider Micosoft.Network. Zie Azure Resource [Providers voor meer informatie.](../azure-resource-manager/management/resource-providers-and-types.md)

 
## <a name="private-link-resource"></a>Private Link-resource 
Een private link-resource is het doel van een bepaald privé-eindpunt. Hier volgt een lijst met beschikbare private link-resourcetypen: 
 
|Resourcenaam private link  |Resourcetype   |Subresources  |
|---------|---------|---------|
|**Private Link Service** (uw eigen service)   |  Microsoft.Network/privateLinkServices       | leeg |
|**Azure Automation** |  Microsoft.Automation/automationAccounts | Webhook, DSCAndHybridWorker |
|**Azure SQL Database** | Microsoft.Sql/servers    |  SQL Server (sqlServer)        |
|**Azure Synapse Analytics** | Microsoft.Sql/servers    |  SQL Server (sqlServer)        | 
|**Azure Storage**  | Microsoft.Storage/storageAccounts    |  Blob (blob, blob_secondary)<BR> Tabel (tabel, table_secondary)<BR> Wachtrij (wachtrij, queue_secondary)<BR> Bestand (bestand, file_secondary)<BR> Web (web, web_secondary)        |
|**Azure Data Lake Storage Gen2**  | Microsoft.Storage/storageAccounts    |  Blob (blob, blob_secondary)<BR> Data Lake File System Gen2 (dfs, dfs_secondary)       |
|**Azure Cosmos DB** | Microsoft.AzureCosmosDB/databaseAccounts    | Sql, MongoDB, Cassandra, Gremlin, Table|
|**Azure Database for PostgreSQL -Enkele server** | Microsoft.DBforPostgreSQL/servers    | postgresqlServer |
|**Azure Database for MySQL** | Microsoft.DBforMySQL/servers    | mysqlServer |
|**Azure Database for MariaDB** | Microsoft.DBforMariaDB/servers    | mariadbServer |
|**Azure IoT Hub** | Microsoft.Devices/IotHubs    | iotHub |
|**Azure Key Vault** | Microsoft.KeyVault/vaults    | kluis |
|**Azure Kubernetes Service - Kubernetes API** | Microsoft.ContainerService/managedClusters    | beheer |
|**Azure Search** | Microsoft.Search/searchService| searchService|  
|**Azure Container Registry** | Microsoft.ContainerRegistry/registries    | registry |
|**Azure App Configuration** | Microsoft.Appconfiguration/configurationStores    | configurationStores |
|**Azure Backup** | Microsoft.RecoveryServices/vaults    | kluis |
|**Azure Event Hub** | Microsoft.EventHub/namespaces    | naamruimte |
|**Azure Service Bus** | Microsoft.ServiceBus/namespaces | naamruimte |
|**Azure Relay** | Microsoft.Relay/naamruimten | naamruimte |
|**Azure Event Grid** | Microsoft.EventGrid/topics    | onderwerp |
|**Azure Event Grid** | Microsoft.EventGrid/domains    | domein |
|**Azure App Service** | Microsoft.Web/sites    | sites |
|**Azure App Service sleuven** | Microsoft.Web/sites    | sites-`<slot name>` |
|**Azure Machine Learning** | Microsoft.MachineLearningServices/workspaces    | amlworkspace |
|**SignalR** | Microsoft.SignalRService/SignalR    | signalR |
|**Azure Monitor** | Microsoft.Insights/privateLinkScopes    | azuremonitor |
|**Cognitive Services** | (Microsoft.CognitiveServices/accounts    | account |
|**Azure File Sync** | Microsoft.StorageSync/storageSyncServices    | Afs |
    
  

  
 
## <a name="network-security-of-private-endpoints"></a>Netwerkbeveiliging van privé-eindpunten 
Wanneer u privé-eindpunten gebruikt voor Azure-services, wordt verkeer naar een specifieke Private Link-resource beveiligd. Het platform voert een toegangscontrole uit om netwerkverbindingen te valideren die alleen de opgegeven persoonlijke koppelingsresource bereiken. Voor toegang tot aanvullende resources binnen dezelfde Azure-service zijn extra privé-eindpunten vereist. 
 
U kunt uw workloads volledig vergrendelen voor toegang tot openbare eindpunten om verbinding te maken met een ondersteunde Azure-service. Dit besturingselement biedt een extra netwerkbeveiligingslaag aan uw resources door een ingebouwde exfiltration-beveiliging te bieden. Hiermee wordt toegang voorkomen tot andere resources die worden gehost op dezelfde Azure-service. 
 
## <a name="access-to-a-private-link-resource-using-approval-workflow"></a>Toegang tot een Private Link-resource met behulp van een goedkeuringswerkstroom 
U kunt verbinding maken met een Private Link-resource met behulp van de volgende methoden voor verbindingsgoedkeuring:
- **Automatisch goedgekeurd wanneer** u de eigenaar bent van of machtigingen hebt voor de specifieke Private Link-resource. De vereiste machtiging is gebaseerd op het resourcetype private link in de volgende indeling: Microsoft. \<Provider> /<resource_type>/privateEndpointConnectionApproval/action
- **Handmatige** aanvraag wanneer u niet de vereiste machtiging hebt en toegang wilt aanvragen. Er wordt een goedkeuringswerkstroom gestart. Het privé-eindpunt en de erop volgende verbinding met het privé-eindpunt worden in de status In behandeling gemaakt. De eigenaar van de Private Link-resource moet de verbinding goedkeuren. Nadat het is goedgekeurd, wordt het privé-eindpunt ingeschakeld voor het normaal verzenden van verkeer, zoals wordt weergegeven in het volgende goedkeuringswerkstroomdiagram.  

![goedkeuring van werkstroom](media/private-endpoint-overview/private-link-paas-workflow.png)
 
De eigenaar van de Private Link-resource kan de volgende acties uitvoeren via een verbinding met een privé-eindpunt: 
- Controleer alle details van privé-eindpuntverbindingen. 
- Een verbinding met een privé-eindpunt goedkeuren. Het bijbehorende privé-eindpunt wordt ingeschakeld voor het verzenden van verkeer naar de Private Link-resource. 
- Een verbinding met een privé-eindpunt weigeren. Het bijbehorende privé-eindpunt wordt bijgewerkt om de status weer te geven.
- Verwijder een verbinding met een privé-eindpunt in een staat. Het bijbehorende privé-eindpunt wordt bijgewerkt met de status Niet-verbonden om de actie weer te geven. De eigenaar van het privé-eindpunt kan de resource op dit moment alleen verwijderen. 
 
> [!NOTE]
> Alleen een privé-eindpunt met een goedgekeurde status kan verkeer verzenden naar een bepaalde Private Link-resource. 

### <a name="connecting-using-alias"></a>Verbinding maken met behulp van alias
Alias is een unieke moniker die wordt gegenereerd wanneer de service-eigenaar de private link-service achter een standaardservice load balancer. De service-eigenaar kan deze alias offline delen met de gebruikers. Consumenten kunnen een verbinding met de Private Link-service aanvragen met behulp van de resource-URI of de Alias. Als u verbinding wilt maken met alias, moet u een privé-eindpunt maken met behulp van een handmatige goedkeuringsmethode voor de verbinding. Als u de handmatige goedkeuringsmethode voor de verbinding wilt gebruiken, stelt u de handmatige aanvraagparameter in op true tijdens het maken van de stroom voor het privé-eindpunt. Bekijk [New-AzPrivateEndpoint](/powershell/module/az.network/new-azprivateendpoint) en [az network private-endpoint create voor](/cli/azure/network/private-endpoint#az-network-private-endpoint-create) meer informatie. 

## <a name="dns-configuration"></a>DNS-configuratie 
Wanneer u verbinding maakt met een Private Link-resource met behulp van een FQDN (Fully Qualified Domain Name) als onderdeel van de connection string, is het belangrijk dat u uw DNS-instellingen correct configureert om om te gaan met het toegewezen privé-IP-adres. Bestaande Azure-services hebben mogelijk al een DNS-configuratie om te gebruiken bij het maken van verbinding via een openbaar eindpunt. Dit moet worden overschrijven om verbinding te maken met behulp van uw privé-eindpunt. 
 
De netwerkinterface die is gekoppeld aan het privé-eindpunt bevat de volledige set informatie die nodig is om uw DNS te configureren, inclusief FQDN en privé-IP-adressen die zijn toegewezen voor een bepaalde private link-resource. 

Raadpleeg het artikel Privé-eindpunt-DNS-configuratie voor gedetailleerde informatie over best practices en aanbevelingen voor het configureren van DNS voor [privé-eindpunten.](private-endpoint-dns.md)



 
## <a name="limitations"></a>Beperkingen
 
De volgende tabel bevat een lijst met bekende beperkingen bij het gebruik van privé-eindpunten: 


|Beperking |Beschrijving |Oplossing  |
|---------|---------|---------|
|Regels voor netwerkbeveiligingsgroep (NSG's) en door de gebruiker gedefinieerde routes zijn niet van toepassing op privé-eindpunten    |NSG wordt niet ondersteund op privé-eindpunten. Hoewel aan subnetten met het privé-eindpunt NSG kan worden gekoppeld, zijn de regels niet effectief voor verkeer dat wordt verwerkt door het privé-eindpunt. Het afdwingen [van netwerkbeleid moet zijn uitgeschakeld](disable-private-endpoint-network-policy.md) om privé-eindpunten in een subnet te implementeren. NSG wordt nog steeds afgedwongen op andere workloads die worden gehost op hetzelfde subnet. Routes op elk clientsubnet gebruiken een /32-voorvoegsel. Voor het wijzigen van het standaardgedrag voor routering is een vergelijkbare UDR vereist  | Beheer het verkeer met behulp van NSG-regels voor uitgaand verkeer op bron-clients. Implementeer afzonderlijke routes met /32-voorvoegsel om privé-eindpuntroutes te overschrijven. NSG-stroomlogboeken en bewakingsgegevens voor uitgaande verbindingen worden nog steeds ondersteund en kunnen worden gebruikt        |


## <a name="next-steps"></a>Volgende stappen
- [Een privé-eindpunt maken voor SQL Database met behulp van de portal](create-private-endpoint-portal.md)
- [Een privé-eindpunt maken voor SQL Database met behulp van PowerShell](create-private-endpoint-powershell.md)
- [Een privé-eindpunt maken voor SQL Database cli](create-private-endpoint-cli.md)
- [Een privé-eindpunt voor een opslagaccount maken met behulp van de portal](./tutorial-private-endpoint-storage-portal.md)
- [Een privé-eindpunt voor een Azure Cosmos-account maken met behulp van de portal](../cosmos-db/how-to-configure-private-endpoints.md)
- [Uw eigen service Private Link maken met Azure PowerShell](create-private-link-service-powershell.md)
- [Uw eigen Private Link voor Azure Database for PostgreSQL - Enkele server met behulp van de portal](../postgresql/howto-configure-privatelink-portal.md)
- [Uw eigen Private Link voor Azure Database for PostgreSQL - Individuele server met cli](../postgresql/howto-configure-privatelink-cli.md)
- [Uw eigen Private Link voor Azure Database for MySQL maken met behulp van de portal](../mysql/howto-configure-privatelink-portal.md)
- [Uw eigen Private Link voor Azure Database for MySQL cli](../mysql/howto-configure-privatelink-cli.md)
- [Uw eigen Private Link voor Azure Database for MariaDB maken met behulp van de portal](../mariadb/howto-configure-privatelink-portal.md)
- [Uw eigen Private Link voor Azure Database for MariaDB cli](../mariadb/howto-configure-privatelink-cli.md)
- [Uw eigen Private Link voor Azure Key Vault met behulp van de portal en CLI](../key-vault/general/private-link-service.md)
