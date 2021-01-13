---
title: DNS-configuratie van Azure-privé-eindpunt
description: Meer informatie over de DNS-configuratie voor het persoonlijke eind punt
services: private-link
author: mblanco77
ms.service: private-link
ms.topic: conceptual
ms.date: 01/12/2021
ms.author: allensu
ms.openlocfilehash: 859768345c2b88e38e09d897391ac8a3501fd901
ms.sourcegitcommit: 431bf5709b433bb12ab1f2e591f1f61f6d87f66c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/12/2021
ms.locfileid: "98134072"
---
# <a name="azure-private-endpoint-dns-configuration"></a>DNS-configuratie van Azure-privé-eindpunt


Wanneer u verbinding maakt met een persoonlijke koppelings bron met behulp van een Fully Qualified Domain Name (FQDN) als onderdeel van de connection string, is het belang rijk om uw DNS-instellingen correct te configureren om te worden omgezet in het toegewezen privé-IP-adres. Bestaande Microsoft Azure Services hebben mogelijk al een DNS-configuratie die kan worden gebruikt om verbinding te maken via een openbaar eind punt. Deze configuratie moet worden overschreven om verbinding te maken via uw persoonlijke eind punt. 
 
De netwerk interface die aan het persoonlijke eind punt is gekoppeld, bevat de volledige set informatie die nodig is voor het configureren van uw DNS, inclusief FQDN-en privé IP-adressen die zijn toegewezen voor een bepaalde persoonlijke koppelings bron. 
 
U kunt de volgende opties gebruiken om uw DNS-instellingen voor privé-eind punten te configureren: 
- **Het hostbestand gebruiken (alleen aanbevolen voor testen)**. U kunt het hostbestand op een virtuele machine gebruiken om de DNS te vervangen.  
- **Gebruik een privé-DNS-zone**. U kunt [privé-DNS-zones](../dns/private-dns-privatednszone.md) gebruiken om de DNS-omzetting voor een bepaald persoonlijk eind punt te onderdrukken. Een persoonlijke DNS-zone kan worden gekoppeld aan uw virtuele netwerk om specifieke domeinen op te lossen.
- **Gebruik uw DNS-doorstuur server (optioneel)**. U kunt de DNS-doorstuur server gebruiken om de DNS-omzetting voor een bepaalde persoonlijke koppelings bron te negeren. Als uw [DNS-server](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server) wordt gehost op een virtueel netwerk, kunt u een DNS-doorstuur regel maken voor het gebruik van een privé-DNS-zone om de configuratie voor alle persoonlijke koppelings bronnen te vereenvoudigen.

> [!IMPORTANT]
> Wordt niet aanbevolen een zone te overschrijven die actief in gebruik is om open bare eind punten op te lossen. Verbindingen met bronnen kunnen niet correct worden omgezet zonder dat DNS naar de open bare DNS doorstuurt. Als u problemen wilt voor komen, maakt u een andere domein naam of volgt u de voorgestelde naam voor elke service hieronder. 



## <a name="azure-services-dns-zone-configuration"></a>Configuratie van DNS-zone voor Azure-Services
Azure-Services maken een canonieke naam DNS-record (CNAME) op de open bare DNS-service om de oplossing om te leiden naar de voorgestelde persoonlijke domein naam. U kunt de oplossing overschrijven met het privé-IP-adres van uw privé-eind punten. 
 
Uw toepassingen hoeven de verbindings-URL niet te wijzigen. Bij het oplossen van een open bare DNS-service, wordt de DNS-server nu omgezet naar uw privé-eind punten. Het proces heeft geen invloed op uw bestaande toepassingen. 

> [!IMPORTANT]
> Particuliere netwerken die al gebruikmaken van de privé-DNS-zone voor een bepaald type, kunnen alleen verbinding maken met open bare resources als ze geen privé-eindpunt verbindingen hebben, anders is een bijbehorende DNS-configuratie vereist in de privé-DNS-zone om de DNS-omzettings reeks te volt ooien. 

Voor Azure-Services gebruikt u de aanbevolen zone namen zoals beschreven in de volgende tabel:

