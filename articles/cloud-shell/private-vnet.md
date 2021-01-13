---
title: Cloud Shell in een Azure Virtual Network
description: Cloud Shell implementeren in een virtueel Azure-netwerk
services: Azure
documentationcenter: ''
author: maertendMSFT
manager: timlt
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 07/15/2020
ms.author: damaerte
ms.openlocfilehash: a820fb4ec5d6c2053b1104b68dbda0ae70f624c0
ms.sourcegitcommit: c136985b3733640892fee4d7c557d40665a660af
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/13/2021
ms.locfileid: "98179194"
---
# <a name="deploy-cloud-shell-into-an-azure-virtual-network"></a>Cloud Shell implementeren in een virtueel Azure-netwerk

Een gewone Cloud Shell sessie wordt uitgevoerd in een container in een micro soft-netwerk gescheiden van uw resources. Dit betekent dat opdrachten die in de container worden uitgevoerd, geen toegang hebben tot bronnen die alleen toegankelijk zijn vanuit een specifiek virtueel netwerk. U kunt SSH bijvoorbeeld niet gebruiken om verbinding te maken van Cloud Shell naar een virtuele machine die alleen een privé-IP-adres heeft, of gebruik kubectl om verbinding te maken met een Kubernetes-cluster dat de toegang heeft vergrendeld. 

Met deze optionele functie worden deze beperkingen opgelost en kunt u Cloud Shell implementeren in een virtueel Azure-netwerk dat u beheert. Vanaf daar kan de container communiceren met resources binnen het virtuele netwerk dat u selecteert.  

Hieronder ziet u de resource architectuur die in dit scenario wordt geïmplementeerd en gebruikt.

![Illustreert de Cloud Shell geïsoleerde VNET-architectuur.](media/private-vnet/data-diagram.png)

Voordat u Cloud Shell in uw eigen Azure Virtual Network kunt gebruiken, moet u verschillende resources maken ter ondersteuning van deze functionaliteit. In dit artikel wordt beschreven hoe u de vereiste resources instelt met behulp van een ARM-sjabloon.

> [!NOTE]
> Deze resources hoeven slechts één keer te worden ingesteld voor het virtuele netwerk. Ze kunnen vervolgens worden gedeeld door alle beheerders met toegang tot het virtuele netwerk.

## <a name="required-network-resources"></a>Vereiste netwerk bronnen

### <a name="virtual-network"></a>Virtueel netwerk
Een virtueel netwerk definieert de adres ruimte waarin een of meer subnetten worden gemaakt.

Het gewenste virtuele netwerk dat moet worden gebruikt voor Cloud Shell moet worden geïdentificeerd. Dit is doorgaans een bestaand virtueel netwerk dat bronnen bevat die u wilt beheren of een netwerk dat peers met netwerken die uw resources bevatten.

### <a name="subnet"></a>Subnet
Binnen het geselecteerde virtuele netwerk moet een toegewezen subnet worden gebruikt voor Cloud Shell containers. Dit subnet wordt overgedragen aan de Azure Container Instances (ACI)-service.  Wanneer een gebruiker een Cloud Shell container aanvraagt in een virtueel netwerk, Cloud Shell gebruikt ACI voor het maken van een container die zich in dit overgedragen subnet bevindt.  Er kunnen geen andere resources worden gemaakt in dit subnet.

### <a name="network-profile"></a>Netwerkprofiel
Een netwerk profiel is een netwerk configuratie sjabloon voor Azure-resources waarmee bepaalde netwerk eigenschappen voor de bron worden opgegeven.

### <a name="azure-relay"></a>Azure Relay
Een [Azure relay](../azure-relay/relay-what-is-it.md) staat twee eind punten toe die niet rechtstreeks bereikbaar zijn om te communiceren. In dit geval wordt deze gebruikt om de browser van de beheerder in staat te stellen te communiceren met de container in het particuliere netwerk.

Het Azure Relay exemplaar dat voor Cloud Shell wordt gebruikt, kan worden geconfigureerd om te bepalen welke netwerken toegang hebben tot container bronnen: 
- Toegankelijk via het open bare Internet: in deze configuratie biedt Cloud Shell een manier om andere interne resources van buiten te halen. 
- Toegankelijk vanuit opgegeven netwerken: in deze configuratie moeten beheerders toegang hebben tot de Azure Portal vanaf een computer met in het juiste netwerk om Cloud Shell te kunnen gebruiken.

## <a name="storage-requirements"></a>Opslag vereisten
Zoals in de standaard Cloud Shell, is een opslag account vereist tijdens het gebruik van Cloud Shell in een virtueel netwerk. Elke beheerder moet een bestands share hebben om hun bestanden op te slaan.  Het opslag account moet toegankelijk zijn vanuit het virtuele netwerk dat wordt gebruikt door Cloud Shell. 

