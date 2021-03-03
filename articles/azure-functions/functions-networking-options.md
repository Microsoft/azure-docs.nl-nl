---
title: Netwerkopties van Azure Functions
description: Een overzicht van alle beschik bare netwerk opties in Azure Functions.
author: cachai2
ms.topic: conceptual
ms.date: 1/21/2021
ms.author: cachai
ms.openlocfilehash: ceef827f7406f8915d205349372a43626c917e4b
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101729229"
---
# <a name="azure-functions-networking-options"></a>Netwerkopties van Azure Functions

In dit artikel worden de netwerk functies beschreven die beschikbaar zijn via de hosting opties voor Azure Functions. Alle volgende netwerk opties bieden u de mogelijkheid om toegang te krijgen tot bronnen zonder Internet Routeer bare adressen of om Internet toegang te beperken tot een functie-app.

De hosting modellen hebben verschillende niveaus van netwerk isolatie beschikbaar. Door de juiste oplossing te kiezen, kunt u voldoen aan de vereisten voor netwerk isolatie.

U kunt functie-apps op verschillende manieren hosten:

* U kunt kiezen uit plan opties die worden uitgevoerd op een multi tenant-infra structuur, met verschillende niveaus van connectiviteit voor virtuele netwerken en schaal opties:
    * Het [verbruiks abonnement](consumption-plan.md) wordt dynamisch geschaald in reactie op belasting en biedt minimale opties voor netwerk isolatie.
    * Het [Premium-abonnement](functions-premium-plan.md) wordt ook dynamisch geschaald en biedt meer uitgebreide netwerk isolatie.
    * Het Azure [app service-abonnement](dedicated-plan.md) werkt op een vaste schaal en biedt netwerk isolatie die vergelijkbaar is met het Premium-abonnement.
* U kunt functies uitvoeren in een [app service Environment](../app-service/environment/intro.md). Deze methode implementeert uw functie in uw virtuele netwerk en biedt volledige netwerk controle en-isolatie.

## <a name="matrix-of-networking-features"></a>Matrix van netwerk functies

[!INCLUDE [functions-networking-features](../../includes/functions-networking-features.md)]

## <a name="inbound-access-restrictions"></a>Beperkingen voor binnenkomende toegang