| Resource type voor persoonlijke koppelingen/subresource |Privé-DNS zone naam | Doorstuur servers voor open bare DNS-zone |
|---|---|---|
| Azure Automation/(micro soft. Automation/automationAccounts)/webhook, DSCAndHybridWorker | privatelink.azure-automation.net | azure-automation.net |
| Azure SQL Database (micro soft. SQL/servers)/SQL Server | privatelink.database.windows.net | database.windows.net |
| Azure Synapse Analytics (micro soft. SQL/servers)/SQL Server  | privatelink.database.windows.net | database.windows.net |
| Opslag account (micro soft. Storage/Storage accounts)/BLOB (BLOB, blob_secondary) | privatelink.blob.core.windows.net | blob.core.windows.net |
| Opslag account (micro soft. Storage/Storage accounts)/tabel (tabel, table_secondary) | privatelink.table.core.windows.net | table.core.windows.net |
| Opslag account (micro soft. Storage/Storage accounts)/wachtrij (wachtrij, queue_secondary) | privatelink.queue.core.windows.net | queue.core.windows.net |
| Opslag account (micro soft. Storage/Storage accounts)/bestand (bestand, file_secondary) | privatelink.file.core.windows.net | file.core.windows.net |
| Opslag account (micro soft. Storage/Storage accounts)/Web (Web, web_secondary) | privatelink.web.core.windows.net | web.core.windows.net |
| Azure Data Lake File System Gen2 (micro soft. Storage/Storage accounts)/Data Lake File System Gen2 (DFS, dfs_secondary) | privatelink.dfs.core.windows.net | dfs.core.windows.net |
| Azure Cosmos DB (micro soft. AzureCosmosDB/databaseAccounts)/SQL | privatelink.documents.azure.com | documents.azure.com |
| Azure Cosmos DB (micro soft. AzureCosmosDB/databaseAccounts)/MongoDB | privatelink.mongo.cosmos.azure.com | mongo.cosmos.azure.com |
| Azure Cosmos DB (micro soft. AzureCosmosDB/databaseAccounts)/Cassandra | privatelink.cassandra.cosmos.azure.com | cassandra.cosmos.azure.com |
| Azure Cosmos DB (micro soft. AzureCosmosDB/databaseAccounts)/Gremlin | privatelink.gremlin.cosmos.azure.com | gremlin.cosmos.azure.com |
| Azure Cosmos DB (micro soft. AzureCosmosDB/databaseAccounts)/Table | privatelink.table.cosmos.azure.com | table.cosmos.azure.com |
| Azure Database for PostgreSQL-één server (micro soft. DBforPostgreSQL/servers)/postgresqlServer | privatelink.postgres.database.azure.com | postgres.database.azure.com |
| Azure Database for MySQL (micro soft. DBforMySQL/servers)/mysqlServer | privatelink.mysql.database.azure.com | mysql.database.azure.com |
| Azure Database for MariaDB (micro soft. DBforMariaDB/servers)/mariadbServer | privatelink.mariadb.database.azure.com | mariadb.database.azure.com |
| Azure Key Vault (micro soft. de sleutel kluis/kluizen)/kluis | privatelink.vaultcore.azure.net | vault.azure.net <br> vaultcore.azure.net |
| Azure Kubernetes service-Kubernetes-API (micro soft. container service/managedClusters)/beheer | privatelink. {Region}. azmk8s. io | {Region}. azmk8s. io |
| Azure Search (micro soft. Search/searchServices)/searchService | privatelink.search.windows.net | search.windows.net |
| Azure Container Registry (micro soft. ContainerRegistry/registers)/REGI ster | privatelink.azurecr.io | azurecr.io |
| Azure-app configuratie (micro soft. AppConfiguration/configurationStores)/configurationStore | privatelink.azconfig.io | azconfig.io |
| Azure Backup (micro soft. Recovery Services/kluizen)/kluis | privatelink. {Region}. backup. windowsazure. com | {Region}. backup. windowsazure. com |
| Azure Event Hubs (micro soft. EventHub/naam ruimte)/naam ruimte | privatelink.servicebus.windows.net | servicebus.windows.net |
| Azure Service Bus (micro soft. ServiceBus/namespaces)/naam ruimte | privatelink.servicebus.windows.net | servicebus.windows.net |
| Azure IoT Hub (micro soft. devices/IotHubs)/iotHub | privatelink.azure-devices.net<br/>privatelink.servicebus.windows.net<sup>1</sup> | azure-devices.net<br/>servicebus.windows.net |
| Azure Relay (micro soft. relay/naam ruimten)/naam ruimte | privatelink.servicebus.windows.net | servicebus.windows.net |
| Azure Event Grid (micro soft. EventGrid/topics)/topic | privatelink.eventgrid.azure.net | eventgrid.azure.net |
| Azure Event Grid (micro soft. EventGrid/domeinen)/domein | privatelink.eventgrid.azure.net | eventgrid.azure.net |
| Azure-Web Apps (micro soft. web/sites)/sites | privatelink.azurewebsites.net | azurewebsites.net |
| Azure Machine Learning (micro soft. MachineLearningServices/werk ruimten)/werk ruimte | privatelink.api.azureml.ms | api.azureml.ms |
| IoT Hub (micro soft. devices/IotHubs)/IotHub | privatelink.azure-devices.net | azure-devices.net |
| Signa lering (micro soft. SignalRService/Signalr)/signaal sterkte | privatelink.service.signalr.net | service.signalr.net |
| Azure Monitor (micro soft. Insights/privateLinkScopes)/azuremonitor | privatelink.monitor.azure.com<br/> privatelink.oms.opinsights.azure.com <br/> privatelink.ods.opinsights.azure.com <br/> privatelink.agentsvc.azure-automation.net | monitor.azure.com<br/> oms.opinsights.azure.com<br/> ods.opinsights.azure.com<br/> agentsvc.azure-automation.net |
| Cognitive Services (micro soft. CognitiveServices/accounts)/account | privatelink.cognitiveservices.azure.com  | cognitiveservices.azure.com  |
| Azure File Sync (micro soft. StorageSync/storageSyncServices)/AFS |  privatelink.afs.azure.net  |  afs.azure.net  |
| Azure Data Factory (micro soft. DataFactory/fabrieken)/dataFactory |  privatelink.datafactory.azure.net  |  datafactory.azure.net  |
| Azure Data Factory (micro soft. DataFactory/fabrieken)/Portal |  privatelink.azure.com  |  azure.com  |
| Azure-cache voor redis (micro soft. cache/redis)/redisCache | privatelink.redis.cache.windows.net | redis.cache.windows.net |

