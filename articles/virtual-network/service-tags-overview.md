---
title: Overzicht van Azure-servicetags
titlesuffix: Azure Virtual Network
description: Meer informatie over servicetags. Servicetags helpen de complexiteit van het maken van beveiligingsregel te minimaliseren.
services: virtual-network
documentationcenter: na
author: allegradomel
ms.service: virtual-network
ms.devlang: NA
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 4/14/2021
ms.author: kumud
ms.reviewer: kumud
ms.openlocfilehash: b5f6f06af3eecabe26f7b587a790912f99b006e4
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107496755"
---
# <a name="virtual-network-service-tags"></a>Servicetags voor virtuele netwerken
<a name="network-service-tags"></a>

Een servicetag vertegenwoordigt een groep IP-adres voorvoegsels van een bepaalde Azure-service. Microsoft beheert de adres voorvoegsels die worden omvat door de servicetag en werkt de servicetag automatisch bij wanneer adressen veranderen, waardoor de complexiteit van frequente updates voor netwerkbeveiligingsregels wordt geminimeerd.

U kunt servicetags gebruiken voor het definiëren van besturingselementen voor netwerktoegang in [netwerkbeveiligingsgroepen](./network-security-groups-overview.md#security-rules) of [Azure Firewall.](../firewall/service-tags.md) Gebruik servicetags in plaats van specifieke IP-adressen wanneer u beveiligingsregels maakt. Door de naam van de servicetag, zoals  **ApiManagement,** op te geven in het juiste bron- of doelveld van een regel, kunt u het verkeer voor de bijbehorende service toestaan of weigeren.  

> [!NOTE] 
> Vanaf maart 2021 kunt u servicetags ook gebruiken in plaats van expliciete IP-adresbereiken in [door de gebruiker gedefinieerde routes.](./virtual-networks-udr-overview.md) Deze functie is momenteel beschikbaar als openbare preview-versie. 

U kunt servicetags gebruiken om netwerkisolatie te bereiken en uw Azure-resources te beschermen tegen het algemene internet tijdens het openen van Azure-services met openbare eindpunten. Maak regels voor binnenkomende/uitgaande netwerkbeveiligingsgroep om verkeer van/naar **internet** te weigeren en verkeer van/naar **AzureCloud** of andere beschikbare [servicetags](#available-service-tags) van specifieke Azure-services toe te staan.

![Netwerkisolatie van Azure-services met behulp van servicetags](./media/service-tags-overview/service_tags.png)

## <a name="available-service-tags"></a>Beschikbare servicetags
De volgende tabel bevat alle servicetags die beschikbaar zijn voor gebruik in [regels voor netwerkbeveiligingsgroep.](./network-security-groups-overview.md#security-rules)

De kolommen geven aan of de tag:

- Is geschikt voor regels die betrekking hebben op inkomende of uitgaande verkeer.
- Ondersteunt [regionaal](https://azure.microsoft.com/regions) bereik.
- Is bruikbaar in [Azure Firewall](../firewall/service-tags.md) regels.

Servicetags weerspiegelen standaard de reeksen voor de hele cloud. Sommige servicetags bieden ook meer gedetailleerde controle door de bijbehorende IP-adresbereiken te beperken tot een opgegeven regio. De servicetag Storage  vertegenwoordigt bijvoorbeeld Azure Storage voor de hele cloud, maar **Storage.WestUS** beperkt het bereik tot alleen de IP-adresbereiken van de opslag van de regio WESTUS. De volgende tabel geeft aan of elke servicetag een dergelijk regionaal bereik ondersteunt.  

| Tag | Doel | Kan ik binnenkomende of uitgaande gegevens gebruiken? | Kan regionaal zijn? | Kunt u gebruiken met Azure Firewall? |
| --- | -------- |:---:|:---:|:---:|
| **ActionGroup** | Actiegroep. | Inkomend | Nee | Nee |
| **ApiManagement** | Beheerverkeer voor aan Azure API Management toegewezen implementaties. <br/><br/>*Opmerking:* Deze tag vertegenwoordigt het Azure API Management service-eindpunt voor het besturingsvlak per regio. Hierdoor kunnen klanten beheerbewerkingen uitvoeren op de API's, bewerkingen, beleidsregels en namedValues die zijn geconfigureerd op de API Management service.  | Inkomend | Ja | Ja |
| **ApplicationInsightsAvailability** | Application Insights beschikbaarheid. | Inkomend | Nee | Nee |
| **AppConfiguration** | App Configuration. | Uitgaand | Nee | Nee |
| **AppService**    | Azure App Service. Deze tag wordt aanbevolen voor uitgaande beveiligingsregels naar web-apps en functie-apps.  | Uitgaand | Ja | Ja |
| **AppServiceManagement** | Beheerverkeer voor implementaties die zijn toegewezen aan App Service Environment. | Beide | Nee | Ja |
| **AzureActiveDirectory** | Azure Active Directory. | Uitgaand | Nee | Ja |
| **AzureActiveDirectoryDomainServices** | Beheerverkeer voor implementaties die zijn toegewezen aan Azure Active Directory Domain Services. | Beide | Nee | Ja |
| **AzureAdvancedThreatProtection** | Azure Advanced Threat Protection. | Uitgaand | Nee | Nee |
| **AzureAPIForFHIR** | Azure API for FHIR (Fast Healthcare Interoperability Resources).<br/><br/> *Opmerking: deze tag kan momenteel niet worden geconfigureerd via de Azure-portal.*| Uitgaand | Nee | Nee |
| **AzureArcInfrastructure** | Azure Arc ingeschakelde servers, kubernetes en gastconfiguratieverkeer Azure Arc ingeschakeld.<br/><br/>*Opmerking:* Deze tag is afhankelijk van de tags **AzureActiveDirectory,****AzureTrafficManager** en **AzureResourceManager.** *Deze tag kan momenteel niet worden geconfigureerd via de Azure-portal.*| Uitgaand | Nee | Ja |
| **AzureBackup** |Azure Backup.<br/><br/>*Opmerking:* Deze tag is afhankelijk van de **tags Storage** en **AzureActiveDirectory.** | Uitgaand | Nee | Ja |
| **AzureBotService** | Azure Bot Service. | Uitgaand | Nee | Nee |
| **AzureCloud** | Alle [openbare IP-adressen van datacenters.](https://www.microsoft.com/download/details.aspx?id=56519) | Uitgaand | Ja | Ja |
| **AzureCognitiveSearch** | Azure Cognitive Search. <br/><br/>Deze tag of de IP-adressen die onder deze tag vallen, kunnen worden gebruikt om indexeerders beveiligde toegang te verlenen tot gegevensbronnen. Raadpleeg de documentatie [over de indexeringsverbinding](../search/search-indexer-troubleshooting.md#connection-errors) voor meer informatie. <br/><br/> *Opmerking:* het IP-adres van de zoekservice is niet opgenomen in  de lijst met IP-adresbereiken voor deze servicetag en moet ook worden toegevoegd aan de IP-firewall van gegevensbronnen. | Inkomend | Nee | Nee |
| **AzureConnectors** | Deze tag vertegenwoordigt de IP-adressen die worden gebruikt voor beheerde connectors die inkomende webhook-callbacks naar de Azure Logic Apps-service maken en uitgaande aanroepen naar hun respectieve services, bijvoorbeeld Azure Storage of Azure Event Hubs. | Inkomende/uitgaande | Ja | Ja |
| **AzureContainerRegistry** | Azure Container Registry. | Uitgaand | Ja | Ja |
| **AzureCosmosDB** | Azure Cosmos DB. | Uitgaand | Ja | Ja |
| **AzureDatabricks** | Azure Databricks. | Beide | Nee | Nee |
| **AzureDataExplorerManagement** | Azure Data Explorer Management. | Inkomend | Nee | Nee |
| **AzureDataLake** | Azure Data Lake Storage Gen1. | Uitgaand | Nee | Ja |
| **AzureDevSpaces** | Azure Dev Spaces. | Uitgaand | Nee | Nee |
| **AzureDevOps** | Azure Dev Ops.<br/><br/>*Opmerking: deze tag kan momenteel niet worden geconfigureerd via de Azure-portal*| Inkomend | Nee | Ja |
| **AzureDigitalTwins** | Azure Digital Twins.<br/><br/>*Opmerking:* Deze tag of de IP-adressen die onder deze tag vallen, kunnen worden gebruikt om de toegang te beperken tot eindpunten die zijn geconfigureerd voor gebeurtenisroutes. *Deze tag kan momenteel niet worden geconfigureerd via de Azure-portal* | Inkomend | Nee | Ja |
| **AzureEventGrid** | Azure Event Grid. | Beide | Nee | Nee |
| **AzureFrontDoor.Frontend** <br/> **AzureFrontDoor.Backend** <br/> **AzureFrontDoor.FirstParty**  | Azure Front Door. | Beide | Nee | Nee |
| **AzureInformationProtection** | Azure Information Protection.<br/><br/>*Opmerking:* Deze tag is afhankelijk van de tags **AzureActiveDirectory,** **AzureFrontDoor.Frontend** **en AzureFrontDoor.FirstParty.** | Uitgaand | Nee | Nee |
| **AzureIoTHub** | Azure IoT Hub. | Uitgaand | Nee | Nee |
| **AzureKeyVault** | Azure Key Vault.<br/><br/>*Opmerking:* Deze tag is afhankelijk van de **tag AzureActiveDirectory.** | Uitgaand | Ja | Ja |
| **AzureLoadBalancer** | De Azure-infrastructuur load balancer. De tag wordt omgezet in het virtuele [IP-adres](./network-security-groups-overview.md#azure-platform-considerations) van de host (168.63.129.16) waar de Azure-statustests vandaan komen. Dit omvat alleen testverkeer, niet echt verkeer naar uw back-endresource. Als u geen gebruik Azure Load Balancer, kunt u deze regel overschrijven. | Beide | Nee | Nee |
| **AzureMachineLearning** | Azure Machine Learning. | Beide | Nee | Ja |
| **AzureMonitor** | Log Analytics, Application Insights, AzMon en aangepaste metrische gegevens (GiG-eindpunten).<br/><br/>*Opmerking:* Voor Log Analytics is de **storage-tag** ook vereist. Als Linux-agents worden gebruikt, is de tag **GuestAndHybridManagement** ook vereist. | Uitgaand | Nee | Ja |
| **AzureOpenDatasets** | Azure Open Datasets.<br/><br/>*Opmerking:* Deze tag is afhankelijk van de **tag AzureFrontDoor.Frontend** **en Storage.** | Uitgaand | Nee | Nee |
| **AzurePlatformDNS** | De basisinfrastructuur (standaard) DNS-service.<br/><br>U kunt deze tag gebruiken om de standaard-DNS uit te schakelen. Wees voorzichtig wanneer u deze tag gebruikt. We raden u aan [azure-platformoverwegingen te lezen.](./network-security-groups-overview.md#azure-platform-considerations) We raden u ook aan om tests uit te voeren voordat u deze tag gebruikt. | Uitgaand | Nee | Nee |
| **AzurePlatformIMDS** | Azure Instance Metadata Service (IMDS), een basisinfrastructuurservice.<br/><br/>U kunt deze tag gebruiken om de standaard-IMDS uit te schakelen. Wees voorzichtig wanneer u deze tag gebruikt. We raden u aan [azure-platformoverwegingen te lezen.](./network-security-groups-overview.md#azure-platform-considerations) We raden u ook aan om tests uit te voeren voordat u deze tag gebruikt. | Uitgaand | Nee | Nee |
| **AzurePlatformLKM** | Windows-licentieverlening of sleutelbeheerservice.<br/><br/>U kunt deze tag gebruiken om de standaardinstellingen voor licenties uit te schakelen. Wees voorzichtig wanneer u deze tag gebruikt. U wordt aangeraden [azure-platformoverwegingen te lezen.](./network-security-groups-overview.md#azure-platform-considerations)  We raden u ook aan om tests uit te voeren voordat u deze tag gebruikt. | Uitgaand | Nee | Nee |
| **AzureResourceManager** | Azure Resource Manager. | Uitgaand | Nee | Nee |
| **AzureSignalR** | Azure SignalR. | Uitgaand | Nee | Nee |
| **AzureSiteRecovery** | Azure Site Recovery.<br/><br/>*Opmerking:* Deze tag is afhankelijk van de tags **AzureActiveDirectory,** **AzureKeyVault,** **EventHub**,**GuestAndHybridManagement** **en Storage.** | Uitgaand | Nee | Nee |
| **AzureTrafficManager** | Azure Traffic Manager IP-adressen van de test.<br/><br/>Zie veelgestelde vragen Traffic Manager meer informatie over het Azure Traffic Manager [van test-IP-adressen.](../traffic-manager/traffic-manager-faqs.md) | Inkomend | Nee | Ja |  
| **BatchNodeManagement** | Beheerverkeer voor implementaties die zijn toegewezen aan Azure Batch. | Beide | Nee | Ja |
| **CognitiveServicesManagement** | De adresbereiken voor verkeer voor Azure Cognitive Services. | Beide | Nee | Nee |
| **Datafactory**  | Azure Data Factory | Beide | Nee | Nee |
| **DataFactoryManagement** | Beheerverkeer voor Azure Data Factory. | Uitgaand | Nee | Nee |
| **Dynamics365ForMarketingEmail** | De adresbereiken voor de marketing-e-mailservice van Dynamics 365. | Uitgaand | Ja | Nee |
| **EventHub** | Azure Event Hubs. | Uitgaand | Ja | Ja |
| **GatewayManager** | Beheerverkeer voor implementaties die zijn toegewezen aan Azure VPN Gateway en Application Gateway. | Inkomend | Nee | Nee |
| **GuestAndHybridManagement** | Azure Automation en gastconfiguratie. | Uitgaand | Nee | Ja |
| **HDInsight** | Azure HDInsight. | Inkomend | Ja | Nee |
| **Internet** | De IP-adresruimte die zich buiten het virtuele netwerk en bereikbaar is via het openbare internet.<br/><br/>Het adresbereik omvat de [openbare IP-adresruimte van Azure.](https://www.microsoft.com/download/details.aspx?id=41653) | Beide | Nee | Nee |
| **LogicApps** | Logic Apps. | Beide | Nee | Nee |
| **LogicAppsManagement** | Beheerverkeer voor Logic Apps. | Inkomend | Nee | Nee |
| **MicrosoftCloudAppSecurity** | Microsoft Cloud App Security. | Uitgaand | Nee | Nee |
| **MicrosoftContainerRegistry** | Containerregister voor Microsoft-containerafbeeldingen. <br/><br/>*Opmerking:* Deze tag is afhankelijk van de **tag AzureFrontDoor.FirstParty.** | Uitgaand | Ja | Ja |
| **PowerBI** | PowerBi. *Opmerking: deze tag kan momenteel niet worden geconfigureerd via de Azure-portal.* | Beide | Nee | Nee|
| **PowerQueryOnline** | Power Query Online. | Beide | Nee | Nee |
| **ServiceBus** | Azure Service Bus verkeer dat gebruikmaakt van de Premium-servicelaag. | Uitgaand | Ja | Ja |
| **ServiceFabric** | Azure Service Fabric.<br/><br/>*Opmerking:* Deze tag vertegenwoordigt het Service Fabric service-eindpunt voor het besturingsvlak per regio. Hierdoor kunnen klanten beheerbewerkingen uitvoeren voor hun Service Fabric clusters van hun VNET (eindpunt bijvoorbeeld https:// westus.servicefabric.azure.com) | Beide | Nee | Nee |
| **Sql** | Azure SQL Database, Azure Database for MySQL, Azure Database for PostgreSQL en Azure Synapse Analytics.<br/><br/>*Opmerking:* Deze tag vertegenwoordigt de service, maar niet specifieke exemplaren van de service. De tag vertegenwoordigt bijvoorbeeld de service Azure SQL Database, maar geen specifieke SQL-database of -server. Deze tag is niet van toepassing op sql managed instance. | Uitgaand | Ja | Ja |
| **SqlManagement** | Beheerverkeer voor aan SQL toegewezen implementaties. | Beide | Nee | Ja |
| **Storage** | Azure Storage. <br/><br/>*Opmerking:* Deze tag vertegenwoordigt de service, maar niet specifieke exemplaren van de service. De tag vertegenwoordigt bijvoorbeeld de service Azure Storage, maar geen specifiek Azure Storage-account. | Uitgaand | Ja | Ja |
| **StorageSyncService** | Opslagsynchronisatieservice. | Beide | Nee | Nee |
| **WindowsVirtualDesktop** | Windows Virtual Desktop. | Beide | Nee | Ja |
| **VirtualNetwork** | De adresruimte van het virtuele netwerk (alle IP-adresbereiken die zijn gedefinieerd voor het virtuele netwerk), alle verbonden on-premises adresruimten, [gekoppelde](virtual-network-peering-overview.md) virtuele netwerken, virtuele netwerken die zijn verbonden met een virtuele netwerkgateway, [](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%3ftoc.json)het virtuele [IP-adres](virtual-networks-udr-overview.md)van de [host](./network-security-groups-overview.md#azure-platform-considerations)en adres voorvoegsels die worden gebruikt op door de gebruiker gedefinieerde routes . Deze tag kan ook standaardroutes bevatten. | Beide | Nee | Nee |

>[!NOTE]
>In het klassieke implementatiemodel (vóór Azure Resource Manager) wordt een subset van de tags die in de vorige tabel worden vermeld, ondersteund. Deze tags worden anders gespeld:
>
>| Klassieke spelling | Equivalente Resource Manager tag |
>|---|---|
>| AZURE_LOADBALANCER | AzureLoadBalancer |
>| INTERNET | Internet |
>| VIRTUAL_NETWORK | VirtualNetwork |

> [!NOTE]
> Servicetags van Azure-services geven de adres voorvoegsels aan van de specifieke cloud die wordt gebruikt. De onderliggende IP-adresbereiken die overeenkomen met de waarde van de **SQL-tag** in de openbare Azure-cloud verschillen bijvoorbeeld van de onderliggende reeksen in de Cloud voor Azure China.

> [!NOTE]
> Als u een [service-eindpunt](virtual-network-service-endpoints-overview.md) voor een virtueel netwerk implementeert voor een service, zoals Azure Storage of Azure SQL Database, voegt Azure een [route](virtual-networks-udr-overview.md#optional-default-routes) toe aan een subnet van een virtueel netwerk voor de service. De adres voorvoegsels in de route zijn dezelfde adres voorvoegsels of CIDR-adresbereiken als die van de bijbehorende servicetag.

## <a name="service-tags-on-premises"></a>Servicetags on-premises  
U kunt de huidige informatie over de servicetag en het huidige bereik verkrijgen om op te nemen als onderdeel van uw on-premises firewallconfiguraties. Deze informatie is de huidige point-in-time-lijst van de IP-adresbereiken die overeenkomen met elke servicetag. U kunt de informatie programmatisch verkrijgen of via een JSON-bestand downloaden, zoals beschreven in de volgende secties.

### <a name="use-the-service-tag-discovery-api-public-preview"></a>De Detectie-API voor servicetags gebruiken (openbare preview)
U kunt de huidige lijst met servicetags programmatisch ophalen, samen met details van het IP-adresbereik:

- [REST](/rest/api/virtualnetwork/servicetags/list)
- [Azure PowerShell](/powershell/module/az.network/Get-AzNetworkServiceTag)
- [Azure-CLI](/cli/azure/network#az-network-list-service-tags)

> [!NOTE]
> Het duurt maximaal vier weken voordat nieuwe servicetaggegevens worden doorgegeven in de API-resultaten. Het wijzigingsnummer in de metagegevens van het antwoord wordt verhoogd wanneer dit gebeurt. Er kunnen tijdelijke verschillen in resultaten zijn wanneer verschillende locatiewaarden worden opgegeven. Wanneer u de resultaten gebruikt om NSG-regels te maken, moet u de locatieparamater zo instellen dat deze overeenkomen met de regio van de NSG. 

> [!NOTE]
> De API-gegevens vertegenwoordigen de tags die kunnen worden gebruikt met NSG-regels, een subset van de tags die momenteel in het downloadbare JSON-bestand staan. In de openbare preview garanderen we niet dat de gegevens van de ene update naar de volgende hetzelfde blijven. 

### <a name="discover-service-tags-by-using-downloadable-json-files"></a>Servicetags ontdekken met behulp van downloadbare JSON-bestanden 
U kunt JSON-bestanden downloaden die de huidige lijst met servicetags bevatten, samen met details van het IP-adresbereik. Deze lijsten worden wekelijks bijgewerkt en gepubliceerd. Locaties voor elke cloud zijn:

- [Azure openbaar](https://www.microsoft.com/download/details.aspx?id=56519)
- [Azure van de Amerikaanse overheid](https://www.microsoft.com/download/details.aspx?id=57063)  
- [Azure China](https://www.microsoft.com/download/details.aspx?id=57062) 
- [Azure Duitsland](https://www.microsoft.com/download/details.aspx?id=57064)   

De IP-adresbereiken in deze bestanden hebben een CIDR-notatie. 

> [!NOTE]
>Een subset van deze informatie is gepubliceerd in XML-bestanden voor [Azure Public,](https://www.microsoft.com/download/details.aspx?id=41653) [Azure China](https://www.microsoft.com/download/details.aspx?id=42064)en [Azure Duitsland.](https://www.microsoft.com/download/details.aspx?id=54770) Deze XML-downloads worden afgeschaft voor 30 juni 2020 en zijn na die datum niet meer beschikbaar. U moet migreren naar met behulp van de discovery-API of JSON-bestanddownloads, zoals beschreven in de vorige secties.

### <a name="tips"></a>Tips 
- U kunt updates van de ene publicatie naar de volgende detecteren door verhoogde *changeNumber-waarden* in het JSON-bestand te noteren. Elke subsectie (bijvoorbeeld **Storage.WestUS)** heeft een eigen *changeNumber* dat wordt verhoogd naarmate er wijzigingen optreden. Het hoogste niveau van het *changeNumber* van het bestand wordt verhoogd wanneer een van de subsecties wordt gewijzigd.
- Zie de [PowerShell-documentatie](/powershell/module/az.network/Get-AzNetworkServiceTag) servicetagdetectie-API voor voorbeelden van het parseren van de servicetaggegevens (bijvoorbeeld alle adresbereiken voor Storage in WestUS op te halen).
- Wanneer nieuwe IP-adressen worden toegevoegd aan servicetags, worden ze ten minste één week niet gebruikt in Azure. Dit geeft u de tijd om alle systemen bij te werken die mogelijk de IP-adressen moeten bijhouden die zijn gekoppeld aan servicetags.

## <a name="next-steps"></a>Volgende stappen
- Meer informatie over het [maken van een netwerkbeveiligingsgroep.](tutorial-filter-network-traffic.md)
