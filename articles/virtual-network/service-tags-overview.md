---
title: Overzicht van Azure-service Tags
titlesuffix: Azure Virtual Network
description: Meer informatie over service tags. Service Tags helpen de complexiteit van het maken van de beveiligings regel te minimaliseren.
services: virtual-network
documentationcenter: na
author: allegradomel
ms.service: virtual-network
ms.devlang: NA
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/30/2020
ms.author: kumud
ms.reviewer: kumud
ms.openlocfilehash: 2d14ca2423d34926a9e297823a6515c2c5dde06a
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2021
ms.locfileid: "105607113"
---
# <a name="virtual-network-service-tags"></a>Service tags van virtueel netwerk
<a name="network-service-tags"></a>

Een servicetag vertegenwoordigt een groep IP-adres voorvoegsels van een bepaalde Azure-service. Micro soft beheert de adres voorvoegsels die zijn opgenomen in het servicetag en werkt de servicetag automatisch bij met gewijzigde adressen, zodat de complexiteit van regel matige updates voor netwerk beveiligings regels wordt geminimaliseerd.

U kunt service tags gebruiken voor het definiëren van netwerk toegangs beheer voor [netwerk beveiligings groepen](./network-security-groups-overview.md#security-rules) of [Azure firewall](../firewall/service-tags.md). Gebruik service tags in plaats van specifieke IP-adressen wanneer u beveiligings regels maakt. Door de servicetag naam op te geven, zoals **ApiManagement**, in het juiste *bron* -of *doel* veld van een regel, kunt u het verkeer voor de bijbehorende service toestaan of weigeren. 

> [!NOTE] 
> Vanaf maart 2021 kunt u ook service tags gebruiken in plaats van expliciete IP-bereiken in door de [gebruiker gedefinieerde routes](./virtual-networks-udr-overview.md). Deze functie is momenteel beschikbaar als openbare preview-versie. 

U kunt service tags gebruiken om netwerk isolatie te bereiken en uw Azure-resources te beveiligen via het algemene Internet tijdens het openen van Azure-Services met open bare eind punten. Maak regels voor binnenkomende/uitgaande netwerk beveiligings groepen om verkeer naar/van **Internet** te weigeren en verkeer naar/van **Cloud** of andere [beschik bare service Tags](#available-service-tags) van specifieke Azure-Services toe te staan.

![Netwerk isolatie van Azure-Services met Service Tags](./media/service-tags-overview/service_tags.png)

## <a name="available-service-tags"></a>Beschik bare service Tags
De volgende tabel bevat alle service tags die beschikbaar zijn voor gebruik in regels voor de [netwerk beveiligings groep](./network-security-groups-overview.md#security-rules) .

De kolommen geven aan of de tag:

- Is geschikt voor regels die betrekking hebben op inkomend of uitgaand verkeer.
- Ondersteunt het [regionale](https://azure.microsoft.com/regions) bereik.
- Is bruikbaar in [Azure firewall](../firewall/service-tags.md) -regels.

Service Tags geven standaard de bereiken weer voor de hele Cloud. Sommige service Tags bieden ook een nauw keurigere controle door de overeenkomende IP-bereiken te beperken tot een bepaalde regio. De service tags **opslag** vertegenwoordigt bijvoorbeeld Azure Storage voor de hele Cloud, maar **opslag. westus** beperkt het bereik tot alleen de IP-adresbereiken van het opslag gebied van de regio westus. In de volgende tabel wordt aangegeven of elk servicetag het regionale bereik ondersteunt.  

| Tag | Doel | Kunt u inkomend of uitgaand gebruiken? | Kan regionaal worden? | Kunt gebruiken met Azure Firewall? |
| --- | -------- |:---:|:---:|:---:|
| **ActionGroup** | Actie groep. | Inkomend | Nee | Nee |
| **ApiManagement** | Beheer verkeer voor Azure API Management-specifieke implementaties. <br/><br/>*Opmerking:* Deze tag vertegenwoordigt het Azure API Management service-eind punt voor besturings vlak per regio. Hierdoor kunnen klanten beheer bewerkingen uitvoeren op de Api's, bewerkingen, beleids regels, NamedValues die zijn geconfigureerd voor de API Management service.  | Inkomend | Ja | Ja |
| **ApplicationInsightsAvailability** | Beschik baarheid van Application Insights. | Inkomend | Nee | Nee |
| **AppConfiguration** | App-configuratie. | Uitgaand | Nee | Nee |
| **AppService**    | Azure App Service. Deze tag wordt aanbevolen voor uitgaande beveiligings regels voor web apps en functie-apps.  | Uitgaand | Ja | Ja |
| **AppServiceManagement** | Beheer verkeer voor implementaties die zijn toegewezen aan App Service Environment. | Beide | Nee | Ja |
| **AzureActiveDirectory** | Azure Active Directory. | Uitgaand | Nee | Ja |
| **AzureActiveDirectoryDomainServices** | Beheer verkeer voor implementaties die zijn toegewezen aan Azure Active Directory Domain Services. | Beide | Nee | Ja |
| **AzureAdvancedThreatProtection** | Azure Advanced Threat Protection. | Uitgaand | Nee | Nee |
| **AzureAPIForFHIR** | Azure API voor FHIR (snelle samenwerkings bronnen voor de gezondheids zorg).<br/><br/> *Opmerking: deze tag kan momenteel niet worden geconfigureerd via Azure Portal.*| Uitgaand | Nee | Nee |
| **AzureArcInfrastructure** | Servers met Azure-Arc, ingeschakelde Kubernetes en gast configuratie verkeer.<br/><br/>*Opmerking:* Deze tag heeft een afhankelijkheid van de **AzureActiveDirectory**-,**AzureTrafficManager**-en **AzureResourceManager** -Tags. *Deze tag kan momenteel niet worden geconfigureerd via Azure Portal*.| Uitgaand | Nee | Ja |
| **AzureBackup** |Azure Backup.<br/><br/>*Opmerking:* Deze tag bevat een afhankelijkheid van de **opslag** -en **AzureActiveDirectory** -Tags. | Uitgaand | Nee | Ja |
| **AzureBotService** | Azure Bot Service. | Uitgaand | Nee | Nee |
| **AzureCloud** | Alle [open bare IP-adressen van data centers](https://www.microsoft.com/download/details.aspx?id=56519). | Uitgaand | Ja | Ja |
| **AzureCognitiveSearch** | Azure Cognitive Search. <br/><br/>Deze tag of de IP-adressen waarop deze tag wordt toegepast, kunnen worden gebruikt om Indexeer functies beveiligde toegang te geven tot gegevens bronnen. Raadpleeg de documentatie van de [indexers verbinding](../search/search-indexer-troubleshooting.md#connection-errors) voor meer informatie. <br/><br/> *Opmerking*: het IP-adres van de zoek service is niet opgenomen in de lijst met IP-bereiken voor deze servicetag en **moet ook worden toegevoegd** aan de IP-firewall van gegevens bronnen. | Inkomend | Nee | Nee |
| **AzureConnectors** | Deze tag vertegenwoordigt de IP-adressen die worden gebruikt voor beheerde connectors die inkomende webhook-retour aanroepen naar de Azure Logic Apps-service en uitgaande aanroepen naar hun respectievelijke Services, bijvoorbeeld Azure Storage of Azure Event Hubs. | Inkomend/uitgaand | Ja | Ja |
| **AzureContainerRegistry** | Azure Container Registry. | Uitgaand | Ja | Ja |
| **AzureCosmosDB** | Azure Cosmos DB. | Uitgaand | Ja | Ja |
| **AzureDatabricks** | Azure Databricks. | Beide | Nee | Nee |
| **AzureDataExplorerManagement** | Beheer van Azure Data Explorer. | Inkomend | Nee | Nee |
| **AzureDataLake** | Azure Data Lake Storage Gen1. | Uitgaand | Nee | Ja |
| **AzureDevSpaces** | Azure dev Spaces. | Uitgaand | Nee | Nee |
| **AzureDevOps** | Azure dev OPS.<br/><br/>*Opmerking: deze tag kan momenteel niet worden geconfigureerd via Azure Portal*| Inkomend | Nee | Ja |
| **AzureDigitalTwins** | Azure Digital Apparaatdubbels.<br/><br/>*Opmerking:* Deze tag of de IP-adressen waarop deze tag wordt toegepast, kunnen worden gebruikt om de toegang te beperken tot eind punten die zijn geconfigureerd voor gebeurtenis routes. *Deze tag kan momenteel niet worden geconfigureerd via Azure Portal* | Inkomend | Nee | Ja |
| **AzureEventGrid** | Azure Event Grid. | Beide | Nee | Nee |
| **AzureFrontDoor. front-end** <br/> **AzureFrontDoor. back-end** <br/> **AzureFrontDoor.FirstParty**  | De voor deur van Azure. | Beide | Nee | Nee |
| **AzureInformationProtection** | Azure Information Protection.<br/><br/>*Opmerking:* Deze tag heeft een afhankelijkheid van de tags **AzureActiveDirectory**, **AzureFrontDoor. frontend** en **AzureFrontDoor. FirstParty** . | Uitgaand | Nee | Nee |
| **AzureIoTHub** | Azure IoT Hub. | Uitgaand | Nee | Nee |
| **AzureKeyVault** | Azure Key Vault.<br/><br/>*Opmerking:* Deze tag bevat een afhankelijkheid van de **AzureActiveDirectory** -tag. | Uitgaand | Ja | Ja |
| **AzureLoadBalancer** | De Azure-infrastructuur load balancer. De tag wordt omgezet in het [virtuele IP-adres van de host](./network-security-groups-overview.md#azure-platform-considerations) (168.63.129.16) waar de Azure Health-tests afkomstig zijn. Dit omvat alleen test verkeer, niet het werkelijke verkeer naar uw back-end-resource. Als u Azure Load Balancer niet gebruikt, kunt u deze regel onderdrukken. | Beide | Nee | Nee |
| **AzureMachineLearning** | Azure Machine Learning. | Beide | Nee | Ja |
| **AzureMonitor** | Log Analytics, Application Insights, AzMon en aangepaste metrische gegevens (GB-eind punten).<br/><br/>*Opmerking:* Voor Log Analytics is de **opslag** label ook vereist. Als Linux-agents worden gebruikt, is de tag **GuestAndHybridManagement** ook vereist. | Uitgaand | Nee | Ja |
| **AzureOpenDatasets** | Open gegevens sets van Azure.<br/><br/>*Opmerking:* Deze tag heeft een afhankelijkheid van het **AzureFrontDoor. frontend** -en **opslag** label. | Uitgaand | Nee | Nee |
| **AzurePlatformDNS** | De basis-DNS-service (standaard).<br/><br>U kunt deze tag gebruiken om de standaard-DNS uit te scha kelen. Wees voorzichtig wanneer u deze tag gebruikt. U wordt aangeraden [Azure-platform overwegingen](./network-security-groups-overview.md#azure-platform-considerations)te lezen. We raden u ook aan eerst tests uit te voeren voordat u deze tag gebruikt. | Uitgaand | Nee | Nee |
| **AzurePlatformIMDS** | Azure Instance Metadata Service (IMDS), een basis infrastructuur service.<br/><br/>U kunt deze tag gebruiken om de standaard IMDS uit te scha kelen. Wees voorzichtig wanneer u deze tag gebruikt. U wordt aangeraden [Azure-platform overwegingen](./network-security-groups-overview.md#azure-platform-considerations)te lezen. We raden u ook aan eerst tests uit te voeren voordat u deze tag gebruikt. | Uitgaand | Nee | Nee |
| **AzurePlatformLKM** | Windows-licentie verlening of service voor sleutel beheer.<br/><br/>U kunt deze tag gebruiken om de standaard instellingen voor licentie verlening uit te scha kelen. Wees voorzichtig wanneer u deze tag gebruikt. U wordt aangeraden [Azure-platform overwegingen](./network-security-groups-overview.md#azure-platform-considerations)te lezen.  We raden u ook aan eerst tests uit te voeren voordat u deze tag gebruikt. | Uitgaand | Nee | Nee |
| **AzureResourceManager** | Azure Resource Manager. | Uitgaand | Nee | Nee |
| **AzureSignalR** | Azure-Signa lering. | Uitgaand | Nee | Nee |
| **AzureSiteRecovery** | Azure Site Recovery.<br/><br/>*Opmerking:* Deze tag heeft een afhankelijkheid van de tags **AzureActiveDirectory**, **AzureKeyVault**, **EventHub**,**GuestAndHybridManagement** en **Storage** . | Uitgaand | Nee | Nee |
| **AzureTrafficManager** | IP-adressen van Azure Traffic Manager test.<br/><br/>Zie [Veelgestelde vragen over Azure Traffic Manager](../traffic-manager/traffic-manager-faqs.md)voor meer informatie over het IP-adres van Traffic Manager test. | Inkomend | Nee | Ja |  
| **BatchNodeManagement** | Beheer verkeer voor implementaties die zijn toegewezen aan Azure Batch. | Beide | Nee | Ja |
| **CognitiveServicesManagement** | De adresbereiken voor verkeer voor Azure Cognitive Services. | Beide | Nee | Nee |
| **DataFactory**  | Azure Data Factory | Beide | Nee | Nee |
| **DataFactoryManagement** | Beheer verkeer voor Azure Data Factory. | Uitgaand | Nee | Nee |
| **Dynamics365ForMarketingEmail** | De adresbereiken voor de marketing-e-mail service van Dynamics 365. | Uitgaand | Ja | Nee |
| **EventHub** | Azure Event Hubs. | Uitgaand | Ja | Ja |
| **GatewayManager** | Beheer verkeer voor implementaties die zijn toegewezen aan Azure VPN Gateway en Application Gateway. | Inkomend | Nee | Nee |
| **GuestAndHybridManagement** | Azure Automation-en gast configuratie. | Uitgaand | Nee | Ja |
| **HDInsight** | Azure HDInsight. | Inkomend | Ja | Nee |
| **Internet** | De IP-adres ruimte die zich buiten het virtuele netwerk bevindt en bereikbaar is via het open bare Internet.<br/><br/>Het adres bereik bevat de [open bare IP-adres ruimte van Azure](https://www.microsoft.com/download/details.aspx?id=41653). | Beide | Nee | Nee |
| **LogicApps** | Logic Apps. | Beide | Nee | Nee |
| **LogicAppsManagement** | Beheer verkeer voor Logic Apps. | Inkomend | Nee | Nee |
| **MicrosoftCloudAppSecurity** | Microsoft Cloud App Security. | Uitgaand | Nee | Nee |
| **MicrosoftContainerRegistry** | Container register voor micro soft container-installatie kopieën. <br/><br/>*Opmerking:* Deze tag bevat een afhankelijkheid van de tag **AzureFrontDoor. FirstParty** . | Uitgaand | Ja | Ja |
| **Power bi** | Power bi. *Opmerking: deze tag kan momenteel niet worden geconfigureerd via Azure Portal.* | Beide | Nee | Nee|
| **PowerQueryOnline** | Power Query online. | Beide | Nee | Nee |
| **ServiceBus** | Azure Service Bus verkeer dat gebruikmaakt van de Premium-servicelaag. | Uitgaand | Ja | Ja |
| **ServiceFabric** | Azure Service Fabric.<br/><br/>*Opmerking:* Deze tag vertegenwoordigt het Service Fabric service-eind punt voor besturings vlak per regio. Hierdoor kunnen klanten beheer bewerkingen uitvoeren voor hun Service Fabric clusters vanuit hun VNET (eind punt. https://westus.servicefabric.azure.com) | Beide | Nee | Nee |
| **SQL** | Azure SQL Database, Azure Database for MySQL, Azure Database for PostgreSQL en Azure Synapse Analytics.<br/><br/>*Opmerking:* Deze tag vertegenwoordigt de service, maar geen specifieke exemplaren van de service. De tag vertegenwoordigt bijvoorbeeld de service Azure SQL Database, maar geen specifieke SQL-database of -server. Deze tag is niet van toepassing op een SQL Managed instance. | Uitgaand | Ja | Ja |
| **SqlManagement** | Beheer verkeer voor SQL-specifieke implementaties. | Beide | Nee | Ja |
| **Storage** | Azure Storage. <br/><br/>*Opmerking:* Deze tag vertegenwoordigt de service, maar geen specifieke exemplaren van de service. De tag vertegenwoordigt bijvoorbeeld de service Azure Storage, maar geen specifiek Azure Storage-account. | Uitgaand | Ja | Ja |
| **StorageSyncService** | Opslag synchronisatie service. | Beide | Nee | Nee |
| **WindowsVirtualDesktop** | Virtueel bureau blad van Windows. | Beide | Nee | Ja |
| **VirtualNetwork** | De adres ruimte van het virtuele netwerk (alle IP-adresbereiken die zijn gedefinieerd voor het virtuele netwerk), alle verbonden on-premises adres ruimten, [peered](virtual-network-peering-overview.md) virtuele netwerken, virtuele netwerken die zijn verbonden met een [virtuele netwerk gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%3ftoc.json), het [virtuele IP-adres van de host](./network-security-groups-overview.md#azure-platform-considerations)en adres voorvoegsels die worden gebruikt voor door de [gebruiker gedefinieerde routes](virtual-networks-udr-overview.md). Deze tag kan ook standaard routes bevatten. | Beide | Nee | Nee |

>[!NOTE]
>In het klassieke implementatie model (vóór Azure Resource Manager) wordt een subset van de tags die in de vorige tabel worden weer gegeven, ondersteund. Deze tags zijn anders gespeld:
>
>| Klassieke spelling | Gelijkwaardige Resource Manager-tag |
>|---|---|
>| AZURE_LOADBALANCER | AzureLoadBalancer |
>| INTERNET | Internet |
>| VIRTUAL_NETWORK | VirtualNetwork |

> [!NOTE]
> Service tags van Azure Services duiden op de adres voorvoegsels van de specifieke Cloud die wordt gebruikt. Het onderliggende IP-bereik dat overeenkomt met de **SQL** -code waarde in de open bare Azure-Cloud, verschilt bijvoorbeeld van de onderliggende bereiken in de Azure China-Cloud.

> [!NOTE]
> Als u een [service-eind punt voor een virtueel netwerk](virtual-network-service-endpoints-overview.md) implementeert voor een service, zoals Azure Storage of Azure SQL database, voegt Azure een [route](virtual-networks-udr-overview.md#optional-default-routes) toe aan een subnet van een virtueel netwerk voor de service. De adres voorvoegsels in de route zijn dezelfde adres voorvoegsels of CIDR-bereiken als die van de bijbehorende servicetag.

## <a name="service-tags-on-premises"></a>On-premises service Tags  
U kunt de huidige informatie over de servicetag en het bereik ophalen om op te nemen als onderdeel van uw on-premises firewall configuraties. Deze informatie is de huidige tijdgebonden lijst met IP-bereiken die overeenkomen met elke servicetag. U kunt de informatie via programma code of via het downloaden van een JSON-bestand verkrijgen, zoals wordt beschreven in de volgende secties.

### <a name="use-the-service-tag-discovery-api-public-preview"></a>De service label detectie-API (open bare preview) gebruiken
U kunt de huidige lijst met Service Tags op een programmatische manier ophalen in combi natie met details van IP-adres bereik:

- [REST](/rest/api/virtualnetwork/servicetags/list)
- [Azure PowerShell](/powershell/module/az.network/Get-AzNetworkServiceTag)
- [Azure-CLI](/cli/azure/network#az-network-list-service-tags)

> [!NOTE]
> In de open bare preview-versie kan de detectie-API informatie retour neren die minder actueel is dan gegevens die door de JSON-down loads worden geretourneerd. (Zie de volgende sectie.)


### <a name="discover-service-tags-by-using-downloadable-json-files"></a>Service Tags detecteren met behulp van Download bare JSON-bestanden 
U kunt JSON-bestanden downloaden die de huidige lijst met Service Tags samen met IP-adres bereik gegevens bevatten. Deze lijsten worden wekelijks bijgewerkt en gepubliceerd. Locaties voor elke Cloud zijn:

- [Azure openbaar](https://www.microsoft.com/download/details.aspx?id=56519)
- [Azure van de Amerikaanse overheid](https://www.microsoft.com/download/details.aspx?id=57063)  
- [Azure China](https://www.microsoft.com/download/details.aspx?id=57062) 
- [Azure Duitsland](https://www.microsoft.com/download/details.aspx?id=57064)   

De IP-adresbereiken in deze bestanden bevinden zich in CIDR-notatie. 

> [!NOTE]
>Een subset van deze gegevens is gepubliceerd in XML-bestanden voor [Azure Public](https://www.microsoft.com/download/details.aspx?id=41653), [Azure China](https://www.microsoft.com/download/details.aspx?id=42064)en [Azure Duitsland](https://www.microsoft.com/download/details.aspx?id=54770). Deze XML-down loads worden vóór 30 juni 2020 verouderd en zijn na die datum niet meer beschikbaar. U moet migreren naar met behulp van de detectie-API of JSON-bestand downloads, zoals beschreven in de vorige secties.

### <a name="tips"></a>Tips 
- U kunt updates van de ene publicatie naar de volgende detecteren door verhoogde *changeNumber* -waarden in het JSON-bestand op te nemen. Elke subsectie (bijvoorbeeld **opslag. westus**) heeft een eigen *changeNumber* die wordt verhoogd wanneer er wijzigingen optreden. Het hoogste niveau van de *changeNumber* van het bestand wordt verhoogd wanneer een van de subsecties wordt gewijzigd.
- Voor voor beelden van het parseren van de servicetag gegevens (bijvoorbeeld het ophalen van alle adresbereiken voor opslag in Westus), raadpleegt u de Help-documentatie voor de [service label detectie-API](/powershell/module/az.network/Get-AzNetworkServiceTag) .
- Wanneer er nieuwe IP-adressen aan service tags worden toegevoegd, worden ze niet ten minste één week in azure gebruikt. Dit geeft u tijd om systemen bij te werken die mogelijk de IP-adressen moeten volgen die aan service tags zijn gekoppeld.

## <a name="next-steps"></a>Volgende stappen
- Meer informatie over het [maken van een netwerk beveiligings groep](tutorial-filter-network-traffic.md).
