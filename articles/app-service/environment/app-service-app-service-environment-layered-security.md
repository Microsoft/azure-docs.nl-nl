---
title: Gelaagde beveiliging v1
description: Meer informatie over het implementeren van een gelaagde beveiligings architectuur in uw App Service omgeving. Dit document is alleen bedoeld voor klanten die gebruikmaken van de oudere V1-ASE.
author: stefsch
ms.assetid: 73ce0213-bd3e-4876-b1ed-5ecad4ad5601
ms.topic: article
ms.date: 08/30/2016
ms.author: stefsch
ms.custom: seodec18
ms.openlocfilehash: 813160ee19c8447fdfcb705e7a9162a41a0c308c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101739505"
---
# <a name="implementing-a-layered-security-architecture-with-app-service-environments"></a>Een gelaagde beveiligings architectuur implementeren met App Service omgevingen
Omdat App Service omgevingen een geïsoleerde runtime-omgeving bieden die is geïmplementeerd in een virtueel netwerk, kunnen ontwikkel aars een gelaagde beveiligings architectuur maken die verschillende niveaus van netwerk toegang biedt voor elke fysieke toepassingslaag.

Een gemeen schappelijke wens is de API-back-ends van algemene Internet toegang te verbergen en alleen Api's toe te staan door upstream-web-apps.  [Netwerk beveiligings groepen (nsg's)][NetworkSecurityGroups] kunnen worden gebruikt op subnetten met app service omgevingen om de open bare toegang tot API-toepassingen te beperken.

In het onderstaande diagram ziet u een voorbeeld architectuur met een op WebAPI gebaseerde app die is geïmplementeerd op een App Service Environment.  Drie afzonderlijke web-app-instanties, geïmplementeerd in drie afzonderlijke App Service omgevingen, maken back-end-aanroepen naar dezelfde WebAPI-app.

![Conceptuele architectuur][ConceptualArchitecture] 

De groene plus tekens geven aan dat de netwerk beveiligings groep op het subnet met ' apiase ' inkomende aanroepen van de web-apps in de upstream toestaat, en dat zelf wordt gebeld.  Met dezelfde netwerk beveiligings groep wordt echter expliciet de toegang geweigerd tot algemeen binnenkomend verkeer van het internet. 

In de rest van dit artikel worden de stappen beschreven die nodig zijn voor het configureren van de netwerk beveiligings groep op het subnet met ' apiase '.

## <a name="determining-the-network-behavior"></a>Het netwerk gedrag bepalen
Als u wilt weten welke netwerk beveiligings regels er nodig zijn, moet u bepalen welke netwerkclients de App Service Environment met de API-app kunnen bereiken en welke clients worden geblokkeerd.