<sup>1</sup> Voor gebruik met de ingebouwde Event hub-compatibel eind punt van IoT Hub. Zie [ondersteuning voor persoonlijke koppelingen voor het ingebouwde eind punt van IOT hub voor](../iot-hub/virtual-network-support.md#built-in-event-hub-compatible-endpoint) meer informatie
 
## <a name="dns-configuration-scenarios"></a>DNS-configuratie scenario's

De FQDN-namen van de services worden automatisch omgezet naar een openbaar IP-adres. Als u wilt omzetten naar het privé-IP-adres van het privé-eind punt, moet u de DNS-configuratie dienovereenkomstig wijzigen.

DNS is een essentieel onderdeel om de toepassing correct te laten werken door het IP-adres van het privé-eind punt op te lossen.

Op basis van uw voor keuren zijn de volgende scenario's beschikbaar met geïntegreerde DNS-resolutie:

- [Virtuele netwerk werkbelastingen zonder aangepaste DNS-server](#virtual-network-workloads-without-custom-dns-server)
- [On-premises workloads met behulp van een DNS-doorstuur server](#on-premises-workloads-using-a-dns-forwarder)
- [Virtuele netwerk-en on-premises workloads met behulp van een DNS-doorstuur server](#virtual-network-and-on-premises-workloads-using-a-dns-forwarder)

> [!NOTE]
> [Azure firewall DNS-proxy](../firewall/dns-settings.md#dns-proxy) kan worden gebruikt als DNS-doorstuur server voor [on-premises workloads](#on-premises-workloads-using-a-dns-forwarder) en [virtuele-netwerk werkbelastingen met behulp van een DNS-doorstuur server](#virtual-network-and-on-premises-workloads-using-a-dns-forwarder).

## <a name="virtual-network-workloads-without-custom-dns-server"></a>Virtuele netwerk werkbelastingen zonder aangepaste DNS-server

Deze configuratie is geschikt voor werk belastingen voor virtuele netwerken zonder aangepaste DNS-server. In dit scenario zoekt de client naar het IP-adres van het privé-eind punt naar de door Azure verschafte DNS-service [168.63.129.16](../virtual-network/what-is-ip-address-168-63-129-16.md). Azure DNS is verantwoordelijk voor de DNS-omzetting van de privé-DNS-zones.

> [!NOTE]
> In dit scenario wordt de Azure SQL Database-aanbevolen privé-DNS-zone gebruikt. Voor andere services kunt u het model aanpassen met behulp van de volgende referentie: [Azure-Services DNS-zone configuratie](#azure-services-dns-zone-configuration).

U hebt de volgende resources nodig om correct te configureren:

- Virtueel netwerk van de client

- Privé-DNS zone [privatelink.database.Windows.net](../dns/private-dns-privatednszone.md)  met [type A-record](../dns/dns-zones-records.md#record-types)

- Informatie over privé-eind punt (FQDN-record naam en privé-IP-adres)

In de volgende scherm afbeelding ziet u de DNS-omzettings volgorde van virtuele netwerk werkbelastingen met behulp van de privé-DNS-zone:

:::image type="content" source="media/private-endpoint-dns/single-vnet-azure-dns.png" alt-text="Eén virtueel netwerk en door Azure verschafte DNS":::

Dit model kan worden uitgebreid naar meerdere peered virtuele netwerken die zijn gekoppeld aan hetzelfde persoonlijke eind punt. U kunt dit doen door [nieuwe virtuele netwerk koppelingen toe te voegen](../dns/private-dns-virtual-network-links.md) aan de privé-DNS-zone voor alle gekoppelde virtuele netwerken.

> [!IMPORTANT]
> Er is één privé-DNS-zone vereist voor deze configuratie. Het maken van meerdere zones met dezelfde naam voor verschillende virtuele netwerken zou hand matige bewerkingen nodig hebben om de DNS-records samen te voegen.

> [!IMPORTANT]
> Als u een persoonlijk eind punt in een hub-en-spoke-model gebruikt vanuit een ander abonnement, moet u dezelfde privé-DNS-zone opnieuw gebruiken op de hub.

In dit scenario is er een [hub-en-spoke](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke) -netwerk topologie met de spoke-netwerken die een gemeen schappelijk persoonlijk eind punt delen en alle spoke-virtuele netwerken zijn gekoppeld aan dezelfde particuliere DNS-zone. 

:::image type="content" source="media/private-endpoint-dns/hub-and-spoke-azure-dns.png" alt-text="Hub en spoke met door Azure verschafte DNS":::

## <a name="on-premises-workloads-using-a-dns-forwarder"></a>On-premises workloads met behulp van een DNS-doorstuur server

Voor on-premises workloads om een FQDN-naam van een persoonlijk eind punt op te lossen in het privé-IP-adres, moet u een DNS-doorstuur server gebruiken om de oplossing van de [open bare DNS-zone](#azure-services-dns-zone-configuration) van Azure-service in azure te implementeren.

Het volgende scenario is geschikt voor een on-premises netwerk met een DNS-doorstuur server in azure, die op zijn beurt verantwoordelijk is voor het omzetten van alle DNS-query's via een doorstuur server van het niveau naar de door Azure verschafte DNS- [168.63.129.16](../virtual-network/what-is-ip-address-168-63-129-16.md). 

> [!NOTE]
> In dit scenario wordt de Azure SQL Database-aanbevolen privé-DNS-zone gebruikt. Voor andere services kunt u het model aanpassen met behulp van de volgende referentie: [Azure-Services DNS-zone configuratie](#azure-services-dns-zone-configuration).

U hebt de volgende resources nodig om correct te configureren:

- On-premises netwerk
- Virtueel netwerk [verbonden met on-premises](/azure/architecture/reference-architectures/hybrid-networking/)
- DNS-doorstuur server geïmplementeerd in azure 
- Privé-DNS zones [privatelink.database.Windows.net](../dns/private-dns-privatednszone.md) met [een record typen](../dns/dns-zones-records.md#record-types)
- Informatie over privé-eind punt (FQDN-record naam en privé-IP-adres)

In het volgende diagram ziet u de DNS-omzettings volgorde van een on-premises netwerk dat gebruikmaakt van een DNS-doorstuur server die is geïmplementeerd in azure, waarbij de oplossing wordt gemaakt door een privé-DNS-zone die is [gekoppeld aan een virtueel netwerk](../dns/private-dns-virtual-network-links.md):

:::image type="content" source="media/private-endpoint-dns/on-premises-using-azure-dns.png" alt-text="On-premises met Azure DNS":::

Deze configuratie kan worden uitgebreid voor een on-premises netwerk waarop al een DNS-oplossing aanwezig is. De on-premises DNS-oplossing moet worden geconfigureerd om DNS-verkeer door te sturen naar Azure DNS via een [voorwaardelijke doorstuur server](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server) die verwijst naar de DNS-doorstuur server die in Azure is geïmplementeerd.

> [!NOTE]
> In dit scenario wordt de Azure SQL Database-aanbevolen privé-DNS-zone gebruikt. Voor andere services kunt u het model aanpassen met behulp van de volgende referentie: [Azure-Services DNS-zone configuratie](#azure-services-dns-zone-configuration)

U hebt de volgende resources nodig om correct te configureren:

- On-premises netwerk met een aangepaste DNS-oplossing 
- Virtueel netwerk [verbonden met on-premises](/azure/architecture/reference-architectures/hybrid-networking/)
- DNS-doorstuur server geïmplementeerd in azure
- Privé-DNS zones [privatelink.database.Windows.net](../dns/private-dns-privatednszone.md)  met [een record typen](../dns/dns-zones-records.md#record-types)
- Informatie over privé-eind punt (FQDN-record naam en privé-IP-adres)

In het volgende diagram ziet u de DNS-omzettings volgorde van een on-premises netwerk dat DNS-verkeer voorwaardelijk doorstuurt naar Azure, waarbij de oplossing wordt gemaakt door een privé-DNS-zone die is [gekoppeld aan een virtueel netwerk](../dns/private-dns-virtual-network-links.md).

> [!IMPORTANT]
> Voorwaardelijk door sturen moet worden uitgevoerd naar de aanbevolen [doorstuur server voor open bare DNS-zone](#azure-services-dns-zone-configuration). Bijvoorbeeld: `database.windows.net` in plaats van **privatelink**. database.Windows.net.

:::image type="content" source="media/private-endpoint-dns/on-premises-forwarding-to-azure.png" alt-text="On-premises door sturen naar Azure DNS":::

## <a name="virtual-network-and-on-premises-workloads-using-a-dns-forwarder"></a>Virtuele netwerk-en on-premises workloads met behulp van een DNS-doorstuur server

Voor een algemene benadering die geschikt is voor werk belastingen die toegang nodig hebben tot een persoonlijk eind punt van virtuele en on-premises netwerken, moet u een gedeelde DNS-doorstuur server gebruiken om de oplossing te maken van de [open bare DNS-zone](#azure-services-dns-zone-configuration) van Azure service die in Azure is geïmplementeerd.

Het volgende scenario is geschikt voor een on-premises netwerk met een DNS-doorstuur server in Azure en virtuele netwerken die toegang nodig hebben tot het persoonlijke eind punt dat zich in een gedeeld hub-netwerk bevindt.  

Deze DNS-doorstuur server is verantwoordelijk voor het omzetten van alle DNS-query's via een doorstuur server van het [168.63.129.16](../virtual-network/what-is-ip-address-168-63-129-16.md)naar de door Azure VERSCHAFTe DNS-service.

> [!IMPORTANT]
> Er is één privé-DNS-zone vereist voor deze configuratie. Alle client verbindingen van on-premises en gekoppelde [virtuele netwerken](../virtual-network/virtual-network-peering-overview.md) moeten ook gebruikmaken van dezelfde privé-DNS-zone.

> [!NOTE]
> In dit scenario wordt de Azure SQL Database-aanbevolen privé-DNS-zone gebruikt. Voor andere services kunt u het model aanpassen met behulp van de volgende referentie: [Azure-Services DNS-zone configuratie](#azure-services-dns-zone-configuration).

U hebt de volgende resources nodig om correct te configureren:

- On-premises netwerk
- Virtueel netwerk [verbonden met on-premises](/azure/architecture/reference-architectures/hybrid-networking/)
- [Gekoppeld virtueel netwerk](../virtual-network/virtual-network-peering-overview.md) 
- DNS-doorstuur server geïmplementeerd in azure
- Privé-DNS zones [privatelink.database.Windows.net](../dns/private-dns-privatednszone.md)  met [een record typen](../dns/dns-zones-records.md#record-types)
- Informatie over privé-eind punt (FQDN-record naam en privé-IP-adres)

In het volgende diagram ziet u de DNS-omzettings volgorde van een on-premises en een virtueel netwerk dat gebruikmaakt van een DNS-doorstuur server die is geïmplementeerd in azure, waarbij de oplossing wordt gemaakt door een privé-DNS-zone die is [gekoppeld aan een virtueel netwerk](../dns/private-dns-virtual-network-links.md):

:::image type="content" source="media/private-endpoint-dns/hybrid-scenario.png" alt-text="Hybride scenario":::

## <a name="next-steps"></a>Volgende stappen
- [Meer informatie over privé-eind punten](private-endpoint-overview.md)