U kunt toegangs beperkingen gebruiken voor het definiëren van een lijst met door prioriteiten gerangschikte IP-adressen die geen toegang tot uw app hebben of geweigerd. De lijst kan IPv4-en IPv6-adressen of specifieke subnetten van het virtuele netwerk bevatten die gebruikmaken van [service-eind punten](#use-service-endpoints). Als er sprake is van een of meer vermeldingen, bevindt zich een impliciete ' weigeren ' aan het einde van de lijst. IP-beperkingen werken met alle opties voor het hosten van functies.

Toegangs beperkingen zijn beschikbaar in de [Premium](functions-premium-plan.md), het [verbruik](consumption-plan.md)en het [app service](dedicated-plan.md).

> [!NOTE]
> Als er netwerk beperkingen zijn ingesteld, kunt u alleen implementeren vanuit uw virtuele netwerk of wanneer u het IP-adres van de computer die u gebruikt voor toegang tot de Azure Portal op de lijst met veilige geadresseerden. U kunt de functie echter wel beheren via de portal.

Zie [Azure app service beperkingen voor statische toegang](../app-service/app-service-ip-restrictions.md)voor meer informatie.

### <a name="use-service-endpoints"></a>Service-eindpunten gebruiken

Door service-eind punten te gebruiken, kunt u de toegang beperken tot geselecteerde subnetten van het virtuele netwerk van Azure. Als u de toegang tot een specifiek subnet wilt beperken, maakt u een beperkings regel met een **Virtual Network** type. U kunt vervolgens het abonnement, het virtuele netwerk en het subnet selecteren dat u wilt toestaan of weigeren van toegang tot. 

Als service-eind punten niet al zijn ingeschakeld met micro soft. web voor het geselecteerde subnet, worden ze automatisch ingeschakeld, tenzij u het selectie vakje **ontbrekende micro soft. webservice-eind punten negeren** inschakelt. Het scenario waarin u mogelijk service-eind punten in de app wilt inschakelen, maar niet het subnet is afhankelijk van het feit of u gemachtigd bent om deze in te scha kelen op het subnet. 

Als iemand anders de service-eind punten in het subnet moet inschakelen, schakelt u het selectie vakje **ontbrekende micro soft. web service-eind punten negeren** in. Uw app wordt geconfigureerd voor service-eind punten in de verwachting dat ze later in het subnet worden ingeschakeld. 

![Scherm opname van het deel venster ' IP-beperking toevoegen ' met het Virtual Network type geselecteerd.](../app-service/media/app-service-ip-restrictions/access-restrictions-vnet-add.png)

U kunt Service-eind punten niet gebruiken om de toegang te beperken tot apps die worden uitgevoerd in een App Service Environment. Als uw app zich in een App Service Environment bevindt, kunt u de toegang hiertoe beheren door IP-toegangs regels toe te passen. 

Zie voor meer informatie over het instellen van service-eind punten [verbinding maken Azure functions toegang tot privé-site](functions-create-private-site-access.md).

## <a name="private-endpoint-connections"></a>Verbindingen met privé-eind punten

[!INCLUDE [functions-private-site-access](../../includes/functions-private-site-access.md)]

Als u andere services wilt aanroepen die een persoonlijke eindpunt verbinding hebben, zoals opslag of service bus, moet u uw app configureren voor het maken van [uitgaande aanroepen naar privé-eind punten](#private-endpoints).

## <a name="virtual-network-integration"></a>Integratie van virtueel netwerk

Met virtuele netwerk integratie kan uw functie-app toegang krijgen tot bronnen in een virtueel netwerk.
Azure Functions ondersteunt twee soorten integratie van virtuele netwerken:

[!INCLUDE [app-service-web-vnet-types](../../includes/app-service-web-vnet-types.md)]

Virtuele netwerk integratie in Azure Functions gebruikt een gedeelde infra structuur met App Service web-apps. Zie voor meer informatie over de twee typen integratie van virtuele netwerken:

* [Regionale virtuele netwerk integratie](../app-service/web-sites-integrate-with-vnet.md#regional-vnet-integration)
* [Gateway-vereiste virtuele netwerk integratie](../app-service/web-sites-integrate-with-vnet.md#gateway-required-vnet-integration)

Zie [een functie-app integreren met een virtueel Azure-netwerk](functions-create-vnet.md)voor meer informatie over het instellen van de integratie van virtuele netwerken.

## <a name="regional-virtual-network-integration"></a>Regionale virtuele netwerk integratie

[!INCLUDE [app-service-web-vnet-types](../../includes/app-service-web-vnet-regional.md)]

## <a name="connect-to-service-endpoint-secured-resources"></a>Verbinding maken met de beveiligde resources van het service-eind punt

Als u een hoger beveiligings niveau wilt bieden, kunt u een aantal Azure-Services beperken tot een virtueel netwerk met behulp van service-eind punten. Vervolgens moet u uw functie-app met dat virtuele netwerk integreren om toegang te krijgen tot de resource. Deze configuratie wordt ondersteund op alle abonnementen die ondersteuning bieden voor de integratie van virtuele netwerken.

Zie [service-eind punten voor virtueel netwerk](../virtual-network/virtual-network-service-endpoints-overview.md)voor meer informatie.

## <a name="restrict-your-storage-account-to-a-virtual-network"></a>Uw opslag account beperken tot een virtueel netwerk 

Wanneer u een functie-app maakt, moet u een Azure Storage-account voor algemeen gebruik maken of koppelen dat ondersteuning biedt voor blob-, wachtrij-en tabel opslag.  U kunt dit opslag account vervangen door een abonnement dat is beveiligd met Service-eind punten of een persoonlijk eind punt.  Deze functie werkt momenteel alleen voor alle door Vnet ondersteunde sku's, waaronder Standard en Premium, met uitzonde ring van op Flex-tempels waarvoor Vnet alleen beschikbaar is voor Premium SKU. Een functie instellen met een opslag account die is beperkt tot een particulier netwerk:

1. Maak een functie met een opslag account waarvoor geen service-eind punten zijn ingeschakeld.
1. Configureer de functie om verbinding te maken met uw virtuele netwerk.
1. Een ander opslag account maken of configureren.  Dit is het opslag account dat wordt beveiligd met Service-eind punten en om onze functie te verbinden.
1. [Maak een bestands share](../storage/files/storage-how-to-create-file-share.md#create-file-share) in het account voor beveiligde opslag.
1. Schakel de service-eind punten of het persoonlijke eind punt in voor het opslag account.  
    * Als u verbindingen met een privé-eind punt gebruikt, heeft het opslag account een persoonlijk eind punt voor de `file` `blob` subresources nodig.  Als u bepaalde functies als Durable Functions gebruikt, hebt u ook `queue` `table` toegang nodig via een verbinding met een privé-eind punt.
    * Als u service-eind punten gebruikt, schakelt u het subnet in dat is toegewezen aan uw functie-apps voor opslag accounts.
1. Beschrijving Kopieer het bestand en de blob-inhoud van het functie-app-opslag account naar het beveiligde opslag account en de bestands share.
1. Kopieer de connection string voor dit opslag account.
1. Werk de **Toepassings instellingen** onder **configuratie** voor de functie-app als volgt bij:
    - `AzureWebJobsStorage` de connection string voor het account voor beveiligde opslag.
    - `WEBSITE_CONTENTAZUREFILECONNECTIONSTRING` de connection string voor het account voor beveiligde opslag.
    - `WEBSITE_CONTENTSHARE` de naam van de bestands share die is gemaakt in het account voor beveiligde opslag.
    - Maak een nieuwe instelling met de naam `WEBSITE_CONTENTOVERVNET` en waarde van `1` .
    - Als het opslag account gebruikmaakt van verbindingen met een privé-eind punt, controleert of voegt u de volgende instellingen toe
        - `WEBSITE_VNET_ROUTE_ALL` met een waarde van `1` .
        - `WEBSITE_DNS_SERVER` met een waarde van `168.63.129.16` 
1. Sla de toepassings instellingen op.  

De functie-app wordt opnieuw gestart en wordt nu verbonden met een beveiligd opslag account.

## <a name="use-key-vault-references"></a>Key Vault-referenties gebruiken

U kunt Azure Key Vault verwijzingen gebruiken om geheimen van Azure Key Vault in uw Azure Functions toepassing te gebruiken zonder dat u code wijzigingen hoeft aan te brengen. Azure Key Vault is een service die gecentraliseerd geheimenbeheer biedt, met volledige controle over het toegangsbeleid en de auditgeschiedenis.

[Key Vault verwijzingen](../app-service/app-service-key-vault-references.md) werken momenteel niet als uw sleutel kluis is beveiligd met Service-eind punten. Als u verbinding wilt maken met een sleutel kluis met behulp van virtuele netwerk integratie, moet u Key Vault aanroepen in de code van uw toepassing.

## <a name="virtual-network-triggers-non-http"></a>Virtuele netwerk triggers (niet-HTTP)

Op dit moment kunt u niet-HTTP-trigger functies vanuit een virtueel netwerk op twee manieren gebruiken:

+ Voer uw functie-app uit in een Premium-abonnement en schakel ondersteuning voor virtuele netwerk triggers in.
+ Voer uw functie-app uit in een App Service plan of App Service Environment.

### <a name="premium-plan-with-virtual-network-triggers"></a>Premium-abonnement met virtuele netwerk triggers

Wanneer u een Premium-abonnement uitvoert, kunt u niet-HTTP-trigger functies verbinden met services die binnen een virtueel netwerk worden uitgevoerd. Hiervoor moet u ondersteuning voor virtuele netwerk triggers inschakelen voor uw functie-app. De instelling **runtime schaal bewaking** vindt u in de [Azure Portal](https://portal.azure.com) onder runtime-instellingen voor de **configuratie**-  >  **functie**.

:::image type="content" source="media/functions-networking-options/virtual-network-trigger-toggle.png" alt-text="VNETToggle":::

U kunt ook virtuele netwerk triggers inschakelen met behulp van de volgende Azure CLI-opdracht:

```azurecli-interactive
az resource update -g <resource_group> -n <function_app_name>/config/web --set properties.functionsRuntimeScaleMonitoringEnabled=1 --resource-type Microsoft.Web/sites
```

> [!TIP]
> Het inschakelen van virtuele netwerk triggers kan invloed hebben op de prestaties van uw toepassing, omdat uw App Service plan-instanties uw Triggers moeten controleren om te bepalen wanneer moet worden geschaald. Deze impact is waarschijnlijk zeer klein.

Virtuele netwerk triggers worden ondersteund in versie 2. x en hoger van de functions-runtime. De volgende niet-HTTP-trigger typen worden ondersteund.

| Toestelnummer | Minimale versie |
|-----------|---------| 
|[Micro soft. Azure. webjobs. Extensions. Storage](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage/) | 3.0.10 of hoger |
|[Micro soft. Azure. webjobs. Extensions. Event hubs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.EventHubs)| 4.1.0 of hoger|
|[Micro soft. Azure. webjobs. Extensions. ServiceBus](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.ServiceBus)| 3.2.0 of hoger|
|[Micro soft. Azure. webjobs. Extensions. CosmosDB](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.CosmosDB)| 3.0.5 of hoger|
|[Micro soft. Azure. webjobs. Extensions. DurableTask](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DurableTask)| 2.0.0 of hoger|

> [!IMPORTANT]
> Wanneer u ondersteuning voor virtuele netwerk triggers inschakelt, worden alleen de trigger typen die in de vorige tabel worden weer gegeven, dynamisch met uw toepassing geschaald. U kunt nog steeds triggers gebruiken die zich niet in de tabel bevinden, maar ze worden niet groter dan het aantal vooraf gewarmde instanties. Zie [Triggers en bindingen](./functions-triggers-bindings.md#supported-bindings)voor de volledige lijst met triggers.

### <a name="app-service-plan-and-app-service-environment-with-virtual-network-triggers"></a>App Service plan en App Service Environment met virtuele netwerk triggers

Wanneer uw functie-app wordt uitgevoerd in een App Service plan of een App Service Environment, kunt u niet-HTTP-trigger functies gebruiken. Als u wilt dat uw functies correct worden geactiveerd, moet u verbinding hebben met een virtueel netwerk met toegang tot de bron die is gedefinieerd in de trigger verbinding.

Stel dat u Azure Cosmos DB wilt configureren om alleen verkeer van een virtueel netwerk te accepteren. In dit geval moet u uw functie-app implementeren in een App Service plan dat virtuele netwerk integratie met dat virtuele netwerk biedt. Met integratie kan een functie worden geactiveerd door die Azure Cosmos DB resource.

## <a name="hybrid-connections"></a>Hybride verbindingen

[Hybride verbindingen](../azure-relay/relay-hybrid-connections-protocol.md) is een functie van Azure relay die u kunt gebruiken om toegang te krijgen tot toepassings bronnen in andere netwerken. Het biedt toegang vanuit uw app tot een eind punt van de toepassing. U kunt deze niet gebruiken voor toegang tot uw toepassing. Hybride verbindingen is beschikbaar voor functies die alleen in Windows worden uitgevoerd in het verbruiks abonnement.

De hybride verbinding wordt gebruikt in Azure Functions en is afgestemd op één combi natie van TCP-host en poort. Dit betekent dat het eind punt van de hybride verbinding zich op elk besturings systeem en elke toepassing kan bevindt, mits u een TCP-Luister poort opent. Met de functie Hybride verbindingen weet u niet wat het toepassings protocol is of wat u toegang hebt. Het biedt alleen toegang tot het netwerk.

Zie de [app service-documentatie voor hybride verbindingen voor](../app-service/app-service-hybrid-connections.md)meer informatie. Deze zelfde configuratie stappen ondersteunen Azure Functions.

>[!IMPORTANT]
> Hybride verbindingen wordt alleen ondersteund op Windows-abonnementen. Linux wordt niet ondersteund.

## <a name="outbound-ip-restrictions"></a>Uitgaande IP-beperkingen

Uitgaande IP-beperkingen zijn beschikbaar in een Premium-abonnement, App Service plan of App Service Environment. U kunt uitgaande beperkingen configureren voor het virtuele netwerk waar uw App Service Environment wordt geïmplementeerd.

Wanneer u een functie-app integreert in een Premium-abonnement of een App Service plan met een virtueel netwerk, kan de app standaard nog steeds uitgaande oproepen naar Internet maken. Door de toepassings instelling toe `WEBSITE_VNET_ROUTE_ALL=1` te voegen, dwingt u af dat alle uitgaand verkeer naar uw virtuele netwerk moet worden verzonden, waarbij regels voor netwerk beveiligings groepen kunnen worden gebruikt om verkeer te beperken.

## <a name="automation"></a>Automation
Met de volgende Api's kunt u via programma code regionale virtuele netwerk integraties beheren:

+ **Azure cli**: gebruik de [`az functionapp vnet-integration`](/cli/azure/functionapp/vnet-integration) opdrachten om een regionale virtuele netwerk integratie toe te voegen, weer te geven of te verwijderen.  
+ **Arm-sjablonen**: regionale integratie van virtuele netwerken kan worden ingeschakeld met behulp van een Azure Resource Manager sjabloon. Voor een volledig voor beeld raadpleegt u [de Quick Start-sjabloon van deze functies](https://azure.microsoft.com/resources/templates/101-function-premium-vnet-integration/).

## <a name="troubleshooting"></a>Problemen oplossen

[!INCLUDE [app-service-web-vnet-troubleshooting](../../includes/app-service-web-vnet-troubleshooting.md)]

## <a name="next-steps"></a>Volgende stappen

Meer informatie over netwerken en Azure Functions:

* [Volg de zelf studie over het aan de slag gaan met virtuele netwerk integratie](./functions-create-vnet.md)
* [Lees de veelgestelde vragen over functies netwerken](./functions-networking-faq.md)
* [Meer informatie over de integratie van virtuele netwerken met App Service/functions](../app-service/web-sites-integrate-with-vnet.md)
* [Meer informatie over virtuele netwerken in azure](../virtual-network/virtual-networks-overview.md)
* [Meer netwerk functies en-beheer inschakelen met App Service omgevingen](../app-service/environment/intro.md)
* [Verbinding maken met afzonderlijke on-premises bronnen zonder Firewall wijzigingen met behulp van Hybride verbindingen](../app-service/app-service-hybrid-connections.md)
