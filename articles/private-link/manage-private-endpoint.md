---
title: Een verbinding met een privé-eind punt beheren in azure
description: Meer informatie over het beheren van privé-eindpunt verbindingen in azure
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: how-to
ms.date: 09/16/2019
ms.author: allensu
ms.openlocfilehash: 2987cd1ff8c678f7079e13e8b9bc657817c066f1
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/24/2020
ms.locfileid: "95531369"
---
# <a name="manage-a-private-endpoint-connection"></a>Manage a Private Endpoint connection (Een privé-eindpuntverbinding beheren)
Een persoonlijke Azure-koppeling werkt op een goedkeurings stroom model voor de persoonlijke koppeling service, waarmee de gebruiker van de private link een verbinding kan aanvragen met de service provider voor het gebruik van de service. De service provider kan vervolgens bepalen of de gebruiker verbinding mag maken of niet. Met de persoonlijke Azure-koppeling kunnen de service providers de verbinding van het particuliere eind punt op hun resources beheren. In dit artikel vindt u instructies voor het beheren van de verbindingen met een privé-eind punt.

![Privé-eind punten beheren](media/manage-private-endpoint/manage-private-endpoint.png)

Er zijn twee methoden voor het goed keuren van een verbinding die een persoonlijke koppelings service gebruiker kan kiezen uit:
- **Automatisch**: als de service verbruiker Azure RBAC-machtigingen heeft voor de resource van de service provider, kan de gebruiker de automatische goedkeurings methode kiezen. In dit geval is er geen actie vereist van de service provider en wordt de verbinding automatisch goedgekeurd wanneer de aanvraag de resource van de service provider bereikt. 
- **Hand matig**: als de service gebruiker geen Azure RBAC-machtigingen voor de resource van de service provider heeft, kan de gebruiker de hand matige goedkeurings methode kiezen. In dit geval wordt de verbindings aanvraag op de service resources weer gegeven als **in behandeling**. De service provider moet de aanvraag hand matig goed keuren voordat verbindingen kunnen worden gemaakt. In hand matige gevallen kan de service gebruiker ook een bericht opgeven met de aanvraag om meer context te bieden aan de service provider. De service provider heeft de volgende opties waaruit u kunt kiezen voor alle verbindingen met privé-eind punten: **goedgekeurd**, **afwijzen**, **verwijderen**.

In de onderstaande tabel ziet u de verschillende acties van de service provider en de resulterende status van de verbinding voor privé-eind punten.  De service provider kan ook de verbindings status van de verbinding met het particuliere eind punt op een later tijdstip wijzigen zonder tussen komst van de gebruiker. Met deze actie wordt de status van het eind punt op de gebruikers zijde bijgewerkt. 


|Actie van service provider   |Status privé-eind punt service gebruiker   |Beschrijving   |
|---------|---------|---------|
|Geen    |    In behandeling     |    De verbinding wordt hand matig gemaakt en is in afwachting van goed keuring door de resource-eigenaar van de persoonlijke koppeling.       |
|Goedkeuren    |  Goedgekeurd       |  De verbinding werd automatisch of handmatig goedgekeurd en is klaar om te worden gebruikt.     |
|Afwijzen     | Afgewezen        | De verbinding werd afgewezen door de resource-eigenaar van de private link.        |
|Verwijderen    |  Ontkoppeld       | De verbinding is verwijderd door de resource-eigenaar van de persoonlijke koppeling, het persoonlijke eind punt wordt informatieve en moet worden verwijderd om op te schonen.        |
|   |         |         |
   
## <a name="manage-private-endpoint-connections-on-azure-paas-resources"></a>Manage Private Endpoint Connections on Azure PaaS resources (Privé-eindpuntverbindingen beheren in Azure PaaS-resources)
Portal is de voorkeurs methode voor het beheren van privé-eindpunt verbindingen op Azure PaaS-resources. Op dit moment hebben we geen Power shell/CLI-ondersteuning voor het beheer van verbindingen op Azure PaaS-resources.
1. Meld u aan bij Azure Portal op https://portal.azure.com.
2. Navigeer naar het persoonlijke koppelings centrum.
3. Onder **resources** selecteert u het bron type dat u wilt beheren van de verbindingen met een privé-eind punt.
4. Voor elk van uw bron typen kunt u het aantal privé-eindpunt verbindingen bekijken dat eraan is gekoppeld. U kunt de resources op de gewenste manier filteren.
5. Selecteer de verbindingen met het persoonlijke eind punt.  Selecteer in de lijst met verbindingen de verbinding die u wilt beheren. 
6. U kunt de status van de verbinding wijzigen door bovenaan de opties te selecteren.

## <a name="manage-private-endpoint-connections-on-a-customerpartner-owned-private-link-service"></a>Particuliere endpoint-verbindingen beheren op een privé koppelings service van een klant/partner

Azure PowerShell en Azure CLI zijn de voorkeurs methoden voor het beheren van privé-eindpunt verbindingen op micro soft partner services of services die eigendom zijn van de klant. Op dit moment hebben we geen portal ondersteuning voor het beheren van verbindingen op een privé koppelings service.  
 
### <a name="powershell"></a>PowerShell 
  
Gebruik de volgende Power shell-opdrachten voor het beheren van verbindingen met een privé-eind punt.  
#### <a name="get-private-link-connection-states"></a>Verbindings statussen van particuliere koppelingen ophalen 
Gebruik de `Get-AzPrivateLinkService` cmdlet om de persoonlijke eindpunt verbindingen en hun statussen op te halen.  
```azurepowershell
Get-AzPrivateLinkService -Name myPrivateLinkService -ResourceGroupName myResourceGroup 
 ```
 
#### <a name="approve-a-private-endpoint-connection"></a>Een verbinding met een privé-eind punt goed keuren 
 
Gebruik de- `Approve-AzPrivateEndpointConnection` cmdlet om een verbinding met een privé-eind punt goed te keuren. 
 
```azurepowershell
Approve-AzPrivateEndpointConnection -Name myPrivateEndpointConnection -ResourceGroupName myResourceGroup -ServiceName myPrivateLinkService
```
 
#### <a name="deny-private-endpoint-connection"></a>Verbinding voor privé-eind punt weigeren 
 
Gebruik de `Deny-AzPrivateEndpointConnection` cmdlet om een verbinding met een privé-eind punt af te wijzen. 
```azurepowershell
Deny-AzPrivateEndpointConnection -Name myPrivateEndpointConnection -ResourceGroupName myResourceGroup -ServiceName myPrivateLinkService 
```
#### <a name="remove-private-endpoint-connection"></a>Verbinding voor privé-eind punt verwijderen 
 
Gebruik de `Remove-AzPrivateEndpointConnection` cmdlet om een verbinding met een privé-eind punt te verwijderen. 
```azurepowershell
Remove-AzPrivateEndpointConnection -Name myPrivateEndpointConnection1 -ResourceGroupName myResourceGroup -ServiceName myPrivateLinkServiceName 
```
 
### <a name="azure-cli"></a>Azure CLI 
 
Gebruiken `az network private-link-service update` voor het beheren van uw privé-eindpunt verbindingen. De verbindings status is opgegeven in de ```azurecli connection-status``` para meter. 
```azurecli
az network private-link-service connection update -g myResourceGroup -n myPrivateEndpointConnection1 --service-name myPLS --connection-status Approved 
```

   

## <a name="next-steps"></a>Volgende stappen
- [Meer informatie over privé-eind punten](private-endpoint-overview.md)
 