Aangezien [netwerk beveiligings groepen (nsg's)][NetworkSecurityGroups] worden toegepast op subnetten en app service omgevingen in subnetten worden geïmplementeerd, zijn de regels in een NSG van toepassing op **alle** apps die worden uitgevoerd op een app service environment.  Als u de voorbeeld architectuur voor dit artikel gebruikt en een netwerk beveiligings groep wordt toegepast op het subnet met ' apiase ', worden alle apps die worden uitgevoerd op de App Service Environment ' apiase ' beveiligd door dezelfde set beveiligings regels. 

* **Het uitgaande IP-adres van upstream-aanroepers bepalen:**  Wat is het IP-adres of de adressen van de upstream-bellers?  Voor deze adressen moet expliciet toegang worden verleend in de NSG.  Omdat aanroepen tussen App Service omgevingen worden beschouwd als Internet-aanroepen, moet het uitgaande IP-adres dat is toegewezen aan elk van de drie upstream-App Service omgevingen, toegang hebben tot de NSG voor het subnet ' apiase '.   Zie het artikel overzicht van de [netwerk architectuur][NetworkArchitecture] voor meer informatie over het bepalen van het uitgaande IP-adres voor apps die worden uitgevoerd in een app service environment.
* **Moet de back-end-API-app zichzelf aanroepen?**  Een soms overziend en subtiel punt is het scenario waarin de back-end-toepassing moet worden aangeroepen.  Als een back-end-API-toepassing op een App Service Environment moet worden aangeroepen, wordt deze ook behandeld als een Internet aanroep.  In de voorbeeld architectuur moet er ook toegang worden toegestaan vanuit het uitgaande IP-adres van de App Service Environment ' apiase '.

## <a name="setting-up-the-network-security-group"></a>De netwerk beveiligings groep instellen
Zodra de set met uitgaande IP-adressen bekend is, is de volgende stap het maken van een netwerk beveiligings groep.  Netwerk beveiligings groepen kunnen worden gemaakt voor virtuele netwerken op basis van Resource Manager en voor klassieke virtuele netwerken.  In de onderstaande voor beelden ziet u hoe u een NSG maakt en configureert in een klassiek virtueel netwerk met behulp van Power shell.

Voor de voorbeeld architectuur bevinden de omgevingen zich in Zuid-Centraal VS, zodat een lege NSG wordt gemaakt in die regio:

```azurepowershell-interactive
New-AzureNetworkSecurityGroup -Name "RestrictBackendApi" -Location "South Central US" 
-Label "Only allow web frontend and loopback traffic"
```

Eerst wordt een regel voor expliciete toegang toegevoegd voor de Azure-beheer infrastructuur zoals vermeld in het artikel over [binnenkomend verkeer][InboundTraffic] voor app service omgevingen.

```azurepowershell-interactive
#Open ports for access by Azure management infrastructure
Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW AzureMngmt" 
-Type Inbound -Priority 100 -Action Allow -SourceAddressPrefix 'INTERNET' -SourcePortRange '*' 
-DestinationAddressPrefix '*' -DestinationPortRange '454-455' -Protocol TCP
```

Vervolgens worden er twee regels toegevoegd voor het toestaan van HTTP-en HTTPS-aanroepen vanaf de eerste upstream-App Service Environment (' fe1ase ').

```azurepowershell-interactive
#Grant access to requests from the first upstream web front-end
Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTP fe1ase" 
-Type Inbound -Priority 200 -Action Allow -SourceAddressPrefix '65.52.xx.xyz'  -SourcePortRange '*' 
-DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP
Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTPS fe1ase" 
-Type Inbound -Priority 300 -Action Allow -SourceAddressPrefix '65.52.xx.xyz'  -SourcePortRange '*' 
-DestinationAddressPrefix '*' -DestinationPortRange '443' -Protocol TCP
```

Spoelen en herhalen voor de tweede en derde upstream App Service omgevingen (' fe2ase ' en ' fe3ase ').

```azurepowershell-interactive
#Grant access to requests from the second upstream web front-end
Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTP fe2ase" 
-Type Inbound -Priority 400 -Action Allow -SourceAddressPrefix '191.238.xyz.abc'  -SourcePortRange '*' 
-DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP
Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTPS fe2ase" 
-Type Inbound -Priority 500 -Action Allow -SourceAddressPrefix '191.238.xyz.abc'  -SourcePortRange '*' 
-DestinationAddressPrefix '*' -DestinationPortRange '443' -Protocol TCP

#Grant access to requests from the third upstream web front-end
Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTP fe3ase" 
-Type Inbound -Priority 600 -Action Allow -SourceAddressPrefix '23.98.abc.xyz'  -SourcePortRange '*' 
-DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP
Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTPS fe3ase" 
-Type Inbound -Priority 700 -Action Allow -SourceAddressPrefix '23.98.abc.xyz'  -SourcePortRange '*' 
-DestinationAddressPrefix '*' -DestinationPortRange '443' -Protocol TCP
```

Verleen ten slotte toegang tot het uitgaande IP-adres van de App Service Environment van de back-end-API, zodat deze weer kan worden aangeroepen.

```azurepowershell-interactive
#Allow apps on the apiase environment to call back into itself
Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTP apiase" 
-Type Inbound -Priority 800 -Action Allow -SourceAddressPrefix '70.37.xyz.abc'  -SourcePortRange '*' 
-DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP
Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTPS apiase" 
-Type Inbound -Priority 900 -Action Allow -SourceAddressPrefix '70.37.xyz.abc'  -SourcePortRange '*' 
-DestinationAddressPrefix '*' -DestinationPortRange '443' -Protocol TCP
```

Er zijn geen andere netwerk beveiligings regels vereist, omdat elke NSG een set standaard regels heeft die binnenkomende toegang van Internet blok keren.

De volledige lijst met regels in de netwerk beveiligings groep wordt hieronder weer gegeven.  U ziet hoe de laatste regel, die is gemarkeerd, binnenkomende toegang blokkeert van alle bellers, met uitzonde ring van bellers die expliciet toegang hebben gekregen.

![NSG-configuratie][NSGConfiguration] 

De laatste stap bestaat uit het Toep assen van de NSG op het subnet met de App Service Environment ' apiase '.

```azurepowershell-interactive
#Apply the NSG to the backend API subnet
Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityGroupToSubnet 
-VirtualNetworkName 'yourvnetnamehere' -SubnetName 'API-ASE-Subnet'
```

Met de NSG die wordt toegepast op het subnet, mogen alleen de drie upstream-App Service omgevingen en de App Service Environment met de API-back-end worden aangeroepen in de omgeving ' apiase '.

## <a name="additional-links-and-information"></a>Aanvullende koppelingen en informatie
Informatie over [netwerk beveiligings groepen](../../virtual-network/network-security-groups-overview.md).

Informatie over [uitgaande IP-adressen][NetworkArchitecture] en app service omgevingen.

[Netwerk poorten][InboundTraffic] die worden gebruikt door app service omgevingen.

[!INCLUDE [app-service-web-try-app-service](../../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[NetworkSecurityGroups]: ../../virtual-network/virtual-network-vnet-plan-design-arm.md
[NetworkArchitecture]:  app-service-app-service-environment-network-architecture-overview.md
[InboundTraffic]:  app-service-app-service-environment-control-inbound-traffic.md

<!-- IMAGES -->
[ConceptualArchitecture]: ./media/app-service-app-service-environment-layered-security/ConceptualArchitecture-1.png
[NSGConfiguration]:  ./media/app-service-app-service-environment-layered-security/NSGConfiguration-1.png