## <a name="virtual-network-deployment-limitations"></a>Beperkingen voor de implementatie van virtuele netwerken
* Als gevolg van de betrokken extra netwerk bronnen is het starten van Cloud Shell in een virtueel netwerk meestal trager dan een standaard Cloud Shell sessie.

* Alle Cloud Shell regio's van Centraal-India worden momenteel ondersteund. 

* [Azure relay](../azure-relay/relay-what-is-it.md) is geen gratis service, Bekijk hun [prijzen](https://azure.microsoft.com/pricing/details/service-bus/). In het Cloud Shell scenario wordt één hybride verbinding voor elke beheerder gebruikt wanneer ze Cloud Shell gebruiken. De verbinding wordt automatisch afgesloten nadat de Cloud Shell sessie is voltooid.

## <a name="register-the-resource-provider"></a>De resourceprovider registreren

De resource provider micro soft. ContainerInstances moet zijn geregistreerd in het abonnement dat het virtuele netwerk bevat dat u wilt gebruiken. Selecteer het juiste abonnement met en `Set-AzContext -Subscription {subscriptionName}` Voer het volgende uit:

```powershell
PS> Get-AzResourceProvider -ProviderNamespace Microsoft.ContainerInstance | select ResourceTypes,RegistrationState

ResourceTypes                             RegistrationState
-------------                             -----------------
{containerGroups}                         Registered
...
```

Als **RegistrationState** is `Registered` , hoeft u geen actie te ondernemen. Als dit het geval is `NotRegistered` , voert u uit `Register-AzResourceProvider -ProviderNamespace Microsoft.ContainerInstance` . 

## <a name="deploy-network-resources"></a>Netwerk bronnen implementeren
 
### <a name="create-a-resource-group-and-virtual-network"></a>Een resource groep en een virtueel netwerk maken
Als u al een gewenst VNET hebt waarmee u verbinding wilt maken, kunt u deze sectie overs Laan.

In de Azure Portal, of met behulp van Azure CLI, Azure PowerShell, enzovoort. Maak een resource groep en een virtueel netwerk in de nieuwe resource groep, **de resource groep en het virtuele netwerk moeten zich in dezelfde regio bevinden**.

> [!NOTE]
> In de open bare Preview moet de resource groep en het virtuele netwerk zich in WestCentralUS of Westus bevinden.

### <a name="arm-templates"></a>ARM-sjablonen
Gebruik de [Azure Quick](https://aka.ms/cloudshell/docs/vnet/template) start-sjabloon voor het maken van Cloud shell-resources in een virtueel netwerk en de [Azure Quick](https://aka.ms/cloudshell/docs/vnet/template/storage) start-sjabloon voor het maken van de benodigde opslag. Noteer de namen van uw resources, voornamelijk de naam van de bestands share.

### <a name="open-relay-firewall"></a>Firewall voor Relais openen
Ga naar de relay die is gemaakt met de bovenstaande sjabloon en selecteer ' netwerken ' in instellingen, toegang vanaf uw browser netwerk tot de relay toestaan. De relay is standaard alleen toegankelijk vanuit het virtuele netwerk waarin het is gemaakt. 

### <a name="configuring-cloud-shell-to-use-a-virtual-network"></a>Cloud Shell configureren voor het gebruik van een virtueel netwerk.
> [!NOTE]
> Deze stap moet worden uitgevoerd voor elke beheerder die gebruikmaakt van Cloud Shell.

Ga na de implementatie van de bovenstaande stappen naar Cloud Shell in het Azure Portal of op https://shell.azure.com . Een van deze ervaringen moet worden gebruikt elke keer dat u verbinding wilt maken met een geïsoleerde Cloud Shell ervaring.

> [!NOTE]
> Als Cloud Shell in het verleden is gebruikt, moet de bestaande clouddrive worden ontkoppeld. U kunt dit uitvoeren `clouddrive unmount` vanuit een actieve Cloud shell-sessie door de pagina te vernieuwen.

Verbinding maken met Cloud Shell, wordt u gevraagd om de eerste keer dat de sessie wordt uitgevoerd. Selecteer de gewenste shell-ervaring, selecteer Geavanceerde instellingen weer geven en schakel het selectie vakje VNET-isolatie-instellingen weer geven in. Vul de velden in het pop-upvenster in.  De meeste velden worden automatisch ingevuld in de beschik bare resources die kunnen worden gekoppeld aan Cloud Shell in een virtueel netwerk.  De naam van de bestands share moet worden ingevuld door de gebruiker.


![Illustreert de Cloud Shell instellingen voor de eerste ervaring van de geïsoleerde VNET.](media/private-vnet/vnet-settings.png)

## <a name="next-steps"></a>Volgende stappen
[Meer informatie over Azure Virtual Networks](../virtual-network/virtual-networks-overview.md)
